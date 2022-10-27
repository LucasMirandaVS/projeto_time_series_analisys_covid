# Analisando a Pandemia na região Sudeste

Neste projeto, realizei uma análise do número de casos confirmados e óbitos por COVID19 nos estados da região sudeste do Brasil. A análise foi realizada através da modelagem e previsão de séries temporais. O projeto foi realizado em Python, usando especialmente os pacotes numpy, pandas, seaborn, matplotlib e statsmodels.

O presente projeto tem como objetivo modelar e comparar as previsões realizadas para cada um dos estados analisados, portanto o foco está na análise de sazonalidade e acurácia das estimativas. Os dados utilizados neste projeto foram disponibilizados pela Alura para um de seus cursos de análise estatística e vão desde as primeiras semanas da pandemia até o início de 2021, de modo a englobar o inpicio do preíodo de vacinação no país

## Decomposição, Tendência de Sazonalidade

A primeira coisa a ser feita é dividir a base de dados originais em quatro dataframes diferentes para cada um dos estados analisados. Dessa forma é possível decompor as séries históricas com mais facilidade, e verificar se elas possuem tendência, sazonalidade e resíduos. Vale destacar que como os dados para óbitos por COVID19 são computados semanalmente, é de se esperar a existência de um componente sazonal bem definido. Uma vez decompostas, é possível comparar os dados de óbitos em cada um dos estados:

### Tendência
Ao comparar as tendências dos estados do sudeste, é possível perceber um período de aumentos nos óbitos, seguido de um um claro achatamento da curva no Rio de Janeiro. É possível perceber também um padrão similar no Espírito Santo. Em contrapartida, há um aumento bem mais expressivo em São Paulo. Em Minas Gerais também houve uma tendência de crescimento, porém ela tem uma taxa bem menor do que a taxa de São Paulo.
![image](https://user-images.githubusercontent.com/77032413/190643532-38d16c46-6be6-410f-94ab-6a5b1f50ecb0.png)

### Sazonalidade
Conforme mencionado anteriormente, em virtude da forma como são computados os dados de óbitos por COVID, esperava-se encontrar um componente sazonal bem definido. E esta expectativa se confirmou ao analisar os componentes sazonais dos estados. 
![image](https://user-images.githubusercontent.com/77032413/190644981-b434f46c-ace1-4937-a9fb-2c37f6b158ca.png)

## Estacionariedade 

Antes de implementar os modelos para previsão, é preciso verificar se as séries são de fato estacionárias. A definição prática de uma série estacionária é quando a média e a variância são constantes ao longo do tempo, ou seja, quando a série não tem nenhuma tendência, e nem sazonalidade.

Para verificar a existencia ou não de estacionariedade, foram aplicados os testes ADF em cada uma delas. Este é um teste de hipótes, e basicamente vai determinar o quão uma série temporal pode ser definida pela sua tendência. Se o valor p for abaixo de um valor limite, que normalmente é 0.05, nós rejeitamos a hipótese nula e definimos que nossa série temporal é estacionária. Se calcularmos o p valor e ele der muito mais alto do que 0.05, provavelmente nossa série temporal é não estacionária. Como esperado, o teste ADF das séries mostrou que elas não são estacionárias e precisarão de um tratamento especial antes de realizar a previsão. 

![image](https://user-images.githubusercontent.com/77032413/190648460-8e87d35f-1d85-482c-b50a-9a1ae6a0d5f1.png)

## Previsões
### ARIMA E SARIMA
O modelo implementado neste projeto se trata de um ARIMA. Este modelo é a junção de três modelos estatísticos, e por isso têm alguns parâmetros que precisam ser ajustados. Temos o p, que é relacionado à parte do modelo autorregressivo, temos o q, que é relacionado à parte do moving average e temos o d, que é a parte da diferenciação.

O próximo passo é definir quais os valores para cada um dos parametros descritos acima melhor se adequa a cada uma das séries. Para isso, basta verificar o valor da métrica AIC para cada um deles. AIC na verdade é um acrônimo, significa critério de informação arcaic e ele basicamente vai comparar o resultado que você obteve e uma métrica de distância entre o resultado que você obteve e o modelo estatístico real. Quanto menor o valor do AIC, melhor.

Entretanto, apenas implementar o modelo ARIMA com o menos AIC não produzirá as melhores previsões para as séries. Conforme destacado anteriormente, há um claro componente sazonal em todas as séries que precisa ser levado em consideração. Se faz necessária a utilização de um modelo que desconte o peso do componente sazonal para cada uma das séries. Portanto, optou-se pelo modelo SARIMA, que é uma versão do ARIMA com a adição do componente sazonal. No SARIMA, além dos componentes p, d e q do ARIMA tradicional, há também mais 4 parametros sazonais que são levados em conta.

### Prevendo o Número de Casos

Primeiro, foquei em analisar os melhores modelos SARIMA para os dados históricos de casos de COVID19. Após a realização dos testes, os modelos SARIMA com os menores valores de AIC são os seguintes:

* São Paulo: (1, 1, 1) x (1, 1, 1, 7) 
* Minas Gerais: (1, 1, 1) x (0, 1, 1, 7) 
* Rio de Janeiro: (1, 1, 1) x (0, 1, 1, 7) 
* Espírito Santo: (1, 1, 1) x (0, 1, 1, 7) 

Uma vez estabelecidos os parâmetros dos SARIMA para cada um dos estados, é hora de comparar as previsões para cada um dos estados. O período escolhido de previsão é de 150 dias no futuro. Vale salientar que não é recomendado realizar previsões de períodos tão avançados, pois a medida que adicionamos dias para a previsão, a acurácia de modelo diminui. Portanto o SARIMA é mais indicado para previsões mais curtas. Contudo, como o objetivo do trabaho era comparar as estimações, esse problema não era algo sensível para a análise.

Ao comparar as previsões dos modelos é possível constatar que com exceção do estado de São Paulo, as demais previsões se mostraram satisfatóriamente precisas, estando dentro do intervalo de confiança estabelecido para a previsão. Isto pode ser confirmado ao verificar que para os estados de Minas, Rio e Espírito Santo, a linha de previsão (azul) encontra-se dentro da área do intervalo de confiança na cor cinza. Por isso podemos afirmar que para os dados de COVID19 analisados, os modelos SARIMA tiveram resultados variados porém satisfatórios para 3 dos 4 estados analisados.

#### São Paulo
![image](https://user-images.githubusercontent.com/77032413/190657430-628213e6-4c37-4e62-98ee-5cbc2d7ee290.png)

#### Minas Gerais
![image](https://user-images.githubusercontent.com/77032413/190657610-2f502ada-5912-48c9-a900-4daa5b1e8a34.png)

#### Rio de Janeiro
![image](https://user-images.githubusercontent.com/77032413/190657772-429e252e-164b-4f1f-974d-99da2b503548.png)

#### Espírito Santo
![image](https://user-images.githubusercontent.com/77032413/190657910-2ba9424c-39e4-43c0-aca5-494c9befc680.png)

### Prevendo o Número de Óbitos
Para realizar a previsão do número de óbitos, foi realizado o mesmo procedimento das séries de número de casos. Os modelos SARIMA com os menores valores do AIC para cada estado foram os seguintes:

* São Paulo: (1, 1, 1) x (0, 1, 1, 7) 
* Minas Gerais: (0, 1, 1) x (0, 1, 1, 7) 
* Rio de Janeiro: (0, 1, 1) x (0, 1, 1, 7) 
* Espírito Santo: (1, 1, 1) x (0, 1, 1, 7) 

Os valores dos parâmetros estão levemente diferentes dos SARIMA para os números de casos, o que era esperado dado que são séries com valores diferentes. Desta vez- optou-se por realizar a previsão para um número menor de dias, de modo a garantir uma maior acurácia para os modelos. O período escolhido portanto foi de 25 dias de previsão.

Ao comparar as previsões dos modelos é possível constatar que todas as previsões se mostraram satisfatóriamente precisas, estando dentro do intervalo de confiança estabelecido para a previsão. Isto pode ser confirmado ao verificar que para os estados de Minas, Rio e Espírito Santo, a linha de previsão encontra-se dentro da área do intervalo de confiança na cor cinza. Esse resultado também corrobora a constatação que os modelos SARIMA realizam previsões mais precisas para um número menor de dias. Também é possível perceber como a sazonalidade encontra-se presente nos dados históricos para óbito, em virtude dos dados serem computados em frequcnia semanal. 

#### São Paulo
![image](https://user-images.githubusercontent.com/77032413/190660463-f27a3a30-8c06-4fe8-91d9-ce2ea0985ebb.png)

#### Minas Gerais
![image](https://user-images.githubusercontent.com/77032413/190660641-a1c3baeb-4916-4bd7-8017-0288791af342.png)

#### Rio de Janeiro
![image](https://user-images.githubusercontent.com/77032413/190660779-a7bc0a03-1b0d-4ef9-a49d-7ad45d8eea2c.png)

#### Espírito Santo
![image](https://user-images.githubusercontent.com/77032413/190660976-cdf1d02d-03d2-4a1d-9f91-040018d5279f.png)

# Conclusão
O objetivo deste projeto era comparar a evolução do número de novos casos e óbitos por COVID19 entre os estados da região sudeste do Brasil. Através da modelagem e visualização de séries temporais, foi possível constatar que enquanto houve um achatemento na curva de óbitos no Rio de Janeiro e espírito Santo, a curva de óbitos para Minas e São Paulo encontra-se em tendência de alta, e as previsões dos modelos para os novos óbitos se mostraram satisfatoriamente precisas. Para o número de casos encontrou-se que apesar de um aumento inicial nas curvas de casos, as previsões mostraram-se precisas para todos os estados com a exceção de São Paulo.

Fica como recomendação para trabalhos futuros a atualização dos dados para inferir como foi o andamento da pandemia na região Sudeste em um intervalo maior de tempo. Os scripts com a análise completa e resultados detalhados encontram-se nos arquivos Jupyter Notebook deste repositório.

