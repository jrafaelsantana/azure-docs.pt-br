---
title: Adicionar sugestões e AutoCompletar em uma caixa de pesquisa-Azure Search
description: Habilite ações de consulta typeahead em Azure Search criando sugestores e solicitações formular que preencham uma caixa de pesquisa com termos ou frases concluídas.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183280"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Adicionar sugestões ou AutoCompletar ao seu aplicativo Azure Search

Neste artigo, aprenda a usar as [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) e a [AutoCompletar](https://docs.microsoft.com/rest/api/searchservice/autocomplete) para criar uma caixa de pesquisa eficiente que dá suporte a comportamentos de pesquisa conforme o tipo.

+ *Sugestões* são resultados sugeridos gerados à medida que você digita, em que cada sugestão é um único resultado do índice que corresponde ao que você digitou até agora. 

+ *Preenchimento automático* "conclui" a palavra ou frase que um usuário está digitando no momento. Em vez de retornar os resultados, ele conclui uma consulta, que você pode executar para retornar os resultados. Assim como acontece com sugestões, uma palavra ou frase concluída em uma consulta é predicada em uma correspondência no índice. O serviço não oferecerá consultas que retornam zero resultados no índice.

Você pode baixar e executar o código de exemplo em **DotNetHowToAutocomplete** para avaliar esses recursos. O código de exemplo tem como alvo um índice predefinido preenchido com [dados de demonstração do NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). O índice NYCJobs contém uma [construção de sugestão](index-add-suggesters.md), que é um requisito para o uso de sugestões ou de preenchimento automático. Você pode usar o índice preparado hospedado em um serviço de área restrita ou [preencher seu próprio índice](#configure-app) usando um carregador de dados na solução de exemplo NYCJobs. 

O exemplo **DotNetHowToAutocomplete** demonstra sugestões e preenchimento automático, nas versões de C# linguagem JavaScript e. C#os desenvolvedores podem percorrer um aplicativo baseado em ASP.NET MVC que usa o [SDK do .net Azure Search](https://aka.ms/search-sdk). A lógica para fazer chamadas de consulta AutoCompletar e sugeridas pode ser encontrada no arquivo HomeController.cs. Os desenvolvedores de JavaScript encontrarão uma lógica de consulta equivalente em IndexJavaScript. cshtml, que inclui chamadas diretas para a [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

Para as duas versões de idioma, a experiência do usuário de front-end baseia-se na [interface de usuário do jQuery](https://jqueryui.com/autocomplete/) e em bibliotecas [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) . Usamos essas bibliotecas para criar a caixa de pesquisa que oferece suporte a sugestões e preenchimento automático. As entradas coletadas na caixa de pesquisa são emparelhadas com sugestões e ações de preenchimento automático, como as definidas em HomeController.cs ou IndexJavaScript. cshtml.

Este exercício orienta você pelas seguintes tarefas:

> [!div class="checklist"]
> * Implementar uma caixa de entrada de pesquisa em JavaScript e emitir solicitações para correspondências sugeridas ou termos autoconcluídos
> * No C#, defina sugestões e ações de preenchimento automático no HomeController.cs
> * Em JavaScript, chame as APIs REST diretamente para fornecer a mesma funcionalidade

## <a name="prerequisites"></a>Pré-requisitos

Um serviço Azure Search é opcional para este exercício porque a solução usa um serviço de área restrita em tempo real que hospeda um índice de demonstração NYCJobs preparado. Se você quiser executar este exemplo em seu próprio serviço de pesquisa, consulte [configurar trabalhos do NYC index](#configure-app) para obter instruções.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.

* Baixe o [exemplo de DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

O exemplo é abrangente, abrangendo sugestões, preenchimento automático, navegação facetada e cache do lado do cliente. Examine o Leiame e os comentários para obter uma descrição completa do que o exemplo oferece.

## <a name="run-the-sample"></a>Execute o exemplo

1. Abra **AutocompleteTutorial. sln** no Visual Studio. A solução contém um projeto MVC ASP.NET com uma conexão com o índice de demonstração de trabalhos do NYC.

2. Pressione F5 para executar o projeto e carregue a página em seu navegador preferido.

Na parte superior, você verá uma opção para selecionar C# ou JavaScript. A C# opção chama o HomeController do navegador e usa o SDK do .net Azure Search para recuperar os resultados. 

A opção JavaScript chama a API REST do Azure Search diretamente do navegador. Essa opção normalmente tem desempenho claramente melhor, pois ela usa o controlador fora do fluxo. Você pode escolher a opção que atender às suas necessidades e preferências de linguagem. Há vários exemplos de preenchimento automático na página com algumas diretrizes para cada um. Cada exemplo tem um texto de exemplo recomendado que você pode usar para experimentar.  

Tente digitar algumas letras em cada caixa de pesquisa para ver o que acontece.

## <a name="search-box"></a>Caixa de pesquisa

Para as C# versões e JavaScript, a implementação da caixa de pesquisa é exatamente a mesma. 

Abra o arquivo **index. cshtml** na pasta \Views\Home para exibir o código:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Este exemplo é uma caixa de texto de entrada simples com uma classe para estilização, uma ID a ser referenciada por JavaScript e texto de espaço reservado.  A mágica está no JavaScript inserido.

O C# exemplo de linguagem usa JavaScript em index. cshtml para aproveitar a [biblioteca de preenchimento automático da interface do usuário do jQuery](https://jqueryui.com/autocomplete/). Essa biblioteca adiciona a experiência de preenchimento automático à caixa de pesquisa, fazendo chamadas assíncronas para o controlador MVC para recuperar sugestões. A versão da linguagem JavaScript está em IndexJavaScript. cshtml. Ele inclui o script abaixo para a barra de pesquisa, bem como chamadas à API REST para Azure Search.

Vamos examinar o código JavaScript para o primeiro exemplo, que chama a função de preenchimento automático da interface do usuário do jQuery, passando uma solicitação de sugestões:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

O código acima é executado no navegador no carregamento de página para configurar a interface do usuário do jQuery AutoCompletar para a caixa de entrada "example1a".  `minLength: 3` faz com que as recomendações só sejam exibidas quando há pelo menos três caracteres na caixa de pesquisa.  O valor de origem é importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A linha acima informa a função de preenchimento automático da interface do usuário do jQuery onde obter a lista de itens a serem mostrados na caixa de pesquisa. Como esse projeto é um projeto do MVC, ele chama a função de sugestão em HomeController.cs que contém a lógica para retornar sugestões de consulta (mais sobre a sugestão na próxima seção). Essa função também passa alguns parâmetros para os realces de controle, correspondência difusa e termo. A API JavaScript de preenchimento automático adiciona o parâmetro de termo.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Estendendo o exemplo para dar suporte à correspondência difusa

A pesquisa difusa permite que você obtenha resultados com base em correspondências próximas, mesmo se o usuário errou a escrita de uma palavra na caixa de pesquisa. Embora não seja necessário, ele melhora significativamente a robustez de uma experiência de typeahead. Vamos experimentar isso alterando a linha de origem para habilitar a correspondência difusa.

Altere a linha abaixo da seguinte forma:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

para isto:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Inicie o aplicativo pressionando F5.

Tente digitar algo como "execativo" e observe como os resultados retornam para "Executivo", mesmo que não sejam uma correspondência exata para as letras digitadas.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>AutoCompletar do jQuery apoiado por Azure Search preenchimento automático

Até agora, o código UX de pesquisa foi centralizado nas sugestões. O próximo bloco de código mostra a função de preenchimento automático da interface do usuário do jQuery (linha 91 em index. cshtml), passando uma solicitação para Azure Search preenchimento automático:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>Exemplo de C#

Agora que revisamos o código JavaScript para a página da Web, vamos examinar o C# código do controlador do lado do servidor que realmente recupera as correspondências sugeridas usando o SDK do .net Azure Search.

Abra o arquivo **HomeController.cs** no diretório de controladores. 

A primeira coisa que você pode observar é um método na parte superior da classe chamada `InitSearch`. Esse método cria um cliente de índice HTTP autenticado para o serviço de Azure Search. Para obter mais informações, consulte [como usar Azure Search de um aplicativo .net](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na linha 41, observe a função sugerir. Ele se baseia no [método DocumentsOperationsExtensions. sugira](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

A função Suggest utiliza dois parâmetros que determinam se os destaques de ocorrências são retornados ou se correspondência difusa é usada em conjunto com o termo de pesquisa de entrada. O método cria um [objeto sugiraparameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), que é passado para a API de sugestão. O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

Na linha 69, observe a função de preenchimento automático. Ele se baseia no [método DocumentsOperationsExtensions. AutoComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

A função de preenchimento automático usa a entrada do termo de pesquisa. O método cria um [objeto Autocompleteparameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

(Opcional) Adicione um ponto de interrupção no início da função Suggest e explore o código. Observe a resposta retornada pelo SDK e como ela é convertida para o resultado retornado do método.

Os outros exemplos na página seguem o mesmo padrão para adicionar realce de clique e facetas para dar suporte ao cache do lado do cliente dos resultados de preenchimento automático. Examine cada um deles para entender como eles funcionam e como aproveitá-los em sua experiência de pesquisa.

## <a name="javascript-example"></a>Exemplo de JavaScript

Uma implementação de JavaScript de AutoCompletar e sugestões chama a API REST, usando um URI como a origem para especificar o índice e a operação. 

Para examinar a implementação do JavaScript, abra **IndexJavaScript. cshtml**. Observe que a função de preenchimento automático da interface do usuário do jQuery também é usada para a caixa de pesquisa, coletando entradas de termo de pesquisa e fazendo chamadas assíncronas para Azure Search para recuperar correspondências sugeridas ou termos concluídos. 

Vamos examinar o código JavaScript do primeiro exemplo:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Se você comparar este exemplo com o exemplo acima que chama o controlador Home, você observará várias semelhanças.  A configuração de preenchimento automático para `minLength` e `position` são exatamente iguais. 

A alteração significativa aqui está na origem. Em vez de chamar o método sugira no controlador Home, uma solicitação REST é criada em uma função JavaScript e executada usando AJAX. A resposta, em seguida, é processada em "success" e usada como a origem.

As chamadas REST usam URIs para especificar se uma chamada de API de [sugestão](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou de [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) está sendo feita. Os URIs a seguir estão nas linhas 9 e 10, respectivamente.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na linha 148, você pode encontrar um script que chama o `autocompleteUri`. A primeira chamada para `suggestUri` está na linha 39.

> [!Note]
> Fazer chamadas REST para o serviço em JavaScript é oferecido aqui como uma demonstração conveniente da API REST, mas não deve ser interpretada como uma prática recomendada ou recomendação. A inclusão de uma chave de API e um ponto de extremidade em um script abre seu serviço até ataques de negação de serviço a qualquer pessoa que possa ler esses valores fora do script. Embora seja seguro usar o JavaScript para fins de aprendizado, talvez em índices hospedados no serviço gratuito, é recomendável usar C# o Java ou a indexação e as operações de consulta no código de produção. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurar o NYCJobs para ser executado em seu serviço

Até agora, você esteve usando o índice de demonstração do NYCJobs hospedado. Se você quiser ter visibilidade total de todo o código, incluindo o índice, siga estas instruções para criar e carregar o índice em seu próprio serviço de pesquisa.

1. [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este exemplo. 

   > [!Note]
   > Se você estiver usando o serviço Azure Search gratuito, estará limitado a três índices. O carregador de dados NYCJobs cria dois índices. Não se esqueça de deixar um espaço em seu serviço para aceitar os novos índices.

1. Baixe o código de exemplo [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) .

1. Na pasta datauploader do código de exemplo NYCJobs, abra **DataLoader. sln** no Visual Studio.

1. Adicione as informações de conexão para seu serviço de Azure Search. Abra o App.config dentro do projeto DataLoader e altere o appSettings TargetSearchServiceName e TargetSearchServiceApiKey para refletir seu serviço Azure Search e a chave de API do serviço Azure Search. Essas informações podem ser encontradas no portal do Azure.

1. Pressione F5 para iniciar o aplicativo, criando dois índices e importando os dados de exemplo do NYCJob.

1. Abra **AutocompleteTutorial. sln** e edite o Web. config no projeto **AutocompleteTutorial** . Altere os valores de SearchServiceName e SearchServiceApiKey para valores que são válidos para o serviço de pesquisa.

1. Pressione F5 para executar o aplicativo. O aplicativo Web de exemplo é aberto no navegador padrão. A experiência é idêntica à versão da área restrita, somente o índice e os dados são hospedados em seu serviço.

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstra as etapas básicas para criar uma caixa de pesquisa que dá suporte a preenchimento automático e sugestões. Você viu como criar um aplicativo MVC ASP.NET e usar o SDK do .NET Azure Search ou a API REST para recuperar sugestões.

Como uma próxima etapa, tentar integrar sugestões e executar AutoCompletar em sua experiência de pesquisa. Os artigos de referência a seguir devem ajudar.

> [!div class="nextstepaction"]
> [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atributo de índice de facetas em uma API REST de criação de índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

