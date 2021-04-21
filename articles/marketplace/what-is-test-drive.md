---
title: Wat is een test drive? Commerciële marketplace van Microsoft
description: Uitleg van marketplace test drive functie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: e44d5d94a8dc172962a26f3e0dae9ccbb7f8a865
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818886"
---
# <a name="what-is-a-test-drive"></a>Wat is een test drive?

Een test drive is een uitstekende manier om uw aanbieding aan potentiële klanten te presenteren door hen de mogelijkheid te geven om het te proberen voordat u iets koopt, waardoor zeer gekwalificeerde leads worden gegenereerd en de conversie is toegenomen. Een test drive brengt uw product tot leven in een praktijkscenario voor implementatie. Klanten die uw product uitproberen, demonstreren een duidelijke intentie om een vergelijkbare oplossing te kopen. Gebruik dit in uw voordeel door te werken met geavanceerdere leads.

Uw klanten profiteren ook van test drive voordelen. Doordat ze uw product eerst kunnen uitproberen, vermindert u de frictie van het aankoopproces. Bovendien test drive vooraf ingericht, dat wil zeggen dat klanten het product niet hoeven te downloaden, in te stellen of te configureren.

## <a name="how-does-it-work"></a>Hoe werkt het?

Teststations zijn beheerde exemplaren die uw oplossing of toepassing op aanvraag starten voor klanten die deze aanvragen. Zodra een test drive is toegewezen, is deze beschikbaar voor gebruik door die klant voor een bepaalde periode. Nadat de periode is beëindigd, wordt deze verwijderd om ruimte te maken voor een andere klant.

Als uitgever beheert en configureert u de test drive in Partner Center. De technische configuratiegegevens zijn afhankelijk van het type aanbieding. Zie technische configuratie voor test drive [voor gedetailleerde richtlijnen.](./test-drive-technical-configuration.md)

Potentiële klanten ontdekken uw test drive als een CTA in uw aanbieding op [AppSource](https://appsource.microsoft.com/en-US/). Ze geven hun contactgegevens op en gaan akkoord met de voorwaarden en het privacybeleid van uw aanbieding en krijgen vervolgens toegang tot uw vooraf geconfigureerde omgeving om deze voor een vaste periode uit te proberen. Klanten krijgen een praktijkgestuurde proefversie van de belangrijkste functies en voordelen van uw product en u ontvangt een waardevolle lead.

## <a name="types-of-test-drives"></a>Typen teststations

Er zijn verschillende teststations beschikbaar op de commerciële marketplace voor bepaalde aanbiedingen, afhankelijk van het type product, het scenario en de marketplace die u gebruikt:

- Azure Resource Manager
    - Azure-toepassingen
    - SaaS
    - Virtual Machines
- Gehoste test drive
    - Dynamics 365 for Business Central (momenteel niet ondersteund)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Logische app (alleen in de ondersteuningsmodus)
- Power BI

Zie Technische test drive-configuratie voor meer informatie over het configureren van een van [deze teststations.](./test-drive-technical-configuration.md) 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Deze implementatiesjabloon bevat alle Azure-resources waar uw oplossing deel van uit maakt. Voor producten die in dit scenario passen, worden alleen Azure-resources gebruikt. De Azure Resource Manager test drive is beschikbaar voor deze aanbiedingstypen: 

- Azure-toepassingen
- SaaS
- Virtuele machines

>[!NOTE]
>Dit is de enige test drive voor aanbiedingen voor virtuele machines en Azure-toepassingen.

### <a name="hosted-test-drive-recommended"></a>Gehoste test drive (aanbevolen)

Een gehoste test drive verwijdert de complexiteit van de installatie door Microsoft de service te laten hosten en onderhouden die de test drive het inrichten en de inrichting van gebruikers defeit. Als u een aanbieding op uw Microsoft AppSource, bouwt u uw test drive verbinding te maken met een Dynamics AX/CRM-exemplaar. U kunt de volgende Typen AppSource-aanbiedingen gebruiken:

- Gebruik [Dynamics 365 for Customer Engagement](dynamics-365-customer-engage-offer-setup.md) en Power Apps voor een Customer Engagement-systeem, zoals verkoop, service, projectservice en veldservice.
- Gebruik [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) voor een resourceplanningssysteem van het bedrijf Finance and Operations, zoals financiën, bedrijfsvoering en productie, toeleveringsketen.

### <a name="logic-app-test-drive"></a>Logische app-test drive

Dit type test drive wordt niet gehost door Microsoft en maakt gebruik van Azure Resource Manager(ARM)-sjablonen voor dynamics AX/CRM-aanbiedingstypen. U moet de ARM-sjabloon uitvoeren om de vereiste resources in uw Azure-abonnement te maken. Logic App Test Drive is momenteel alleen beschikbaar in de ondersteuningsmodus en wordt niet aanbevolen door Microsoft. Zie Technische configuratie voor test drive voor meer informatie over het configureren van een Logic App Test [Drive.](./test-drive-technical-configuration.md)

### <a name="power-bi-test-drive"></a>Power BI test drive

Dit is gewoon een ingesloten koppeling naar een aangepast gemaakt dashboard. Elk product dat alleen een interactieve Power BI laat zien, moet dit type test drive.

## <a name="transforming-examples"></a>Voorbeelden transformeren

Het proces van het ombouwen van een architectuur van resources test drive kan een hele uitdaging zijn. Bekijk deze voorbeelden van hoe u huidige architecturen het beste kunt transformeren.

[Een websitesjabloon transformeren in een test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Een virtuele-machinesjabloon transformeren in een test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Een bestaande Resource Manager sjabloon transformeren naar een test drive](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Leads genereren op test drive

Een commerciële marketplace test drive is een geweldig hulpmiddel voor marketeers. We raden u aan deze op te nemen in uw go-to-market-inspanningen wanneer u start om meer leads voor uw bedrijf te genereren. Zie Leads van klanten van uw commerciële [marketplace-aanbieding voor gedetailleerde richtlijnen.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)

Als u een deal sluit met een test drive lead, moet u deze registreren bij [Microsoft Partner Sales Connect.](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect) Bovendien horen we graag dat uw klant wint wanneer een test drive een rol heeft gespeeld.

## <a name="other-resources"></a>Meer informatie

Aanvullende test drive resources:

- [Best practices voor Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Overzicht](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; zorg ervoor dat de pop-upblokkering is uitgeschakeld)

## <a name="next-step"></a>Volgende stap

- [Technische configuratie van test drive](test-drive-technical-configuration.md)