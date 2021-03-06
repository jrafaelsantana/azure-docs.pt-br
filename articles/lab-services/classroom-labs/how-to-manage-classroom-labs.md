---
title: Gerenciar laboratórios de salas de aula no Azure Lab Services | Microsoft Docs
description: Saiba como criar e configurar um laboratório de sala de aula, exibir todos os laboratórios de sala de aula, compartilhar o link de registro com um usuário de laboratório, ou excluir um laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873585"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerenciar laboratórios de sala de aula no Azure Lab Services 
Este artigo descreve como criar e excluir um laboratório de sala de aula. Isso também mostra como exibir todos os laboratórios de sala de aula em uma conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente a essa função. Um proprietário de laboratório pode adicionar outros usuários à função Criador de Laboratório usando as etapas do seguinte artigo: [Adicionar um usuário à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). Observe que ainda não há suporte para o Internet Explorer 11. 
2. Selecione **Entrar**. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório. 
    2. Especifique o **número máximo de máquinas virtuais** no laboratório. Posteriormente, você poderá aumentar ou diminuir o número de máquinas virtuais no laboratório. 
    6. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, siga estas etapas:
    1. Selecione um **tamanho** para as VMs (máquinas virtuais) criadas no laboratório. No momento, os tamanhos **pequeno**, **médio**, **médio (virtualização)** , **grande** e **GPU** são permitidos. Para obter detalhes, consulte a seção [tamanhos de VM](#vm-sizes) .
    1. Selecione a **região** na qual você deseja criar as VMs. 
    1. Selecione a **imagem da VM** a ser usada para criar as VMs no laboratório. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, veja [Habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    1. Selecione **Avançar**.

        ![Definir as especificações da VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na página **Definir credenciais**, especifique as credenciais padrão para todas as VMs no laboratório. 
    1. Especifique o **nome do usuário** para todas as VMs no laboratório.
    2. Especifique a **senha** do usuário. 

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** se desejar que os alunos definam suas próprias senhas. Esta etapa é **opcional**. 

        Um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do Windows e do Linux, exceto para Ubuntu. Quando você seleciona VM **Ubuntu** , essa configuração é desabilitada, portanto, os alunos serão solicitados a definir uma senha quando entrarem pela primeira vez.
    1. Selecione **Criar**. 

        ![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na página **Configurar modelo**, você vê o status do processo de criação do laboratório. A criação do modelo no laboratório leva até 20 minutos. Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure a máquina virtual do modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório.  

    ![Configurar o modelo](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Após a configuração do modelo ser concluída, você verá a seguinte página: 

    ![Página de configuração do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. As seguintes etapas do tutorial são opcionais: 
    2. Conecte-se à VM modelo selecionando **Conectar**. Se for uma VM de modelo do Linux, você escolherá se deseja se conectar usando SSH ou RDP (se RDP estiver habilitado).
    1. Selecione **Redefinição de senha** para redefinir a senha da VM. 
    1. Instale e configure software em sua VM modelo. 
    1. **Pare** a VM.  
    1. Insira uma **descrição** do modelo
9. Selecione **Avançar** na página do modelo. 
10. Na página **Publicar o modelo**, execute as seguintes ações. 
    1. Para publicar o modelo imediatamente, marque a caixa de seleção *Eu entendo que não posso modificar o modelo depois da publicação. Esse processo só pode ser feito uma vez e pode levar até uma hora* e selecione **Publicar**.  Publique o modelo para disponibilizar instâncias da VM modelo para os usuários do laboratório.

        > [!WARNING]
        > Depois de publicar, você não pode cancelar a publicação. 
    2. Para publicar mais tarde, selecione **Salvar para mais tarde**. É possível publicar a VM modelo após a conclusão do assistente. Para obter detalhes sobre como configurar e publicar após a conclusão do assistente, consulte a seção Publicar o modelo no artigo [Como gerenciar laboratórios de curso](how-to-manage-classroom-labs.md).

        ![Publicar modelo](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Você vê o **andamento da publicação** do modelo. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Você verá a página a seguir quando o modelo for publicado com êxito. Selecione **Concluído**.

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Você verá o **painel** do laboratório. 
    
    ![Painel de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Alterne para a página **Máquinas virtuais** e confirme que você vê cinco máquinas virtuais no estado **Não Atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>Tamanhos de VM  

| Size | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequeno | 2 | 3,5 GB | Esse tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Média | 4 | 7 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise | 
| Médio (virtualização aninhada) | 4 | 16 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise. Esse tamanho também dá suporte à virtualização aninhada. <p>Esse tamanho pode ser usado em cenários em que cada aluno precisa de várias VMs. Os professores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno tamanho dentro da máquina virtual. </p> |
| Grande | 8 | 32 GB | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande. Esse tamanho também dá suporte à virtualização aninhada |  
| GPU pequena (visualização) | 6 | 56 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 
| GPU pequena (computação) | 6 | 56 GB | Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aplicativos de aprendizado profundo. | 
| GPU média (visualização) | 12 | 112 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Portal do Azure Lab Services](https://labs.azure.com).
2. Selecione **Entrar**. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Confirme se você vê todos os laboratórios na conta de laboratório selecionada. 

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você verá laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula
1. No bloco para o laboratório, selecione três pontos (...) no canto. 

    ![Selecione o laboratório](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selecione **Excluir**. 

    ![Botão Excluir](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo **Excluir laboratório**, selecione **Excluir**. 

    ![Caixa de diálogo Excluir](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Alternar para outro laboratório de sala de aula
Para alternar do atual laboratório de sala de aula para outro, selecione a lista suspensa de laboratórios na conta de laboratório na parte superior.

![Selecione o laboratório na lista suspensa na parte superior](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)

