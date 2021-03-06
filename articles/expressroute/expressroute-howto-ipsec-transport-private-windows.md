---
title: 'Configurar o modo de transporte IPsec para hosts do Windows – emparelhamento particular: ExpressRoute: Azure | Microsoft Docs'
description: Como habilitar o modo de transporte IPsec entre as VMs do Windows do Azure e os hosts do Windows local usando GPOs e UOs de emparelhamento privado do ExpressRoute.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: d728980517988e2dc39be4e4b64d20157a1aef54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60366663"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Configurar o modo de transporte IPsec para emparelhamento privado do ExpressRoute

Este artigo ajuda você a criar túneis IPsec no modo de transporte por meio do emparelhamento privado do ExpressRoute entre VMs do Azure executando Windows e hosts do Windows locais. As etapas neste artigo criam essa configuração usando objetos de política de grupo. Embora seja possível criar esta configuração sem usar UOs (unidades organizacionais) e GPOs (objetos de política de grupo), a combinação de UOs e GPOs ajudará a simplificar o controle de suas políticas de segurança e permitirá que você aumente a escala rapidamente. As etapas neste artigo pressupõem que você já tem uma configuração do Active Directory e está familiarizado com o uso de UOs e GPOs.

## <a name="about-this-configuration"></a>Sobre esta configuração

As configurações nas etapas a seguir usam uma única VNet (rede virtual) do Azure com o emparelhamento privado do ExpressRoute. No entanto, essa configuração pode abranger mais redes virtuais do Azure e redes locais. Este artigo ajudará você a definir uma política de criptografia IPsec e aplicá-la a um grupo de hosts e VMs do Azure locais que fazem parte da mesma UO. Você configura a criptografia entre as VMs do Azure (vm1 e vm2) e o host1 local somente para tráfego HTTP com a porta de destino 8080. Diferentes tipos de política de IPsec podem ser criados com base nos seus requisitos.

### <a name="working-with-ous"></a>Como trabalhar com UOs 

A política de segurança associada a uma UO é enviada aos computadores por meio do GPO. Algumas vantagens de usar UOs, em vez de aplicar políticas a um único host, são:

* Associar uma política a uma UO garante que os computadores que pertencem à mesma UO obtenham as mesmas políticas.
* Alterar a política de segurança associada à UO aplicará as alterações a todos os hosts na UO.

### <a name="diagrams"></a>Diagramas

O diagrama a seguir mostra a interconexão e o espaço de endereços IP atribuído. As VMs do Azure e o host local estão em execução no Windows 2016. As VMs do Azure e o host1 local fazem parte do mesmo domínio. As VMs do Azure e os hosts locais podem resolver nomes corretamente usando DNS.

[![1]][1]

Este diagrama mostra os túneis de IPsec em trânsito no emparelhamento privado do ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Trabalhar com a política de IPsec

No Windows, a criptografia é associada à política de IPsec. A política IPsec determina qual tráfego IP é protegido e o mecanismo de segurança aplicado aos pacotes IP.
As **políticas IPsec** são compostas dos seguintes itens: **Listas de Filtros**, **Ações de Filtros** e **Regras de Segurança**.

Ao configurar a política de IPsec, é importante compreender a terminologia de política de IPsec a seguir:

* **Política IPsec:** Uma coleção de regras. Apenas uma política pode estar ativa ("atribuída") em um determinado momento. Cada política pode ter uma ou mais regras, podendo estar todas elas ativas simultaneamente. É possível atribuir apenas uma política de IPsec a um computador por vez. No entanto, na política de IPsec, você pode definir várias ações que podem ser executadas em situações diferentes. Cada conjunto de regras de IPsec está associado a uma lista de filtros que afeta o tipo de tráfego de rede ao qual a regra se aplica.

* **Listas de Filtros:** São pacotes de um ou mais filtros. Uma lista pode conter vários filtros. O filtro define se a comunicação é permitida, protegida ou bloqueada, de acordo com os intervalos de endereços IP, protocolos ou mesmo portas de protocolo específicas. Cada filtro corresponde a um determinado conjunto de condições. Por exemplo, os pacotes enviados de uma sub-rede específica a um computador específico em uma porta de destino específica. Quando as condições de rede correspondem a um ou mais desses filtros, a lista de filtros é ativada. Cada filtro é definido dentro de uma lista de filtros específica. Filtros não podem ser compartilhados entre as listas de filtro. No entanto, uma lista de filtro fornecidos pode ser incorporada em várias políticas de IPsec. 

* **Ações de Filtros:** Um método de segurança define um conjunto de algoritmos de segurança, protocolos e chaves que um computador oferece durante as negociações de IKE. Ações de filtro são listas de métodos de segurança, classificados em ordem de preferência.  Quando um computador negocia uma sessão IPsec, ela aceita ou envia propostas com base na configuração de segurança armazenada na lista de ações de filtro.

* **Regras de segurança:** Controlam como e quando uma política IPsec protege a comunicação. Usa **lista de filtros** e **ações de filtro** para criar uma regra de IPsec para criar a conexão IPsec. Cada política pode ter uma ou mais regras, podendo estar todas elas ativas simultaneamente. Cada regra contém uma lista de filtros IP e uma coleção de ações de segurança que ocorrem mediante a correspondência com essa lista de filtros:
  * Ações de Filtro IP
  * Métodos de autenticação
  * Configurações de túnel IP
  * Tipos de conexão

[![5]][5]

## <a name="before-you-begin"></a>Antes de começar

Verifique se os seguintes pré-requisitos foram atendidos:

* Você deve ter uma configuração funcional do Active Directory que pode usar para implementar as configurações de Política de Grupo. Para obter mais informações sobre GPOs, confira [Objetos de Política de Grupo](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Você deve ter um circuito do ExpressRoute ativo.
  * Para obter mais informações sobre como criar um circuito do ExpressRoute, confira [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Verifique se o circuito está habilitado pelo provedor de conectividade. 
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-arm.md) para obter instruções sobre roteamento. 
  * Verifique se você tem uma VNet e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o GatewayType “ExpressRoute”, não VPN.

* O gateway de rede virtual ExpressRoute deve estar conectado ao circuito do ExpressRoute. Para obter mais informações, confira [Conectar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Verifique se as VMs do Windows do Azure são implantadas para a rede virtual.

* Verifique se há conectividade entre os hosts locais e as VMs do Azure.

* Verifique se as VMs do Windows do Azure e os hosts locais estão aptos a usar o DNS para resolver nomes corretamente.

### <a name="workflow"></a>Fluxo de trabalho

1. Crie um GPO e associe-o à UO.
2. Defina uma **Ação de Filtro** do IPsec.
3. Definir uma **Lista de Filtros** do IPsec.
4. Criar uma Política de IPsec com **Regras de Segurança**.
5. Atribua o GPO de IPsec à UO.

### <a name="example-values"></a>Valores de exemplo

* **Nome de Domínio:** ipsectest.com

* **OU:** IPSecOU

* **Computador do Windows local:** host1

* **VMs do Windows do Azure:** vm1, vm2

## <a name="creategpo"></a>1. Criar um GPO

1. Para criar um novo GPO vinculado a uma UO, abra o snap-in de Gerenciamento de Política de Grupo e localize a UO à qual o GPO será vinculado. No exemplo, a UO é denominada **IPSecOU**. 

   [![9]][9]
2. No snap-in Gerenciamento de Política de Grupo, selecione a UO e clique com o botão direito do mouse. No menu suspenso, clique em "**Criar uma GPO neste domínio e vinculá-la aqui…** ".

   [![10]][10]
3. Dê ao GPO um nome intuitivo para que você possa localizá-lo facilmente mais tarde. Clique em **OK** para criar e vincular o GPO.

   [![11]][11]

## <a name="enablelink"></a>2. Habilitar o link de GPO

Para aplicar o GPO à UO, o GPO deve não apenas ser vinculado a UO, mas o link deve ser habilitado também.

1. Localize o GPO que você criou, clique com o botão direito do mouse e selecione **Editar** na lista suspensa.
2. Para aplicar o GPO à UO, selecione **Link Habilitado**.

   [![12]][12]

## <a name="filteraction"></a>3. Definir a ação de filtro IP

1. Na lista suspensa, clique com o botão direito do mouse em **Política de Segurança de IP no Active Directory** e, em seguida, clique em **Gerenciar listas de filtro IP e ações de filtro...** .

   [![15]][15]
2. Na guia "**Gerenciar Ações de filtro**", clique em **Adicionar**.

   [![16]][16]

3. No **Assistente de Ação de Filtro de Segurança de IP**, clique em **Avançar**.

   [![17]][17]
4. Dê à ação de filtro um nome intuitivo para que você possa encontrá-lo posteriormente. Neste exemplo, a ação de filtro é denominada **myEncryption**. Também é possível adicionar uma descrição. Em seguida, clique em **Avançar**.

   [![18]][18]
5. **Negociar segurança** permite que você defina o comportamento caso o IPsec não possa ser estabelecido com outro computador. Selecione **Negociar segurança** e, em seguida, clique em **Avançar**.

   [![19]][19]
6. Na página **Comunicação com computadores que não dão suporte a IPsec**, selecione **Não permitir comunicação não segura** e, em seguida, clique em **Avançar**.

   [![20]][20]
7. Na página **Tráfego e Segurança de IP**, selecione **Personalizado** e clique em **Configurações...** .

   [![21]][21]
8. Na página **Configurações de Método de Segurança Personalizado**, selecione **Integridade de dados e criptografia (ESP): SHA1, 3DES**. Em seguida, clique em **OK**.

   [![22]][22]
9. Na página **Gerenciar Ações de Filtro**, você pode ver que o filtro **myEncryption** foi adicionado com êxito. Clique em **fechar**

   [![23]][23]

## <a name="filterlist1"></a>4. Definir uma lista de filtros IP

Crie uma lista de filtros que especifica o tráfego HTTP criptografado com a porta de destino 8080.

1. Para qualificar os tipos de tráfego que devem ser criptografados, use uma **lista de filtros IP**. Na guia **Gerenciar Listas de Filtros IP**, clique em **Adicionar** para adicionar uma nova lista de filtros IP.

   [![24]][24]
2. No campo **Nome:** , digite um nome para sua lista de filtros IP. Por exemplo, **azure-onpremises-HTTP8080**. Em seguida, clique em **Adicionar**.

   [![25]][25]
3. Na página **Descrição do filtro IP e propriedade espelhada**, selecione **Espelhado**. A configuração espelhada corresponde a pacotes que vão em ambos os sentidos, o que permite comunicação bidirecional. Em seguida, clique em **Próximo**.

   [![26]][26]
4. Na página **Origem do Tráfego IP**, na lista suspensa **Endereço de origem:** , escolha **Uma Sub-rede ou Endereço IP Específico**. 

   [![27]][27]
5. Especifique **Sub-rede ou Endereço IP:** do endereço de origem do tráfego IP e clique em **Avançar**.

   [![28]][28]
6. Especifique o **Endereço de destino:** Endereço IP ou Sub-rede. Em seguida, clique em **Avançar**.

   [![29]][29]
7. Na página **Tipo de Protocolo IP**, selecione **TCP**. Em seguida, clique em **Avançar**.

   [![30]][30]
8. Na página **Porta do Protocolo IP**, selecione **De qualquer porta** e **Para esta porta:** . Digite **8080** na caixa de texto. Essas configurações especificam que apenas o tráfego HTTP na porta 8080 do destino será criptografado. Em seguida, clique em **Avançar**.

   [![31]][31]
9. Exibir a lista de filtro IP.  A configuração da Lista de Filtro IP **azure-onpremises-HTTP8080** dispara a criptografia para todo o tráfego que corresponde aos critérios a seguir:

   * Qualquer endereço de origem em 10.0.1.0/24 (Sub-rede 2 do Azure)
   * Qualquer endereço de destino em 10.2.27.0/25 (sub-rede local)
   * Protocolo TCP
   * Porta de destino 8080

   [![32]][32]

## <a name="filterlist2"></a>5. Editar a lista de filtros IP

Para criptografar o mesmo tipo de tráfego na direção oposta (do host local para a VM do Azure), é necessário um segundo filtro IP. O processo de configuração do novo filtro é o mesmo processo usado para configurar o primeiro filtro IP. As únicas diferenças são as sub-redes de origem e de destino.

1. Para adicionar um novo filtro IP à lista de filtros IP, selecione **Editar**.

   [![33]][33]
2. Na página **Lista de Filtros IP**, clique em **Adicionar**.

   [![34]][34]
3. Crie um segundo filtro IP usando as configurações no exemplo a seguir:

   [![35]][35]
4. Depois de criar o segundo filtro IP, a lista de filtros IP terá esta aparência:

   [![36]][36]

Se a criptografia for necessária entre um caminho local e uma sub-rede do Azure para proteger um aplicativo, em vez de modificar a lista de filtros de IP existente, você poderá adicionar uma nova lista de filtros de IP em vez disso. Associar duas listas de filtros à mesma política de IPsec oferece maior flexibilidade porque uma lista de filtros IP específica pode ser modificada ou removida a qualquer momento sem afetar as outras listas de filtro IP.

## <a name="ipsecpolicy"></a>6. Criar uma política de segurança de IPsec 

Criar uma Política de IPsec com regras de segurança.

1. Selecione **Políticas de IPSecurity no Active Directory** associado à UO. Clique com o botão direito do mouse e selecione **Criar Política de Segurança de IP**.

   [![37]][37]
2. Dê um nome à política de segurança. Por exemplo, **policy-azure-onpremises**. Em seguida, clique em **Avançar**.

   [![38]][38]
3. Clique em **Avançar** sem marcar a caixa de seleção.

   [![39]][39]
4. Verifique se a caixa de seleção **Editar propriedades** está selecionada e, em seguida, clique em **Concluir**.

   [![40]][40]

## <a name="editipsec"></a>7. Editar a política de segurança de IPsec

Adicione à política de IPsec a **Lista de Filtros IP** e a **Ação de Filtro** que você configurou anteriormente.

1. Na guia **Regras** de Propriedades da política HTTP, clique em **Adicionar**.

   [![41]][41]
2. Na página de Boas-vindas, clique em **Avançar**.

   [![42]][42]
3. Uma regra fornece a opção de definir o modo de IPsec: modo de túnel ou modo de transporte.

   * No modo de túnel, o pacote original é encapsulado por um conjunto de cabeçalhos de IP. O modo de túnel protege as informações de roteamento internas ao criptografar o cabeçalho IP do pacote original. O modo de túnel é implementado amplamente entre os gateways em cenários de VPN site a site. O modo de túnel é, na maioria dos casos, usado para criptografia de ponta a ponta entre hosts.

   * O modo de transporte criptografa apenas o payload e o trailer ESP; o cabeçalho IP do pacote original não é criptografado. No modo de transporte, o IP de origem e o IP de destino dos pacotes são inalterados.

   Selecione **Esta regra não especifica um túnel** e, em seguida, clique em **Avançar**.

   [![43]][43]
4. **Tipo de Rede** define qual conexão de rede se associa à política de segurança. Selecione **Todas as conexões de rede** e, em seguida, clique em **Avançar**.

   [![44]][44]
5. Selecione a lista de filtros IP que você criou anteriormente, **azure-onpremises-HTTP8080** e, em seguida, clique em **Avançar**.

   [![45]][45]
6. Selecione a Ação de Filtro **myEncryption** existente que você criou anteriormente.

   [![46]][46]
7. O Windows é compatível com quatro tipos diferentes de autenticações: Kerberos, certificados, NTLMv2 e chave pré-compartilhada. Como estamos trabalhando com hosts adicionados a domínio, selecione **Padrão do Active Directory (protocolo Kerberos V5)** e, em seguida, clique em **Avançar**.

   [![47]][47]
8. A nova política cria a regra de segurança: **azure-onpremises-HTTP8080**. Clique em **OK**.

   [![48]][48]

A política IPsec requer que todas as conexões de HTTP na porta 8080 usem o modo de transporte IPsec de destino. Uma vez que HTTP é um protocolo de texto não criptografado, ter a política de segurança habilitada garante que os dados sejam criptografados quando forem transferidos por meio de emparelhamento privado do ExpressRoute. É mais complexo configurar a política de Segurança de IP para o Active Directory do que o Firewall do Windows com segurança avançada, mas ela permite mais personalização da conexão IPsec.

## <a name="assigngpo"></a>8. Atribuir o GPO de IPsec à UO

1. Exiba a política. A política de grupo de segurança está definida, mas ainda não foi atribuída.

   [![49]][49]
2. Para atribuir a política de grupo de segurança à UO **IPSecOU**, clique com o botão direito do mouse na política de segurança e escolha **Atribuir**.
   Cada computador que pertence à UO terá a política de grupo de segurança atribuída.

   [![50]][50]

## <a name="checktraffic"></a>Verificar a criptografia de tráfego

Para conferir a GPO de criptografia aplicada à UO, instale o IIS em todas as VMs do Azure e no host1. Todo IIS é personalizado para responder às solicitações HTTP na porta 8080.
Para verificar a criptografia, você pode instalar um rastreador de rede (como Wireshark) em todos os computadores na UO.
Um script do PowerShell funciona como um cliente HTTP para gerar solicitações HTTP na porta 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
A captura de rede a seguir mostra que os resultados para host1 local com o filtro de exibição ESP correspondem apenas ao tráfego criptografado:

[![51]][51]

Se você executar o script PowerShell localmente (cliente HTTP), a captura de rede na VM do Azure mostrará um rastreamento semelhante.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "modo de transporte do IPsec de Diagrama de rede por meio do ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "Tráfego interessante do IPsec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Política IPsec do Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Unidade Organizacional na Política de Grupo"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "criar um GPO associado à UO"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "atribuir um nome ao GPO associado à UO"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "editar o GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Gerenciar Listas de Filtro IP e Ações de Filtro"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "adicionar Ação de Filtro"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Assistente de Ação"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "nome da Ação de Filtro"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Ação de Filtro"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "especificar que o comportamento é de uma conexão não segura é estabelecida"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mecanismo de segurança"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Método de segurança personalizado"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "lista de ações de filtro"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Adicionar uma nova lista de filtros IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Adicionar tráfego HTTP ao filtro IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "combinar pacote em ambos os sentidos"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "seleção de sub-rede de Origem"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Rede de Origem"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Rede de Destino"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protocolo"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "porta de origem e porta de destino"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "Lista de filtros"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "lista de filtros IP com tráfego HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Como adicionar um segundo Filtro IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "lista de Filtros IP – segunda entrada"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "lista de Filtros IP – segunda entrada"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "criar a política de Segurança de IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "nome da política de IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Assistente de política de IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "edição da política de IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "adicionar nova regra de segurança à política de IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "criar uma nova regra de segurança"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Modo de Transporte"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Tipo de rede"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "seleção da Lista de Filtros IP existente"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "seleção de Ação de Filtro existente"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "seleção do método de autenticação"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "fim do processo de criação da política de segurança"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "Política IPsec vinculada ao GPO, mas não atribuída"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "Política de IPsec atribuída ao GPO"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Captura do tráfego criptografado de IPsec"
