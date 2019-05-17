# 머신 러닝 인퍼런스를 위한 RabbitMQ

어느 훈련된 모델의 용량은 2가기를 넘기기도 합니다. 용량이 큰 모델을 인퍼런스하는 서버를 만드려면 디버깅이 힘듭니다. 모델 로딩에 시간이 낭비되기 때문입니다. 이를 해결하기 위해 웹서버를 만들어서 하는 방법이 있습니다. 또 내부적으로만 사용할 경우 메시지 큐가 더 빠르고 나은 경우가 있습니다. 간단히 메시지 큐를 사용하여 인퍼런스 서버를 만드는 방법을 공개 합니다. 이 코드는 오래전에 만들어 두었으나 원우님의 연구에 필요하여 코드를 공유합니다.

## RabbitMQ

메시지큐는 RabbitMQ, ActiveMQ, ZeroMQ, Kafka 등 여러가지가 있습니다. Kafka가 기능이 많고 대용량 로그 처리에 적합하지만 설치가 어렵습니다. 그래서 설치만하면 바로 사용할 수 있는 RabbitMQ를 사용하기로 하였습니다.

이 데모는 서비스용이 아닌 머신러닝 모델 개발 과정에 사용하는 점에 주의하시기 바랍니다. 소스코드 라이센스는 MIT 입니다.

### Installation of RabbitMQ

리눅스에서 RabbitMQ 설치는 [이 블로그](http://abh0518.net/tok/?p=384)를 참조합니다.

## Python package

Python에서 RabbitMQ를 사용하여 데이터를 전송하고 받기 위해서는 두가지 패키지를 필요로 합니다.

- pika
- pickle

RabbitMQ는 여러가지 메시지프로토콜을 지원합니다. 그중에 AMQP도 지원하는데 pika는 파이썬에서 AMQP를 통해 RabbitMQ에 메시지를 보내고 받는데 필요합니다.

AMQP는 바이트배열이나 문자열만을 주고 받을 수 있습니다. 그래서 메모리에 존재하는 파이썬 개체는   바로 보낼 수 없는 경우가 많습니다. 파이썬 패키지 pickle은 파이썬 개체들을 바이트배열이나 문자열로 변환하여 네트워크를 통해 보내고 받을 수 있게 합니다. pickle는 파이썬 개체를 파일에 저장하고 읽어들이는데도 유용합니다. C# 같은 언어에서는 직렬화(Serialization)-역직렬화(Deserialization)하는 것과 유사하다고 보면 됩니다.

## Inference server

인퍼런스 서버는 별도의 프로세스로 시작하여 모델을 로드하고, 메시지 큐에서 메시지를 받아 해석하고 딥러닝 모델에 넣어 추론 결과를 받은 후 클라이언트로 보냅니다.

```python
import numpy as np
import pickle
import pika

import custom_layers

class RpcServer(object):

    def __init__(self):
        print(" [x] Loading model...")
        self.model = self.load_model()
        print(" [x] Model loaded")

        self.connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
        self.channel = self.connection.channel()
        self.channel.queue_declare(queue='my_rpc_queue')
        self.channel.basic_qos(prefetch_count=1)
        self.channel.basic_consume(on_message_callback=self.on_request, queue='my_rpc_queue')
        print(" [x] Awaiting RPC requests")
        self.channel.start_consuming()


    def on_request(self, ch, method, props, body):
        image = pickle.loads(body)
        print(" [.] on_request. predict image {} {}".format(type(image), image.shape))
        pred = self.model.predict(image)
        print(" [.] on_request. predicted image {} {}".format(type(pred), pred.shape))
        response = pickle.dumps(pred)

        ch.basic_publish(exchange='',
                        routing_key=props.reply_to,
                        properties = pika.BasicProperties(correlation_id = props.correlation_id),
                        body=response)
        ch.basic_ack(delivery_tag = method.delivery_tag)


    def load_model():
      # load your model here
      return model

if __name__ == "__main__":
	server = RpcServer()
```

`RpcServer`를 보면 `load_model` 함수가 있습니다. 이 함수에서 모델을 로드하여 반환하도록 코드를 변경합니다. 별도의 장비에서 RabbitMQ를 돌린다면 `host='localhost'`를 RabbitMQ가 설치된 장비의 주소로 수정하며 됩니다.

실행은 다음과 같습니다.

```sh
python3 rpc_server.py
```

이 프로세스는 계속 띄워두면 됩니다.

## Client

모델을 인퍼런스하는 개발중인 프로그램은 클라이언트입니다. 다음 메시지큐 사용 코드를 클라이언트를 추가 합니다.

```python
import numpy as np
import pickle
import pika
import time
import uuid

class RpcClient(object):

    def __init__(self):
        self.connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
        self.channel = self.connection.channel()
        result = self.channel.queue_declare(queue='', exclusive=True)
        self.callback_queue = result.method.queue
        self.channel.basic_consume(on_message_callback=self.on_response, auto_ack=True, queue=self.callback_queue)


    def on_response(self, ch, method, props, body):
        if self.corr_id == props.correlation_id:
            self.response = body


    def __call__(self, arr):
        self.response = None
        self.corr_id = str(uuid.uuid4())
        body = pickle.dumps(arr)
        self.channel.basic_publish(
                exchange='',
                routing_key='my_rpc_queue',
                properties=pika.BasicProperties(
                        reply_to = self.callback_queue,
                        correlation_id = self.corr_id,
                ),
                body=body)
        while self.response is None:
            self.connection.process_data_events()
            time.sleep(0.01)
		return pickle.loads(self.response)
```

RabbitMQ가 다른 서버에서 구동된다면 `host='localhost'`를 해당 서버 주소로 변경합니다.

### Inference

다음은 OpenCV로 이미지를 열어서 인퍼런스하는 과정입니다.

```python
import cv2
import numpy as np

from rpc_client import RpcClient

client = RpcClient()

img = cv2.imread('image.jpg')
pred = client(img)
```

이미지를 읽어서 `RpcClient`개체를 호출하면 RabbitMQ를 통해 `RpcServer`에 전달되고, 서버에서 인퍼런스가 끝나면 추론 결과를 반환 합니다.

### Clear

때로 개발중 어떤 문제가 발생하여 메시지 큐를 비워야 하는 경우가 있습니다. 

```python
import pika


class RpcClear(object):

    def __init__(self):
        self.connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
        self.channel = self.connection.channel()
        self.channel.queue_declare(queue='my_rpc_queue')
        self.channel.basic_qos(prefetch_count=1)
        self.channel.basic_consume(on_message_callback=self.on_request, queue='my_rpc_queue')
        print(" [x] Awaiting RPC requests")
        self.channel.start_consuming()


    def on_request(self, ch, method, props, body):
        ch.basic_publish(exchange='',
                        routing_key=props.reply_to,
                        properties = pika.BasicProperties(correlation_id = props.correlation_id),
                        body="")
        ch.basic_ack(delivery_tag = method.delivery_tag)


if __name__ == "__main__":
	clear = RpcClear()
```

```sh
python3 rpc_clear.py
```

메시지 큐를 비웁니다.

소스코드는 [booiljung/rabbitmq_rpc_demo](https://github.com/booiljung/rabbitmq_rpc_demo) 에 배포 하였습니다.

## 참조

- RabbitMQ
- pickle
- pika

