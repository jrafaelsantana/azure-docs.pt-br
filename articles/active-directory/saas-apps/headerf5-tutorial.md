---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao F5 | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165519"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure AD (Azure Active Directory). Ao integrar o F5 ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao F5.
* Permitir que os usuários sejam conectados automaticamente ao F5 com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do F5.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO do F5 pode ser configurado de três maneiras.

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](#configure-f5-single-sign-on-for-header-based-application)

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Adicionar o F5 da galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar logon único do Azure AD para o F5

Configure e teste o SSO do Azure AD com o F5 usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do F5](#configure-f5-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do F5](#create-f5-test-user)** – para ter um equivalente de B. Fernandes no F5 vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **F5**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o F5**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao F5.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **F5**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configurar logon único do F5 para aplicativo baseado em cabeçalho

1. Abra uma nova janela do navegador da Web, entre no site da empresa F5 (Baseado em Cabeçalho) como administrador e execute as seguintes etapas:

1. Você precisa importar o Certificado de Metadados no F5 (Baseado em Cabeçalho) que será usado posteriormente no processo de instalação. Acesse **Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego >> Lista de Certificados SSL**. Clique em **Importar** do canto direito

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure12.png)
 
1. Além disso, você também precisa de um **Certificado SSL** para o Nome do host (`headerapp.superdemo.live`); neste exemplo, usamos o Certificado Curinga.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure13.png)

1. Acesse – **Acesso de Clique BIG-IP do F5 (Baseado em Cabeçalho) > Configuração Guiada > Federação > Provedor de Serviços SAML**.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure01.png)

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure02.png)
 
1. Especifique a **ID da Entidade** (a mesma que você definiu na Configuração de Aplicativos do Azure AD)

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure03.png) 

1. Crie um Servidor Virtual e especifique o **Endereço de Destino**; a **Porta de Redirecionamento** é opcional. Escolha o **Certificado Curinga** (ou o **Cert** que você carregou para o aplicativo) que carregamos anteriormente e a **Chave Privada Associada**.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure04.png) 

1. Carregue os **metadados** de configuração e especifique um novo **Nome para o conector IDP do SAML**. Também será necessário especificar o Certificado de Federação que foi carregado anteriormente.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure05.png)
 
1. **Crie** um pool de aplicativos de back-end e especifique os **Endereços IP** dos Servidores de Aplicativo de Back-end.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure06.png)

1. Em Logon Único, escolha **Baseado em cabeçalho HTTP**. Você pode adicionar outros cabeçalhos baseados em seu aplicativo. Confira o apêndice da lista de variáveis de sessão do SAML

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure07.png) 

1. Contate a [equipe de suporte ao Cliente F5 (Baseado em cabeçalho)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter detalhes sobre a documentação de **Propriedades de Verificação do Ponto de Extremidade**.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure08.png)

1. Contate a [equipe de suporte ao Cliente F5 (Baseado em cabeçalho)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter detalhes sobre a documentação de **Propriedades de Gerenciamento da Sessão**.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure09.png)

1. **Examine o resumo** e clique em **Implantar**.

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure10.png)

    ![Configuração do F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B.Fernandes no F5. Trabalhe com a [equipe de suporte ao Cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários à plataforma do F5. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do F5 no Painel de Acesso, você deverá ser conectado automaticamente ao F5 no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o F5 com o Azure AD](https://aad.portal.azure.com/)

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

