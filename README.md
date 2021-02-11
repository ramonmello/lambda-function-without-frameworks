# Como criar uma função lambda na AWS sem usar frameworks

### Requisitos
aws-cli: >2.1.25

### 1º passo: Criar arquivo de políticas de segurança
### 2º passo: Criar role de segurança na AWS

```
aws iam create-role \
    --role-name lambda-exemplo \
    --assume-role-policy-document file://policies.json \
    | tee logs/role.log
```

### 3º passo: criar arquivo com conteúdo e zipa-lo
```
zip function.zip index.js
```
É importante lembrar que o parâmetro _role_ deve ser informado de acordo com o ARN que estiver no arquivo _role.log_
```
aws lambda create-function \
    --function-name hello-cli \
    --zip-file fileb://function.zip \
    --handler index.handler \
    --runtime nodejs12.x \
    --role [ARN encontrado no arquivo role.log gerado na criação da role] \
    | tee logs/lambda-create.log
```
### 4º passo: Invocar lambda
```
aws lambda invoke \
    --function-name hello-cli \
    --log-type Tail \
    logs/lambda-exec.log
```

### Atualizar a função lambda
Antes de executar esse comando é necessário zipar novamente o arquivo
```
aws lambda update-function-code \
    --zip-file fileb://function.zip \
    --function-name hello-cli \
    --publish \
    | tee logs/lambda-update.log
```

### Remover função e role
```
aws lambda delete-function \
    --function-name hello-cli
```
```
aws iam delete-role \
    --role-name lambda-exemplo
```