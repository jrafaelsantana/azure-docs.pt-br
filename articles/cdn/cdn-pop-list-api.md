---
title: Recuperar a lista de IP POP atual para a CDN do Azure | Microsoft Docs
description: Saiba como recuperar a lista POP atual.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: f677d6b8edfe60646c6368acce9d47b23a35237d
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146883"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IP POP atual para a CDN do Azure

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IPS POP do Verizon atual para a CDN do Azure

Você pode usar a API REST para recuperar o conjunto de IPs para o ponto da Verizon dos servidores de presença (POP). Esses servidores POP fazem solicitações para servidores de origem que estão associados com os pontos de extremidade do Azure Content Delivery Network (CDN) em um perfil Verizon (**Azure CDN padrão da Verizon** ou **Premium do Azure CDN da Verizon**). Observe que esse conjunto de IPs é diferente dos IPs que um cliente veria ao fazer solicitações aos POPs. 

Para obter a sintaxe da operação da API REST para recuperar a lista POP, consulte [Nós de Borda - Lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IP POP atual da Microsoft para a CDN do Azure

Para bloquear seu aplicativo para aceitar o tráfego somente da CDN do Azure da Microsoft, você precisará configurar ACLs de IP para seu back-end. Você também pode restringir o conjunto de valores aceitos para o cabeçalho ' X-Forwarded-host ' enviado pela CDN do Azure da Microsoft. Essas etapas são detalhadas como a seguir:

Configure o IP atuação para seus back-ends para aceitar o tráfego da CDN do Azure do espaço de endereço IP de back-end da Microsoft e dos serviços de infraestrutura do Azure apenas. 

* CDN do Azure do espaço IP de back-end IPv4 da Microsoft: 147.243.0.0/16
* CDN do Azure do espaço IP de back-end IPv6 da Microsoft: 2a01:111:2050::/44

Os intervalos de IP e as marcas de serviço para os serviços da Microsoft podem ser encontrados [aqui](https://www.microsoft.com/download/details.aspx?id=56519)

Filtrar os valores para o cabeçalho de entrada ' X-Forwarded-host ' enviado pela CDN do Azure da Microsoft. Os únicos valores permitidos para o cabeçalho devem ser todos os hosts de ponto de extremidade, conforme definido em sua configuração de CDN. Na verdade, ainda mais especificamente, apenas os nomes de host para os quais você deseja aceitar o tráfego, nessa origem específica de seu.

## <a name="typical-use-case"></a>Caso de uso típico

Por motivos de segurança, use essa lista de IPs para impor que as solicitações para o seu servidor de origem sejam feitas apenas a partir de um POP válido da Verizon. Por exemplo, se alguém descobrir o nome do host ou o endereço IP do servidor de origem de um ponto de extremidade de CDN, seria possível fazer solicitações diretamente ao servidor de origem, ignorando os recursos de dimensionamento e segurança fornecidos pela CDN do Azure. Ao definir os IPs na lista retornada como os únicos IPs permitidos em um servidor de origem, esse cenário pode ser evitado. Para garantir que você tenha a lista POP mais recente, recupere-a pelo menos uma vez por dia. 

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a API REST, consulte [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
