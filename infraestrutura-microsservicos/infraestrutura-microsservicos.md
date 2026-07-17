# Infraestrutura dos Microsserviços
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonwebservices&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)

## Contexto

A plataforma de negociação de ativos é composta por múltiplos microsserviços desenvolvidos em diferentes tecnologias. Para atender a essa diversidade, foi projetada uma esteira de CI/CD única que suporta tanto aplicações **Java com Spring Boot** quanto **.NET 8**, garantindo que cada integrante da equipe possa escolher a tecnologia mais adequada para seu domínio sem perder a padronização operacional.

Independentemente da linguagem, todos os microsserviços compartilham os mesmos padrões:
- **Versionamento semântico automático** no formato `0.<release>.<commits>`
- **Containerização com Docker** para garantir portabilidade entre ambientes
- **Deploy manual** com rollback facilitado
- **Rede compartilhada** na EC2 para comunicação entre serviços

---

## Artefatos de Infraestrutura

Os arquivos de infraestrutura estão organizados em duas pastas conforme a tecnologia:

- [`/artefatos-microsservicos-java`](./artefatos-microsservicos-java/) - Artefatos para aplicações Spring Boot
- [`/artefatos-microsservicos-dotnet`](./artefatos-microsservicos-dotnet/) - Artefatos para aplicações .NET 8

Cada pasta contém:

- **Dockerfile**: Define a imagem base (JDK 26 para Java, ASP.NET 8 para .NET), copia o artefato gerado (JAR ou DLL) e configura o entrypoint para execução da aplicação.
- **docker-compose.yml**: Orquestra o container, definindo o nome, a porta interna (8080), a rede compartilhada e a política de reinicialização.
- **Workflows**:
    - `release.yml`: Automatiza a criação de tags e releases a partir da branch `main`.
    - `deploy.yml`: Executa o build, containerização e deploy na EC2.

Embora os workflows sigam a mesma estrutura lógica, o `deploy.yml` do .NET possui etapas específicas para o ecossistema da linguagem, como `dotnet restore` e `dotnet publish`, além da derivação automática do nome da DLL a partir do caminho do projeto.

---

## Fluxo de CI/CD

- **Build**: a etapa de build ocorre dentro do workflow de deploy e transforma o código fonte em um artefato executável. O processo varia conforme a tecnologia, mas o resultado é sempre um único artefato pronto para ser containerizado.

- **Release**: o workflow de release é acionado manualmente pela equipe e automatiza a criação de versões, seguindo um padrão semântico simples e autoincrementável. A versão gerada é composta por três partes:
  - **0**: Fixo, indicando versões iniciais do projeto
  - **Release**: Número incrementado automaticamente com base nas tags existentes
  - **Commits**: Total de commits do repositório

- **Deploy**: o deploy é o processo de publicação de uma versão específica na EC2. Diferente da release, o deploy é acionado manualmente e requer que o usuário informe a versão desejada, garantindo controle total sobre o que está em produção. O workflow realiza as seguintes etapas:
  1. Checkout do código da tag informada
  2. Build da aplicação (Gradle para Java, dotnet para .NET)
  3. Build da imagem Docker
  4. Exportação da imagem como `image.tar`
  5. Transferência dos arquivos para a EC2 via SCP
  6. Deploy remoto: carregamento da imagem, criação do `.env` com secrets, recriação do container e reinicialização do Nginx

- **Rollback**: o rollback utiliza o mesmo fluxo do deploy, bastando informar uma versão anterior. Por exemplo, se a versão `0.6.7` apresentar problemas, executa-se o workflow de deploy informando `0.5.6` para reativar a versão estável. Esse mecanismo é possível porque todas as versões são imutáveis (cada tag gera uma imagem única) e permanecem disponíveis para deploy a qualquer momento.

---

## Gestão de Secrets

As credenciais sensíveis nunca são versionadas no código fonte, sendo armazenadas como **GitHub Secrets** e injetadas dinamicamente durante o deploy através da criação do arquivo `.env` na EC2.

As secrets obrigatórios para todos os microsserviços são:

| Secret | Descrição |
|--------|-----------|
| `EC2_HOST` | IP público ou DNS da instância EC2 |
| `EC2_USER` | Usuário SSH para acesso à EC2 |
| `EC2_SSH_KEY` | Chave privada SSH (formato PEM) |
| `DB_URL` | URL de conexão com o PostgreSQL |
| `DB_USERNAME` | Usuário do banco de dados |
| `DB_PASSWORD` | Senha do banco de dados |
| `AWS_REGION` | Região AWS |
| `SQS_ATIVO_ALTERADO_URL` | URL da fila FIFO de alteração de ativos |
| `SQS_OPERACAO_REGISTRADA_URL` | URL da fila FIFO de operações registradas |

---