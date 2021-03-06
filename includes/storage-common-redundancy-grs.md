---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 28b53864abc37a880a9573cc9657231aff862336
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029737"
---
O armazenamento com redundância geográfica (GRS) foi desenvolvido para fornecer pelo menos 99.99999999999999% (16 9’s) durabilidade dos objetos em um determinado ano, replicando dados para uma região secundária situada a centenas de milhas de distância da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Se você optar pelo GRS, você tem duas opções relacionadas para escolher:

* O GRS replica seus dados para outro datacenter em uma região secundária, mas esses dados estão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária.
* O armazenamento com redundância geográfica de acesso de leitura (RA-GRS) é baseado no GRS. O RA-GRS replica seus dados para outro datacenter em uma região secundária e também fornece a opção de ler a partir da região secundária. Com o RA-GRS, você pode ler a partir da região secundária, independentemente de a Microsoft iniciar um failover do primário para a região secundária.

Para uma conta de armazenamento com GRS ou RA-GRS habilitada, todos os dados são primeiro replicados com armazenamento redundante localmente (LRS). Uma atualização primeiro é confirmada para o local primário e replicados usando o LRS. A atualização, em seguida, é replicada assincronamente para a região secundária usando GRS. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS.

Ambas as regiões primárias e secundárias gerenciam réplicas entre domínios de falha separados e atualizar domínios dentro de uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica dentro do datacenter. A replicação nesse nível é fornecida por LRS; para obter mais informações, [consulte LRS (armazenamento com redundância local): Redundância de dados de baixo custo para o Armazenamento do Azure](../articles/storage/common/storage-redundancy-lrs.md).

Lembre-se esses pontos ao decidir qual opção de replicação para usar:

* O armazenamento com redundância de zona geográfica (GZRS) (visualização) fornece alta disponibilidade junto com a durabilidade máxima, replicando os dados de forma síncrona em três zonas de disponibilidade do Azure e, em seguida, replicando os dados de maneira assíncrona para a região secundária. Você também pode habilitar o acesso de leitura para a região secundária. O GZRS foi projetado para fornecer pelo menos a durabilidade de objetos de 99.99999999999999% (16 9) em um determinado ano. Para obter mais informações sobre o GZRS, consulte [armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)](../articles/storage/common/storage-redundancy-gzrs.md).
* O ZRS (armazenamento com redundância de zona) fornece alta disponibilidade com replicação síncrona em três zonas de disponibilidade do Azure e pode ser uma opção melhor para alguns cenários do que GRS ou RA-GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* A replicação assíncrona envolve um atraso entre a hora em que dados são gravados na região primária e quando são replicados na região secundária. Caso ocorra um desastre na região, as alterações que ainda não foram replicadas para a região secundária poderão ser pedidas se os dados não puderem ser recuperados na região primária.
* Com o GRS, a réplica não está disponível para acesso de leitura ou gravação, a menos que a Microsoft inicie um failover na região secundária. Em caso de failover, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de recuperação de desastre](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Se seu aplicativo precisar ler a partir da região secundária, habilite RA-GRS ou RA-GZRS (versão prévia).
