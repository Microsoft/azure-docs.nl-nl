---
title: Technische configuratie van Dynamics 365 for Customer Engagement & PowerApps-aanbieding op Microsoft AppSource (Azure Marketplace)
description: Dynamics 365 for Customer Engagement & PowerApps bieden technische configuratie op Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820395"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Dynamics 365 for Customer Engagement instellen voor & Power Apps technische configuratie van aanbieding

Deze pagina definieert de technische details die worden gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kunnen we uw aanbieding inrichten voor de eindklant als ze ervoor kiezen om deze te verkrijgen.

## <a name="offer-information"></a>Aanbiedingsgegevens

**Het basislicentiemodel** bepaalt hoe klanten uw toepassing toewijzen in het CRM-beheercentrum. Selecteer **Resource** voor licentieverlening op basis van een exemplaar **of** Gebruiker als er een licentie per tenant is toegewezen.

Het **selectievakje Vereist uitgaande S2S-** en CRM Secure Store-toegang schakelt de configuratie van uitgaande toegang voor CRM Secure Store of Server-to-Server (S2S) in. Deze functie vereist speciale aandacht van het Dynamics 365-team tijdens de certificeringsfase. Microsoft neemt contact met u op om aanvullende stappen uit te voeren ter ondersteuning van deze functie.

Laat **Toepassingsconfiguratie-URL** leeg; dit is voor toekomstig gebruik.

## <a name="crm-package"></a>CRM-pakket

Voer **bij URL van de pakketlocatie** de URL in van een Azure Blob Storage account dat het geüploade ZIP-bestand van het CRM-pakket bevat. Neem een alleen-lezen SAS-sleutel op in de URL, zodat Microsoft uw pakket kan ophalen voor verificatie.

> [!IMPORTANT]
> Om een publicatieblok te voorkomen, moet u ervoor zorgen dat de vervaldatum in de URL van uw Blob-opslag niet is verlopen. U kunt de datum herzien door uw beleid te openen. We raden u **aan de verlooptijd** in de toekomst ten minste één maand te laten verlopen.

Selecteer het **vak Er is meer dan één CRM-pakket in mijn pakketbestand,** indien van toepassing. Als dat het zo is, moet u alle pakketten in uw ZIP-bestand opnemen.

Zie Stap [3:](/powerapps/developer/common-data-service/create-package-app-appsource)een AppSource-pakket maken voor uw app voor gedetailleerde informatie over het bouwen van uw pakket en het bijwerken van de structuur.

## <a name="crm-package-availability"></a>Beschikbaarheid van CRM-pakketten

Selecteer **+ Regio toevoegen om** de geografische regio's op te geven waarin uw CRM-pakket beschikbaar zal zijn voor klanten. Implementatie in de volgende soevereine regio's vereist speciale machtigingen en validatie tijdens het certificeringsproces: [Duitsland,](../germany/index.yml) [US Government Cloud](../azure-government/documentation-government-welcome.md)en TIP.

Standaard wordt de **toepassingsconfiguratie-URL die** u hierboven hebt ingevoerd, gebruikt voor elke regio. Als u dat liever hebt, kunt u een afzonderlijke TOEPASSINGsconfiguratie-URL invoeren voor een of meer specifieke regio's.

Selecteer **Concept opslaan** voordat u verdergaat met het volgende tabblad in het linkernavigatiemenu, **Co-sell met Microsoft**. Zie Partnerbetrokkenheid voor co-verkoop voor meer informatie over het instellen van [co-sell met Microsoft (optioneel).](marketplace-co-sell.md) Als u geen co-verkoop instelt of als u klaar bent, gaat u verder met **volgende stappen** hieronder.

## <a name="next-steps"></a>Volgende stappen

- [Aanvullende inhoud configureren](dynamics-365-customer-engage-supplemental-content.md)
