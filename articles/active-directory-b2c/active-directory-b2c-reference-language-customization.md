---
title: Personalização de idioma no Azure Active Directory B2C
description: Saiba mais sobre como personalizar a experiência de idioma em seus fluxos de usuário.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bced7a4b994172a1a2076149d6f25adb39c99b54
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015557"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização de idioma no Azure Active Directory B2C

A personalização de idioma no Azure AD B2C (Azure Active Directory B2C) permite que o fluxo de usuário acomode diferentes idiomas para atender às necessidades do cliente. A Microsoft fornece as traduções para [36 idiomas](#supported-languages), mas você também pode fornecer suas próprias traduções para qualquer idioma. Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas.

## <a name="how-language-customization-works"></a>Como funciona a personalização de idioma

Você usa a personalização de idioma para selecionar em quais idiomas o fluxo de usuários está disponível. Depois que o recurso estiver habilitado, você poderá fornecer o parâmetro da cadeia de caracteres de consulta `ui_locales`, a partir do seu aplicativo. Quando você chama o Azure AD B2C, sua página é traduzida para a localidade que você indicou. Esse tipo de configuração oferece controle total sobre os idiomas no fluxo do usuário e ignora as configurações de idioma do navegador do cliente.

Talvez você não precise desse nível de controle sobre quais idiomas seu cliente vê. Se você não fornecer um parâmetro `ui_locales` a experiência do cliente será determinada pelas configurações do navegador. Você ainda poderá controlar para quais idiomas o fluxo do usuário será traduzido, adicionando-o como um idioma com suporte. Se o navegador de um cliente estiver configurado para mostrar um idioma que você não deseja fornecer suporte, o idioma que você selecionou como padrão nas culturas com suporte será mostrado.

* **idioma especificado por ui-locales**: Após habilitar a personalização de idioma, o fluxo de usuário será traduzido para o idioma especificado aqui.
* **Idioma solicitado pelo navegador**: Se nenhum parâmetro `ui_locales` foi especificado, o fluxo de usuário será traduzido para o idioma solicitado pelo navegador, *se houver suporte para o idioma*.
* **Idioma padrão da política**: Se o navegador não especificar um idioma ou especificar um que não tenha suporte, o percurso do usuário será traduzido para o idioma padrão da política.

> [!NOTE]
> Se você estiver usando atributos de usuário personalizados, precisará fornecer suas próprias traduções. Para obter mais informações, consulte [Personalizar as cadeias de caracteres](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Solicitação de suporte de idiomas para ui_locales

Políticas criadas antes da disponibilidade geral de personalização de idioma precisarão, primeiro, habilitar esse recurso. Políticas e fluxos dos usuários que foram criados após a personalização de idioma são habilitados por padrão.

Ao habilitar a personalização de idioma em um fluxo de usuário, é possível controlar o idioma do fluxo de usuário, adicionando o parâmetro `ui_locales`.

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário que você quer habilitar para traduções.
1. Selecione os **Idiomas**.
1. Selecione **Habilitar personalização de idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecionar quais idiomas no fluxo de usuários estão habilitados

Habilite um conjunto de idiomas para que o fluxo de usuário seja traduzido quando solicitado pelo navegador sem o parâmetro `ui_locales`.

1. Certifique-se de que o fluxo de usuário tenha a personalização de idioma habilitada a partir das instruções anteriores.
1. Na página **Idiomas** do fluxo de usuário, selecione um idioma que você quer dar suporte.
1. No painel de propriedades, altere **Habilitado** para **Sim**.
1. Selecione **Salvar** na parte superior do painel de propriedades.

>[!NOTE]
>Se um parâmetro `ui_locales` não for fornecido, a página será traduzida para o idioma do navegador do cliente somente se estiver habilitada.
>

## <a name="customize-your-strings"></a>Como personalizar suas cadeias de caracteres

A personalização de idioma permite personalizar qualquer cadeia de caracteres no fluxo de usuário.

1. Certifique-se de que o fluxo de usuário tenha a personalização de idioma habilitada a partir das instruções anteriores.
1. Na página **Idiomas** do fluxo de usuário, selecione o idioma que você quer personalizar.
1. Em **Arquivos de recursos de nível de página**, selecione a página que você quer editar.
1. Selecione **Baixar Padrões** (ou **Baixar substituições**, se esse idioma já foi editado anteriormente).

Essas etapas fornecem um arquivo JSON que você pode usar para começar a editar suas cadeias de caracteres.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer cadeia de caractere na página

1. Abra o arquivo JSON baixado das instruções anteriores em um editor de JSON.
1. Localize o elemento que você deseja alterar. Você pode localizar `StringId` para a cadeia de caracteres que está procurando ou procurar o atributo `Value` que deseja alterar.
1. Atualize o atributo `Value` com o que você deseja exibir.
1. Para cada cadeia de caracteres que você deseja alterar, altere `Override` para `true`.
1. Salve o arquivo e carregue suas alterações. (É possível localizar o controle de upload no mesmo local de onde baixou o arquivo JSON.)

> [!IMPORTANT]
> Se você precisar substituir uma cadeia de caracteres, certifique-se de que o valor `Override` esteja definido para `true`. Se o valor não mudou, a entrada será ignorada.

### <a name="change-extension-attributes"></a>Alterar atributos de extensão

Se você quiser alterar a cadeia de caracteres de um atributo de usuário personalizado ou se deseja adicionar um ao JSON, ele estará no seguinte formato:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Substitua `<ExtensionAttribute>` pelo nome do seu atributo de usuário personalizado.

Substitua `<ExtensionAttributeValue>` por uma cadeia de caracteres nova a ser exibida.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornecer uma lista de valores usando LocalizedCollections

Se você quiser fornecer uma lista configurada de valores para respostas, será necessário criar um atributo `LocalizedCollections`. `LocalizedCollections` é uma matriz de pares `Name` e `Value`. A ordem dos itens será a ordem em que elas são exibidas. Para adicionar `LocalizedCollections`, use o formato a seguir:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` é o atributo do usuário ao qual esse atributo `LocalizedCollections` é uma resposta.
* `Name` é o valor mostrado para o usuário.
* `Value` é o que é retornado na declaração quando essa opção é selecionada.

### <a name="upload-your-changes"></a>Carregamento das suas alterações

1. Após concluir as alterações no arquivo JSON, retorne para o locatário B2C.
1. Selecione **Fluxos dos Usuários** e clique no fluxo de usuário que você quer habilitar para as traduções.
1. Selecione os **Idiomas**.
1. Selecione o idioma para o qual você deseja traduzir.
1. Selecione a página onde você deseja fornecer traduções.
1. Selecione o ícone da pasta e selecione o arquivo JSON para upload.

As alterações são salvas no fluxo de usuário automaticamente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizar a interface do usuário da página usando personalização de idioma

Há duas maneiras de localizar o conteúdo HTML. Uma maneira é ativar a [personalização de idioma](active-directory-b2c-reference-language-customization.md). Habilitar esse recurso permite que Azure ad B2C encaminhe o parâmetro OpenID Connect, `ui-locales`, para seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML personalizadas que são específicas a um idioma.

Como alternativa, é possível extrair conteúdo de lugares diferentes com base na localidade que está sendo usada. No ponto de extremidade habilitado para CORS, é possível configurar uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará adequadamente se usar o valor curinga `{Culture:RFC5646}`. Por exemplo, suponha que essa é a sua URI de página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Você pode carregar a página em `fr`. Quando a página efetua pulls de conteúdo HTML e CSS, ela efetua pulls de:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Adicionar idiomas personalizados

Também é possível adicionar idiomas para os quais a Microsoft atualmente não oferece traduções. Será necessário fornecer as traduções para todas as cadeias de caracteres no fluxo de usuário. Códigos de idioma e localidade são limitados a esses no padrão ISO 639-1.

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
2. Clique no fluxo de usuário no qual você quer adicionar idiomas personalizados e, em seguida, clique em **Idiomas**.
3. Selecione **Adicionar idioma personalizado** a partir da parte superior da página.
4. No painel de contexto aberto, identifique o idioma para o qual você está fornecendo traduções, inserindo um código de localidade válido.
5. Para cada página, você poderá baixar um conjunto de substituições para o idioma inglês e trabalhar nas traduções.
6. Após concluir os arquivos JSON, você poderá enviá-los para cada página.
7. Selecione **Habilitar** e o fluxo de usuário agora poderá mostrar esse idioma para seus usuários.
8. Salve o idioma.

>[!IMPORTANT]
>Você precisa habilitar os idiomas personalizados ou carregar substituições para ele antes de salvar.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Rótulos de personalização da interface do usuário da página como substituições

Ao habilitar a personalização de idioma, as edições anteriores para rótulos usando a personalização da interface do usuário da página são persistidas em um arquivo JSON para inglês (en). Você pode continuar a alterar os rótulos e outras cadeias de caracteres, carregando recursos de idiomas na personalização de idioma.

### <a name="up-to-date-translations"></a>Atualizar traduções

A Microsoft está comprometida em fornecer as traduções mais atualizadas para seu uso. A Microsoft aprimora continuamente as traduções e as mantém em conformidade para você. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão diretamente no fluxo de usuário.

### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda

A Microsoft atualmente não fornece suporte para idiomas escritos da direita para a esquerda. Você pode fazer isso usando localidades personalizadas e CSS para alterar a maneira como as cadeias de caracteres são exibidas. Se você precisar desse recurso, vote para ele nos [Comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções de provedor de identidade social

A Microsoft fornece o parâmetro OIDC `ui_locales` para logons sociais. Mas alguns provedores de identidade social, incluindo o Facebook e o Google, não os consideram.

### <a name="browser-behavior"></a>Comportamento do navegador

Ambos Chrome e o Firefox solicitam o idioma definido. Se for um idioma com suporte, será exibido antes do padrão. Atualmente, o Microsoft Edge não solicita um idioma e exibe diretamente o idioma padrão.

## <a name="supported-languages"></a>Idiomas com suporte

O Azure AD B2C inclui suporte para os seguintes idiomas. As linguagens de fluxo de usuário são fornecidas pelo Azure AD B2C. As linguagens de notificação da MFA (autenticação multifator) são fornecidas pelo [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Idioma              | Código de idioma | Fluxos de usuário         | Notificações de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | :x:                | :heavy_check_mark: |
| Búlgaro             | BG            | :x:                | :heavy_check_mark: |
| Bengali                | bn            | :heavy_check_mark: | :x:                |
| Catalão               | AC            | :x:                | :heavy_check_mark: |
| Tcheco                 | cs            | :heavy_check_mark: | :heavy_check_mark: |
| Dinamarquês                | da            | :heavy_check_mark: | :heavy_check_mark: |
| Alemão                | de            | :heavy_check_mark: | :heavy_check_mark: |
| Grego                 | el            | :heavy_check_mark: | :heavy_check_mark: |
| Inglês               | pt-BR            | :heavy_check_mark: | :heavy_check_mark: |
| Espanhol               | es            | :heavy_check_mark: | :heavy_check_mark: |
| Estoniano              | os/390            | :x:                | :heavy_check_mark: |
| basco                | UE            | :x:                | :heavy_check_mark: |
| Finlandês               | fi            | :heavy_check_mark: | :heavy_check_mark: |
| Francês                | fr            | :heavy_check_mark: | :heavy_check_mark: |
| Galego              | GL            | :x:                | :heavy_check_mark: |
| Guzerate              | gu            | :heavy_check_mark: | :x:                |
| Hebraico                | he            | :x:                | :heavy_check_mark: |
| Híndi                 | hi            | :heavy_check_mark: | :heavy_check_mark: |
| Croata              | h            | :heavy_check_mark: | :heavy_check_mark: |
| Húngaro             | hu            | :heavy_check_mark: | :heavy_check_mark: |
| Indonésio            | id            | :x:                | :heavy_check_mark: |
| Italiano               | it            | :heavy_check_mark: | :heavy_check_mark: |
| Japonês              | ja            | :heavy_check_mark: | :heavy_check_mark: |
| Cazaque                | kk            | :x:                | :heavy_check_mark: |
| canarim               | kn            | :heavy_check_mark: | :x:                |
| Coreano                | ko            | :heavy_check_mark: | :heavy_check_mark: |
| Lituano            | lt            | :x:                | :heavy_check_mark: |
| Letão               | LV            | :x:                | :heavy_check_mark: |
| Malaiala             | ml            | :heavy_check_mark: | :x:                |
| Marati               | mr            | :heavy_check_mark: | :x:                |
| Malaio                 | ms            | :heavy_check_mark: | :heavy_check_mark: |
| Norueguês Bokmal      | nb            | :heavy_check_mark: | :x:                |
| Holandês                 | nl            | :heavy_check_mark: | :heavy_check_mark: |
| Norueguês             | não            | :x:                | :heavy_check_mark: |
| Panjabi               | pa            | :heavy_check_mark: | :x:                |
| Polonês                | pl            | :heavy_check_mark: | :heavy_check_mark: |
| Português - Brasil   | pt-br         | :heavy_check_mark: | :heavy_check_mark: |
| Português - Portugal | pt-pt         | :heavy_check_mark: | :heavy_check_mark: |
| Romeno              | ro            | :heavy_check_mark: | :heavy_check_mark: |
| Russo               | ru            | :heavy_check_mark: | :heavy_check_mark: |
| Eslovaco                | sk            | :heavy_check_mark: | :heavy_check_mark: |
| Esloveno             | SL            | :x:                | :heavy_check_mark: |
| Sérvio-cirílico    | Sr-CRYL-cs    | :x:                | :heavy_check_mark: |
| Sérvio-latino       | Sr-LATN-CS    | :x:                | :heavy_check_mark: |
| Sueco               | sv            | :heavy_check_mark: | :heavy_check_mark: |
| Tâmil                 | ta            | :heavy_check_mark: | :x:                |
| Telugu                | te            | :heavy_check_mark: | :x:                |
| Tailandês                  | º            | :heavy_check_mark: | :heavy_check_mark: |
| Turco               | tr            | :heavy_check_mark: | :heavy_check_mark: |
| Ucraniano             | britânico            | :x:                | :heavy_check_mark: |
| Vietnamita            | vi            | :x:                | :heavy_check_mark: |
| Chinês - Simplificado  | zh-hans       | :heavy_check_mark: | :heavy_check_mark: |
| Chinês - Tradicional | zh-hant       | :heavy_check_mark: | :heavy_check_mark: |
