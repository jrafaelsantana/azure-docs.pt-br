---
title: Fazer backup e restaurar arquivos do Azure usando o backup do Azure e o PowerShell
description: Faça backup e restaure os arquivos do Azure usando o backup do Azure e o PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: dacurwin
ms.reviewer: pullabhk
ms.openlocfilehash: 2c9ca71816d6688881de465a575a8a0eef3cde1f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650433"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Fazer backup e restaurar arquivos do Azure com o PowerShell

Este artigo descreve como usar Azure PowerShell para fazer backup e recuperar um compartilhamento de arquivos de arquivos do Azure usando um cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) . 

Este tutorial explica como:

> [!div class="checklist"]
> * Configure o PowerShell e registre o provedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure o backup para um compartilhamento de arquivos do Azure.
> * Execute um trabalho de backup.
> * Restaure um compartilhamento de arquivos do Azure de backup ou um arquivo individual de um compartilhamento.
> * Monitore os trabalhos de backup e restauração.


## <a name="before-you-start"></a>Antes de começar

- [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos serviços de recuperação.
- Leia sobre os recursos de visualização para fazer backup de compartilhamentos de [arquivos do Azure](backup-azure-files.md).
- Examine a hierarquia de objetos do PowerShell para serviços de recuperação.


## <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos Serviços de Recuperação

A hierarquia de objetos é resumida no diagrama a seguir.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Examine a referência de [referência do cmdlet](/powershell/module/az.recoveryservices) **AZ. recoveryservices** na biblioteca do Azure.


## <a name="set-up-and-install"></a>Configurar e instalar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte maneira:

1. [Baixe a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps). A versão 1.0.0 é a mínima necessária.

2. Localize os cmdlets do PowerShell de backup do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Examine os aliases e cmdlets para o backup do Azure, Azure Site Recovery e o cofre dos serviços de recuperação são exibidos. Aqui está um exemplo do que você pode ver. Não é uma lista completa de cmdlets.

    ![Listar cmdlets dos Serviços de Recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Entre em sua conta do Azure com **Connect-AzAccount**.
4. Na página da Web que aparece, você será solicitado a inserir suas credenciais de conta.

    - Como alternativa, você pode incluir suas credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount** com **-Credential**.
    - Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Um exemplo é **Connect-AzAccount -Tenant** fabrikam.com.

4. Associe a assinatura que você deseja usar com a conta, pois uma conta pode ter várias assinaturas.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se você estiver usando um Backup do Azure pela primeira vez, use o cmdlet **Register-AzResourceProvider** para registrar o provedor dos Serviços de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Verifique se os provedores foram registrados com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. Na saída do comando, verifique se **RegistrationState** muda para **registrado**. Caso contrário, execute o cmdlet **Register-AzResourceProvider** novamente.



## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Siga estas etapas para criar um cofre dos Serviços de Recuperação:

- O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **New-AzResourceGroup**. Ao criar um grupo de recursos, especifique o nome e o local. 

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um grupo de recursos existente, crie um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, criamos um novo grupo de recursos na região oeste dos EUA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Use o cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) para criar o cofre. Especifique o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Especifique o tipo de redundância a ser usado para o armazenamento do cofre.

   - Você pode usar [armazenamento com redundância local](../storage/common/storage-redundancy-lrs.md) ou [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md).
   - O exemplo a seguir define a opção **-BackupStorageRedundancy** para o cmd[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) para **testvault** definidocomo georedundante.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres da assinatura, use [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. Observe que o grupo de recursos e o local associados são fornecidos.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do cofre

Armazene o objeto de cofre em uma variável e defina o contexto do cofre.

- Muitos cmdlets de backup do Azure exigem o objeto de cofre dos serviços de recuperação como uma entrada, portanto, é conveniente armazenar o objeto de cofre em uma variável.
- O contexto do cofre é o tipo de dados protegido no cofre. Defina-o com [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes.


O exemplo a seguir define o contexto de cofre de **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Buscar a ID do cofre

Planejamos substituir a configuração de contexto do cofre de acordo com as diretrizes de Azure PowerShell. Em vez disso, você pode armazenar ou buscar a ID do cofre e passá-la para comandos relevantes. Portanto, se você não tiver definido o contexto do cofre ou desejar especificar o comando a ser executado para um determinado cofre, passe a ID do cofre como "-vaultid" para todo o comando relevante da seguinte maneira:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o agendamento de backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos:

- Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído.
- Exiba a retenção de política de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Exiba o agendamento da política de backup padrão usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Use o cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) para criar uma nova política de backup. Você insere os objetos de política de retenção e agendamento.

Por padrão, uma hora de início é definida no objeto de política de agenda. Use o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada também deve estar em UTC. O exemplo abaixo pressupõe que a hora de início desejada seja 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Você precisa fornecer a hora de início apenas em 30 minutos. No exemplo acima, ele pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00"

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, ele usa essas variáveis como parâmetros para uma nova política (**NewAFSPolicy**). **NewAFSPolicy** faz um backup diário e os retém por 30 dias.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Habilitar backup

Depois de definir a política de backup, você pode habilitar a proteção para o compartilhamento de arquivos do Azure usando a política.

### <a name="retrieve-a-backup-policy"></a>Recuperar uma política de backup

Você busca o objeto de política relevante com [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Use este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperar uma política para um tipo de carga de trabalho

O exemplo a seguir recupera políticas para o tipo de carga de trabalho **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> O fuso horário do campo **BackupTime** no PowerShell é o UTC (Tempo Universal Coordenado). Quando a hora de backup é mostrada no Portal do Azure, o horário é ajustado para seu fuso horário local.

### <a name="retrieve-a-specific-policy"></a>Recuperar uma política específica

A política a seguir recupera a política de backup **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Habilitar backup e aplicar política

Habilite a proteção com [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Depois que a política estiver associada ao cofre, os backups serão disparados de acordo com o agendamento da política.

O exemplo a seguir habilita a proteção para o compartilhamento de arquivos do Azure **testAzureFileShare** na conta de armazenamento **testStorageAcct**, com a política **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho de configurar a proteção seja concluído e fornece uma saída semelhante à mostrada abaixo.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Disparar um backup sob demanda

Use [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) para executar um backup sob demanda para um compartilhamento de arquivos do Azure protegido.

1. Recupere a conta de armazenamento e o compartilhamento de arquivos do contêiner no cofre que contém os dados de backup com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar um trabalho de backup, você obtém informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Execute um backup sob demanda com[backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Execute o backup sob demanda da seguinte maneira:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando retorna um trabalho com uma ID a ser rastreada, como no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Os instantâneos do compartilhamento de arquivos do Azure são usados enquanto os backups são feitos, portanto, o trabalho normalmente é concluído no momento em que o comando retorna essa saída.

### <a name="using-on-demand-backups-to-extend-retention"></a>Usando backups sob demanda para estender a retenção

Backups sob demanda podem ser usados para manter seus instantâneos por 10 anos. Os agendadores podem ser usados para executar scripts do PowerShell sob demanda com retenção escolhida e, portanto, tirar instantâneos em intervalos regulares a cada semana, mês ou ano. Ao fazer instantâneos regulares, consulte as [limitações de backups sob demanda](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share-) usando o backup do Azure.

Se você estiver procurando scripts de exemplo, poderá consultar o script de exemplo no GitHub (https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) usando o runbook de automação do Azure que permite agendar backups periodicamente e mantê-los até 10 anos.

### <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política usada para fazer backup do compartilhamento de arquivos do Azure, use [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Especifique o item de backup relevante e a nova política de backup.

O exemplo a seguir altera a política de proteção de **testAzureFS** de **dailyafs** para **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Restaurar arquivos e compartilhamentos de arquivos do Azure

Você pode restaurar um compartilhamento de arquivos inteiro ou arquivos específicos no compartilhamento. Você pode restaurar para o local original ou para um local alternativo. 

### <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) para listar todos os pontos de recuperação para o item de backup.

No script a seguir:

- A variável **$RP** é uma matriz de pontos de recuperação para o item de backup selecionado dos últimos sete dias.
- A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice **0**.
- Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação.
- No exemplo, **$rp[0]** seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A saída é semelhante à seguinte.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```
Depois que o ponto de recuperação relevante é selecionado, você restaura o compartilhamento de arquivos ou o arquivo para o local original ou para um local alternativo.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar um compartilhamento de arquivos do Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo: 

- **TargetStorageAccountName**: a conta de armazenamento na qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
- **TargetFileShareName**: compartilhamentos de arquivos dentro da conta de armazenamento de destino na qual o conteúdo de backup é restaurado.
- **TargetFolder**: a pasta no compartilhamento de arquivos na qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
- **ResolveConflict**: a instrução para o caso de um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Execute o cmdlet com os parâmetros da seguinte maneira:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

O comando retorna um trabalho com uma ID a ser rastreada, como no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um arquivo do Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo e para identificar exclusivamente o arquivo que você deseja restaurar.

* **TargetStorageAccountName**: a conta de armazenamento na qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: compartilhamentos de arquivos dentro da conta de armazenamento de destino na qual o conteúdo de backup é restaurado.
* **TargetFolder**: a pasta no compartilhamento de arquivos na qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **SourceFilePath**: o caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. Esse caminho é o mesmo que foi usado no cmdlet **Get-AzStorageFile** do PowerShell.
* **SourceFileType**: se um diretório ou um arquivo está selecionado. Aceita **Directory** ou **File**.
* **ResolveConflict**: a instrução para o caso de um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados somente ao arquivo individual que você deseja restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Esse comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado na seção anterior.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar arquivos e compartilhamentos de arquivos do Azure para o local original

Ao restaurar para um local original, você não precisa especificar parâmetros relacionados ao destino e ao destino. Somente **ResolveConflict** deve ser fornecido.

#### <a name="overwrite-an-azure-file-share"></a>Substituir um compartilhamento de arquivos do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Substituir um arquivo do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Acompanhar tarefas de backup e restauração

As operações de backup e restauração sob demanda retornam um trabalho junto com uma ID, conforme mostrado quando você [executou um backup sob demanda](#trigger-an-on-demand-backup). Use o cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) para acompanhar o progresso e os detalhes do trabalho.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre como](backup-azure-files.md) fazer backup de arquivos do Azure no portal do Azure.
