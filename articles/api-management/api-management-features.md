---
title: Comparação baseada em recursos dos níveis de gerenciamento de API do Azure | Microsoft Docs
description: Este artigo compara os níveis de gerenciamento de API com base nos recursos que eles oferecem.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: bc5eab6bb5044e474c386d3e5771d8517e6ba9cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072524"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos dos níveis de gerenciamento de API do Azure

>[!IMPORTANT]
> Observe que a camada de desenvolvedor é para casos de uso e avaliações de não produção. Ele não oferece SLA. 

Cada [tipo de preço](https://aka.ms/apimpricing) de Gerenciamento de API oferece um conjunto distinto de recursos e por unidade de [capacidade](api-management-capacity.md). A tabela a seguir resume os principais recursos disponíveis em cada um dos níveis. Alguns recursos podem funcionar de maneira diferente ou ter recursos diferentes dependendo da camada. Em tais casos, as diferenças são mencionadas nos artigos de documentação que descrevem esses recursos individuais.

| Recurso                                                                                      | Consumo | Desenvolvedor      | Básico          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integração do Azure AD<sup>1</sup>                                                             | Não                            | Sim            | Não             | Sim            | Sim            |
| Suporte de rede virtual (VNet)                                                               | Não                            | Sim            | Não             | Não             | Sim            |
| Implantação em várias regiões                                                                      | Não                            | Não             | Não             | Não             | Sim            |
| Vários nomes de domínio personalizado                                                                 | Não                            | Não             | Não             | Não             | Sim            |
| Portal do desenvolvedor<sup>2</sup>                                                                 | Não                            | Sim            | Sim            | Sim            | Sim            |
| Cache embutido                                                                               | Não                            | Sim            | Sim            | Sim            | Sim            |
| Análise integrada                                                                           | Não                            | Sim            | Sim            | Sim            | Sim            |
| [Configurações de SSL](api-management-howto-manage-protocols-ciphers.md)                             | Sim                            | Sim            | Sim            | Sim            | Sim            |
| [Cache externo](https://aka.ms/apimbyoc)                                                    | Sim                           | Sim            | Sim            | Sim            | Sim            |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Sim                | Sim            | Sim            | Sim            | Sim            |
| [Backup e restauração](api-management-howto-disaster-recovery-backup-restore.md)               | Não                            | Sim            | Sim            | Sim            | Sim            |
| [Gerenciamento via Git](api-management-configuration-repository-git.md)                        | Não                            | Sim            | Sim            | Sim            | Sim            |
| API de Gerenciamento                                                                        | Não                            | Sim            | Sim            | Sim            | Sim            |
| Métricas e logs do Azure Monitor                                                               | Não                | Sim            | Sim            | Sim            | Sim            |
| IP Estático                                                               | Não                | Sim            | Sim            | Sim            | Sim            |

<sup>1</sup> habilita o uso do Azure AD (e Azure ad B2C) como um provedor de identidade para entrada do usuário no portal do desenvolvedor.<br/>
<sup>2</sup> Incluindo funcionalidade relacionada, por ex. usuários, grupos, problemas, aplicativos e modelos de email e notificações.<br/>
