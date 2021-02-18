---
title: Azure front deur Premium verbinden met uw oorsprong met persoonlijke koppeling
description: Meer informatie over hoe u met behulp van de Azure Portal uw Azure front-deur Premium kunt verbinden met uw oorsprong met een privé koppelings service.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098883"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Azure front deur Premium verbinden met uw oorsprong met persoonlijke koppeling

In dit artikel vindt u instructies voor het configureren van Azure front-deur Premium SKU om verbinding te maken met uw toepassingen die worden gehost in een virtueel netwerk met behulp van de service Azure private link.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Maak een [persoonlijke koppelings](../../private-link/create-private-link-service-portal.md) service voor uw oorspronkelijke webservers.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Persoonlijk eind punt inschakelen in de Azure front-deur service

In deze sectie wijst u de Azure Private Link-service toe aan een persoonlijk eind punt dat is gemaakt in het particuliere netwerk van de Azure front deur Premium-SKU. 

1. Selecteer in uw Azure front deur Premium-profiel onder *instellingen* de optie **oorspronkelijke groepen**.

1. Selecteer de oorspronkelijke groep die de oorsprong bevat waarvoor u een privé-koppeling wilt inschakelen.

1. Selecteer **+ een oorsprong toevoegen** om een nieuwe oorsprong toe te voegen of selecteer een eerder gemaakte oorsprong in de lijst. Schakel vervolgens het selectie vakje in om de **service voor persoonlijke koppelingen in te scha kelen**.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Scherm opname van het inschakelen van een persoonlijke koppeling in een originele pagina toevoegen.":::

1. Selecteer voor **een Azure-resource selecteren** **in mijn Directory**. Selecteer of voer de volgende instelling in om de resource te configureren die u wilt laten verbinden met Azure front deur Premium.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Region | Selecteer de regio die gelijk is aan of het dichtst bij uw oorsprong. |
    | Resourcetype | Selecteer **micro soft. Network/privateLinkServices**. |
    | Resource | Selecteer **myPrivateLinkService**. |
    | Doel-subresource | Laat dit veld leeg. |
    | Aanvraag bericht | Bericht aanpassen of het standaard bericht kiezen. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure front deur Premium-koppeling](concept-private-link.md).
