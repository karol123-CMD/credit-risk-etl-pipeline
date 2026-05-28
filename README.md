# Credit Risk Analytics

Projeto de analise exploratoria de risco de credito desenvolvido com Excel e Power Query a partir do dataset publico **Give Me Some Credit**. O objetivo e investigar padroes associados a inadimplencia grave, documentar o tratamento dos dados e comunicar os principais achados de forma clara para contexto de portfolio.

## Visao Geral do Projeto

O projeto analisa uma base de credito ao consumidor com cerca de 150 mil registros. A variavel central e `SeriousDlqin2yrs`, que indica se o cliente apresentou atraso grave de 90 dias ou mais nos dois anos posteriores ao registro.

A proposta nao e construir um modelo preditivo. O foco esta em:

- limpar e estruturar a base;
- criar variaveis derivadas interpretaveis;
- analisar Default Rate por segmentos;
- identificar associacoes relevantes com inadimplencia;
- organizar a documentacao para leitura tecnica e executiva.

## Objetivos Analiticos

As principais perguntas investigadas foram:

- Clientes com mais eventos de atraso apresentam maior Default Rate?
- Utilizacao critica do credito rotativo esta associada a maior inadimplencia?
- Faixas de renda diferentes apresentam comportamento de risco distinto?
- Uma segmentacao simples por regras consegue separar grupos com risco observado diferente?
- A imputacao de renda afeta de forma relevante as analises descritivas?

## Metodologia Aplicada

O fluxo analitico foi construido no arquivo `02_analysis/credit_risk_analysis.xlsx`, usando Power Query para ETL e Excel para analises exploratorias, tabelas dinamicas, KPIs e visualizacoes.

Principais etapas:

- ingestao da base original em `01_data/raw/`;
- remocao de um registro invalido com `age = 0`;
- tratamento de sentinelas `96` e `98` nas colunas de atraso;
- preservacao de variaveis originais para auditoria;
- criacao de colunas tratadas, como `Renda_Mensal_Clean` e `Utilizacao_Rotativo_Clean`;
- imputacao de renda mensal pela mediana global;
- criacao de categorias de renda, idade, utilizacao e comprometimento de renda;
- construcao da segmentacao `Nivel_Risco`;
- analise de Default Rate por grupos e comparacao de multiplicadores observados.

## Principais Insights

O historico de atrasos foi o marcador com maior separacao observada. O Default Rate cresce de forma consistente conforme aumenta o numero de eventos de atraso, chegando a 57,20% no grupo `5+`.

A utilizacao critica do credito rotativo tambem apresentou forte associacao com inadimplencia. Clientes na categoria `Critica` tiveram Default Rate de 22,50%, cerca de 10,14x o valor observado na categoria `Baixa`.

A segmentacao `Nivel_Risco` organizou a carteira em tres grupos com Default Rates distintos. O grupo `Alto` representa 15,58% da base e concentra 57,26% dos inadimplentes, com Default Rate de 24,57%.

A renda apresentou associacao inversa com inadimplencia, mas essa leitura exige cautela porque 19,82% dos registros tiveram renda imputada pela mediana global.

Todos os resultados sao descritivos e observados na amostra. O projeto nao realiza inferencia causal nem validacao preditiva fora da base analisada.

## Estrutura do Projeto

```text
├── 01_data/
│   ├── raw/
│   │   └── samples/
│
├── 02_analysis/
│   └── credit_risk_analysis.xlsx
│
├── 03_docs/
│   ├── data_dictionary.md
│   ├── methodology.md
│   └── insights.md
│
├── 04_visuals/
│   ├── charts/
│   └── deck/
│
├── .gitignore
└── README.md
```

| Caminho | Proposito |
|---|---|
| `01_data/raw/` | Base original preservada. |
| `01_data/raw/samples/` | Amostras ou arquivos auxiliares da fonte. |
| `02_analysis/credit_risk_analysis.xlsx` | Arquivo principal com ETL, analises, KPIs e dashboard. |
| `03_docs/data_dictionary.md` | Dicionario das variaveis finais e regras de tratamento. |
| `03_docs/methodology.md` | Metodologia aplicada no ETL e na analise exploratoria. |
| `03_docs/insights.md` | Principais achados, KPIs e limitacoes. |
| `04_visuals/charts/` | Graficos exportados do workbook. |
| `04_visuals/deck/` | Materiais de apresentacao. |

## Ferramentas Utilizadas

- **Excel**: ambiente central de analise e visualizacao.
- **Power Query**: ETL, limpeza, tipagem e criacao de variaveis derivadas.
- **Tabelas Dinamicas**: exploracao, segmentacoes e KPIs.
- **Markdown**: documentacao tecnica e executiva.

## Consideracoes Finais

Este projeto demonstra um fluxo completo de analise de dados aplicado a risco de credito: tratamento da base, criacao de variaveis interpretaveis, analise exploratoria e comunicacao dos resultados.

A principal preocupacao metodologica foi manter rastreabilidade. As variaveis originais foram preservadas quando necessario, as colunas tratadas foram documentadas separadamente e os resultados foram apresentados com ressalvas sobre escopo, imputacao e limites de interpretacao.
