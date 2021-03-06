---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67172285"
---
| Recurso | Limite padrão |
| --- | :--- |
| Máximo de clusters por assinatura | 100 |
| Máximo de nós por cluster | 100 |
| Máximo de pods por nó: [Sistema de rede básico][basic-networking] com Kubenet | 110 |
| Máximo de pods por nó: [Rede avançada][advanced-networking] com a interface de rede de contêiner do Azure | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo de Azure Resource Manager: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup> Quando você implanta um cluster do AKS (serviço kubernetes do Azure) com o CLI do Azure ou um modelo do Resource Manager, esse valor é configurado até 250 pods por nó. Não é possível configurar o pods máximo por nó depois que você já tiver implantado um cluster AKS ou se você implantar um cluster usando o portal do Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
