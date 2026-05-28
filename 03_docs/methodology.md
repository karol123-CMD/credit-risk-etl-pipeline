# Metodologia

## Analise de Risco de Credito - Give Me Some Credit

> **Dataset:** Give Me Some Credit - Kaggle  
> **Arquivo principal:** `02_analysis/credit_risk_analysis.xlsx`  
> **Ferramentas:** Excel, Power Query, Tabelas Dinamicas e Markdown  
> **Escopo:** ETL, analise exploratoria, segmentacoes e KPIs descritivos

Este documento descreve a metodologia aplicada no projeto. O objetivo foi construir uma analise exploratoria de risco de credito com boa rastreabilidade, sem criar modelo preditivo nem inferencias causais.

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

| Pasta/arquivo | Finalidade |
|---|---|
| `01_data/raw/` | Armazena a base original preservada. |
| `01_data/raw/samples/` | Amostras ou arquivos auxiliares da fonte, quando aplicavel. |
| `02_analysis/credit_risk_analysis.xlsx` | Workbook principal com Power Query, tabelas de analise, KPIs e dashboard. |
| `03_docs/` | Documentacao tecnica e analitica do projeto. |
| `04_visuals/charts/` | Graficos exportados para apresentacao e README. |
| `04_visuals/deck/` | Materiais de apresentacao, quando houver. |

## Fonte de Dados

O projeto utiliza o dataset publico **Give Me Some Credit**, disponibilizado no Kaggle. A base contem informacoes de credito ao consumidor, com aproximadamente 150 mil registros e uma variavel-alvo binaria:

`SeriousDlqin2yrs`: indica se o cliente apresentou inadimplencia grave, definida como atraso de 90 dias ou mais em alguma obrigacao financeira nos dois anos posteriores ao registro.

No pipeline final, essa variavel foi renomeada para `Inadimplente`.

## Objetivo Analitico

O projeto investiga, de forma exploratoria, quais caracteristicas da base se associam a maiores taxas de inadimplencia. As perguntas principais foram:

- O historico de atrasos diferencia grupos com maior Default Rate?
- A utilizacao elevada do credito rotativo esta associada a maior inadimplencia?
- A faixa de renda apresenta relacao relevante com o risco observado?
- Uma segmentacao simples por regras (`Nivel_Risco`) consegue separar perfis com Default Rates distintos?
- A imputacao de renda introduz sinais evidentes de distorcao nas analises descritivas?

## Pipeline ETL

O tratamento foi realizado no Power Query, dentro de `02_analysis/credit_risk_analysis.xlsx`. A estrutura logica do pipeline e:

| Query | Papel |
|---|---|
| `RAW_CreditData` | Ingestao da base original sem transformacoes analiticas. |
| `MedianaRenda` | Calculo da mediana global de `MonthlyIncome`, ignorando nulos. |
| `CLEAN_pipeline` | Tratamento, criacao de variaveis derivadas, segmentacoes e organizacao final. |

### Rastreabilidade do ETL

O workbook tambem utiliza registro das etapas de transformacao para permitir auditoria do processo. As etapas documentadas seguem a sequencia logica do Power Query:

1. Importacao da base original.
2. Conversao defensiva de tipos em campos numericos sensiveis.
3. Renomeacao de colunas para padrao final.
4. Remocao de registro invalido com `age = 0`.
5. Tratamento dos sentinelas `96` e `98` nas colunas de atraso.
6. Tratamento de outliers de utilizacao do credito rotativo.
7. Criacao da flag de renda informada.
8. Imputacao da renda mensal pela mediana global.
9. Tratamento de dependentes nulos.
10. Criacao de variaveis derivadas e categorias.
11. Criacao de `Nivel_Risco` e `Grupo_Atrasos`.
12. Reordenacao final das colunas.

Essa ordem evita inconsistencias temporais: a flag de renda e criada antes da imputacao; as categorias sao geradas depois das colunas tratadas; os grupos de atraso sao criados depois da remocao dos sentinelas.

## Tratamentos Aplicados

### Idade Invalida

Foi identificado um registro com `age = 0`. Como idade zero nao representa um tomador de credito valido, esse registro foi removido. A base final passou de 150.000 para 149.999 linhas.

### Sentinelas nas Colunas de Atraso

Os valores `96` e `98` foram identificados nas tres colunas de atraso. Eles nao foram interpretados como eventos reais de atraso, pois gerariam distorcoes fortes em `Total_Eventos_Atraso`.

Tratamento aplicado:

```m
each if List.Contains({96, 98}, _) then null else _
```

O valor `0` foi preservado, pois representa ausencia registrada de atraso.

### Utilizacao do Credito Rotativo

`RevolvingUtilizationOfUnsecuredLines` foi preservada como coluna original. Para analises e categorias, foi criada `Utilizacao_Rotativo_Clean`, em que valores acima de `2` foram tratados como `null`.

Essa decisao mantem valores ate 200% como possiveis casos extremos de uso acima do limite, mas remove valores claramente inadequados para analise descritiva.

### Renda Mensal

`MonthlyIncome` apresentava nulos relevantes. O projeto preserva a renda original em `Renda_Mensal_Original` e cria:

| Coluna | Funcao |
|---|---|
| `Flag_Renda_Informada` | Indica se a renda original estava disponivel (`1`) ou foi imputada (`0`). |
| `Renda_Mensal_Clean` | Usa a renda original quando disponivel e a mediana global quando nula. |

Essa abordagem permite analisar renda sem perder rastreabilidade sobre quais registros foram imputados.

### Dependentes

`Numero_Dependentes` preserva os nulos da fonte. `Dependentes_Clean` substitui nulos por `0` para uso em segmentacoes simples.

## Engenharia de Variaveis

As variaveis derivadas foram criadas para tornar a analise mais interpretavel:

| Variavel | Finalidade |
|---|---|
| `Faixa_Etaria` | Agrupar clientes por ciclo de vida financeiro. |
| `Faixa_Renda` | Comparar Default Rate por nivel de renda mensal. |
| `Utilizacao_Rotativo_Clean` | Remover outliers extremos da leitura de utilizacao. |
| `Categoria_Utilizacao` | Transformar utilizacao em faixas de risco interpretaveis. |
| `Categoria_Razao_Divida` | Classificar comprometimento de renda. |
| `Categoria_Razao_Divida_Visual` | Apoiar graficos sem nulos. |
| `Dependentes_Clean` | Permitir analises agregadas de dependentes. |
| `Total_Eventos_Atraso` | Consolidar o historico de atraso. |
| `Nivel_Risco` | Segmentar clientes por regras simples. |
| `Grupo_Atrasos` | Criar grupos ordinais para visualizacao do gradiente de atraso. |

## Segmentacao `Nivel_Risco`

`Nivel_Risco` e o principal entregavel analitico do ETL. Ele combina historico de atraso e utilizacao do credito rotativo:

| Nivel | Regra |
|---|---|
| `Alto` | `Total_Eventos_Atraso >= 3` ou `Categoria_Utilizacao = "Critica"` |
| `Medio` | `Total_Eventos_Atraso >= 1` ou `Categoria_Utilizacao = "Alta"` |
| `Baixo` | Demais casos |

Essa segmentacao e uma regra de negocio exploratoria. Ela nao estima probabilidade de default e nao substitui um modelo estatistico. Sua utilidade esta em organizar a carteira em grupos com Default Rates observados diferentes.

## Hipoteses Investigadas

### H1 - Eventos de atraso e inadimplencia

O gradiente de `Grupo_Atrasos` indicou associacao positiva entre numero de eventos de atraso e Default Rate. O grupo `5+` apresentou Default Rate muito superior ao grupo `0`.

### H2 - Utilizacao critica e inadimplencia

A categoria `Critica` de utilizacao do credito rotativo apresentou Default Rate substancialmente maior que a categoria `Baixa`, indicando associacao entre uso intenso do limite e maior risco observado.

### H3 - Renda e inadimplencia

As faixas de menor renda apresentaram Default Rates maiores que as faixas superiores. A leitura foi mantida como associacao observada, com ressalva sobre a imputacao de 19,82% da renda.

### H4 - Renda x utilizacao

A combinacao de menor renda com utilizacao critica apresentou maior concentracao de risco na amostra. A interpretacao foi mantida de forma exploratoria: o cruzamento sugere associacao entre restricao de renda, uso elevado de credito e inadimplencia, mas nao permite afirmar causalidade.

### H5 - Segmentacao `Nivel_Risco`

`Nivel_Risco` demonstrou separacao observada entre os grupos `Baixo`, `Medio` e `Alto`. O resultado indica utilidade descritiva da segmentacao, sem caracterizar validacao preditiva.

## KPIs Utilizados

### Default Rate

```text
Default Rate = clientes inadimplentes / total de clientes
```

Indicador principal para comparar segmentos.

### Multiplicadores de Risco

Comparam o Default Rate de um segmento ao Default Rate de outro segmento de referencia. Exemplos usados no projeto:

- `Alto` vs. `Baixo` em `Nivel_Risco`.
- `Critica` vs. `Baixa` em `Categoria_Utilizacao`.
- `5+` vs. `0` em `Grupo_Atrasos`.

Esses multiplicadores descrevem diferencas observadas na amostra.

### IIR - Indice de Imputacao de Renda

```text
IIR = registros com Flag_Renda_Informada = 0 / total de registros
```

O IIR monitora a proporcao da base cuja renda foi imputada.

### CRS - Score de Risco Composto

O CRS foi tratado como indicador auxiliar de leitura e comparacao no workbook, nao como uma coluna final do `CLEAN_pipeline` e nao como modelo estatistico calibrado. Sua interpretacao deve permanecer descritiva, apoiando a leitura do dashboard sem substituir os KPIs principais.

## Graficos e Exportacoes

Os graficos finais do projeto devem ser armazenados em:

```text
04_visuals/charts/
```

O workbook principal permanece em:

```text
02_analysis/credit_risk_analysis.xlsx
```

Qualquer imagem usada no README ou em apresentacoes deve derivar dos resultados auditados da tabela `CLEAN_pipeline`. Prints ou graficos antigos devem ser evitados quando houver divergencia de cache de Tabela Dinamica.

## Limites de Interpretacao

- O projeto e exploratorio e descritivo.
- Nao ha inferencia causal.
- Nao ha treinamento, teste ou validacao fora da amostra.
- `Nivel_Risco` e uma segmentacao por regras, nao um modelo de credito.
- Multiplicadores sao observados nesta base e nao devem ser aplicados diretamente a outras carteiras.
- A renda imputada pode reduzir a variabilidade real da distribuicao.
- Outliers de `DebtRatio` limitam a interpretacao dessa variavel.

## Consideracoes Finais

A metodologia prioriza clareza, rastreabilidade e coerencia documental. O projeto demonstra um fluxo analitico completo em Excel e Power Query, com tratamento de dados, criacao de variaveis derivadas, analise exploratoria e comunicacao de insights, mantendo escopo compativel com um projeto de portfolio em nivel junior avancado.
