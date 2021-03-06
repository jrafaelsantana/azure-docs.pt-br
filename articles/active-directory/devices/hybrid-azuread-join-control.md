---
title: Validação controlada do ingresso no Azure AD híbrido-Azure AD
description: Saiba como fazer uma validação controlada do ingresso do Azure AD híbrido antes de habilitá-lo em toda a organização, tudo de uma vez
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c897d52c10efdb8824f676d7640dcc7275915a9e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851787"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validação controlada de ingresso no Azure AD híbrido

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows serão automaticamente registrados como dispositivos no seu locatário do Azure AD. O estado dessas identidades de dispositivo no Azure AD é chamado de junção híbrida do Azure AD. Mais informações sobre os conceitos abordados neste artigo podem ser encontradas nos artigos [introdução ao gerenciamento de dispositivos em Azure Active Directory](overview.md) e [planejar sua implementação de junção de Azure Active Directory híbrida](hybrid-azuread-join-plan.md).

As organizações podem querer fazer uma validação controlada do ingresso no Azure AD híbrido antes de habilitá-lo em toda a organização de uma só vez. Este artigo explicará como realizar uma validação controlada da junção híbrida do Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validação controlada do ingresso no Azure AD híbrido em dispositivos atuais do Windows

Para os dispositivos que executam o sistema operacional da área de trabalho do Windows, a versão com suporte é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a última versão do Windows 10.

Para fazer uma validação controlada da junção híbrida do Azure AD em dispositivos atuais do Windows, você precisa:

1. Desmarque a entrada do SCP (ponto de conexão de serviço) do Active Directory (AD), se existir
1. Definir a configuração do registro do lado do cliente para o SCP em seus computadores ingressados no domínio usando um objeto de Política de Grupo (GPO)
1. Se você estiver usando AD FS, também deverá definir a configuração do registro do lado do cliente para o SCP no servidor de AD FS usando um GPO  



### <a name="clear-the-scp-from-ad"></a>Limpar o SCP do AD

Use o Editor ADSI (editor de interfaces de serviços Active Directory) para modificar os objetos SCP no AD.

1. Inicie o aplicativo de área de trabalho **Editor ADSI** a partir de uma Workstation administrativa ou de um controlador de domínio como um administrador corporativo.
1. Conecte-se ao **contexto de nomenclatura de configuração** do seu domínio.
1. Navegue até **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Configuração de registro do dispositivo**
1. Clique com o botão direito do mouse no objeto folha em **CN = Configuração de registro do dispositivo** e selecione **Propriedades**
   1. Selecione **palavras-chave** na janela **Editor de atributos** e clique em **Editar**
   1. Selecione os valores de **azureADId** e **azureADName** (um de cada vez) e clique em **remover**
1. Fechar **Editor ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Definir a configuração do registro do lado do cliente para o SCP

Use o exemplo a seguir para criar um objeto de Política de Grupo (GPO) para implantar uma configuração de registro Configurando uma entrada SCP no registro de seus dispositivos.

1. Abra um console de gerenciamento de Política de Grupo e crie um novo objeto de Política de Grupo em seu domínio.
   1. Forneça um nome de GPO criado recentemente (por exemplo, ClientSideSCP).
1. Edite o GPO e localize o seguinte caminho: >  > Preferênciasde > configuração do computador registro de configurações**do** Windows
1. Clique com o botão direito do mouse no registro e selecione **novo** > **item do registro**
   1. Na guia **geral** , configure o seguinte
      1. Ação: **Atualização**
      1. Sessão **HKEY_LOCAL_MACHINE**
      1. Caminho da chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **TenantId**
      1. Tipo de valor: **REG_SZ**
      1. Dados do valor: O GUID ou a **ID de diretório** da sua instância do Azure AD (esse valor pode ser encontrado no **portal do Azure** > **Azure Active Directory** > **ID de diretório**de**Propriedades** > )
   1. Clique em **OK**
1. Clique com o botão direito do mouse no registro e selecione **novo** > **item do registro**
   1. Na guia **geral** , configure o seguinte
      1. Ação: **Atualização**
      1. Sessão **HKEY_LOCAL_MACHINE**
      1. Caminho da chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **TenantName**
      1. Tipo de valor: **REG_SZ**
      1. Dados do valor: Seu **nome de domínio** verificado se você estiver usando ambiente federado, como AD FS. Seu **nome de domínio** verificado ou seu nome de domínio onmicrosoft.com, `contoso.onmicrosoft.com` por exemplo, se você estiver usando um ambiente gerenciado
   1. Clique em **OK**
1. Feche o editor do GPO criado recentemente
1. Vincular o GPO recém-criado à UO desejada que contém computadores ingressados no domínio que pertencem à sua população de distribuição controlada

### <a name="configure-ad-fs-settings"></a>Definir configurações de AD FS

Se estiver usando AD FS, primeiro você precisará configurar o SCP do lado do cliente usando as instruções mencionadas acima, mas vinculando o GPO aos seus servidores de AD FS. O objeto SCP define a origem de autoridade para objetos de dispositivo. Ele pode ser local ou Azure AD. Quando isso é configurado para AD FS, a origem dos objetos de dispositivo é estabelecida como Azure AD.

> [!NOTE]
> Se você não configurou o SCP do lado do cliente em seus servidores de AD FS, a origem das identidades de dispositivo seria considerada como local e AD FS começaria a excluir objetos de dispositivo do diretório local após um período estipulado.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validação controlada do ingresso no Azure AD híbrido em dispositivos de nível inferior do Windows

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar o [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro quiet. O atual branch do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo com Azure AD com as credenciais do usuário após a autenticação no Azure AD.

Para controlar o registro do dispositivo, você deve implantar o pacote de Windows Installer em seu grupo selecionado de dispositivos de nível inferior do Windows.

> [!NOTE]
> Se um SCP não estiver configurado no AD, você deverá seguir a mesma abordagem descrita para definir a [configuração do registro do lado do cliente para o SCP](#configure-client-side-registry-setting-for-scp)) em seus computadores ingressados no domínio usando um objeto de política de grupo (GPO).


Depois de verificar se tudo está funcionando conforme o esperado, você pode registrar automaticamente o restante de seus dispositivos atuais e de nível inferior do Windows com o Azure AD Configurando o [SCP usando o Azure ad Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Próximas etapas

[Planejar sua implementação de junção do Azure Active Directory híbrido](hybrid-azuread-join-plan.md)
