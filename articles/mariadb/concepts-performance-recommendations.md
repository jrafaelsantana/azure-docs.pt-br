---
title: Recomendações de desempenho no banco de dados do Azure para MariaDB
description: Este artigo descreve o recurso de recomendação de desempenho no banco de dados do Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: b363a994024b4a53703b6107ef4190129e900547
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950644"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Recomendações de desempenho no banco de dados do Azure para MariaDB

**Aplica-se a:** Banco de dados do Azure para MariaDB 10,2

> [!IMPORTANT]
> As recomendações de desempenho estão em versão prévia.

O recurso de recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizadas para melhorar o desempenho. Para produzir as recomendações, a análise examina várias características do banco de dados, incluindo o esquema. Habilite [repositório de consultas](concepts-query-store.md) no seu servidor para utilizar totalmente o recurso de recomendações de desempenho. Se o esquema de desempenho estiver desativado, ativar Repositório de Consultas habilita o performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para o recurso. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações.

## <a name="permissions"></a>Permissões

**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho

O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **recomendações de desempenho** na seção de **desempenho inteligente** da barra de menus na página de portal do Azure para seu servidor do MariaDB.

![Página das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **analisar** e escolha um banco de dados, que iniciará a análise. Dependendo de sua carga de trabalho, a análise pode levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. A análise executa um exame profundo do seu banco de dados. Recomendamos que você execute a análise fora dos períodos de pico.

A janela **recomendações** mostrará uma lista de recomendações, se forem encontradas e a ID de consulta relacionada que gerou essa recomendação. Com a ID de consulta, você pode usar a exibição [MySQL. query_store](concepts-query-store.md#mysqlquery_store) para saber mais sobre a consulta.

![Nova página de recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e execute-o do seu cliente de sua escolha. Lembre-se de testar e monitorar para avaliar a recomendação.

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, há suporte apenas para criar recomendações de *índice* .

### <a name="create-index-recommendations"></a>Criar recomendações de índice

*Criar* recomendações de índice sugerem novos índices para acelerar a execução com mais frequência ou as consultas demoradas na carga de trabalho. Este tipo de recomendação requer que [repositório de consultas](concepts-query-store.md) seja habilitado. Repositório de Consultas coleta informações de consulta e fornece as estatísticas detalhadas de tempo de execução de consulta e frequência que a análise usa para fazer a recomendação.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no banco de dados do Azure para MariaDB.