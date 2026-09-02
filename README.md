# ems-algashop-meta

Repositório *meta* do AlgaShop: agrega os repositórios do projeto como submódulos Git
e centraliza o ambiente de desenvolvimento.

## Estrutura

```
ems-algashop-meta
├── microservices
│   ├── ordering          → algashop-ordering          (submódulo)
│   ├── billing           → algashop-billing           (submódulo)
│   ├── billing-scheduler → algashop-billing-scheduler (submódulo)
│   └── product-catalog   → algashop-product-catalog   (submódulo)
├── docs                  → algashop-docs              (submódulo)
├── template                    projeto Spring Boot base para novos microsserviços
└── etc                         configuração dos serviços de apoio (postgres, wiremock, stub-runner)
```

| Repositório | Caminho | Descrição |
|---|---|---|
| [algashop-ordering](https://github.com/juniorpaiva95/algashop-ordering) | `microservices/ordering` | Microsserviço de pedidos |
| [algashop-billing](https://github.com/juniorpaiva95/algashop-billing) | `microservices/billing` | Microsserviço de faturamento |
| [algashop-billing-scheduler](https://github.com/juniorpaiva95/algashop-billing-scheduler) | `microservices/billing-scheduler` | Microsserviço de agendamento de cobranças |
| [algashop-product-catalog](https://github.com/juniorpaiva95/algashop-product-catalog) | `microservices/product-catalog` | Microsserviço de catálogo de produtos |
| [algashop-docs](https://github.com/juniorpaiva95/algashop-docs) | `docs` | Documentação e arquitetura |

## Clonando

```bash
git clone --recurse-submodules git@github.com:juniorpaiva95/ems-algashop-meta.git
```

Se já tiver clonado sem os submódulos:

```bash
git submodule update --init --recursive
```

## Ambiente de desenvolvimento

Os composes são separados por finalidade e o `docker-compose.yml` da raiz inclui apenas
as ferramentas de apoio — é o que se usa no dia a dia, rodando os microsserviços pela IDE:

```bash
docker compose up -d
```

| Arquivo | Contém |
|---|---|
| `docker-compose.tools.yml` | `algashop-postgres` (5432), `wiremock` (8787), `fastpay` (9995) |
| `docker-compose.services.yml` | os microsserviços em container (`ordering` 8080, `billing` 8082) + inclui as ferramentas |
| `docker-compose.yml` | inclui somente `docker-compose.tools.yml` |

Para subir também os microsserviços em container:

```bash
docker compose -f docker-compose.services.yml up -d
```

O `etc/` guarda o que esses serviços montam: o script de criação dos bancos
(`etc/postgres/init-user-db.sh`), os mappings do WireMock (`etc/wiremock`) e o
stub runner do product-catalog (`etc/stub-runner`).

## Criando um novo microsserviço

O diretório `template/` é um projeto Spring Boot base. Copie-o para um novo repositório,
ajuste `settings.gradle`, o pacote e o `README.md`, e depois registre-o aqui como submódulo:

```bash
git submodule add -b main git@github.com:juniorpaiva95/algashop-<nome>.git microservices/<nome>
git commit -m "chore: adiciona o submodulo <nome>"
```

## Trabalhando com os submódulos

Atualizar todos os submódulos para o último commit das respectivas branches:

```bash
git submodule update --remote --merge
```

Cada submódulo é um repositório independente. Para publicar uma alteração:

```bash
cd microservices/ordering
git add . && git commit -m "feat: ..." && git push

# de volta no meta, registrar o novo ponteiro
cd ../..
git add microservices/ordering
git commit -m "chore: atualiza ponteiro do submódulo ordering"
git push
```

### Ponteiros `.git` dos submódulos sumiram

Se `git submodule status` listar os caminhos com prefixo `-`, o arquivo-ponteiro `.git`
de dentro do submódulo se perdeu. Os metadados continuam em `.git/modules/`, então basta
recriar os ponteiros — nenhum arquivo de trabalho é afetado:

```bash
for m in billing billing-scheduler ordering product-catalog; do
  printf 'gitdir: ../../.git/modules/microservices/%s\n' "$m" > "microservices/$m/.git"
done
```
