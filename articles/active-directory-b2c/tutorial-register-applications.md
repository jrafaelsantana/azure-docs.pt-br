---
title: Tutorial-registrar um aplicativo-Azure Active Directory B2C
description: Saiba como registrar seus aplicativo Web no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06b2c273f41bfa74ee968b6fd6676e83767ce8b2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063264"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: Registrar um aplicativo no Azure Active Directory B2C

Antes que seus [aplicativos](active-directory-b2c-apps.md) possam interagir com Azure Active Directory B2C (Azure ad B2C), eles devem ser registrados em um locatário que você gerencia. Este tutorial mostra como registrar um aplicativo Web usando o portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário do Azure AD B2C](tutorial-create-tenant.md), crie um agora. É possível usar um locatário existente do Azure AD B2C.

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *webapp1*.
1. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
1. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, você pode defini-lo para escutar localmente `https://localhost:44316`em. Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente.

    Para fins de teste como este tutorial, você pode defini `https://jwt.ms` -lo como que exibe o conteúdo de um token para inspeção. Para este tutorial, defina a **URL** de resposta `https://jwt.ms`como.

    As seguintes restrições se aplicam a URLs de resposta:

    * A URL de resposta deve começar com o `https`esquema.
    * A URL de resposta diferencia maiúsculas de minúsculas. Seu caso deve corresponder ao caso do caminho da URL do seu aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos como diferenciando maiúsculas de `.../abc/response-oidc` minúsculas, os cookies associados a podem ser excluídos se forem redirecionados para a `.../ABC/response-oidc` URL incompatível com maiúsculas e minúsculas.

1. Clique em **criar** para concluir o registro do aplicativo.

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se seu aplicativo trocar um código para um token, você precisará criar um segredo do aplicativo.

1. Na página **Azure ad B2C-aplicativos** , selecione o aplicativo que você criou, por exemplo *webapp1*.
1. Selecione **chaves** e, em seguida, selecione **gerar chave**.
1. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Você usa esse valor como o segredo do aplicativo no código do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

Em seguida, saiba como criar fluxos de usuário para permitir que os usuários se inscrevam, entrem e gerenciem seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de usuário no Azure Active Directory B2C >](tutorial-create-user-flows.md)
