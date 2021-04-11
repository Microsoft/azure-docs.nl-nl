---
title: Azure front-deur Premium verbinden met een interne load balancer oorsprong met persoonlijke koppeling
titleSuffix: Azure Private Link
description: Meer informatie over hoe u uw Azure front-deur Premium verbindt met een interne load balancer.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803726"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Azure front-deur Premium verbinden met een interne load balancer oorsprong met persoonlijke koppeling

In dit artikel vindt u instructies voor het configureren van Azure front-deur Premium SKU om verbinding te maken met uw interne load balancer-oorsprong met behulp van de Azure Private Link-service.

## <a name="prerequisites"></a>Vereisten

Maak een [persoonlijke koppelings service](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Persoonlijke koppeling naar een interne load balancer inschakelen
 
In deze sectie wijst u de persoonlijke koppelings service toe aan een persoonlijk eind punt dat is gemaakt in het particuliere netwerk van de front-deur van Azure. 

1. Selecteer in uw Azure front deur Premium-profiel onder *instellingen* de optie **oorspronkelijke groepen**.

1. Selecteer de oorspronkelijke groep waarvoor u een persoonlijke koppeling wilt inschakelen voor de interne load balancer.

1. Selecteer **+ een oorsprong toevoegen** om een interne Load Balancer oorsprong toe te voegen.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Scherm opname van het inschakelen van een persoonlijke koppeling naar een interne load balancer.":::

1. Selecteer voor **een Azure-resource selecteren** **in mijn Directory**. Selecteer of voer de volgende instellingen in om de site te configureren waarvoor Azure front-deur Premium moet worden aangesloten.

    | Instelling | Waarde |
    | ------- | ----- |
    | Region | Selecteer de regio die gelijk is aan of het dichtst bij uw oorsprong. |
    | Resourcetype | Selecteer **micro soft. Network/privateLinkServices**. |
    | Resource | Selecteer uw persoonlijke koppeling die is gekoppeld aan de interne load balancer. |
    | Doel-subresource | Leeg laten. |
    | Aanvraag bericht | Bericht aanpassen of de standaard waarde kiezen. |

1. Selecteer vervolgens **toevoegen** en vervolgens **bijwerken** om uw configuratie op te slaan.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Particuliere eindpunt verbinding van het opslag account goed keuren

1. Ga naar het persoonlijke koppelings centrum en selecteer **persoonlijke koppelings Services**. Selecteer vervolgens de naam van uw persoonlijke koppeling.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Scherm opname van de lijst met persoonlijke koppelingen.":::

1. Selecteer **particuliere endpoint-verbindingen** onder *instellingen*.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Scherm afbeelding van de pagina overzicht van persoonlijke koppelingen.":::

1. Selecteer de aanvraag voor een privé-eind punt in *behandeling* van Azure front-deur Premium en selecteer vervolgens **goed keuren**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Scherm opname van goed keuring in behandeling voor persoonlijke koppeling.":::

1. Na goed keuring moet de onderstaande scherm afbeelding eruitzien. Het duurt enkele minuten voordat de verbinding volledig tot stand is gebracht. U hebt nu toegang tot uw interne load balancer vanuit Azure front-deur Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Scherm opname van de aanvraag voor een goedgekeurde persoonlijke koppeling.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [service voor persoonlijke koppelingen](../../private-link/private-link-service-overview.md).
