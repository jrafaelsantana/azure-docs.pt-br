---
title: Transferir dados de ou para arquivos do Azure usando AzCopy v10 | Microsoft Docs
description: Transferir dados com o AzCopy e o armazenamento de arquivos.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648769"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferir dados com o AzCopy e o armazenamento de arquivos 

AzCopy é um utilitário de linha de comando que você pode usar para copiar BLOBs ou arquivos de ou para uma conta de armazenamento. Este artigo contém comandos de exemplo que funcionam com os arquivos do Azure.

Antes de começar, consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e se familiarizar com a ferramenta.

## <a name="create-file-shares"></a>Criar compartilhamentos de arquivos

Você pode usar o comando `make` AzCopy para criar um compartilhamento de arquivos. O exemplo nesta seção cria um compartilhamento de arquivos chamado `myfileshare`.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Exemplo** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Carregar arquivos

Você pode usar o comando `copy` AzCopy para carregar arquivos e diretórios do seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um arquivo
> * Carregar um diretório
> * Carregar arquivos usando caracteres curinga

> [!NOTE]
> AzCopy não calcula e armazena automaticamente o código hash MD5 do arquivo. Se você quiser que o AzCopy faça isso, anexe o `--put-md5` sinalizador a cada comando de cópia. Dessa forma, quando o arquivo for baixado, AzCopy calculará um hash MD5 para dados baixados e verificará se o hash MD5 armazenado na Propriedade do `Content-md5` arquivo corresponde ao hash calculado.

### <a name="upload-a-file"></a>Carregar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Exemplo** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Carregar um diretório

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um compartilhamento de arquivos. O resultado é um diretório no compartilhamento de arquivos com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Para copiar para um diretório dentro do compartilhamento de arquivos, basta especificar o nome desse diretório na cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Se você especificar o nome de um diretório que não existe no compartilhamento de arquivos, o AzCopy criará um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Carregar o conteúdo de um diretório

Você pode carregar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Exemplo** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para carregar arquivos em todos os subdiretórios.

## <a name="download-files"></a>Baixar arquivos

Você pode usar o comando `copy` AzCopy para baixar arquivos, diretórios e compartilhamentos de arquivos em seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Baixar um arquivo
> * Baixar um diretório
> * Baixar arquivos usando caracteres curinga

> [!NOTE]
> Se o `Content-md5` valor da propriedade de um arquivo contiver um hash, AzCopy calculará um hash MD5 para os dados baixados e verificará se o hash MD5 armazenado `Content-md5` na Propriedade do arquivo corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará, a menos que você `--check-md5=NoCheck` substitua `--check-md5=LogOnly` esse comportamento acrescentando ou ao comando de cópia.

### <a name="download-a-file"></a>Baixar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Baixar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Este exemplo resulta em um diretório chamado `C:\myDirectory\myFileShareDirectory` que contém todos os arquivos baixados.

### <a name="download-the-contents-of-a-directory"></a>Baixar o conteúdo de um diretório

Você pode baixar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para baixar arquivos em todos os subdiretórios.

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com o AzCopy e o Armazenamento de Blobs](storage-use-azcopy-blobs.md)

- [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)