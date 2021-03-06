---
title: Restrições de recursos do banco de dados SQL do Azure | Microsoft Docs
description: As restrições de recurso do banco de dados SQL do Azure aprimoram a segurança do banco de dados restringindo recursos em seu banco de dados que podem ser invasores para obter acesso a informações neles.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: f2fd6cb73428c69fbb27cb93377f851a4e06221d
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959126"
---
# <a name="azure-sql-database-feature-restrictions"></a>Restrições de recursos do banco de dados SQL do Azure

Uma fonte comum de ataques de SQL Server é por meio de aplicativos Web que acessam o banco de dados em que várias formas de ataques de injeção de SQL são usadas para obter informações sobre o banco de dados.  O ideal é que o código do aplicativo seja desenvolvido para não permitir a injeção de SQL.  No entanto, em grandes bases de código que incluem código herdado e externo, nunca é possível ter certeza de que todos os casos foram resolvidos, portanto, as injeções de SQL são um fato da vida que precisamos nos proteger.  A meta das restrições de recursos é impedir que algumas formas de injeção de SQL vazassem informações sobre o banco de dados, mesmo quando a injeção de SQL é bem-sucedida.

## <a name="enabling-feature-restrictions"></a>Habilitando restrições de recursos

A habilitação de restrições de recursos `sp_add_feature_restriction` é feita usando o procedimento armazenado da seguinte maneira:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Os seguintes recursos podem ser restritos:

| Recurso          | Descrição |
|------------------|-------------|
| N'ErrorMessages' | Quando restrito, todos os dados de usuário dentro da mensagem de erro serão mascarados. Ver a [restrição de recursos de mensagens de erro](#error-messages-feature-restriction) |
| N'Waitfor'       | Quando restrito, o comando retornará imediatamente sem nenhum atraso. Consulte a [restrição de recurso WAITFOR](#waitfor-feature-restriction) |

O valor de `object_class` pode `N'User'` ser ou `N'Role'` para indicar se `object_name` é um nome de usuário ou um nome de função no banco de dados.

O exemplo a seguir fará com que todas as mensagens `MyUser` de erro do usuário sejam mascaradas:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Desabilitando restrições de recursos

A desabilitação das restrições de recursos é `sp_drop_feature_restriction` feita usando o procedimento armazenado da seguinte maneira:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

O exemplo a seguir desabilita o mascaramento de mensagens de erro `MyUser`para o usuário:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Exibindo restrições de recursos

A `sys.sql_feature_restrictions` exibição apresenta todas as restrições de recurso definidas no momento no banco de dados. Ele tem as seguintes colunas:

| Nome da coluna | Tipo de dados | Descrição |
|-------------|-----------|-------------|
| classe       | nvarchar(128) | Classe de objeto ao qual a restrição se aplica |
| objeto      | nvarchar(256) | Nome do objeto ao qual a restrição se aplica |
| recurso     | nvarchar(128) | Recurso que é restrito |

## <a name="feature-restrictions"></a>Restrições de recursos

### <a name="error-messages-feature-restriction"></a>Restrição de recurso de mensagens de erro

Um método comum de ataque de injeção de SQL é injetar código que cause um erro.  Ao examinar a mensagem de erro, um invasor pode aprender informações sobre o sistema, permitindo outros ataques mais direcionados.  Esse ataque pode ser especialmente útil quando o aplicativo não exibe os resultados de uma consulta, mas exibe mensagens de erro.

Considere um aplicativo Web que tenha uma solicitação na forma de:

```html
http://www.contoso.com/employee.php?id=1
```

Que executa a seguinte consulta de banco de dados:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se o valor passado como o `id` parâmetro para a solicitação do aplicativo Web for copiado para substituir $empid na consulta de banco de dados, um invasor poderá fazer a seguinte solicitação:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

E o seguinte erro será retornado, permitindo que o invasor aprendesse o nome do banco de dados:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Depois de habilitar a restrição de recurso de mensagens de erro para o usuário do aplicativo no banco de dados, a mensagem de erro retornada é mascarada para que nenhuma informação interna sobre o banco de dados seja vazada:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Da mesma forma, o invasor pode fazer a seguinte solicitação:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

E o seguinte erro seria retornado, permitindo que o invasor aprendesse o salário do funcionário:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Usando a restrição de recursos de mensagens de erro, o banco de dados retornaria:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Restrição de recurso WAITFOR

Uma injeção de SQL cego é quando um aplicativo não fornece um invasor com os resultados do SQL injetado ou com uma mensagem de erro, mas o invasor pode inferir informações do banco de dados construindo uma consulta condicional na qual as duas ramificações condicionais Reserve um período de tempo diferente para executar. Comparando o tempo de resposta, o invasor pode saber qual ramificação foi executada e, portanto, aprender informações sobre o sistema. A variante mais simples desse ataque é usar a `WAITFOR` instrução para introduzir o atraso.

Considere um aplicativo Web que tenha uma solicitação na forma de:

```html
http://www.contoso.com/employee.php?id=1
```

que executa a seguinte consulta de banco de dados:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se o valor passado como o parâmetro de ID para as solicitações do aplicativo Web for copiado para substituir $EmpId na consulta de banco de dados, um invasor poderá fazer a seguinte solicitação:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

E a consulta levaria mais 5 segundos se a `sa` conta estivesse sendo usada. Se `WAITFOR` a restrição de recurso estiver desabilitada no banco `WAITFOR` de dados, a instrução será ignorada e não as informações serão vazadas usando esse ataque.