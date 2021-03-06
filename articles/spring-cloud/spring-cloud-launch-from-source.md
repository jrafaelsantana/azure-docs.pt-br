---
title: 'Início Rápido: Iniciar seu aplicativo Spring Cloud a partir do código-fonte'
description: Saiba como iniciar seu aplicativo de nuvem Spring do Azure diretamente do seu código-fonte
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 445cac1494828362d54a8c15e68d27f01b165841
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170527"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>Iniciar seu aplicativo Spring Cloud a partir do código-fonte

O Azure Spring Cloud permite que você inicie seu aplicativo diretamente do código-fonte Java ou de um JAR predefinido. Este artigo orienta você pelas etapas necessárias.

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> Antes de iniciar este guia de início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud.  Como um serviço de visualização, pedimos que você entre em contato conosco para que possamos adicionar sua assinatura à nossa lista de permissões.  Se você quiser explorar os recursos do Azure Spring Cloud, [fill este formulário @ no__t-1.

Antes de começar, verifique se sua assinatura do Azure tem as dependências necessárias:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Instalar o Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/free/)

> [!TIP]
> O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure com o seguinte comando

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Provisionar uma instância de serviço usando o CLI do Azure

Faça logon no CLI do Azure e escolha sua assinatura ativa. Certifique-se de escolher a assinatura ativa que está na lista de permissões para o Azure Spring Cloud

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Abra uma janela de CLI do Azure e execute os comandos a seguir para provisionar uma instância do Azure Spring Cloud. Observe que também dizemos ao Azure Spring Cloud para atribuir um domínio público aqui.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

A instância do serviço levará cerca de cinco minutos para ser implantada.

Defina o nome do grupo de recursos padrão e o nome do cluster usando os seguintes comandos:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Criar o aplicativo Spring Cloud

O comando a seguir cria um aplicativo Spring Cloud em sua assinatura.  Isso cria um serviço de nuvem Spring vazio no qual podemos carregar nosso aplicativo.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Implantar seu aplicativo Spring Cloud

Você pode implantar seu aplicativo de um JAR predefinido ou de um repositório gradle ou Maven.  Encontre instruções para cada caso abaixo.

### <a name="deploy-a-built-jar"></a>Implantar um JAR criado

Para implantar a partir de um JAR criado em seu computador local, verifique se sua compilação produz um [Fat-jar](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Para implantar o Fat-JAR em uma implantação ativa

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Para implantar o Fat-JAR em uma implantação específica

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Implantar do código-fonte

O Azure Spring Cloud usa o [kpack](https://github.com/pivotal/kpack) para criar seu projeto.  Você pode usar CLI do Azure para carregar seu código-fonte, criar seu projeto usando kpack e implantá-lo no aplicativo de destino.

> [!WARNING]
> O projeto deve produzir apenas um arquivo JAR com uma entrada `main-class` no `MANIFEST.MF` no `target` (para implantações Maven ou `build/libs` (para implantações gradle).  Vários arquivos JAR com entradas `main-class` farão com que a implantação falhe.

Para projetos do único módulo Maven/gradle:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Para projetos Maven/gradle com vários módulos, repita para cada módulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Mostrar logs de implantação

Examine os logs de compilação do kpack usando o seguinte comando:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Os logs do kpack só mostrarão a implantação mais recente se essa implantação tiver sido criada a partir da origem usando kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto de extremidade público ao gateway

1. Abra a página **painel do aplicativo** .
2. Selecione o aplicativo `gateway` para mostrar a página de **detalhes do aplicativo** .
3. Selecione **atribuir domínio** para atribuir um ponto de extremidade público ao gateway. Isso pode demorar alguns minutos. 
4. Insira o IP público atribuído em seu navegador para exibir o aplicativo em execução.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microserviço localmente
> * Implantar cada microserviço
> * Editar variáveis de ambiente para aplicativos
> * Atribuir IP público para o gateway de aplicativo

> [!div class="nextstepaction"]
> [Preparar seu aplicativo do Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
