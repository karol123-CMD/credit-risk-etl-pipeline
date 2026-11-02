# Metodologia

## Escopo

Análise exploratória da base **Give Me Some Credit** no Excel, com Power Query para ETL e tabelas dinâmicas, fórmulas e gráficos para análise descritiva. Não há treinamento ou validação de modelo preditivo, inferência causal, nem avaliação para uso em concessão de crédito.

## Origem e arquivos

A fonte declarada no projeto é a competição [Give Me Some Credit no Kaggle](https://www.kaggle.com/c/GiveMeSomeCredit), de 2011. A página identifica a competição e seu objetivo original de previsão, mas os termos específicos aplicáveis à redistribuição dos dados não foram confirmados. Não se presume permissão de redistribuição. O pacote inclui `cs-training.csv` (usado), `cs-test.csv` e `sampleEntry.csv` (não usados nesta análise), além de `Data Dictionary.xls` (dicionário original). O dicionário em Markdown neste repositório facilita a leitura e documenta as variáveis finais do projeto.

## Consultas Power Query observadas

O workbook contém estas cinco consultas:

| Consulta | Papel observado | Situação de atualização |
|---|---|---|
| `CaminhoArquivo` | Consulta auxiliar que fornece o caminho do CSV à importação. | Contém caminho local absoluto; precisa ser configurada em outro computador. |
| `RAW_CreditData` | Importa o CSV de treinamento e aplica a leitura/tipagem inicial. | Depende de `CaminhoArquivo`. |
| `MedianaRenda` | Calcula a mediana global de `MonthlyIncome` para imputação. | Consulta auxiliar usada por `CLEAN_pipeline`. |
| `CLEAN_pipeline` | Trata dados, cria colunas derivadas e organiza a tabela final. | Base das análises e da tabela `CLEAN_pipeline`. |
| `ETL_LOG_Projeto01` | Importa um log de etapas a partir de outro arquivo CSV. | O arquivo externo não está no repositório; dependência pode impedir uma atualização completa. A folha de qualidade preserva o registro legível das etapas. |

A atualização não foi executada nesta revisão. Para reproduzir em outro computador, obtenha os arquivos na fonte original, abra o Editor do Power Query e edite `CaminhoArquivo` para selecionar o `cs-training.csv` local. Atualize primeiro as consultas auxiliares `CaminhoArquivo` e `MedianaRenda`, depois `RAW_CreditData` e `CLEAN_pipeline`; se a consulta `ETL_LOG_Projeto01` continuar ativa, será necessário localizar o CSV de log original ou desabilitar/remover essa dependência no Excel. Por fim, atualize tabelas dinâmicas e confira os totais abaixo. Recomenda-se Excel desktop recente para Windows com Power Query; versão exata e compatibilidade não foram testadas em outro computador.

**Limitação conhecida:** a origem é atualmente um caminho absoluto de máquina local. A sugestão para próxima revisão é substituir por uma seleção/parâmetro de caminho documentado e verificar se o log pode ser preservado como registro estático sem continuar como dependência da atualização. Isso requer edição e validação interativa no Excel.

## Etapas de tratamento documentadas

1. Importar o CSV de treinamento e tipar as colunas.
2. Remover uma linha com `age = 0`; a tabela final passa de 150.000 para 149.999 registros.
3. Tratar os códigos 96 e 98 nas três colunas de atraso como ausentes, preservando o zero.
4. Preservar as variáveis originais relevantes e criar colunas tratadas separadas.
5. Criar `Flag_Renda_Informada` antes de imputar `MonthlyIncome` pela mediana global.
6. Criar `Dependentes_Clean`, substituindo nulos por zero para as agregações previstas.
7. Tratar valores de utilização rotativa acima de 2 como ausentes na variável limpa, sem remover as linhas.
8. Criar faixas de idade, renda e utilização, grupos de atraso e `Nivel_Risco`.

As regras acima descrevem a implementação documentada. Como o refresh não foi executado, este texto não certifica que a consulta atual continue atualizando corretamente.

## Definições analíticas

**Taxa observada de inadimplência (Default Rate)** = registros positivos de `Inadimplente` / total de registros no grupo. É uma proporção observada na amostra, não uma probabilidade individual estimada.

`Nivel_Risco` é uma segmentação por regras: `Alto` quando `Total_Eventos_Atraso >= 3` ou `Categoria_Utilizacao = "Critica"`; `Medio` quando `Total_Eventos_Atraso >= 1` ou `Categoria_Utilizacao = "Alta"`; `Baixo` nos demais casos. A classificação é descritiva, não calibrada e não adequada, por si só, para decidir sobre pessoas.

## Referência numérica e limitações de reconciliação

A tabela armazenada `CLEAN_pipeline` foi lida sem alterar o workbook e comparada ao CSV: 149.999 registros, 10.026 positivos, taxa global de 6,684%, 29.731 rendas imputadas, 3.924 dependentes ausentes na coluna original e 371 valores de utilização acima de 2. Esses totais de referência não equivalem à validação dos pivôs, gráficos ou segmentações salvos.

Não foi possível concluir a edição do workbook nesta execução. Permanecem por corrigir/verificar: `EDA!N39` com `#DIV/0!`; grafias divergentes de faixas de renda entre células; cache de três pivôs da folha de renda com intervalo aparentemente inadequado/desatualizado; filtros/segmentações e números visuais que precisam de atualização e reconciliação; caminho local e dependência do log externo; nome definido `CLEAN_pipiline`; conexão duplicada de `MedianaRenda`; metadados locais. O binário permanece inalterado. Até validação interativa no Excel, use os números da tabela de referência com essa ressalva e não apresente o dashboard/PDF como reconciliado.

## Uso responsável

Os resultados mostram associações na amostra, não causalidade. `Nivel_Risco` não é um modelo preditivo. Diferenças por idade ou renda não devem ser convertidas em regras para clientes. Qualquer uso decisório exigiria avaliação independente de viés, justiça, validade e conformidade, além de dados e validações apropriados.

