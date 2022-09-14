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
   <img src="https://github.com/bpriantti/projeto_asset_allocation_otimizacao_e_backtest_carteiras_de_investimento/blob/main/files_/image_1.png?raw=true"  width="800" height = "220">
