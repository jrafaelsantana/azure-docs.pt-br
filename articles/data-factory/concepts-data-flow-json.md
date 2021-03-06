---
title: Conceitos de JSON de fluxo de dados de mapeamento de Azure Data Factory
description: O fluxo de dados de mapeamento de Data Factory tem recursos internos para lidar com documentos JSON com hierarquias
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 37db3e153e8dfcbc1120fcb1f6d2f77187edc78e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029657"
---
# <a name="mapping-data-flow-json-handling"></a>Mapeando manipulação de JSON de fluxo de dados



## <a name="creating-json-structures-in-expression-editor"></a>Criando estruturas JSON no editor de expressão
### <a name="derived-column-transformation"></a>Transformação coluna derivada
Adicionar uma coluna complexa ao fluxo de dados é mais fácil por meio do editor de expressão de coluna derivada. Depois de adicionar uma nova coluna e abrir o editor, há duas opções: Insira a estrutura JSON manualmente ou use a interface do usuário para adicionar Subcolunas de forma interativa.

#### <a name="interactive-ui-json-design"></a>Design JSON de interface do usuário interativa
No painel do lado do esquema de saída, novas Subcolunas podem ser adicionadas usando o menu `+`: ![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

A partir daí, novas colunas e Subcolunas podem ser adicionadas da mesma maneira. Para cada campo não complexo, uma expressão pode ser adicionada no editor de expressão à direita.

(media/data-flow/complexcolumn.png "Coluna complexa") de ![coluna complexa]

#### <a name="manual-json-design"></a>Design de JSON manual
Para adicionar manualmente uma estrutura JSON, adicione uma nova coluna e insira a expressão no editor. A expressão segue o seguinte formato geral:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Se essa expressão foi inserida para uma coluna chamada "complexColumn", ela seria gravada no coletor como o JSON a seguir:
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Exemplo de script manual para a definição hierárquica completa
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Opções de formato de origem
### <a name="default"></a>Padrão
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Documento único
* Opção um
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Opção dois
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Nomes de colunas sem aspas
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Tem comentários
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Entre aspas simples
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Barra invertida
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funções de ordem superior
## <a name="filter"></a>filter
Filtra elementos da matriz que não atendem ao predicado fornecido. O filtro espera uma referência a um elemento na função de predicado como #item.

### <a name="examples"></a>Exemplos
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
Mapeia cada elemento da matriz para um novo elemento usando a expressão fornecida. O mapa espera uma referência a um elemento na função de expressão como #item.

### <a name="examples"></a>Exemplos
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>diminu
Acumula elementos em uma matriz. A redução espera uma referência a um acumulador e um elemento na primeira função de expressão como #acc e #item e espera que o valor resultante como #result seja usado na segunda função de expressão.

### <a name="examples"></a>Exemplos
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
Classifica a matriz usando a função de predicado fornecida. Sort espera uma referência a dois elementos consecutivos na função Expression como #item1 e #item2.

### <a name="examples"></a>Exemplos
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contains
Retornará true se qualquer elemento na matriz fornecida for avaliado como true no predicado fornecido. Contains espera uma referência a um elemento na função de predicado como #item.

### <a name="examples"></a>Exemplos
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Próximas etapas

* [Use a transformação coluna derivada para criar suas estruturas hierárquicas](data-flow-derived-column.md)
