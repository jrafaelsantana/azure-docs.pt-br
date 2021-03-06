---
title: Autenticação multifator do Azure-como funciona-Azure Active Directory
description: A Autenticação Multifator do Azure ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b441803a066b794d17b46afec4d930b94ebdea
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125128"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: Autenticação Multifator do Azure

A segurança da verificação em duas etapas baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga descobrir a senha do usuário, ela será inútil se ele também não estiver de posse do método de autenticação adicional. Isso funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe (geralmente uma senha)
* Algo que você tem (um dispositivo confiável que não é duplicado facilmente, como um telefone)
* Algo que você é (biometria)

<center>

![Imagem dos métodos de autenticação conceitual](./media/concept-mfa-howitworks/methods.png)</center>

A MFA (Autenticação Multifator do Azure) ajuda a proteger o acesso a dados e aplicativos enquanto mantém a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de [métodos de autenticação](concept-authentication-methods.md) fáceis de usar. Os usuários podem ou não ser desafiados para MFA com base em decisões de configuração tomadas por um administrador.

## <a name="how-to-get-multi-factor-authentication"></a>Como obter a Autenticação Multifator?

A autenticação multifator é fornecida como parte das seguintes ofertas:

* **Azure Active Directory Premium** ou **Microsoft 365 Business** -uso completo da autenticação multifator do Azure usando políticas de acesso condicional para exigir a autenticação multifator.

* **Azure ad gratuito** ou licenças autônomas **do Office 365** – use [políticas de proteção de linha de base de acesso condicional](../conditional-access/concept-baseline-protection.md) pré-criado para exigir a autenticação multifator para seus usuários e administradores.

* **Administradores Globais do Azure Active Directory** – um subconjunto das funcionalidades de Autenticação Multifator do Azure está disponível como um meio para proteger as contas de administrador global.

> [!NOTE]
> Novos clientes não poderão mais comprar Autenticação Multifator do Microsoft Azure como uma oferta independente a partir de 1º de setembro de 2018. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

## <a name="supportability"></a>Capacidade de suporte

Como a maioria dos usuários está acostumada a usar apenas senhas para a autenticação, é importante que a sua empresa comunique todos os usuários sobre esse processo. A conscientização pode reduzir a probabilidade de que os usuários liguem para o seu suporte técnico para resolver pequenos problemas relacionados a MFA. No entanto, existem alguns cenários em que é necessário desabilitar temporariamente o MFA. Use as diretrizes abaixo para entender como lidar com esses cenários:

* Treine sua equipe de suporte para lidar com cenários em que o usuário não consegue entrar porque não têm acesso a seus métodos de autenticação ou esses métodos não estão funcionando corretamente.
   * Usando políticas de acesso condicional para o serviço do Azure MFA, sua equipe de suporte pode adicionar um usuário a um grupo que é excluído de uma política que requer MFA.
* Considere o uso de locais nomeados de acesso condicional como uma maneira de minimizar prompts de verificação em duas etapas. Com essa funcionalidade, os administradores podem ignorar a verificação em duas etapas para usuários que estão entrando de um local de rede confiável seguro, como um segmento de rede usado para integração de novo usuário.
* Implante [Azure ad Identity Protection](../active-directory-identityprotection.md) e dispare a verificação em duas etapas com base nas detecções de risco.

## <a name="next-steps"></a>Próximas etapas

- [Implantação passo a passo da autenticação multifator do Azure](howto-mfa-getstarted.md)
