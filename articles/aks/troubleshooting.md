---
title: Solucionar problemas comuns do Serviço do Azure Kubernetes
description: Aprenda a solucionar problemas comuns ao usar o Serviço de Kubernetes do Azure (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: d2561b1882ea612f29c0ff0eeb4bd6614403c9ff
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025488"
---
# <a name="aks-troubleshooting"></a>Solução de problemas do AKS

Quando você cria ou gerencia clusters do Serviço de Kubernetes do Azure (AKS), você pode ocasionalmente encontrar problemas. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre como depurar problemas do Kubernetes?

Experimente o [guia oficial para solucionar problemas de clusters de Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Há também um [guia de solução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) publicado por um engenheiro da Microsoft sobre solução de problemas de pods, nós, clusters e outros recursos.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de “cota excedida” durante a criação ou atualização. O que devo fazer? 

Você precisa [solicitar núcleos](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a configuração máxima de pods por nó para o AKS?

A configuração máxima de pods por nó é 30 por padrão, se você implantar um cluster AKS no portal do Azure.
A configuração máxima de pods por nó é 110 por padrão, se você implantar um cluster AKS na CLI do Azure. (Verifique se está usando a versão mais recente da CLI do Azure). Essa configuração padrão pode ser alterada usando o sinalizador `–-max-pods` no comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo o erro “insuficienteSubnetSize” ao implantar um cluster AKS com a rede avançada. O que devo fazer?

Se o CNI (rede avançada) do Azure for usado, o AKS pré-aloca o endereço IP com base no máximo de pods por nó configurado. O número de nós em um cluster AKS pode estar em qualquer lugar entre 1 e 110. Com base no máximo de pods por nó, o tamanho da sub-rede deve ser maior que o "produto do número de nós e o máximo de pods por nó". A seguinte equação básica descreve isso:

Tamanho da sub-rede > número de nós no cluster (levando em consideração os futuros requisitos de escala) * máximo de pods por nó.

Para saber mais, confira [Planejar o endereçamento IP para o cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está preso no modo CrashLoopBackOff. O que devo fazer?

Pode haver vários motivos para o pod trave nesse modo. Você pode examinar:

* O próprio pod, usando `kubectl describe pod <pod-name>`.
* Os logs, usando `kubectl log <pod-name>`.

Para obter mais informações sobre como solucionar problemas de pod, consulte [Depurar aplicativos](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou tentando habilitar RBAC em um cluster existente. Como posso fazer isso?

Infelizmente, a ativação do controle de acesso baseado em função (RBAC) em clusters existentes não é suportada no momento. Você deve explicitamente criar novos clusters. Se você usar a CLI, o RBAC é habilitado por padrão. Se você usar o portal do AKS, um botão de alternância para habilitar o RBAC está disponível no fluxo de trabalho de criação.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Eu criei um cluster com o RBAC habilitado usando a CLI do Azure com padrões ou o portal do Azure, agora vejo vários avisos no painel do Kubernetes. O painel costumava trabalhar sem nenhum aviso. O que devo fazer?

O motivo para os avisos no painel é que agora o cluster está habilitado com o RBAC e o acesso a ele foi desabilitado por padrão. Em geral, essa abordagem é uma boa prática, já que a exposição padrão do painel a todos os usuários do cluster pode levar a ameaças de segurança. Se você ainda quiser ativar o painel, siga os passos nesta [postagem de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não consigo conectar-me ao painel. O que devo fazer?

A maneira mais fácil de acessar seu serviço fora do cluster é executar `kubectl proxy`, que enviará solicitações de proxy para sua porta local 8001 ao servidor Kubernetes API. A partir daí, o servidor de API pode usar um proxy ao seu serviço: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se você não vir o painel do Kubernetes, verifique se o pod `kube-proxy` está em execução no namespace `kube-system`. Se não estiver em estado de execução, exclua o pod e ele será reiniciado.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo obter os logs, usando kubectl logs ou não é possível conectar-se ao servidor de API. Estou recebendo "erro do servidor: erro ao discar back-end: discar TCP...". O que devo fazer?

Verifique se o grupo de segurança de rede padrão não foi modificado e se a porta 22 e 9000 estão abertas para conexão com o servidor de API. Verifique se o Pod `tunnelfront` está em execução no namespace *Kube-System* usando o comando `kubectl get pods --namespace kube-system`. Se não estiver, force a exclusão do pod e ele será reiniciado.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Eu estou tentando atualizar ou dimensionar e estou recebendo erro "mensagem: Não é permitido alterar a propriedade 'imageReference'". Como faço para corrigir esse problema?

Você pode estar recebendo este erro porque modificou as tags nos nós do agente dentro do cluster do AKS. Modificar e excluir tags e outras propriedades de recursos no grupo de recursos MC_ * pode levar a resultados inesperados. Modificar os recursos sob o grupo MC_ * no cluster do AKS quebra o objetivo de nível de serviço (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou recebendo erros de que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que seja corrigido

*Esta assistência para solução de problemas é direcionada do https://aka.ms/aks-cluster-failed*

Esse erro ocorre quando os clusters entram em um estado de falha por vários motivos. Siga as etapas abaixo para resolver o estado de falha do cluster antes de repetir a operação que falhou anteriormente:

1. Até que o cluster esteja fora do estado `failed`, as operações `upgrade` e `scale` não terão sucesso. As resoluções e problemas de raiz comuns incluem:
    * Dimensionamento com **cota de computação insuficiente (CRP)** . Para resolver, primeiro dimensione o cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga estas [etapas para solicitar um aumento de cota de computação](../azure-supportability/resource-manager-core-quotas-request.md) antes de tentar escalar verticalmente novamente além dos limites de cota iniciais.
    * Dimensionamento de um cluster com rede avançada e **recursos de sub-rede (rede) insuficientes**. Para resolver, primeiro dimensione o cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga [estas etapas para solicitar um aumento de cota de recursos](../azure-resource-manager/resource-manager-quota-errors.md#solution) antes de tentar escalar verticalmente novamente além dos limites de cota iniciais.
2. Depois que a causa subjacente da falha de atualização for resolvida, o cluster deverá estar em um estado com êxito. Quando um estado bem-sucedido for verificado, repita a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Estou recebendo erros ao tentar atualizar ou dimensionar o estado em que meu cluster está sendo atualizado no momento ou com falha na atualização

*Esta assistência para solução de problemas é direcionada do https://aka.ms/aks-pending-upgrade*

As operações de atualização e dimensionamento em um cluster com um único pool de nós ou um cluster com [vários pools de nós](use-multiple-node-pools.md) são mutuamente exclusivas. Você não pode ter um cluster ou pool de nós simultaneamente para atualizar e dimensionar. Em vez disso, cada tipo de operação deve ser concluído no recurso de destino antes da próxima solicitação no mesmo recurso. Como resultado, as operações são limitadas quando as operações de atualização ativa ou de escala estão ocorrendo ou tentadas e subsequentemente falharam. 

Para ajudar a diagnosticar o problema, execute `az aks show -g myResourceGroup -n myAKSCluster -o table` para recuperar o status detalhado no cluster. Com base no resultado:

* Se o cluster estiver sendo atualizado ativamente, aguarde até que a operação seja encerrada. Se tiver êxito, repita a operação anterior com falha novamente.
* Se o cluster tiver falhado na atualização, siga as etapas descritas na seção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mover meu cluster para uma assinatura diferente ou minha assinatura com meu cluster para um novo locatário?

Se você moveu o cluster AKS para uma assinatura diferente ou o cluster que possui a assinatura para um novo locatário, o cluster perderá a funcionalidade devido à perda de atribuições de função e aos direitos de entidades de serviço. **AKs não dá suporte à movimentação de clusters entre assinaturas ou locatários** devido a essa restrição.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou recebendo erros ao tentar usar recursos que exigem conjuntos de dimensionamento de máquinas virtuais

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-vmss-enablement*

Você pode receber erros que indicam que o cluster AKS não está em um conjunto de dimensionamento de máquinas virtuais, como o exemplo a seguir:

**O AgentPool ' AgentPool ' definiu o dimensionamento automático como habilitado, mas não está em conjuntos de dimensionamento de máquinas virtuais**

Para usar recursos como os pools de dimensionamento de vários nós ou do cluster, os clusters AKS devem ser criados para usar conjuntos de dimensionamento de máquinas virtuais. Os erros são retornados se você tentar usar recursos que dependem de conjuntos de dimensionamento de máquinas virtuais e você tiver como alvo um cluster AKS de conjunto de dimensionamento de máquinas não virtuais regular.

Siga as etapas *antes de começar* no documento apropriado para criar corretamente um cluster AKs:

* [Usar o dimensionamento de cluster](cluster-autoscaler.md)
* [Criar e usar vários pools de nós](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quais restrições de nomenclatura são impostas para recursos e parâmetros AKS?

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-naming-rules*

As restrições de nomenclatura são implementadas pela plataforma do Azure e AKS. Se um nome de recurso ou parâmetro quebrar uma dessas restrições, será retornado um erro solicitando que você forneça uma entrada diferente. As seguintes diretrizes de nomenclatura comuns se aplicam:

* O nome do grupo de recursos AKS *MC_* combina o nome do grupo de recursos e o nome do recurso. A sintaxe gerada automaticamente de `MC_resourceGroupName_resourceName_AzureRegion` não deve ser maior que 80 caracteres. Se necessário, reduza o tamanho do nome do grupo de recursos ou do nome do cluster AKS.
* O *dnsPrefix* deve começar e terminar com valores alfanuméricos. Os caracteres válidos incluem valores alfanuméricos e hifens (-). O *dnsPrefix* não pode incluir caracteres especiais, como um ponto (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou recebendo erros ao tentar criar, atualizar, dimensionar, excluir ou atualizar o cluster. essa operação não é permitida porque outra operação está em andamento.

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-pending-operation*

As operações de cluster são limitadas quando uma operação anterior ainda está em andamento. Para recuperar um status detalhado do cluster, use o comando `az aks show -g myResourceGroup -n myAKSCluster -o table`. Use seu próprio grupo de recursos e o nome do cluster AKS, conforme necessário.

Com base na saída do status do cluster:

* Se o cluster estiver em qualquer estado de provisionamento diferente de *êxito* ou *falha*, aguarde até que a operação (*atualização/atualização/criação/dimensionamento/exclusão/migração*) seja encerrada. Quando a operação anterior for concluída, tente novamente a operação de cluster mais recente.

* Se o cluster tiver uma falha de atualização, siga as etapas descritas estou [recebendo erros de que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que seja corrigido](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Estou recebendo erros de que minha entidade de serviço não foi encontrada quando tento criar um novo cluster sem passar um existente.

Ao criar um cluster AKS, ele requer uma entidade de serviço para criar recursos em seu nome. O AKS oferece a capacidade de ter um novo criado no momento da criação do cluster, mas isso requer que Azure Active Directory propague totalmente a nova entidade de serviço em um tempo razoável para que o cluster tenha sucesso na criação. Quando essa propagação demorar muito, o cluster falhará na validação para criar, pois ele não pode encontrar uma entidade de serviço disponível para fazer isso. 

Use as seguintes soluções alternativas para isso:
1. Use uma entidade de serviço existente que já propagada entre regiões e exista para passar para AKS no momento da criação do cluster.
2. Se você estiver usando scripts de automação, adicione atrasos entre a criação da entidade de serviço e a criação do cluster AKS.
3. Se estiver usando portal do Azure, retorne para as configurações de cluster durante a criação e repita a página de validação após alguns minutos.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Estou recebendo erros depois de restringir o tráfego de egresso

Ao restringir o tráfego de saída de um cluster AKS, há regras de saída/portas de rede [recomendadas e opcionais necessárias](limit-egress-traffic.md) e regras de FQDN/aplicativo para AKs. Se suas configurações estiverem em conflito com qualquer uma dessas regras, talvez você não possa executar determinados comandos `kubectl`. Você também pode ver erros ao criar um cluster AKS.

Verifique se as configurações não estão em conflito com nenhuma das portas de rede/regras de saída necessárias ou opcionais recomendadas ou regras de FQDN/aplicativo.
