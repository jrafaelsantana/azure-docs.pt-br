---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 055909d51fcd1228e8eb26189ba682e09aee6a1a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390640"
---
As entradas no `inferenceconfig.json` documento são mapeadas para os parâmetros da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . A tabela a seguir descreve o mapeamento entre entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para um arquivo local que contém o código a ser executado para a imagem. |
| `runtime` | `runtime` | Qual tempo de execução usar para a imagem. Os tempos de execução com `spark-py` suporte `python`atuais são e. |
| `condaFile` | `conda_file` | Opcional. Caminho para um arquivo local que contém uma definição de ambiente Conda a ser usada para a imagem. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcional. Caminho para um arquivo local que contém etapas adicionais do Docker para executar ao configurar a imagem. |
| `sourceDirectory` | `source_directory` | Opcional. Caminho para pastas que contêm todos os arquivos para criar a imagem. |
| `enableGpu` | `enable_gpu` | Opcional. Se o suporte à GPU deve ser habilitado na imagem. A imagem GPU deve ser usada em um serviço do Azure, como instâncias de contêiner do Azure, Azure Machine Learning computação, máquinas virtuais do Azure e serviço kubernetes do Azure. O padrão é false. |
| `baseImage` | `base_image` | Opcional. Imagem personalizada a ser usada como uma imagem de base. Se nenhuma imagem base for fornecida, a imagem será baseada no parâmetro de tempo de execução fornecido. |
| `baseImageRegistry` | `base_image_registry` | Opcional. Registro de imagem que contém a imagem base. |
| `cudaVersion` | `cuda_version` | Opcional. Versão do CUDA a ser instalada para imagens que precisam de suporte à GPU. A imagem GPU deve ser usada em um serviço do Azure, como instâncias de contêiner do Azure, Azure Machine Learning computação, máquinas virtuais do Azure e serviço kubernetes do Azure. As versões com suporte são 9,0, 9,1 e 10,0. Se `enable_gpu` for definido, o padrão será 9,1. |
| `description` | `description` | Uma descrição para a imagem. |

O JSON a seguir é uma configuração de inferência de exemplo para uso com a CLI:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```