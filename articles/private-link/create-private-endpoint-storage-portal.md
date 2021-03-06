---
title: Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
description: Saiba como conectar-se de forma privada a uma conta de armazenamento no Azure usando um ponto de extremidade privado.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: bb0c6e9d20c12df3532a52df1fe4d9574344d4b3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104727"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.

Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, uma conta de armazenamento com um ponto de extremidade privado usando o portal do Azure. Em seguida, você pode acessar com segurança a conta de armazenamento da VM.


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma máquina virtual
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (uma conta de armazenamento neste exemplo).

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar o recurso de link privado.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.
1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Assinatura | Selecione sua assinatura.|
    | Grupo de recursos | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Location | Selecione **WestCentralUS**.|
    | Sub-rede – Nome | Insira *mysubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
1. Deixe o restante com os valores padrão e selecione **Criar**.


### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**.

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Assinatura | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **WestCentralUS**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Image | Selecione **Windows Server 2019 datacenter**. |
    | Size | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirme a senha | Reinsira a senha. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |
    |||

1. Selecione **Avançar: Discos**.

1. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **avançar: Rede**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**padrão.  |
    | Espaço de endereço | Deixe o **10.1.0.0/24**padrão.|
    | Subnet | Deixe o padrão **mysubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="create-your-private-endpoint"></a>Criar seu ponto de extremidade privado
Nesta seção, você criará uma conta de armazenamento particular usando um ponto de extremidade privado para ele. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **armazenamento** > **conta de armazenamento**.

1. Em **criar conta de armazenamento-noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Assinatura | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da conta de armazenamento  | Insira *mystorageaccount*. Se esse nome já estiver sendo usado, crie um nome exclusivo. |
    | Região | Selecione **WestCentralUS**. |
    | Desempenho| Deixe o padrão **Standard**. |
    | Tipo de conta | Deixe o **armazenamento padrão (uso geral v2)** . |
    | Replicação | Selecione **armazenamento com redundância geográfica com acesso de leitura (ra-grs)** . |
    |||
  
3. Selecione **avançar: Rede**.
4. Em **criar uma conta de armazenamento –** método de conectividade, rede, selecione **ponto de extremidade privado**.
5. Em **criar uma conta de armazenamento – rede**, selecione **Adicionar ponto de extremidade privado**. 
6. Em **criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Assinatura | Selecione sua assinatura. |
    | Grupo de recursos | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    |Location|Selecione **WestCentralUS**.|
    |Nome|Insira *myPrivateEndpoint*.  |
    |Subrecurso de armazenamento|Deixe o **blob**padrão. |
    | **REDE** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *MyResource*Group. |
    | Subnet | Selecione *mysubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO**|  |
    | Integrar com a zona DNS privada  | Deixe o padrão **Sim**. |
    | Zona de DNS privado  | Deixe o padrão  ** (novo) privatelink.blob.Core.Windows.net**. |
    |||
7. Selecione **OK**. 
8. Selecione **revisão + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 
10. Navegue até o recurso de conta de armazenamento que você acabou de criar.
11. Selecione **chaves de acesso** no menu conteúdo à esquerda.
12. Selecione **copiar** na cadeia de conexão para key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte maneira:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo. rdp * baixado.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  

## <a name="access-storage-account-privately-from-the-vm"></a>Acessar a conta de armazenamento de forma privada da VM

Nesta seção, você se conectará de forma privada à conta de armazenamento usando o ponto de extremidade privado.

> [!IMPORTANT]
> A configuração de DNS para armazenamento precisa de uma modificação manual no arquivo de hosts para incluir o FQDN da conta específica, modifique o seguinte arquivo usando permissões de administrador no Windows: c:\Windows\System32\Drivers\etc\hosts ou Linux/etc/hosts Inclua as informações de DNS para a conta da etapa anterior no seguinte formato [endereço IP privado] myaccount.blob.core.windows.net

1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.
2. Digite `nslookup mystorageaccount.blob.core.windows.net` você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
