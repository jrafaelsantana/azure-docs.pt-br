---
title: Entidades predefinidas do DatetimeV2 – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade predefinida de datetimeV2 em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4f46efaeddb0bfe789ef752abdd133c14da514da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677701"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entidade DatetimeV2 predefinida para um aplicativo LUIS

A entidade predefinida **datetimeV2** extrai os valores de data e hora. Esses valores são resolvidos em um formato padronizado para programas do cliente consumirem. Quando um enunciado tem uma data ou hora que não está concluído, o LUIS inclui _valores passados e futuros_ na resposta do ponto de extremidade. Como essa entidade já está treinada, não é necessário adicionar enunciados contendo datetimeV2 às intenções do aplicativo. 

## <a name="types-of-datetimev2"></a>Tipos de datetimeV2
DatetimeV2 é gerenciado por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>JSON de exemplo 
A resposta JSON de exemplo a seguir tem uma entidade `datetimeV2` com um subtipo de `datetime`. Para obter exemplos de outros tipos de entidades de datetimeV2, veja [Subtipos datetimeV2](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Descrições de propriedade JSON

|Nome da propriedade |Tipo e descrição da propriedade|
|---|---|
|Entidade|**cadeia de caracteres** – texto extraído do enunciado com tipo de data, hora, intervalo de datas ou intervalo de tempo.|
|type|**cadeia de caracteres** – um do [subtipos de datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** – o índice no enunciado em que a entidade começa.|
|endIndex|**int** – o índice no enunciado em que a entidade termina.|
|resolução|Tem uma matriz `values` com um, dois ou quatro [valores de resolução](#values-of-resolution).|
|encerrar|O valor final de uma hora ou intervalo de datas no mesmo formato que `value`. Usado somente se `type` for `daterange`, `timerange` ou `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Subtipos de datetimeV2

A entidade predefinida **datetimeV2** tem os seguintes subtipos, e exemplos de cada um são fornecidos na tabela a seguir:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Valores de resolução
* A matriz terá um elemento se a data ou hora no enunciado for totalmente especificado e não ambígua.
* A matriz terá dois elementos se o valor de datetimeV2 for ambíguo. Ambiguidade inclui falta de um ano, hora ou intervalo de tempo específico. Veja [datas ambíguas](#ambiguous-dates) para obter exemplos. Quando a hora é ambígua para A.M. ou P.M., ambos os valores são incluídos.
* A matriz terá quatro elementos se o enunciado tiver dois elementos com ambiguidade. Essa ambiguidade inclui elementos que têm:
  * Uma data ou um intervalo de datas ambíguo quanto ao ano
  * Um horário ou intervalo de tempo ambíguo como A.M. ou P.M. Por exemplo, 3h de 3 de abril.

Cada elemento da matriz `values` pode ter os seguintes campos: 

|Nome da propriedade|Descrição da propriedade|
|--|--|
|timex|hora, data ou intervalo de datas expressado no formato TIMEX que segue o [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e os atributos TIMEX3 para anotação usando a linguagem TimeML. Essa anotação é descrita nas [diretrizes TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|O subtipo, que pode ser um dos seguintes itens: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|value|**Opcional** Um objeto datetime no formato yyyy:MM:dd (date), HH:mm:ss (time) yyyy:MM:dd HH:mm:ss (datetime). Se `type` for `duration`, o valor será o número de segundos (duration) <br/> Usado somente se `type` for `datetime` ou `date`, `time` ou `duration.|

## <a name="valid-date-values"></a>Valores de data válidos

O **datetimeV2** é compatível com datas entre os seguintes intervalos:

| Min. | Máx |
|----------|-------------|
| 1º de janeiro de 1900   | 31 de dezembro de 2099 |

## <a name="ambiguous-dates"></a>Datas ambíguas

Se a data puder estar no passado ou no futuro, o LUIS fornecerá ambos os valores. Um exemplo é um enunciado que inclui o mês e o dia sem o ano.  

Por exemplo, dado enunciado "2 de maio":
* Se a data de hoje for 3 de maio de 2017, o LUIS fornecerá tanto "2017-05-02" quanto "2018-05-02" como valores. 
* Quando a data de hoje for 1º de maio de 2017, o LUIS fornecerá tanto "2016-05-02" quanto "2017-05-02" como valores.

O exemplo a seguir mostra a resolução da entidade "may 2nd". Essa resolução pressupõe que a data de hoje é uma data entre 2 de maio de 2017 e 1º de maio de 2018.
Campos com `X` no campo `timex` fazem parte da data que não está explicitamente especificada no enunciado.

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Exemplos de resolução de intervalo de datas para data numérica

A entidade `datetimeV2` extrai os intervalos de data e hora. Os campos `start` e `end` especificam o início e o fim do intervalo. Para o enunciado "2 de maio a 5 de maio", o LUIS fornece valores de **daterange** para o ano atual e o próximo ano. No campo `timex`, os valores `XXXX` indicam a ambiguidade do ano. `P3D` indica que o período é de três dias.

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Exemplos de resolução de intervalo de data para o dia da semana

O exemplo a seguir mostra como o LUIS usa **datetimeV2** para resolver o enunciado "Terça-feira a quinta-feira". Neste exemplo, a data atual é 19 de junho. O LUIS inclui valores de **daterange** para ambos os intervalos de datas que precedem e seguem a data atual.

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Horário ambíguo
A matriz de valores terá dois elementos de tempo se o tempo ou o intervalo de tempo for ambíguo. Quando há um tempo ambíguo, os valores têm ambos A.M. e P.M. como horários.

## <a name="time-range-resolution-example"></a>Exemplo de resolução de intervalo de tempo

O exemplo a seguir mostra como o LUIS usa **datetimeV2** para resolver o enunciado que tem um intervalo de tempo.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão v2](#tab/V2)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão v3](#tab/V3)

A resposta JSON DatetimeV2 foi alterada na API v3. 

Alterações da API v2:
* a propriedade `datetimeV2.timex.type` não é mais retornada porque é retornada no nível pai, `datetimev2.type`. 
* A propriedade `datetimeV2.timex` foi renomeada para `datetimeV2.value`.

Para o expressão, `8am on may 2nd 2017`, a versão v3 de DatetimeV2 é:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

O JSON a seguir é com o parâmetro `verbose` definido como `false`:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```


* * * 

## <a name="deprecated-prebuilt-datetime"></a>Datetime predefinido preterido

A entidade pré-compilada `datetime` é reprovada e substituída por **datetimeV2**. 

Para substituir `datetime` por `datetimeV2` em seu aplicativo LUIS, conclua as seguintes etapas:

1. Abra o painel **Entidades** da interface Web do LUIS. 
2. Exclua a entidade predefinida **datetime**.
3. Clique em **Adicionar entidade predefinida**
4. Selecione **datetimeV2** e clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [dimensão](luis-reference-prebuilt-dimension.md), [email](luis-reference-prebuilt-email.md) e [número](luis-reference-prebuilt-number.md). 

