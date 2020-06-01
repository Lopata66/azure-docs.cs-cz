---
title: Nasazení modelu ML na Edge Azure SQL pomocí ONNX
description: V třetí části tohoto kurzu Azure SQL Edge pro předpověď nečistot železa spustíte modely strojového učení ONNX pro SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 02850b8780e70d402cff2b8fd301c2f79cd6c55e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235069"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Nasazení modelu ML na Edge Azure SQL pomocí ONNX 

V třetí části tohoto kurzu pro předpověď nečistot železa ve službě Azure SQL Edge:

1. Pomocí Azure Data Studio se připojte k SQL Database v instanci Azure SQL Edge.
2. Předpovídat nečistoty železa pomocí ONNX ve službě Azure SQL Edge.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Připojení k SQL Database v instanci Azure SQl Edge

1. Otevřete Azure Data Studio.

2. Na kartě **Vítejte** spusťte nové připojení s následujícími podrobnostmi:

   |_Pole_|_Hodnota_|
   |-------|-------|
   |Typ připojení| Microsoft SQL Server|
   |Sloužit|Veřejná IP adresa zmíněná na virtuálním počítači, který se vytvořil pro tuto ukázku|
   |Uživatelské jméno|sa|
   |Heslo|Silné heslo, které se použilo při vytváření instance Azure SQL Edge|
   |Databáze|Výchozí|
   |Skupina serverů|Výchozí|
   |Název (volitelné)|Zadání volitelného názvu|

3. Klikněte na **připojit** .

4. V části **soubor** otevřete nový Poznámkový blok nebo použijte klávesovou zkratku Alt + Windows + N. 

5. Nastavte jádro na Python 3.

## <a name="predict-iron-ore-impurities-with-onnx"></a>Předpověď nečistoty železa pomocí ONNX

Do poznámkového bloku Azure Data Studio zadejte následující kód Pythonu a spusťte ho.

1. Nejdřív nainstalujte a importujte požadované balíčky.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. Definujte pracovní prostor Azure AutoML a konfiguraci experimentu AutoML pro regresní experiment.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. Importujte datovou sadu do rámce Panda. Pro účely školení modelů použijte školicí [předpověď kvality sady dat v procesu dolování](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) z Kaggle. Stáhněte si datový soubor a uložte ho místně na svém vývojovém počítači. Tato data použijete k odhadu, kolik nečistoty je v rudy.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. Analyzujte data a Identifikujte případné zkosení. Během tohoto procesu se podívejte na distribuci a informace o zkosení pro každý sloupec v datovém rámečku.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. Kontroluje a opravuje úroveň asymetrie v datech.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. Podívejte se na korelaci dalších funkcí s funkcí předpovědi. Pokud korelace není vysoká, odeberte tyto funkce.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. Spusťte experiment AzureML, který vyhledá a vyškolí nejlepší algoritmus. V tomto případě testujete se všemi regresními algoritmy s primární metrikou normalizované střední chyby (NRMSE). Další informace najdete v části [experimenty Azure ml – primární metrika](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric). Následující kód spustí místní běh experimentu ML.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. Načtěte model v databázi Azure SQL Edge pro místní bodování.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. Nakonec použijte model hraničních zařízení Azure SQL k provádění předpovědi pomocí trained model.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. Pomocí Pythonu vytvořte graf předpovězeného procenta oddálení v informačním kanálu železa, DateTime a křemenném kanálu.

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>Další kroky

Další informace o používání modelů ONNX ve službě Azure SQL Edge najdete v tématu [Machine Learning a AI s ONNX v SQL Edge (Preview)](onnx-overview.md).
