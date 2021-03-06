---
title: 'Tutorial: Configurando o Velpic para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337641"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Tutorial: Configurando o Velpic para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Velpic e Azure AD para provisionar e desprovisionar automaticamente contas de usuário do Azure AD ao Velpic.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um locatário do Velpic com o [plano Enterprise](https://www.velpic.com/pricing.html) ou outro melhor habilitado
* Uma conta de usuário no Velpic com permissões de administrador

## <a name="assigning-users-to-velpic"></a>Atribuir usuários ao Velpic

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Velpic. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo Velpic seguindo as instruções aqui:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Dicas importantes para atribuir usuários ao Velpic

* É recomendável que um único usuário do Azure AD ser atribuído ao Velpic para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Velpic, você deve selecionar o **usuário** função, ou outra válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Observe que o **acesso padrão** função não funciona para provisionamento e esses usuários serão ignorados.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurando o provisionamento de usuário velpic

Esta seção orienta você pela conexão do Azure AD à API de provisionamento de conta de usuário do Velpic e configurar o serviço de provisionamento para criar, atualizar e desabilitar atribuídos a contas de usuário no Velpic com base na atribuição de usuário e grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML Single Sign-On para o Velpic, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Para configurar o provisionamento automático de usuário velpic no Azure AD:

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o Velpic para logon único, pesquise sua instância do Velpic usando o campo de pesquisa. Caso contrário, selecione **Add** e pesquise **Velpic** na Galeria de aplicativos. Selecione Velpic dos resultados da pesquisa e adicione-o à sua lista de aplicativos.

3. Selecione sua instância do Velpic e, em seguida, selecione a **provisionamento** guia.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Sob o **credenciais de administrador** seção, insira o **URL do locatário e segredo do Token** do Velpic. ( Você pode encontrar esses valores em sua conta do Velpic: **Gerencie** > **integração** > **plug-in** > **SCIM**)

    ![Valores de autorização](./media/velpic-provisioning-tutorial/Velpic2.png)

6. No portal do Azure, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao seu aplicativo Velpic. Se a conexão falhar, certifique-se de que sua conta do Velpic tem permissões de administrador e repita a etapa 5.

7. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

8. Clique em **Salvar**.

9. Na seção Mapeamentos, selecione **sincronizar Azure usuários do Active Directory ao Velpic**.

10. No **mapeamentos de atributo** seção, examine os atributos de usuário que serão sincronizados do Azure AD ao Velpic. Observe que os atributos selecionados como **correspondentes** propriedades serão usadas para corresponder as contas de usuário no Velpic para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o Azure AD Velpic serviço de provisionamento, altere o **Status de provisionamento** para **na** no **configurações** seção

12. Clique em **Salvar**.

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Velpic na seção usuários e grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatando o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)