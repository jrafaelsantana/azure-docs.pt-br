---
title: Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD | Microsoft Docs
description: Como solucionar problemas comuns enfrentados ao configurar provisionamento do usuário para um aplicativo já listado na Galeria de aplicativos do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8eaa46b46551f9b6075ec10b38de80f84c22a0
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034169"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD

Configurando [provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) para um aplicativo (onde houver suporte), requer que as instruções específicas ser seguidas para preparar o aplicativo para o provisionamento automático. Em seguida, você pode usar o portal do Azure para configurar o serviço de provisionamento para sincronizar contas de usuário ao aplicativo.

Você sempre deve começar encontrando o tutorial de instalação específica de configuração de provisionamento para seu aplicativo. Siga essas etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento. Uma lista de tutoriais de aplicativos podem ser encontrados em [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Como saber se o provisionamento está funcionando 

Depois que o serviço estiver configurado, a maioria das informações sobre a operação do serviço podem ser obtidas de dois lugares:

-   **Provisionando logs (versão prévia)** – os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registram todas as operações executadas pelo serviço de provisionamento, incluindo a consulta do Azure ad para usuários atribuídos que estão no escopo do provisionamento. O aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema de consulta. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação. Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos** &gt; empresariais **logs de provisionamento (versão prévia)** na seção **atividade** .

-   **Status atual –** Um resumo da última execução de provisionamento para um determinado aplicativo pode ser visto na seção **Azure Active Directory &gt; o provisionamento de nome &gt; \] &gt;de aplicativo dos \[aplicativos empresariais** , na parte inferior do na tela nas configurações do serviço. A seção status atual mostra se um ciclo de provisionamento começou a provisionar contas de usuário. Você pode ver o progresso do ciclo, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas. Se houver erros, os detalhes poderão ser encontrados nos logs de [provisionamento (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar

Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

* [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
* Não é possível salvar a configuração devido a credenciais de aplicativo não estarem funcionando
* [Os logs de provisionamento dizem que os usuários são "ignorados" e não provisionados, embora sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar

Se você definir o **Status de provisionamento** como **Ligado** na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** do portal do Azure. No entanto, nenhum outro detalhe de status é mostrado nessa página depois recarregamentos subsequentes. É provável que o serviço esteja em execução, mas ainda não tenha concluído um ciclo inicial. Verifique os **logs de provisionamento** descritos acima para determinar quais operações o serviço está executando e se há erros.

>[!NOTE]
>Um ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. As sincronizações subsequentes após o ciclo inicial serão mais rápidas, pois o serviço de provisionamento armazena as marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível salvar a configuração devido a credenciais de aplicativo não estarem funcionando

Para o provisionamento funcionar, Azure AD requer credenciais válidas que permitem a conexão com uma API fornecida pelo aplicativo de gerenciamento de usuário. Se as credenciais não funcionarem, ou você não souber quais devem ser, examine o tutorial de configuração do aplicativo, descrito anteriormente.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de provisionamento, digamos que os usuários sejam ignorados e não provisionados mesmo que sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de provisionamento, é muito importante ler os detalhes estendidos na mensagem de log para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

- **Um filtro de escopo foi configurado** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

- **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações sobre filtros de escopo, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

- **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento ser examinar e configurar os mapeamentos de atributo e fluxos de trabalho que definem quais fluxo propriedades de usuário (ou grupo) do Azure AD para o aplicativo. Isso inclui a configuração de "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre esse importante processo, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

  * **Mapeamentos de atributo para grupos:** Provisionamento do nome do grupo e dos detalhes do grupo, além dos membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade, habilitando ou desabilitando o **Mapeamento** para objetos de grupo mostrados na guia **Provisionamento**. Se o provisionamento de grupos é habilitado, certifique-se de rever os mapeamentos de atributos para garantir que um campo apropriado está sendo usado para a "ID correspondente". Este pode ser o nome de exibição ou alias de email, já que o grupo e seus membros não são provisionados se a propriedade correspondente está vazia ou não está preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](user-provisioning.md)
