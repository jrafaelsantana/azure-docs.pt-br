---
title: Configurar a inscrição e a entrada com uma conta da Amazon-Azure Active Directory B2C
description: Forneça a inscrição e entrada aos consumidores com contas da Amazon em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bddbd9b588d8ffc2ace3a937fa2c72876eb5e4e9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065221"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com a conta da Amazon usando o Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Criar um aplicativo da Amazon

Para usar uma conta da Amazon como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do Amazon, poderá se inscrever em [https://www.amazon.com/](https://www.amazon.com/).

1. Entre no [Centro de Desenvolvedores da Amazon](https://login.amazon.com/) com suas credenciais de conta da Amazon.
1. Se você ainda não tiver feito isso, clique em **Inscrever-se**, siga as etapas de registro do desenvolvedor e aceite a política.
1. Selecione **Registrar novo aplicativo**.
1. Insira o **Nome**, a **Descrição** e a **URL do Aviso de Privacidade** e clique em **Salvar**. O aviso de privacidade é uma página que você gerencia e que fornece informações de privacidade aos usuários.
1. Na seção **Configurações da Web**, copie os valores de **ID do cliente**. Selecione **Mostrar segredo** para obter o segredo do cliente e copie-o. Você precisará de ambos para configurar uma conta da Amazon como um provedor de identidade em seu locatário. **Segredo do Cliente** é uma credencial de segurança importante.
1. Na seção **Configurações da Web**, selecione **Editar** e insira `https://your-tenant-name.b2clogin.com` em **JavaScript Origins permitido** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de retorno permitidas**. Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Salvar**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurar uma conta da Amazon como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Amazon**.
1. Insira um **Nome**. Por exemplo, *Amazon*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo Amazon que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Clique em **Salvar**.
