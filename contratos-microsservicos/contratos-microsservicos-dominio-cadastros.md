# Contrato de Microsserviços | Domínio de Cadastros

Os microsserviços do domínio de cadastros da plataforma expõem endpoints REST para gerenciamento de ativos, corretoras, investidores. Todas as requisições são roteadas através do Nginx, que atua como proxy reverso e centraliza o acesso às APIs.

**Base URL:** `http://34.204.232.188`

**Postman com todas as requisições de API:** [`Plataforma Negociação de Ativos.postman_collection.json`](Plataforma%20Negociação%20de%20Ativos.postman_collection.json)

## 1. API de Ativos

Responsável pelo gerenciamento dos ativos financeiros disponíveis para negociação.

**Base Path:** `/cadastro-ativos-api/v1/ativos`


| Método | Endpoint | Descrição                         |
|--------|----------|-----------------------------------|
| GET | `/` | Lista todos os ativos cadastrados |
| GET | `/{codigo}` | Busca um ativo pelo código        |
| POST | `/` | Cria um novo ativo                |
| PUT | `/{codigo}` | Atualiza um ativo existente       |
| DELETE | `/{codigo}` | Inativa um ativo                  |

### Estrutura do Ativo

```json
{
  "codigo": "PETR4",
  "nome": "Petroleo Brasileiro SA Petrobras",
  "valorBase": 35.50,
  "indexador": 0.13,
  "aptaNegociacao": true,
  "quantidade": 1000
}
````

| Campo  | Tipo        | Descrição                                        |
|--------|-------------|--------------------------------------------------|
| codigo    | string      | Código único do ativo                            |
| nome      | string      | Razão social da empresa  do ativo correspondente |
| valorBase | decimal     | Valor base para operação do ativo                |
| indexador | decimal     | Percentual de valorização/desvalorização do ativo |
| aptaNegociacao | boolean | Indica se o ativo está disponível para negociação |
| quantidade | integer    | Quantidade disponível do ativo                   |


## 2. API de Corretoras

Responsável pelo gerenciamento das corretoras participantes da plataforma.

**Base Path:** `/cadastro-corretora-api/v1/corretoras`

| Método | Endpoint | Descrição                          |
|--------|----------|------------------------------------|
| GET | `/` | Lista todas as corretoras cadastradas |
| GET | `/{codigo}` | Busca uma corretora pelo código    |
| POST | `/` | Cria uma nova corretora            |
| PUT | `/{codigo}` | Atualiza uma corretora existente   |
| DELETE | `/{codigo}` | Inativa uma corretora            |

### Estrutura da Corretora

```json
{
  "nome": "Itau Corretora",
  "razaoSocial": "Itau Corretora de Valores S.A.",
  "cnpj": "60.872.504/0001-23",
  "aptaNegociacao": true
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| nome | string | Nome comercial da corretora |
| razaoSocial | string | Razão social completa |
| cnpj | string | CNPJ da corretora |
| aptaNegociacao | boolean | Indica se a corretora está apta para negociação |


## 3. API de Investidores
   Responsável pelo gerenciamento dos investidores que realizam operações na plataforma.

**Base Path:** `/cadastro-investidor-api/v1/investidores`

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/` | Lista todos os investidores cadastrados |
| GET | `/{codigo}` | Busca um investidor pelo código |
| POST | `/` | Cria um novo investidor |
| PUT | `/{codigo}` | Atualiza um investidor existente |
| DELETE | `/{codigo}` | Remove um investidor |

### Estrutura do Investidor

```json
{
  "nome": "Vitoria Moreira Bentes",
  "cpf": "45528076067",
  "aptaNegociacao": true
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| nome | string | Nome do investidor |
| cpf | string | CPF do investidor |
| aptaNegociacao | boolean | Indica se o investidor está apto para negociar |

