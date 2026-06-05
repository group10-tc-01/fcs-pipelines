# Secrets e Variáveis Obrigatórias

Use GitHub Environments para limitar o escopo das credenciais de deploy. Evite
secrets globais no repositório para produção sempre que possível.

## Secrets da organização ou do repositório

| Nome                    | Usado por                                            | Finalidade                                            |
| ----------------------- | ---------------------------------------------------- | ----------------------------------------------------- |
| `SONAR_TOKEN`           | `dotnet-service-ci.yml`                              | Token de análise do SonarCloud.                       |
| `AZURE_CLIENT_ID`       | `dotnet-service-delivery.yml`, `terraform-azure.yml` | Client ID da aplicação Azure AD para OIDC.            |
| `AZURE_TENANT_ID`       | `dotnet-service-delivery.yml`, `terraform-azure.yml` | Tenant ID do Azure.                                   |
| `AZURE_SUBSCRIPTION_ID` | `dotnet-service-delivery.yml`, `terraform-azure.yml` | Subscription ID do Azure.                             |
| `GITOPS_TOKEN`          | `gitops-image-update.yml`                            | Token com permissão de escrita no repositório GitOps. |

## Variáveis sugeridas no GitHub

| Nome                            | Exemplo         | Finalidade                    |
| ------------------------------- | --------------- | ----------------------------- |
| `AZURE_CONTAINER_REGISTRY_NAME` | `Fcsregistry`   | Nome do ACR sem `azurecr.io`. |
| `AKS_RESOURCE_GROUP`            | `rg-fcs-prod`   | Resource group do AKS.        |
| `AKS_CLUSTER_NAME`              | `aks-fcs-prod`  | Nome do cluster AKS.          |
| `K8S_NAMESPACE`                 | `fcs`           | Namespace do Kubernetes.      |
| `SONAR_ORGANIZATION`            | `group10-tc-01` | Organização do SonarCloud.    |

## Segredos da aplicação em runtime

Não armazene estes valores em arquivos de workflow nem em arquivos `appsettings`
commitados no controle de versão:

- Strings de conexão de banco de dados
- Credenciais de MongoDB ou DynamoDB
- Strings de conexão Redis
- Credenciais de OpenSearch ou Elasticsearch
- Usuário e senha SASL do Kafka
- Chaves de assinatura JWT

Use Kubernetes Secrets ou integração com Azure Key Vault para injetar esses
valores em runtime.
