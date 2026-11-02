# Resultados e leituras exploratórias

## Base de referência

Os totais abaixo foram conferidos na tabela armazenada `CLEAN_pipeline` da versão de referência anteriormente inspecionada e comparados com o CSV de treinamento. A autora enviou uma versão corrigida do workbook, incorporada à branch, mas os números não foram recalculados nessa versão; portanto, continuam sendo uma referência histórica, não uma reconciliação dos pivôs, gráficos, imagem ou PDF atuais.

| Indicador | Valor de referência |
|---|---:|
| Linhas no CSV | 150.000 |
| Linhas em `CLEAN_pipeline` | 149.999 |
| Registros positivos em `Inadimplente` | 10.026 |
| Taxa global observada | 6,684% |
| Renda imputada pela mediana global | 29.731 (19,82%) |
| Dependentes ausentes na coluna original | 3.924 |
| Valores de utilização acima de 2 tratados como ausentes | 371 |

Uma linha com `age = 0` foi removida conforme a regra documentada. Os 371 valores de utilização acima de 2 foram tratados na coluna limpa, não excluídos da base.

## Segmentação por regras

| Grupo | Registros | Participação na base | Taxa observada | Participação nos positivos |
|---|---:|---:|---:|---:|
| `Baixo` | 99.083 | 66,06% | 1,66% | 16,37% |
| `Medio` | 27.550 | 18,37% | 9,60% | 26,37% |
| `Alto` | 23.366 | 15,58% | 24,57% | 57,26% |

A regra `Nivel_Risco` combina eventos de atraso e categoria de utilização. A diferença entre as taxas é uma associação observada na amostra e é esperada, em parte, porque as próprias regras usam essas variáveis. Não é validação independente nem evidência de capacidade preditiva.

## Grupos de atraso

| Grupo de atrasos | Registros | Taxa observada |
|---|---:|---:|
| `0` | 119.637 | 2,73% |
| `1` | 17.242 | 12,21% |
| `2` | 5.942 | 24,08% |
| `3` | 2.884 | 34,95% |
| `4` | 1.595 | 42,63% |
| `5+` | 2.430 | 57,20% |
| `Sem Informacao` | 269 | 54,65% |

A taxa observada cresce nos grupos com mais eventos registrados. O grupo sem informação é pequeno (269 registros) e tem taxa alta; isso é um sinal para investigar qualidade e mecanismo de ausência, não uma conclusão sobre essas pessoas.

## Utilização rotativa

| Categoria | Registros | Taxa observada |
|---|---:|---:|
| `Baixa` | 92.882 | 2,22% |
| `Moderada` | 21.887 | 6,68% |
| `Alta` | 15.162 | 13,30% |
| `Critica` | 19.697 | 22,50% |
| `Nao Informado` | 371 | 14,56% |

As taxas diferem entre categorias na amostra. A categoria é derivada de uma regra de faixa e não deve ser interpretada como causa ou como instrução de tratamento individual.

## Renda

| Faixa | Registros | Taxa observada |
|---|---:|---:|
| `<2K` | 9.253 | 9,16% |
| `2K-5K` | 42.215 | 8,85% |
| `5K-10K` | 77.112 | 5,86% |
| `10K+` | 19.785 | 4,33% |
| `Nao Informada` | 1.634 | 4,04% |

A associação entre faixas e taxa deve ser lida com cautela: 29.731 rendas (19,82% da base) foram imputadas pela mediana global. Renda, idade e atributos que possam funcionar como proxies não devem ser convertidos em critérios de decisão sem avaliação específica de viés, justiça e conformidade.

## Limitações e pendências do material visual

- A análise é descritiva; não há inferência causal, treinamento nem validação fora da amostra.
- `Nivel_Risco` é regra exploratória e não score calibrado.
- O conjunto de dados representa um contexto e uma população específicos; não se presume representatividade de outras carteiras.
- A autora atualizou o workbook, mas não foi feita validação interativa nem reconciliação dos pivôs e gráficos desta versão. Os prints recebidos exibem filtros ativos e conteúdo não reconciliado; não foram adicionados ao repositório. A imagem `Dashboard.png` e o PDF permanecem sem alteração e não são apresentados como versões validadas.
- Na versão anteriormente inspecionada, o Power Query dependia de caminho local e de arquivo externo de log. A nova configuração e a necessidade do log não foram verificadas; não afirmo que a atualização seja portátil.

Para a metodologia, as regras de transformação e o estado de reprodutibilidade, consulte [`methodology.md`](methodology.md). Os números desta página não devem ser usados fora do contexto exploratório descrito.

