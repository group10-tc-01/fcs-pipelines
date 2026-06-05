# Exemplo Real: Catalog

Esta pasta mostra como o `fcs-catalog` pode consumir todos os workflows
reutilizáveis do `fcs-pipelines` com valores alinhados aos caminhos reais do
serviço.

## Arquivos para o `fcs-catalog`

Copie estes arquivos para `.github/workflows/` no repositório `fcs-catalog`:

| Arquivo                           | Reusable utilizado            | Objetivo                                                                                                         |
| --------------------------------- | ----------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `catalog-branch-name.yml`         | `branch-name-check.yml`       | Valida o padrão de nomes das branches em pull requests.                                                          |
| `catalog-ci.yml`                  | `dotnet-service-ci.yml`       | Executa restore, build, testes, cobertura, SonarCloud, secret scan, dependency scan e validação de build Docker. |
| `catalog-delivery-aks.yml`        | `dotnet-service-delivery.yml` | Faz build, scan, push da imagem no ACR e deploy direto no AKS com rolling update.                                |
| `catalog-gitops-image-update.yml` | `gitops-image-update.yml`     | Promove uma imagem já publicada atualizando o repositório GitOps.                                                |

## Arquivos para o `fcs-orchestration`

Copie estes arquivos para o repositório `fcs-orchestration`:

| Arquivo                                              | Destino sugerido                                     | Objetivo                                                                     |
| ---------------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------- |
| `catalog-terraform-azure.yml`                        | `.github/workflows/catalog-terraform-azure.yml`      | Executa plan/apply da infraestrutura Azure com OIDC.                         |
| `k8s/overlays/production/catalog/kustomization.yaml` | `k8s/overlays/production/catalog/kustomization.yaml` | Permite que o reusable de GitOps atualize a imagem do Catalog via Kustomize. |

## Secrets e variáveis esperadas

Configure os seguintes secrets nos repositórios que executam os workflows:

- `SONAR_TOKEN`
- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_SUBSCRIPTION_ID`
- `GITOPS_TOKEN`

Configure as seguintes variáveis:

- `AZURE_CONTAINER_REGISTRY_NAME`: exemplo `fiapcr`
- `AKS_RESOURCE_GROUP`: resource group do AKS
- `AKS_CLUSTER_NAME`: nome do cluster AKS
- `CATALOG_HEALTHCHECK_URL`: URL externa de healthcheck do Catalog

## Observação sobre repositório privado

Como `fcs-pipelines` é privado, habilite o acesso dos repositórios da
organização para consumir workflows reutilizáveis deste repositório antes de
substituir os workflows atuais.
