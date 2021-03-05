---
title: Azure front-deur Premium verbinden met een opslag account oorsprong met een persoonlijke koppeling
titleSuffix: Azure Private Link
description: Meer informatie over hoe u uw Azure front-deur Premium kunt verbinden met een opslag account.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201665"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Azure front-deur Premium verbinden met een opslag account oorsprong met een persoonlijke koppeling

In dit artikel vindt u instructies voor het configureren van Azure front-deur Premium SKU om een privé verbinding te maken met uw opslag account met behulp van de Azure Private Link service.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Een persoonlijke koppeling naar een opslag account inschakelen
 
In deze sectie wijst u de persoonlijke koppelings service toe aan een persoonlijk eind punt dat is gemaakt in het particuliere netwerk van de front-deur van Azure. 

1. Selecteer in uw Azure front deur Premium-profiel onder *instellingen* de optie **oorspronkelijke groepen**.

1. Selecteer de oorspronkelijke groep die het opslag account bevat waarvan u een persoonlijke koppeling wilt inschakelen.

1. Selecteer **+ een oorsprong toevoegen** om een nieuwe herkomst van het opslag account toe te voegen of selecteer een eerder gemaakte opslag account in de lijst.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Scherm opname van het inschakelen van een persoonlijke koppeling naar een opslag account.":::

1. Selecteer voor **een Azure-resource selecteren** **in mijn Directory**. Selecteer of voer de volgende instellingen in om de site te configureren waarvoor Azure front-deur Premium moet worden aangesloten.

    | Instelling | Waarde |
    | ------- | ----- |
    | Region | Selecteer de regio die gelijk is aan of het dichtst bij uw oorsprong. |
    | Resourcetype | Selecteer **micro soft. Storage/Storage accounts**. |
    | Resource | Selecteer uw opslagaccount. |
    | Doel-subresource | U kunt *BLOB* of *Web* selecteren. |
    | Aanvraag bericht | Bericht aanpassen of de standaard waarde kiezen. |

1. Selecteer vervolgens **toevoegen** om uw configuratie op te slaan.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Particuliere eindpunt verbinding van het opslag account goed keuren

1. Ga naar het opslag account waarvoor u een persoonlijke koppeling in de laatste sectie hebt geconfigureerd. Selecteer **netwerken** onder **instellingen**.

1. Selecteer in **netwerken** **particuliere endpoint-verbindingen**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Scherm opname van netwerk instellingen in een web-app.":::

1. Selecteer de aanvraag voor een privé-eind punt in *behandeling* van Azure front-deur Premium en selecteer vervolgens **goed keuren**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Scherm afbeelding van privé-eindpunt aanvraag in behandeling.":::

1. Na goed keuring moet de onderstaande scherm afbeelding eruitzien. Het duurt enkele minuten voordat de verbinding volledig tot stand is gebracht. U hebt nu toegang tot uw opslag account vanuit Azure front-deur Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Scherm opname van de goedgekeurde opslag eindpunt aanvraag.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [service voor persoonlijke koppelingen met het opslag account](../../storage/common/storage-private-endpoints.md).
