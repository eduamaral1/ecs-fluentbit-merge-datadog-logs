# ecs-fluentbit-merge-datadog-logs

Configuração do Fluent Bit (FireLens) no AWS ECS/Fargate para evitar splits de logs maiores que 16KB enviados para o Datadog.

## 📦 Estrutura

- `Dockerfile`: Define uma imagem customizada baseada na imagem oficial da AWS com um arquivo de configuração extra.
- `extra.conf`: Contém o filtro `multiline` com `partial_message` para unir logs fragmentados.
- `README.md`: Instruções detalhadas de configuração.

## 🚀 Como usar

1. Faça build da imagem customizada:

```bash
docker build -t fluent-bit-custom .
docker tag fluent-bit-custom:latest <ID-CONTA-AWS>.dkr.ecr.<REGIAO>.amazonaws.com/fluent-bit-custom:latest
docker push <ID-CONTA-AWS>.dkr.ecr.<REGIAO>.amazonaws.com/fluent-bit-custom:latest
```

2. No ECS, configure o container `log_router` com:

Adicione no Options:

    "config-file-type": "file",
    "config-file-value": "/extra.conf",

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

