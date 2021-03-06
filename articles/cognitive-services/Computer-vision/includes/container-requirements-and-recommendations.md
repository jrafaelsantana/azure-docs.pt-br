---
title: Recomendações e requisitos do contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129814"
---
> [!NOTE]
> Os requisitos e as recomendações baseiam-se em benchmarks com uma única solicitação por segundo, usando uma imagem de 8 MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres.

#### <a name="readtabread"></a>[Ler](#tab/read)

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de leitura.

| Contêiner | Mínimo | Recomendado |TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
| Leitura | 1 núcleos, 8 GB de memória, 0,24 TPS | 8 núcleos, 16 GB de memória, 1,17 TPS | 0,24, 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Reconhecimento de Texto](#tab/recognize-text)

A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de Reconhecimento de Texto.

| Contêiner | Mínimo | Recomendado |TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
| Reconhecimento de Texto | 1 núcleo, 8 GB de memória, 0,12 TPS | 8 núcleos, 16 GB de memória, 0,60 TPS | 0,12, 0,60 |

***

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS-transações por segundo.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.
