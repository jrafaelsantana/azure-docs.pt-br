---
title: 'Script do PowerShell: Copiar dados na nuvem usando o Azure Data Factory | Microsoft Docs'
description: Este script do PowerShell copia dados de um local em um Armazenamento de Blobs do Azure para outro local no mesmo Armazenamento de Blobs.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: jingwang
ms.openlocfilehash: 19cb37ea40daa1e416f0dea82b2ddc8ad107454c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167449"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Usar o PowerShell para criar um pipeline do data factory para copiar dados na nuvem

Este exemplo de script do PowerShell cria um pipeline no Azure Data Factory que copia dados de um local para outro em um Armazenamento de Blobs do Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como ambos o armazenamento de dados de **origem** e o de **coletor**. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) sobre como criar uma. 
* Crie um **contêiner de blob** no Armazenamento de Blobs, crie uma **pasta** de entrada no contêiner e carregue alguns arquivos na pasta. Você pode usar ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para se conectar ao armazenamento de Blobs do Azure, criar um contêiner de blobs, carregar arquivos de entrada e verificar os arquivos de saída.

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem as entidades do Data Factory (serviço vinculado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Limpar a implantação

Depois de executar o exemplo de script, use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover o data factory do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar um data factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Cria um conjunto de dados no data factory. Um conjunto de dados representa a entrada/saída para uma atividade em um pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, a atividade de cópia copia dados de um local para outro no Armazenamento de Blobs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução da atividade) no pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de scripts do Azure Data Factory PowerShell podem ser encontrados nos [Exemplos do Azure Data Factory PowerShell](../samples-powershell.md).
