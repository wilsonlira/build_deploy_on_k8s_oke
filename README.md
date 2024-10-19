# CI/CD com Docker e Deploy no Oracle Kubernetes Engine (OKE)

Este repositório contém pipelines automatizadas para a construção, versionamento e deploy de imagens Docker no Oracle Kubernetes Engine (OKE). Abaixo estão descritos os principais workflows e instruções para o funcionamento das pipelines.

## Estrutura das Pipelines

### 1. **Pipeline de Build e Push de Imagem Docker**
Este workflow é responsável por versionar automaticamente, construir e publicar as imagens Docker do serviço `pagamentos`.

- **Disparo:**
  - Ao abrir um pull request ou push na branch `main` que contenha alterações na pasta `pagamentos/`.
  - Manualmente via `workflow_dispatch`.

- **Etapas Principais:**
  1. **Checkout do Código**: Faz o checkout do repositório para garantir que o código esteja disponível na pipeline.
  2. **Configuração do Buildx**: Configura o Docker Buildx para permitir builds multiplataforma.
  3. **Login no Docker Hub**: Autentica no Docker Hub usando as credenciais configuradas nos secrets.
  4. **Versionamento Semântico**: Gera uma nova versão baseada no último patch, incrementando automaticamente.
  5. **Commit da Nova Versão**: Atualiza o arquivo `VERSION` no repositório e comita a nova versão.
  6. **Build e Push da Imagem**: Constrói e envia a imagem Docker para o Docker Hub com as tags de versão e `latest`.
  7. **Scan de Vulnerabilidades**: Utiliza o Snyk para escanear a imagem Docker em busca de vulnerabilidades.

### 2. **Pipeline de Deploy no OKE**
Este workflow realiza o deploy da aplicação `pagamentos` no cluster Oracle Kubernetes Engine (OKE).

- **Disparo:**
  - Automático após a conclusão bem-sucedida da pipeline de build e push de imagem Docker.

- **Etapas Principais:**
  1. **Checkout do Código**: Faz o checkout do repositório para garantir que a configuração necessária esteja disponível.
  2. **Configuração do Kubectl**: Autentica no OKE e configura o `kubectl` para operar com o cluster.
  3. **Deploy**: Atualiza a imagem da aplicação no cluster Kubernetes com a versão `latest` e verifica o status do rollout.

## Secrets e Variáveis

As pipelines utilizam **secrets** e **variáveis de ambiente** para garantir a segurança e flexibilidade:

- **DOCKER_HUB_USERNAME**: Nome de usuário do Docker Hub.
- **DOCKER_HUB_PASSWORD**: Senha do Docker Hub.
- **OCI_CLI_USER**: ID do usuário OCI para autenticação.
- **OCI_CLI_TENANCY**: Tenancy OCID no Oracle Cloud.
- **OCI_CLI_FINGERPRINT**: Impressão digital da chave OCI.
- **OCI_CLI_KEY_CONTENT**: Conteúdo da chave privada OCI.
- **OKE_CLUSTER_OCID**: OCID do cluster no Oracle Kubernetes Engine.
- **SNYK_TOKEN**: Token para autenticação no Snyk para scans de vulnerabilidades.

## Como Executar as Pipelines

### Build e Push
A pipeline de build e push é automaticamente acionada ao realizar um push ou pull request na branch `main` com alterações na pasta `pagamentos/`. Ela também pode ser executada manualmente pelo `workflow_dispatch` na interface do GitHub Actions.

### Deploy no OKE
A pipeline de deploy é automaticamente disparada após o sucesso da pipeline de build e push, garantindo que a imagem Docker mais recente seja implantada no cluster Kubernetes.
