---
title: Disponibilidade do cenário-serviço de fala
titleSuffix: Azure Cognitive Services
description: Referência para regiões do serviço de Fala.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: af5bb7126af65a755cb1d58788d39cb8bdcbbb3b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959035"
---
# <a name="scenario-availability"></a>Disponibilidade do cenário

O SDK do serviço de fala apresenta muitos cenários em uma ampla variedade de linguagens de programação e ambientes.  Nem todos os cenários estão atualmente disponíveis em todas as linguagens de programação ou todos os ambientes ainda.  Listada abaixo está a disponibilidade de cada cenário.

- **Reconhecimento de fala (SR), lista de frases, intenção, tradução e contêineres locais**
  - Todas as linguagens de programação/ambientes em que há um link de seta <img src="media/index/link.jpg" height="15" width="15"></img> na tabela de início rápido [aqui](https://aka.ms/csspeech).
- **Conversão de texto em fala (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE e Android)
  - Python
  - Swift
  - Objective-C
  - A API REST de TTS pode ser usada em todas as outras situações.
- **Ativar palavra (palavra-chave spotter/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)
  - A funcionalidade wake Word (keyword spotter/KWS) pode funcionar com qualquer tipo de microfone, o suporte oficial de KWS, no entanto, está limitado atualmente às matrizes de microfone encontradas no hardware do Azure Kinect DK ou no SDK de dispositivos de fala
- **Assistente virtual de voz-primeiro**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (SDK de dispositivos de fala)
- **Transcrição de Conversas**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)
- **Transcrição do Call Center**
  - API REST e pode ser usada em qualquer situação
- **Entrada de áudio compactada por codec**
  - C++/Linux
  - C#/Linux
  - Java/Linux, Android e iOS
