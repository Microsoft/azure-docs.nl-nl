---
title: Standard-contract voor micro soft Commercial Marketplace
description: Standard-contract voor Azure Marketplace en AppSource in Partner Center
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/20/2020
ms.openlocfilehash: 20a257bde6022249fd7b2ab875b94f356234b490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94488873"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Standard-contract voor micro soft Commercial Marketplace

Micro soft biedt een standaard contract voor micro soft Commercial Marketplace. Dit helpt het aankoop proces voor klanten te vereenvoudigen, de juridische complexiteit voor software leveranciers te verlagen en trans acties op Marketplace te vergemakkelijken. In plaats van aangepaste voor waarden te gebruiken, kunt u als een commerciële Marketplace-Uitgever kiezen om uw software aan te bieden onder het [Standard-contract](https://go.microsoft.com/fwlink/?linkid=2041178), wat klanten alleen hoeven te bevestigen en één keer te accepteren.

De voor waarden voor een aanbieding worden gedefinieerd bij het maken van de aanbieding in Partner Center. U kunt het standaard contract voor de micro soft Commercial Marketplace selecteren, in plaats van uw eigen aangepaste voor waarden op te geven.

>[!Note]
>Zodra u een aanbieding publiceert met het standaard contract voor de micro soft Commercial Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt uw oplossing aanbieden onder het standaard contract *of* uw eigen voor waarden. Aangepaste voor waarden worden gedefinieerd op het niveau van de aanbieding en gelden voor alle plannen; Schrijf uw aangepaste voor waarden op de pagina **Eigenschappen** van uw aanbieding in partner centrum. Als u de voor waarden van het standaard contract wilt wijzigen, kunt u dit doen via de standaard wijzigingen in contracten.

## <a name="standard-contract-amendments"></a>Wijzigingen in het standaard contract

Met de wijzigingen in het standaard contract kunnen uitgevers het standaard contract voor eenvoud selecteren en met aangepaste voor waarden voor hun product of bedrijf. Klanten hoeven alleen de wijzigingen in het contract door te nemen als ze het micro soft Standard-contract al hebben gecontroleerd en geaccepteerd.

Er zijn twee soorten wijzigingen beschikbaar voor uitgevers van commerciële Marketplace:

* Universele wijzigingen: deze wijzigingen worden universeel toegepast op het Standard-contract voor alle klanten. Universele wijzigingen worden weer gegeven aan elke klant van de aanbieding in de inkoop stroom. Klanten moeten akkoord gaan met de voor waarden van het standaard contract en de wijziging voordat ze uw aanbieding kunnen gebruiken.

* Aangepaste wijzigingen: deze wijzigingen zijn speciale wijzigingen in het standaard contract die alleen voor specifieke klanten zijn gericht via Azure-Tenant-Id's. Uitgevers kunnen de Tenant kiezen die ze willen instellen. Alleen klanten van de Tenant worden weer gegeven met de aangepaste wijzigings voorwaarden in de inkoop stroom van de aanbieding.  Klanten moeten akkoord gaan met de voor waarden van het standaard contract en de wijziging (en) voordat ze uw aanbieding kunnen gebruiken.

>[!Note]
>Deze twee typen wijzigingen worden boven op elkaar gestapeld. Klanten die zijn gericht op aangepaste wijzigingen, krijgen ook de universele wijziging in het standaard contract tijdens de aankoop. Wijzigingen zijn beperkt tot 4000 tekens, inclusief spaties.

U kunt gebruikmaken van het Standard-contract voor de micro soft Commercial Marketplace voor de volgende aanbiedings typen: Azure-toepassingen (oplossings sjablonen en beheerde toepassingen), Virtual Machines en SaaS.

## <a name="customer-experience"></a>Gebruikers ervaring

Tijdens de detectie-ervaring in azure Marketplace of AppSource kunnen klanten de voor waarden van de aanbieding als standaard contract voor de micro soft Commercial Marketplace en eventuele universele wijzigingen bekijken.

![De Azure Portal klant detectie-ervaring.](media/marketplace-publishers-guide/azure-discovery-process.png)

Tijdens het aankoop proces in de Azure Portal kunnen klanten de voor waarden van de aanbieding als standaard contract voor de micro soft Commercial Marketplace en eventuele wijzigingen in universele en/of tenants bekijken.

![De Azure Portal klant aankoop ervaring.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Klanten kunnen Get-AzureRmMarketplaceTerms gebruiken om de voor waarden van een aanbieding op te halen en deze te accepteren. Het standaard contract en de bijbehorende wijzigingen worden geretourneerd in de uitvoer van de cmdlet.
