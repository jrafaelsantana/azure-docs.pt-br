---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845999"
---
- O SDK do Azure Machine Learning para Python instalado. Use as instruções em [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para fazer o seguinte:


1. Use as instruções em [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para fazer o seguinte:
    * Criar um ambiente Miniconda[Criar e gerenciar workspaces do serviço do Azure Machine Learning]
    * Instalar o SDK do Azure Machine Learning para Python

1. Criar um [workspace do Serviço do Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Escrever um [arquivo de configuração](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de notebook do seu diretório clonado.

    ```shell
    jupyter notebook
    ```