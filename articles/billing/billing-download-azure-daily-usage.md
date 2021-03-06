---
title: Exibir e baixar uso e encargos do Azure
description: Descreve como baixar ou exibir usos e encargos diários do Azure.
keywords: billing usage,usage charges, usage download, view usage, azure invoice,azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 23cd7c3765fc99eb5907aa853d7431d5e247aea6
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709722"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Exibir e baixar uso e encargos do Azure

Se você for um cliente EA ou tiver um [Contrato de Cliente da Microsoft](#check-your-access-to-a-microsoft-customer-agreement), poderá baixar o uso e os encargos do Azure no [portal do Azure](https://portal.azure.com/). Para outras assinaturas, acesse o [Centro de Contas do Azure](https://account.azure.com/Subscriptions) para baixar o uso.

Apenas determinadas funções têm permissão para receber informações de uso do Azure, como o administrador da conta ou o administrador corporativo. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

Se você tiver um [Contrato de Cliente da Microsoft](#check-your-access-to-a-microsoft-customer-agreement), deverá ter um perfil de cobrança Proprietário, Colaborador, Leitor ou Gerenciador de faturas para exibir o uso e os encargos do Azure. Para obter mais informações sobre funções de cobrança de Contratos de Cliente da Microsoft, confira [Funções e tarefas do perfil de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Baixar uso do Centro de Contas (.csv)

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions) como o Administrador da Conta.

2. Selecione a assinatura da qual deseja obter informações de fatura e uso.

3. Clique em **HISTÓRICO DE COBRANÇA**.

    ![Captura de tela que mostra a opção de histórico de cobrança](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Você pode ver os demonstrativos dos últimos seis períodos de cobrança e o período atual não faturado.

    ![Captura de tela que mostra os períodos de cobrança, as opções para baixar a fatura e o uso diário e as cobranças totais para cada período de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecione **Exibir Demonstrativo Atual** para ver uma estimativa dos seus encargos no momento em que a estimativa foi gerada. Essas informações são atualizadas diariamente e talvez não incluam todo o seu uso. Sua fatura mensal pode ser diferente dessa estimativa.

    ![Captura de tela que mostra a opção de Exibir Demonstrativo Atual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de tela que mostra a estimativa das cobranças atuais](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecione **Baixar Uso** para baixar os dados de uso diário como um arquivo CSV. Se vir duas versões disponíveis, baixe a versão 2.

    ![Captura de tela que mostra a opção de Baixar Uso](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Somente o Administrador da Conta pode acessar o Centro de Contas do Azure. Outros administradores de cobrança, por exemplo um proprietário, podem obter informações de uso usando as [APIs de cobrança](billing-usage-rate-card-overview.md).

Para saber mais sobre o uso diário, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda sobre como gerenciar os custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Faça o download uso para clientes do EA

Para exibir e baixar os dados de uso como cliente do EA, você deverá ser administrador corporativo, proprietário da conta ou administrador do departamento com a política de cobranças de exibição habilitada.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise *Gerenciamento de Custos + Cobrança*.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecione **uso + encargos**.
1. No mês que você deseja fazer o download, selecione **fazer o download**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Baixar uso para seu Contrato de Cliente da Microsoft

Se você tiver um Contrato de Cliente da Microsoft, poderá baixar o uso e os encargos do Azure para seu perfil de cobrança. É necessário ter um perfil de cobrança Proprietário, Colaborador, Leitor ou Gerenciador de faturas para baixar o CSV de uso e de encargos do Azure.

### <a name="download-usage-for-billed-charges"></a>Baixar uso para encargos cobrados

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Pesquise *Gerenciamento de Custos + Cobrança*.
3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança primeiro.
4. Selecione **Faturas**.
5. Na grade da fatura, encontre a linha da fatura correspondente ao uso que você deseja baixar.
6. Clique nas reticências (`...`) no final da linha.

    ![Captura de tela que mostra as reticências no fim da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. No menu de contexto do download, selecione **Uso e encargos do Azure**.

     ![Captura de tela que mostra o uso e os encargos do Azure selecionados](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Baixar uso para encargos pendentes

Também é possível baixar o uso do mês atual para o período de cobrança atual. Esses encargos de uso que ainda não foram cobrados.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Pesquise *Gerenciamento de Custos + Cobrança*.
3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança primeiro.
4. Na área **Visão Geral**, encontre os links de download abaixo dos encargos do mês atual.
5. Selecione **Uso e encargos do Azure**.

    ![Captura de tela que mostra o download em Visão Geral](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verificar acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a fatura e os encargos, de uso, confira:

- [Compreender os termos em uso detalhado do Microsoft Azure](billing-understand-your-usage.md)
- [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- [Exibir e baixar a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Exibir e baixar o preço do Azure de sua organização](billing-ea-pricing.md)

Se você tiver um Contrato de Cliente da Microsoft, confira:

- [Noções básicas sobre os termos no seu uso detalhado do Azure do Contrato de Cliente da Microsoft](billing-mca-understand-your-usage.md)
- [Noções básicas sobre os encargos em sua fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-bill.md)
- [Exibir e baixar a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Exibir e baixar documentos de imposto do seu Contrato de Cliente da Microsoft](billing-mca-download-tax-document.md)
- [Exibir e baixar o preço do Azure de sua organização](billing-ea-pricing.md)
