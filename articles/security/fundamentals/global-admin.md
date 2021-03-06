---
title: Habilitar a MFA para todos os administradores do Azure
description: Diretrizes para habilitar o administrador global
ms.service: security
ms.subservice: security-fundamentals
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: e30ce71a66dd5cb6c810111d359660d875ae97a8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927912"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Impor a MFA (autenticação multifator) para administradores de assinaturas

Quando você cria seus administradores, incluindo sua conta de administrador global, é essencial usar métodos de autenticação muito fortes.

Você pode executar a administração cotidiana atribuindo funções específicas de administrador – como administrador do Exchange ou administrador de senhas – para contas de usuário da equipe de TI conforme necessário.
Além disso, habilitar a [Autenticação Multifator (MFA) do Azure](../../active-directory/authentication/multi-factor-authentication.md) para seus administradores adiciona uma segunda camada de segurança para logons de usuário e transações. A MFA do Azure também ajuda o departamento de TI a reduzir a probabilidade de que uma credencial comprometida tenha acesso aos dados da organização.

Por exemplo:  Você impõe o Azure MFA para seus usuários e o configura para usar uma chamada telefônica ou uma mensagem de texto como verificação. Se as credenciais do usuário estiverem comprometidas, o invasor não poderá acessar nenhum recurso, pois não terá acesso ao telefone do usuário. As organizações que não adicionam camadas adicionais de proteção de identidade são mais suscetíveis a ataques de roubo de credenciais, que podem levar ao comprometimento dos dados.

Uma alternativa para as organizações que desejam manter todo o controle da autenticação localmente é usar o [Servidor de Autenticação Multifator do Azure](../../active-directory/authentication/howto-mfaserver-deploy.md), também chamado de "MFA local". Usando esse método você ainda poderá impor a autenticação multifator, mantendo o servidor MFA local.

Você pode verificar quem em sua organização tem privilégios administrativos usando o seguinte comando de PowerShell V2 do Microsoft Azure AD:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Enabling MFA

Revise como a [MFA](../../active-directory/authentication/howto-mfa-mfasettings.md) opera antes de continuar.

Se os usuários tiverem licenças que incluam a Autenticação Multifator do Microsoft Azure, nada precisa ser feito para ativar o Azure MFA. Você pode começar solicitando uma verificação em duas etapas em base de usuário individual. As licenças que habilitam o Azure MFA são:

- Autenticação Multifator do Azure
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação em duas etapas para usuários

Use um dos procedimentos listados em [Como exigir uma verificação em duas etapas](../../active-directory/authentication/howto-mfa-userstates.md) para um usuário ou grupo para começar a usar o Azure MFA. Você pode optar por impor a verificação em duas etapas para todas as entradas ou pode criar políticas de acesso condicional para exigir a verificação em duas etapas somente quando for importante para você.

