---
title: Configurar a recuperação de desastre para VMs do Azure após a migração para o Azure com Azure Site Recovery
description: Este artigo descreve como preparar computadores para configurar a recuperação de desastre entre regiões do Azure após a migração para o Azure usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: ff35c5e23c5d8a448d62a3eeb8d15ba8d5a531e4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814541"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurar a recuperação de desastres para VMs do Azure após a migração para o Azure 


Siga este artigo se você [migrou computadores locais para VMs do Azure](tutorial-migrate-on-premises-to-azure.md) usando o serviço [site Recovery](site-recovery-overview.md) e agora deseja obter as VMs configuradas para recuperação de desastre em uma região secundária do Azure. O artigo descreve como garantir que o agente de VM do Azure esteja instalado em VMs migradas e como remover o serviço de mobilidade de Site Recovery que não é mais necessário após a migração.



## <a name="verify-migration"></a>Verificar migração

Antes de configurar a recuperação de desastres, certifique-se de que a migração foi concluída conforme o esperado. Para concluir uma migração com êxito, após o failover, você deve selecionar a opção **Concluir a migração** para cada computador que você deseja migrar. 

## <a name="verify-the-azure-vm-agent"></a>Verificar o agente de VM do Azure

Cada VM do Azure deve ter o [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) instalado. Para replicar VMs do Azure, Site Recovery instala uma extensão no agente.

- Se o computador estiver executando a versão 9.7.0.0 ou posterior do serviço de mobilidade Site Recovery, o agente de VM do Azure será instalado automaticamente pelo serviço de mobilidade em VMs do Windows. Em versões anteriores do serviço de mobilidade, você precisa instalar o agente automaticamente.
- Para VMs do Linux, você deve instalar o agente de VM do Azure manualmente. Você só precisa instalar o agente de VM do Azure se o serviço de mobilidade instalado no computador migrado for v 9,6 ou anterior.


### <a name="install-the-agent-on-windows-vms"></a>Instalar o agente em VMs do Windows

Se você estiver executando uma versão do serviço de mobilidade de Site Recovery anterior à 9.7.0.0, ou se tiver alguma outra necessidade de instalar o agente manualmente, faça o seguinte:  

1. Verifique se você tem permissões de administrador na VM.
2. Baixe o [instalador do agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Execute o arquivo do instalador.

#### <a name="validate-the-installation"></a>validar a instalação
Para verificar se o agente está instalado:

1. Na VM do Azure, na pasta C:\WindowsAzure\Packages, você deve ver o arquivo WaAppAgent.exe.
2. Clique com o botão direito do mouse no arquivo e em **Propriedades**, selecione a guia **Detalhes**.
3. Verifique se o campo **Versão do Produto** mostra 2.6.1198.718 ou mais recente.

[Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) sobre a instalação do agente para Windows.

### <a name="install-the-agent-on-linux-vms"></a>Instalar o agente em VMs Linux

Instale manualmente o agente de [VM Linux do Azure](../virtual-machines/extensions/agent-linux.md) da seguinte maneira:

1. Verifique se você tem permissões de administrador no computador.
2. É altamente recomendável que você instale o agente de VM do Linux usando um RPM ou um pacote de DEB do repositório de pacotes da distribuição. Todos os [provedores de distribuição aprovados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios.
    - É altamente recomendável que você atualize o agente somente por meio de um repositório de distribuição.
    - Não é recomendável instalar o agente de VM do Linux diretamente do GitHub e atualizá-lo.
    -  Se o agente mais recente para a sua distribuição não está disponível, entre em contato com o suporte da distribuição para obter instruções de como instalá-lo. 

#### <a name="validate-the-installation"></a>validar a instalação 

1. Execute este comando: **PS-e** para garantir que o agente do Azure esteja em execução na VM do Linux.
2. Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:
    - Para o Ubuntu: **serviço walinuxagent iniciar**
    - Para outras distribuições: **serviço waagent iniciar**


## <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de Mobilidade

1. Desinstale manualmente o serviço de mobilidade da VM do Azure, usando um dos métodos a seguir. 
    - Para Windows, no Painel de controle > **Adicionar ou remover programas**, desinstale **Serviço de Mobilidade do Microsoft Azure Site Recovery/Servidor de destino mestre**. Em um prompt de comandos com privilégios elevados, execute:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Para o Linux, entre como o usuário raiz. Em um terminal, vá para **/user/local/ASR** e execute o seguinte comando:
        ```
        ./uninstall.sh -Y
        ```
2. Reinicie a máquina virtual antes de configurar a replicação.

## <a name="next-steps"></a>Próximas etapas

[Examine a solução de problemas](site-recovery-extension-troubleshoot.md) para a extensão de site Recovery no agente de VM do Azure.
[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
