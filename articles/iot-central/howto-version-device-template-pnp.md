---
title: Noções básicas sobre o controle de versão de modelo de dispositivo para aplicativos Azure IoT Central | Microsoft Docs
description: Iterar modelos de dispositivo criando novas versões e sem afetar os dispositivos conectados em tempo real
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ac2af605859d9555cc1c165afac4ca5e7ba343cc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879650"
---
# <a name="create-a-new-device-template-version-preview-features"></a>Criar uma nova versão de modelo de dispositivo (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

A Microsoft IoT Central permite o rápido desenvolvimento de Aplicativos IoT. Você pode iterar rapidamente seus designs de modelo de dispositivo adicionando, editando ou excluindo recursos, exibições e personalizações de dispositivo. Depois de publicar o modelo de dispositivo, o modelo de funcionalidade do dispositivo será mostrado como **publicado** com ícones de bloqueio ao lado do modelo. Para fazer alterações no modelo de capacidade do dispositivo, você precisará criar uma nova versão do modelo de dispositivo. Enquanto isso, as propriedades, personalizações e exibições de nuvem podem ser editadas a qualquer momento sem a necessidade de versão do modelo de dispositivo. Depois de salvar qualquer uma dessas alterações, você pode publicar o modelo de dispositivo para disponibilizar as alterações mais recentes para o operador exibir em Device Explorer.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo [, consulte Configurar e gerenciar um modelo de dispositivo](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Adicionar personalizações ao modelo de dispositivo sem controle de versão

Determinados elementos de seus recursos de dispositivo podem ser editados sem a necessidade de versão do modelo e das interfaces do dispositivo. Por exemplo, alguns desses campos incluem nome de exibição, tipo semântico, valor mínimo, valor máximo, casas decimais, cor, unidade, unidade de exibição, comentário e descrição. Para adicionar uma dessas personalizações:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você deseja personalizar.
1. Escolha a guia **Personalizar** .
1. Todos os recursos definidos no modelo de funcionalidade do dispositivo serão listados aqui. Todos os campos que você pode editar aqui podem ser salvos e usados em seu aplicativo, sem a necessidade de versão do modelo de dispositivo. Se houver campos que você deseja editar que sejam somente leitura, você precisará criar sua versão do modelo de dispositivo para alterá-los. Selecione um campo que você deseja editar e insira um novo valor.
1. Clique em **Salvar**. Agora, esses valores substituirão tudo o que foi salvo inicialmente em seu modelo de dispositivo e será usado em todo o aplicativo.

## <a name="versioning-a-device-template"></a>Controle de versão de um modelo de dispositivo

A criação de uma nova versão do modelo de dispositivo criará uma versão de rascunho do modelo em que o modelo de capacidade do dispositivo pode ser editado. Todas as interfaces publicadas permanecerão publicadas até que sejam com controle de versão individual. Para modificar uma interface publicada, você deve primeiro criar uma nova versão de modelo de dispositivo.

O modelo de dispositivo só deve ter controle de versão quando você está tentando editar uma parte do modelo de funcionalidade do dispositivo que não pode ser editado na seção personalizações do modelo de dispositivo. 

Para obter uma versão de um modelo de dispositivo:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você está tentando executar na versão.
1. Clique no botão **versão** na parte superior da página e dê um novo nome ao modelo. Sugerimos um novo nome para você, que pode ser editado.
1. Clique em **Criar**.
1. Agora seu modelo de dispositivo está no modo de rascunho. Você verá que suas interfaces ainda estão bloqueadas e devem ter a versão individual para serem editadas. 

### <a name="versioning-an-interface"></a>Controle de versão de uma interface

O controle de versão de uma interface permite que você adicione, atualize e remova os recursos dentro da interface que você já criou. 

Para a versão de uma interface:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você tem em um modo de rascunho.
1. Selecione a interface que está no modo publicado que você deseja para a versão e edição.
1. Clique no botão **versão** na parte superior da página da interface. 
1. Clique em **Criar**.
1. Agora sua interface está no modo de rascunho. Você poderá adicionar ou editar recursos à sua interface sem interromper as exibições e visualizações existentes. 

> [!NOTE]
> As interfaces padrão publicadas pelo Azure IoT não podem ter controle de versão ou edição. Essas interfaces padrão são usadas para certificação de dispositivo.

> [!NOTE]
> Depois que a interface tiver sido publicada, você não poderá excluir nenhum dos recursos dele mesmo em um modo de rascunho. Os recursos só podem ser editados ou adicionados à interface no modo de rascunho.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrar um dispositivo nas versões de modelos de dispositivos

É possível criar várias versões do modelo de dispositivo. Com o tempo, você terá vários dispositivos conectados usando esses modelos de dispositivo. Você pode migrar dispositivos de uma versão do modelo de dispositivo para outra. As etapas a seguir descrevem como migrar um dispositivo:

1. Vá para a página de **Device Explorer** .
1. Selecione o dispositivo que você precisa migrar para outra versão.
1. Escolha **migrar**.
1. Selecione o modelo de dispositivo com o número de versão para o qual você deseja migraro dispositivo e escolha migrar.

![Como migrar um dispositivo](media/howto-version-device-template-pnp/pick-version.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar as versões de modelos de dispositivo no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
