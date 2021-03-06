---
title: O que é um espaço de trabalho
titleSuffix: Azure Machine Learning
description: O espaço de trabalho é o recurso de nível superior para Azure Machine Learning. Ele mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts. Você usa essas informações para determinar qual execução de treinamento produz o melhor modelo
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: de215502f1ecb55bc331f29057a4f7c3f30b0132
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720169"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é um espaço de trabalho Azure Machine Learning?

O espaço de trabalho é o recurso de nível superior para Azure Machine Learning, fornecendo um local centralizado para trabalhar com todos os artefatos que você criar ao usar Azure Machine Learning.  O espaço de trabalho mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts. Essas informações são usadas para determinar quais execuções de treinamento produzem o melhor modelo.  

Quando você tiver um modelo que desejar, registre-o no espaço de trabalho. Em seguida, você usa o modelo registrado e os scripts de Pontuação para implantar nas instâncias de contêiner do Azure, no serviço kubernetes do Azure ou em uma FPGA (matriz de porta programável por campo) como um ponto de extremidade HTTP baseado em REST. Você também pode implantar o modelo em um dispositivo Azure IoT Edge como um módulo.

## <a name="taxonomy"></a>Taxonomia 

O diagrama a seguir é uma taxonomia do workspace:

[![Taxonomia de espaço de trabalho](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de um espaço de trabalho:

+ Um espaço de trabalho pode conter [VMs de notebook](tutorial-1st-experiment-sdk-setup.md), recursos de nuvem configurados com o ambiente Python necessário para executar o Azure Machine Learning.
+ As [funções de usuário](how-to-assign-roles.md) permitem que você compartilhe seu espaço de trabalho com outros usuários, equipes ou projetos.
+ Os [destinos de computação](concept-azure-machine-learning-architecture.md#compute-targets) são usados para executar seus experimentos.
+ Quando você cria o espaço de trabalho, os [recursos associados](#resources) também são criados para você.
+ Os [experimentos](concept-azure-machine-learning-architecture.md#experiments) são as execuções de treinamento que você usa para criar seus modelos.  Você pode criar e executar experimentos com
    + O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + A seção [experimentos automatizados do Machine Learning (versão prévia)](how-to-create-portal-experiments.md) no portal do Azure ou na página de aterrissagem do espaço de trabalho (versão prévia).
    + A [interface visual (visualização)](ui-concept-visual-interface.md).
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) são fluxos de trabalho reutilizáveis para treinar e treinar novamente seu modelo.
+ [DataSets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) auxiliam no gerenciamento dos dados que você usa para treinamento de modelo e criação de pipeline.
+ Quando você tiver um modelo que deseja implantar, crie um modelo registrado.
+ Use o modelo registrado e um script de Pontuação para criar uma [implantação](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação do espaço de trabalho

Você pode interagir com seu espaço de trabalho das seguintes maneiras:

+ Na Web:
    + O [Portal do Azure](https://portal.azure.com)
    + [Página de aterrissagem do espaço de trabalho (visualização)](https://ml.azure.com)
    + A [interface visual (visualização)](ui-concept-visual-interface.md)
+ No Python usando o [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) do Azure Machine Learning
+ Na linha de comando usando a [extensão da CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) do Azure Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Aprendizado de máquina com um espaço de trabalho

As tarefas de aprendizado de máquina lêem e/ou gravam artefatos em seu espaço de trabalho. 

+ Executar um experimento para treinar um modelo – grava os resultados da execução do experimento no espaço de trabalho.
+ Use o ML automatizado para treinar um modelo – grava os resultados de treinamento no espaço de trabalho.
+ Registrar um modelo no espaço de trabalho.
+ Implantar um modelo – usa o modelo registrado para criar uma implantação.
+ Crie e execute fluxos de trabalho reutilizáveis.
+ Exiba artefatos de Machine Learning, como experimentos, pipelines, modelos, implantações.
+ Acompanhe e monitore modelos.

## <a name="workspace-management"></a>Gerenciamento de espaço de trabalho

Você também pode executar as seguintes tarefas de gerenciamento de espaço de trabalho:

| Tarefa de gerenciamento de espaço de trabalho   | Portal              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Criar um espaço de trabalho        | **&check;**     | **&check;** | **&check;** |
| Criar e gerenciar recursos de computação    | **&check;**   | **&check;** |  **&check;**   |
| Gerenciar o acesso ao espaço de trabalho    | **&check;**   | |  **&check;**    |
| Criar uma VM de notebook | **&check;**   | |     |

## <a name='create-workspace'></a>Criar um espaço de trabalho

Há várias maneiras de criar um espaço de trabalho.

* Use o [portal do Azure](how-to-manage-workspace.md) para uma interface de apontar e clicar para orientá-lo em cada etapa.
* Use o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) para criar um espaço de trabalho instantaneamente de scripts Python ou notebooks Júpiter
* Use um [modelo de Azure Resource Manager](how-to-create-workspace-template.md) ou a [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md) quando precisar automatizar ou personalizar a criação com os padrões de segurança corporativa.
* Se você trabalhar em Visual Studio Code, use a [extensão vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="resources"></a>Recursos associados

Quando você cria um novo workspace, ele automaticamente cria vários recursos do Azure que são usados pelo workspace:

+ [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/): Registra os contêineres do docker que a serem usados durante o treinamento e ao implantar um modelo. Para minimizar os custos, o ACR é **carregado com o preguiçoso** até que as imagens de implantação sejam criadas.
+ [Conta de Armazenamento do Azure](https://azure.microsoft.com/services/storage/): Usado como o repositório de dados padrão para o workspace.  Os notebooks Jupyter usados com suas VMs de notebook também são armazenados aqui.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Armazena informações de monitoramento sobre seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos usados por destinos de computação e outras informações confidenciais necessárias para o workspace.

> [!NOTE]
> Além de criar novas versões, você também pode usar os serviços existentes do Azure.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure Machine Learning, consulte:

+ [Visão geral de Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Criar um workspace](how-to-manage-workspace.md)
+ [Gerenciar um workspace](how-to-manage-workspace.md)
+ [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
