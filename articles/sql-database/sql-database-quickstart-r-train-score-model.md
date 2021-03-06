---
title: Criar e treinar um modelo preditivo no R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Criar um modelo preditivo simples em R usando os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia) e, em seguida, prever um resultado usando dados novos.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: c1719064de53b79a127146d0ab034f461657cc64
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714901"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Criar e treinar um modelo preditivo em R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Neste início rápido, você criará e treinará um modelo preditivo usando o R, salvará o modelo em uma tabela no Banco de Dados SQL e usará o modelo para prever valores de novos dados usando a versão prévia pública dos [Serviços do Machine Learning (com R) no Banco de Dados SQL do Azure](sql-database-machine-learning-services-overview.md). 

O modelo que você usará neste início rápido é um modelo de regressão simples que prevê a distância de parada de um carro com base na velocidade. Você usará o conjunto de dados **carros** incluído com o R, pois ele é pequeno e fácil de entender.

> [!TIP]
> Há vários conjuntos de dados, pequenos e grandes, incluídos no tempo de execução de R. Para obter uma lista dos conjuntos de dados instalados com R, digite `library(help="datasets")` em um prompt de comando de R.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, primeiro você precisa ter um Banco de dados SQL do Azure com os Serviços do Machine Learning (com R) habilitados. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

- Verifique se você instalou a versão mais recente do SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)). Você pode executar os scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

- Este início rápido exige a configuração de uma regra de firewall no nível de servidor. Para obter informações sobre como fazer isso, confira [Criar regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md).

## <a name="create-and-train-a-predictive-model"></a>Criar e treinar um modelo preditivo

Os dados de velocidade do carro no conjunto de dados **carros** contém duas colunas, ambas numéricas: **dist** e **velocidade**. Os dados incluem várias observações de parada em velocidades diferentes. Com esses dados, você criará um modelo de regressão linear que descreve a relação entre a velocidade do carro e a distância necessária para que ele pare.

Os requisitos de um modelo linear são simples:
- Defina uma fórmula que descreve a relação entre a variável dependente *velocidade* e a variável independente *distância*.
- Forneça dados de entrada para usar no treinamento do modelo.

> [!TIP]
> Caso você precise relembrar os modelos lineares, experimente este tutorial, que descreve o processo de ajuste de um modelo usando rxLinMod: [Ajustando modelos lineares](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

Nas etapas a seguir, você configurará os dados de treinamento, criará um modelo de regressão, treinará esse modelo usando os dados de treinamento e o salvará em uma tabela SQL.

1. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados SQL.

   Se você precisar de ajuda para se conectar, veja o [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Crie a tabela **CarSpeed** para salvar os dados de treinamento.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Crie um modelo de regressão usando `rxLinMod`. 

   Para criar o modelo, defina a fórmula dentro do código R e, em seguida, passe **CarSpeed** dos dados de treinamento como um parâmetro de entrada.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     O primeiro argumento para rxLinMod é o parâmetro *formula*, que define a distância como dependente da velocidade. Os dados de entrada são armazenados na variável `CarsData`, que é preenchida pela consulta SQL.

1. Crie uma tabela na qual armazenar o modelo, para que você possa usá-lo posteriormente para previsão. 

   A saída de um pacote de R que cria um modelo é geralmente um **objeto binário**, então a tabela precisa ter uma coluna de tipo **VARBINARY(max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Agora, chame o procedimento armazenado, gere o modelo e salve-o em uma tabela.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Observe que, se você executar esse código uma segunda vez, receberá este erro:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Uma opção para evitar esse erro é atualizar o nome de cada modelo novo. Por exemplo, você pode alterar o nome para algo mais descritivo e incluir o tipo de modelo, o dia em que ele foi criado e assim por diante.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Exibir a tabela de coeficientes

Em geral, a saída de R do procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) é limitada a uma única estrutura de dados. No entanto, é possível retornar saídas de outros tipos, como escalares, além da estrutura de dados.

Por exemplo, suponha que você queria treinar um modelo, mas queria exibir imediatamente uma tabela de coeficientes do modelo. Para fazer isso, você cria a tabela de coeficientes como o conjunto de resultados principal e gera o modelo treinado como saída em uma variável SQL. Você pode reutilizar o modelo imediatamente chamando a variável ou pode salvar o modelo em uma tabela, conforme mostrado aqui.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Resultados**

![Modelo treinado com saída adicional](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Pontuar novos dados usando o modelo treinado

*Pontuação* é um termo usado em ciência de dados para significar a geração de previsões, probabilidades ou de outros valores com base em novos dados inseridos em um modelo treinado. Você usará o modelo que você criou na seção anterior para pontuar previsões em relação a novos dados.

Você notou que os dados de treinamento originais param a uma velocidade de 40 km/h? Isso ocorre porque os dados originais têm base em um experimento de 1920! Talvez você se pergunte: "quanto tempo um automóvel de 1920 demoraria para parar se ele pudesse chegar a 96 km/h ou até mesmo a 160 km/h?" Para responder a essa pergunta, você pode fornecer alguns novos valores de velocidade para seu modelo.

1. Crie uma tabela com novos dados de velocidade.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Preveja a distância de parada segundo esses novos valores de velocidade.

   Considerando que seu modelo é baseado no algoritmo **rxLinMod** fornecido como parte do pacote **RevoScaleR**, chame a função [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict), em vez da função R genérica `predict`.

   Este script de exemplo:
   - Usa uma instrução SELECT para obter um único modelo da tabela
   - Passa-a como um parâmetro de entrada
   - Chama a função `unserialize` no modelo
   - Aplica a função `rxPredict` com os argumentos apropriados ao modelo
   - Fornece os novos dados de entrada

   > [!TIP]
   > Para a pontuação em tempo real, consulte as [funções de serialização](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) fornecidas pelo RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Resultados**

   ![Conjunto de resultados para previsão da distância de parada](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> Neste script de exemplo, a função `str` é adicionada durante a fase de teste para verificar o esquema dos dados retornado de R. Você pode remover a instrução mais tarde.
>
> Os nomes de coluna usados no script R não são necessariamente passados para a saída do procedimento armazenado. Aqui, a cláusula WITH RESULTS define novos nomes de coluna.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Serviços de Machine Learning do Banco de Dados SQL do Azure com R (versão prévia), confira os seguintes artigos.

- [Serviços de Machine Learning do Banco de Dados SQL do Azure com R (versão prévia)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-quickstart-r-create-script.md)
- [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
- [Trabalhar usando dados do R e do SQL nos Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)