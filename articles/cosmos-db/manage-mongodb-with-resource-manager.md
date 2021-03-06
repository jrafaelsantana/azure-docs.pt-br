---
title: Modelos de Azure Resource Manager para Azure Cosmos DB API para MongoDB
description: Use modelos de Azure Resource Manager para criar e configurar a API de Azure Cosmos DB para MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 683da62ad78cde1a4f72b2ac0554e90d78b3ac6e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815019"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gerenciar Azure Cosmos DB recursos de API do MongoDB usando modelos de Azure Resource Manager

## Criar Azure Cosmos DB API para a conta, banco de dados e coleção do MongoDB<a id="create-resource"></a>

Crie Azure Cosmos DB recursos usando um modelo de Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para a API do MongoDB com duas coleções que compartilham a taxa de transferência de 400 RU/s no nível do banco de dados. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o `--template-file` parâmetro.

> [!NOTE]
> Os nomes de conta devem ter letras minúsculas e < 31 caracteres.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Implantar via CLI do Azure

Para implantar o modelo do Resource Manager usando CLI do Azure, **Copie** o script e selecione **Experimente** para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois que ela é provisionada. Se você optar por usar uma versão instalada localmente do CLI do Azure em vez de usar o CloudShell, consulte o artigo [CLI (interface de linha de comando) do Azure](/cli/azure/) .

## Taxa de transferência de atualização (RU/s) em um banco de dados<a id="database-ru-update"></a>

O modelo a seguir atualizará a taxa de transferência de um banco de dados. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o `--template-file` parâmetro.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Implantar modelo de banco de dados via CLI do Azure

Para implantar o modelo do Resource Manager usando CLI do Azure, selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Atualizar taxa de transferência (RU/s) em uma coleção<a id="collection-ru-update"></a>

O modelo a seguir atualizará a taxa de transferência de uma coleção. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o `--template-file` parâmetro.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Implantar o modelo de coleção via CLI do Azure

Para implantar o modelo do Resource Manager usando CLI do Azure, selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)