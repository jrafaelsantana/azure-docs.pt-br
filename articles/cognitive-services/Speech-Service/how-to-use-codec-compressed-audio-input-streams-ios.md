---
title: Áudio compactado do codec de fluxo com o SDK de fala no serviço de fala do iOS
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado para os serviços de fala do Azure com o SDK de fala no iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 9a66e4ecf2230caad233a4eff12c0fadc95409d5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803806"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Usando o codec de entrada de áudio compactado com o SDK de fala no iOS

A API de **fluxo de entrada de áudio compactada** do SDK de fala fornece uma maneira de transmitir áudio compactado para o serviço de fala usando um fluxo de recepção ou de envio por push.

> [!IMPORTANT]
> O SDK de fala versão 1.7.0 ou superior é necessário para streaming de áudio compactado no iOS. Também há suporte para [ C++o, C#o e o Java no Linux (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) e [Java no Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

Para wav/PCM, consulte a documentação de fala principal.  Fora de wav/PCM, há suporte para os seguintes formatos de entrada compactados do Codec:

- MP3
- OPUS/OGG
- FLAC
- ALAW no contêiner WAV
- MULAW no contêiner WAV

## <a name="prerequisites"></a>Pré-requisitos

O tratamento de áudio compactado é implementado usando o [GStreamer](https://gstreamer.freedesktop.org).
Por motivos de licenciamento, essas funções não podem ser enviadas com o SDK, mas uma biblioteca de wrapper que contém essas funções precisa ser criada por desenvolvedores de aplicativos e fornecida com os aplicativos usando o SDK.
Para criar essa biblioteca de wrapper, primeiro Baixe e instale o [SDK do GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg).
Em seguida, baixe o projeto Xcode para a [biblioteca de wrapper](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).
Abra o projeto no Xcode e compile-o para o destino do **dispositivo IOS genérico** --ele não funcionará para compilá-lo para um destino específico.
A etapa de compilação irá gerar um pacote de estrutura dinâmica com uma biblioteca dinâmica para todas as arquiteturas necessárias com o nome de `GStreamerWrapper.framework`.
Essa estrutura deve ser incluída em todos os aplicativos que usam fluxos de áudio compactados com o SDK dos serviços de fala.

Aplique as seguintes configurações em seu projeto Xcode para fazer isso:

1. Copie o `GStreamerWrapper.framework` que você acabou de criar e a estrutura do SDK de fala dos serviços cognitivas, que pode ser baixado [aqui](https://aka.ms/csspeech/iosbinary)para o diretório que contém o projeto de exemplo.
1. Ajuste os caminhos para as estruturas nas configurações do *projeto*.
    1. Na guia **Geral** no cabeçalho **Binários Inseridos**, adicione a biblioteca do SDK como uma estrutura: **Adicionar binários inseridos** > **Adicionar outro...** > Navegue até o diretório escolhido e selecione ambas as estruturas.
    1. Vá para a guia **Configurações de Build** e ative **Todas** as configurações.
1. Adicione o diretório `$(SRCROOT)/..` a *Caminhos de Pesquisa do Framework* no cabeçalho **Caminhos de Pesquisa**.

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo usando a entrada de áudio compactado por codec

Para transmitir em um formato de áudio compactado para os serviços de fala, crie um `SPXPullAudioInputStream` ou `SPXPushAudioInputStream`.
O trecho a seguir mostra como criar um `SPXAudioConfiguration` de uma instância de um `SPXPushAudioInputStream`, especificando MP3 como o formato de compactação do fluxo.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

O próximo trecho mostra como os dados de áudio compactados podem ser lidos de um arquivo e bombeados para o `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
