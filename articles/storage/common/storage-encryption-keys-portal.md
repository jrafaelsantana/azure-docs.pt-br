---
title: Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure do portal do Azure
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 892cbe66222626a6847ad7a5b6c990d23991c182
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002261"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure do portal do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o [portal do Azure](https://portal.azure.com/). Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: Defina e recupere um segredo de Azure Key Vault usando o portal do Azure](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure exige que o cofre de chaves tenha duas propriedades obrigatórias configuradas, **exclusão reversível** e **não limpar**. Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal do Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, deverá usar o PowerShell ou CLI do Azure.
> Somente chaves RSA e tamanho de chave 2048 têm suporte.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento.
1. Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Selecione a opção **Usar sua própria chave**, conforme a imagem a seguir.

    ![Captura de tela do portal mostrando a opção de criptografia](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave a ser associada à conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI de chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **chaves** . Selecione a chave desejada e clique na chave para exibir suas configurações. Copie o valor do campo **identificador de chave** , que fornece o URI.

    ![Captura de tela mostrando o URI da chave do Key Vault](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Nas configurações de **criptografia** para sua conta de armazenamento, escolha a opção **inserir URI de chave** .
1. No campo **Chave URI**, especifique o URI.

   ![Captura de tela mostrando como inserir o URI da chave](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro verifique se você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
2. Escolha o Cofre de chaves que contém a chave que deseja usar.
3. Escolha a chave do Cofre de chaves.

   ![Captura de tela mostrando a opção de chave gerenciada pelo cliente](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao criar uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Especifique o URI para a nova versão de chave. Como alternativa, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
