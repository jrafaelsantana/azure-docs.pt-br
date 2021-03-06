---
title: 'Tutorial: Vozes habilitar o bot usando o SDK de fala'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um bot de eco usando o Microsoft bot-Framework, o implantará no Azure e o registrará com o canal de fala de linha direta do bot-Framework. Em seguida, você configurará um aplicativo cliente de exemplo para o Windows que lhe permite falar com o bot e ouvi-lo responderá a você.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: dcohen
ms.openlocfilehash: 3c57200591f3b7de9a1f9ab4198e55ed844b4d07
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932035"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Tutorial: Ativar voz para o bot usando o SDK de fala

Agora você pode usar o poder dos serviços de fala para habilitar com facilidade um bot de chat.

Neste tutorial, você criará um bot de eco usando o Microsoft bot-Framework, o implantará no Azure e o registrará com o canal de fala de linha direta do bot-Framework. Em seguida, você configurará um aplicativo cliente de exemplo para o Windows que lhe permite falar com o bot e ouvi-lo responderá a você.

Este tutorial destina-se a desenvolvedores que estão apenas começando sua jornada com o Azure, bots-estrutura de bot, fala de linha direta ou o SDK de fala, e querem criar rapidamente um sistema funcional com codificação limitada. Nenhuma experiência ou familiaridade com esses serviços é necessária.

No final deste exercício, você terá configurado um sistema que funcionará da seguinte maneira:

1. O aplicativo cliente de exemplo está configurado para se conectar ao canal de fala de linha direta e ao bot de eco
2. O áudio é registrado no microfone padrão no botão de pressionamento (ou registrado continuamente se a palavra de ativação personalizada for ativada)
3. Opcionalmente, a detecção personalizada de palavras de ativação acontece, a retenção de streaming de áudio para a nuvem
4. Usando o SDK de fala, o aplicativo se conecta ao canal de fala de linha direta e transmite áudio
5. Opcionalmente, a verificação da palavra de ativação de precisão mais alta ocorre no serviço
6. O áudio é passado para o serviço de reconhecimento de fala e transcrita para texto
7. O texto reconhecido é passado para o Echo-bot como uma atividade de estrutura de bot 
8. O texto de resposta é transformado em áudio pelo serviço de conversão de texto em fala (TTS) e transmitido de volta para o aplicativo cliente para reprodução

![diagrama-marca](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "O fluxo do canal de fala")

> [!NOTE]
> As etapas neste tutorial não exigem um serviço pago. Como um novo usuário do Azure, você poderá usar créditos de sua assinatura do Azure Trail gratuita e a camada gratuita de serviços de fala para concluir este tutorial.

Este tutorial abrange:
> [!div class="checklist"]
> * Criar novos recursos do Azure
> * Criar, testar e implantar o exemplo de bot de eco em um serviço de Azure App
> * Registrar seu bot com canal de fala de linha direta
> * Compilar e executar o cliente de fala de linha direta para interagir com o bot de eco
> * Adicionar ativação personalizada de palavra de ativação
> * Saiba como alterar o idioma da fala reconhecida e falada

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você precisará para concluir este tutorial:

- Um PC com Windows 10 com um microfone de trabalho e alto-falantes (ou fones de ouvido)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou superior
- [SDK do .NET Core](https://dotnet.microsoft.com/download) versão 2,1 ou posterior
- Uma conta do Azure. [Inscreva-se gratuitamente](https://azure.microsoft.com/free/ai/).
- Uma conta do [GitHub](https://github.com/)
- [Git para Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O aplicativo cliente que você criará neste tutorial usa alguns serviços do Azure. Para reduzir o tempo de ida e volta das respostas do bot, você desejará certificar-se de que esses serviços estejam localizados na mesma região do Azure. Nesta seção, você criará um grupo de recursos na região **oeste dos EUA** . Esse grupo de recursos será usado ao criar recursos individuais para o bot-Framework, o canal de fala de linha direta e os serviços de fala.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, selecione **grupos de recursos**. Em seguida, clique em **Adicionar** para adicionar um novo grupo de recursos.
3. Você será solicitado a fornecer algumas informações:
   * Defina a **assinatura** para **avaliação gratuita** (você também pode usar uma assinatura existente).
   * Insira um nome para seu **grupo de recursos**. É recomendável **SpeechEchoBotTutorial-resourcegroup**.
   * Na lista suspensa **região** , selecione **oeste dos EUA**.
4. Clique em revisar **e criar**. Você deve ver uma faixa que a **validação**de leitura passou.
5. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.
6. Assim como nos recursos que você criará posteriormente neste tutorial, é uma boa ideia fixar esse grupo de recursos em seu painel para facilitar o acesso. Se você quiser fixar esse grupo de recursos, clique no ícone de pino no canto superior direito do painel.

### <a name="choosing-an-azure-region"></a>Escolhendo uma região do Azure

Se você quiser usar uma região diferente para este tutorial, esses fatores podem limitar suas opções:

* O canal de fala de linha direta é um serviço de visualização. Como tal, ele pode ser limitado a regiões específicas do Azure. Para obter mais informações sobre regiões disponíveis, consulte [assistentes virtuais da primeira voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#voice-first-virtual-assistants).
* O canal de fala de linha direta usa o serviço de conversão de texto em fala, que tem vozes padrão e neural. As vozes neurais são [limitadas a regiões específicas do Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
* As chaves de avaliação gratuitas podem ser restritas a uma região específica.

Para obter mais informações sobre regiões, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Criar recursos

Agora que você tem um grupo de recursos na região **oeste dos EUA** , a próxima etapa é criar recursos individuais para cada serviço que você usará neste tutorial.

### <a name="create-a-speech-services-resource"></a>Criar um recurso de serviços de fala

Siga estas instruções para criar um recurso de fala:

1. Selecione **criar um recurso** no painel de navegação esquerdo.
2. Na barra de pesquisa, digite **fala**.
3. Selecione **fala**e clique em **criar**.
4. Você será solicitado a fornecer algumas informações:
   * Dê um **nome**ao seu recurso. Recomendamos **SpeechEchoBotTutorial-Speech**
   * Para **assinatura**, verifique se a **avaliação gratuita** está selecionada.
   * Para **local**, selecione **oeste dos EUA**.
   * Para **tipo de preço**, selecione **F0**. Essa é a camada gratuita.
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**.
5. Depois de inserir todas as informações necessárias, clique em **criar**. Pode levar alguns minutos para criar seu recurso.
6. Posteriormente neste tutorial, você precisará de chaves de assinatura para esse serviço. Você pode acessar essas chaves a qualquer momento da **visão geral** do recurso (gerenciar chaves) ou **chaves**.

Neste ponto, verifique se seu grupo de recursos (**SpeechEchoBotTutorial-resourcegroup**) tem um recurso de fala:

| NOME | TIPO  | LOCAL |
|------|-------|----------|
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | Oeste dos EUA |

### <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

A próxima etapa é criar um plano do serviço de aplicativo. Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo Web ser executado.

1. Selecione **criar um recurso** no painel de navegação esquerdo.
2. Na barra de pesquisa, digite **plano do serviço de aplicativo**. Em seguida, localize e selecione o cartão do **plano do serviço de aplicativo** nos resultados da pesquisa.
3. Clique em **Criar**.
4. Você será solicitado a fornecer algumas informações:
   * Defina a **assinatura** para **avaliação gratuita** (você também pode usar uma assinatura existente).
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**.
   * Dê um **nome**ao seu recurso. Recomendamos **SpeechEchoBotTutorial-AppServicePlan**
   * Para **sistema operacional**, selecione **Windows**.
   * Para **região**, selecione **oeste dos EUA**.
   * Para **tipo de preço**, verifique se **padrão S1** está selecionado. Esse deve ser o valor padrão.
5. Clique em revisar **e criar**. Você deve ver uma faixa que a **validação**de leitura passou.
6. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.

Neste ponto, verifique se seu grupo de recursos (**SpeechEchoBotTutorial-resourcegroup**) tem dois recursos:

| NOME | TIPO  | LOCAL |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | Oeste dos EUA |

## <a name="build-an-echo-bot"></a>Criar um bot de eco

Agora que você criou alguns recursos, vamos criar um bot. Vamos começar com o exemplo de bot de eco, que como o nome indica, ecoa o texto que você inseriu como resposta. Não se preocupe, o código de exemplo está pronto para uso sem nenhuma alteração. Ele está configurado para trabalhar com o canal de fala de linha direta, que vamos conectar depois de implantarmos o bot no Azure.

> [!NOTE]
> As instruções a seguir, bem como informações adicionais sobre o bot de eco, estão disponíveis no [Leiame do exemplo no GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="download-and-run-the-sample"></a>Baixar e executar o exemplo

1. Clone o repositório de exemplos:
   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```
2. Inicie o Visual Studio.
3. Na barra de ferramentas, selecione **arquivo** > **abrir** > **projeto/solução**e abra o arquivo de projeto do bot de eco que foi configurado para uso com canal de fala de linha direta:
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
4. Depois que o projeto for carregado, `F5` Pressione para executar o projeto.

### <a name="test-with-the-bot-framework-emulator"></a>Testar com o emulador do bot Framework

O [emulador do bot Framework](https://github.com/microsoft/botframework-emulator) é um aplicativo de desktop que permite que os desenvolvedores de bot testem e depurem seus bots localmente ou remotamente por meio de um túnel. Siga estas etapas para usar o emulador do bot Framework para testar seu bot de eco.

1. Instalar o [emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versão 4.3.0 ou superior
2. Inicie o emulador do bot Framework e abra o bot:
   *  -> **Bot de abertura**de arquivo.
3. Insira a URL para o bot. Por exemplo:
   ```
   http://localhost:3978/api/messages
   ```
4. Use a interface do usuário para se comunicar com o bot usando texto digitado. Confirme que você obtém uma resposta.


## <a name="deploy-your-bot-to-an-azure-app-service"></a>Implantar o bot em um serviço de Azure App

A próxima etapa é implantar o Echo bot no Azure. Há algumas maneiras de implantar um bot, mas neste tutorial vamos nos concentrar na publicação diretamente do Visual Studio.

> [!NOTE]
> Como alternativa, você pode implantar um bot usando os modelos de [CLI do Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) e de [implantação](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. No Visual Studio, abra o bot de eco que foi configurado para uso com canal de fala de linha direta:
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
2. Na **Gerenciador de soluções**, clique com o botão direito do mouse na solução **EchoBot** e selecione **publicar...**
3. Uma nova janela intitulada **escolher um destino de publicação** será aberta.
3. Selecione **serviço de aplicativo** no painel de navegação esquerdo, selecione **criar novo**e clique em **publicar**.
5. Quando a janela **Criar serviço de aplicativo** for exibida:
   * Clique em **Adicionar uma conta**e entre com suas credenciais de conta do Azure. Se você já estiver conectado, selecione a conta que deseja na lista suspensa.
   * Para o **nome do aplicativo**, você precisará inserir um nome globalmente exclusivo para o bot. Esse nome é usado para criar uma URL de bot exclusiva. Um valor padrão será populado, incluindo a data e a hora (por exemplo: "EchoBot20190805125647"). Você pode usar o nome padrão para este tutorial.
   * Para **assinatura**, defina-a como **avaliação gratuita**
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**
   * Para o **plano de hospedagem**, selecione **SpeechEchoBotTutorial-AppServicePlan**
6. Clique em **Criar**
7. Você deverá ver uma mensagem de êxito no Visual Studio parecida com esta:
   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```
8. O navegador padrão deve abrir e exibir uma página que lê: "O bot está pronto!".
9. Neste ponto, verifique o grupo de recursos **SpeechEchoBotTutorial-resourcegroup** no portal do Azure e confirme se há três recursos:

| NOME | TIPO  | LOCAL |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-AppServicePlan | plano de Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | Oeste dos EUA |

## <a name="enable-web-sockets"></a>Habilitar soquetes Web

Você precisará fazer uma pequena alteração de configuração para que o bot possa se comunicar com o canal de fala de linha direta usando os soquetes da Web. Siga estas etapas para habilitar o Web Sockets:

1. Navegue até a [portal do Azure](https://portal.azure.com)e localize seu serviço de aplicativo. O recurso deve ser nomeado semelhante a **EchoBot20190805125647** (seu nome de aplicativo exclusivo).
2. No painel de navegação esquerdo, selecione **configurações**e clique em **configuração**.
3. Selecione a guia **configurações gerais** .
4. Localize a alternância para **Web Sockets** e defina-a como **ativado**.
5. Clique em **Salvar**.

> [!TIP]
> Você pode usar os controles na parte superior da página Azure App serviço para parar ou reiniciar o serviço. Isso pode ser útil ao solucionar problemas.

## <a name="create-a-channel-registration"></a>Criar um registro de canal

Agora que você criou um serviço de Azure App para hospedar o bot, a próxima etapa é criar um **registro de canais de bot**. A criação de um registro de canal é um pré-requisito para registrar seu bot com canais de bot-Framework, incluindo canal de fala de linha direta.

> [!NOTE]
> Se você quiser saber mais sobre como os bots utilizam canais, consulte [conectar um bot a canais](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

1. A primeira etapa é criar um novo recurso para o registro. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.
2. No **bot**do tipo de barra de pesquisa, depois que os resultados forem exibidos, selecione o **registro de canais de bot**.
3. Clique em **Criar**.
4. Você será solicitado a fornecer algumas informações:
   * Para **nome do bot**, digite **SpeechEchoBotTutorial-BotRegistration**.
   * Para **assinatura**, selecione **avaliação gratuita**.
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**.
   * Para **local**, selecione **oeste dos EUA**.
     * Para **tipo de preço**, selecione **F0**.
     * Para **ponto de extremidade do sistema de mensagens**, insira a URL para `/api/messages` seu aplicativo Web com o caminho anexado no final. Por exemplo: se o nome do aplicativo exclusivo globalmente era **EchoBot20190805125647**, seu ponto de extremidade de `https://EchoBot20190805125647.azurewebsites.net/api/messages/`mensagens seria:.
     * Para o **Application**insights, você pode definir isso como **desativado**. Para obter mais informações, consulte [bot Analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignorar **criação automática de ID do aplicativo e senha**.
5. Navegue de volta para o **registro de canais de bot** e clique em **criar**.

Neste ponto, verifique o grupo de recursos **SpeechEchoBotTutorial-resourcegroup** no portal do Azure. Agora, ele deve mostrar quatro recursos:

| NOME | TIPO  | LOCAL |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-AppServicePlan | plano de Serviço de Aplicativo | Oeste dos EUA |
| SpeechEchoBotTutorial-BotRegistration | Registro de canais de bot | Global |
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | Oeste dos EUA |

> [!IMPORTANT]
> O recurso de registro de canais de bot mostrará a região global, embora você tenha selecionado oeste dos EUA. Isso é esperado.

## <a name="register-the-direct-line-speech-channel"></a>Registrar o canal de fala de linha direta

Agora é hora de registrar seu bot com o canal de fala de linha direta. Esse canal é o que é usado para criar uma conexão entre o bot de eco e um aplicativo cliente compilado com o SDK de fala.

1. Localize e abra o recurso **SpeechEchoBotTutorial-BotRegistration** no [portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, selecione **canais**.
   * Procure **mais canais**, localize e clique em **Direct line Speech**.
   * Examine o texto na página intitulado **Configurar Direct line Speech (versão prévia)** e, em seguida, clique em **salvar**.
   * Como parte da criação, duas **chaves secretas** foram geradas. Essas chaves são exclusivas do bot. Quando você escreve um aplicativo cliente usando o [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/), você fornecerá uma dessas chaves para estabelecer uma conexão entre o aplicativo cliente, o canal de fala de linha direta e o serviço de bot. Neste tutorial, você usará o cliente Direct line Speech (WPF, C#).
   * Clique em **Mostrar** e copie uma das chaves em algum lugar para poder acessá-la facilmente. Não se preocupe, você sempre pode acessar as chaves do portal do Azure.
3. No painel de navegação esquerdo, clique em **configurações**.
   * Marque a caixa rotulada **habilitar ponto de extremidade de streaming**. Isso é necessário para habilitar um protocolo de comunicação criado em soquetes da Web entre o bot e o canal de fala de linha direta.
   * Clique em **Salvar**.

> [!TIP]
> Se você quiser saber mais, consulte [conectar um bot à Direct line Speech (versão prévia)](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Esta página inclui informações adicionais e problemas conhecidos.

## <a name="build-the-direct-line-speech-client"></a>Criar o cliente de fala de linha direta

Nesta etapa, você vai criar o cliente Direct line Speech. O cliente é um aplicativo Windows Presentation Foundation (WPF) no C# que usa o [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) para gerenciar a comunicação com o bot usando o canal de fala de linha direta. Use-o para interagir com e testar o bot antes de gravar um aplicativo cliente personalizado.

O cliente Direct line Speech tem uma interface do usuário simples que permite configurar a conexão com o bot, exibir a conversa de texto, exibir as atividades de bot-Framework no formato JSON e exibir cartões adaptáveis. Ele também dá suporte ao uso de palavras de ativação personalizadas. Você usará esse cliente para falar com o bot e receber uma resposta de voz.

Antes de continuarmos, verifique se o microfone e os alto-falantes estão habilitados e funcionando.

1. Navegue até o repositório GitHub para o [cliente Direct line Speech](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md).
2. Siga as instruções fornecidas para clonar o repositório, compilar o projeto, configurar o cliente e iniciar o cliente.
3. Clique em reconectar e verifique se você vê a mensagem **pressione o botão MIC ou digite para começar a conversar com o bot**.
4. Vamos testá-lo. Clique no botão do microfone e fale com algumas palavras em inglês. O texto reconhecido será exibido à medida que você falar. Quando você terminar de falar, o bot responderá em sua própria voz, dizendo "Echo" seguido das palavras reconhecidas.
5. Você também pode usar texto para se comunicar com o bot. Basta digitar o texto na barra inferior. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Solucionando problemas de erros no cliente Direct line Speech

Se você receber uma mensagem de erro na janela do aplicativo principal, use esta tabela para identificar e solucionar o erro:

| Erro | O que você deve fazer? |
|-------|----------------------|
|Erro de aplicativo (consulte o log para obter detalhes): Microsoft. Cognitivaservices. Speech. CSharp: O valor não pode ser nulo. Nome do parâmetro: speechConfig | Este é um erro de aplicativo cliente. Verifique se você tem um valor não vazio para o *segredo de bot* na janela principal do aplicativo (consulte [a seção registrar seu bot com canal de fala de linha direta](#register-the-direct-line-speech-channel)) |
|Erro AuthenticationFailure: Falha na atualização do WebSocket com um erro de autenticação (401). Verificar a chave de assinatura (ou o token de autorização) correta e o nome da região| Na página Configurações do aplicativo, verifique se você inseriu a chave de assinatura de fala e sua região corretamente.<br>Verifique se o segredo do bot foi digitado corretamente. |
|Erro ConnectionFailure: A conexão foi fechada pelo host remoto. Código de Erro: 1011. Detalhes do erro: Não foi possível conectar ao bot antes de enviar uma mensagem | Verifique se você [marcou a caixa "Habilitar ponto de extremidade de streaming"](#register-the-direct-line-speech-channel) e/ou os [soquetes **da Web** alternados](#enable-web-sockets) para ativado.<br>Verifique se o serviço de Azure App está em execução. Se for, tente reiniciar o serviço de aplicativo.|
|Erro ConnectionFailure: A conexão foi fechada pelo host remoto. Código de Erro: 1011. Detalhes do erro: O código de status de resposta não indica êxito: 500 (InternalServerError)| O bot especificou uma voz neural em seu campo [ler](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) atividade de saída, mas a região do Azure associada à sua chave de assinatura de fala não oferece suporte a vozes neurais. Consulte [vozes padrão e neural](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Erro ConnectionFailure: A conexão foi fechada pelo host remoto. Código de Erro: 1.000. Detalhes do erro: Tempo ocioso máximo de conexão de soquete da Web excedido (> 300000 MS)| Esse é um erro esperado quando uma conexão com o canal é deixada aberta e inativa por mais de cinco minutos. |

Se o problema não for resolvido na tabela, consulte [visualização de assistentes virtuais da primeira voz: Perguntas frequentes](https://docs.microsoft.com/azure/cognitive-services/speech-service/faq-voice-first-virtual-assistants).

### <a name="view-bot-activities"></a>Exibir atividades de bot

Cada bot envia e recebe mensagens de **atividade** . Na janela **log de atividades** do cliente Direct line Speech, você verá os logs com carimbo de data/hora com cada atividade que o cliente recebeu do bot. Você também pode ver as atividades que o cliente enviou para o bot usando o [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) método. Quando você seleciona um item de log, ele mostrará os detalhes da atividade associada como JSON.

Aqui está um exemplo de JSON de uma atividade recebida pelo cliente:
```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Para saber mais sobre o que é retornado na saída JSON, consulte [campos na atividade](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Para fins deste tutorial, você pode se concentrar nos campos de [texto](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e de [fala](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) .

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Exibir o código-fonte do cliente para chamadas para o SDK de fala

O cliente Direct line Speech usa o pacote NuGet [Microsoft. cognitivaservices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), que contém o SDK de fala. Um bom lugar para começar a revisar o código de exemplo é o método InitSpeechConnector [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)() no arquivo, que cria esses dois objetos SDK de fala:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)-Para definições de configuração (chave de assinatura de fala, região de chave, segredo de bot)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)-Para gerenciar a conexão de canal e os eventos de assinatura de cliente para tratamento de respostas reconhecidas de fala e bot.

## <a name="add-custom-wake-word-activation"></a>Adicionar ativação personalizada de palavra de ativação

O SDK de fala dá suporte à ativação de palavra de ativação personalizada. Semelhante a "Ei Cortana" para o assistente da Microsoft, você pode escrever um aplicativo que ouvirá continuamente uma palavra de ativação de sua escolha. Tenha em mente que uma palavra de ativação pode ser uma palavra única ou uma frase com várias palavras.

> [!NOTE]
> O termo o *trabalho de ativação* geralmente é usado de forma intercambiável com a *palavra-chave*, e você pode ver ambos usados na documentação da Microsoft.

A detecção de palavra de ativação é feita no aplicativo cliente. Se estiver usando uma palavra de ativação, o áudio será transmitido somente para o canal de fala de linha direta se a palavra de ativação for detectada. O canal de fala de linha direta inclui um componente chamado *KWV (verificação de palavra-chave)* , que faz processamento mais complexo na nuvem para verificar se a palavra de ativação escolhida está no início do fluxo de áudio. Se a verificação da palavra-chave for realizada com sucesso, o canal se comunicará com o bot.

Siga estas etapas para criar um modelo de ativação do Word, configurar o cliente de fala de linha direta para usar esse modelo e, por fim, testá-lo com o bot.

1. Siga estas instruções para [criar uma palavra de ativação personalizada usando o serviço de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Descompacte o arquivo de modelo que você baixou na etapa anterior. Ele deve ser nomeado para sua palavra de ativação. Você está procurando um arquivo chamado `kws.table`.
3. No cliente Direct line Speech, localize o menu **configurações** (procure o ícone de engrenagem no canto superior direito). Localize o **caminho do arquivo de modelo** e insira o nome do `kws.table` caminho completo para o arquivo da etapa 2.
4. Certifique-se de marcar a caixa denominada **habilitado**. Você deverá ver essa mensagem ao lado da caixa de seleção: "Escutará a palavra de ativação após a próxima conexão". Se você tiver fornecido o arquivo errado ou um caminho inválido, verá uma mensagem de erro.
5. Insira sua **chave de assinatura**de fala, região de **chave de assinatura**e clique em **OK** para fechar o menu **configurações** .
6. Selecione um **segredo de bot**e cliqueem reconectar. Você deverá ver uma mensagem que lê: "Nova conversa iniciada-tipo, pressione o botão de microfone ou diga a palavra de ativação". Agora, o aplicativo está ouvindo continuamente.
7. Fale com qualquer frase que comece com sua palavra de ativação. Por exemplo: " **{sua palavra de ativação}** , qual é a hora?". Você não precisa fazer uma pausa após a desativação da palavra de ativação. Quando terminar, duas coisas acontecem:
   * Você verá uma transcrição do que falou
   * Logo após, você ouvirá a resposta do bot
8. Continue experimentando os três tipos de entrada aos quais o bot dá suporte:
   * Texto digitado na barra inferior
   * Pressionando o ícone de microfone e falando
   * Dizendo uma frase que começa com sua palavra de ativação

### <a name="view-the-source-code-that-enables-wake-word"></a>Exibir o código-fonte que habilita a palavra de ativação

No código-fonte do cliente de linha direta, dê uma olhada nesses arquivos para examinar o código usado para habilitar a detecção de palavras de ativação:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs)inclui uma chamada para o método [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/en-us/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)SDK de fala, que é usado para instanciar o modelo de um arquivo local no disco.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)inclui uma chamada para o método [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)SDK de fala, que ativa a detecção contínua de palavras de ativação.

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>Adicional Alterar o idioma e reimplantar o bot

O bot que você criou irá escutar e responder em inglês. No entanto, você não está limitado a usar o inglês. Nesta seção, você aprenderá a alterar o idioma que o bot escutará e responderá e reimplantará o bot.

### <a name="change-the-language"></a>Alterar o idioma

1. Vamos começar abrindo `\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs`.
2. Em seguida, localize o SSML. É fácil de encontrar, pois está entre `<speak></speak>` as marcas.
3. Na cadeia de caracteres SSML, localize `<voice name>` a marca, substitua- `<voice name='de-DE-Stefan-Apollo'>`a por e salve. Essa cadeia de caracteres formatada diz ao serviço de conversão de texto em fala para retornar uma resposta de fala sintetizada usando a voz `de-DE-Stefan-Apollo`, que é otimizada para alemão.

>[!NOTE]
> Você não está limitado a alemão e pode escolher na lista de vozes disponíveis no serviço de [fala](language-support.md#text-to-speech).

### <a name="redeploy-your-bot"></a>Reimplantar o bot

Agora que você fez a alteração necessária no bot, a próxima etapa é republicá-la em seu serviço de Azure App e experimentá-la:

1. Crie sua solução no Visual Studio e corrija quaisquer erros de compilação.
2. Na janela Gerenciador de Soluções, clique com o botão direito do mouse no projeto **EchoBot** e selecione **publicar**.
3. Sua configuração de implantação anterior já foi carregada como padrão. Basta clicar em **publicar** ao lado de **EchoBot20190805125647-implantação da Web**.
4. A mensagem **publicar com êxito** aparecerá na janela saída do Visual Studio e uma página da Web será iniciada com a mensagem "o bot está pronto!".
5. Abra o aplicativo cliente Direct line Speech, clique no botão Configurações (ícone de engrenagem superior direito) e insira `de-de` no campo idioma. Isso define o idioma falado a ser reconhecido, substituindo o `en-us`padrão.
6. Siga as instruções em [criar o cliente de fala de linha direta](#build-the-direct-line-speech-client) para se reconectar com o bot implantado recentemente, fale na nova linguagem e ouça a resposta do bot nesse idioma com a nova voz.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando o Echo-bot implantado neste tutorial, poderá removê-lo e todos os seus recursos do Azure associados simplesmente excluindo o grupo de recursos do Azure **SpeechEchoBotTutorial-resourcegroup**.

1. No [portal do Azure](https://portal.azure.com), clique em **grupos de recursos** no painel de navegação esquerdo.
2. Localize o grupo de recursos denominado: **SpeechEchoBotTutorial-resourcegroup**. Clique nos três pontos (...).
3. Selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crie seu próprio aplicativo cliente com o SDK de fala](quickstart-virtual-assistant-csharp-uwp.md)

## <a name="see-also"></a>Consulte também

* Implantando em uma [região do Azure perto de você](https://azure.microsoft.com/global-infrastructure/locations/) para ver o aperfeiçoamento do tempo de resposta do bot
* Implantando em uma [região do Azure que dá suporte a vozes de TTS de alta qualidade](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Preço associado ao canal de fala de linha direta:
  * [Preços do Serviço de Bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Serviços de Fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Criando e implantando seu próprio bot habilitado para voz:
    * Crie um [bot de estrutura de bot](https://dev.botframework.com/). Registre-o com o [canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) e [Personalize seu bot para voz](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
    * Explore as [soluções de bot-Framework](https://github.com/microsoft/botframework-solutions)existentes: Criar um [assistente virtual de primeiro voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-first-virtual-assistants) e um [habilitador de voz](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md) personalizados
