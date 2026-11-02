# Metodologia

## Escopo

Análise exploratória da base **Give Me Some Credit** no Excel, com Power Query para ETL e tabelas dinâmicas, fórmulas e gráficos para análise descritiva. Não há treinamento ou validação de modelo preditivo, inferência causal, nem avaliação para uso em concessão de crédito.

## Origem e arquivos

A fonte declarada no projeto é a competição [Give Me Some Credit no Kaggle](https://www.kaggle.com/c/GiveMeSomeCredit), de 2011. A página identifica a competição e seu objetivo original de previsão, mas os termos específicos aplicáveis à redistribuição dos dados não foram confirmados. Não se presume permissão de redistribuição. O pacote inclui `cs-training.csv` (usado), `cs-test.csv` e `sampleEntry.csv` (não usados nesta análise), além de `Data Dictionary.xls` (dicionário original). O dicionário em Markdown neste repositório facilita a leitura e documenta as variáveis finais do projeto.

## Consultas Power Query observadas

O workbook contém estas cinco consultas:

| Consulta | Papel observado | Situação de atualização |
|---|---|---|
| `CaminhoArquivo` | Consulta auxiliar que fornece o caminho do CSV à importação. | Na versão anteriormente analisada, continha caminho local; a configuração da versão corrigida enviada pela autora não foi verificada. |
| `RAW_CreditData` | Importa o CSV de treinamento e aplica a leitura/tipagem inicial. | Depende de `CaminhoArquivo`. |
| `MedianaRenda` | Calcula a mediana global de `MonthlyIncome` para imputação. | Consulta auxiliar usada por `CLEAN_pipeline`. |
| `CLEAN_pipeline` | Trata dados, cria colunas derivadas e organiza a tabela final. | Base das análises e da tabela `CLEAN_pipeline`. |
| `ETL_LOG_Projeto01` | Importava um log de etapas a partir de outro arquivo CSV. | Na versão anteriormente analisada, o arquivo externo não estava no repositório. Não confirmei se a consulta continua ativa na versão atual; a folha de qualidade preserva o registro legível das etapas. |

A autora informou ter ajustado o caminho de origem e enviou a planilha atualizada, agora incorporada à branch. Não executei o refresh nem confirmei no Excel se o log externo foi removido/desabilitado ou se a nova origem funciona em outra pasta. Para reproduzir, abra o Editor do Power Query, confirme a origem em `CaminhoArquivo` e a disponibilidade do CSV; atualize as consultas auxiliares `CaminhoArquivo` e `MedianaRenda`, depois `RAW_CreditData` e `CLEAN_pipeline`. Confirme também se `ETL_LOG_Projeto01` continua ativa e exige arquivo externo. Por fim, atualize as tabelas dinâmicas e compare os totais com os números de referência. Recomenda-se Excel desktop recente para Windows com Power Query; versão exata, configuração atual e compatibilidade em outro computador não foram testadas.

**Limitação conhecida:** o caminho foi ajustado pela autora na versão enviada, mas não foi inspecionado nem testado por mim. A portabilidade só poderá ser afirmada depois de confirmar a consulta no Excel e executar uma atualização em outra pasta ou computador. Também falta confirmar se o log é uma dependência ativa e se pode permanecer apenas como registro estático.

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

Na versão de referência anteriormente inspecionada, a tabela armazenada `CLEAN_pipeline` foi lida sem alterar o workbook e comparada ao CSV: 149.999 registros, 10.026 positivos, taxa global de 6,684%, 29.731 rendas imputadas, 3.924 dependentes ausentes na coluna original e 371 valores de utilização acima de 2. Esses totais pertencem à versão observada naquela revisão; não foram recalculados com o workbook corrigido pela autora e não equivalem à validação dos pivôs, gráficos ou segmentações atuais.

A versão corrigida enviada pela autora foi incorporada a esta branch. A autora informou ter corrigido `EDA!N39` e ajustado o caminho de origem; não abri essa versão no Excel nem executei recálculo ou refresh. Portanto, não trato como comprovadas a correção da fórmula, a padronização das faixas de renda, a reconciliação dos caches de pivôs, a limpeza de filtros/segmentações, a revisão do nome definido e da conexão duplicada, ou a remoção de metadados locais. Os números desta página são referência da versão anteriormente conferida, não uma reconciliação do dashboard ou do PDF atuais.

## Uso responsável

Os resultados mostram associações na amostra, não causalidade. `Nivel_Risco` não é um modelo preditivo. Diferenças por idade ou renda não devem ser convertidas em regras para clientes. Qualquer uso decisório exigiria avaliação independente de viés, justiça, validade e conformidade, além de dados e validações apropriados.

