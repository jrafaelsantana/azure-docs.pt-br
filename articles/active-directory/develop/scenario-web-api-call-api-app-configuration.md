---
title: API Web que chama APIs da Web downstream (configuração de código do aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar uma API Web que chama APIs da Web (configuração de código do aplicativo)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 529665a03d2203dcb501b59d7647f4390bdaeb78
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936733"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>API Web que chama as APIs da Web-configuração de código

Depois de registrar sua API Web, você pode configurar o código para o aplicativo.

O código para configurar sua API Web para que ele chame as APIs da Web downstream com base no código usado para proteger uma API da Web. Para obter mais informações, consulte [Protected Web API-configuração de aplicativo](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Código assinado para OnTokenValidated

Na parte superior da configuração de código de qualquer API Web protegida, você precisa assinar a validação do token de portador que é recebido quando sua API é chamada:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Fluxo em nome de

O método AddAccountToCacheFromJwt () precisa:

- Crie uma instância de um aplicativo cliente MSAL confidencial.
- Chame `AcquireTokenOnBehalf` para trocar o token de portador que foi adquirido pelo cliente para a API da Web, em relação a um token de portador para o mesmo usuário, mas para nossa API chamar uma API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Criar uma instância de um aplicativo cliente confidencial

Esse fluxo só está disponível no fluxo de cliente confidencial para que a API Web protegida forneça credenciais de cliente (segredo do cliente ou certificado) para o [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) por meio dos métodos `WithClientSecret` ou `WithCertificate`, respectivos.

![imagem](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Por fim, em vez de um segredo do cliente ou de um certificado, os aplicativos cliente confidenciais também podem provar sua identidade usando as asserções do cliente.
Este cenário avançado é detalhado em [asserções do cliente](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Como chamar em nome de

A chamada em nome de (obo) é feita chamando o método [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na `IConfidentialClientApplication` interface.

O `UserAssertion` é criado a partir do token de portador recebido pela API da Web de seus próprios clientes. Há [dois construtores](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), um que usa um token de portador JWT e um que usa qualquer tipo de declaração de usuário (outro tipo de token de segurança, que tipo é especificado em um parâmetro adicional chamado `assertionType`).

![imagem](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Na prática, o fluxo de obo geralmente é usado para adquirir um token para uma API downstream e armazená-lo no cache de tokens de usuário MSAL.net para que outras partes da API da Web possam, posteriormente, chamar as [substituições](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` para chamar as APIs de downstream. Essa chamada tem o efeito de atualizar os tokens, se necessário.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

## <a name="protocol"></a>Protocol

Para obter mais informações sobre o protocolo em nome de, consulte [plataforma de identidade da Microsoft e fluxo em nome de OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirindo um token para o aplicativo](scenario-web-api-call-api-acquire-token.md)
