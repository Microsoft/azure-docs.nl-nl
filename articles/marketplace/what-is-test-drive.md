---
title: Wat is een test drive? Micro soft Commercial Marketplace
description: Uitleg van Marketplace test drive-functie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490037"
---
# <a name="what-is-a-test-drive"></a>Wat is een test drive?

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te geven om te proberen voordat u koopt, het genereren van uiterst gekwalificeerde leads en het resultaat van een verhoogde conversie. Een test drive uw product tot leven brengt in een praktijk scenario voor de implementatie. Klanten die uw product uitproberen, zien een duidelijke intentie om een soort gelijke oplossing te kopen. Gebruik dit voor uw voor deel door meer geavanceerde leads te volgen.

Uw klanten profiteren ook van een test drive. Door hen in staat te stellen uw product eerst te proberen, vermindert u de wrijving van het aankoop proces. Daarnaast is test drive vooraf ingericht, d.w.z. klanten hoeven het product niet te downloaden, in te stellen of te configureren.

## <a name="how-does-it-work"></a>Hoe werkt het?

Test stations zijn beheerde instanties die uw oplossing of toepassing op aanvraag starten voor klanten die het verzoek indienen. Zodra een test drive-exemplaar is toegewezen, is het beschikbaar voor gebruik door die klant voor een bepaalde periode. Nadat de periode is beëindigd, wordt deze verwijderd om ruimte te maken voor een andere klant.

Als uitgever kunt u de test drive-instellingen beheren en configureren in partner centrum. De technische configuratie gegevens variëren afhankelijk van het type aanbieding. Zie de technische configuratie van het [test station](./test-drive-technical-configuration.md)voor gedetailleerde richt lijnen.

Potentiële klanten ontdekken uw test drive als een CTA in uw aanbieding op [AppSource](https://appsource.microsoft.com/en-US/). Ze bieden hun contact gegevens en gaan akkoord met de voor waarden en het privacybeleid van uw aanbieding. vervolgens krijgt u toegang tot uw vooraf geconfigureerde omgeving om deze te proberen voor een bepaalde periode. Klanten ontvangen een praktijk gerichte, zelf-begeleide proef versie van de belangrijkste functies en voor delen van uw product en u ontvangt een waardevolle lead.

## <a name="types-of-test-drives"></a>Typen test stations

Er zijn verschillende test stations beschikbaar op de commerciële Marketplace voor Select-aanbiedingen, afhankelijk van het type product, scenario en Marketplace dat u hebt:

- Azure Resource Manager
    - Azure-toepassingen
    - SaaS
    - Virtual Machines
- Gehoste test drive
    - Dynamics 365 voor bedrijven Central (momenteel niet ondersteund)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Logische app (alleen in ondersteunings modus)
- Power BI

Zie [technische configuratie van test station](./test-drive-technical-configuration.md)voor meer informatie over het configureren van een van deze test stations. 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Deze implementatie sjabloon bevat alle Azure-resources waaruit uw oplossing bestaat. Voor producten die in dit scenario passen, worden alleen Azure-resources gebruikt. De Azure Resource Manager test drive is beschikbaar voor de volgende aanbiedings typen: 

- Azure-toepassingen
- SaaS
- Virtuele machines

>[!NOTE]
>Dit is de enige test drive optie voor aanbiedingen van virtuele machines en Azure-toepassingen.

### <a name="hosted-test-drive-recommended"></a>Gehoste test drive (aanbevolen)

Een gehoste test drive verwijdert de complexiteit van Setup door micro soft host te laten en de service te onderhouden waarmee het inrichten van de test drive gebruiker wordt uitgevoerd en de inrichting ongedaan wordt gemaakt. Als u een aanbieding op Microsoft AppSource hebt, bouwt u uw test drive om verbinding te maken met een Dynamics AX/CRM-exemplaar. U kunt de volgende typen AppSource-aanbiedingen gebruiken:

- Gebruik [Dynamics 365 voor klant betrokkenheid](partner-center-portal/create-new-customer-engagement-offer.md) voor een klant engagementsysteem, zoals verkoop, service, project service en veld service.
- Gebruik [Dynamics 365 voor bewerkingen](partner-center-portal/create-new-operations-offer.md) voor een bedrijf voor de planning van Financiën en bedrijfs middelen zoals financiën, bewerkingen en productie, toeleverings keten.

### <a name="logic-app-test-drive"></a>test drive van logische app

Dit type test drive wordt niet gehost door micro soft en gebruikt ARM-sjablonen (Azure Resource Manager) voor Dynamics AX/CRM-aanbiedings typen. U moet de ARM-sjabloon uitvoeren om de vereiste resources in uw Azure-abonnement te maken. Het test station van de logische app bevindt zich momenteel alleen in de ondersteunings modus en wordt niet aanbevolen door micro soft voor meer informatie over het configureren van een test schijf voor een logische app, Zie [technische configuratie testen](./test-drive-technical-configuration.md).

### <a name="power-bi-test-drive"></a>Power BI test drive

Dit is gewoon een Inge sloten koppeling naar een aangepast dash board. Een product dat alleen een interactieve Power BI illustreert, moet gebruikmaken van dit type test drive.

## <a name="transforming-examples"></a>Voor beelden transformeren

Het proces waarbij een architectuur van bronnen in een test drive wordt omgezet, kan onopvallend zijn. Bekijk deze voor beelden van hoe u de beste huidige architecturen kunt transformeren.

[Een website sjabloon transformeren naar een test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Een virtuele-machine sjabloon transformeren naar een test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Een bestaande resource manager-sjabloon omzetten in een test drive](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Leads van uw test drive genereren

Een commerciële Marketplace test drive is een uitstekend hulp programma voor markt verhandelers. We raden u aan om deze op te nemen in uw Go-to-Market-inspanningen wanneer u start om meer leads voor uw bedrijf te genereren. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

Als u een oplossing met een test drive-lead sluit, dient u deze te registreren bij [micro soft partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Het is ook belang rijk om te horen over uw klant wint waar een test drive een rol heeft gespeeld.

## <a name="other-resources"></a>Meer informatie

Aanvullende test drive resources:

- [Aanbevolen procedures testen](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Overzicht](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Controleer of de pop-upblokkering is uitgeschakeld)

## <a name="next-step"></a>Volgende stap

- [Technische configuratie van test drive](test-drive-technical-configuration.md)