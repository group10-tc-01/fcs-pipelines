# FCS Pipelines

O `fcs-pipelines` centraliza os workflows reutilizáveis de CI e delivery da
Conexão Solidária. Os repositórios de aplicação usam wrappers pequenos para
consumir essas automações.

## Responsabilidades

- validação de branch, qualidade, testes e segurança
- build e publicação de imagens no GHCR
- entrega no K3s por túnel SSH privado
- gates do GitHub Environment `production`

Veja as interfaces disponíveis no [README](../README.md).
