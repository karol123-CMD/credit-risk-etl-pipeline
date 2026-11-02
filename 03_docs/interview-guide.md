# Guia de conversa sobre o projeto

Use este material como apoio para explicar decisões reais do projeto, adaptando as respostas à conversa. Não é necessário decorar o texto.

## Apresentação em 60–90 segundos

“Este projeto é uma análise exploratória de risco de crédito feita principalmente no Excel, com Power Query para preparar os dados. Usei o conjunto Give Me Some Credit e organizei o fluxo desde o CSV, passando pelas consultas RAW e CLEAN, até as tabelas dinâmicas e o dashboard. No tratamento, por exemplo, preservei os campos originais, tratei os códigos 96 e 98 das variáveis de atraso como ausentes e marquei a renda imputada pela mediana para manter rastreabilidade. A tabela final tem 149.999 registros. Uma segmentação simples por regras mostrou taxas observadas diferentes entre grupos, mas não é um modelo preditivo e não serve para decidir crédito. Também estou deixando explícitas limitações do arquivo: a atualização ainda usa caminho local e os pivôs e visuais precisam de reconciliação no Excel antes de serem apresentados como atualizados.”

## Cinco decisões técnicas para saber explicar

1. **Manter original e tratado em colunas separadas.** Isso permite comparar valores de origem, transformação e efeito da imputação sem perder rastreabilidade.
2. **Criar `Flag_Renda_Informada` antes da imputação.** A flag diferencia os registros informados dos que receberam mediana global.
3. **Tratar 96 e 98 como sentinelas de ausência nas variáveis de atraso.** Somá-los como eventos reais distorceria os grupos; o projeto os converte em nulos.
4. **Tratar utilização acima de 2 como ausente na coluna limpa, sem remover a linha.** A decisão limita o efeito dos valores extremos na categorização e preserva os demais dados do registro.
5. **Usar uma segmentação explícita por regras, sem chamá-la de score preditivo.** Ela facilita a comparação exploratória, mas não foi calibrada nem validada fora da amostra.

## Perguntas prováveis

**O que significa o fluxo CSV → RAW → CLEAN → pivôs → dashboard?**  
O CSV é a fonte. `RAW_CreditData` faz a ingestão; `CLEAN_pipeline` concentra tratamento e colunas derivadas. As tabelas dinâmicas e o dashboard resumem a tabela final para análise descritiva.

**Por que não chamar `Nivel_Risco` de modelo?**  
Porque é uma regra criada com limiares e categorias do próprio projeto. Não estima probabilidades, não foi treinada nem avaliada em dados separados e não passou por validação de desempenho ou justiça.

**Como você avaliou a qualidade dos dados?**  
Comparei totais de origem e saída, identifiquei a linha com idade zero, contei valores ausentes, documentei a flag de imputação e os 371 valores de utilização acima do limite analítico. A validação dos pivôs e gráficos do workbook ainda precisa ser concluída no Excel.

**Como outra pessoa atualiza a análise?**  
Ainda há uma limitação de portabilidade: `CaminhoArquivo` usa um caminho absoluto local, e a consulta `ETL_LOG_Projeto01` referencia um arquivo que não está no repositório. A configuração precisa ser ajustada no Editor do Power Query antes de prometer reprodução integral.

**Que conclusão é possível tirar sobre renda ou idade?**  
Somente diferenças descritivas observadas nos grupos desta base. Elas não demonstram causalidade nem justificam tratar pessoas de forma diferente; renda e idade podem exigir avaliação de viés e conformidade.

**O que você faria como próximo passo?**  
Corrigiria e validaria o workbook no Excel: resolver a fórmula com divisão por zero e as categorias com pontuação diferente, configurar uma origem portátil, verificar a consulta de log, limpar filtros, atualizar pivôs e reconciliar os visuais com `CLEAN_pipeline`. Depois regeneraria a imagem e o PDF a partir de fontes editáveis e faria a conferência visual.

## Limitações que devem ser apresentadas com clareza

- análise exploratória, sem inferência causal ou teste preditivo fora da amostra;
- regra `Nivel_Risco` descritiva, não modelo de concessão;
- 19,82% das rendas foram imputadas pela mediana global;
- ausência de teste de atualização do Power Query em outro computador;
- workbook não foi alterado nesta revisão; fórmula `EDA!N39`, faixas de renda, alguns caches de pivô e visuais ainda precisam de validação interativa;
- a licença/termos de redistribuição do conjunto não foram confirmados.

