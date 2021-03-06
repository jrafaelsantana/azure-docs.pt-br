---
title: Conectar redes locais ao Microsoft Cloud usando o Alcance Global – Azure ExpressRoute | Microsoft Docs
description: Este artigo explica o que é o Alcance Global do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: e522c7ea306667f675af4bbe7486e25fb7f4ec9f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122797"
---
# <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
O ExpressRoute é uma maneira particular e resiliente de conectar suas redes locais ao Microsoft Cloud. Você pode acessar muitos serviços de nuvem da Microsoft, como o Azure, e o Office 365 de seu data center particular ou sua rede corporativa. Por exemplo, é possível ter uma filial em São Francisco com um circuito ExpressRoute no Vale do Silício e outra filial em Londres com um circuito ExpressRoute na mesma cidade. Ambas as filiais podem ter conectividade de alta velocidade aos recursos do Azure no Oeste dos EUA e no Sul do Reino Unido. No entanto, as filiais não podem trocar dados diretamente entre si. Em outras palavras, 10.0.1.0/24 pode enviar dados para 10.0.3.0/24 e 10.0.4.0/24, mas NÃO para 10.0.2.0/24.

![sem][1]

Com o **Alcance Global do ExpressRoute**, é possível vincular circuitos do ExpressRoute em conjunto para criar uma rede privada entre as redes locais. No exemplo acima, com a adição do Alcance Global do ExpressRoute, a filial de São Francisco (10.0.1.0/24) pode trocar dados diretamente com a filial de Londres (10.0.2.0/24) por meio dos circuitos do ExpressRoute existentes e da rede global da Microsoft. 

![com][2]

## <a name="use-case"></a>Caso de uso
O Alcance Global do ExpressRoute foi desenvolvido para complementar a implementação de WAN do provedor de serviços e conectar as filiais ao redor do mundo. Por exemplo, se o provedor de serviços opera principalmente nos Estados Unidos e vinculou todas as filiais nos EUA, mas o provedor de serviços não opera no Japão e em Hong Kong, com o Alcance Global do ExpressRoute é possível trabalhar com um provedor de serviços local e a Microsoft conectará as filiais àquelas dos EUA usando o ExpressRoute e nossa rede global.

![caso de uso][3]

## <a name="availability"></a>Disponibilidade 
Atualmente, o Alcance Global do ExpressRoute tem suporte nos seguintes locais.

* Austrália
* Canadá
* França
* Alemanha
* Região Administrativa Especial de Hong Kong
* Irlanda
* Japão
* Coreia do Sul
* Países Baixos
* Cingapura
* Suíça
* Reino Unido
* Estados Unidos

Os circuitos do ExpressRoute devem ser criados nos [locais de emparelhamento do ExpressRoute](expressroute-locations.md) nos países ou região acima. Para habilitar o Alcance Global do ExpressRoute entre [diferentes regiões geopolíticas](expressroute-locations.md), seus circuitos devem ter o SKU Premium.

## <a name="next-steps"></a>Próximas etapas
1. [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-faqs.md)
2. [Como habilitar o Alcance Global do ExpressRoute](expressroute-howto-set-global-reach.md)
3. [Vincular circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagrama sem alcance global"
[2]: ./media/expressroute-global-reach/2.png "diagrama com alcance global"
[3]: ./media/expressroute-global-reach/3.png "caso de uso do alcance global"
