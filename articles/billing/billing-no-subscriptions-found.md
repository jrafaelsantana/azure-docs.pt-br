---
title: Erro Nenhuma assinatura encontrada - Entrada no portal do Azure | Microsoft Docs
description: Fornece a solução para um problema no qual o erro Nenhuma assinatura encontrada ocorre ao entrar no Portal do Azure ou no centro de contas do Azure.
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 19a30a99b9df23358aafd88c55bde08c73a3be72
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121536"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Erro de entrada Nenhuma assinatura encontrada no portal do Azure ou no centro de contas do Azure

Você poderá receber uma mensagem de erro "Nenhuma assinatura encontrada" ao tentar entrar no [Portal do Azure](https://portal.azure.com/) ou no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions). Este artigo fornece uma solução para esse problema.

## <a name="symptom"></a>Sintoma

Ao tentar entrar no [portal do Azure](https://portal.azure.com/) ou no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions), a seguinte mensagem de erro é exibida: "Nenhuma assinatura encontrada".

## <a name="cause"></a>Causa

Esse problema ocorrerá se tiver selecionado no diretório errado ou se sua conta não tiver permissões suficientes. 

## <a name="solution"></a>Solução

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Cenário 1: A mensagem de erro é recebida no [portal do Azure](https://portal.azure.com)

Para corrigir esse problema:

* Verifique se o diretório correto do Azure está selecionado clicando em sua conta no canto superior direito.

  ![Selecione o diretório na parte superior direita do portal do Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Se o diretório correto do Azure estiver selecionado, mas você continuar recebendo a mensagem de erro, [atribua a função Proprietário à sua conta](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Cenário 2: A mensagem de erro é recebida no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta usada é o Administrador da Conta. Para verificar quem é o Administrador da Conta, siga estas etapas:

1. Entre em [Modo de exibição Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
1. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .  

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
