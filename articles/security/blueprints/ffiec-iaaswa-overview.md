---
title: Blueprint de Segurança e Conformidade do Azure – aplicativo Web de IaaS para FFIEC
description: Blueprint de Segurança e Conformidade do Azure – aplicativo Web de IaaS para FFIEC
services: security
author: meladie
ms.assetid: 8577e982-8bc0-4817-a16e-adf2ffefc6f5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 60098aeb0382ddcbc880a734bdd8a0516fd460c6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257194"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-ffiec-financial-services"></a>Blueprint de Segurança e Conformidade do Azure: Aplicativo Web IaaS para serviços financeiros do FFIEC

## <a name="overview"></a>Visão geral

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para a implantação de um ambiente de IaaS (infraestrutura como serviço) adequado para a coleta, o armazenamento e a recuperação de dados financeiros, regulamentado pelo FFIEC (Federal Financial Institution Examination Council).

Essa arquitetura de referência, o guia de implementação e o modelo de ameaça fornecem uma base para que os clientes fiquem em conformidade com os requisitos do FFIEC. Essa solução fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure em conformidade com o FFIEC. No entanto, essa solução não deve ser usada no estado em que se encontra em um ambiente de produção porque uma configuração adicional é necessária.

Para alcançar a conformidade com o FFIEC é necessário que auditores qualificados certifiquem a solução de produção do cliente. As auditorias são supervisionadas por examinadores de agências membros do FFIEC, incluindo o Comitê de Administradores dos órgãos FRB (Federal Reserve System), FDIC (Federal Deposit Insurance Corporation), NCUA (National Credit Union Administration), OCC (Office of the Comptroller of the Currency) e CFPB (Consumer Financial Protection Bureau). Esses examinadores certificam que as auditorias sejam realizadas por avaliadores independentes da instituição auditada. Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Este Blueprint de Segurança e Conformidade do Azure implanta uma arquitetura de referência para um aplicativo Web de IaaS com um back-end do SQL Server. A arquitetura inclui uma camada da Web, uma camada de dados, a infraestrutura do Active Directory, o Gateway de Aplicativo e o Load Balancer. As máquinas virtuais implantadas nas camadas da Web e de dados são configuradas em um conjunto de disponibilidade, e as instâncias do SQL Server são configuradas em um grupo de disponibilidade AlwaysOn para alta disponibilidade. As máquinas virtuais são associadas ao domínio e as políticas de grupo do Active Directory são usadas para aplicar configurações de conformidade e segurança no nível do sistema operacional.

Toda a solução é baseada em um Armazenamento do Azure que os clientes configuram no portal do Azure. O Armazenamento do Azure criptografa todos os dados com a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O armazenamento com redundância geográfica garante que os dados sejam replicados para um datacenter secundário centenas de quilômetros de distância e armazenados novamente como três cópias nesse datacenter, impedindo que um evento prejudicial no datacenter primário do cliente resulte em perda de dados.

Para maior segurança, todos os recursos nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O controle de acesso baseado em função do Azure Active Directory é usado para controlar o acesso aos recursos implantados, incluindo as chaves no Azure Key Vault. A integridade do sistema é monitorada por meio do Azure Monitor. Os clientes configuram os dois serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel facilmente navegável.

Um bastião de gerenciamento fornece conexão segura para os administradores acessarem recursos implantados. **A Microsoft recomenda configurar uma conexão VPN ou do ExpressRoute para gerenciamento e importação de dados para a sub-rede na arquitetura de referência.**

![Diagrama da arquitetura de referência do aplicativo Web de IaaS para FFIEC](images/ffiec-iaaswa-architecture.png "IaaS Web Application for FFIEC reference architecture diagram")

A solução usa os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão localizados na seção [Arquitetura de implantação](#deployment-architecture).

- Conjuntos de Disponibilidade
    - (1) controladores de domínio do Active Directory
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Active Directory do Azure
- Gateway de Aplicativo do Azure
    - (1) Firewall do Aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Porta ouvinte: 443
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (logs)
- Azure Resource Manager
- Central de Segurança do Azure
- Armazenamento do Azure
    - (7) contas de armazenamento com redundância geográfica
- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastião (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster do SQL Server (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - (1) /16 rede
    - (5) /24 Redes
    - (5) Grupos de Segurança de Rede
- Testemunha de nuvem
- Cofre dos Serviços de Recuperação

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Host bastião**: O host bastião é o único ponto de entrada que permite que os usuários acessem os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura com os recursos implantados, permitindo apenas tráfego remoto de endereços IP públicos em uma lista de segurança. Para permitir o tráfego RDP (área de trabalho remota), a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma máquina virtual como um host bastião ingressado em domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensão de Diagnóstico do Azure](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) usando o Azure Key Vault
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando eles não estiverem em uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado, para que as credenciais e outras informações secretas sejam executadas em um ambiente protegido isolado do sistema operacional em execução

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implanta recursos em uma arquitetura com uma sub-rede separada e Web, sub-rede do banco de dados, sub-rede do Active Directory e sub-rede de gerenciamento em uma rede virtual. Sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas a sub-redes individuais para restringir o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema.

Consulte a configuração dos [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantada com esta solução. As organizações podem configurar grupo de segurança de rede editando o arquivo acima usando [esta documentação](../../virtual-network/virtual-network-vnet-plan-design-arm.md) como guia.

Cada uma das sub-redes tem um Grupo de Segurança de rede dedicado:
- 1 Grupo de Segurança de Rede para o Gateway de Aplicativo (LBNSG)
- 1 Grupo de Segurança de Rede para o host bastião (MGTNSG)
- 1 Grupo de Segurança de Rede para os controladores de domínio primário e de backup (ADNSG)
- 1 Grupo de Segurança de Rede para SQL Servers e a Testemunha de Nuvem (SQLNSG)
- 1 Grupo de Segurança de Rede para a camada da Web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. Além disso, todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure**: Para satisfazer os requisitos de dados em repouso criptografado, todo o [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](../../storage/common/storage-service-encryption.md). Isso ajuda a proteger os dados pessoais em apoio aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo FFIEC.

**Azure Disk Encryption**: O [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utiliza o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: A instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- A [autenticação e a autorização do Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidade dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
- A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
- O Banco de Dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não fiquem sujeitos a acesso não autorizado.
- [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
- A [Detecção de Ameaças SQL](../../sql-database/sql-database-threat-detection.md) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
- As [Colunas Criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca sejam exibidos como texto não criptografado no sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. A Máscara de Dados Dinâmicos pode descobrir automaticamente dados potencialmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. Isso ajuda a identificar e reduzir o acesso aos dados, para que eles não saiam do banco de dados por meio de acesso não autorizado. Os clientes são responsáveis por ajustar as configurações da máscara de dados dinâmicos para seguir seu esquema de banco de dados.

### <a name="identity-management"></a>Gerenciamento de identidades

As seguintes tecnologias oferecem funcionalidades para gerenciar o acesso a dados no ambiente do Azure:

- O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL do Azure.
- A autenticação no aplicativo é executada usando o Azure Active Directory. Para obter mais informações, confira [Integrando aplicativos com o Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Além disso, a criptografia da coluna do banco de dados utiliza o Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Em vez de conceder permissão irrestrita aos recursos do Azure a todos os usuários, os administradores podem permitir apenas determinadas ações para acessar os dados. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite que os clientes minimizem o número de usuários que tenham acesso a determinadas informações. Os administradores podem usar o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
- O [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura respostas automatizadas para detectar ações suspeitas relacionadas a essas identidades e investiga incidentes suspeitos para tomar medidas apropriadas para resolvê-los.


### <a name="security"></a>Segurança

**Gerenciamento de segredos**: A solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. As seguintes funcionalidades do Azure Key Vault ajudam os clientes a proteger os dados e o acesso a eles:

- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma Chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.
- A solução é integrada ao Azure Key Vault para gerenciar as chaves de criptografia de disco e segredos da máquina virtual IaaS.

**Gerenciamento de patches**: Máquinas Virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual as implantações atualizadas podem ser criadas para aplicar patch em máquinas virtuais quando necessário.

**Proteção contra malware**: O [antimalware da Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para Máquinas Virtuais oferece uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou executar em máquinas virtuais protegidas.

**Central de Segurança do Azure**: Com a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques de forma centralizada. Além disso, a Central de Segurança do Azure acessa as configurações existentes de serviços do Azure para fornecer recomendações de serviço e configuração, a fim de ajudar a melhorar a postura de segurança e proteger os dados.

A Central de Segurança do Azure usa uma variedade de funcionalidades de detecção para alertar os clientes de ataques potenciais direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança do Azure tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são disparados em caso de ameaça ou atividade suspeita. As [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na Central de Segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados já coletados do ambiente.

A Central de Segurança do Azure fornece alertas de segurança e incidentes priorizados, simplificando a descoberta e a resolução por parte dos clientes de possíveis problemas de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada, a fim de ajudar as equipes de resposta a incidentes a investigar e corrigir as ameaças.

**Gateway de Aplicativo do Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um Gateway de Aplicativo do Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](../../application-gateway/create-ssl-portal.md)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](../../application-gateway/waf-overview.md) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0
- [Habilitar o registro em log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](../../application-gateway/quick-create-portal.md)
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade dos negócios

**Alta disponibilidade**: a solução implanta todas máquinas virtuais em um [Conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade fazem com que as máquinas virtuais sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma máquina virtual fica disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA do Azure de 99,95%.

**Cofre dos Serviços de Recuperação**: o [Cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) armazena dados de backup e protege todas as configurações das Máquinas Virtuais do Azure nesta arquitetura. Com um Cofre dos Serviços de Recuperação, os clientes podem restaurar arquivos e pastas de uma máquina virtual de IaaS sem restaurar a máquina virtual inteira, acelerando o tempo de restauração.

**Testemunha de Nuvem**: a [Testemunha de Nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum do cluster de failover no Windows Server 2016 que aproveita o Azure como o ponto de arbitragem. A Testemunha de Nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum, mas usa o Armazenamento de Blobs do Azure padrão, disponível ao público. Isso elimina a sobrecarga extra de manutenção de máquinas virtuais hospedadas em uma nuvem pública.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividades**: Os [logs de atividades](../../azure-monitor/platform/activity-logs-overview.md) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: Os [Logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) incluem todos os registros emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs de Armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs do Azure Monitor**: Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Log Analytics, o que permite que todos os dados sejam analisados juntos, independentemente de sua origem original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do Agente](../../monitoring/monitoring-solution-agenthealth.md): A solução de Integridade do Agente informa quantos agentes são implantados e sua distribuição geográfica, além de quantos agentes sem resposta e o número de agentes que estão enviando dados operacionais.
-   [Análise do Log de Atividades](../../azure-monitor/platform/collect-activity-logs.md): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nessa solução, os runbooks ajudam a coletar logs do Banco de Dados SQL do Azure. A solução [Controle de Alterações](../../automation/change-tracking.md) da Automação permite que os clientes identifiquem com facilidade as alterações no ambiente.

**Azure Monitor**: O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o rastreamento de chamadas à API nos recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/ffiec-iaaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Modelo de ameaça de aplicativo Web de IaaS para FFIEC](images/ffiec-iaaswa-threat-model.png "IaaS Web Application for FFIEC threat model")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Blueprint de Segurança e Conformidade do Azure – matriz de responsabilidades do cliente para FFIEC](https://aka.ms/ffiec-crm) lista todos os objetivos de segurança exigidos pelo FFIEC. A matriz indica se a implementação de cada controle é responsabilidade da Microsoft, do cliente ou é compartilhada pelos dois.

O [Blueprint de Segurança e Conformidade do Azure – matriz de implementação do aplicativo Web de IaaS para FFIEC](https://aka.ms/ffiec-iaaswa-cim) fornece informações sobre quais requisitos do FFIEC são abordados pela arquitetura de aplicativo Web de IaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada objetivo abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Um túnel de VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão segura com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de IaaS. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre na Internet e permite que os clientes enviem informações por &quot;túnel&quot; com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não são expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="disclaimer"></a>Isenção de responsabilidade

- Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
- Os clientes podem copiar e usar este documento para fins de consulta interna.
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
- Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
- Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
