# Lake Flow Declarative Pipelines (LDP)

## 📖 Visão Geral

O **Lake Flow Declarative Pipelines (LDP)** (anteriormente conhecido como *Delta Live Tables* ou DLT) é uma estrutura de ETL baseada no Apache Spark projetada para criar pipelines de dados confiáveis e de fácil manutenção.

Diferente da abordagem imperativa padrão do Spark, o LDP utiliza um modelo **declarativo**: você define o resultado desejado das transformações (o "o quê") e o framework gerencia a complexidade da execução, orquestração e infraestrutura (o "como").

> **Nota:** A Databricks abriu o código-fonte desta solução, integrando-a ao ecossistema Apache Spark sob o nome **Spark Declarative Pipelines**.

---

## 🚀 Principais Benefícios

* **Abstração de Complexidade:** Elimina a necessidade de gerenciar detalhes de baixo nível do Spark (checkpoints, escritas de stream).
* **Orquestração Automática:** Identifica dependências entre tabelas e visualiza o fluxo de execução (DAG) automaticamente.
* **Gerenciamento Autônomo:** Lida nativamente com *checkpoints*, novas tentativas (retries) e otimização de performance.
* **Operações Avançadas Simplificadas:** Suporte nativo e facilitado para CDC (Change Data Capture), SCD Tipo 2 e verificações de Qualidade de Dados (Expectations).
* **Validação:** Suporte a "Dry Run" para validar a lógica do pipeline sem processar dados.

---

## 🧱 Tipos de Objetos no LDP

O LDP permite a definição de três tipos principais de objetos, tanto em Python quanto em SQL:

| Tipo de Objeto | Persistência | Comportamento de Processamento | Caso de Uso Ideal |
| --- | --- | --- | --- |
| **Streaming Table** | Sim (Catálogo) | **Incremental.** Processa apenas novos dados desde a última execução (append-only). | Ingestão quase em tempo real (baixa latência/ms), leitura de Kafka/Autoloader. |
| **Materialized View** | Sim (Catálogo) | **Atualização Completa** (geralmente). Recarrega/substitui dados a cada execução.* | Agregações complexas, Joins, dados que sofrem updates/deletes na origem. Latência de segundos/minutos. |
| **Temporary View** | Não (Efêmera) | Processamento temporário durante a execução do pipeline. | Transformações intermediárias e verificações de qualidade que não precisam ser salvas. |

**Nota: Em computação Serverless, algumas Materialized Views podem ser otimizadas para atualizações incrementais.*

---

## ⚡ LDP vs. Apache Spark Tradicional

### Sintaxe Python

* **Spark:** Exige definição explícita de `readStream`, `writeStream` e gestão manual de caminhos de checkpoint.
* **LDP:** Utiliza **decoradores** (`@table` ou `@dlt.table`). A escrita e o checkpoint são abstraídos pelo framework.

### Sintaxe SQL

* **Spark:** Não suporta nativamente a criação de pipelines de streaming apenas com SQL (exige API PySpark).
* **LDP:** Suporte nativo total via SQL:
```sql
CREATE OR REFRESH STREAMING TABLE nome_tabela AS SELECT ...

```



---

## 🛠️ Mudanças da Estrutura Antiga (DLT)

* **Arquivos:** Migração de base exclusiva em Notebooks para suporte a arquivos de script (`.py`, `.sql`).
* **Validação:** Introdução do conceito de execução de teste ("Dry Run") para validação de código.
* **Sintaxe:** Evolução dos decoradores e comandos para maior clareza entre objetos de streaming e estáticos.

---

### Próximo passo

Você gostaria de ver um exemplo prático de código convertendo um pipeline **PySpark padrão** para a sintaxe **LDP (Python)** para visualizar as diferenças?