---
title: Problemas conhecidos com o plug-in de Projeto Acústico
titlesuffix: Azure Cognitive Services
description: Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 37084480423de90f50beced187eda202b39f8bf1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933056"
---
# <a name="project-acoustics-known-issues"></a>Problemas conhecidos do projeto acústicos
Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parâmetros acústicos são perdidos quando você renomeia uma cena

Se você renomear uma cena, todos os parâmetros acústicos que pertencem a essa cena não serão transferidos automaticamente para a nova cena. No entanto, eles ainda existirão no arquivo de ativo antigo. Procure o arquivo **SceneName_AcousticParameters.asset** dentro do diretório **Editor** ao lado do seu arquivo de cena. Renomeie o arquivo para refletir o novo nome da cena.

## <a name="deploying-to-android-from-some-unity-versions"></a>Implantando no Android de algumas versões do Unity

Algumas versões do Unity têm um bug com a implantação de plug-ins de áudio no Android. Verifique se você não está usando uma versão afetada por [esse bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Eu recebo um erro que 'não foi possível encontrar o arquivo de metadados dll'

Assegure-se de que a Versão do Runtime do Script nas configurações do Player esteja definida como **Equivalente ao .NET 4.x** e reinicie o Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Estou tendo problemas de autenticação ao se conectar ao Azure

Verifique se você usou as credenciais corretas para sua conta do Azure, se sua conta oferece suporte ao tipo de nó solicitado no bake e se o relógio do sistema está correto.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Cancelar um bake deixa a guia Bake em estado de "exclusão"
Os acústicos do projeto limparão todos os recursos do Azure para um trabalho após a conclusão ou cancelamento bem-sucedido. Isso pode levar até 5 minutos.

## <a name="next-steps"></a>Próximas etapas
* Experimente o [Unity](unity-quickstart.md) ou o conteúdo de exemplo não [real](unreal-quickstart.md)

