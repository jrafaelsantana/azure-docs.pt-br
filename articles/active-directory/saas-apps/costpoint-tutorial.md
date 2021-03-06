---
title: 'Tutorial: Integração do Azure Active Directory ao Costpoint | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879578"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar o Costpoint com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Costpoint ao Azure AD (Azure AD). Quando você integra o Costpoint com o Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Costpoint.
* Permitir que seus usuários se conectem automaticamente ao Costpoint usando as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Costpoint.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Costpoint dá suporte ao SSO iniciado por **SP e IDP**.

## <a name="adding-costpoint-from-the-gallery"></a>Com adicionar o Costpoint da galeria

Para configurar a integração do Costpoint ao Azure AD, você precisa adicionar o Costpoint da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Costpoint** na caixa de pesquisa.
1. Selecione **Costpoint** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Costpoint usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Costpoint.

Para configurar e testar o SSO do Azure AD com o Costpoint, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o Costpoint](#configure-costpoint)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Costpoint](#create-costpoint-test-user)** para ter um equivalente de B.Fernandes no Costpoint que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Costpoint**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    > [!NOTE]
    > Você obterá o arquivo de metadados do Provedor de Serviços na seção **Gerar Metadados do Costpoint**, que é explicada mais adiante neste tutorial.
 
    1. Clique em **Carregar arquivo de metadados**.
    
    1. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.
    
    1. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente nas caixas de texto da seção do Costpoint

        > [!Note]
        > Se os valores de **Identificador** e **URL de resposta** não forem preenchidos automaticamente, preencha-os manualmente de acordo com seus requisitos. Verifique se **ID (Identificador) da entidade** e **URL de Resposta (URL do Serviço do Consumidor de Asserção)** estão definidos corretamente e se a **URL do ACS** é uma URL válida do Costpoint que termina com **/LoginServlet.cps**.

    1. Clique em **Definir URLs adicionais**.

    1. Na caixa de texto **Estado de Retransmissão**, digite um valor usando o seguinte padrão:`system=[your system], (for example, **system=DELTEKCP**)`

1. Se você deseja configurar o aplicativo no modo **SP** iniciado, execute o seguinte passo:
    
    Na caixa de texto **URL de Logon**, digite uma URL: `https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e o Estado de Retransmissão reais. Contate a [equipe de suporte ao Cliente do Costpoint](https://www.deltek.com/about/contact-us) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no ícone Copiar para copiar a **URL de metadados de federação de aplicativos** e salve-a Bloco de Notas.

   ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Gerar metadados do Costpoint

A configuração de SSO do SAML do Costpoint é explicada no guia **DeltekCostpoint711Security.pdf**. Nesse guia, confira a seção **Configuração de Logon único do SAML -> Configurar o Logon Único do SAML entre o Costpoint e o Azure AD**. Siga as instruções e gere o arquivo **XML de Metadados de Federação do Costpoint SP**. Use isso na **Configuração Básica do SAML** no portal do Azure.

![Utilitário de Configuração do Costpoint](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> Você obterá o guia **DeltekCostpoint711Security.pdf** da [equipe de suporte ao cliente do Costpoint](https://www.deltek.com/about/contact-us). Se você não tiver esse arquivo, entre em contato com ela para obtê-lo.

### <a name="configure-costpoint"></a>Configurar Costpoint

Retorne ao **Utilitário de configuração do Costpoint** e cole a **URL de Metadados de Federação do Aplicativo** na caixa de texto **XML de Metadados de Federação do IdP** e continue as instruções do guia **DeltekCostpoint711Security.pdf** para concluir a configuração do Costpoint SAML. 

![Utilitário de Configuração do Costpoint](./media/costpoint-tutorial/config01.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Costpoint.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Costpoint**.
1. Na seção **Gerenciar** da página de visão geral do aplicativo, selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na Lista de usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, clique no botão **Atribuir**.

### <a name="create-costpoint-test-user"></a>Criar usuário de teste do Costpoint

Nesta seção, você criará um usuário no Costpoint. Suponha que a **ID de usuário** seja **B.FERNANDES** e o nome seja **B.Fernandes**. Trabalhe com a [equipe de suporte ao Cliente do Costpoint](https://www.deltek.com/about/contact-us) para adicionar o usuário à plataforma Costpoint. O usuário deve ser criado e ativado antes de usar o logon único.
 
Depois de criada, a seleção de **Método de Autenticação** do usuário deve ser **Active Directory**, a caixa de seleção **Logon Único do SAML** deve ser selecionada e o nome de usuário do Azure Active Directory deve ser **Active Directory ou ID do Certificado** (como mostrado abaixo).

![Usuário do Costpoint](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Costpoint no Painel de Acesso, você deverá ser conectado automaticamente ao Costpoint, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)