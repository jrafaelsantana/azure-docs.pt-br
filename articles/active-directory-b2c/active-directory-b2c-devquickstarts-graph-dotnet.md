---
title: Usar o API do Graph no Azure Active Directory B2C
description: Como gerenciar usuários em um locatário Azure AD B2C chamando o API do Graph do Azure AD e usando uma identidade de aplicativo para automatizar o processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2585b47d049047cc191bfc284c4486361917f1ed
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802082"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure Active Directory B2C: Usar a API do Graph do Azure AD

Os locatários de Azure Active Directory B2C (Azure AD B2C) podem ter milhares ou milhões de usuários. Isso significa que muitas tarefas comuns de gerenciamento de locatário devem ser executadas programaticamente. O principal exemplo é o gerenciamento de usuário.

Talvez seja necessário migrar o repositório de usuário existente para um locatário B2C. Talvez seja melhor hospedar o registro de usuário em sua própria página e criar contas de usuário no seu diretório do Azure AD B2C nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Você pode executar essas tarefas usando o API do Graph do Azure AD.

Para locatários B2C, há dois modos principais de se comunicar com o API do Graph:

* Para tarefas **interativas**e de execução única, você deve atuar como uma conta de administrador no locatário B2C ao executar as tarefas. Esse modo requer que um administrador entre com credenciais antes de poder executar todas as chamadas à API do Graph.
* Para tarefas **automatizadas**e contínuas, você deve usar algum tipo de conta de serviço fornecido com os privilégios necessários para executar tarefas de gerenciamento. No Azure AD, você pode fazer isso registrando um aplicativo e autenticando no Azure AD. Isso é feito usando uma *ID de aplicativo* que usa a [concessão de credenciais do cliente OAuth 2.0](../active-directory/develop/service-to-service.md). Nesse caso, o aplicativo atua em nome próprio, e não como usuário, para chamar a API do Graph.

Neste artigo, você aprenderá a executar o caso de uso automatizado. Você vai criar um .NET 4.5 `B2CGraphClient` que executa as operações CRUD (criar, ler, atualizar e excluir) do usuário. O cliente terá uma CLI (interface de linha de comando) do Windows que permite que você chame vários métodos. No entanto, o código é escrito para se comportar de maneira não interativa e automatizada.

>[!IMPORTANT]
> Você **deve** usar o [API do Graph do Azure ad](../active-directory/develop/active-directory-graph-api-quickstart.md) para gerenciar usuários em um diretório Azure ad B2C. O API do Graph do Azure AD é diferente da API do Microsoft Graph. Saiba mais nesta postagem no blog do MSDN: [Microsoft Graph ou Azure ad Graph](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar aplicativos ou usuários, é necessário um locatário do Azure AD B2C. Se você ainda não tiver uma, [crie um locatário Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Registrar um aplicativo

Depois de ter um locatário Azure AD B2C, você precisa registrar seu aplicativo de gerenciamento usando o [portal do Azure](https://portal.azure.com). Você também precisa conceder a ele as permissões necessárias para executar tarefas de gerenciamento em nome do seu aplicativo de gerenciamento ou de script automatizado.

### <a name="register-application-in-azure-active-directory"></a>Registrar aplicativo no Azure Active Directory

Para usar o API do Graph do Azure AD com seu locatário B2C, você precisa registrar um aplicativo usando o fluxo de trabalho de registro do aplicativo Azure Active Directory.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Atribuir permissões de acesso à API

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Criar segredo do cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Agora você tem um aplicativo que tem permissão para *criar*, *ler*e *atualizar* usuários em seu locatário Azure ad B2C. Continue na próxima seção para adicionar permissões de *exclusão* de usuário e *atualização de senha* .

## <a name="add-user-delete-and-password-update-permissions"></a>Adicionar permissões de exclusão de usuário e atualização de senha

A permissão *ler e gravar dados do diretório* que você concedeu anteriormente **não** inclui a capacidade de excluir usuários ou atualizar suas senhas.

Se você quiser dar ao aplicativo a capacidade de excluir usuários ou atualizar senhas, será necessário conceder a ela a função de *administrador de usuários* .

1. Entre no [portal do Azure](https://portal.azure.com) e alterne para o diretório que contém o locatário Azure ad B2C.
1. Selecione **Azure ad B2C** no menu à esquerda. Ou então, selecione **todos os serviços** e, em seguida, pesquise e selecione **Azure ad B2C**.
1. Em **gerenciar**, selecione **funções e administradores**.
1. Selecione a função de **administrador de usuários** .
1. Selecione **Adicionar atribuição**.
1. Na caixa de texto **selecionar** , digite o nome do aplicativo que você registrou anteriormente, por exemplo, *managementapp1*. Selecione seu aplicativo quando ele aparecer nos resultados da pesquisa.
1. Selecione **Adicionar**. Pode levar alguns minutos para que as permissões se propaguem totalmente.

Seu aplicativo Azure AD B2C agora tem as permissões adicionais necessárias para excluir usuários ou atualizar suas senhas em seu locatário B2C.

## <a name="get-the-sample-code"></a>Obter o código de amostra

O exemplo de código é um aplicativo de console .NET que usa o [biblioteca de autenticação do Active Directory (Adal)](../active-directory/develop/active-directory-authentication-libraries.md) para interagir com o API do Graph do Azure AD. Seu código demonstra como chamar a API para gerenciar programaticamente os usuários em um locatário Azure AD B2C.

Você pode [baixar o arquivo morto de exemplo](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) ou clonar o repositório github:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Depois de obter o exemplo de código, configure-o para o seu ambiente e, em seguida, compile o projeto:

1. Abra a solução `B2CGraphClient\B2CGraphClient.sln` no Visual Studio.
1. No projeto **B2CGraphClient** , abra o arquivo *app. config* .
1. Substitua a `<appSettings>` seção pelo XML a seguir. Em seguida `{your-b2c-tenant}` , substitua pelo nome do seu locatário `{Client secret}` e `{Application ID}` pelos valores que você registrou anteriormente.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Compilar a solução. Clique com o botão direito do mouse na solução **B2CGraphClient** no Gerenciador de soluções e selecione **Recompilar solução**.

Se a compilação for bem-sucedida, o `B2C.exe` aplicativo de console poderá ser encontrado `B2CGraphClient\bin\Debug`em.

## <a name="review-the-sample-code"></a>Revisar o código de exemplo

Para usar o B2CGraphClient, abra um prompt de comando`cmd.exe`() e altere para o `Debug` diretório do projeto. Em seguida, execute `B2C Help` o comando.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

O `B2C Help` comando exibe uma breve descrição dos subcomandos disponíveis. Cada vez que você invoca um de seus subcomandos `B2CGraphClient` , o envia uma solicitação para o API do Graph do Azure AD.

As seções a seguir discutem como o código do aplicativo faz chamadas para o API do Graph do Azure AD.

### <a name="get-an-access-token"></a>Obter um token de acesso

Qualquer solicitação para o API do Graph do Azure AD requer um token de acesso para autenticação. `B2CGraphClient`usa o ADAL (Biblioteca de Autenticação do Active Directory de software livre) para auxiliar na obtenção de tokens de acesso. A ADAL facilita a aquisição de token ao fornecer uma API auxiliar e cuidar de alguns detalhes importantes, como o cache de tokens de acesso. No entanto, você não precisa usar a ADAL para obter tokens. Em vez disso, você pode obter tokens criando manualmente solicitações HTTP.

> [!NOTE]
> Você deve usar a ADAL v2 ou superior para obter tokens de acesso que podem ser usados com o API do Graph do Azure AD. Você não pode usar o ADAL v1.

Quando `B2CGraphClient` é executado, ele cria uma instância `B2CGraphClient` da classe. O construtor dessa classe configura o scaffolding de autenticação ADAL:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos usar o `B2C Get-User` comando como exemplo.

Quando `B2C Get-User` é invocado sem argumentos adicionais, o aplicativo chama `B2CGraphClient.GetAllUsers()` o método. `GetAllUsers()`em seguida `B2CGraphClient.SendGraphGetRequest()`, chama, que envia uma solicitação HTTP Get para o API do Graph do Azure AD. Antes `B2CGraphClient.SendGraphGetRequest()` de enviar a solicitação GET, primeiro ele obtém um token de acesso usando a Adal:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

Você pode obter um token de acesso para a API do Graph chamando o método ADAL `AuthenticationContext.AcquireToken()`. Em seguida, a ADAL retorna um `access_token` que representa a identidade do aplicativo.

### <a name="read-users"></a>Ler usuários

Quando desejar obter uma lista de usuários ou obter um usuário específico do API do Graph do Azure AD, você poderá enviar uma solicitação HTTP `GET` para o `/users` ponto de extremidade. Uma solicitação para todos os usuários em um locatário tem esta aparência:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver essa solicitação, execute:

 ```cmd
 B2C Get-User
 ```

Há dois aspectos importantes a serem observados:

* O token de acesso obtido usando a Adal é adicionado ao `Authorization` cabeçalho usando o `Bearer` esquema.
* Para locatários B2C, você deve usar o parâmetro de consulta `api-version=1.6`.

Ambos esses detalhes são tratados no `B2CGraphClient.SendGraphGetRequest()` método:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Criar contas de usuário consumidor

Ao criar contas de usuário em seu locatário B2C, você pode enviar uma solicitação `POST` http para o `/users` ponto de extremidade. A solicitação HTTP `POST` a seguir mostra um exemplo de usuário a ser criado no locatário.

A maioria das propriedades na solicitação a seguir é necessária para criar usuários do consumidor. Os `//` comentários foram incluídos para ilustração--não os incluam em uma solicitação real.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Para ver a solicitação, execute um dos seguintes comandos:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

O `Create-User` comando usa como um parâmetro de entrada um arquivo JSON que contém uma representação JSON de um objeto de usuário. Há dois arquivos JSON de exemplo no exemplo de código: `usertemplate-email.json` e `usertemplate-username.json`. Você pode modificar esses arquivos para atender às suas necessidades. Além dos campos obrigatórios acima, vários campos opcionais são incluídos nos arquivos.

Para obter mais informações sobre os campos obrigatórios e opcionais, consulte a [entidade e a referência de tipo complexo | Referência de API do Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Você pode ver como a solicitação POST é construída em `B2CGraphClient.SendGraphPostRequest()`:

* Ela anexa um token de acesso ao cabeçalho `Authorization` da solicitação.
* Ela define `api-version=1.6`.
* Ela inclui o objeto de usuário JSON no corpo da solicitação.

> [!NOTE]
> Se as contas que você deseja migrar de um repositório de usuários existente tiverem uma intensidade de senha menor do que a [força de senha forte imposta por Azure ad B2C](active-directory-b2c-reference-password-complexity.md), você poderá desabilitar o requisito de senha forte `DisableStrongPassword` usando o valor no `passwordPolicies`propriedade. Por exemplo, você pode modificar a solicitação de criação de usuário anterior da `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`seguinte maneira:.

### <a name="update-consumer-user-accounts"></a>Atualizar contas de usuário consumidor

Quando você atualiza objetos de usuário, o processo é semelhante ao que você usa para criar objetos de usuário, mas usa o `PATCH` método http:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Tente atualizar um usuário modificando alguns valores em seus arquivos JSON e, em seguida, `B2CGraphClient` use o para executar um destes comandos:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o `B2CGraphClient.SendGraphPatchRequest()` método para obter detalhes sobre como enviar esta solicitação.

### <a name="search-users"></a>Pesquisar usuários

Você pode pesquisar usuários em seu locatário B2C de duas maneiras:

* Referencie a ID de **objeto**do usuário.
* Referencie seu identificador de entrada, a `signInNames` propriedade.

Execute um dos seguintes comandos para procurar um usuário:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Por exemplo:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
```

### <a name="delete-users"></a>Excluir usuários

Para excluir usuários, use o método `DELETE` http e construa a URL com a ID de objeto do usuário:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, insira esse comando e exiba a solicitação Delete exclusão resultante que é mostrada no console:

```cmd
B2C Delete-User <object-id-of-user>
```

Inspecione o `B2CGraphClient.SendGraphDeleteRequest()` método para obter detalhes sobre como enviar esta solicitação.

Você pode executar outras ações com a API do Graph do Azure AD além do gerenciamento de usuários. A [Referência da API do Graph do Azure AD](/previous-versions/azure/ad/graph/api/api-catalog) fornece detalhes de cada ação, bem como solicitações de exemplo.

## <a name="use-custom-attributes"></a>Usar atributos personalizados

Quase todos os aplicativos consumidores precisam armazenar algum tipo de informação de perfil de usuário personalizada. Uma maneira de fazer isso é definir um atributo personalizado em seu locatário B2C. Em seguida, você pode tratar esse atributo da mesma maneira que trata qualquer outra propriedade em um objeto de usuário. Você pode atualizar, excluir ou consultar o atributo, enviá-lo como uma declaração de entrada em tokens e assim por diante.

Para definir um atributo personalizado no locatário B2C, veja a [Referência de atributo personalizado do B2C](active-directory-b2c-reference-custom-attr.md).

Você pode exibir os atributos personalizados definidos em seu locatário B2C usando os seguintes `B2CGraphClient` comandos:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A saída revela os detalhes de cada atributo personalizado. Por exemplo:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Você pode usar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade nos objetos de usuário. Atualize o arquivo JSON com a nova propriedade e um valor para a propriedade e, em seguida, execute:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Próximas etapas

Usando `B2CGraphClient`, você terá um aplicativo de serviço que pode gerenciar os usuários de locatário B2C de forma programática. `B2CGraphClient` usa sua própria identidade de aplicativo para se autenticar na API do Graph do Azure AD. Ele também adquire tokens usando um segredo do cliente.

Ao incorporar essa funcionalidade em seu próprio aplicativo, lembre-se de alguns pontos-chave para aplicativos B2C:

* Conceda ao aplicativo as permissões necessárias no locatário.
* Por enquanto, você precisa usar ADAL (não MSAL) para obter tokens de acesso. (Você também pode enviar mensagens de protocolo diretamente, sem usar uma biblioteca.)
* Ao chamar a API do Graph, use `api-version=1.6`.
* Quando você cria e atualiza usuários consumidores, algumas propriedades são obrigatórias, conforme descrito acima.
