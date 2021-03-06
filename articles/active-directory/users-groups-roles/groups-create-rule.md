---
title: Crie um grupo dinâmico e verifique o status - Azure Active Directory | Microsoft Docs
description: Como criar uma regra de associação de grupo no portal do Azure, verifique o status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343acce228c38e38152fc2ea9d8fe0a59d8254d4
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193937"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Criar um grupo dinâmico e verificar o status

No Azure Active Directory (AD do Azure), você pode usar regras para determinar a associação de grupo com base nas propriedades do usuário ou do dispositivo. Este artigo informa como configurar uma regra para um grupo dinâmico no portal do Azure.
A associação dinâmica tem suporte para grupos de segurança ou grupos do Office 365. Quando uma regra de associação de grupo é aplicada, os atributos de usuário e de dispositivo são avaliados para correspondências com a regra de associação. Quando um atributo é alterado para um usuário ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Os usuários e dispositivos serão adicionados ou removidos se atenderem às condições de um grupo. Os grupos de segurança podem ser usados para dispositivos ou usuários, mas os grupos do Office 365 podem ser apenas grupos de usuários.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal do Azure

O Azure AD fornece um construtor de regras para criar e atualizar suas regras importantes mais rapidamente. O construtor de regras dá suporte à construção de até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, ela não pode ser usada para reproduzir todas as regras. Se o construtor de regras não oferecer suporte à regra que você deseja criar, você poderá usar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que você construa usando a caixa de texto:

- Regra com mais de cinco expressões
- A regra de relatórios diretos
- Definindo a precedência de [operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas](groups-dynamic-membership.md#rules-with-complex-expressions); por exemplo`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Você poderá ver uma mensagem quando o construtor de regras não puder exibir a regra. O construtor de regras não altera a sintaxe com suporte, a validação nem o processamento de regras de grupo dinâmicas de forma alguma.

![Adicionar regra de associação a um grupo dinâmico](./media/groups-update-rule/update-dynamic-group-rule.png)

Para obter exemplos de sintaxe, propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que esteja na função administrador global, administrador do Intune ou administrador de usuários no locatário.
1. Selecione **Grupos**.
1. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Selecione o comando para adicionar um novo grupo](./media/groups-create-rule/new-group-creation.png)

1. Na página **grupo** , insira um nome e uma descrição para o novo grupo. Selecione um **tipo de associação** para usuários ou dispositivos e, em seguida, selecione **Adicionar consulta dinâmica**. O construtor de regras dá suporte a até cinco expressões. Para adicionar mais de cinco expressões, você deve usar a caixa de texto.

   ![Adicionar regra de associação a um grupo dinâmico](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para sua consulta de associação:
   1. Selecionar **obter propriedades de extensão personalizadas**
   1. Insira a ID do aplicativo e, em seguida, selecione **Atualizar Propriedades**.
1. Depois de criar a regra, selecione **salvar**.
1. Selecione **criar** na página **novo grupo** para criar o grupo.

Se a regra que você inseriu não for válida, uma explicação do motivo pelo qual a regra não pôde ser processada será exibida em uma notificação do Azure no Portal. Leia com atenção para entender como corrigir a regra.

## <a name="turn-on-or-off-welcome-email"></a>Ativar ou desativar email de boas-vindas

Quando um novo grupo do Office 365 é criado, uma notificação por email de boas-vindas é enviada aos usuários que são adicionados ao grupo. Posteriormente, se qualquer atributo de um usuário ou dispositivo for alterado, todas as regras dinâmicas do grupo na organização serão processadas para alterações de associação. Os usuários que são adicionados também recebem a notificação de boas-vindas. Você pode desativar esse comportamento no [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Verificar o status de processamento de uma regra

Veja o status do processamento de associação e a data da última atualização na página **Visão Geral** do grupo.
  
  ![exibição do status do grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de status podem ser mostradas para o status **Processamento de associação**:

- **Avaliando**:  a alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
- **Processando**: as atualizações estão sendo processadas.
- **Atualização concluída**: o processamento foi concluído e todas as atualizações aplicáveis foram feitas.
- **Erro de processamento**:  O processamento não pôde ser concluído devido a um erro ao avaliar a regra de associação.
- **Atualização em pausa**: as atualizações dinâmicas da regra de associação foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Em pausa".

As seguintes mensagens de status podem ser mostradas para o status da **Última atualização da associação**:

- &lt;**Data e hora**&gt;: a última vez em que a associação foi atualizada.
- **Em andamento**: as atualizações estão em andamento no momento.
- **Desconhecido**: A hora da última atualização não pode ser recuperada. O grupo pode ser novo.

Se ocorrer um erro ao processar a regra de associação de um grupo específico, um alerta será mostrado na parte superior da **página Visão Geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada de nenhum dos grupos do locatário por mais de 24 horas, um alerta será mostrado na parte superior da **Todos os grupos**.

![Processando alertas de mensagens de erro](./media/groups-create-rule/processing-error.png)

Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

- [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
