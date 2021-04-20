---
title: Azure Marketplace
description: Hierin wordt beschreven hoe EA-klanten Azure Marketplace kunnen gebruiken
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: ce9dff017a796e420586ad191a59c149bed07190
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726822"
---
# <a name="azure-marketplace"></a>Azure Marketplace

In dit artikel wordt uitgelegd hoe EA-klanten en -partners de Marketplace-kosten kunnen bekijken en Azure Marketplace-aankopen kunnen inschakelen.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace voor EA-klanten

Voor directe klanten zijn Azure Marketplace-kosten zichtbaar in de Azure Enterprise-portal. Azure Marketplace-aankopen en -verbruik worden buiten de Azure-vooruitbetaling gefactureerd, en worden per kwartaal of per maand gefactureerd.

Indirecte klanten kunnen hun Azure Marketplace-abonnementen vinden op de pagina **Abonnementen beheren** van de Azure Enterprise-portal, maar de prijzen zijn verborgen. Klanten kunnen contact opnemen met hun LSP (Licensing Solutions Provider) voor informatie over de Azure Marketplace-kosten.

Nieuwe maandelijks of jaarlijks terugkerende Azure Marketplace-aankopen worden volledig gefactureerd tijdens de periode waarin Azure Marketplace-items zijn aangeschaft. Deze items worden in de volgende periode op dezelfde dag van de oorspronkelijke aankoop automatisch verlengd.

Bestaande, maandelijkse terugkerende kosten worden op de eerste dag van elke kalendermaand verlengd. Jaarlijkse kosten worden verlengd op de aankoopdatum.

Sommige services van externe resellers die beschikbaar zijn op Azure Marketplace, gebruiken nu het saldo van uw Azure-vooruitbetaling voor Enterprise-overeenkomsten (EA). Eerder werden deze services buiten de Azure-vooruitbetaling voor EA gehouden en werden ze afzonderlijk gefactureerd. De Azure-vooruitbetaling voor EA voor deze services in Azure Marketplace vereenvoudigt het aankoopproces voor klanten en de betalingsafhandeling. Raadpleeg de [update van 6 maart 2018, op de Azure-website](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) voor een volledige lijst met services die nu uw Azure-vooruitbetaling gebruiken.

### <a name="partners"></a>Partners

LSP’s kunnen een Azure Marketplace-prijslijst downloaden op de pagina met het prijzenoverzicht in de Azure Enterprise-portal. Selecteer de koppeling **Marketplace-prijslijst** in de rechterbovenhoek. In de Azure Marketplace-prijslijst kunt u alle beschikbare services en de bijbehorende prijzen bekijken.

De prijslijst downloaden:

1. Ga in de Azure Enterprise-portal naar **Rapporten** > **Prijzenoverzicht**.
1. Zoek in de rechterbovenhoek de koppeling naar de Azure Marketplace-prijslijst onder uw gebruikersnaam.
1. Klik met de rechtermuisknop op de koppeling en selecteer **Document opslaan als**.
1. Wijzig in het venster **Opslaan** de titel van het document in `AzureMarketplacePricelist.zip`. Hierdoor wordt het bestand gewijzigd van een xlsx-bestand in een zip-bestand.
1. Nadat het downloaden is voltooid, hebt u een zip-bestand met landspecifieke prijslijsten.
1. LSP's moeten verwijzen naar het afzonderlijke landsbestand voor landspecifieke prijzen. LSP’s kunnen het tabblad **Meldingen** gebruiken voor informatie over SKU’s die net nieuw zijn of buiten gebruik zijn gesteld.
1. Prijzen worden niet vaak gewijzigd. LSP’s ontvangen 30 dagen vooraf een e-mailmelding over prijsverhogingen en wijzigingen in vreemde valuta (FX).
1. LSP's ontvangen per kwartaal één factuur per inschrijving en per ISV.

### <a name="enabling-azure-marketplace-purchases"></a>Azure Marketplace-aankopen inschakelen

Ondernemingsbeheerders kunnen Azure Marketplace-aankopen uit- of inschakelen voor alle Azure-abonnementen onder hun inschrijving. Als de ondernemingsbeheerder de aankopen uitschakelt en er Azure-abonnementen zijn die al Azure Marketplace-abonnementen hebben, worden deze Azure Marketplace-abonnementen niet geannuleerd of beïnvloed.

Hoewel klanten hun directe Azure-abonnementen naar Azure EA kunnen converteren door ze te koppelen aan hun inschrijving in de Azure Enterprise-portal, worden de onderliggende abonnementen met deze actie niet automatisch geconverteerd.

Azure Marketplace-aankopen inschakelen:

1. Meld u als ondernemingsbeheerder aan bij de Azure Enterprise-portal.
1. Ga naar **Beheren**.
1. Selecteer onder **Details van inschrijving** het potloodpictogram naast het regelitem **Azure Marketplace**.
1. Gebruik de wisselknop **Ingeschakeld/uitgeschakeld** of **Alleen gratis/BYOL-SKU's** indien van toepassing.
1. Selecteer **Opslaan**.

> [!NOTE]
> BYOL (Bring Your Own License) en met de optie Alleen gratis kunt u de aankoop en verwerving van Azure Marketplace-SKU's beperken tot BYOL en Alleen gratis SKU's.

### <a name="services-billed-hourly-for-azure-ea"></a>Services die per uur worden gefactureerd voor Azure EA

De volgende services worden per uur gefactureerd onder Enterprise Agreement, in plaats van het maandelijkse tarief in MOSP:

- Application Delivery Network
- Web Application Firewall

### <a name="azure-remoteapp"></a>Azure RemoteApp

Als u een Enterprise Agreement hebt, betaalt u voor Azure RemoteApp op basis van het Enterprise Agreement-prijsniveau. Er zijn geen extra kosten. De standaardprijs is inclusief de eerste 40 uur. De onbeperkte prijs omvat de eerste 80 uur. Het gebruik van een RemoteApp stopt na 80 uur.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Prijzen](ea-pricing-overview.md).
- Lees de [Cost Management + Billing veelgestelde](../cost-management-billing-faq.yml) vragen voor een lijst met vragen en antwoorden over Azure Marketplace services en Azure EA-vooruitbetaling.