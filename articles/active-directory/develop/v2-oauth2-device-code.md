---
title: Usar a plataforma de identidade da Microsoft para conectar usuários em dispositivos sem navegador | Azure
description: Crie fluxos de autenticação inseridos e sem navegador usando a concessão de código do dispositivo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd99899494e9f7b3c0caa4e83f18803b969db1e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192725"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Plataforma de identidade da Microsoft e o fluxo de código do dispositivo OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A plataforma de identidade da Microsoft dá suporte à [concessão de código de dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), que permite aos usuários entrar em dispositivos com restrição de entrada, como uma TV inteligente, um dispositivo IOT ou uma impressora.  Para habilitar esse fluxo, o dispositivo exige que o usuário visite uma página da Web no navegador em outro dispositivo para entrar.  Depois que o usuário entra, o dispositivo é capaz de acessar e atualizar tokens, conforme o necessário.  

> [!IMPORTANT]
> Neste momento, o ponto de extremidade da plataforma Microsoft Identity só dá suporte ao fluxo do dispositivo para locatários do Azure AD, mas não a contas pessoais.  Isso significa que você deve usar um ponto de extremidade configurado como um locatário ou o `organizations` ponto de extremidade.  Esse suporte será habilitado em breve. 
>
> As contas pessoais que forem convidadas para um locatário do Azure AD poderão usar a concessão de fluxo do dispositivo, mas somente no contexto do locatário.
>
> Como uma observação adicional, o `verification_uri_complete` campo de resposta não é incluído ou tem suporte no momento.  Mencionamos isso porque, se você ler o padrão, verá `verification_uri_complete` que está listado como uma parte opcional do padrão de fluxo de código do dispositivo.

> [!NOTE]
> O ponto de extremidade da plataforma Microsoft Identity não dá suporte a todos os cenários e recursos de Azure Active Directory. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de código do dispositivo inteiro é semelhante ao diagrama a seguir. Descrevemos cada uma das etapas neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Solicitação de autorização de dispositivo

O cliente deve primeiro verificar o servidor de autenticação para obter um código de usuário e de dispositivo usado para iniciar a autenticação. O cliente coleta essa solicitação do ponto de extremidade `/devicecode`. Nessa solicitação, o cliente também deve incluir as permissões que precisa adquirir do usuário. A partir do momento em que essa solicitação é enviada, o usuário tem apenas 15 minutos para entrar (o valor normal para `expires_in`), portanto, somente faça essa solicitação quando o usuário indicar que está pronto para entrar.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Tente executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário |O locatário do diretório para o qual você deseja solicitar permissão. Pode estar no formato de nome amigável ou de GUID.  |
| `client_id` | Necessário | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `scope` | Recomendado | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) para os quais você deseja o consentimento do usuário.  |

### <a name="device-authorization-response"></a>Resposta de autorização de dispositivo

Uma resposta bem-sucedida será um objeto JSON contendo as informações necessárias para permitir que o usuário faça login.  

| Parâmetro | Formatar | Descrição |
| ---              | --- | --- |
|`device_code`     | Cadeia | Uma cadeia de caracteres longa usada para verificar a sessão entre o cliente e o servidor de autorização. O cliente usa esse parâmetro para solicitar o token de acesso do servidor de autorização. |
|`user_code`       | Cadeia | Uma cadeia de caracteres curta mostrada para o usuário que é usado para identificar a sessão em um dispositivo secundário.|
|`verification_uri`| URI | O URI que o usuário deve acessar com o `user_code` para entrar. |
|`expires_in`      | int | O número de segundos antes que o `device_code` e o `user_code` expirem. |
|`interval`        | int | O número de segundos que o cliente deve aguardar entre as solicitações de sondagem. |
| `message`        | Cadeia | Uma cadeia de caracteres legível com instruções para o usuário. Ela pode ser localizada incluindo um **parâmetro de consulta** na solicitação do formulário `?mkt=xx-XX`, preenchendo o código de cultura do idioma apropriado. |

## <a name="authenticating-the-user"></a>Autenticação do usuário

Depois de receber `user_code` o `verification_uri`e o, o cliente os exibe para o usuário, instruindo-os a entrar usando seu telefone celular ou navegador de PC.  Além disso, o cliente pode usar um código QR ou um mecanismo semelhante para exibir o `verfication_uri_complete`, o que levará à etapa de inserir o `user_code` para o usuário.

Enquanto o usuário está se autenticando no `verification_uri`, o cliente deverá estar sondando o ponto de extremidade `/token` para o token solicitado usando o `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parâmetro | Obrigatório | Descrição|
| -------- | -------- | ---------- |
| `grant_type` | Necessário | Precisa ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Necessário | Precisa corresponder à `client_id` usada na solicitação inicial. |
| `device_code`| Necessário | O `device_code` retornado na solicitação de autorização de dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código do dispositivo é um protocolo de sondagem, de modo que o cliente deve esperar receber erros antes de concluir a autenticação do usuário.  

| Erro | Descrição | Ação do Cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O usuário não concluiu a autenticação, mas não cancelou o fluxo. | Repita a solicitação depois de pelo menos `interval` segundos. |
| `authorization_declined` | O usuário final negou a solicitação de autorização.| Interrompa a sondagem e reverta para um estado não autenticado.  |
| `bad_verification_code`| O `device_code` enviado para o `/token` ponto de extremidade não foi reconhecido. | Verifique se o cliente está enviando o `device_code` correto na solicitação. |
| `expired_token` | Pelo menos `expires_in` segundos foram decorridos e a autenticação não é mais possível com este `device_code`. | Pare a sondagem e a reversão para um estado não autenticado. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

Uma resposta de token bem-sucedida se parecerá com esta:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parâmetro | Formatar | Descrição |
| --------- | ------ | ----------- |
| `token_type` | Cadeia| Sempre "Portador. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso for retornado, isso listará os escopos em que o token de acesso é válido. |
| `expires_in`| int | Número de segundos antes que o token de acesso incluído seja válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados.  |
| `id_token`   | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`.  |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`.  |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo documentado na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
