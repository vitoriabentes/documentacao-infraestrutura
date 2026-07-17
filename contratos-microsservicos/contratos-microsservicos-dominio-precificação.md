# Contrato de Microsserviços | Domínio de Precificação

Os microsserviços do domínio de precificação da plataforma expõem um endpoint REST para o resgate do preço atual dos ativos e realiza comunicação assíncronca com fila para atualização constante dessas precificações. A requisição é roteada através do Nginx, que atua como proxy reverso e centraliza o acesso às APIs. A comunicação assíncrona é realizada atraves de filas `.fifo` e SQS da AWS.

## 1. API de Preço Atual

**Base URL:** `http://34.204.232.188/preco-atual/v1/precos`

**Postman com todas as requisições de API:** [`Plataforma Negociação de Ativos.postman_collection.json`](Plataforma%20Negociação%20de%20Ativos.postman_collection.json)

| Método | Endpoint | Descrição                         |
|--------|----------|-----------------------------------|
| GET | `/{codigoAtivo}` | Busca o preço atual de um ativo específico |

### Estrutura da Resposta
```json
{
  "id": 1,
  "codigoAtivo": "PETR4",
  "preco": 35.50,
  "dataHoraAtualizacao": "2026-07-17T10:30:00",
  "atualizado": true
}
```
| Campo | Tipo | Descrição                                          |
|--------|------|----------------------------------------------------|
| id | integer | Identificador único do registro de precificação    |
| codigoAtivo | string | Código do ativo                                    |
| preco | decimal | Preço atual do ativo                               |
| dataHoraAtualizacao | datetime | Data e hora da última atualização                  |
| atualizado | boolean | Indica se esse é o preço atualizado |

## 2. Comunicação Assíncrona via Fila Ativo Alterado

**Nome da fila:** `ativo-alterado.fifo`

### Estrutura da Mensagem

```json
{
  "codigo": "ITAU3",
  "nome": "ITAÚ S.A.",
  "valor_Base": 100.00,
  "apta_negociacao": true
}
```
| Campo | Tipo | Descrição                                   |
|--------|------|---------------------------------------------|
| codigo | string | Código do ativo que foi criado/alterado     |
| nome | string | Nome do ativo que foi criado/alterado       |
| valor_base | decimal | Valor base do ativo que foi criado/alterado        |
| apta_negociacao | boolean | Indica se o ativo está apto para negociação |

