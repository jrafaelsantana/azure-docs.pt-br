---
title: Conectar dados do Check Point ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Check Point ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 1bfa59e92ce2cde45b448174260396f4e93a6282
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240131"
---
# <a name="connect-your-check-point-appliance"></a>Conectar seu dispositivo de verificação de ponto



Você pode conectar o Azure Sentinel a qualquer dispositivo de verificação de ponto salvando os arquivos de log como syslog CEF. A integração com o Azure Sentinel permite que você execute facilmente análises e consultas nos dados do arquivo de log do Check Point. Para obter mais informações sobre como o Azure Sentinel ingeri dados de CEF, consulte [conectar dispositivos CEF](connect-common-event-format.md).

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>Etapa 1: Conectar seu dispositivo de verificação de ponto usando um agente

Para conectar seu dispositivo do Check Point ao Azure Sentinel, você precisa implantar um agente em um computador dedicado (VM ou local) para dar suporte à comunicação entre o dispositivo e o Azure Sentinel. 

Como alternativa, você pode implantar o agente manualmente em uma VM do Azure existente em uma VM em outra nuvem ou em um computador local.

> [!NOTE]
> Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar sua rede para se alinhar com sua política de segurança de rede corporativa e alterar as portas e protocolos no daemon para se alinhar com seus requisitos. 

Para ver um diagrama de rede de ambas as opções, consulte [conectar fontes de dados](connect-data-sources.md).

### <a name="deploy-the-agent"></a>Implantar o agente
1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **ponto de verificação** e **abra a página conector**. 

1. Em **baixar e instalar o agente de syslog**, selecione o tipo de computador, o Azure ou o local. 
1. Na tela **máquinas virtuais** que é aberta, selecione o computador que você deseja usar e clique em **conectar**.
1. Se você escolher **baixar e instalar o agente para máquinas virtuais Linux do Azure**, selecione o computador e clique em **conectar**. Se você escolheu **baixar e instalar o agente para máquinas virtuais Linux que não são do Azure**, na tela **agente direto** , execute o script em **baixar e integrar agente para Linux**.
 
1. Na tela do conector, em **configurar e encaminhar syslog**, defina se o daemon do syslog é **rsyslog. d** ou **syslog-ng**. 
1. Copie esses comandos e execute-os em seu dispositivo:
     - Se você selecionou rsyslog. d:
            
       1. Instrua o daemon do syslog a escutar no recurso local_4 e no "Check Point" e enviar as mensagens do syslog para o agente do Azure Sentinel usando a porta 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
           
       2. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Onde {0} deve ser substituído pelo GUID do espaço de trabalho.
           
       1. Reiniciar o daemon do syslog`sudo service rsyslog restart`
             
    - Se você selecionou syslog-ng:

         1. Instrua o daemon do syslog a escutar no recurso local_4 e no "Check Point" e enviar as mensagens do syslog para o agente do Azure Sentinel usando a porta 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
         2. Baixe e instale o [arquivo de configuração security_events](https://aka.ms/asi-syslog-config-file-linux) que configura o agente syslog para escutar na porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Onde {0} deve ser substituído pelo GUID do espaço de trabalho.

        3. Reiniciar o daemon do syslog`sudo service syslog-ng restart`
2. Reinicie o agente de syslog usando este comando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Confirme se não há erros no log do agente executando este comando:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>Etapa 2: Encaminhar logs de ponto de verificação para o agente de syslog

Configure seu dispositivo de verificação de ponto para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Vá para a [exportação de log do Check Point](https://aka.ms/asi-syslog-checkpoint-forwarding).
2. Role para baixo até a **implantação básica** e siga as instruções para configurar a conexão, usando as seguintes diretrizes:
   - Defina a **porta do syslog** como **514** ou a porta que você definiu no agente.
     - Substitua o **nome** e o **endereço IP do servidor de destino** na CLI pelo nome do agente de syslog e pelo endereço IP.
     - Defina o formato como **CEF**.
3. Se você estiver usando a versão R 77.30 ou R 80.10, role até **instalações** e siga as instruções para instalar um exportador de log para sua versão.
 
## <a name="step-3-validate-connectivity"></a>Etapa 3: Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

1. Certifique-se de usar a instalação correta. O recurso deve ser o mesmo em seu dispositivo e no Azure Sentinel. Você pode verificar qual arquivo de recurso está usando no Azure Sentinel e modificá-lo no arquivo `security-config-omsagent.conf`. 

2. Verifique se os logs estão chegando à porta certa no agente de syslog. Execute este comando no computador do agente de syslog: `tcpdump -A -ni any  port 514 -vv`Esse comando mostra os logs que o transmite do dispositivo para o computador syslog. Verifique se os logs estão sendo recebidos do dispositivo de origem na porta correta e no recurso certo.

3. Certifique-se de que os logs enviados estão em conformidade com a [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. No computador que executa o agente de syslog, verifique se essas portas 514, 25226 estão abertas e ouvindo, usando o `netstat -a -n:`comando. Para obter mais informações sobre como usar esse comando, consulte [netstat (8)-página do manual do Linux](https://linux.die.net/man/8/netstat). Se ele estiver escutando corretamente, você verá:

   ![Portas do Azure Sentinel](./media/connect-cef/ports.png) 

5. Verifique se o daemon está definido para escutar na porta 514, na qual você está enviando os logs.
    - Para rsyslog:<br>Certifique-se de que `/etc/rsyslog.conf` o arquivo inclui essa configuração:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Para obter mais informações, [consulte imudp: Módulo](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) de entrada do syslog [UDP e imtcp: Módulo de entrada do syslog TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Para syslog-ng:<br>Certifique-se de que `/etc/syslog-ng/syslog-ng.conf` o arquivo inclui essa configuração:

           # source s_network {
            network( transport(UDP) port(514));
             };
    Para obter mais informações, consulte [syslog-ng Open Source Edition 3,16-guia de administração](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verifique se há comunicação entre o daemon do syslog e o agente. Execute este comando no computador do agente de syslog: `tcpdump -A -ni any  port 25226 -vv`Esse comando mostra os logs que o transmite do dispositivo para o computador syslog. Verifique se os logs também estão sendo recebidos no agente.

6. Se ambos os comandos forneceram resultados bem-sucedidos, verifique Log Analytics para ver se os logs estão chegando. Todos os eventos transmitidos por meio desses dispositivos aparecem em formato bruto no `CommonSecurityLog` log Analytics em tipo.

7. Para verificar se há erros ou se os logs não chegam, examine `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Se houver erros de incompatibilidade de formato de log, vá `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` para e examine o arquivo `security_events.conf`e verifique se os logs correspondem ao formato Regex que você vê neste arquivo.

8. Verifique se o tamanho padrão da mensagem syslog é limitado a 2048 bytes (2 KB). Se os logs forem muito longos, atualize o security_events. conf usando este comando:`message_length_limit 4096`

4. Certifique-se de executar estes comandos:
  
   - Se você usar o syslog-ng, execute estes comandos (Observe que ele reinicia o agente de syslog):

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        Reinicie o daemon do syslog:`sudo service syslog-ng restart`

   - Se você usar rsyslog, execute estes comandos (Observe que ele reinicia o agente de syslog): 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     Reinicie o daemon do syslog:`sudo service rsyslog restart`




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar dispositivos do Check Point ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

