# Function Comparison by Model

| Model   | Activation | Out        | Loss    | Optimizer |
| ------- | ---------- | ---------- | ------- | --------- |
| AE      | ReLU       | Tanh       | MSELoss | Adam      |
| CAE     | ReLU       | Tanh       | MSELoss | Adam      |
| VAE     | -          | Sigmoid    | MSELoss | Adam      |
| ConvNet | ReLU       | LogSoftmax | NLLLoss | SGD       |
| GAN G   | LeakyReLU  | Tanh       | BCELoss | Adam      |
| GAN D   | LeakyReLU  | Sigmoid    | BCELoss | Adam      |
| cGAN G  | LeakyReLU  | Tanh       | MSELoss | Adam      |
| cGAN D  | LeakyReLU  | -          | MSELoss | Adam      |
| DCGAN G | LeakyReLU  | Tanh       | BCELoss | Adam      |
| DCGAN D | LeakyReLU  | Sigmoid    | BCELoss | Adam      |
| SGAN G  | LeakyReLU  | Tanh       | BCELoss | Adam      |
| SGAN D  | LeakyReLU  | Softmax    | BCELoss | Adam      |
| SRGAN G | PReLU      | -          | MSELoss | Adam      |
| SRGAN D | PReLU      | -          | L1Loss  | Adam      |
|         |            |            |         |           |

