
# API - Predição de Score de Crédito

Esta API tem como objetivo realizar a predição de bons e maus pagadores, utilizando um modelo de machine learning treinado previamente. O modelo é carregado via arquivo local (`model.pkl`) e seu metadado é lido a partir de `model_metadata.json`.

## Arquitetura Geral

- A API está preparada para execução via AWS Lambda e integração com o API Gateway.
- O modelo realiza predição com base em atributos financeiros e de comportamento de crédito.
- Após cada predição, os dados são armazenados em um bucket S3 e métricas são registradas no Amazon CloudWatch.

---

## Entradas da API

O payload deve ser enviado em JSON no seguinte formato:

```json
{
  "data": {
    "Age": 35,
    "Annual_Income": 75000,
    "Monthly_Inhand_Salary": 6200,
    "Num_Bank_Accounts": 3,
    "Num_Credit_Card": 2,
    "Interest_Rate": 13.5,
    "Delay_from_due_date": 4,
    "Num_of_Delayed_Payment": 1,
    "Changed_Credit_Limit": 0,
    "Num_Credit_Inquiries": 2,
    "Credit_Utilization_Ratio": 30,
    "Total_EMI_per_month": 200,
    "Amount_invested_monthly": 500,
    "Monthly_Balance": 1500
  }
}
```

---

## Saída

A resposta da API é um JSON com:

- `prediction`: valor inteiro representando a predição do modelo (ex.: 0 = mau pagador, 1 = bom pagador)
- `version`: versão atual do modelo usada na inferência

```json
{
  "prediction": 1,
  "version": "1.0.0"
}
```

---

## Componentes da Solução

### `handler(event, context)`

Função principal, executada em chamadas Lambda. Processa os dados de entrada, executa a predição e retorna o resultado.

### `prepare_payload(data)`

Transforma o dicionário de dados brutos no formato esperado pelo modelo de machine learning.

### `input_metrics(data, prediction)`

Registra métricas customizadas no Amazon CloudWatch para monitoramento e rastreamento de atributos e resultados.

### `write_real_data(data, prediction)`

Armazena as predições reais em um arquivo `.csv` dentro de um bucket S3, permitindo análises futuras de performance e deriva de dados.

---

## Observações

- O modelo e metadados devem estar disponíveis no caminho `model/model.pkl` e `model/model_metadata.json`.
- É necessário ter permissões adequadas para escrita no S3 e envio de métricas para o CloudWatch.
- Idealmente, o modelo deve ser versionado e mantido em um repositório de modelos (MLflow, SageMaker, DagsHub etc).

---

## Requisitos

- Python >= 3.8
- Bibliotecas: `boto3`, `joblib`, `json`, `datetime`

---

## Exemplo de Deploy

A API foi preparada para ser usada com AWS Lambda + API Gateway. Para expô-la:

1. Empacote o código junto com os arquivos `model.pkl` e `model_metadata.json`.
2. Crie uma função Lambda com tempo de execução Python.
3. Adicione permissões para escrita no S3 e envio de métricas para CloudWatch.
4. Configure um endpoint no API Gateway com integração para Lambda.
5. Adicione uma API Key, se necessário.

---

Desenvolvido para o projeto de MBA em Data Science e IA - QuantumFinance
