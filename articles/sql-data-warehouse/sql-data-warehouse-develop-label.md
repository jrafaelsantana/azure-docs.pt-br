---
title: Usar rótulos para consultas de instrumento no SQL Data Warehouse | Microsoft Docs
description: Dicas para usar rótulos para consultas de instrumento no SQL Data Warehouse do Azure para desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ee991fdfcd93ea064d1205d61d07adf377cce667
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480032"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Usar rótulos para consultas de instrumento no SQL Data Warehouse do Azure
Dicas para usar rótulos para consultas de instrumento no SQL Data Warehouse do Azure para desenvolvimento de soluções.


## <a name="what-are-labels"></a>O que são rótulos?
O SQL Data Warehouse oferece suporte a um conceito chamado rótulos de consulta. Antes de entrar em qualquer profundidade, vamos examinar um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a cadeia de caracteres ‘Meu Rótulo de Consulta’ à consulta. Essa marca é particularmente útil, pois o rótulo é capaz de executar consultas por meio de DMVs. Consultar rótulos fornece um mecanismo para localizar as consultas de problema e ajudar a identificar o andamento por meio de uma execução de ELT.

Uma boa convenção de nomenclatura ajuda muito. Por exemplo, começar o rótulo com PROJECT, PROCEDURE, STATEMENT ou COMMENT ajuda a identificar exclusivamente a consulta entre todos os códigos no controle de origem.

A consulta a seguir usa um modo de exibição de gerenciamento dinâmico para pesquisar por rótulo.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial colocar colchetes ou aspas duplas em torno da palavra do rótulo ao consultar. Rótulo é uma palavra reservada e causa um erro quando ele não é delimitado. 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).


