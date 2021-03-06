---
title: Laboratórios de sala de aula em Azure Lab Services-perguntas frequentes | Microsoft Docs
description: Encontre respostas para perguntas comuns sobre os laboratórios de sala de aula em Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 411037dd97350d877aff4e2d094c3408f168f9fd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648569"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Laboratórios de sala de aula em Azure Lab Services-perguntas frequentes
Obtenha respostas para algumas das perguntas mais comuns sobre os laboratórios de sala de aula em Azure Lab Services. 

## <a name="quotas"></a>Cotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A cota por usuário ou por semana ou por toda a duração do laboratório? 
A cota que você definiu para um laboratório é para cada aluno toda a duração do laboratório. E o [tempo de execução agendado das VMs](how-to-create-schedules.md) não conta com a cota alocada para um usuário. A cota é para o tempo fora dos horários agendados que um aluno passa em VMs.  Para obter mais informações sobre cotas, consulte [definir cotas para usuários](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Agendamentos

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Todas as VMs no laboratório são iniciadas automaticamente quando uma agenda é definida? 
Nº Nem todas as VMs. Somente as VMs que são atribuídas aos usuários em um agendamento. As VMs que não estão atribuídas a um usuário não são iniciadas automaticamente. É por design. 

## <a name="lab-accounts"></a>Contas de laboratório

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Por que não consigo criar um laboratório devido à indisponibilidade do intervalo de endereços? 
Os laboratórios de sala de aula podem criar VMs de laboratório dentro de um intervalo de endereços IP especificado ao criar sua conta de laboratório no portal do Azure. Quando um intervalo de endereços é fornecido, cada laboratório criado depois de ser alocado 512 endereços IP para VMs de laboratório. O intervalo de endereços para a conta do laboratório deve ser grande o suficiente para acomodar todos os laboratórios que você pretende criar na conta do laboratório. 

Por exemplo, se você tiver um bloco de/19-10.0.0.0/19, esse intervalo de endereços acomoda 8192 endereços IP e 16 laboratórios (8192/512 = 16 laboratórios). Nesse caso, a criação de laboratório falha na criação de 17 laboratórios.

## <a name="blog-post"></a>Postagem no blog
Assine o [blog do Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Notificações de atualização
Assine [as atualizações do Lab Services](https://azure.microsoft.com/updates/?product=lab-services) para se manter informado sobre os novos recursos no Lab Services.

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Se sua pergunta não estiver listada aqui, informe-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Poste uma pergunta no final destas Perguntas Frequentes. 
- Para alcançar um público mais amplo, poste uma pergunta no [Fórum de Azure Lab Services Stack Overflow](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Para solicitações de recursos, envie suas solicitações e ideias para [Azure Lab Services voz do usuário](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

