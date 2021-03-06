---
title: Usar um controlador de entrada do traefik personalizado e configurar o HTTPS
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Saiba como configurar Azure Dev Spaces para usar um controlador de entrada do traefik personalizado e configurar o HTTPS usando esse controlador de entrada
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 50908bde65b69cb475391cd30bca758dd571f114
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036937"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Usar um controlador de entrada do traefik personalizado e configurar o HTTPS

Este artigo mostra como configurar Azure Dev Spaces para usar um controlador de entrada do traefik personalizado. Este artigo também mostra como configurar esse controlador de entrada personalizado para usar HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma, poderá [criar uma conta gratuita][azure-account-create].
* A [CLI do Azure][az-cli] instalada.
* [Cluster do serviço de kubernetes do Azure (AKs) com Azure dev Spaces habilitado][qs-cli].
* [kubectl][kubectl] instalado.
* [Helm 2.13 ou superior instalado][helm-installed].
* [Um domínio personalizado][custom-domain] com uma [zona DNS][dns-zone] no mesmo grupo de recursos que o cluster AKs.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configurar um controlador de entrada do traefik personalizado

Conecte-se ao cluster usando o [kubectl][kubectl], o cliente de linha de comando do kubernetes. Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Para verificar a conexão ao seu cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista de nós do cluster.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Crie um namespace kubernetes para o controlador de entrada traefik e instale-o `helm`usando.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Obtenha o endereço IP do serviço do controlador de entrada traefik usando o [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída de exemplo mostra os endereços IP para todos os serviços no espaço de nome *traefik* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um registro a à zona DNS com o endereço IP externo do serviço traefik usando [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

O exemplo acima adiciona um registro a à zona DNS *MY_CUSTOM_DOMAIN* .

Neste artigo, você usa o [Aplicativo de exemplo de Compartilhamento de Bicicletas do Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar o uso do Azure Dev Spaces. Clone o aplicativo do GitHub e navegue para seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Abra [Values. YAML][values-yaml] e substitua todas as instâncias de *< REPLACE_ME_WITH_HOST_SUFFIX >* por *traefik. MY_CUSTOM_DOMAIN* usando seu domínio para *MY_CUSTOM_DOMAIN*. Substitua também *kubernetes.Io/ingress.class: traefik-azds # dev Spaces – specific* com *kubernetes.Io/ingress.class: Traefik # entrada personalizada*. Abaixo está um exemplo de um arquivo `values.yaml` atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Salve suas alterações e feche o arquivo.

Implante o aplicativo de exemplo `helm install`usando.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

O exemplo acima implanta o aplicativo de exemplo no namespace do *dev* .

Selecione o espaço de *desenvolvimento* com seu aplicativo de `azds space select` exemplo usando e exiba as URLs para acessar o aplicativo `azds list-uris`de exemplo usando.

```console
azds space select -n dev
azds list-uris
```

A saída abaixo mostra as URLs de exemplo `azds list-uris`de.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *bikesharingweb* abrindo a URL pública com o comando `azds list-uris`. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

Use o `azds space select` comando para criar um espaço filho em *dev* e liste as URLs para acessar o espaço de desenvolvimento filho.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

A saída abaixo mostra as URLs de exemplo `azds list-uris` de para acessar o aplicativo de exemplo no espaço de desenvolvimento de *azureuser1* filho.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *bikesharingweb* no espaço de desenvolvimento filho do *azureuser1* abrindo a `azds list-uris` URL pública do comando. No exemplo acima, a URL pública para o serviço *bikesharingweb* no espaço de desenvolvimento filho *azureuser1* é `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configurar o controlador de entrada traefik para usar HTTPS

Crie um `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` arquivo semelhante ao exemplo abaixo. Atualize o valor de *email* com seu próprio email, que é usado para gerar o certificado com o que está criptografado.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Atualize seu serviço *traefik* usando `helm repo update` e incluindo o arquivo *traefik-Values. YAML* que você criou.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

O comando acima executa uma nova versão do serviço traefik usando os valores de *traefik-Values. YAML* e remove o serviço anterior. O serviço traefik também cria um certificado TLS usando vamos criptografar e começa a redirecionar o tráfego da Web para usar HTTPS.

> [!NOTE]
> Pode levar alguns minutos para que a nova versão do serviço traefik seja iniciada. Você pode verificar o progresso usando `kubectl get pods --namespace traefik --watch`.

Navegue até o aplicativo de exemplo no espaço filho *dev/azureuser1* e observe que você é redirecionado para usar HTTPS. Observe também que a página é carregada, mas o navegador mostra alguns erros. Abrir o console do navegador mostra o erro relacionado a uma página HTTPS tentando carregar recursos HTTP. Por exemplo:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Para corrigir esse erro, atualize [BikeSharingWeb/azds. YAML][azds-yaml] para usar *traefik* para *kubernetes.Io/ingress.Class* e seu domínio personalizado para *$ (hostSuffix)* . Por exemplo:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Atualize [BikeSharingWeb/Package. JSON][package-json] com uma dependência para o pacote de *URL* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Atualize o método *getApiHostAsync* em [BikeSharingWeb/Pages/Helpers. js][helpers-js] para usar https:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Navegue até o `BikeSharingWeb` diretório e use `azds up` para executar o serviço BikeSharingWeb atualizado.

```console
cd BikeSharingWeb/
azds up
```

Navegue até o aplicativo de exemplo no espaço filho *dev/azureuser1* e observe que você é redirecionado para usar HTTPS sem erros.

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml