# Insights

## Analise de Risco de Credito - Give Me Some Credit

> **Arquivo principal:** `02_analysis/credit_risk_analysis.xlsx`  
> **Tabela de referencia:** `CLEAN_pipeline`  
> **Graficos finais:** `04_visuals/charts/`  
> **Escopo:** Analise exploratoria e descritiva

Este documento consolida os principais achados do projeto. Os numeros apresentados derivam da tabela `CLEAN_pipeline` e devem ser lidos como resultados observados na amostra, nao como inferencia causal ou modelo preditivo.

## Sumario Executivo

A base final possui **149.999 clientes** apos a remocao de um registro com idade invalida (`age = 0`). Desses, **10.026 clientes** sao inadimplentes, resultando em **Default Rate global de 6,684%**.

Os sinais mais fortes observados na amostra foram:

- historico de eventos de atraso;
- utilizacao critica do credito rotativo;
- segmentacao `Nivel_Risco`, que combina atraso e utilizacao;
- renda mensal, com leitura mais cautelosa por causa da imputacao.

Todos os achados sao exploratorios. O projeto nao comprova causalidade e nao estima probabilidade individual de default.

## KPIs Principais

| KPI | Valor |
|---|---:|
| Clientes na base final | 149.999 |
| Inadimplentes | 10.026 |
| Default Rate global | 6,684% |
| Clientes `Nivel_Risco = Alto` | 23.366 |
| Default Rate - Alto Risco | 24,57% |
| Default Rate - Baixo Risco | 1,66% |
| Multiplicador Alto vs. Baixo | 14,84x |
| Clientes com renda imputada | 29.731 |
| Percentual de renda imputada | 19,82% |
| Outliers de utilizacao tratados | 371 |

## Insight 1 - Historico de Atrasos

| Grupo de Atrasos | Clientes | Default Rate |
|---|---:|---:|
| `0` | 119.637 | 2,73% |
| `1` | 17.242 | 12,21% |
| `2` | 5.942 | 24,08% |
| `3` | 2.884 | 34,95% |
| `4` | 1.595 | 42,63% |
| `5+` | 2.430 | 57,20% |
| `Sem Informacao` | 269 | 54,65% |

O Default Rate cresce de forma consistente conforme aumenta o numero de eventos de atraso. O grupo `5+` apresenta Default Rate de 57,20%, contra 2,73% no grupo sem atrasos registrados.

Essa diferenca indica que o historico de atrasos e o marcador comportamental com maior separacao observada no projeto. Ainda assim, a leitura deve permanecer descritiva: atraso historico esta associado ao risco observado, mas o projeto nao testa causalidade nem desempenho preditivo fora da amostra.

## Insight 2 - Grupo `Sem Informacao` em Atrasos

O grupo `Sem Informacao` possui 269 clientes, apenas 0,18% da base. Ele surgiu quando os tres campos de atraso ficaram nulos apos a remocao dos sentinelas `96` e `98`.

O Default Rate desse grupo foi 54,65%, proximo ao grupo `5+`. Isso merece atencao, mas nao deve ser tratado como conclusao robusta por causa da baixa volumetria.

Uma interpretacao prudente e que esses registros podem representar um padrao atipico de qualidade de dados. Sem informacoes adicionais, nao e possivel afirmar se a ausencia e aleatoria ou se esta relacionada a perfis especificos de clientes.

## Insight 3 - Utilizacao do Credito Rotativo

| Categoria de Utilizacao | Clientes | % da Base | Default Rate |
|---|---:|---:|---:|
| `Baixa` | 92.882 | 61,92% | 2,22% |
| `Moderada` | 21.887 | 14,59% | 6,68% |
| `Alta` | 15.162 | 10,11% | 13,30% |
| `Critica` | 19.697 | 13,13% | 22,50% |
| `Nao Informado` | 371 | 0,25% | 14,56% |

A utilizacao do credito rotativo apresenta gradiente claro: quanto maior a categoria de uso, maior o Default Rate observado. A categoria `Critica` teve Default Rate de 22,50%, cerca de 10,14x o valor da categoria `Baixa`.

Essa associacao sugere que uso intenso do limite pode funcionar como sinal de pressao financeira. A interpretacao deve evitar conclusoes fortes: a variavel mostra associacao relevante com inadimplencia, mas nao permite afirmar que a utilizacao critica causa default.

## Insight 4 - Segmentacao `Nivel_Risco`

| Nivel de Risco | Clientes | % da Base | Default Rate | % dos Defaults |
|---|---:|---:|---:|---:|
| `Baixo` | 99.083 | 66,06% | 1,66% | 16,37% |
| `Medio` | 27.550 | 18,37% | 9,60% | 26,37% |
| `Alto` | 23.366 | 15,58% | 24,57% | 57,26% |

`Nivel_Risco` separa a base em tres grupos com Default Rates bem distintos. O grupo `Alto` concentra 57,26% dos inadimplentes em 15,58% da carteira, enquanto o grupo `Baixo` apresenta Default Rate de 1,66%.

O resultado indica que a regra e util para priorizacao exploratoria e comunicacao executiva. Ela nao deve ser interpretada como modelo de credito, score probabilistico ou regra suficiente para decisao de concessao.

## Insight 5 - Renda e Vulnerabilidade

| Faixa de Renda | Clientes | % da Base | Default Rate |
|---|---:|---:|---:|
| `<2K` | 9.253 | 6,17% | 9,16% |
| `2K-5K` | 42.215 | 28,14% | 8,85% |
| `5K-10K` | 77.112 | 51,41% | 5,86% |
| `10K+` | 19.785 | 13,19% | 4,33% |
| `Nao Informada` | 1.634 | 1,09% | 4,04% |

As faixas de menor renda apresentam Default Rates mais altos que as faixas superiores. A leitura sugere associacao entre menor renda e maior vulnerabilidade financeira observada.

O principal cuidado metodologico e a imputacao de renda: 29.731 registros, ou 19,82% da base, receberam a mediana global. Isso pode suavizar diferencas reais entre faixas e reduzir a variabilidade observada.

## Insight 6 - Renda x Utilizacao Critica

O cruzamento entre renda e utilizacao indica que clientes de menor renda com utilizacao critica tendem a concentrar maior risco observado. Essa leitura e coerente com a combinacao de menor margem financeira e maior uso do credito rotativo.

A interpretacao foi mantida como associacao exploratoria. O projeto nao permite inferir causas socioeconomicas amplas, nem afirmar que renda baixa leva diretamente a utilizacao critica ou inadimplencia.

## Insight 7 - Comprometimento de Renda

| Categoria | Clientes | % da Base | Default Rate |
|---|---:|---:|---:|
| `Saudavel` | 72.060 | 48,04% | 5,82% |
| `Atencao` | 21.641 | 14,43% | 6,76% |
| `Comprometido` | 25.253 | 16,84% | 10,37% |
| `Indefinido` | 31.045 | 20,70% | 5,64% |

A categoria `Comprometido` apresentou Default Rate superior as categorias `Saudavel` e `Atencao`, sugerindo associacao entre maior comprometimento de renda e maior inadimplencia observada.

O grupo `Indefinido` deve ser analisado com cautela, pois agrega outliers e valores inadequados para categorizacao. Por isso, ele foi tratado como categoria visual e nao como evidencia estatistica forte.

## Insight 8 - Impacto do ETL

A limpeza da utilizacao do credito rotativo teve impacto relevante:

| Medida | Valor |
|---|---:|
| Media original de utilizacao | 6,0485 |
| Media limpa de utilizacao | 0,3203 |
| Outliers `> 2` convertidos em `null` | 371 |
| Maximo original observado | 50.708 |

Sem essa etapa, a media da variavel seria pouco interpretavel. O ETL foi essencial para evitar que poucos valores extremos distorcessem as analises.

## Insight 9 - Imputacao de Renda

| Grupo | Default Rate |
|---|---:|
| Renda informada | 6,95% |
| Renda imputada | 5,61% |
| Diferenca | 1,33 p.p. |

A diferenca de **1,33 p.p.** entre os grupos com renda informada e imputada sugere que a imputacao pela mediana nao gerou distorcao evidente nas analises descritivas principais.

Esse resultado e um controle de qualidade, nao uma validacao formal da imputacao. A mediana global e adequada para o escopo exploratorio do projeto, desde que a flag de imputacao continue documentada e disponivel para filtros.

## Alerta Sobre Pivos e Graficos

Durante a revisao, foram observadas divergencias pontuais entre alguns pivos/graficos e os calculos derivados diretamente da tabela `CLEAN_pipeline`.

Esse ponto deve ser tratado como alerta operacional, nao como falha conclusiva do projeto. Em Excel, divergencias desse tipo podem ocorrer por cache de Tabela Dinamica, filtros ativos, slicers ou ranges desatualizados.

Para publicacao, os numeros de referencia devem vir da tabela `CLEAN_pipeline`, e os graficos exportados para `04_visuals/charts/` devem ser atualizados a partir dessa base.

## Limitacoes

- A analise e exploratoria e descritiva.
- Nao ha causalidade comprovada.
- Nao ha validacao preditiva fora da amostra.
- A renda possui 19,82% de imputacao.
- `DebtRatio` possui outliers severos.
- `Nivel_Risco` e segmentacao por regras, nao modelo estatistico.
- O dataset representa um contexto especifico de credito ao consumidor nos Estados Unidos.

## Consideracoes Finais

O projeto mostra que, mesmo sem machine learning, um pipeline bem documentado em Excel e Power Query pode gerar uma leitura consistente de risco de credito. Os principais resultados apontam associacoes relevantes entre inadimplencia, historico de atrasos, utilizacao do credito e segmentacao por regras.

A principal forca do projeto esta na combinacao entre tratamento de dados, rastreabilidade das transformacoes e comunicacao clara dos limites da analise.
