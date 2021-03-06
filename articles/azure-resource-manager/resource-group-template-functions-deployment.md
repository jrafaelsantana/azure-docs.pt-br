---
title: Funções de modelo do Azure Resource Manager – implantação | Microsoft Docs
description: Descreve as funções a serem usadas em um modelo do Resource Manager para recuperar informações sobre implantação.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 12698d1655c414b1ee3b9866cc975dc53e4ef095
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983984"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funções de implantação para modelos do Azure Resource Manager 

O Gerenciador de Recursos fornece as seguintes funções para obter os valores de seções do modelo e os valores relacionados à implantação:

* [implantação](#deployment)
* [parâmetros](#parameters)
* [variáveis](#variables)

Para obter valores de recursos, de grupos de recursos ou de assinaturas, veja [Funções de recurso](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>deployment
`deployment()`

Retorna informações sobre a operação de implantação atual.

### <a name="return-value"></a>Valor retornado

Essa função retorna o objeto que é passado durante a implantação. As propriedades no objeto retornado vão variar dependendo se o objeto de implantação for transmitido como um link ou como um objeto na linha. Quando o objeto de implantação é passado na linha, como ao usar o parâmetro **-TemplateFile** no Azure PowerShell para apontar para um arquivo local, o objeto retornado tem no seguinte formato:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando o objeto é transmitido como um link, como ao usar o parâmetro **- TemplateUri** para apontar para um objeto remoto, o objeto é retornado no seguinte formato: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Ao [implantar em uma assinatura do Azure](deploy-to-subscription.md), em vez de um grupo de recursos, o objeto retornado inclui uma propriedade `location`. A propriedade de localização é incluída ao implantar um modelo local ou externo.

### <a name="remarks"></a>Comentários

Você pode usar a implantação() para vincular a outro modelo com base no URI do modelo pai.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Se você reimplantar um modelo usando o histórico de implantação no portal, o modelo será implantando como um arquivo local. A propriedade `templateLink` não é retornada na função de implantação. Se o seu modelo depender de `templateLink` para criar um link para outro modelo, não use o portal para reimplantação. Ao invés disso, emita os comandos usados para implantar originalmente o modelo.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a seguir retorna o objeto de implantação:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

O exemplo anterior retorna o seguinte objeto:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Para um modelo no nível da assinatura, que usa a função de implantação, confira [função de implantação de assinatura](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Ele é implantado com os comandos `az deployment create` ou `New-AzDeployment`.

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Retorna um valor de parâmetro. O nome do parâmetro especificado deve ser definido na seção de parâmetros do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro a retornar. |

### <a name="return-value"></a>Valor retornado

O valor do parâmetro especificado.

### <a name="remarks"></a>Comentários

Normalmente, você usa parâmetros para definir valores de recursos. O exemplo a seguir define o nome do site para o valor do parâmetro passado durante a implantação.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) a seguir mostra um uso simplificado da função parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | opção 1 |
| intOutput | int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | opção 1 |

Para obter mais informações sobre como usar parâmetros, consulte [parâmetros no modelo Azure Resource Manager](template-parameters.md).

<a id="variables" />

## <a name="variables"></a>variáveis
`variables(variableName)`

Retorna o valor da variável. O nome do parâmetro especificado deve ser definido na seção variáveis do modelo.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| variableName |Sim |String |O nome da variável a retornar. |

### <a name="return-value"></a>Valor retornado

O valor da variável especificada.

### <a name="remarks"></a>Comentários

Normalmente, você usa variáveis para simplificar seu modelo criando valores complexos apenas uma vez. O exemplo a seguir constrói um nome exclusivo para uma conta de armazenamento.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) a seguir retorna valores de variáveis diferentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Para obter mais informações sobre como usar variáveis, consulte [variáveis no modelo Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar diversos modelos, confira [Usar modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, consulte [Implantar um aplicativo com o Modelo do Azure Resource Manager](resource-group-template-deploy.md).

