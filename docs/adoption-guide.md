# Guia de Adoção

Este guia mostra como adotar o repositório central de pipelines nos repositórios das aplicações.

## 1. Mantenha wrappers de workflow em cada aplicação

Cada aplicação deve manter apenas arquivos simples em `.github/workflows/*.yml`. A implementação real fica neste repositório e é referenciada com `uses:`.

Como o `fcs-pipelines` é privado, habilite o acesso para os repositórios da organização antes da adoção:

- Configurações do repositório `fcs-pipelines`
- Actions
- General
- Access
- Allow repositories in the organization to use these reusable workflows

Use `examples/` como base para os wrappers de cada repositório.

## 2. Configure GitHub Environments

Crie pelo menos um ambiente protegido chamado `production` em cada repositório que faz deploy.

Proteções recomendadas:

- Revisores obrigatórios para aprovação manual ao fazer deploy em produção.
- Restrição de branch para `main`.
- Secrets de ambiente para acesso à cloud e tokens de produto.

## 3. Use Azure OIDC em vez de client secrets

Crie uma aplicação no Azure AD com credenciais federadas para cada repositório ou para a organização. Armazene apenas os IDs nos secrets do GitHub:

- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_SUBSCRIPTION_ID`

Nenhum client secret do Azure é necessário quando o OIDC está configurado corretamente.

## 4. Use Kubernetes ou cofres gerenciados em runtime

Segredos da aplicação não devem ser commitados no código-fonte nem nos arquivos de workflow.

Fontes recomendadas em runtime:

- Kubernetes Secrets
- Azure Key Vault com workload identity
- Managed identities

Exemplos de valores sensíveis:

- Strings de conexão SQL
- Credenciais de MongoDB
- Credenciais SASL do Kafka
- Chaves de assinatura JWT
- Tokens de API

## 5. Adoção em serviços .NET

Serviços .NET devem usar `dotnet-service-ci.yml` para CI e, quando houver deploy, `dotnet-service-delivery.yml`.

Gates recomendados:

- Política de nome de branch
- Restore
- Build
- Testes unitários
- Testes integrados quando disponíveis
- Testes funcionais quando disponíveis
- Limite mínimo de cobertura
- Secret scan
- Análise de vulnerabilidades em dependências
- Validação de build Docker
- SonarCloud analysis quando configurado

## 6. Adoção em aplicações Angular

Aplicações Angular devem usar dois wrappers:

- `branch-name-check.yml`, para o ruleset exigir o check de nome de branch de forma independente do CI principal.
- `angular-web-ci.yml`, para os gates de qualidade, segurança, build e Docker.

Use o workflow reutilizável `.github/workflows/branch-name-check.yml` para a branch policy e `examples/solidarity-web-ci.yml` como referência para o CI Angular.

Pré-requisitos no repositório da aplicação:

- `package-lock.json` versionado.
- Scripts `format:check`, `lint`, `test:ci` e `build` no `package.json`.
- `@vitest/coverage-v8` instalado quando `run_coverage` estiver habilitado.
- `Dockerfile` quando `dockerfile_path` for informado.

Gates executados:

- Política de nome de branch
- Secret scan com Gitleaks
- `npm audit --audit-level=high`
- Verificação de formatação
- Lint
- Testes unitários
- Cobertura mínima
- Build Angular
- Validação de build Docker

Use Node 24 como padrão para aplicações Angular 21.

## 7. Prefira GitOps para produção

Para a arquitetura final, mantenha os manifests Kubernetes no repositório de infraestrutura e deixe Argo CD ou Flux reconciliar as mudanças no AKS.

O workflow reutilizável `gitops-image-update.yml` atualiza referências de imagem em um kustomization. O deploy direto no AKS continua disponível para uma demonstração simples de live deploy.
