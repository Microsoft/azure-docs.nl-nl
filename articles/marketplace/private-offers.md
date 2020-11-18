---
title: Privé aanbiedingen in micro soft Commercial Marketplace
description: Persoonlijke aanbiedingen in micro soft Commercial Marketplace voor app-en service-Publishers.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: 5396845e13e94b4d957f42618baaf19c408f559d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843771"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Persoonlijke aanbiedingen in micro soft Commercial Marketplace

Met persoonlijke aanbiedingen in [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) kunnen uitgevers plannen maken die alleen zichtbaar zijn voor doel klanten. In dit artikel worden de opties en voor delen van privé aanbiedingen beschreven.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Zakelijke deals met persoonlijke aanbiedingen ontgrendelen

Enter prise-klanten gebruiken vaker online markt plaatsen om cloud oplossingen te zoeken, te proberen en te kopen. Uitgevers kunnen nu met persoonlijke aanbiedingen gebruikmaken van Marketplace om persoonlijke oplossingen te delen met de doel klanten, met mogelijkheden die ondernemingen nodig hebben:

- Met de *overeengekomen prijzen* kunnen uitgevers kortingen en afwijkende prijzen uit openbaar beschik bare aanbiedingen uitbreiden.
- Met *persoonlijke voor* waarden kunnen uitgevers voor waarden aanpassen aan een specifieke klant.
- Met *gespecialiseerde configuraties* kunnen uitgevers hun virtual machines, Azure-toepassingen en SaaS-apps aanpassen aan de behoeften van een individuele klant. Met deze optie kunnen uitgevers ook preview-toegang bieden tot nieuwe product functies, voordat ze ruim uitgebreid worden gestart naar alle klanten.

Met persoonlijke aanbiedingen kunnen uitgevers profiteren van de schaal en wereld wijde Beschik baarheid van een open bare Marketplace, met de flexibiliteit en controle die nodig is om te onderhandelen en aangepaste deals en configuraties te leveren. Met deze functies wordt de deur van een krachtige Enter prise-acceptatie van Cloud marketplaces geopend. Ondernemingen kunnen nu kopen en verkopen op hun verwachte en aanbod wijze.

Persoonlijke aanbiedingen zijn nu beschikbaar voor virtuele machines, Azure-toepassing (geïmplementeerd als oplossings sjablonen of beheerde toepassingen) en SaaS-apps bieden.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Persoonlijke aanbiedingen maken met abonnementen

Voor *nieuwe of bestaande aanbiedingen met abonnementen* kunnen uitgevers eenvoudig nieuwe, persoonlijke variaties maken door nieuwe plannen (voorheen sku's) te maken en ze als privé te markeren. Elke aanbieding kan Maxi maal 45 privé-abonnementen hebben.

<!--- [Private SKUs]() --->

Privé plannen zijn onderdelen van een aanbieding en zijn alleen zichtbaar en tevens door de doel klanten. Privé plannen kunnen de basis installatie kopieën en/of de meta gegevens van de aanbieding die al zijn gepubliceerd voor een openbaar abonnement hergebruiken. Met deze optie kunnen uitgevers meerdere persoonlijke variaties van een open bare aanbieding maken zonder dat ze meerdere versies van dezelfde basis installatie kopie hoeven te publiceren en meta gegevens te bieden. Voor de aanbiedingen van de virtuele machine en Azure-toepassing geldt dat als een persoonlijk plan een basis installatie kopie deelt met een openbaar abonnement, alle wijzigingen in de basis installatie kopie van de aanbieding worden door gegeven aan alle open bare en persoonlijke plannen die gebruikmaken van die basis installatie kopie.

Voor *nieuwe aanbiedingen die alleen persoonlijke abonnementen bevatten*, kunnen uitgevers hun aanbiedingen als elk ander aanbod maken en de abonnementen markeren als privé. De aanbiedingen die alleen privé abonnementen hebben, kunnen niet worden gedetecteerd of toegankelijk via micro soft Commercial Marketplace of de [Azure Portal](https://azure.microsoft.com/features/azure-portal/) door klanten die niet aan de aanbieding zijn gekoppeld.

>[!NOTE]
>Een aanbieding die alleen persoonlijke plannen bevat, is niet zichtbaar in de open bare Azure Marketplace of AppSource.

## <a name="targeting-customers-with-private-offers"></a>Klanten richten met persoonlijke aanbiedingen

Voor zowel nieuwe als bestaande persoonlijke aanbiedingen kunnen uitgevers klanten richten met behulp van abonnements-id's. Uitgevers die gebruikmaken van een virtuele machine of Azure-toepassing aanbieding, kunnen de beschik baarheid van een privé plan beperken tot een afzonderlijke Azure-abonnements-ID of een CSV van Maxi maal 20.000 Azure-abonnement-Id's uploaden. Bij het gebruik van een SaaS-app-aanbieding kunnen uitgevers een Tenant-ID koppelen om de beschik baarheid van een privé-abonnement te beperken met behulp van de hand matige of CSV-upload methode.

Zodra een aanbieding is gecertificeerd en gepubliceerd, kunnen klanten binnen enkele minuten worden bijgewerkt of uit het plan worden verwijderd met behulp van de functie Persoonlijke abonnementen synchroniseren. Met deze mogelijkheid kunnen uitgevers snel en eenvoudig de lijst met klanten bijwerken waarvoor het privé plan wordt gepresenteerd zonder dat de aanbieding opnieuw wordt gecertificeerd of gepubliceerd.

>[!NOTE]
>Azure gov toepasselijkheid: persoonlijke aanbiedingen (of nauw keuriger: privé-abonnementen) zijn beschikbaar voor gov Cloud, zoals elke andere aanbieding. Een plan kan ook de status van privé naar openbaar verplaatsen en vice versa. Het besturings element waarop de aanbieding beschikbaar is, bevindt zich in de handen van de partner en kan worden geconfigureerd via partner centrum. 

## <a name="deploying-private-offers"></a>Persoonlijke aanbiedingen implementeren

Persoonlijke aanbiedingen kunnen alleen worden gedetecteerd via de [Azure Portal](https://azure.microsoft.com/features/azure-portal/) en worden niet weer gegeven in [Microsoft AppSource](https://appsource.microsoft.com/) of [Azure Marketplace](https://azuremarketplace.microsoft.com). Zie [uw publicatie optie bepalen](./determine-your-listing-type.md)voor meer informatie over het publiceren naar de verschillende online winkels voor commerciële markt plaatsen.

Zodra klanten zijn aangemeld bij de Azure Portal, kunnen ze het navigatie-element voor Marketplace selecteren voor toegang tot hun persoonlijke aanbiedingen. Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten en kunnen worden geïmplementeerd via opdracht regel-en Azure Resource Manager sjablonen zoals andere aanbiedingen.

![[Persoonlijke aanbiedingen]](./media/marketplace-publishers-guide/private-offer.png)

Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten. U ziet gewoon de **persoonlijke** badge.

>[!Note]
>Persoonlijke aanbiedingen worden niet ondersteund met abonnementen die zijn gemaakt via een wederverkoper van het programma van de Cloud Solution Provider (CSP).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
