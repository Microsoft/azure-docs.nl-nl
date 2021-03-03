---
title: Azure front-deur Premium koppelen aan een web-app-oorsprong met een persoonlijke koppeling
titleSuffix: Azure Private Link
description: Meer informatie over hoe u uw Azure front-deur Premium kunt verbinden met een webwebapp.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: f3250101449971ffd985fcfcce400870e2a3d50f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745364"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Azure front-deur Premium koppelen aan een web-app-oorsprong met een persoonlijke koppeling

In dit artikel vindt u instructies voor het configureren van Azure front-deur Premium SKU om via de Azure Private Link-service privé verbinding te maken met uw web-app.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Maak een [persoonlijke koppelings](../../private-link/create-private-link-service-portal.md) service voor uw oorspronkelijke webservers.

> [!Note]
> Privé-eindpunten zijn beschikbaar in openbare regio’s voor de prijscategorieën PremiumV2 en PremiumV3, voor web-apps in Windows en Linux, en voor het Azure Functions Premium-abonnement (soms het Elastische Premium-abonnement genoemd).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Een persoonlijke koppeling naar een web-app inschakelen in azure front-deur Premium
 
In deze sectie wijst u de persoonlijke koppelings service toe aan een persoonlijk eind punt dat is gemaakt in het particuliere netwerk van de front-deur van Azure. 

1. Selecteer in uw Azure front deur Premium-profiel onder *instellingen* de optie **oorspronkelijke groepen**.

1. Selecteer de oorspronkelijke groep met de oorsprong van de web-app waarvoor u een persoonlijke koppeling wilt inschakelen.

1. Selecteer **+ een oorsprong toevoegen** om een nieuwe oorsprong van een web-app toe te voegen of selecteer een eerder gemaakte web-app-oorsprong in de lijst.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Scherm opname van het inschakelen van een persoonlijke koppeling naar een web-app.":::

1. Selecteer voor **een Azure-resource selecteren** **in mijn Directory**. Selecteer of voer de volgende instellingen in om de site te configureren waarvoor Azure front-deur Premium moet worden aangesloten.

    | Instelling | Waarde |
    | ------- | ----- |
    | Region | Selecteer de regio die gelijk is aan of het dichtst bij uw oorsprong. |
    | Resourcetype | Selecteer **Microsoft.Web/sites**. |
    | Resource | Selecteer **myPrivateLinkService**. |
    | Doel-subresource | sites |
    | Aanvraag bericht | Bericht aanpassen of de standaard waarde kiezen. |

1. Selecteer vervolgens **toevoegen** om uw configuratie op te slaan.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Azure front deur Premium-verbinding met privé-eind punt goed keuren via web-app

1. Ga naar de web-app waarvoor u een persoonlijke koppeling in de laatste sectie hebt geconfigureerd. Selecteer **netwerken** onder **instellingen**.

1. Selecteer in **Netwerken** de optie **Uw privé-eindpuntverbindingen configureren**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Scherm opname van netwerk instellingen in een web-app.":::

1. Selecteer de aanvraag voor een privé-eind punt in *behandeling* van Azure front-deur Premium en selecteer vervolgens **goed keuren**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Scherm opname van aanvraag voor privé-eind punt in behandeling.":::

1. Na goed keuring moet de onderstaande scherm afbeelding eruitzien. Het duurt enkele minuten voordat de verbinding volledig tot stand is gebracht. U hebt nu toegang tot uw web-app vanuit Azure front-deur Premium. Directe toegang tot de web-app vanaf het open bare Internet wordt uitgeschakeld nadat het persoonlijke eind punt is ingeschakeld.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Scherm opname van goedgekeurde eindpunt aanvraag.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [service voor persoonlijke koppelingen met Azure web app](../../app-service/networking/private-endpoint).
