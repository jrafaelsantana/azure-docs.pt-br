---
title: Limites de recursos do servidor do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do servidor do Banco de Dados SQL do Azure para bancos de dados individuais e pools elásticos. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 04/18/2019
ms.openlocfilehash: 175f694cbe46f871349136c9ce91888b6de48d21
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566856"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limites de recursos de Banco de Dados SQL para servidor do Banco de Dados SQL do Azure

Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL para um servidor do Banco de Dados SQL que gerencia bancos de dados individuais e pools elásticos. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.

> [!NOTE]
> Para limites de Instâncias Gerenciadas, confira [Limites de recursos do Banco de Dados SQL para Instâncias Gerenciadas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bancos de dados por servidor | 5\.000 |
| Número padrão de servidores por assinatura por região | 20 |
| Número máximo padrão de servidores por assinatura por região | 200 |  
| DTU / cota de eDTU por servidor | 54.000 |  
| Cota de vCore por servidor/instância | 540 |
| Pools de máx por servidor | Limitado pelo número de DTUs ou vCores. Por exemplo, se cada pool tiver 1.000 DTUs, um servidor poderá dar suporte a 54 pools.|
|||

> [!NOTE]
> Para obter mais cotas DTU / eDTU, vCore ou mais servidores do que o valor padrão, uma nova solicitação de suporte pode ser enviada no portal do Azure para a assinatura com o tipo de problema "Cota". O DTU / eDTU limite de cota e o banco de dados por servidor restringe o número de pools Elásticos por servidor.
> [!IMPORTANT]
> Conforme o número de bancos de dados vai se aproximando do limite por servidor do Banco de Dados SQL, pode ocorrer o seguinte:
> - Aumento de latência nas consultas em execução no banco de dados mestre.  Isso inclui modos de exibição de estatísticas de utilização de recursos, como sys.resource_stats.
> - Aumento de latência nas operações de gerenciamento e pontos de vista do portais de renderização que envolvem a enumeração de bancos de dados no servidor.

### <a name="storage-size"></a>Tamanho do armazenamento
- Para bancos de dados individuais rources, consulte limites de [recursos baseados em DTU](sql-database-dtu-resource-limits-single-databases.md) ou [limites de recursos baseados em vCore](sql-database-vcore-resource-limits-single-databases.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados são atingidos

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs/ vCores)

Quando a utilização de computação de banco de dados (medida por DTUs e eDTUs ou vCores) torna-se alta, a latência da consulta aumenta e pode até mesmo atingir o tempo limite. Sob essas condições, as consultas poderão ser colocadas em fila pelo serviço e receberão recursos para execução à medida que os recursos forem liberados.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o tamanho da computação do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais recursos de computação. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md). Os SELECTS e DELETES do banco de dados continuarão a ter êxito.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo do banco de dados ou pool elástico ou adicione mais armazenamento. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, o banco de dados poderá ser movido para fora do pool, de modo que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Encolher um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço no arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações)

O número máximo de sessões e trabalhos é determinado pela camada de serviço e tamanho da computação (DTUs e eDTUs). Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante os períodos de pico de carga, quando os limites de recurso de banco de dados são atingidos e os trabalhos acumulam devido a consultas de execução mais longas.

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:

- Aumentar a camada de serviço ou o tamanho da computação do banco de dados ou do pool elástico. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Governança de taxa de log de transações 
A governança de taxa do log de transações é um processo no banco de dados SQL do Azure usado para limitar altas taxas de ingestão para cargas de trabalho, como inserção em massa, seleção INTO e compilações de índice. Esses limites são rastreados e aplicados no nível de subsegundos à taxa de geração de registro de log, limitando a taxa de transferência, independentemente de quantos IOs podem ser emitidos em relação aos arquivos de dados.  As taxas de geração de log de transações são atualmente dimensionadas linearmente até um ponto dependente de hardware, com a taxa de log máxima permitida de 96 MB/s com o modelo de compra vCore. 

> [!NOTE]
> O IOs físico real para os arquivos de log de transações não são governados ou limitados. 

As taxas de log são definidas de modo que elas possam ser alcançadas e mantidas em vários cenários, enquanto o sistema geral pode manter sua funcionalidade com impacto minimizado na carga do usuário. A governança de taxa de log garante que os backups de log de transações permaneçam dentro dos SLAs de recuperação publicados.  Essa governança também impede uma pendência excessiva em réplicas secundárias.

À medida que os registros de log são gerados, cada operação é avaliada e avaliada se deve ser atrasada para manter uma taxa máxima de log desejada (MB/s por segundo). Os atrasos não são adicionados quando os registros de log são liberados para armazenamento, em vez disso, a governança de taxa de log é aplicada durante a própria geração de taxa de log.

As taxas de geração de log reais impostas em tempo de execução também podem ser influenciadas por mecanismos de comentários, reduzindo temporariamente as taxas de log permitidas para que o sistema possa se estabilizar. Gerenciamento de espaço de arquivo de log, evitando a execução de condições de espaço de log e os mecanismos de replicação de grupo de disponibilidade podem diminuir temporariamente os limites gerais do sistema. 

A modelagem de tráfego do administrador de taxa de log é revelada por meio dos seguintes tipos de espera (expostos na DMV [Sys. dm _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) ):

| Tipo de espera | Observações |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação de banco de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação de pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação de nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controle de comentários, replicação física do grupo de disponibilidade em Premium/Comercialmente Crítico não está acompanhando |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controle de comentários, limitando as taxas para evitar uma condição de espaço de log insuficiente |
|||

Ao encontrar um limite de taxa de log que está atrasando a escalabilidade desejada, considere as seguintes opções:
- Escale verticalmente para uma camada maior a fim de obter a taxa máxima de logs de 96 MB/s. 
- Se os dados que estão sendo carregados forem transitórios, ou seja, os dados de preparo em um processo de ETL, eles poderão ser carregados em tempdb (que é minimamente registrado). 
- Para cenários analíticos, carregue em uma tabela coberta por columnstore clusterizado. Isso reduz a taxa de log necessária devido à compactação. Essa técnica aumenta a utilização da CPU e só é aplicável a conjuntos de dados que se beneficiam de índices columnstore clusterizados. 

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para saber mais sobre DTUs e eDTUs, confira [DTUs e eDTUs](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para obter informações sobre limites de tamanho de tempdb, consulte [TempDB no Banco de Dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
