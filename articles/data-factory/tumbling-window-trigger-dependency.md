---
title: Criar dependências de gatilho de janela em cascata no Azure Data Factory | Microsoft Docs
description: Saiba como criar dependência em um gatilho de janela em cascata no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 6e5e293e9759f091b6537d5efab9884e0a20fabc
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725527"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de gatilho de janela em cascata

Este artigo fornece etapas para criar uma dependência em um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos de Janela em Cascata, consulte [Como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

Para criar uma cadeia de dependência e certificar-se de que um gatilho executará somente após a execução com êxito de outro gatilho no data factory, use esse recurso avançado para criar uma dependência de janela em cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na interface do usuário do Data Factory

Para criar dependência em um gatilho, selecione **Gatilho > Avançado > Novo** e, em seguida, escolha o gatilho para depender do deslocamento e tamanho apropriados. Selecione **Concluir** e publique as alterações do data factory para que as dependências entrem em vigor.

![Criação de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Criação de dependência")

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janela em cascata

Um gatilho de janela em cascata com uma dependência tem as seguintes propriedades:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

A tabela a seguir fornece a lista de atributos necessários para definir uma dependência de Janela em Cascata.

| **Nome da propriedade** | **Descrição**  | **Tipo** | **Necessária** |
|---|---|---|---|
| type  | Todos os gatilhos da janela em cascata existentes são exibidos neste menu suspenso. Escolha o gatilho para assumir a dependência.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerReference | Sim |
| offset | Deslocamento do gatilho de dependência. Forneça um valor no formato de intervalo de tempo e deslocamentos negativos e positivos são permitidos. Essa propriedade será obrigatória se o gatilho estiver dependendo de si mesmo e em todos os outros casos for opcional. A autodependência deverá sempre ser um deslocamento negativo. Se nenhum valor for especificado, a janela será igual ao próprio gatilho. | Intervalo de tempo<br/>(hh:mm:ss) | Autodependência: Sim<br/>Outros: Não |
| size | Tamanho da janela em cascata de dependência. Forneça um valor de TimeSpan positivo. Essa propriedade é opcional. | Intervalo de tempo<br/>(hh:mm:ss) | Não  |

> [!NOTE]
> Um gatilho de janela em cascata pode depender de um máximo de dois outros gatilhos.

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de autodependência de janela em cascata

Em cenários em que o gatilho não deve prosseguir para a próxima janela até que a janela anterior seja concluída com êxito, crie uma autodependência. Um gatilho de dependência automática dependente do sucesso das execuções anteriores de si mesmo no RH anterior terá as propriedades abaixo:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Cenários e exemplos de uso

Veja abaixo as ilustrações de cenários e o uso de propriedades de dependência de janela em cascata.

### <a name="dependency-offset"></a>Deslocamento de dependência

![Exemplo de deslocamento](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Exemplo de deslocamento")

### <a name="dependency-size"></a>Tamanho da dependência

![Exemplo de tamanho](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Exemplo de tamanho")

### <a name="self-dependency"></a>Autodependência

![Dependência automática](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Dependência automática")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho de janela em cascata

Um trabalho de processamento diário de telemetria dependendo de outro trabalho diário agregando a saída dos últimos sete dias e gera fluxos de janela sem interrupção de sete dias:

![Exemplo de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Exemplo de dependência")

### <a name="dependency-on-itself"></a>Dependência de si próprio

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![Exemplo de dependência automática](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Exemplo de dependência automática")

## <a name="monitor-dependencies"></a>Monitorar dependências

Você pode monitorar a cadeia de dependência e as janelas correspondentes na página monitoramento de execução de gatilho. Navegue até  **Monitoramento > Execuções de Gatilho**.

![Monitorar execuções de gatilho](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorar execuções de gatilho")

Clique no ícone de ação para exibir todas as execuções de gatilho dependentes da janela selecionada.

![Monitorar dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorar dependências")

No exemplo acima, um gatilho diário depende de um gatilho por hora sem nenhuma janela definida e um deslocamento de 3 horas. Como resultado, o gatilho é executado após 24 execuções bem-sucedidas da dependência.

## <a name="next-steps"></a>Próximas etapas

* Examine [como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md)
