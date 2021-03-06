---
title: Corrija problemas de associação dinâmica de grupos - Azure Active Directory | Microsoft Docs
description: Solução de problemas para a associação dinâmica para grupos no Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eededcc180d7652fd52c79b85ca3c34f65a22a4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469691"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Solucionar problemas e resolver problemas de grupos

## <a name="troubleshooting-group-creation-issues"></a>Solução de problemas de criação de grupo

**Eu o desabilitei criação do grupo de segurança no portal do Azure, mas ainda podem ser criados grupos por meio do Powershell** as **usuário pode criar grupos de segurança nos portais do Azure** configuração nos controles do portal do Azure ou não não-administrador os usuários podem criar grupos de segurança no painel de acesso ou no portal do Azure. Ele não controla a criação do grupo de segurança por meio do Powershell.

Para desativar a criação de grupo para usuários não administradores no Powershell:
1. Verifique se os usuários não administradores têm permissão para criar grupos:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Se retornar `UsersPermissionToCreateGroupsEnabled : True`, os usuários não administradores podem criar grupos. Para desabilitar esse recurso:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Recebi um grupos máximo permitido de erro ao tentar criar um grupo dinâmico no Powershell**<br/>
Se você receber uma mensagem no Powershell indicando _políticas de grupo dinâmico máx permitidas contagem de grupos atingida_, isso significa que você tenha atingido o limite máximo para grupos dinâmicos no seu locatário. O número máximo de grupos dinâmicos por locatário é 5.000.

Para criar novos grupos de dinâmicos, primeiro será necessário excluir alguns grupos dinâmicos existentes. Não há nenhuma maneira de aumentar o limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solucionando problemas de associações dinâmicas a grupos

**Configurei uma regra em um grupo, mas nenhuma associação foi atualizada no grupo**<br/>
1. Verifique se os valores para atributos de dispositivo na regra ou de usuário. Verifique se há usuários que atendem à regra. Para dispositivos, verifique as propriedades do dispositivo para garantir que todos os atributos sincronizados contêm os valores esperados.<br/>
2. Verifique a status de processamento de associação para confirmar se ele foi concluído. Você pode verificar a [status de processamento de associação](groups-create-rule.md#check-processing-status-for-a-rule) e a última data de atualização no **visão geral** page do grupo.

Se tudo estiver correto, aguarde alguns instantes para que o grupo seja populado. Dependendo do tamanho do seu locatário, o grupo pode levar até 24 horas para ser populado pela primeira vez ou depois de uma alteração de regra.

**Configurei uma regra, mas agora os membros da regra existentes foram removidos**<br/>Este comportamento é esperado. Membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. Os usuários retornados da avaliação da regra são adicionados como membros ao grupo.

**Não vejo as alterações da associação instantaneamente quando adiciono ou altero uma regra, por que não?**<br/>A avaliação de associação dedicada é feita periodicamente em um processo assíncrono em segundo plano. O tempo que o processo leva é determinado pelo número de usuários no diretório e pelo tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com um pequeno número de usuários verão as alterações de associação de grupo em poucos minutos. Os diretórios com um grande número de usuários podem levar até 30 minutos ou mais para serem populados.

**Como forçar o grupo a ser processada agora?**<br/>
Atualmente, não há nenhuma maneira para disparar automaticamente o grupo a ser processada sob demanda. No entanto, você pode disparar manualmente o reprocessamento atualizando a regra de associação para adicionar um espaço em branco no final.  

**Eu encontrei uma erro de processamento de regra**<br/>A seguinte tabela relacionará os erros de regra de associação e como corrigi-los.

| Erro do analisador de regra | Erro de uso | Uso corrigido |
| --- | --- | --- |
| Erro: Atributo sem suporte. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade<br/><br/>Verifique se o atributo está na [lista de propriedades com suporte](groups-dynamic-membership.md#supported-properties). |
| Erro: Não há suporte para o operador no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade<br/><br/>Não há suporte para o operador usado para o tipo de propriedade (neste exemplo, -contains não pode ser usado no tipo booliano). Use os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação da consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Operador ausente. Use -and ou -or para unir predicados<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Erro na expressão regular usada com -match<br>(user.userPrincipalName -match "*@domain.ext")<br>ou alternativamente: (user.userPrincipalName -match "@domain.ext") |

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](../fundamentals/active-directory-manage-groups.md)
* [Gerenciamento de aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Active Directory do Azure?](../fundamentals/active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](../hybrid/whatis-hybrid-identity.md)