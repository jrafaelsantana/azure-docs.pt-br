---
title: Adicionar ou alterar administradores de assinatura do Azure
description: Descreve como adicionar ou alterar o administrador da assinatura do Azure usando o RBAC (Controle de Acesso Baseado em Função).
author: genlin
manager: dcscontentpm
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: banders
ms.openlocfilehash: 2054fbb7d0a9f450ad487fc0f03d0af920c6cc4b
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260916"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Adicionar ou alterar administradores de assinatura do Azure


Para gerenciar o acesso aos recursos do Azure, você deve ter a função de administrador apropriada. O Azure tem um sistema de autorização chamado de [RBAC](../role-based-access-control/overview.md) (Controle de Acesso Baseado em Função) com várias funções internas dentre as quais você pode escolher. Você pode atribuir essas funções em escopos diferentes, como o grupo de gerenciamento, a assinatura ou o grupo de recursos. Por padrão, a pessoa que cria uma assinatura do Azure pode atribuir a outros usuários acesso administrativo a uma assinatura.

Este artigo descreve como adicionar ou alterar a função de administrador para um usuário usando RBAC no escopo da assinatura.

A Microsoft recomenda que você gerencie o acesso aos recursos usando o RBAC. No entanto, se você está usando o modelo de implantação clássico e gerenciando os recursos clássicos com o [Módulo do PowerShell para Gerenciamento de Serviços do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure), precisa usar um administrador clássico.

> [!TIP]
> Se você usar apenas o portal do Azure para gerenciar os recursos clássicos, não precisará usar o administrador clássico.

Para obter mais informações, confira [Azure Resource Manager versus implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md) e [Administradores da assinatura clássica do Azure](../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Atribuir um administrador de assinatura

Para tornar um usuário administrador de uma assinatura do Azure, um administrador existente atribui a eles a função [Proprietário](../role-based-access-control/built-in-roles.md#owner) (uma função RBAC) no escopo da assinatura. A função Proprietário permite ao usuário acesso completo a todos os recursos na assinatura, inclusive o direito de delegar acesso a outras pessoas. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

Se você não tiver certeza de quem é o administrador da conta de uma assinatura, use as etapas a seguir para descobrir.

1. Abra a [página Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
1. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .

### <a name="to-assign-a-user-as-an-administrator"></a>Para atribuir um usuário como um administrador

1. Entre no portal do Azure como o proprietário da assinatura e abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Clique na assinatura em que você deseja conceder acesso.

1. Clique em **controle de acesso (IAM)** .

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

    ![Captura de tela que mostra atribuições de função](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função**.

    Se você não tiver permissões para atribuir funções, a opção será desabilitada.

1. Na lista suspensa **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um usuário. Se o usuário não estiver na lista, digite na caixa **Selecionar** para pesquisar no diretório os nomes de exibição e os endereços de email.

    ![Captura de tela que mostra a função Proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Clique em **Salvar** para atribuir a função.

    Após alguns instantes, o usuário é atribuído à função Proprietário no escopo da assinatura.

## <a name="next-steps"></a>Próximas etapas

* [O que é o RBAC (controle de acesso baseado em função)?](../role-based-access-control/overview.md)
* [Entender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Como: Associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Permissões da função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
