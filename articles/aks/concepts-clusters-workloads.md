---
title: Conceitos - Princípios básicos do Kubernetes para o Azure Kubernetes Services (AKS)
description: Conheça os cluster básico e os componentes de carga de trabalho do Kubernetes e como elas se relacionam aos recursos no serviço de Kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: 3792eed170d3e3e1cdd267c0c88d2d2d6c520733
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672806"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Conceitos de Kubernetes para o serviço de Kubernetes do Azure (AKS)

À medida que o desenvolvimento de aplicativos se move para uma abordagem baseada em contêiner, a necessidade de orquestrar e gerenciar recursos é importante. O Kubernetes é a plataforma líder que fornece a capacidade de fornecer agendamento confiável de cargas de trabalho de aplicativos tolerantes a falhas. O Azure Kubernetes Service (AKS) é uma oferta gerenciada da Kubernetes que simplifica ainda mais a implantação e o gerenciamento de aplicativos baseados em contêiner.

Este artigo apresenta os principais componentes da infraestrutura do Kubernetes, como os *conjuntos mestre de clusters*, *nós* e *conjuntos de nós*. Recursos de carga de trabalho, como *pods*, *implantações* e *conjuntos*, também são apresentados, além de como agrupar recursos em *namespaces*.

## <a name="what-is-kubernetes"></a>O que é Kubernetes?

O Kubernetes é uma plataforma em rápida evolução que gerencia aplicativos baseados em contêiner e seus componentes de rede e armazenamento associados. O foco está nas cargas de trabalho de aplicativos, não nos componentes de infraestrutura subjacentes. O Kubernetes fornece uma abordagem declarativa para implementações, apoiada por um conjunto robusto de APIs para operações de gerenciamento.

Você pode criar e executar aplicativos modernos, portáteis e baseados em micros serviços que se beneficiam do Kubernetes orquestrando e gerenciando a disponibilidade desses componentes de aplicativo. O Kubernetes suporta aplicativos sem estado e com estado, à medida que as equipes progridem através da adoção de aplicativos baseados em micros serviços.

Como uma plataforma aberta, o Kubernetes permite que você construa seus aplicativos com sua linguagem de programação, sistema operacional, bibliotecas ou barramento de mensagens preferido. As ferramentas existentes de integração contínua e entrega contínua (CI/CD) podem ser integradas ao Kubernetes para agendar e implantar versões.

O Serviço de Kubernetes do Azure (AKS) fornece um serviço de Kubernetes gerenciado que reduz a complexidade das tarefas de implantação e gerenciamento principal, incluindo a coordenação de atualizações. Os mestres de cluster do AKS são gerenciados pela plataforma do Azure e você paga apenas pelos nós do AKS que executam seus aplicativos. O AKS é criado sobre o mecanismo de serviço de kubernetes do Azure de software livre ([AKs-Engine][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Arquitetura de cluster do Kubernetes

Um cluster Kubernetes é dividido em dois componentes:

- *Nós do mestre de cluster* fornecem os serviços centrais do Kubernetes e a orquestração de cargas de trabalho de aplicativos.
- *Nós* executar suas cargas de trabalho do aplicativo.

![Componentes mestre e nó do cluster do Kubernetes](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Mestre do cluster

Quando você cria um cluster AKS, um mestre de cluster é criado e configurado automaticamente. Esse mestre de cluster é fornecido como um recurso gerenciado do Azure abstraído do usuário. Não há custo para o mestre de cluster, somente os nós que fazem parte do cluster AKS.

O mestre do cluster inclui os seguintes componentes principais do Kubernetes:

- *kube-apiserver* -servidor a API é como as APIs do Kubernetes subjacentes são expostas. Esse componente fornece a interação para ferramentas de gerenciamento, tais como `kubectl` ou o painel do Kubernetes.
- *etcd* - Para manter o estado do seu cluster e configuração do Kubernetes, o altamente disponível *etcd* é um armazenamento de valores chave dentro do Kubernetes.
- *kube-scheduler* - Quando você cria ou dimensiona aplicativos, o Scheduler determina quais nós podem executar a carga de trabalho e iniciá-los.
- *Gerenciador do controlador de kube* -o Gerenciador do controlador supervisiona um número de controladores de menores do que executar ações como replicar pods e lidar com operações de nó.

O AKS fornece um mestre de cluster de locatário único, com um servidor de API dedicado, Agendador, etc. Você define o número e o tamanho dos nós e a plataforma do Azure configura a comunicação segura entre o mestre do cluster e os nós. A interação com o mestre do cluster ocorre por meio das APIs do Kubernetes, como `kubectl` ou o painel do Kubernetes.

Esse mestre de cluster gerenciado significa que você não precisa configurar componentes como um repositório *etcd* altamente disponível, mas também significa que você não pode acessar o mestre de cluster diretamente. Os upgrades para o Kubernetes são orquestrados por meio do CLI do Azure ou do portal do Azure, que atualiza o mestre do cluster e, em seguida, os nós. Para solucionar possíveis problemas, você pode examinar os logs de mestre do cluster por meio do Logs do Azure Monitor.

Se precisar configurar o mestre de cluster de forma específica ou precisar de acesso direto a eles, você poderá implantar seu próprio cluster kubernetes usando o [AKs-Engine][aks-engine].

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para segurança e atualizações de cluster no AKs][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nós e pools de nós

Para executar seus aplicativos e serviços de suporte, é necessário um Kubernetes *nó*. Um cluster AKS tem um ou mais nós, que é uma máquina virtual (VM) do Azure que executa os componentes do nó e o tempo de execução do contêiner do Kubernetes:

- O `kubelet` é o agente do Kubernetes que processa as solicitações de orquestração do mestre do cluster e o agendamento da execução dos contêineres solicitados.
- A rede virtual é tratada pelos *kube-proxy* em cada nó. As rotas de proxy o tráfego de rede e gerencia o endereçamento IP para os serviços e os pods.
- O *tempo de execução do contêiner* é o componente que permite que aplicativos em contêiner sejam executados e interajam com recursos adicionais, como a rede virtual e o armazenamento. Em AKS, Moby é usado como o tempo de execução do contêiner.

![Máquina virtual do Azure e recursos de suporte para um nó do Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

O tamanho da VM do Azure para seus nós define quantas CPUs, quanto de memória e tamanho e tipo de armazenamento disponível (como SSD de alto desempenho ou HDD normal). Se você antecipar a necessidade de aplicativos que exijam grandes quantidades de CPU e memória ou armazenamento de alto desempenho, planeje o tamanho do nó de acordo. Você também pode aumentar o número de nós em seu cluster AKS para atender à demanda.

No AKS, a imagem de VM para os nós no cluster está atualmente baseada no Ubuntu Linux ou no Windows Server 2019. Quando você cria um cluster AKS ou aumenta o número de nós, a plataforma do Azure cria o número solicitado de VMs e as configura. Não há nenhuma configuração manual a ser executada. Os nós de agente são cobrados como máquinas virtuais padrão, portanto, os descontos que você tem no tamanho da VM que você está usando (incluindo as [reservas do Azure][reservation-discounts]) são aplicados automaticamente.

Se você precisar usar um sistema operacional host diferente, tempo de execução de contêiner ou incluir pacotes personalizados, poderá implantar seu próprio cluster kubernetes usando o [AKs-Engine][aks-engine]. O `aks-engine` upstream libera recursos e fornece opções de configuração antes que eles tenham suporte oficial nos clusters do AKS. Por exemplo, se você quiser usar um tempo de execução de contêiner diferente de Moby, poderá usar `aks-engine` para configurar e implantar um cluster kubernetes que atenda às suas necessidades atuais.

### <a name="resource-reservations"></a>Reservas de recursos

Os recursos de nó são utilizados pelo AKS para fazer a função de nó como parte do cluster. Isso pode criar um discrepency entre os recursos totais do seu nó e os recursos que se encontram quando usados em AKS. Isso é importante para observar ao definir solicitações e limites para pods implantados pelo usuário.

Para localizar os recursos de localização de um nó, execute:
```kubectl
kubectl describe node [NODE_NAME]

```

Para manter o desempenho e a funcionalidade do nó, os recursos são reservados em cada nó por AKS. À medida que um nó cresce mais em recursos, a reserva de recursos aumenta devido a uma quantidade maior de pods implantados pelo usuário que precisam de gerenciamento.

>[!NOTE]
> O uso de Complementos, como OMS, consumirá recursos de nó adicionais.

- A CPU reservada para **CPU** depende do tipo de nó e da configuração de cluster, o que pode causar menos inlocalizável de CPU devido à execução de recursos adicionais

| Núcleos de CPU no host | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube-reservado (milicores)|60|100|140|180|260|420|740|

- A reserva de **memória** da memória segue uma taxa progressiva
  - 25% dos primeiros 4 GB de memória
  - 20% dos próximos 4 GB de memória (até 8 GB)
  - 10% dos próximos 8 GB de memória (até 16 GB)
  - 6% dos próximos 112 GB de memória (até 128 GB)
  - 2% de qualquer memória acima de 128 GB

Essas reservas significam que a quantidade de CPU e memória disponíveis para seus aplicativos pode parecer menor do que o próprio nó contém. Se houver restrições de recursos devido ao número de aplicativos que você executa, essas reservas garantem que a CPU e a memória permaneçam disponíveis para os componentes principais do Kubernetes. As reservas de recursos não podem ser alteradas.

O SO do nó subjacente também requer uma certa quantidade de recursos de CPU e memória para concluir suas próprias funções principais.

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para recursos básicos do Agendador no AKs][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pools de nós

Os nós da mesma configuração são agrupados em *conjuntos de nós*. Um cluster Kubernetes contém um ou mais pools de nó. O número inicial de nós e o tamanho são definidos quando você cria um cluster AKS, que cria um *conjunto de nós padrão*. Esse pool de nó padrão no AKS contém as VMs subjacentes que executam o agente de nós. O suporte ao pool de vários nós está atualmente em visualização no AKS.

> [!NOTE]
> Para garantir que o cluster opere de forma confiável, você deve executar pelo menos 2 (dois) nós no pool de nós padrão.

Quando você dimensiona ou atualizar um cluster AKS, a ação é executada no pool de nó padrão. Você também pode optar por dimensionar ou atualizar um pool de nós específico. Para operações de atualização, os contêineres em execução são planejados em outros nós no conjunto de nós até que todos os nós sejam atualizados com êxito.

Para obter mais informações sobre como usar vários pools de nós no AKS, consulte [criar e gerenciar vários pools de nós para um cluster no AKs][use-multiple-node-pools].

### <a name="node-selectors"></a>Seletores de nó

Em um cluster AKS que contém vários pools de nós, talvez seja necessário informar ao agendador de kubernetes qual pool de nós usar para um determinado recurso. Por exemplo, controladores de entrada não devem ser executados em nós do Windows Server (atualmente em visualização no AKS). Os seletores de nó permitem definir vários parâmetros, como o sistema operacional do nó, para controlar onde um pod deve ser agendado.

O exemplo básico a seguir agenda uma instância de NGINX em um nó do Linux usando o seletor de nó *"beta.kubernetes.Io/os": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Para obter mais informações sobre como controlar onde os pods estão agendados, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pods

O Kubernetes usa *pods* para executar uma instância do seu aplicativo. Um pod representa uma única instância do seu aplicativo. Os pods normalmente têm um mapeamento de 1: 1 com um contêiner, embora haja cenários avançados em que um pod pode conter vários contêineres. Esses pods de vários contêineres são agendados juntos no mesmo nó e permitem que os contêineres compartilhem recursos relacionados.

Quando você cria um pod, você pode definir *limites de recursos* para solicitar uma determinada quantidade de recursos de CPU ou memória. O Kubernetes Scheduler tenta programar os pods para serem executados em um nó com recursos disponíveis para atender à solicitação. Você também pode especificar limites máximos de recursos que impedem que um determinado pod consuma muito recurso de computação do nó subjacente. Uma prática recomendada é incluir limites de recursos para todos os pods a fim de ajudar o Agendador do Kubernetes a entender quais recursos são necessários e permitidos.

Para obter mais informações, consulte [kubernetes pods][kubernetes-pods] e [ciclo de vida do pod kubernetes][kubernetes-pod-lifecycle].

Um pod é um recurso lógico, mas o (s) contêiner (es) é onde as cargas de trabalho do aplicativo são executadas. Pods são recursos descartáveis, normalmente efêmeros e pods programados individualmente perderem alguns dos recursos de redundância e disponibilidade alta que kubernetes fornece. Em vez disso, os pods geralmente são implementados e gerenciados pelos *Controladores* do Kubernetes, como o Deployment Controller.

## <a name="deployments-and-yaml-manifests"></a>Manifestos de implantações e YAML

Uma *implementação* representa um ou mais pods idênticos, gerenciados pelo Kubernetes Deployment Controller. Uma implantação define o número de *réplicas* (pods) a serem criadas e o Kubernetes Scheduler garante que, se os pods ou nós encontrarem problemas, os pods adicionais serão agendados em nós normais.

Você pode atualizar as implantações para alterar a configuração de pods, a imagem do contêiner usada ou o armazenamento anexado. O controlador de implantação drena e encerra um determinado número de réplicas, cria réplicas da nova definição de implantação e continua o processo até que todas as réplicas na implantação sejam atualizadas.

A maioria dos aplicativos sem monitoração de estado no AKS devem usar o modelo de implantação em vez de agendamento pods individuais. O Kubernetes pode monitorar a integridade e o status das implantações para garantir que o número necessário de réplicas seja executado dentro do cluster. Quando você agenda apenas pods individuais, os pods não serão reiniciados se encontrarem um problema e não serão reagendados em nós íntegros se o nó atual encontrar um problema.

Se um aplicativo exigir que um quorum de instâncias esteja sempre disponível para que as decisões de gerenciamento sejam tomadas, você não deseja que um processo de atualização interrompa essa capacidade. *Orçamentos de interrupção de pod* podem ser usados para definir quantas réplicas em uma implantação podem ser desativadas durante uma atualização ou atualização de nó. Por exemplo, se você tiver *5* réplicas em sua implantação, você pode definir uma interrupção de pod do *4* para permitir apenas uma réplica do que está sendo excluído/reagendada por vez. Assim como os limites de recursos do pod, uma prática recomendada é definir orçamentos de interrupção de pod em aplicativos que exigem que um número mínimo de réplicas esteja sempre presente.

Implantações geralmente são criadas e gerenciadas com o `kubectl create` ou `kubectl apply`. Para criar uma implantação, você define um arquivo de manifesto no formato YAML (YAML Ain't Markup Language). O exemplo a seguir cria uma implementação básica do servidor da Web NGINX. A implementação especifica *3* réplicas a serem criadas e que a porta *80* esteja aberta no contêiner. Solicitações de recursos e limites também são definidos para CPU e memória.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Aplicativos mais complexos podem ser criados incluindo também serviços como balanceadores de carga no manifesto YAML.

Para obter mais informações, consulte [implantações do kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gerenciamento de pacotes com Helm

Uma abordagem comum para gerenciar aplicativos no kubernetes é com o [Helm][helm]. Você pode criar e usar o Helm público existente *gráficos* que contêm uma versão empacotada do código do aplicativo e manifestos do Kubernetes YAML para implantar recursos. Esses gráficos de Helm podem ser armazenados localmente ou com frequência em um repositório remoto, como o repositório de [gráficos Helm do registro de contêiner do Azure][acr-helm].

Para usar o Helm, um componente de servidor chamado *Tiller* está instalado em seu cluster Kubernetes. O Tiller gerencia a instalação de gráficos dentro do cluster. O próprio cliente do Helm é instalado localmente no seu computador ou pode ser usado dentro do [Azure cloud Shell][azure-cloud-shell]. Você pode procurar ou criar gráficos de Helm com o cliente e instalá-los em seu cluster do Kubernetes.

![O Helm inclui um componente cliente e um componente Tiller do lado do servidor que cria recursos dentro do cluster do Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Para obter mais informações, consulte [install Applications with Helm in Azure kubernetes Service (AKs)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets e DaemonSets

O Deployment Controller usa o Kubernetes Scheduler para executar um determinado número de réplicas em qualquer nó disponível com recursos disponíveis. Essa abordagem de uso de implantações pode ser suficiente para aplicativos sem estado, mas não para aplicativos que exigem uma convenção ou armazenamento de nomenclatura persistente. Para aplicativos que exigem uma réplica existir em cada nó ou nós selecionados, em um cluster, o controlador de implantação não examina como réplicas são distribuídas entre os nós.

Existem dois recursos do Kubernetes que permitem gerenciar esses tipos de aplicativos:

- *StatefulSets* -manter o estado de aplicativos além do ciclo de vida um pod individual, como o armazenamento.
- *DaemonSets* - Assegure uma instância em execução em cada nó, no início do processo de bootstrap do Kubernetes.

### <a name="statefulsets"></a>StatefulSets

O desenvolvimento moderno de aplicativos geralmente visa aplicativos sem estado, mas os *StatefulSets* podem ser usados para aplicativos com estado, como aplicativos que incluem componentes de banco de dados. Um StatefulSet é semelhante a uma implantação em que um ou mais pods idênticos são criados e gerenciados. As réplicas em um StatefulSet seguem uma abordagem detalhada e sequencial para implantação, dimensionamento, upgrades e terminações. Com um StatefulSet, a convenção de nomenclatura, os nomes de rede e o armazenamento persistem à medida que as réplicas são reprogramadas.

Você define o aplicativo no formato YAML usando `kind: StatefulSet` e o StatefulSet Controller manipula a implementação e o gerenciamento das réplicas necessárias. Os dados são gravados no armazenamento persistente, fornecido pelos discos gerenciados do Azure ou pelos arquivos do Azure. Com StatefulSets, o armazenamento persistente subjacente permanece mesmo quando o StatefulSet é excluído.

Para obter mais informações, consulte [kubernetes StatefulSets][kubernetes-statefulsets].

Réplicas de um StatefulSet sejam agendadas e executadas em qualquer nó disponível em um cluster do AKS. Se você precisa garantir que pelo menos um pod em seu conjunto seja executado em um nó, você pode usar um DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Para necessidades específicas de coleta ou monitoramento de logs, talvez seja necessário executar um determinado pod em todos os nós selecionados. Um *DaemonSet* é novamente usado para implantar um ou mais pods idênticos, mas o DaemonSet Controller assegura que cada nó especificado execute uma instância do pod.

O DaemonSet do controlador pode agendar os pods em nós no início do processo de inicialização do cluster, antes do padrão Agendador Kubernetes foi iniciada. Essa capacidade garante que os pods em um DaemonSet sejam iniciados antes de pods tradicionais em uma implantação ou StatefulSet estão agendados.

Como StatefulSets, um DaemonSet é definido como parte de uma definição YAML usando `kind: DaemonSet`.

Para obter mais informações, consulte [kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Se você estiver usando o [complemento de nós virtuais](virtual-nodes-cli.md#enable-virtual-nodes-addon), o DaemonSets não criará pods no nó virtual.

## <a name="namespaces"></a>Namespaces

Os recursos do Kubernetes, como pods e Deployments, são logicamente agrupados em um *namespace*. Esses agrupamentos fornecem uma maneira de dividir logicamente um cluster AKS e restringir o acesso para criar, exibir ou gerenciar recursos. Você pode criar namespaces para separar grupos de negócios, por exemplo. Os usuários podem interagir apenas com recursos dentro de seus namespaces atribuídos.

![Espaços de nomes do Kubernetes para dividir logicamente recursos e aplicativos](media/concepts-clusters-workloads/namespaces.png)

Quando você cria um cluster do AKS, os namespaces a seguir estão disponíveis:

- *padrão* -este namespace é onde os pods e implantações são criadas por padrão quando nenhum seja fornecido. Em ambientes menores, você pode implantar aplicativos diretamente no namespace padrão sem criar separações lógicas adicionais. Quando você interage com a API do Kubernetes, como com `kubectl get pods`, o namespace padrão é usado quando nenhum é especificado.
- *kube-system* - Este namespace é o local onde os recursos principais existem, como recursos de rede como DNS e proxy, ou o painel do Kubernetes. Normalmente, você não implantar seus próprios aplicativos para esse namespace.
- *kube-public* – esse namespace normalmente não é usado, mas pode ser usado para que os recursos sejam visíveis em todo o cluster e possam ser visualizados por todos os usuários.

Para obter mais informações, consulte [namespaces do kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Próximas etapas

Este artigo aborda alguns dos componentes principais do Kubernetes e como elas se aplicam aos clusters AKS. Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Acesso e identidade de kubernetes/AKS][aks-concepts-identity]
- [Segurança do kubernetes/AKS][aks-concepts-security]
- [Redes virtuais kubernetes/AKS][aks-concepts-network]
- [Armazenamento kubernetes/AKS][aks-concepts-storage]
- [Escala de kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md