---
title: Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: O modelo de compra baseado em vCore permite que você dimensione de forma independente os recursos de computação e armazenamento, correspondam ao desempenho local e otimize o preço.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/01/2019
ms.openlocfilehash: af2e8826c40fb0d16844b6c67f151b0affbf3efd
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034992"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Escolha entre as camadas de serviço vCore e migre das camadas de serviço de DTU

O modelo de compra baseado no núcleo virtual (vCore) permite que você dimensione de forma independente os recursos de computação e armazenamento, correspondam ao desempenho local e otimize o preço. Ele também permite que você escolha a geração de hardware:

- **Gen4**: Até 24 CPUs lógicas baseadas em processadores Intel E5-2673 v3 (Haswell) 2,4-GHz, vCore = 1 PP (núcleo físico), 7 GB por vCore, SSD anexado
- **Gen5**: Até 80 CPUs lógicas baseadas em processadores Intel E5-2673 V4 (Broadwell) 2,3 GHz, vCore = 1 LP (Hyper-thread), 5,1 GB por vCore para computação provisionada e até 24 GB por vCore para computação sem servidor, SSD rápido de eNVM

Hardware Ger 4 oferece substancialmente mais memória por vCore. No entanto, o hardware Gen5 permite que você amplie recursos de computação muito maiores.

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.
> [!NOTE]
> Para obter informações sobre as camadas de serviço baseadas em DTU, consulte [camadas de serviço para o modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md). Para obter informações sobre as diferenças entre as camadas de serviço para os modelos de compra baseados em DTU e com base em vCore, consulte [modelos de compra do banco de dados SQL do Azure](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Características da camada de serviço

O modelo de compra baseado em vCore fornece três camadas de serviço: uso geral, hiperescala e comercialmente crítico. Essas camadas de serviço são diferenciadas por uma variedade de tamanhos de computação, projetos de alta disponibilidade, métodos de isolamento de falhas, tipos e tamanhos de armazenamento e intervalos de e/s.

Configure separadamente o período necessário de armazenamento e retenção para backups. Para definir o período de retenção de backup, abra o portal do Azure, vá para o servidor (não o banco de dados) e vá para **gerenciar backups** > **configurar a política** > **ponto no tempo configuração de restauração** > **7-35 dias**.

A tabela a seguir explica as diferenças entre as três camadas:

||**Propósito geral**|**Comercialmente crítico**|**Em hiperescala**|
|---|---|---|---|
|Mais adequado para|Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento.|Aplicativos OLTP com alta taxa de transação e baixa latência de e/s. Oferece maior resiliência a falhas e failovers rápidos usando várias réplicas atualizadas de forma síncrona.|A maioria das cargas de trabalho comerciais. Dimensionamento automático do tamanho de armazenamento de até 100 TB, dimensionamento vertical e horizontal de computação de fluxo, restauração rápida de banco de dados.|
|Computação|**Computação provisionada**:<br/>Gen4: 1 a 24 vCores<br/>Gen5: 2 a 80 vCores<br/>**Computação sem servidor**:<br/>Gen5: 0,5-16 vCores|**Computação provisionada**:<br/>Gen4: 1 a 24 vCores<br/>Gen5: 2 a 80 vCores|**Computação provisionada**:<br/>Gen4: 1 a 24 vCores<br/>Gen5: 2 a 80 vCores|
|Memória|**Computação provisionada**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore<br/>**Computação sem servidor**:<br/>Gen5: Até 24 GB por vCore|**Computação provisionada**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore |**Computação provisionada**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore|
|Armazenamento|Usa o armazenamento remoto.<br/>**Computação provisionada de banco de dados individual e pool elástico**:<br/>5 GB – 4 TB<br/>**Computação sem servidor**:<br/>5 GB-3 TB<br/>**Instância gerenciada**: 32 GB - 8 TB |Usa o armazenamento SSD local.<br/>**Computação provisionada de banco de dados individual e pool elástico**:<br/>5 GB – 4 TB<br/>**Instância gerenciada**:<br/>32 GB - 4 TB |Crescimento automático flexível do armazenamento, conforme necessário. Dá suporte a até 100 TB de armazenamento. Usa o armazenamento SSD local para o cache do pool de buffers local e o armazenamento de dados local. Usa o armazenamento remoto do Azure como armazenamento de dados de longo prazo final. |
|Taxa de transferência de e/s (aproximada)|**Banco de dados individual e pool elástico**: 500 IOPS por vCore até 40000 IOPS máximo.<br/>**Instância gerenciada**: Depende [do tamanho do arquivo](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS por núcleo até 200.000 IOPS máximo|O hiperscale é uma arquitetura de várias camadas com cache em vários níveis. O IOPs efetivo dependerá da carga de trabalho.|
|Disponibilidade|1 réplica, sem réplicas de escala de leitura|3 réplicas, 1 [réplica em escala de leitura](sql-database-read-scale-out.md),<br/>alta disponibilidade com redundância de zona (HA)|1 réplica de leitura/gravação, além [de 0-4 réplicas de escala de leitura](sql-database-read-scale-out.md)|
|Backups|[Armazenamento com redundância geográfica com acesso de leitura (ra-grs)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|Backups baseados em instantâneo no armazenamento remoto do Azure. As restaurações usam esses instantâneos para recuperação rápida. Os backups são instantâneos e não afetam O desempenho de e/s de computação. As restaurações são rápidas e não são uma operação de tamanho de dados (levando minutos em vez de horas ou dias).|
|Na memória|Sem suporte|Suportado|Sem suporte|
|||

> [!NOTE]
> Você pode obter um banco de dados SQL do Azure gratuito na camada de serviço básica em conjunto com uma conta gratuita do Azure. Para obter mais informações, consulte [criar um banco de dados de nuvem gerenciado com sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

- Para obter mais informações sobre limites de recursos vCore, consulte [limites de recursos VCORE em um banco de dados individual](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos VCORE em uma instância gerenciada](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Para obter mais informações sobre as camadas de serviço de uso geral e crítico para os negócios, consulte [camadas de serviço de uso geral e crítico](sql-database-service-tiers-general-purpose-business-critical.md)para os negócios.
- Para obter mais informações sobre a camada de serviço de hiperescala no modelo de compra baseado em vCore, consulte [camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Na camada de computação provisionada do modelo de compra baseado em vCore, você pode trocar suas licenças existentes por tarifas com desconto no banco de dados SQL usando [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Esse benefício do Azure permite que você economize até 30% no banco de dados SQL do Azure usando suas licenças de SQL Server locais com o Software Assurance.

![preços](./media/sql-database-service-tiers/pricing.png)

Com Benefício Híbrido do Azure, você pode optar por pagar apenas pela infraestrutura subjacente do Azure usando sua licença de SQL Server existente para o próprio mecanismo de banco de dados SQL (preço de computação base) ou pode pagar pela infraestrutura subjacente e pelo SQL Server licença (preço incluído na licença).

Você pode escolher ou alterar seu modelo de licenciamento usando o portal do Azure ou usando uma das seguintes APIs:

- Para definir ou atualizar o tipo de licença usando o PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Para definir ou atualizar o tipo de licença usando o CLI do Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Para definir ou atualizar o tipo de licença usando a API REST:

  - [Banco de Dados – Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bancos de Dados – Atualizar](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrar do modelo baseado em DTU para o modelo baseado em vCore

### <a name="migrate-a-database"></a>Migrar um banco de dados

A migração de um banco de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou ao downgrade entre as camadas de serviço Standard e Premium no modelo de compra baseado em DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrar bancos de dados com links de replicação geográfica

A migração do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou ao downgrade de relações de replicação geográfica entre bancos de dados nas camadas de serviço Standard e Premium. Durante a migração, você não precisa parar a replicação geográfica, mas deve seguir estas regras de sequenciamento:

- Ao atualizar, será necessário primeiro fazer upgrade do banco de dados secundário e, em seguida, upgrade do primário.
- Ao fazer downgrade, inverta a ordem: primeiro, você deverá fazer downgrade do banco de dados primário e, em seguida, fazer downgrade do secundário.

Quando você estiver usando a replicação geográfica entre dois pools elásticos, recomendamos que você designe um pool como o primário e o outro como o secundário. Nesse caso, quando estiver migrando pools elásticos, você deverá usar as mesmas diretrizes de sequenciamento. No entanto, se você tiver pools elásticos que contêm bancos de dados primários e secundários, trate o pool com a utilização mais alta como a primária e siga as regras de sequenciamento adequadamente.  

A tabela a seguir fornece orientação para cenários de migração específicos:

|Camada de serviço atual|Camada de serviço de destino|Tipo de migração|Ações do usuário|
|---|---|---|---|
|Standard|Propósito geral|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore*|
|Premium|Comercialmente crítico|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore*|
|Standard|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Standard|Downgrade|Deve migrar primeiro o primário|
|Premium|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Premium|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
||||

\* a cada 100 DTUs na camada Standard exigem pelo menos 1 vCore e cada 125 DTUs na camada Premium requer pelo menos 1 vCore.

### <a name="migrate-failover-groups"></a>Migrar grupos de failover

A migração de grupos de failover com vários bancos de dados requer a migração individual dos bancos de dados primário e secundário. Durante esse processo, as mesmas considerações e regras de sequenciamento se aplicam. Depois que os bancos de dados são convertidos para o modelo de compra baseado em vCore, o grupo de failover permanecerá em vigor com as mesmas configurações de política.

### <a name="create-a-geo-replication-secondary-database"></a>Criar um banco de dados secundário de replicação geográfica

Você pode criar um banco de dados secundário de replicação geográfica (um secundário geográfico) somente usando a mesma camada de serviço usada para o banco de dados primário. Para bancos de dados com uma alta taxa de geração de logs, é recomendável criar o secundário geográfico com o mesmo tamanho de computação que o primário.

Se você estiver criando um secundário geográfico no pool elástico para um único banco de dados primário, verifique se a configuração `maxVCore` para o pool corresponde ao tamanho de computação do banco de dados primário. Se você estiver criando um secundário geográfico para um primário em outro pool elástico, recomendamos que os pools tenham as mesmas configurações `maxVCore`.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Use a cópia do banco de dados para converter um banco de dados baseado em DTU em um banco de dados baseado em vCore

É possível copiar qualquer banco de dados com um tamanho da computação baseado em DTU para um banco de dados com um tamanho da computação baseado em vCore sem restrições ou sequenciamento especial, desde que o tamanho da computação de destino dê suporte ao tamanho máximo do banco de dados de origem. A cópia de banco de dados cria um instantâneo dos dados a partir da hora de início da operação de cópia e não sincroniza os dados entre a origem e o destino.

## <a name="next-steps"></a>Próximas etapas

- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados individuais, consulte [limites de recursos baseados em vCore do banco de dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md).
- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para pools elásticos, consulte [limites de recursos baseados em vCore do banco de dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
