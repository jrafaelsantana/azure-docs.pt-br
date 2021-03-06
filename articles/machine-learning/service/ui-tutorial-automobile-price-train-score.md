---
title: 'Tutorial: Prever o preço de automóveis com a interface visual'
titleSuffix: Azure Machine Learning
description: Saiba como treinar, pontuar e implantar um modelo de machine learning usando uma interface visual do tipo "arrastar e soltar". Este tutorial é a primeira parte de uma série de duas partes sobre a previsão de preços de automóveis usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 08/16/2019
ms.openlocfilehash: 11c65c217ef6c150c47f387f7f80070488a8df89
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996774"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prever o preço de automóveis com a interface visual

Neste tutorial de duas partes, você aprenderá a usar a interface visual do Azure Machine Learning para desenvolver e implantar uma solução analítica preditiva que prevê o preço de qualquer carro. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Na parte um, você configura o ambiente, arrasta e solta conjuntos de dados e módulos de análise sobre uma tela interativa e os conecta um ao outro para criar um experimento. 

Na primeira parte do tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um novo experimento
> * Importar dados
> * Preparar dados
> * Treinar um modelo de machine learning
> * Avaliar um modelo de machine learning

Na [parte dois](ui-tutorial-automobile-price-deploy.md) do tutorial, você aprende a implantar seu modelo preditivo como um serviço Web do Azure, para que seja possível usá-lo para prever o preço de qualquer carro com base nas especificações técnicas enviadas. 

Uma versão concluída deste tutorial está disponível como um teste de exemplo.

Para encontrá-la, na **página Experimentos**, selecione **Adicionar Novo** e, em seguida, selecione o experimento **Amostra 1 – Regressão: Previsão de Preços de Automóveis (Básica)** .

## <a name="create-a-new-experiment"></a>Criar um novo experimento

Para criar um experimento de interface visual, primeiro você precisará de um Workspace do Serviço do Azure Machine Learning. Nesta seção, você aprenderá a criar esses dois recursos.

### <a name="create-a-new-workspace"></a>Criar um novo workspace

Caso você tenha um Workspace do Azure Machine Learning, passe para a próxima seção.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-an-experiment"></a>Criar uma experiência

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).

1. No workspace, selecione **Interface visual**. Em seguida, selecione **Iniciar interface visual**. 

    ![Captura de tela do portal do Azure mostrando como acessar a Interface visual em um workspace do serviço do Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Crie um teste selecionando **+Novo** na parte inferior da janela da interface visual.

1. Selecione **Teste em branco**.

1. Selecione o nome de experimento padrão **"Experimento criado em ...** " na parte superior da tela e renomeie-o para algo significativo. Por exemplo, **"Previsão de preços de automóveis"** . O nome não precisa ser exclusivo.

## <a name="import-data"></a>Importar dados

O aprendizado de máquina depende dos dados. Felizmente, há várias amostras de conjuntos de exemplo incluídas nessa interface, disponíveis para você fazer experimentos. Neste tutorial, use o conjunto de dados de exemplo **Dados de preços de automóveis (brutos)** . 

1. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos de Dados Salvos**; em seguida, selecione **Exemplos** para exibir os conjuntos de dados de exemplo disponíveis.

1. Selecione o conjunto de dados, **Dados de preço de automóvel (brutos)** e arraste-o para a tela.

   ![Arraste os dados para a tela](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Selecione com quais colunas de dados você deseja trabalhar. Digite **Selecionar** na caixa de Pesquisa, na parte superior da paleta, para encontrar o módulo **Selecionar colunas no conjunto de dados**.

1. Clique e arraste no módulo **Selecionar colunas no conjunto de dados** para a tela. Solte o módulo embaixo do módulo do conjunto de dados.

1. Conecte o conjunto de dados que você adicionou anteriormente ao módulo **Selecionar colunas no conjunto de dados** clicando e arrastando. Arraste da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados da tela, até a porta de entrada do módulo **Selecionar colunas no conjunto de dados**, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Crie um fluxo de dados por meio do seu teste quando você conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Conectar módulos](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    O ponto de exclamação vermelho indica que você não definiu as propriedades do módulo ainda.

1. Selecione o módulo **Selecionar colunas no conjunto de dados**.

1. No painel **Propriedades** à direita da tela, selecione **Editar colunas**.

    Na caixa de diálogo **Selecionar colunas**, selecione **TODAS AS COLUNAS** e inclua **todos os recursos**. A caixa de diálogo deve ter esta aparência:

     ![column-selector](./media/ui-tutorial-automobile-price-train-score/select-all.gif)

1. No canto inferior direito, selecione **Ok** para fechar o seletor de coluna.

### <a name="run-the-experiment"></a>Execute o experimento

A qualquer momento, clique na porta de saída de um conjunto de dados ou de um módulo para ver qual é a aparência dos dados nesse momento no fluxo de dados. Se a opção **Visualizar** estiver desabilitada, primeiro será necessário executar o teste.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Depois que o destino de computação estiver disponível, o teste será executado. Quando a execução for concluída, uma marca de seleção verde será exibida em cada módulo.


### <a name="visualize-the-data"></a>Visualizar os dados

Agora que já executou o experimento inicial, visualize os dados para entender mais sobre o conjunto de dados que você tem.

1. Selecione a porta de saída na parte inferior de **Selecionar colunas no conjunto de dados** e, em seguida, selecione **Visualizar**.

1. Clique em diferentes colunas na janela de dados para exibir informações sobre ela.

    No conjunto de dados de exemplo, cada linha representa uma instância de um automóvel e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas nesse conjunto de dados.

    Cada vez que você clica em uma coluna de dados, as informações de **Estatísticas** e a imagem de **Visualização** dessa coluna são exibidas à esquerda.

    [![Visualizar os dados](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)](./media/ui-tutorial-automobile-price-train-score/preview-data.gif#lightbox)

1. Clique em cada coluna para entender mais sobre o conjunto de dados e pense se essas colunas serão úteis para prever o preço de um automóvel.

## <a name="prepare-data"></a>Preparar dados

Normalmente, um conjunto de dados requer algum processamento prévio antes que possa ser analisado. Talvez você tenha observado alguns valores ausentes ao visualizar o conjunto de dados. Os valores ausentes precisam ser limpos para que o modelo possa analisar os dados corretamente. Você removerá quaisquer linhas com valores ausentes. Além disso, a coluna **normalized-losses** tem uma grande proporção de valores ausentes; portanto, você a excluirá do modelo completamente.

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos.

### <a name="remove-column"></a>Remover coluna

Primeiro, remova a coluna **normalized-losses** por completo.

1. Selecione o módulo **Selecionar colunas no conjunto de dados**.

1. No painel **Propriedades** à direita da tela, selecione **Editar colunas**.

    * Mantenha as opções **Com regras** e **TODAS AS COLUNAS** selecionadas.

    * Nos menus suspensos, selecione **Excluir** e **nomes da coluna** e clique dentro da caixa de texto. Digite **normalized-losses**.

    * No canto inferior direito, selecione **Ok** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/ui-tutorial-automobile-price-train-score/exclude-column.gif)
        
    Agora o painel de propriedades para Selecionar colunas no Conjunto de dados indica que ele passará todas as colunas do conjunto de dados, exceto **normalized-losses**.
        
    O painel de propriedades mostra que a coluna **normalized-losses** foi excluída.

1. Clique duas vezes no módulo **Selecionar Colunas no Conjunto de Dados** e digite o comentário "Excluir perdas normalizadas." 
    
    Após digitar o comentário, clique fora do módulo. Uma seta para baixo é exibida para mostrar que o módulo contém um comentário.

1. Clique na seta para baixo para exibir o comentário.

    Agora o módulo mostra uma seta para cima para ocultar o comentário.
        
    ![Comentários](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Limpar dados ausentes

Quando você treina um modelo, precisa fazer algo sobre os dados que estão faltando. Nesse caso, você adicionará um módulo para remover qualquer linha restante que tenha dados ausentes.

1. Digite **Limpar** na caixa de pesquisa para encontrar o módulo **Limpar Dados Ausentes**.

1. Arraste o módulo **Limpar Dados Ausentes** para a tela do teste e conecte-o ao módulo **Selecionar Colunas no Conjunto de Dados**. 

1. No painel Propriedades, selecione **Remover linha inteira** em **Modo de limpeza**.

1. Clique duas vezes no módulo e digite o comentário “Remover linhas de valor ausente".

    Agora seu teste deve ter a seguinte aparência:
    
    ![select-column](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de machine learning

Agora que os dados estão prontos, é possível construir um modelo preditivo. Você usará seus dados para treinar o modelo. Em seguida, você testará o modelo para ver o nível de precisão com o qual ele prevê os preços.

### <a name="select-an-algorithm"></a>Selecionar um algoritmo

**Classificação** e **regressão** são dois tipos de técnicas de algoritmo de machine learning supervisionado. A **classificação** prevê uma resposta com base em um conjunto definido de categorias, como uma cor (vermelho, azul ou verde). A **regressão** é usada para prever um número.

Como você deseja prever o preço, que é um número, use um algoritmo de regressão. Para este exemplo, você usará um modelo de regressão linear.

### <a name="split-the-data"></a>Dividir os dados

Use seus dados para treinar o modelo e testá-lo, dividindo os dados em conjuntos de dados separados de treinamento e teste.

1. Digite **dividir dados** na caixa de pesquisa para encontrar o módulo **Dividir Dados** e conectá-lo à porta esquerda do módulo **Limpar Dados Ausentes**.

1. Selecione o módulo **Dividir dados**. No painel Propriedades, defina a Fração de linhas no primeiro conjunto de dados de saída como 0,7. Dessa forma, usaremos 70% dos dados para treinar o modelo e manteremos 30% para o teste.

1. Clique duas vezes no módulo **Dividir Dados** e digite o comentário "Dividir o conjunto de dados em um conjunto de treinamento(0,7) e um conjunto de teste(0,3)"

### <a name="train-the-model"></a>Treinar o modelo

Treine o modelo fornecendo a ele um conjunto de dados que inclua o preço. O modelo examina os dados e procura correlações entre os recursos de um carro e seu preço.

1. Para selecionar o algoritmo de aprendizado, limpe a caixa de pesquisa da paleta de módulos.

1. Expanda o **Aprendizado de Máquina** e, em seguida, expanda **Inicializar Modelo**. Isso exibe várias categorias de módulos que podem ser usados para inicializar os algoritmos de Aprendizado de Máquina.

1. Para este teste, selecione **Regressão** > **Regressão Linear** e arraste-a para a tela do teste.

1. Encontre e arraste o módulo **Treinar Modelo** para a tela do teste. Conecte a saída do módulo Regressão Linear à entrada esquerda do módulo Treinar Modelo e conecte a saída de dados de treinamento (porta esquerda) do módulo **Dividir Dados** à entrada direita do módulo **Treinar Modelo**.

    ![Captura de tela mostrando a configuração correta do módulo Treinar Modelo. O módulo Regressão Linear se conecta à porta esquerda do módulo Treinar Modelo e o módulo Dividir Dados se conecta à porta direita do módulo Treinar Modelo](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Selecione o módulo **Treinar Modelo**. No painel Propriedades, selecione Iniciar seletor de colunas e, em seguida, digite **preço** ao lado de **Incluir nomes de coluna**. O preço é o valor que o modelo vai prever

    ![Captura de tela mostrando a configuração correta do módulo do seletor de colunas. Com regras > Incluir nomes de coluna > "preço"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    O experimento deverá ser semelhante ao seguinte:

    ![Captura de tela mostrando a configuração correta do experimento após a adição do módulo Treinar Modelo.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Avaliar um modelo de machine learning

Agora que você treinou o modelo usando 70% de seus dados, use-o para pontuar os outros 30% dos dados e ver se o modelo funciona bem.

1. Digite **pontuar modelo** na caixa de pesquisa para encontrar o módulo **Pontuar Modelo** e arraste o módulo para a tela do teste. Conecte a saída do módulo **Treinar Modelo** à porta de entrada esquerda do módulo **Pontuar Modelo**. Conecte a saída de dados de teste (porta direita) do módulo **Dividir Dados** à porta de entrada direita do módulo **Pontuar Modelo**.

1. Digite **avaliar** na caixa de pesquisa para encontrar **Avaliar Modelo** e arraste o módulo para a tela do teste. Conecte a saída do módulo **Pontuar Modelo** à entrada esquerda do módulo **Avaliar Modelo**. O experimento final deve se parecer como o seguinte:

    ![Captura de tela mostrando a configuração correta final do teste.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Execute o experimento usando o recurso de computação que você criou anteriormente.

1. Exiba a saída do módulo **Pontuar Modelo** selecionando a porta de saída do módulo **Pontuar Modelo** e selecione **Visualizar**. A saída mostra os valores previstos para o preço e os valores conhecidos dos dados de teste.

    ![Captura de tela da visualização de saída realçando a coluna "Rótulo Pontuado"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Para ver a saída do módulo **Avaliar modelo**, selecione a porta de saída e, em seguida, selecione **Visualizar**.

    ![Captura de tela mostrando os resultados da avaliação para o teste final.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

As seguintes estatísticas são mostradas para o modelo:

* **MAE (Média de Erros Absolutos)** : A média de erros absolutos (um erro é a diferença entre o valor previsto e o valor real).
* **RMSE (Raiz Quadrada da Média de Erros Quadrados)** : a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
* **Erro absoluto relativo**: a média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado relativo**: a média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: Também conhecido como o valor de R-quadrado, essa é uma métrica estatística que indica se o modelo se encaixa bem nos dados.

Para cada estatística de erro, menos é melhor. Um valor menor indica que as previsões se aproximam mais dos valores reais. Para Coeficiente de Determinação, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Na primeira parte deste tutorial, você concluiu estas etapas:

* Criou um experimento
* Preparar os dados
* Treinar o modelo
* Pontuar e avaliar o modelo

Na segunda parte, você aprenderá a implantar seu modelo como um serviço Web do Azure.

> [!div class="nextstepaction"]
> [Continuar a implantação de modelos](ui-tutorial-automobile-price-deploy.md)
