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
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857306"
---
1. [Criar um workspace do Serviço do Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um arquivo de configuração do workspace ao diretório clonado usando um destes métodos:

    * No [portal do Azure](https://ms.portal.azure.com), selecione **Baixar config.json** na seção **Visão geral** do seu workspace. 

    ![Baixe o config.json](./media/aml-dsvm-server/download-config.png)

    * Criar um novo workspace usando o código no notebook [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) em seu diretório clonado.

1. Inicie o servidor de notebook do seu diretório clonado.

    ```shell
    jupyter notebook
    ```