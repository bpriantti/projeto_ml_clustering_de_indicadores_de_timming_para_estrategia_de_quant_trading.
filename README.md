# Projeto ML Clustering de Indicadores de Timming para Estrategia de Quant Trading.

__Bussines Problem:__

> Durante o desenvolvimento de estratégias de quant trading, que nada mais são do que sistemas objetivos para obter lucros no mercado de renda variável, decidindo a melhor hora para comprar ou vender determinado ativo com base em padrões estatísticos que tiveram uma boa performance no passado, torna-se necessário um estudo quantitativo sobre o indicador de timing ADX,DI+,DI-, idealizado por Willes Wilder, verificando se existe uma parametrização lucrativa para comprar ou vender o ativo PETR4 com base neste indicador.

__Objetivo:__

> Desenvolver uma estratégia quantitativa utilizando o conceito de clusterização, por meio do algoritmo k-means em seguida realizar uma análise bivariada entre um alvo de 5 dias para o retorno e os clusters verificando em um rank qual tendeu a ser mais lucrativo para a base de treinamento e em seguida validar isto em uma base de teste.

__Autor:__  
   - Bruno Priantti.
    
__Contato:__  
  - bpriantti@gmail.com

__Encontre-me:__  
   -  https://www.linkedin.com/in/bpriantti/  
   -  https://github.com/bpriantti
   -  https://www.instagram.com/brunopriantti/
   
__Frameworks Utilizados:__

- Numpy: https://numpy.org/doc/  
- Pandas: https://pandas.pydata.org/
- Matplotlib: https://matplotlib.org/ 
- Seaborn: https://seaborn.pydata.org/  
- Plotly: https://plotly.com/  
- Scikit learn: https://scikit-learn.org/stable/index.html
- Statsmodels: https://www.statsmodels.org/stable/index.html

___

## Contents
 - [Importando Libraries](#importando-libraries) 
 - [Data Request](#data-request) 
 - [Data Wralling](#data-wralling)
 - [Data Visualization](#data-visualization)
 - [Feature Calculation](#feature-calculation)
 - [Train Test Split](#train-test-split)
 - [Elbow Method Clusters](#elbow-method-clusters)
 - [Treinando Modelo K-Means](#treinando-modelo-k-means)

### Importando Libraries:
> inicialmente para este projeto realizou-se o import das bibliotecas que serao utilizadas para machine learning, data wralling e data visualization dos dados, utilizou-se os comandos abaixo para esta etapa:

```
#import libs:
import pandas as pd
import numpy as np

#libs para visualization dos dados:
import seaborn as sns
import matplotlib.pyplot as plt
import matplotlib.dates as md

#---:
import plotly.express as px
import plotly.graph_objects as go
import plotly.graph_objects as Dash
from plotly.subplots import make_subplots

#lib para api com ativos da bolsa:
import yfinance as yf
import talib as ta

#---
import warnings
warnings.filterwarnings("ignore")
```

### Data Request:

> Em seguida realizou-se o processo de data request, com o provedor de dados yfinace, utilizou-se a conexao API com o servidor, realizou-se o request da serie historica para o ativo PETR4, do periodo de 2005 a 2022, utilizou-se o codigo abaixo para esta etapa:

```
database = yf.download('PETR4.SA', '2005-1-1','2022-12-31')
```

### Data Wralling:

> Em seguida realizou-se o processo de data wralling, que consiste em tratamentos na base de dados para posterior uso dos dados para o desenvolvimento do modelo de machinne learning e backtesting, realizou-se esta etapa pelo codigo abaixo:

```
database['Open']  = database.Open * database['Adj Close']/database['Close']
database['High']  = database.High * database['Adj Close']/database['Close']
database['Low']   = database.Low  * database['Adj Close']/database['Close']
database['Close'] = database['Adj Close']

del database['Adj Close']
database.dropna(inplace=True)
```
### Data Visualization:

> Apos o tratamento dos dados no processo de data wralling, realizou-se o processo de Data Visualization, que consiste em visualizar a base de dados, para o caso atual foi realizado esta etapa para verificar possiveis incosistencia visivieis na serie historica.

<p align="center">
   <img src="https://github.com/bpriantti/projeto_ml_clustering_de_indicadores_de_timming_para_estrategia_de_quant_trading./blob/main/images/image-1.png?raw=true"  width="800" height = "460">
   
### Feature Calculation: 

> Nesta Etapa realizou-se o calculo das features DI+,DI-,ADX e Retorno Futuro em 2 dias, em seguida visualizou-se o histograma para essas features, utilizando os comandos abaixo.

```
#calc adx
database['adx']         = ta.ADX(database['High'],database['Low'],database['Close'],14)
database['pos_dir_mov'] = ta.PLUS_DI(database['High'],database['Low'],database['Close'],14)
database['neg_dir_mov'] = ta.MINUS_DI(database['High'],database['Low'],database['Close'],14)

#calc retorno futuro
database['target_var'] = database['Close'].pct_change(2).shift(-2)
database.dropna(inplace=True)

#visualization:
database.loc[:,'adx':].hist(figsize = (15,10), rwidth = 0.95);
```

<p align="center">
   <img src="https://github.com/bpriantti/projeto_ml_clustering_de_indicadores_de_timming_para_estrategia_de_quant_trading./blob/main/images/image-2.PNG?raw=true"  width="800" height = "460">
 
 ### Train-Test Split:
 
 > Para realizar o treino e em seguida o teste do modelo, realizou-se a divisao da base em data-train e data-test.
 
```
#data train-test split:
data_train = database.loc['2005-02-14':'2012-12-31']
data_test = database.loc['2013-01-01':]

#features x, train-test:
x_train = data_train.loc[:,'adx':'neg_dir_mov']
x_test = data_test.loc[:,'adx':'neg_dir_mov']
```

### Elbow Method Clusters:

> Utilizou-se o método de elbow method para verificar o número de clusters necessário, verificou-se que clusters entre 4 e 6 demonstraram bons resultados de agrupamento:
   
<p align="center">
   <img src="https://github.com/bpriantti/projeto_ml_clustering_de_indicadores_de_timming_para_estrategia_de_quant_trading./blob/main/images/image-3.png?raw=true"  width="800" height = "460">

### Treinando Modelo K-Means:

> Em seguida realizou-se o treinando do modelo com 5 clusters, e visualizou-se os clustres em um plot 3d e tambem o histograma de frequencia para os clusters 

```
#fit k-means model:
kmodel = KMeans(n_clusters = 5, random_state = 1)
clusters = kmodel.fit(k)
```

```
#data visualization:
df = data_train.loc[:,'adx':]

fig = px.scatter_3d(df, x='pos_dir_mov', y='neg_dir_mov', z='adx',
                    color='clusters',height=500, width=800, title = 'Clusters Plot: ')

fig.update_layout(margin=dict(l=10, r=10, t=50, b=10))

fig.show()
```
- Clusters Plot:
<p align="center">
   <img src="https://github.com/bpriantti/projeto_ml_clustering_de_indicadores_de_timming_para_estrategia_de_quant_trading./blob/main/images/cluster_3d.png?raw=true"  width="740" height = "460">

- Histograma Frequencia Clusters:
<p align="center">
   <img src="https://github.com/bpriantti/projeto_ml_clustering_de_indicadores_de_timming_para_estrategia_de_quant_trading./blob/main/images/image-4.PNG?raw=true"  width="740" height = "300">
