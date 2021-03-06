---
title: Criar um banco de dados individual – Banco de Dados SQL do Azure | Microsoft Docs
description: Crie e consulte um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure, o PowerShell e a CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: 831ebbd3f85ffa9b78ac3e97a6ec68a8c41bceb5
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845298"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Início Rápido: Criar um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure, o PowerShell e a CLI do Azure

A criação de um [banco de dados individual](sql-database-single-database.md) é a opção de implantação mais rápida e simples para criação de um Banco de Dados SQL do Azure. Este Início Rápido mostra como criar e, em seguida, consultar um banco de dados individual usando o portal do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/). 

Para todas as etapas deste início rápido, entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Criar um banco de dados individual

Um banco de dados individual pode ser criado na camada de computação (versão prévia) provisionada ou sem servidor.

- Um banco de dados individual na camada de computação provisionada tem uma quantidade pré-alocada de recursos de computação, incluindo a CPU e a memória, usando um de dois [modelos de compra](sql-database-purchase-models.md).
- Um banco de dados individual na camada de computação sem servidor tem uma variedade de recursos de computação, incluindo CPU e memória, que são dimensionados automaticamente e está disponível apenas nos [modelos de compra baseado em vCore](sql-database-service-tiers-vcore.md).

Quando você cria um banco de dados individual, você também define um [servidor do Banco de Dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo no [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) em uma região especificada.

> [!NOTE]
> Este início rápido usa o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), mas o [modelo de compra baseado em DTU](sql-database-service-tiers-DTU.md) também está disponível.

Para criar um banco de dados individual que contém os dados de exemplo AdventureWorksLT:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Consultar o banco de dados

Agora que você criou o banco de dados, use a ferramenta de consulta interna no portal do Azure para se conectar ao banco de dados e consultar os dados.

1. Na página **Banco de Dados SQL** do banco de dados, selecione **Editor de consulta (versão prévia)** no menu à esquerda.

   ![Entrar no Editor de consultas](./media/sql-database-get-started-portal/query-editor-login.png)

2. Insira suas informações de logon e selecione **OK**.
3. Insira a consulta a seguir no painel **Editor de consultas**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Selecione **Executar** e, em seguida, examine os resultados da consulta no painel **Resultados**.

   ![Resultados do Editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

5. Feche a página **Editor de consultas** e selecione **OK** quando solicitado para descartar as edições não salvas.

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha esse grupo de recursos, o servidor de banco de dados e o banco de dados individual caso deseje ir para as [Próximas etapas](#next-steps). As próximas etapas mostram como se conectar e consultar seu banco de dados usando diferentes métodos.

Quando terminar de usar esses recursos, você poderá excluí-los da seguinte maneira:

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, **myResourceGroup**.
2. Na página do grupo de recursos, selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* no campo e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Crie uma regra de firewall no nível do servidor para se conectar ao banco de dados individual por meio de ferramentas locais ou remotas. Para obter mais informações, consulte [Criar uma regra de firewall no nível do servidor](sql-database-server-level-firewall-rule.md).
- Depois de criar uma regra de firewall no nível do servidor, [conecte-se e consulte](sql-database-connect-query.md) seu banco de dados usando várias ferramentas e linguagens diferentes.
  - [Conectar e consultar usando o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Conectar e consultar usando o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar um banco de dados individual na camada de computação provisionada usando a CLI do Azure, confira [Exemplos de CLI do Azure](sql-database-cli-samples.md).
- Para criar um banco de dados individual na camada de computação provisionada usando o Azure PowerShell, confira [Exemplos de CLI do Azure](sql-database-powershell-samples.md).
- Para criar um banco de dados individual na camada de computação sem servidor usando o Azure Powershell, confira [Criar banco de dados sem servidor](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).
