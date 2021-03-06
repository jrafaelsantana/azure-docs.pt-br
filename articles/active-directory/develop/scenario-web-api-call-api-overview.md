---
title: API Web que chama APIs da Web downstream (visão geral) – plataforma de identidade da Microsoft
description: Saiba como criar uma API Web que chama APIs da Web downstream (visão geral).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852492"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Cenário: API Web que chama as APIs Web

Saiba tudo o que você precisa para criar uma API Web que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

Esse cenário, a API Web protegida que chama APIs da Web, se baseia no cenário "proteger uma API Web". Para saber mais sobre esse cenário básico, confira [API Web protegida-cenário](scenario-protected-web-api-overview.md) primeiro.

## <a name="overview"></a>Visão geral

- Um cliente (Web, desktop, móvel ou aplicativo de página única) – não representado no diagrama abaixo – chama uma API Web protegida e fornece um token de portador JWT em seu cabeçalho http "Authorization".
- A API da Web protegida valida o token e usa o método `AcquireTokenOnBehalfOf` MSAL para solicitar (do Azure AD) outro token para que ele possa, por sua vez, chamar uma segunda API da Web (chamada de API da Web downstream) em nome do usuário.
- A API Web protegida usa esse token para chamar uma API downstream. Ele também pode chamar `AcquireTokenSilent`mais tarde para solicitar tokens para outras APIs downstream (mas ainda em nome do mesmo usuário). `AcquireTokenSilent`atualiza o token quando necessário.

![API Web chamando uma API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Especificações

A parte do registro de aplicativo relacionada às permissões de API é clássica. A configuração do aplicativo envolve o uso do fluxo em nome de do OAuth 2,0 para trocar o token de portador JWT em relação a um token para uma API downstream. Esse token é adicionado ao cache de token, onde está disponível nos controladores da API Web e pode adquirir um token silenciosamente para chamar as APIs de downstream.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do Aplicativo](scenario-web-api-call-api-app-registration.md)
