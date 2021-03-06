---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: c58977798fabc120f3a8647774f575bc32515dcd
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219019"
---
Aqui estão as restrições de uso e outros limites de serviço para o serviço Microsoft Azure Active Directory (Azure AD).

| Categoria | Limites |
| --- | --- |
| Diretórios | Um único usuário pode pertencer a um máximo de 500 diretórios do Microsoft Azure Active Directory como um membro ou convidado.<br/>Um único usuário pode criar no máximo 20 diretórios. |
| Domínios | É possível adicionar no máximo 900 nomes de domínio gerenciados. Ao configurar todos os domínios para a federação com o Active Directory local, você poderá adicionar no máximo 450 nomes de domínio em cada diretório. |
|Recursos |<ul><li>Um máximo de 50.000 recursos do Azure AD pode ser criado em um único diretório por usuários da edição gratuita do Azure Active Directory por padrão. Se você tiver pelo menos um domínio verificado, a cota de serviço de diretório padrão no Azure AD será estendida para os recursos 300.000 do AD do Azure. </li><li>Um usuário não administrador pode criar no máximo 250 recursos do Azure AD. Recursos ativos e excluídos que estão disponíveis para restaurar a contagem em direção a essa cota. Somente os recursos do Azure AD excluídos que foram excluídos há menos de 30 dias estão disponíveis para restauração. Recursos do Azure AD excluídos que não estão mais disponíveis para restaurar a contagem em direção a essa cota em um valor de um trimestre por 30 dias. Se você tiver os desenvolvedores que provavelmente excederão repetidamente essa cota no decorrer de suas tarefas regulares, você poderá [criar e atribuir uma função personalizada](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) com permissão para criar um número ilimitado de registros de aplicativo.</li></ul> |
| Extensões de esquema |<ul><li>As extensões do tipo cadeia de caracteres podem ter no máximo 256 caracteres. </li><li>As extensões do tipo binário são limitadas a 256 bytes.</li><li>Somente os valores de extensão 100, em *todos os* tipos e *todos os* aplicativos, podem ser gravados em qualquer recurso único do AD do Azure.</li><li>Somente as entidades Grupo, TenantDetail, Dispositivo, Aplicativo e ServicePrincipal podem ser estendidas com atributos de valor único do tipo cadeia de caracteres ou binário.</li><li>As extensões de esquema estão disponíveis somente na API do Graph versão 1.21 – versão prévia. O aplicativo precisa obter acesso de gravação para registrar uma extensão.</li></ul> |
| Aplicativos |Um máximo de 100 usuários podem ser proprietários de um único aplicativo. |
| Grupos |<ul><li>Um máximo de 100 usuários podem ser proprietários de um único grupo.</li><li>Qualquer número de recursos do AD do Azure pode ser membros de um único grupo.</li><li>Um usuário pode ser um membro de qualquer número de grupos.</li><li>O número de membros em um grupo que podem ser sincronizados do Active Directory local para o Azure Active Directory usando o Azure AD Connect é limitado a 50 mil membros.</li></ul> |
| Proxy do Aplicativo | <ul><li>Um máximo de 500 de transações por segundo por aplicativo de proxy de aplicativo</li><li>Um máximo de 750 transações por segundo para o locatário</li></ul><br/>Uma transação é definida como uma única solicitação HTTP e uma resposta para um recurso exclusivo. Quando limitado, os clientes receberão uma resposta de 429 (muitas solicitações). |
| Painel de acesso |<ul><li>Não há nenhum limite para o número de aplicativos que podem ser vistos no Painel de Acesso por usuário. Isso se aplica às licenças do Azure AD Premium ou do Enterprise Mobility + Security E3 atribuídas aos usuários.</li><li>No máximo 10 blocos de aplicativos podem ser vistos no Painel de Acesso para cada usuário. Esse limite se aplica a usuários que recebem licenças para Azure AD Gratuito plano de licença. Exemplos de blocos de aplicativos incluem o Box, o Salesforce ou o Dropbox. Esse limite não se aplica às contas de administrador.</li></ul> |
| Relatórios | Um máximo de 1.000 linhas podem ser exibidas ou baixadas em qualquer relatório. Todos os dados adicionais serão truncados. |
| Unidades administrativas | Um recurso do Azure AD pode ser um membro de no máximo 30 unidades administrativas. |
| Funções e permissões do administrador | <ul><li>Um grupo não pode ser adicionado como [proprietário](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Um grupo não pode ser atribuído a uma [função](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>A capacidade dos usuários de ler informações de diretório de outros usuários não pode ser restrita fora do comutador de todo o locatário para desabilitar o acesso de todos os usuários não administradores a todas as informações de diretório (não recomendado). Mais informações sobre as permissões padrão [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Pode levar até 15 minutos ou sair/entrar antes que as adições e as revogações de associação de função de administrador entrem em vigor.</li></ul> |
