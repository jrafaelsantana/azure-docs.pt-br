---
title: 'PowerShell: Habilitar TDE com BYOK – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs'
description: Saiba como configurar um Instância Gerenciada do SQL do Azure para começar a usar a TDE (Transparent Data Encryption) com BYOK para criptografia em repouso usando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569689"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Gerenciar Transparent Data Encryption em um Instância Gerenciada usando sua própria chave de Azure Key Vault (versão prévia)

Este exemplo de script do PowerShell configura Transparent Data Encryption (TDE) no cenário Bring Your Own Key (versão prévia) para Instância Gerenciada do SQL do Azure, usando uma chave de Azure Key Vault. Para saber mais sobre a TDE com suporte BYOK (Bring Your Own Key), visite [TDE com Bring Your Own Key para SQL do Azure](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um Instância Gerenciada existente. Consulte [usar o PowerShell para criar uma instância gerenciada do banco de dados SQL do Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Usar o PowerShell localmente ou usando Azure Cloud Shell requer AZ PowerShell 1.1.1-Preview ou uma versão de visualização posterior. Se você precisar atualizar, consulte [instalar Azure PowerShell módulo](/powershell/azure/install-az-ps)ou execute o script de exemplo abaixo para instalar o módulo.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
