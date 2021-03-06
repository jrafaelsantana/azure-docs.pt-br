---
title: CLI do Azure Service Fabric – implantação da malha do sfctl | Microsoft Docs
description: Descreve os comandos de implantação da malha do sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b3f506b46ef563f47fc7c67b759d3fcd08b7509d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035182"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Criar recursos da Malha do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| criar | Cria uma implantação de Recursos da Malha do Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Cria uma implantação de Recursos da Malha do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --input-yaml-files [Obrigatório] | Caminhos de arquivo relativos/absolutos separados por vírgula de todos os arquivos YAML ou o caminho relativo/absoluto do diretório (recursivo) que contêm arquivos YAML. |
| --parameters | Um caminho relativo/absoluto para o arquivo YAML ou um objeto JSON que contém os parâmetros que precisam ser substituídos. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Consolida e implanta todos os recursos no cluster substituindo os parâmetros mencionados no arquivo YAML

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Consolida e implanta todos os recursos em um diretório no cluster substituindo os parâmetros mencionados no arquivo YAML

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e implanta todos os recursos em um diretório no cluster substituindo os parâmetros, que são passados diretamente como um objeto JSON

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).