---
title: Explorar os logs de rastreamento do Java no Azure Application Insights | Microsoft Docs
description: Pesquisar rastreamentos Log4J ou Logback no Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 99a18125fa5ce07a46fcc1af554fd03746ebf110
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038142"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Explore os logs de rastreamento de Java no Application Insights
Se você estiver usando Logback ou Log4J (v 1.2 ou 2.0) para rastreamento, você pode enviar seus logs de rastreamento automaticamente para o Application Insights, no qual você pode explorá-los e pesquisar o conteúdo deles.

> [!TIP]
> Você só precisa definir sua chave de instrumentação Application Insights uma vez para seu aplicativo. Se você estiver usando uma estrutura como o Java Spring, talvez já tenha registrado a chave em outro lugar na configuração do aplicativo.

## <a name="using-the-application-insights-java-agent"></a>Usando o agente Java Application Insights

Você pode configurar o Application Insights agente Java para capturar seus logs automaticamente, habilitando o recurso no arquivo `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Como alternativa, você pode seguir as instruções abaixo.

## <a name="install-the-java-sdk"></a>Instalar o SDK do Java

Siga as instruções para instalar [Application insights SDK para Java][java], caso ainda não tenha feito isso.

## <a name="add-logging-libraries-to-your-project"></a>Adicionar bibliotecas de log ao seu projeto
*Escolha o modo apropriado para seu projeto.*

#### <a name="if-youre-using-maven"></a>Se você estiver usando o Maven...
Se o seu projeto já estiver configurado para usar o Maven para compilação, realize a mesclagem de um dos seguintes snippets de código ao seu arquivo pom.xml.

Em seguida, atualize as dependências do projeto para obter os binários baixados.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Se você estiver usando o Gradle...
Se seu projeto já está configurado para usar Gradle para compilação, adicione uma das linhas a seguir para o grupo `dependencies` em seu arquivo build.gradle:

Em seguida, atualize as dependências do projeto para obter os binários baixados.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Caso contrário...
Siga as orientações para instalar manualmente o SDK do Java do Application Insights, baixe o jar (após chegar na Página Central do Maven, clique no link para "jar" na seção de download) correspondente ao appender apropriado e adicione-o ao projeto.

| Agente | Download | Biblioteca |
| --- | --- | --- |
| Logback |[Jar do appender de Logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Jar do appender de Log4J v2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Jar do appender de Log4J v1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Adicionar o appender à sua estrutura de log
Para começar a obter rastreamentos, mescle o snippet de código relevante ao arquivo de configuração Log4J ou Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Os appenders Application Insights podem ser referenciados por qualquer agente configurado e não necessariamente pelo agente raiz (conforme mostrado nos exemplos de código acima).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Explorar seus rastreamentos no portal do Application Insights
Agora que você configurou seu projeto para enviar rastreamentos para Application Insights, você pode exibir e Pesquisar esses rastreamentos no portal de Application Insights, na folha [Pesquisar][diagnostic] .

Exceções enviadas por meio de agentes serão exibidas no portal como Telemetria de exceção.

![No portal do Application Insights, abra a Pesquisa](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Próximas etapas
[Pesquisa de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


