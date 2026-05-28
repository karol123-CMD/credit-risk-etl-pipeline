# Dicionario de Dados

## Credit Risk Analytics - Give Me Some Credit

> **Fonte:** Give Me Some Credit - Kaggle  
> **Arquivo analitico:** `02_analysis/credit_risk_analysis.xlsx`  
> **Tabela final:** `CLEAN_pipeline`  
> **Atualizacao:** Maio de 2026

Este documento descreve as variaveis utilizadas na tabela final do projeto. A base original foi preservada em `01_data/raw/` e o tratamento foi realizado no Power Query dentro de `02_analysis/credit_risk_analysis.xlsx`.

O projeto usa uma abordagem exploratoria: as variaveis derivadas apoiam segmentacoes e KPIs descritivos, sem substituir a base original nem criar modelo preditivo.

## Estrutura Oficial Relacionada

```text
01_data/raw/
02_analysis/credit_risk_analysis.xlsx
03_docs/
04_visuals/charts/
04_visuals/deck/
```

## Visao Geral da Base

| Item | Valor |
|---|---:|
| Dataset original | Give Me Some Credit |
| Arquivo de origem | `cs-training.csv` |
| Registros originais | 150.000 |
| Registros apos ETL | 149.999 |
| Registro removido | 1 linha com `age = 0` |
| Variaveis originais do CSV | 11 |
| Colunas finais na tabela limpa | 24 |

## Preservacao das Variaveis Originais

O pipeline preserva as variaveis originais relevantes para auditoria e cria colunas derivadas separadas para tratamento, segmentacao e visualizacao.

As seguintes variaveis originais permanecem disponiveis com nomenclatura final em portugues:

| Nome final | Nome original | Observacao |
|---|---|---|
| `Inadimplente` | `SeriousDlqin2yrs` | Variavel-alvo original |
| `Idade` | `age` | Apenas o registro com idade zero foi removido |
| `Renda_Mensal_Original` | `MonthlyIncome` | Nulos originais preservados |
| `Razao_Divida_Renda` | `DebtRatio` | Outliers preservados na coluna original |
| `Linhas_Credito_Abertas` | `NumberOfOpenCreditLinesAndLoans` | Sem transformacao direta |
| `Emprestimos_Imoveis` | `NumberRealEstateLoansOrLines` | Sem transformacao direta |
| `Numero_Dependentes` | `NumberOfDependents` | Nulos preservados na coluna original |
| `Utilizacao_Credito_Rotativo` | `RevolvingUtilizationOfUnsecuredLines` | Outliers preservados na coluna original |
| `Atrasos_30_59_Dias` | `NumberOfTime30-59DaysPastDueNotWorse` | Sentinelas 96/98 tratados como `null` |
| `Atrasos_60_89_Dias` | `NumberOfTime60-89DaysPastDueNotWorse` | Sentinelas 96/98 tratados como `null` |
| `Atrasos_90_Dias` | `NumberOfTimes90DaysLate` | Sentinelas 96/98 tratados como `null` |

As colunas tratadas, categorizadas ou imputadas foram criadas como novas variaveis. Exemplos: `Renda_Mensal_Clean`, `Dependentes_Clean`, `Utilizacao_Rotativo_Clean`, `Faixa_Renda`, `Nivel_Risco` e `Grupo_Atrasos`.

## Dicionario das Colunas Finais

| # | Coluna | Tipo | Origem | Descricao |
|---:|---|---|---|---|
| 1 | `Inadimplente` | Inteiro | Original | Indica inadimplencia grave nos dois anos posteriores ao registro (`0` ou `1`). |
| 2 | `ID` | Inteiro | Indice tecnico | Identificador de linha importado/renomeado no Power Query. Nao e chave de negocio. |
| 3 | `Idade` | Inteiro | Original | Idade do cliente em anos. Linhas com `age = 0` foram removidas. |
| 4 | `Faixa_Etaria` | Texto | Derivada | Segmentacao: `Jovem`, `Adulto`, `Maduro`, `Senior`. |
| 5 | `Renda_Mensal_Original` | Numero | Original | Renda mensal declarada, mantendo nulos da fonte. |
| 6 | `Flag_Renda_Informada` | Inteiro | Controle | `1` quando a renda original estava informada; `0` quando foi imputada. |
| 7 | `Renda_Mensal_Clean` | Numero | Derivada | Renda mensal apos imputacao pela mediana global quando a renda original era nula. |
| 8 | `Faixa_Renda` | Texto | Derivada | Agrupamento da renda tratada: `<2K`, `2K-5K`, `5K-10K`, `10K+`, `Nao Informada`. |
| 9 | `Razao_Divida_Renda` | Numero | Original | Razao entre dividas/despesas mensais e renda. |
| 10 | `Categoria_Razao_Divida` | Texto | Derivada | Categoria analitica de comprometimento de renda; outliers retornam `null`. |
| 11 | `Categoria_Razao_Divida_Visual` | Texto | Derivada | Versao para visualizacao, usando `Indefinido` quando a categoria analitica e nula. |
| 12 | `Linhas_Credito_Abertas` | Inteiro | Original | Quantidade de linhas de credito e emprestimos ativos. |
| 13 | `Emprestimos_Imoveis` | Inteiro | Original | Quantidade de emprestimos ou linhas garantidas por imoveis. |
| 14 | `Numero_Dependentes` | Inteiro | Original | Numero de dependentes, mantendo nulos da fonte. |
| 15 | `Dependentes_Clean` | Inteiro | Derivada | Nulos de dependentes substituidos por `0`. |
| 16 | `Utilizacao_Credito_Rotativo` | Numero | Original | Percentual de utilizacao do credito rotativo, incluindo outliers originais. |
| 17 | `Utilizacao_Rotativo_Clean` | Numero | Derivada | Valores acima de `2` tratados como `null`; demais valores preservados. |
| 18 | `Categoria_Utilizacao` | Texto | Derivada | Agrupamento da utilizacao: `Baixa`, `Moderada`, `Alta`, `Critica`, `Nao Informado`. |
| 19 | `Atrasos_30_59_Dias` | Inteiro | Original tratada | Eventos de atraso leve; sentinelas 96/98 convertidos para `null`. |
| 20 | `Atrasos_60_89_Dias` | Inteiro | Original tratada | Eventos de atraso moderado; sentinelas 96/98 convertidos para `null`. |
| 21 | `Atrasos_90_Dias` | Inteiro | Original tratada | Eventos de atraso grave; sentinelas 96/98 convertidos para `null`. |
| 22 | `Total_Eventos_Atraso` | Inteiro | Derivada | Soma dos tres campos de atraso, ignorando nulos. Se todos forem nulos, retorna `null`. |
| 23 | `Nivel_Risco` | Texto | Derivada | Segmentacao por regras: `Baixo`, `Medio`, `Alto`. |
| 24 | `Grupo_Atrasos` | Texto | Derivada | Agrupamento ordinal: `0`, `1`, `2`, `3`, `4`, `5+`, `Sem Informacao`. |

## Regras de Tratamento

### Renda e `Flag_Renda_Informada`

`Flag_Renda_Informada` foi criada antes da imputacao da renda. A logica correta e:

```m
each if [MonthlyIncome] is null then 0 else 1
```

| Valor | Significado |
|---:|---|
| `1` | A renda estava informada na coluna original `MonthlyIncome`. |
| `0` | A renda original era nula e `Renda_Mensal_Clean` recebeu a mediana global. |

Essa flag nao indica qualidade de credito nem renda alta/baixa. Ela existe para rastrear quais registros tiveram renda imputada e para permitir comparacoes entre dados informados e dados tratados.

### Renda Mensal Tratada

`Renda_Mensal_Original` preserva os nulos da fonte. `Renda_Mensal_Clean` e a coluna usada nas segmentacoes de renda:

```m
each if [MonthlyIncome] is null then MedianaRenda else [MonthlyIncome]
```

A mediana global usada no projeto foi calculada pela query auxiliar `MedianaRenda`.

### Dependentes

`Numero_Dependentes` preserva a informacao original, incluindo nulos. `Dependentes_Clean` substitui nulos por `0` para permitir analises agregadas simples:

```m
each if [NumberOfDependents] is null then 0 else [NumberOfDependents]
```

### Sentinelas 96 e 98

Nas tres colunas de atraso, os valores `96` e `98` aparecem como codigos atipicos da base original. Eles nao foram tratados como eventos reais de atraso, pois distorceriam diretamente as somas e os grupos de atraso.

Tratamento aplicado:

```m
each if List.Contains({96, 98}, _) then null else _
```

O valor `0` foi preservado porque representa ausencia registrada de atrasos. Quando os tres campos de atraso ficam nulos, `Total_Eventos_Atraso` fica `null` e `Grupo_Atrasos` recebe `Sem Informacao`.

### Utilizacao do Credito Rotativo

A coluna original `Utilizacao_Credito_Rotativo` foi preservada. A coluna `Utilizacao_Rotativo_Clean` trata valores acima de `2` como outliers:

```m
if [RevolvingUtilizationOfUnsecuredLines] > 2 then null
else [RevolvingUtilizationOfUnsecuredLines]
```

O limite `2` foi usado de forma conservadora: valores ate 200% foram mantidos como possiveis casos de uso acima do limite; valores superiores foram considerados inadequados para segmentacao.

### Razao Divida/Renda

`Razao_Divida_Renda` permanece original. A categorizacao analitica usa limiares simples:

| Categoria | Criterio |
|---|---|
| `Saudavel` | Menor que `0,35` |
| `Atencao` | Maior ou igual a `0,35` e menor que `0,50` |
| `Comprometido` | Maior ou igual a `0,50` e ate `2` |
| `null` | Nulo ou maior que `2` |

`Categoria_Razao_Divida_Visual` converte o `null` analitico em `Indefinido` para facilitar graficos.

## Variaveis Derivadas de Segmentacao

### `Faixa_Etaria`

| Criterio | Categoria |
|---|---|
| `< 30` | `Jovem` |
| `30` a `< 50` | `Adulto` |
| `50` a `< 65` | `Maduro` |
| `>= 65` | `Senior` |

### `Faixa_Renda`

| Criterio | Categoria |
|---|---|
| Renda nula ou `<= 0` | `Nao Informada` |
| `< 2.000` | `<2K` |
| `2.000` a `< 5.000` | `2K-5K` |
| `5.000` a `< 10.000` | `5K-10K` |
| `>= 10.000` | `10K+` |

### `Categoria_Utilizacao`

| Criterio | Categoria |
|---|---|
| Nulo | `Nao Informado` |
| `< 0` | `Valor Invalido` |
| `<= 0,30` | `Baixa` |
| `<= 0,60` | `Moderada` |
| `<= 0,90` | `Alta` |
| `> 0,90` | `Critica` |

### `Nivel_Risco`

`Nivel_Risco` e uma segmentacao por regras, nao um score estatistico calibrado.

| Nivel | Regra |
|---|---|
| `Alto` | `Total_Eventos_Atraso >= 3` ou `Categoria_Utilizacao = "Critica"` |
| `Medio` | `Total_Eventos_Atraso >= 1` ou `Categoria_Utilizacao = "Alta"` |
| `Baixo` | Demais casos |

A regra usa `OR` para capturar dois sinais diferentes de risco: historico de atraso e pressao de uso do credito rotativo.

## Metricas de Qualidade Documentadas

| Metrica | Valor |
|---|---:|
| Linhas na base original | 150.000 |
| Linhas na base final | 149.999 |
| Registros removidos por `age = 0` | 1 |
| Renda imputada (`Flag_Renda_Informada = 0`) | 29.731 |
| Renda informada (`Flag_Renda_Informada = 1`) | 120.268 |
| Dependentes nulos na coluna original | 3.924 |
| Outliers de utilizacao `> 2` tratados como `null` | 371 |
| Registros com `Grupo_Atrasos = "Sem Informacao"` | 269 |

## Observacoes Finais

Este dicionario documenta a tabela final usada nas analises exploratorias. As colunas originais foram mantidas sempre que necessario para rastreabilidade, enquanto as colunas derivadas foram usadas para padronizar leituras, segmentacoes e graficos.

Qualquer divergencia entre documentos deve ser resolvida tomando como referencia o pipeline `CLEAN_pipeline` em `02_analysis/credit_risk_analysis.xlsx`.
