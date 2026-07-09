# Delivery de Imagens no Coolify

O workflow `coolify-image-delivery.yml` cria uma imagem imutavel no GHCR, executa
o scan Trivy, atualiza a referencia da imagem no recurso Coolify e dispara o
redeploy pelo webhook configurado.

## Contrato

Inputs obrigatorios:

- `app_name`: identificador curto da aplicacao.
- `dockerfile_path`: caminho do Dockerfile.

Inputs normalmente usados pelos wrappers:

- `image_registry: ghcr.io`, `image_owner: group10-tc-01` e `image_name` para a imagem publicada.
- `coolify_base_url: ${{ vars.COOLIFY_BASE_URL }}`.
- `coolify_resource_uuid: ${{ vars.COOLIFY_RESOURCE_UUID }}`.
- `source_ref: ${{ github.event.workflow_run.head_sha }}` quando acionado por `workflow_run`.

Por padrao, o PATCH envia o nome e a tag em campos separados, compativeis com
aplicacoes Docker Image do Coolify:

```json
{
  "docker_registry_image_name": "__IMAGE_NAME__",
  "docker_registry_image_tag": "__IMAGE_TAG__"
}
```

O workflow publica a tag `sha-<commit-curto>`. O Coolify deve estar configurado
como aplicacao de Docker Image e ter permissao para baixar a imagem privada do GHCR.

## Configuracao no repositorio da aplicacao

Crie as seguintes variables de repositorio ou do environment de producao:

| Nome                    | Finalidade                                                      |
| ----------------------- | --------------------------------------------------------------- |
| `COOLIFY_BASE_URL`      | URL base do Coolify, por exemplo `https://coolify.example.com`. |
| `COOLIFY_RESOURCE_UUID` | UUID da aplicacao Docker Image no Coolify.                      |

Crie os seguintes secrets no mesmo escopo:

| Nome              | Finalidade                                                        |
| ----------------- | ----------------------------------------------------------------- |
| `COOLIFY_TOKEN`   | Token de API do Coolify com permissao para atualizar a aplicacao. |
| `COOLIFY_WEBHOOK` | Webhook de deploy gerado pelo recurso Coolify.                    |

Para GHCR, o workflow usa o `GITHUB_TOKEN` e exige `packages: write`. Registros
que nao sejam GHCR exigem tambem `REGISTRY_USERNAME` e `REGISTRY_TOKEN`.

## Fluxo de deploy

1. O CI da `main` termina com sucesso.
2. O wrapper chama este workflow com o SHA validado.
3. A imagem `ghcr.io/group10-tc-01/<app>:sha-<sha>` e publicada e escaneada.
4. A API do Coolify recebe a nova imagem e o webhook inicia o deploy.

Um `workflow_dispatch` nao informa `source_ref`; nesse caso, o checkout usa a
branch/ref selecionada na execucao manual.
