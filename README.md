# Análise exploratória de risco de crédito com Excel

Projeto de ETL e análise exploratória do dataset **Give Me Some Credit**, desenvolvido principalmente com Excel e Power Query. O trabalho documenta a preparação de dados, a criação de grupos descritivos e a comparação de taxas observadas — não é um modelo preditivo nem uma ferramenta de decisão de crédito.

> **Situação do material visual:** não exibo a imagem do dashboard nesta página porque o arquivo disponível contém filtros ativos e recomendações direcionais por idade/renda, além de indicadores ainda não reconciliados. A imagem foi mantida sem alteração; consulte-a somente como material de trabalho até revisão no Excel.

## Em 30 segundos

A análise parte dos arquivos do desafio Give Me Some Credit, prepara a base no Power Query e organiza os resultados em tabelas dinâmicas, indicadores e um dashboard no Excel. A tabela final tem 149.999 registros, após a exclusão de uma linha com idade igual a zero. Na amostra, o grupo de regras `Alto` teve taxa observada de inadimplência de 24,57%, ante 1,66% no grupo `Baixo`. Esses valores são descritivos e não estimam a probabilidade individual de inadimplência.

## Pergunta e escopo

O projeto explora como o histórico de atrasos, a utilização do crédito rotativo, a renda e uma segmentação simples por regras se relacionam com `SeriousDlqin2yrs`, indicador de atraso grave nos dois anos seguintes ao registro. A análise busca organizar os dados e comunicar diferenças observadas; não testa causalidade, não treina um modelo e não foi validada para concessão de crédito.

## Dados e ferramentas

- **Fonte:** [Give Me Some Credit no Kaggle](https://www.kaggle.com/c/GiveMeSomeCredit), competição de 2011. Os termos específicos de redistribuição dos arquivos não foram confirmados nesta revisão; consulte a página e as regras antes de reutilizar os dados.
- **Excel e Power Query (linguagem M):** importação, tipagem, tratamento e variáveis derivadas.
- **Tabelas dinâmicas, fórmulas, segmentações e gráficos:** exploração e apresentação no workbook.
- **Markdown:** metodologia, dicionário e interpretação dos resultados.

## Fluxo do trabalho

`CSV de treinamento → RAW_CreditData → CLEAN_pipeline → tabelas dinâmicas e KPIs → dashboard`

O tratamento documentado inclui remover o único registro com `age = 0`, tratar os códigos 96 e 98 nas três colunas de atraso como ausentes, preservar as colunas originais relevantes, imputar a renda ausente pela mediana global com uma flag de rastreabilidade e tratar valores de utilização acima de 2 como ausentes para as categorias analíticas.

## Resultados de referência

Os números abaixo foram calculados a partir da tabela armazenada `CLEAN_pipeline` e conferidos com o CSV do desafio nesta revisão. A apresentação atual do workbook não pôde ser atualizada ou validada no Excel; por isso, estes números não devem ser confundidos com uma reconciliação dos pivôs e visuais atualmente salvos.

- 150.000 linhas na origem e 149.999 após o filtro de idade inválida;
- 10.026 registros positivos em `Inadimplente` (6,684% da base final);
- 29.731 registros com renda imputada (19,82%);
- a taxa observada no grupo de atraso `5+` é 57,20%;
- a segmentação por regras `Nivel_Risco` contém 23.366 registros no grupo `Alto` (15,58% da base), com taxa observada de 24,57% e 57,26% dos registros positivos.

Essas comparações descrevem esta amostra. Não demonstram causalidade nem desempenho fora dela.

## Uso responsável e limitações

Este é um estudo exploratório. `Nivel_Risco` é uma segmentação descritiva baseada em regras, não um modelo preditivo ou score calibrado. Os resultados não devem ser usados isoladamente para aprovar, negar ou precificar crédito. Idade, renda e variáveis que possam funcionar como proxies exigem avaliação de viés, justiça e conformidade antes de qualquer uso decisório. A renda imputada em 19,82% dos registros também limita a interpretação por faixa.

## Abrir e atualizar

Abra [`credit_risk_analysis.xlsx`](02_analysis/credit_risk_analysis.xlsx) em uma versão desktop recente do Microsoft Excel para Windows com Power Query. Para atualizar, configure a consulta de origem para apontar ao `cs-training.csv` obtido na fonte, atualize as consultas auxiliares e depois `CLEAN_pipeline`; ao final, atualize as tabelas dinâmicas e confira os totais com a metodologia.

**Reprodutibilidade ainda pendente:** a configuração atual contém caminho local para o CSV e uma consulta de log que depende de um arquivo externo não versionado. As consultas não foram atualizadas nesta revisão. As etapas para corrigir e testar isso estão descritas em [`methodology.md`](03_docs/methodology.md); portanto, não afirmo que a atualização seja portátil neste momento.

## Materiais do projeto

- [Planilha principal](02_analysis/credit_risk_analysis.xlsx)
- [Dicionário de dados](03_docs/data_dictionary.md)
- [Metodologia e consultas](03_docs/methodology.md)
- [Insights e números de referência](03_docs/insights.md)
- [Guia para entrevista](03_docs/interview-guide.md)
- [Apresentação em PDF](04_visuals/deck/Credit-Risk-Analytics.pdf)
- [Imagem atual do dashboard — não reconciliada](04_visuals/charts/Dashboard.png)

O PDF e a imagem são mantidos como materiais existentes; não foram regenerados nesta revisão, pois não havia uma fonte editável disponível para assegurar atualização fiel aos números reconciliados.

## Estrutura

```text
01_data/raw/       arquivos do pacote original do desafio
02_analysis/       workbook de análise
03_docs/           dicionário, metodologia, insights e guia de entrevista
04_visuals/        imagem do dashboard e apresentação em PDF
```

`cs-test.csv` e `sampleEntry.csv` pertencem ao pacote original do desafio e não são usados na análise exploratória apresentada. `Data Dictionary.xls` é o dicionário fornecido junto ao material original; `03_docs/data_dictionary.md` é a versão de consulta direta no GitHub. Os arquivos foram mantidos, sem declaração de licença própria para os dados de terceiros.

