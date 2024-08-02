# Previsão de Estoque Inteligente na AWS com SageMaker Canvas


## Pré-requisitos

Criar uma conta na AWS.


## Objetivos
Realizar a previsão de um estoque inteligente no SageMaker Canvas da AWS, uma ferramenta no-code para machine learning. Abaixo segue o projeto final do Bootcamp Nexa - Machine Learning para Iniciantes na AWS @ Dio, que traz a seleção do dataset limpo, construção do modelo de série temporal, análise, previsão e avaliação final sobre os resultados.


## Passo a Passo

### 1. Select

Selecionado o dataset [dataset-1000-com-preco-promocional-e-renovacao-estoque](https://raw.githubusercontent.com/JoiceO/Projeto-de-estoque-com-AWS-SageMaker-Canvas/main/datasets/dataset-1000-com-preco-promocional-e-renovacao-estoque.csv), que possui 1000 linhas e as colunas:
* `ID_PRODUTO`, 
* `DATA_EVENTO`, 
* `PRECO`, 
* `FLAG_PROMOCAO`, 
* `QUANTIDADE_ESTOQUE`

Em seguida, foi feito upload do arquivo CSV no SageMaker Canvas.


### 2. Build

A configuração do modelo foi feita da seguinte forma para uma _time series forecasting_:
* Coluna-alvo: `QUANTIDADE_ESTOQUE`
* Coluna de chave-primária: `ID_PRODUTO`
* Coluna para agrupamento (é opcional): `FLAG_PROMOCAO`
* Coluna de data: `DATA_EVENTO`
* Especificar o número de dias futuros a serem previstos: 7
* Selecionada a opção de usar feriados no Brasil
<img src="/building and analysing/projetoEstoque-etapa2-build.png">
Por fim, a geração do modelo foi feita pelo Quick Build.


### 3. Analyse

Após o treinamento, apareceram as métricas,descritas abaixo junto aos seus significados:
<img src="/building and analysing/projetoEstoque-etapa3-analyse.png">
 
**1. Avg. wQL (Average Weighted Quantile Loss -> Perda da Média Quantil Ponderada):<br>**
Esta é uma forma de ver se as previsões estão próximas da realidade.
Mede a perda ponderada em diferentes quantis, ajudando a avaliar a precisão de previsões em diferentes níveis de probabilidade. É útil para entender a qualidade das previsões, especialmente quando há variação na distribuição dos dados.<br>
_Valor_: 0.528<br>
_Significado_: Quanto menor, melhor. Um valor de 0,528 indica que o modelo tem um desempenho moderado na previsão de quantis.

**2. MAPE (Mean Absolute Percentage Error -> Erro Percentual Médio Absoluto):<br>**
Mostra, em média, quanto a previsão erra, comparado com o número real do modelo. Calcula a média dos erros absolutos em porcentagem, útil para comparar previsões em diferentes escalas.<br>
_Valor_: 1.036<br>
_Significado_: Um MAPE de 1,036% indica que, em média, as previsões do modelo desviam-se 1,036% dos valores reais. Este é um valor bastante baixo, indicando alta precisão.

**3. WAPE (Weighted Absolute Percentage Error -> Erro Percentual Absoluto Ponderado):<br>**
Similar ao MAPE, mas mede o erro absoluto como uma porcentagem da soma dos valores reais.<br>
_Valor_: 0.795<br>
_Significado_: Um WAPE de 0.795 indica que o erro percentual ponderado das previsões é de 0,795%, o que sugere uma boa precisão.

**4.RMSE (Root Mean Squared Error -> Raiz do Erro Quadrático Médio):<br>**
Enxerga o tamanho médio do nosso erro, mas levando em conta que errar por muito é pior do que errar por pouco. <br>
Calcula a raiz quadrada da média dos erros quadrados, destacando erros grandes.<br>
_Valor_: 36.263<br>
_Significado_: Um RMSE de 36.263 indica que, em média, a diferença entre os valores previstos e os valores reais é de 36.263 unidades. Quanto menor, melhor, mas a interpretação exata depende do contexto e da escala dos dados, porque o RMSE é expresso nas mesmas unidades dos dados. Se as unidades são grandes, o RMSE também será grande

**5. MASE (Mean Absolute Scaled Error -> Erro Escalado Médio Absoluto):<br>**
Compara o erro absoluto médio do modelo com o erro absoluto médio de um modelo de referência (geralmente um modelo ingênuo, que não muda por usar a média histórica ou o último valor observado).<br>
Esta é uma forma de ver se as previsões são mais precisas ou não do que se estivéssemos usando um modelo de referência fixo.<br>
_Valor_: 0.968<br>
_Significado_: Um MASE de 0.968 significa que o modelo tem um desempenho ligeiramente melhor do que o modelo de referência (um valor menor que 1 é desejável). Em outras palavras, as previsões são melhores do que simplesmente usar o valor anterior como previsão (estratégia de persistência).


### 4. Predict

Por ser uma conta gratuita, só foi possível fazer a previsão simples (unitária) e não em lote.<br><br>
Foram selecionados dois itens, 1008 e 1020, para realizar previsões de estoque sob duas condições:<br>
`Em promoção (FLAG_PROMOCAO = 1)`<br>
`Sem promoção (FLAG_PROMOCAO = 0)`<br><br>
Os resultados estão apresentados em quatro figuras abaixo. A demanda real do estoque nos primeiros seis dias é representada em azul. As previsões para os próximos dias são mostradas em:
- P10 (10º Percentil, em rosa): Representa um cenário de baixa demanda. Apenas 10% das previsões são menores que este valor, enquanto 90% são maiores. Isso significa que, com previsão mínima, evita-se excessos no estoque.
- P50 (50º Percentil, em verde): Mostra a demanda média esperada. É o valor central das previsões.
- P90 (90º Percentil, em marrom): Representa um cenário de alta demanda. Apenas 10% das previsões são maiores que este valor, enquanto 90% são menores.

  
Figura 1 – Vendas diárias do item 1008 em promoção
<img src="/building and analysing/single_prediction_results 1008 com promoção.png">
 
Figura 2 – Vendas diárias do item 1008 sem promoção
<img src="/building and analysing/single_prediction_results 1008 sem promoção.png">
 
Figura 3 – Vendas do item 1020 em promoção
<img src="/building and analysing/single_prediction_results 1020 com promoção.png">
 
Figura 4- Vendas do item 1020 sem promoção
<img src="/building and analysing/single_prediction_results 1020 sem promoção.png">

### 5. Avaliação final
Pensando na otimização do estoque e planejamento promocional, pode-se:<br>
1. Manter estoque base com base nos valores de P50;
2. Fazer ajustes para promoções com base nos valores de P90. Isso garantirá produtos suficientes para atender ao aumento na demanda durante a promoção;
3. Minimizar o risco de excesso de estoque, e demais custos de armazenamento, em períodos de baixa demanda por meio das previsões do P10;
4. Realizar monitoramento contínuo para evitar excessos ou faltas durantes campanhas promocionais ou feriados e assim ajustar o estoque com antecedência.



