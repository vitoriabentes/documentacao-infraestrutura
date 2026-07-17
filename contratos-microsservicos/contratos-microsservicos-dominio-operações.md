# Contrato de Microsserviços | Domínio de Operações

Os microsserviços do domínio de operações da plataforma expõe um endpoint REST para a realização de operações na plataforma de negociação de ativos e realiza comunicação assincrona para registrar operação válidas e atualizar a precificação do ativo de acordo com a valorização/desvalorização dele. A requisição é roteadas através do Nginx, que atua como proxy reverso e centraliza o acesso às APIs. As comunicações assincronas são realizadas atraves de filas `.fifo` e SQS da AWS.

## 1. API de Operações

**Base URL:** `http://34.204.232.188/operacao-api/v1/`

**Postman com todas as requisições de API:** [`Plataforma Negociação de Ativos.postman_collection.json`](Plataforma%20Negociação%20de%20Ativos.postman_collection.json)

| Método | Endpoint | Descrição                         |
|--------|----------|-----------------------------------|
| POST   | `/` | Realiza uma operação de compra/venda de ativo |

### Estrutura da Resposta
```json
{
  "tipo": "COMPRA",
  "codigo_ativo": "ITAU3",
  "codigo_corretora": 2,
  "codigo_investidor": 1,
  "quantidade": 10,
  "preco_unitario": 38.75
}
```

| Campo | Tipo | Descrição                                          |
|--------|------|----------------------------------------------------|
| tipo | string | Tipo da operação (Compra/Venda)                    |
| codigo_ativo | string | Código do ativo                                    |
| codigo_corretora | integer | Código da corretora                                |
| codigo_investidor | integer | Código do investidor                                 |
| quantidade | integer | Quantidade de ações negociadas                       |
| preco_unitario | decimal | Preço unitário da operação                         |

## 2. Comunicação Assíncrona via Fila Operação Registrada

**Nome da fila:** `operacao-registrada.fifo`

```json
{
  "codigo_ativo":"ITAU3",
  "quantidade": 10,
  "preco_unitario":38.75,
  "indexador":0.05,
  "tipo_operacao":"COMPRA"
}
```

| Campo | Tipo | Descrição                                        |
|--------|------|--------------------------------------------------|
| codigo_ativo | string | Código do ativo                                  |
| quantidade | integer | Quantidade de ações negociadas                   |
| preco_unitario | decimal | Preço unitário da operação                       |
| indexador | decimal | Indexador de valorização/desvalorização do ativo |
| tipo_operacao | string | Tipo da operação (COMPRA / VENDA)                |