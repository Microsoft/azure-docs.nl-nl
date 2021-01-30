---
title: Privé aanbiedingen in micro soft Commercial Marketplace
description: Persoonlijke aanbiedingen in micro soft Commercial Marketplace voor app-en service-Publishers.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 01/28/2021
ms.openlocfilehash: 786ecbf553ace6a90515347e8138eeb6e022589b
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063093"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Persoonlijke aanbiedingen in micro soft Commercial Marketplace

Persoonlijke aanbiedingen, ook wel privé-abonnementen genoemd, kunnen uitgevers plannen maken die alleen zichtbaar zijn voor doel klanten. In dit artikel worden de opties en voor delen van privé aanbiedingen beschreven.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Zakelijke deals met persoonlijke aanbiedingen ontgrendelen

Door persoonlijke aanbiedingen te maken, kunnen uitgevers persoonlijke oplossingen aanbieden aan doel klanten, met mogelijkheden die ondernemingen nodig hebben:

- Met de _overeengekomen prijzen_ kunnen uitgevers kortingen en afwijkende prijzen uit openbaar beschik bare aanbiedingen uitbreiden.
- Met _persoonlijke voor_ waarden kunnen uitgevers voor waarden aanpassen aan een specifieke klant.
- Met _gespecialiseerde configuraties_ kunnen uitgevers hun virtual machines, Azure-toepassingen en software als een service (SaaS) aanpassen aan de behoeften van een individuele klant. Met deze optie kunnen uitgevers ook preview-toegang bieden tot nieuwe product functies, voordat ze worden gestart voor alle klanten.

Met persoonlijke aanbiedingen kunnen uitgevers profiteren van de schaal en wereld wijde Beschik baarheid van een open bare Marketplace, met de flexibiliteit en controle die nodig is om te onderhandelen en aangepaste deals en configuraties te leveren. Ondernemingen kunnen nu kopen en verkopen op hun verwachte manier 

## <a name="create-private-offers-using-plans"></a>Persoonlijke aanbiedingen maken met abonnementen

Voor *nieuwe of bestaande aanbiedingen met abonnementen* kunnen uitgevers eenvoudig nieuwe, persoonlijke variaties maken door nieuwe plannen (voorheen sku's) te maken en ze als privé te markeren. Elke aanbieding kan Maxi maal 45 privé-abonnementen hebben.

<!--- [Private SKUs]() --->

Privé plannen zijn beschikbaar voor de volgende aanbiedings typen:

- Virtuele Azure-machine
- Azure-toepassing (geïmplementeerd als oplossings sjablonen of beheerde toepassingen)
- Beheerde service
- SaaS-aanbiedingen

Privé plannen zijn onderdelen van een aanbieding en zijn alleen zichtbaar en tevens door de doel klanten. Privé plannen zijn alleen zichtbaar en tevens door de doel klanten.  Persoonlijke abonnementen kunnen worden gemaakt voor klanten in zowel Azure Global als Azure Government.

Privé plannen kunnen de basis installatie kopieën en/of de meta gegevens van de aanbieding die al zijn gepubliceerd voor een openbaar abonnement hergebruiken. Met deze optie kunnen uitgevers meerdere persoonlijke variaties van een open bare aanbieding maken zonder dat ze meerdere versies van dezelfde basis installatie kopie hoeven te publiceren en meta gegevens te bieden. Voor de Azure virtual machine en de Azure-toepassing bieden alleen, wanneer een privé-plan een basis installatie kopie deelt met een openbaar abonnement, alle wijzigingen in de basis installatie kopie van de aanbieding worden door gegeven aan alle open bare en persoonlijke plannen met die basis installatie kopie.

Voor *nieuwe aanbiedingen die alleen persoonlijke abonnementen bevatten*, kunnen uitgevers hun aanbiedingen als elk ander aanbod maken en de abonnementen markeren als privé. De aanbiedingen die alleen privé abonnementen hebben, kunnen niet worden gedetecteerd of [Azure Portal](https://azure.microsoft.com/features/azure-portal/) toegankelijk zijn voor klanten die niet aan de aanbieding zijn gekoppeld.

>[!NOTE]
>Een aanbieding die alleen persoonlijke plannen bevat, is niet zichtbaar in de open bare Azure Marketplace of AppSource.

## <a name="target-customers-with-private-offers"></a>Doel klanten met privé aanbiedingen

Voor zowel nieuwe als bestaande persoonlijke aanbiedingen kunnen uitgevers klanten richten met behulp van abonnements-id's. Voor Azure virtual machine, Azure-toepassing en beheerde service aanbiedingen kunnen uitgevers de beschik baarheid van een privé plan beperken tot een afzonderlijke Azure-abonnements-ID of een CSV van Maxi maal 10.000 Azure-abonnement-Id's uploaden. Voor SaaS-aanbiedingen kunnen uitgevers een Azure Active Directory Tenant-ID koppelen om de beschik baarheid van een privé-abonnement te beperken met behulp van de hand matige of CSV-upload methode.

Zodra een aanbieding is gecertificeerd en gepubliceerd, kunnen klanten worden bijgewerkt of uit het plan worden verwijderd met behulp van de functie Persoonlijke abonnementen synchroniseren. Met deze mogelijkheid kunnen uitgevers snel en eenvoudig de lijst met klanten bijwerken waarvoor het privé plan wordt gepresenteerd zonder dat de aanbieding opnieuw wordt gecertificeerd of gepubliceerd.

## <a name="deploying-private-offers"></a>Persoonlijke aanbiedingen implementeren

Wanneer klanten zich hebben aangemeld bij de Azure Portal, kunnen ze deze stappen volgen om uw persoonlijke aanbiedingen te selecteren.

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com/).
1. Onder **Azure-Services** selecteert u **een resource maken**.
1. Selecteer op de pagina **Nieuw** naast **Azure Marketplace** de optie **alles weer geven**. De Marketplace-pagina wordt weer gegeven.
1. Selecteer **privé aanbiedingen** in het linkernavigatievenster.

> [!NOTE]
> Persoonlijke aanbiedingen kunnen alleen worden gedetecteerd in [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Ze worden niet weer gegeven in [Microsoft AppSource](https://appsource.microsoft.com/) of [Azure Marketplace](https://azuremarketplace.microsoft.com). Zie [Introduction to List Options](./determine-your-listing-type.md)(Engelstalig) voor meer informatie over het publiceren naar de verschillende online winkels voor commerciële markt plaatsen.

Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten en kunnen worden geïmplementeerd via opdracht regel-en Azure Resource Manager sjablonen, zoals andere aanbiedingen.

![[Persoonlijke aanbiedingen]](./media/marketplace-publishers-guide/private-offer.png)

Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten. U ziet gewoon de **persoonlijke** badge.

>[!Note]
>Persoonlijke aanbiedingen worden niet ondersteund met abonnementen die zijn gemaakt via een wederverkoper van het programma van de Cloud Solution Provider (CSP).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
