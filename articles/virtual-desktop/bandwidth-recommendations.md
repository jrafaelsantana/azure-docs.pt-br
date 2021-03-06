---
title: Recomendações de largura de banda para sessões remotas – Azure
description: Recomendações de largura de banda disponíveis para sessões remotas.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: helohr
ms.openlocfilehash: 8352858b9fd6059257b5ba7637822f85c56eb070
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802620"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Recomendações de largura de banda para sessões remotas

Ao usar uma sessão remota do Windows, a largura de banda disponível da sua rede afeta muito a qualidade da sua experiência. Diferentes aplicativos e resoluções de exibição exigem configurações de rede diferentes, portanto, é importante verificar se a rede está configurada para atender às suas necessidades.

>[!NOTE]
>As recomendações a seguir se aplicam a redes com menos de 0,1% de perda.

## <a name="applications"></a>Aplicativos

A tabela a seguir lista as larguras de banda mínimas recomendadas para uma experiência de usuário tranqüila. 

|Carga de Trabalho        |Aplicativos de exemplo                                                                                           |Largura de banda recomendada|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Profissional de tarefas     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1,5 @ no__t-0Mbps        |
|Office Worker   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, visualizador de fotos                                        |3 @ no__t-0Mbps          |
|Trabalhador de conhecimento|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, visualizador de fotos, Java                                  |5 @ no__t-0Mbps          |
|Power Worker    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, visualizador de fotos, Java, CAD/CAM, ilustração/publicação|15 @ no__t-0Mbps         |

>[!NOTE]
>Essas recomendações se aplicam independentemente de quantos usuários estão na sessão.

Tenha em mente que o estresse colocado em sua rede depende da taxa de quadros de saída da carga de trabalho do aplicativo e da sua resolução de vídeo. Se a taxa de quadros ou a resolução de vídeo aumentar, o requisito de largura de banda também aumentará. Por exemplo, uma carga de trabalho leve com uma exibição de alta resolução requer mais largura de banda disponível do que uma carga de trabalho leve com resolução regular ou baixa.

Outros cenários podem ter seus requisitos de largura de banda alterados, dependendo de como você os usa, como:

- Conferência de voz ou vídeo
- Comunicação em tempo real
- Vídeo de 4K em streaming

Certifique-se de carregar o teste desses cenários em sua implantação usando ferramentas de simulação como o VSI de logon. Varie o tamanho da carga, execute testes de estresse e teste cenários de usuário comuns em sessões remotas para entender melhor os requisitos da sua rede. 

## <a name="display-resolutions"></a>Resoluções de vídeo

Resoluções de vídeo diferentes exigem diferentes larguras de banda disponíveis. A tabela a seguir lista as larguras de banda que recomendamos para uma experiência de usuário tranqüila em resoluções de vídeo típicas com uma taxa de quadros de 30 quadros por segundo (FPS). Essas recomendações se aplicam a cenários de usuário único e múltiplo. Tenha em mente que os cenários que envolvem uma taxa de quadros inferior a 30 fps, como a leitura de texto estático, exigem menos largura de banda disponível. 

|Resoluções de vídeo típicas a 30 fps    |Largura de banda recomendada|
|-----------------------------------------|---------------------|
|Cerca de 1024 × 768 px                      |1,5 Mbps             |
|Cerca de 1280 × 720 px                      |3 Mbps               |
|Cerca de 1920 × 1080 px                     |5 Mbps               |
|Cerca de 3840 × 2160 px (4K)                |15 Mbps              |

>[!NOTE]
>Essas recomendações se aplicam independentemente de quantos usuários estão na sessão.

## <a name="additional-resources"></a>Recursos adicionais

A região do Azure em que você está pode afetar a experiência do usuário tanto quanto as condições de rede. Confira o [estimador de experiência da área de trabalho virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) para saber mais.
