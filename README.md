# ems-algashop-meta

Repositório *meta* do AlgaShop: agrega os repositórios do projeto como submódulos Git
e centraliza o ambiente de desenvolvimento.

## Estrutura

```
ems-algashop-meta
├── microservices
│   ├── ordering        → algashop-ordering        (submódulo)
│   ├── billing         → algashop-billing         (submódulo)
│   └── product-catalog → algashop-product-catalog (submódulo)
└── docs                → algashop-docs            (submódulo)
```

| Repositório | Caminho | Descrição |
|---|---|---|
| [algashop-ordering](https://github.com/juniorpaiva95/algashop-ordering) | `microservices/ordering` | Microsserviço de pedidos |
| [algashop-billing](https://github.com/juniorpaiva95/algashop-billing) | `microservices/billing` | Microsserviço de faturamento |
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
