---
title: Copiar dados entre o Azure Data Lake Storage Gen1 e o Banco de Dados SQL do Azure usando o Sqoop | Microsoft Docs
description: Usar o Sqoop para copiar dados entre o Banco de Dados SQL do Azure e o Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 22789deca0934a9d4e88d587cd24aacacc9b12c6
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619998"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Copiar dados entre o Data Lake Storage Gen1 e o Azure SQL Database usando o Sqoop

Saiba como usar o Apache Sqoop para importar e exportar dados entre o Banco de Dados SQL do Azure e o Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>O que é o Sqoop?

Os aplicativos de big data são uma opção natural para o processamento de dados semi-estruturados e não estruturados, como logs e arquivos. No entanto, você também pode ter a necessidade de processar dados estruturados que são armazenados em bancos de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e um repositório de Big Data, como o Azure Data Lake Storage Gen1. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacionais), como o Banco de Dados SQL do Azure no Azure Data Lake Storage Gen1. Você pode transformar e analisar os dados usando cargas de trabalho de Big Data e, em seguida, exportar os dados de volta para um RDBMS. Neste artigo, você usa um banco de dados SQL do Azure como seu banco de dados relacional para importar/exportar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deverá ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar a conta, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo pressupõe que você tenha um cluster HDInsight Linux com acesso ao Azure Data Lake Storage Gen1.
* **Banco de dados SQL do Azure**. Para saber mais sobre como criar um, confira [Criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Criar tabelas de exemplo no banco de dados SQL do Azure

1. Para começar, crie duas tabelas de exemplo no banco de dados SQL do Azure. Use [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para se conectar ao banco de dados e, em seguida, execute as consultas a seguir.

    **Criar Tabela1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

    **Criar Tabela2**

       CREATE TABLE [dbo].[Table2](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. Execute o comando a seguir para adicionar alguns dados de exemplo à **tabela1**. Deixe a **Tabela2** vazia. Posteriormente, você importará dados da **tabela1** para data Lake Storage Gen1. Em seguida, você exportará dados de Data Lake Storage Gen1 para o **Table2**.

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Usar o Sqoop de um cluster HDInsight com acesso ao Data Lake Storage Gen1

Um cluster HDInsight já tem os pacotes Sqoop disponíveis. Se você configurou o cluster HDInsight para usar Data Lake Storage Gen1 como armazenamento adicional, você pode usar Sqoop (sem nenhuma alteração de configuração) para importar/exportar dados entre um banco de dado relacional, como o Azure SQL Database, e uma conta de Data Lake Storage Gen1 .

1. Neste artigo, presumimos que você criou um cluster do Linux para que você deve usar o SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Verifique se você pode acessar a conta de do Data Lake Storage Gen1 no cluster. Execute o comando a seguir do prompt do SSH:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Esse comando fornece uma lista de arquivos/pastas na conta de Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importar dados do Banco de Dados SQL do Azure para o Data Lake Storage Gen1

1. Navegue até o diretório onde os pacotes do Sqoop estão disponíveis. Normalmente, esse local é `/usr/hdp/<version>/sqoop/bin`.

1. Importe os dados da **Tabela1** para a conta do Data Lake Storage Gen1. Use a seguinte sintaxe:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   O espaço reservado **SQL-Database-Server-Name** representa o nome do servidor no qual o banco de dados SQL do Azure está em execução. **sql-database-name** representa o nome do banco de dados real.

   Por exemplo,

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Verifique se os dados foram transferidos para a conta do Data Lake Storage Gen1. Execute o seguinte comando:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Você deve ver a saída a seguir.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Cada arquivo **part-m-** corresponde a uma linha na tabela de origem, **Table1**. Você pode exibir o conteúdo dos arquivos part-m-* para verificação.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Exportar dados do Data Lake Storage Gen1 para o Banco de Dados SQL do Azure

1. Exporte os dados da conta do Data Lake Storage Gen1 para a tabela vazia, a **Tabela2**, no Banco de Dados SQL do Azure. Use a sintaxe a seguir.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Por exemplo,

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Verifique se os dados foram carregados na tabela do Banco de Dados SQL. Use o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para se conectar ao Banco de Dados SQL do Azure e então execute a consulta a seguir.

       SELECT * FROM TABLE2

   Esse comando deve ter a seguinte saída.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considerações de desempenho ao usar o Sqoop

Para obter informações sobre ajuste de desempenho do trabalho do Sqoop para copiar dados para Data Lake Storage Gen1, consulte a postagem do [blog de desempenho do Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="next-steps"></a>Próximas etapas

* [Copiar dados do Azure Storage Blobs para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
