---
title: CLI do Azure – Dimensionar um servidor de Banco de Dados do Azure para MySQL
description: Este exemplo de script da CLI dimensiona um Banco de Dados do Azure para o servidor MySQL para um nível de desempenho diferente após consultar a métrica.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/07/2019
ms.openlocfilehash: f54b3f6fa8bb37f57479d6a9e7bc05340e411a48
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882935"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Monitorar e dimensionar um Banco de Dados do Azure para servidor MySQL usando a CLI do Azure
Este exemplo de script da CLI dimensiona a computação e o armazenamento para um único servidor de Banco de Dados do Azure para MySQL após consultar a métrica. A computação pode aumentar ou reduzir. O armazenamento só pode aumentar.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Atualize o script com sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Cria um servidor MySQL que hospeda os bancos de dados. |
| [az mysql server update](/cli/azure/mysql/server#az-mysql-server-update) | Atualiza as propriedades do servidor MySQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Lista o valor de métrica para os recursos. |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [computação e armazenamento do Banco de Dados do Azure para MySQL](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para MySQL](../sample-scripts-azure-cli.md)
- Saiba mais sobre a [CLI do Azure](/cli/azure)