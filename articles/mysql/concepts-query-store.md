---
title: Repositório de Consultas no banco de dados do Azure para MySQL
description: Saiba mais sobre o recurso Repositório de Consultas no banco de dados do Azure para MySQL para ajudá-lo a acompanhar o desempenho ao longo do tempo.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: bac270dc0d49c0eaa8c01b030256aa9bb597db80
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029872"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorar o desempenho do banco de dados do Azure para MySQL com o Repositório de Consultas

**Aplica-se a:** Banco de dados do Azure para MySQL 5,7

> [!IMPORTANT]
> Repositório de Consultas está em versão prévia.

O recurso Repositório de Consultas no banco de dados do Azure para MySQL fornece uma maneira de controlar o desempenho de consulta ao longo do tempo. O Repositório de Consultas simplifica a solução de problemas ajudando você a rapidamente localizar as consultas de execução mais longa e que consomem mais recursos. O Repositório de Consultas captura automaticamente um histórico das estatísticas de tempo de execução e consultas e o retém para sua análise. Ele separa os dados por janelas de tempo para que você possa ver padrões de uso do banco de dados. Todos os usuários, bancos de dados e consultas são armazenados no banco de dados de esquema do **MySQL** na instância do banco de dados do Azure para MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para usar o Repositório de Consultas

O repositório de consultas pode ser usado em vários cenários, incluindo o seguinte:

- Detectando consultas regressadas
- Determinação do número de vezes que uma consulta foi executada em uma determinada janela de tempo
- Comparar o tempo médio de execução de uma consulta entre janelas de tempo para ver grandes deltas

## <a name="enabling-query-store"></a>Habilitando o Repositório de Consultas

O Repositório de Consultas é um recurso que requer aceitação, portanto, ele não está ativo em um servidor por padrão. O repositório de consultas é habilitado ou desabilitado globalmente para todos os bancos de dados em um determinado servidor e não pode ser ativado ou desativado por banco de dado.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitar o Repositório de Consultas usando o portal do Azure

1. Entre no portal do Azure e selecione o servidor do banco de dados do Azure para MySQL.
1. Selecione **Parâmetros de Servidor** na seção **Configurações** do menu.
1. Procure o parâmetro query_store_capture_mode.
1. Defina o valor como todos e **salve**.

Para habilitar as estatísticas de espera no seu Repositório de Consultas:

1. Procure o parâmetro query_store_wait_sampling_capture_mode.
1. Defina o valor como todos e **salve**.

Aguarde até 20 minutos para que o primeiro lote de dados persista no banco de dado MySQL.

## <a name="information-in-query-store"></a>Informações no Repositório de Consultas

O Repositório de Consultas tem dois repositórios:

- Um repositório de estatísticas de tempo de execução para manter as informações de estatísticas de execução de consulta.
- Um repositório de estatísticas de espera para manter informações de estatísticas de espera.

Para minimizar o uso de espaço, as estatísticas de execução de tempo de execução no repositório de estatísticas de tempo de execução são agregadas em uma janela de tempo fixa e configurável. As informações nesses repositórios são visíveis consultando as exibições do repositório de consultas.

A consulta a seguir retorna informações sobre consultas no Repositório de Consultas:

```sql
SELECT * FROM mysql.query_store;
```

Ou esta consulta para estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Localizando consultas de espera

Os tipos de evento de espera combinam diferentes eventos de espera em buckets por semelhança. O Repositório de Consultas fornece o tipo de evento de espera, o nome do evento de espera específico e a consulta em questão. Ser capaz de correlacionar essas informações de espera com as estatísticas de tempo de execução de consulta significa que você pode obter uma compreensão mais profunda do que contribui para as características de desempenho de consulta.

Aqui estão alguns exemplos de como você pode obter mais insights sobre sua carga de trabalho usando as estatísticas de espera no Repositório de Consultas:

| **Observação** | **Ação** |
|---|---|
|Esperas de bloqueio alto | Verifique os textos de consulta para as consultas afetadas e identifique as entidades de destino. Procure no Repositório de Consultas outras consultas que modificam a mesma entidade, que é executada com frequência e/ou têm alta duração. Depois de identificar essas consultas, considere alterar a lógica do aplicativo para melhorar a simultaneidade ou use um nível de isolamento menos restritivo. |
|Esperas de E/S de buffer alto | Localize as consultas com um grande número de leituras físicas no Repositório de Consultas. Se eles corresponderem às consultas com altas esperas de e/s, considere a introdução de um índice na entidade subjacente, para fazer buscas em vez de verificações. Isso minimizaria a sobrecarga de E/S das consultas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice para esse servidor que otimizariam as consultas. |
|Esperas de memória alta | Localize as consultas que consomem mais memória no Repositório de Consultas. Essas consultas estão provavelmente atrasando o andamento das consultas afetadas. Verifique as **Recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice que otimizariam essas consultas.|

## <a name="configuration-options"></a>Opções de configuração

Quando o Repositório de Consultas está habilitado, ele salva dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela.

As opções a seguir estão disponíveis para configurar os parâmetros do Repositório de Consultas.

| **Parâmetro** | **Descrição** | **Padrão** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Ativar/desativar o recurso de repositório de consultas com base no valor. Observação: Se performance_schema estiver OFF, ativar o query_store_capture_mode ativará performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para esse recurso. | ALL | NENHUM, TUDO |
| query_store_capture_interval | O intervalo de captura do repositório de consultas em minutos. Permite especificar o intervalo no qual as métricas de consulta são agregadas | 15 | 5 - 60 |
| query_store_capture_utility_queries | Ativar ou desativar o para capturar todas as consultas do utilitário que estão sendo executadas no sistema. | NÃO | SIM, NÃO |
| query_store_retention_period_in_days | Janela de tempo em dias para manter os dados no repositório de consultas. | 7 | 1 a 30 |

As opções a seguir se aplicam especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Padrão** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite ativar/desativar as estatísticas de espera. | NENHUM | NENHUM, TUDO |
| query_store_wait_sampling_frequency | Altera a frequência de amostragem de espera em segundos. 5 a 300 segundos. | 30 | 5-300 |

> [!NOTE]
> Atualmente, o **query_store_capture_mode** substitui essa configuração, o que significa que **query_store_capture_mode** e **query_store_wait_sampling_capture_mode** precisam ser habilitados para que todas as estatísticas de espera funcionem. Se **query_store_capture_mode** for desativado, as estatísticas de espera serão desativadas, já que as estatísticas de espera utilizam o performance_schema habilitado e o query_text capturado pelo repositório de consultas.

Use o [portal do Azure](howto-server-parameters.md) or [CLI do Azure](howto-configure-server-parameters-using-cli.md) to Get ou defina um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Exibições e funções

Exiba e gerencie o Repositório de Consultas usando as seguintes exibições e funções. Qualquer pessoa na [função pública selecionar privilégio](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) pode usar essas exibições para ver os dados em repositório de consultas. Essas exibições estão disponíveis somente no banco de dados **MySQL** .

Consultas são normalizadas examinando sua estrutura após a remoção de literais e constantes. Se duas consultas forem idênticas, exceto por valores literais, elas terão o mesmo hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Essa exibição retorna todos os dados no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário e ID de consulta distinta.

| **Name** | **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | NÃO | Nome do esquema |
| `query_id`| bigint(20) | NÃO| ID exclusiva gerada para a consulta específica, se a mesma consulta for executada em um esquema diferente, uma nova ID será gerada |
| `timestamp_id` | timestamp| NÃO| Carimbo de data/hora em que a consulta é executada. Isso se baseia na configuração do query_store_interval|
| `query_digest_text`| longtext| NÃO| O texto de consulta normalizado após a remoção de todos os literais|
| `query_sample_text` | longtext| NÃO| Primeira aparência da consulta real com literais|
| `query_digest_truncated` | bit| SIM| Se o texto da consulta foi truncado. O valor será Sim se a consulta tiver mais de 1 KB|
| `execution_count` | bigint(20)| NÃO| O número de vezes que a consulta foi executada para esta ID de carimbo de data/hora durante o período de intervalo configurado|
| `warning_count` | bigint(20)| NÃO| Número de avisos que esta consulta gerou durante a|
| `error_count` | bigint(20)| NÃO| Número de erros que esta consulta gerou durante o intervalo|
| `sum_timer_wait` | double| SIM| Tempo de execução total desta consulta durante o intervalo|
| `avg_timer_wait` | double| SIM| Tempo médio de execução para esta consulta durante o intervalo|
| `min_timer_wait` | double| SIM| Tempo mínimo de execução para esta consulta|
| `max_timer_wait` | double| SIM| Tempo de execução máximo|
| `sum_lock_time` | bigint(20)| NÃO| Quantidade total de tempo gasto para todos os bloqueios para esta execução de consulta durante esta janela de tempo|
| `sum_rows_affected` | bigint(20)| NÃO| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| NÃO| Número de linhas enviadas ao cliente|
| `sum_rows_examined` | bigint(20)| NÃO| Número de linhas verificadas|
| `sum_select_full_join` | bigint(20)| NÃO| Número de junções completas|
| `sum_select_scan` | bigint(20)| NÃO| Número de verificações selecionadas |
| `sum_sort_rows` | bigint(20)| NÃO| Número de linhas classificadas|
| `sum_no_index_used` | bigint(20)| NÃO| Número de vezes em que a consulta não usou nenhum índice|
| `sum_no_good_index_used` | bigint(20)| NÃO| Número de vezes em que o mecanismo de execução de consulta não usou índices bons|
| `sum_created_tmp_tables` | bigint(20)| NÃO| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| NÃO| Número total de tabelas temporárias criadas no disco (gera e/s)|
| `first_seen` | timestamp| NÃO| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` | timestamp| NÃO| A última ocorrência (UTC) da consulta durante esta janela de agregação|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Essa exibição retorna os dados de eventos de espera no Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário, ID de consulta e evento distinto.

| **Name**| **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` | timestamp | NÃO| Início do intervalo (incremento de 15 minutos)|
| `interval_end` | timestamp | NÃO| Fim do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | NÃO| ID exclusiva gerada na consulta normalizada (do repositório de consultas)|
| `query_digest_id` | varchar(32) | NÃO| O texto de consulta normalizado após a remoção de todos os literais (do repositório de consultas) |
| `query_digest_text` | longtext | NÃO| Primeira aparência da consulta real com literais (do repositório de consultas) |
| `event_type` | varchar(32) | NÃO| Categoria do evento de espera |
| `event_name` | varchar(128) | NÃO| Nome do evento de espera |
| `count_star` | bigint(20) | NÃO| Número de eventos de espera amostrados durante o intervalo para a consulta |
| `sum_timer_wait_ms` | double | NÃO| Tempo de espera total (em milissegundos) desta consulta durante o intervalo |

### <a name="functions"></a>Funções

| **Name**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Limpa todos os dados do repositório de consultas antes do carimbo de data/hora fornecido |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Limpa todos os dados de evento de espera antes do carimbo de data/hora fornecido |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Limpa as recomendações cuja expiração é anterior ao carimbo de data/hora especificado |

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- Se um servidor MySQL tiver o parâmetro `default_transaction_read_only` em, Repositório de Consultas não poderá capturar dados.
- Repositório de Consultas funcionalidade poderá ser interrompida se encontrar consultas longas em Unicode (\> = 6000 bytes).
- O período de retenção para estatísticas de espera é de 24 horas.
- Estatísticas de espera usa a captura de ti de exemplo uma fração de eventos. A frequência pode ser modificada usando o parâmetro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [informações de desempenho de consulta](concepts-query-performance-insight.md)