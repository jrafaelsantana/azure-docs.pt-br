---
title: Lista de verificação de criação de oferta de SaaS-Marketplace comercial para Azure
description: Os detalhes que você pode fornecer no processo de criação da oferta de SaaS. -Marketplace comercial para o Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 66c28039f9126ed9e3f56c3ac15b1b3d82279b64
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036376"
---
# <a name="saas-offer-creation-checklist"></a>Lista de verificação de criação de oferta de SaaS

O processo de criação da oferta de SaaS levará você por várias páginas. Aqui estão os detalhes que você pode fornecer em cada página, com links para saber mais sobre cada item.

Os itens que você precisa fornecer ou especificar estão indicados abaixo. Algumas áreas são opcionais ou têm valores padrão fornecidos, que você pode alterar conforme desejado. Você não precisa trabalhar nessas seções na ordem listada aqui.

| **Item**    | **Finalidade**  |
| :---------- | :-------------------|
| [**Nova oferta modal**](#new-offer-modal) | Coleta informações de identidade de oferta.  |
| [Página de instalação da oferta](#offer-setup-page) | Permite que você opte por usar os principais recursos e escolha como vender sua oferta pela Microsoft.  |
| [Página de propriedades](#properties-page) | Defina as categorias e os setores usados para agrupar sua oferta nos Marketplaces, os contratos legais que dão suporte à sua oferta e sua versão do aplicativo. |
| [Página de listagem da oferta](#offer-listing-page) | Defina os detalhes da oferta a serem exibidos no Marketplace, incluindo descrições de sua oferta e ativos de marketing. |
| [Página de visualização](#preview-page) | Defina um público-alvo limitado para liberar sua oferta antes de publicar sua oferta em tempo real para os maiores participantes do Marketplace. |
| [Página de configuração técnica da oferta](#technical-configuration-page)  | Disponível somente se você optar por vender a oferta pela Microsoft. Defina os detalhes técnicos (caminho da URL, webhook, ID do locatário e ID do aplicativo) usados para se conectar à sua oferta. |
| [**Novo plano modal**](#plan-identity-modal) | Coleta informações de identidade do plano.  |
| [Página de listagem do plano](#plan-listing-page)  | Disponível somente se você optar por vender a oferta pela Microsoft. Defina os detalhes usados para listar o plano no Marketplace.  |
| [Planejar o preço & página disponibilidade](#plan-pricing--availability-page)  | Disponível somente se você optar por vender a oferta pela Microsoft.  Coleta as características comerciais (modelo de preços), a disponibilidade do público e do mercado para cada plano (versão) de sua oferta.  |
| [Página de listagem do Test Drive](#test-drive-listing-page)  | Disponível somente se você optar por oferecer um test drive para sua oferta. Defina os detalhes usados para listar os test drive no Marketplace.  |
| Página de configuração técnica do Test Drive  | Disponível somente se você optar por oferecer um test drive para sua oferta. Defina os detalhes técnicos para a demonstração (ou "test drive"), que permitirá que os clientes experimentem sua oferta antes de confirmar a compra.  |
| [Página revisar e publicar](#review-and-publish-page)  | Selecione as alterações que você deseja publicar, consulte o status de cada página e forneça notas para a equipe de certificação.  |


## <a name="new-offer-modal"></a>Nova oferta modal 

As primeiras informações que você será solicitado a fornecer são uma ID e um alias para sua oferta. 

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| ID de oferta  | Obrigatório, não pode ser alterado após a criação. Máximo de 50 caracteres e deve consistir apenas em letras minúsculas, caracteres alfanuméricos, traços ou sublinhados. |
| Alias de oferta  | Obrigatória. |

## <a name="offer-setup-page"></a>Página de instalação da oferta

A página de instalação da oferta é onde você pode aceitar diferentes canais e vender movimentos, bem como declarar o uso de principais recursos, como test drive e leads do cliente. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------|  
| Você gostaria de vender pela Microsoft?  | Obrigatória. Padrão: Sim |
| Como você deseja que clientes potenciais interajam com a listagem de ofertas? (Plano de ação)  | Necessário se não estiver vendendo pela Microsoft. Padrão: Avaliação gratuita, opções: "Obtenha agora", "avaliação gratuita", "entre em contato comigo". |
| URL da Avaliação  | Necessário se a opção "avaliação gratuita" estiver selecionada, como o modo como os clientes devem interagir com a listagem de ofertas. |
| URL da oferta  | Necessário se "obter agora" estiver selecionado, como o modo como os clientes devem interagir com a listagem de ofertas |
| Canais  | Opcional. Padrão: Não foi optado pelo canal do CSP (revendedor).  |
| Test Drive | Opcional. Padrão: Nenhum test drive habilitado.  |
| Tipo de Test Drive | Necessário se estiver habilitado um test drive. Padrão: Nenhum selecionado. Opções: Azure Resource Manager, Dynamics 365 for Business central, Dynamics 365 para compromisso com o cliente, Dynamics 365 para operações, aplicativo lógico Power BI.  |
| Gerenciamento de leads – conectar a um sistema de CRM | Necessário se estiver vendendo pela Microsoft ou se a listagem oferecer como "entre em contato comigo". Padrão: nenhum sistema CRM conectado. Opções de CRM: Tabela do Azure, BLOB do Azure, Dynamics CRM Online, HTTPs ' ponto de extremidade, Marketo, Salesforce  |

## <a name="properties-page"></a>Página Propriedades

A página de propriedades é onde você define as categorias e os setores usados para agrupar sua oferta nos Marketplaces, os contratos legais que dão suporte à sua oferta e sua versão do aplicativo. Certifique-se de fornecer detalhes completos e precisos sobre sua oferta nesta página, para que ele seja exibido adequadamente e oferecido ao conjunto certo de clientes. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------|  
| Categoria e subcategoria | Necessário 1 e máximo 3. Padrão: Nenhum selecionado. |
| Indústrias e subsetors | Opcional. máximo de 2 setores L1 e 2 submercados em cada setor de L1, padrão: Nenhum selecionado |
| Versão do aplicativo  | Opcional. Padrão: nenhuma. |
| Usar contrato padrão  | Opcional. Padrão: não selecionado.  | |
| Termos de uso  | Obrigatório se o contrato padrão não estiver selecionado.  |

## <a name="offer-listing-page"></a>Página de listagem da oferta

A página de listagem é onde você fornece o texto e as imagens que os clientes veem ao exibir a listagem da sua oferta no Marketplace. 

| **Nome do campo**    | **Observações**   |
| :---------------- | :-----------| 
| Nome  | São necessários, no máximo 50 caracteres. |
| Resumo  | São necessários, no máximo 100 caracteres. | 
| Descrição  | São necessários, no máximo 3000 caracteres. |
| Instruções de introdução  | São necessários, no máximo 3000 caracteres. |
| Instruções de introdução  | São necessários, no máximo 3000 caracteres. |
| Palavras-chave para pesquisa  | Palavras-chave opcional, recomendado, máximo 3. |
| URL da política de privacidade  | Obrigatória. |
| URL de materiais de marketing do programa CSP  | Opcional. |
| Título de links úteis + URL  | Opcional. |
| Título de documentos de suporte + arquivo  | Obrigatório, mín. 1 e máx. 3. Deve ser formato de arquivo PDF. |
| Capturas de tela  | Necessário, mín. 1 captura de tela e máx. 5; quatro ou mais recomendados. Deve ser 1280 X 720 no formato PNG. |
| Logotipos da loja (pequeno, médio, grande, largo, herói)  | Pequeno (48 X 48) e grande (216 X 216) necessário; outros tamanhos opcionais, mas recomendados: Médio (90 x 90), largo (255 x 115), Hero (815 x 290). Deve estar no formato PNG. |
| Nome dos vídeos + URL + miniatura  | Opcional, recomendado, máximo de 4 vídeos. A miniatura deve ser 1280 x 720 no formato PNG. O vídeo deve ser hospedado no YouTube ou no Vimeo. |
| Contatos (programa CSP, engenharia, suporte)  | Contato de engenharia e suporte necessário (nome, email e número de telefone); Contato do programa CSP opcional, mas recomendado. |
| URL do suporte  | Obrigatória. |

## <a name="preview-page"></a>Página de visualização

A página de visualização é onde você especifica o público-alvo para ter acesso à versão prévia da oferta, para verificar se a oferta atende a todos os seus requisitos antes de entrar em tempo real. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Email do AAD/MSA + descrição | Obrigatório, min 1 e Max 10 se inseridos manualmente ou até 20 se estiver carregando um arquivo CSV. |

## <a name="technical-configuration-page"></a>Página de configuração técnica 

A página de configuração técnica é onde você especifica os detalhes técnicos usados pela Microsoft para se conectar à sua oferta. Esta página não será visível se você decidiu não vender pela Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| URL da página de aterrissagem | Necessário se estiver vendendo pela Microsoft. |
| Webhook de conexão | Necessário se estiver vendendo pela Microsoft. |
| ID do locatário do Azure AD | Necessário se estiver vendendo pela Microsoft. |
| ID do aplicativo do Azure AD | Necessário se estiver vendendo pela Microsoft. |

## <a name="plan-identity-modal"></a>Planejar identidade modal

As primeiras informações que você precisa fornecer são um nome e uma ID para seu plano. Esta página não será visível se você tiver decidido não vender pela Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| ID do plano  | Necessário se estiver vendendo pela Microsoft. Ele não pode ser alterado após a criação. Máximo de 50 caracteres e deve consistir apenas em letras minúsculas, caracteres alfanuméricos, traços ou sublinhados. |
| Nome do Plano  | Necessário se estiver vendendo pela Microsoft. Deve ser exclusivo em todos os planos na oferta. Máximo de 50 caracteres. |

## <a name="plan-listing-page"></a>Página de listagem do plano

A página de listagem do plano é onde você fornece o texto para que os clientes vejam ao exibir o plano no Marketplace. Esta página não será visível se você decidiu não vender pela Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| Descrição do Plano   | Necessário se estiver vendendo pela Microsoft. Máximo de 500 caracteres. | |

## <a name="plan-pricing--availability-page"></a>Planejar o preço & página disponibilidade

A página planejar preços e disponibilidade é onde você define as características de negócios, o público e a disponibilidade do mercado para cada plano (versão) de sua oferta. Esta página não será visível se você decidiu não vender pela Microsoft.

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Disponibilidade do mercado  | Obrigatório, mín. 1 e máx. 141. |
| Modelo de Preços  | Obrigatória. Padrão: Taxa fixa. Opções: Taxa fixa, por usuário. |
| Estações mínimas e máximas  | Opcional, disponível somente se o modelo de preços baseado em assentos estiver selecionado. |
| Termo de cobrança  | Obrigatória. Padrão: Mensais. Opções: Mensal, anual. |
| Preço  | Obrigatório USD por mês, se o termo de cobrança mensal for selecionado; ou USD por ano se o termo de cobrança anual selecionado. |
| Planejar público  | Opcional. Padrão: Plano público. Opções: Público, privado por ID de locatário |
| Público do plano restrito (ID do locatário + descrição)  | Necessário se o plano privado for selecionado. As IDs de locatário min 1 e Max 10 se forem inseridas manualmente. Máximo 20000 se a importação de arquivo CSV. |

## <a name="test-drive-listing-page"></a>Página de listagem do Test Drive

Disponível somente se você optar por oferecer um test drive para sua oferta. Defina os detalhes usados para listar os test drive no Marketplace.

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Descrição  | Obrigatória. |
| Nome manual do usuário + arquivo  | Necessário, máximo 1 documento. Deve ser o formato PDF. |
| Nome do vídeo, URL + miniatura  | Opcional, recomendado. A miniatura deve ser 533 x 324 no formato JPGP ou PNG. O vídeo deve ser hospedado no YouTube ou no Vimeo. |

## <a name="review-and-publish-page"></a>Página revisar e publicar

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Notas de certificação  | Opcional. |

## <a name="next-steps"></a>Próximas etapas

- [Criar uma nova oferta de SaaS](./create-new-saas-offer.md)
