# Projeto ESDUG DABs - Espírito Santo Databricks User Group

Bem-vindo ao projeto **esdug_dabs**! Este projeto é um exemplo prático desenvolvido para o Espírito Santo Databricks User Group (ESDUG) demonstrando o uso e o poder dos **Databricks Asset Bundles (DABs)** na Engenharia de Dados. 

O projeto implementa uma pipeline de dados utilizando a **Arquitetura Medallion** (Bronze, Silver e Gold) para análise de reservas.

## Estrutura do Projeto

A organização de pastas e os principais artefatos definidos neste bundle são:

* `databricks.yml`: O arquivo de configuração principal do Asset Bundle, onde o projeto e seus targets (ambientes) são definidos.
* `resources/`: Contém todas as configurações de recursos do Databricks geridos por este bundle:
  * `resources/job/job_reservations_analytics.yml`: Define a orquestração do Job Databricks que executa a pipeline medallion (tasks: `bronze` -> `silver` -> `gold`). 
  * `resources/env/`: Configurações específicas para cada ambiente (`dev.yml` e `prd.yml`).
  * `resources/variables/`: Definição de variáveis interligadas aos recursos (`variables.yml`), isolando nomes dos catálogos (`bronze_catalog`, `silver_catalog`, `gold_catalog`).
* `src/notebooks/`: Armazena o código fonte executável da pipeline (notebooks PySpark).

## Arquitetura da Pipeline (Reservations Analytics)

O fluxo automatizado de processamento de dados do job `job_reservations_analytics` está dividido em três etapas dependentes:

1. **Camada Bronze:** Executa `src/notebooks/bronze.ipynb`. Ingestão de dados a partir de uma origem (`samples`) para o catálogo alvo da etapa Bronze.
2. **Camada Silver:** Executa `src/notebooks/silver.ipynb`. Tratamento, estruturação e refino dos dados da etapa Bronze.
3. **Camada Gold:** Executa `src/notebooks/gold.ipynb`. Aplicação das regras de negócio, modelagem e agregações finais para as tomadas de decisões do time analítico.

## Começando com o Projeto

### Autenticação

Configure a integração entre a **Databricks CLI** (comando `databricks`) e o seu workspace do Databricks, caso não tenha logado anteriormente:
```bash
databricks configure
```

### Desenvolvimento com IDEs (VS Code, Cursor)

Para maior produtividade, recomendada-se o uso de dependências localmente. Caso faça uso do `uv`:
```bash
uv sync --dev
```

### Executando Deploys (Implantação de Recursos)

Utilize a CLI para construir seu pacote e efetuar a implantação das configurações e notebooks no workspace no ambiente de desenvolvimento:
```bash
databricks bundle deploy --target dev
```
*(Nota: O "dev" foi configurado neste bundle como o target padrão. Rodar apenas `databricks bundle deploy` também surtirá efeito enviando para dev).*

O deploy carregará os pipelines com os respectivos prefixos e de acordo com as variáveis pré-configuradas no seu `dev.yml`.

### Executando os Jobs via Bundle

Assim que efetuado o deploy, a orquestração do job já se encontra no ar (com base nos recursos definidos nos YAMLs) para ser testada:
```bash
databricks bundle run job_reservations_analytics --target dev
```

### Deploy no Ambiente de Produção

Quando seu desenvolvimento está concluído e pronto pra implantação nos ambientes produtivos, os comandos se mantêm homogêneos, bastando apenas alterar seu target parameter:
```bash
databricks bundle deploy --target prod
databricks bundle run job_reservations_analytics --target prod
```
