https://aqueduct.io/docs/deploy/deploy_docker/

# Deploying an Aqueduct Application using Docker

다른 배포 옵션은 [Aqueduct 응용 프로그램 배포](https://aqueduct.io/docs/deploy/)를 참조하십시오.

### Purpose

이 문서에서는 Docker 및/또는 Kubernetes와 같은 컨테이너 오케스트레이션 플랫폼을 통해 Aqueduct 응용 프로그램을 배포하는 단계에 대해 설명합니다. Dockerfile 및 Kubernetes 템플릿의 경우 [이 저장소](https://github.com/stablekernel/kubernetes)를 참조하십시오.

이 방법으로 응용 프로그램을 배포하는 것에 익숙하지 않은 사용자는이 초보자 용 가이드가 아니므로 Docker 또는 Kubernetes의 주제는 다루지 않습니다.

### Dockerfiles

다음 Dockerfile은 Aqueduct 응용 프로그램을 실행합니다.

```dockerfile
FROM google/dart

WORKDIR /app
ADD pubspec.* /app/
RUN pub get --no-precompile
ADD . /app/
RUN pub get --offline --no-precompile

WORKDIR /app
EXPOSE 80

ENTRYPOINT ["pub", "run", "aqueduct:aqueduct", "serve", "--port", "80"]
```

### Kubernetes Objects

데이터베이스 마이그레이션 및 OAuth 2.0 클라이언트 관리 작업과 같은 Kubernetes 객체에 대해서는 [이 저장소](https://github.com/stablekernel/kubernetes)를 참조하십시오. 다음은 Aqueduct 응용 프로그램을 시작하고 서비스로 공개하는 Kubernetes 구성 파일입니다. `<APP_NAME>`을 신청서의 이름으로 대체하십시오.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-service
  namespace: <APP_NAME>
spec:
  selector:
    app: <APP_NAME>
    role: backend
    type: api
  ports:
    - port: 80
      targetPort: 8082
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: api-deployment
  namespace: <APP_NAME>
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: <APP_NAME>
        role: backend
        type: api
    spec:
      containers:
        - name: <APP_NAME>
          # In development, setting `imagePullPolicy: Always` and using :latest tag is useful.
          # imagePullPolicy: Always
          image: <IMAGE>
          envFrom:
            - secretRef:
                name: secrets
            - configMapRef:
                name: config
          ports:
            - containerPort: 8082
      securityContext:
              runAsNonRoot: true
```

