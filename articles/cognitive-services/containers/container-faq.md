---
title: Perguntas frequentes sobre os contêineres de serviços cognitivas
titleSuffix: Azure Cognitive Services
description: Perguntas e respostas frequentes.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 6e218f33bdc33708cef0c94eb85298abf2b8927c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316617"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Perguntas frequentes sobre os contêineres de serviços cognitivas do Azure

## <a name="general-questions"></a>Perguntas gerais

**P: O que está disponível?**

**R:** O [suporte a contêineres nos serviços cognitivas do Azure](../cognitive-services-container-support.md) permite que os desenvolvedores usem as mesmas APIs inteligentes disponíveis no Azure, mas com os [benefícios](../cognitive-services-container-support.md#features-and-benefits) da Containerização. Atualmente, o suporte a contêiner está disponível em versão prévia para um subconjunto de serviços cognitivas do Azure, incluindo partes do:

> [!div class="checklist"]
> * [Detector de anomalias][ad-containers]
> * [Pesquisa Visual Computacional][cv-containers]
> * [Detecção Facial][fa-containers]
> * [Reconhecedor de formulário][fr-containers]
> * [Reconhecimento vocal (LUIS)][lu-containers]
> * [API do Serviço de Fala][sp-containers]
> * [Análise de Texto][ta-containers]
<!-- > * [Translator Text][tt-containers] -->

**P: Há alguma diferença entre a nuvem de serviços cognitivas e os contêineres?**

**R:** Os contêineres de serviços cognitivas são uma alternativa à nuvem de serviços cognitivas. Os contêineres oferecem os mesmos recursos que os serviços de nuvem correspondentes. Os clientes podem implantar os contêineres no local ou no Azure. A tecnologia principal de ia, as camadas de preço, as chaves de API e a assinatura de API são as mesmas entre o contêiner e os serviços de nuvem correspondentes. Aqui estão os [recursos e os benefícios](../cognitive-services-container-support.md#features-and-benefits) para escolher contêineres sobre seu equivalente de serviço de nuvem.

**P: Os contêineres estarão disponíveis para todos os serviços cognitivas e qual será o próximo conjunto de contêineres que devemos esperar?**

**R:** Gostaríamos de disponibilizar serviços mais cognitivas como ofertas de contêiner. Entre em contato com seu Gerenciador de conta Microsoft local para obter atualizações sobre novas versões de contêiner e outros anúncios de serviços cognitivas.

**P: Qual será o SLA (contrato de nível de serviço) para os contêineres de serviços cognitivas?**

**R:** Os contêineres de serviços cognitivas não têm um SLA.

As configurações de contêiner de serviços cognitivas de recursos são controladas pelos clientes, portanto, a Microsoft não oferecerá um SLA para GA (disponibilidade geral). Os clientes são livres para implantar contêineres no local, portanto, eles definem os ambientes de host.

> [!IMPORTANT]
> Para saber mais sobre os contratos de nível de serviço de serviços cognitivas, [visite nossa página de SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**P: Esses contêineres estão disponíveis em nuvens soberanass?**

**R:** Nem todos estão familiarizados com o termo "nuvem soberanas", portanto, vamos começar com a definição:

> A "nuvem soberanas" consiste nas nuvens [Azure governamental](../../azure-government/documentation-government-welcome.md), [Azure Alemanha](../../germany/germany-welcome.md)e [Azure China 21vianet](https://docs.microsoft.com/azure/china/overview-operations) .

Infelizmente, os contêineres de serviços cognitivas *não* têm suporte nativo nas nuvens soberanas. Os contêineres podem ser executados nessas nuvens, mas eles serão extraídos da nuvem pública e precisarão enviar dados de uso para o ponto de extremidade público.

### <a name="versioning"></a>Controle de versão

**P: Como os contêineres são atualizados para a versão mais recente?**

**R:** Os clientes podem escolher quando atualizar os contêineres implantados. Os contêineres serão marcados com [marcas](https://docs.docker.com/engine/reference/commandline/tag/) padrão do Docker `latest` , como, para indicar a versão mais recente. Incentivamos os clientes a efetuar pull da versão mais recente dos contêineres à medida que eles são lançados, fazer check-in [do registro de contêiner do Azure](../../container-registry/container-registry-webhook.md) para obter detalhes sobre como ser notificado quando uma imagem for atualizada.
 
**P: Quais versões terão suporte?**

**R:** Haverá suporte para a versão atual e última principal do contêiner. No entanto, incentivamos os clientes a se manterem atualizados para obter a tecnologia mais recente.
 
**P: Como as atualizações são atualizadas?**

**R:** As alterações de versão principais indicam que há uma alteração significativa na assinatura da API. Prevemos que isso geralmente coincidirá com as principais alterações de versão para a oferta de nuvem de serviço cognitiva correspondente. Alterações de versão secundárias indicam correções de bug, atualizações de modelo ou novos recursos que não fazem uma alteração significativa na assinatura de API.

## <a name="technical-questions"></a>Perguntas técnicas

**P: Como devo executar os contêineres de serviços cognitivas em dispositivos IoT?**

Se você não tem uma conexão com a Internet confiável ou deseja economizar no custo de largura de banda. Ou, se tiver requisitos de baixa latência ou se estiver lidando com dados confidenciais que precisam ser analisados no local, [Azure IOT Edge com os contêineres de serviços cognitivas](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) proporcionam consistência com a nuvem.

**P: Como fazer fornecer comentários sobre o produto e recomendações de recursos?**

**R:** Os clientes são incentivados a [faturar suas preocupações](https://cognitive.uservoice.com/) publicamente e a outras pessoas que fizeram o mesmo onde os problemas potenciais se sobrepõem. A ferramenta de voz do usuário pode ser usada para comentários de produtos e recomendações de recursos.

**P: Com quem devo contatar para obter suporte?**

**R:** Os canais de atendimento ao cliente são os mesmos da oferta de nuvem de serviços cognitivas. Todos os contêineres de serviços cognitivas incluem recursos de registro em log que nos ajudarão e os clientes de suporte da Comunidade. Para obter suporte adicional, consulte as opções a seguir.

### <a name="customer-support-plan"></a>Plano de atendimento ao cliente

Os clientes devem consultar o [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) para ver quem contatar para obter suporte.

### <a name="azure-knowledge-center"></a>Centro de conhecimento do Azure

O cliente tem a liberdade de explorar a [central de conhecimento do Azure](https://azure.microsoft.com/resources/knowledge-center/) para responder a perguntas e problemas de suporte.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) é um site de perguntas e respostas para programadores profissionais e entusiastas.

Explore as seguintes marcas para obter possíveis perguntas e respostas que se alinhem às suas necessidades.

* [Serviços Cognitivos do Azure](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Cognitiva da Microsoft](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**P: Como funciona a cobrança?**

**R:** Os clientes são cobrados com base no consumo, de forma semelhante à nuvem de serviços cognitivas. Os contêineres precisam ser configurados para enviar dados de medição para o Azure e as transações serão cobradas de acordo. Os recursos usados nos serviços hospedados e locais serão adicionados a uma única cota com preços em camadas, contando com ambos os usos. Para obter mais detalhes, consulte a página de preços da oferta correspondente.

* [Detector de anomalias][ad-containers-billing]
* [Pesquisa Visual Computacional][cv-containers-billing]
* [Detecção Facial][fa-containers-billing]
* [Reconhecedor de formulário][fr-containers-billing]
* [Reconhecimento vocal (LUIS)][lu-containers-billing]
* [API do Serviço de Fala][sp-containers-billing]
* [Análise de Texto][ta-containers-billing]
<!-- * [Translator Text][tt-containers-billing] -->

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam dados do cliente para a Microsoft.
 
**P: Qual é a garantia de suporte atual para contêineres?**

**R:** Não há garantia para visualizações. A garantia padrão da Microsoft para o software empresarial será aplicada quando os contêineres forem formalmente anunciados como disponibilidade geral (GA).
 
**P: O que acontece com os contêineres de serviços cognitivas quando a conectividade com a Internet é perdida?**

**R:** Os contêineres de serviços cognitivas *não são licenciados* para serem executados sem serem conectados ao Azure para medição. Os clientes precisam habilitar os contêineres para se comunicarem com o serviço de medição em todos os momentos.

**P: Por quanto tempo o contêiner pode operar sem estar conectado ao Azure?**

**R:** Os contêineres de serviços cognitivas *não são licenciados* para serem executados sem serem conectados ao Azure para medição. Os clientes precisam habilitar os contêineres para se comunicarem com o serviço de medição em todos os momentos.
 
**P: O que é o hardware atual necessário para executar esses contêineres?**

**R:** Os contêineres de serviços cognitivas são contêineres baseados em x64 que podem executar qualquer nó do Linux compatível, VM e dispositivo de borda que dá suporte a contêineres do Docker do Linux x64. Todos eles exigem processadores de CPU. As configurações mínimas e recomendadas para cada oferta de contêiner estão disponíveis abaixo:

* [Detector de anomalias][ad-containers-recommendations]
* [Pesquisa Visual Computacional][cv-containers-recommendations]
* [Detecção Facial][fa-containers-recommendations]
* [Reconhecedor de formulário][fr-containers-recommendations]
* [Reconhecimento vocal (LUIS)][lu-containers-recommendations]
* [API do Serviço de Fala][sp-containers-recommendations]
* [Análise de Texto][ta-containers-recommendations]
<!-- * [Translator Text][tt-containers-recommendations] -->
 
**P: Esses contêineres têm suporte no momento no Windows?**

**R:** Os contêineres de serviços cognitivas são contêineres do Linux, no entanto, há algum suporte para contêineres do Linux no Windows. Para obter mais informações sobre contêineres do Linux no Windows, consulte a [documentação do Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**P: Como fazer descobrir os contêineres?**

**R:** Os contêineres de serviços cognitivas estão disponíveis em vários locais, como o portal do Azure, o Hub do Docker e os registros de contêiner do Azure. Para os locais de contêiner mais recentes, consulte [repositórios e imagens de contêiner](../cognitive-services-container-support.md#container-repositories-and-images).

**P: Como os contêineres de serviços cognitivas se comparam às ofertas do AWS e do Google?**

**R:** A Microsoft é o primeiro provedor de nuvem a mover seus modelos de ia pré-treinados em contêineres com cobrança simples por transação, como se os clientes estivessem usando um serviço de nuvem. A Microsoft acredita que uma nuvem híbrida oferece aos clientes mais opções.

**P: Quais certificações de conformidade têm contêineres?**

**R:** Os contêineres de serviços cognitivas não têm nenhuma certificação de conformidade

**P: Em quais regiões os contêineres de serviços cognitivas estão disponíveis?**

**R:** Os contêineres podem ser executados em qualquer lugar em qualquer região, no entanto, precisam de uma chave e para retornar ao Azure para medição. Todas as regiões com suporte para o serviço de nuvem têm suporte para a chamada de medição de contêineres.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md
<!-- [tt-containers]: ../translator/how-to-install-containers.md -->

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing
<!-- [tt-containers-billing]: ../translator/how-to-install-containers.md#billing -->

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
<!-- [tt-containers-recommendations]: ../translator/how-to-install-containers.md#container-requirements-and-recommendations -->
