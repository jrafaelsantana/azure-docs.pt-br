---
title: Recomendações de desempenho no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso de recomendação de desempenho no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 5f1b64753d19158b17d4de1b3fbbe50d30ea0254
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764679"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Recomendações de desempenho no banco de dados do Azure para PostgreSQL-servidor único

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

O recurso de recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizadas para melhorar o desempenho. Para produzir as recomendações, a análise examina várias características do banco de dados, incluindo o esquema. Habilite [repositório de consultas](concepts-query-store.md) no seu servidor para utilizar totalmente o recurso de recomendações de desempenho. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho
O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **recomendações de desempenho** na seção de **desempenho inteligente** da barra de menus na página portal do Azure para o servidor PostgreSQL.

![Página das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **analisar** e escolha um banco de dados, que iniciará a análise. Dependendo de sua carga de trabalho, a análise de th pode levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. A análise executa um exame profundo do seu banco de dados. Recomendamos que você execute a análise fora dos períodos de pico. 

A janela **recomendações** mostrará uma lista de recomendações, se alguma for encontrada.

![Nova página de recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e execute-o do seu cliente de sua escolha. Lembre-se de testar e monitorar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, há suporte para dois tipos de recomendações: *Criar índice* e *drop index*.

### <a name="create-index-recommendations"></a>Criar recomendações de índice
*Criar* recomendações de índice sugerem novos índices para acelerar a execução com mais frequência ou as consultas demoradas na carga de trabalho. Este tipo de recomendação requer que [repositório de consultas](concepts-query-store.md) seja habilitado. Repositório de Consultas coleta informações de consulta e fornece as estatísticas detalhadas de tempo de execução de consulta e frequência que a análise usa para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações para Remover Índice
Além de detectar índices ausentes, o banco de dados do Azure para PostgreSQL analisa o desempenho dos índices existentes. Se um índice for raramente usado ou redundante, o analisador recomendará soltá-lo.

## <a name="considerations"></a>Considerações
* As recomendações de desempenho não estão disponíveis para [réplicas de leitura](concepts-read-replicas.md).
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.

