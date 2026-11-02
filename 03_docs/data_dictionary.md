# Dicionário de dados

Este dicionário descreve as colunas da tabela `CLEAN_pipeline` na versão de referência anteriormente inspecionada do workbook. Essa tabela tinha 24 colunas e 149.999 linhas, para 150.000 registros na origem. A autora enviou uma planilha corrigida, agora incorporada à branch, mas a estrutura e os valores não foram conferidos novamente no Excel. As regras são exploratórias e não constituem uma política de crédito.

**Fonte declarada:** [Give Me Some Credit — Kaggle](https://www.kaggle.com/c/GiveMeSomeCredit). `Data Dictionary.xls` é o dicionário incluído no pacote do desafio; este Markdown descreve as variáveis finais utilizadas no projeto. Os termos de redistribuição dos dados não foram confirmados nesta revisão.

## Variáveis finais

| # | Coluna | Origem/tipo | Descrição |
|---:|---|---|---|
| 1 | `Inadimplente` | Original, inteiro | Indicador `SeriousDlqin2yrs` (`0`/`1`), relacionado a atraso grave nos dois anos posteriores ao registro. |
| 2 | `ID` | Índice técnico, inteiro | Identificador de linha; não é chave de negócio. |
| 3 | `Idade` | Original, inteiro | Idade em anos; uma linha com idade zero foi removida. |
| 4 | `Faixa_Etaria` | Derivada, texto | Grupos `Jovem`, `Adulto`, `Maduro` e `Senior`. |
| 5 | `Renda_Mensal_Original` | Original, numérico | Renda da fonte, com ausências preservadas. |
| 6 | `Flag_Renda_Informada` | Controle, inteiro | `1` se a renda original estava preenchida; `0` se foi imputada. |
| 7 | `Renda_Mensal_Clean` | Derivada, numérico | Renda original ou mediana global quando a original estava ausente. |
| 8 | `Faixa_Renda` | Derivada, texto | `<2K`, `2K-5K`, `5K-10K`, `10K+` ou `Nao Informada`. |
| 9 | `Razao_Divida_Renda` | Original, numérico | `DebtRatio`; mantida sem alteração direta. |
| 10 | `Categoria_Razao_Divida` | Derivada, texto | `Saudavel`, `Atencao`, `Comprometido` ou nulo, segundo faixas documentadas no Power Query. |
| 11 | `Categoria_Razao_Divida_Visual` | Derivada, texto | Versão para visualização; nulos são apresentados como `Indefinido`. |
| 12 | `Linhas_Credito_Abertas` | Original, inteiro | Número de linhas de crédito e empréstimos abertos. |
| 13 | `Emprestimos_Imoveis` | Original, inteiro | Número de empréstimos/linhas relacionados a imóveis. |
| 14 | `Numero_Dependentes` | Original, inteiro | Número de dependentes; nulos preservados. |
| 15 | `Dependentes_Clean` | Derivada, inteiro | Nulos substituídos por zero para agregações simples. |
| 16 | `Utilizacao_Credito_Rotativo` | Original, numérico | Utilização de crédito rotativo sem limpeza. |
| 17 | `Utilizacao_Rotativo_Clean` | Derivada, numérico | Valores acima de 2 convertidos em nulo; as linhas são mantidas. |
| 18 | `Categoria_Utilizacao` | Derivada, texto | `Baixa`, `Moderada`, `Alta`, `Critica`, `Nao Informado` ou `Valor Invalido`. |
| 19 | `Atrasos_30_59_Dias` | Original tratada, inteiro | Campo de atraso; sentinelas 96/98 convertidas em nulo. |
| 20 | `Atrasos_60_89_Dias` | Original tratada, inteiro | Campo de atraso; sentinelas 96/98 convertidas em nulo. |
| 21 | `Atrasos_90_Dias` | Original tratada, inteiro | Campo de atraso; sentinelas 96/98 convertidas em nulo. |
| 22 | `Total_Eventos_Atraso` | Derivada, inteiro | Soma dos campos de atraso disponíveis; nulo se todos forem nulos. |
| 23 | `Nivel_Risco` | Derivada, texto | Grupos `Baixo`, `Medio`, `Alto`, definidos por regras; não é score preditivo. |
| 24 | `Grupo_Atrasos` | Derivada, texto | Grupos ordinais `0`, `1`, `2`, `3`, `4`, `5+` e `Sem Informacao`. |

## Regras documentadas

- **Renda:** `Flag_Renda_Informada` é criada antes de preencher ausências; `Renda_Mensal_Clean` usa a mediana global calculada na consulta auxiliar `MedianaRenda`. Foram imputados 29.731 registros (19,82%).
- **Dependentes:** `Dependentes_Clean` substitui nulos por zero; a coluna original continua disponível. A tabela de referência contém 3.924 ausências originais.
- **Atrasos:** 96 e 98 são tratados como códigos sentinela e substituídos por nulo nos três campos. Zero é preservado. Quando os três campos ficam nulos, o grupo é `Sem Informacao`.
- **Utilização rotativa:** valores acima de 2 na coluna original são nulos na coluna limpa para a análise por faixas. Foram identificados 371 casos; não foram removidos registros por essa condição.
- **Faixa etária:** `<30`, `30–<50`, `50–<65`, `>=65`, respectivamente `Jovem`, `Adulto`, `Maduro`, `Senior`.
- **Faixa de renda:** renda ausente ou não positiva = `Nao Informada`; `<2.000` = `<2K`; `2.000–<5.000` = `2K-5K`; `5.000–<10.000` = `5K-10K`; `>=10.000` = `10K+`. A grafia das faixas e suas contagens nos pivôs da versão corrigida ainda precisam ser conferidas no Excel.
- **Faixas de utilização:** nulo = `Nao Informado`; `<0` = `Valor Invalido`; até 0,30 = `Baixa`; até 0,60 = `Moderada`; até 0,90 = `Alta`; acima de 0,90 = `Critica`.
- **Segmentação `Nivel_Risco`:** `Alto` se total de atrasos `>=3` ou utilização `Critica`; `Medio` se total de atrasos `>=1` ou utilização `Alta`; caso contrário `Baixo`.

As descrições e números de referência documentam a versão observada anteriormente; o workbook atualizado pela autora ainda não foi recalculado ou validado por esta revisão. Veja as limitações de atualização e reconciliação em [`methodology.md`](methodology.md).

