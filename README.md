# ecs-fluentbit-merge-datadog-logs

Configuração do Fluent Bit (FireLens) no AWS ECS/Fargate para evitar splits de logs maiores que 16KB enviados para o Datadog.

## 📦 Estrutura

- `Dockerfile`: Crie uma imagem customizada baseada na imagem oficial da AWS com um arquivo de configuração extra.
- `extra.conf`: Contém o filtro `multiline` com `partial_message` para unir logs fragmentados.
- `README.md`: Instruções detalhadas de configuração.

## 🚀 Como usar

1. Crie seu repo privado no Amazon ECR.
2. Faça build da imagem customizada e o push para o repo:

```bash
docker build -t fluent-bit-custom .
docker tag fluent-bit-custom:latest <ID-ACCOUNT-AWS>.dkr.ecr.<REGION>.amazonaws.com/fluent-bit-custom:latest
docker push <ID-ACCOUNT-AWS>.dkr.ecr.<REGION>.amazonaws.com/fluent-bit-custom:latest
```

2. No ECS, configure o container `log_router` com:

Adicione no Options do firelensConfiguration:

    "config-file-type": "file",
    "config-file-value": "/extra.conf",

Exemplo:

```json
"firelensConfiguration": {
  "type": "fluentbit",
  "options": {
    "config-file-type": "file",
    "config-file-value": "/extra.conf",
    "enable-ecs-log-metadata": "true"
  }
}
```

3. Isso irá evitar que logs sejam fragmentados no Datadog.

---

