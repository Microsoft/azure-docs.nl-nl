---
title: Fysieke beveiliging van Azure-data centers-Microsoft Azure | Microsoft Docs
description: In dit artikel wordt beschreven wat micro soft doet om de Azure-data centers te beveiligen, met inbegrip van fysieke infra structuur, beveiliging en naleving.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 3eac3c9abda74f0414fc97512f011d235d3991f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87076322"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure-faciliteiten,-locaties en fysieke beveiliging
In dit artikel wordt beschreven wat micro soft doet bij het beveiligen van de Azure-infra structuur.

## <a name="datacenter-infrastructure"></a>Data Center-infra structuur
Azure bestaat uit een [wereld wijd gedistribueerde Data Center-infra structuur](https://azure.microsoft.com/global-infrastructure/), die ondersteuning biedt voor duizenden onlineservices en meer dan 100 uiterst veilige faciliteiten over de hele wereld.

De infra structuur is ontworpen om toepassingen dichter bij gebruikers over de hele wereld te brengen, gegevens locatie te behouden en uitgebreide nalevings-en tolerantie opties voor klanten aan te bieden. Azure heeft 58 regio's over de hele wereld en is beschikbaar in 140 landen/regio's.

Een regio is een set data centers die zijn verbonden via een enorm en flexibel netwerk. Het netwerk omvat inhouds distributie, taak verdeling, redundantie en [gegevenskoppeling slaag versleuteling standaard](encryption-overview.md#encryption-of-data-in-transit) voor alle Azure-verkeer binnen een regio of tussen regio's. Met meer wereld wijde regio's dan elke andere Cloud provider biedt Azure u de flexibiliteit om toepassingen te implementeren waar u ze nodig hebt.

Azure-regio's zijn ingedeeld in geographs. Een Azure-geografie zorgt ervoor dat de vereisten voor gegevens locatie, soevereiniteit, naleving en tolerantie binnen geografische grenzen worden gerespecteerd.

Klanten met specifieke behoeften ten aanzien van gegevenslocatie en naleving, kunnen met geografische gebieden hun gegevens en toepassingen in de buurt houden. Geographs zijn fout tolerantie die van belang is voor de volledige regio fout, via de verbinding met de speciale netwerk infrastructuur met hoge capaciteit.

Beschikbaarheids zones zijn fysiek afzonderlijke locaties binnen een Azure-regio. Elke beschikbaarheidszone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Met beschikbaarheids zones kunt u essentiële toepassingen uitvoeren met hoge Beschik baarheid en replicatie met lage latentie.

In de volgende afbeelding ziet u hoe de Azure Global Infrastructure paren regio-en beschikbaarheids zones binnen dezelfde gegevens locatie grens voor hoge Beschik baarheid, herstel na nood gevallen en back-ups.

![Diagram van de grens van de gegevens locatie](./media/physical-security/data-residency-boundary.png)

Geografisch gedistribueerde data centers zorgen ervoor dat micro soft zich dicht bij klanten kan betrekken om de netwerk latentie te verminderen en een geografisch redundante back-up en failover mogelijk te maken.

## <a name="physical-security"></a>Fysieke beveiliging
Micro soft ontwerpt, bouwt en exploiteert data centers op een manier die de fysieke toegang tot de gebieden waar uw gegevens worden opgeslagen, strikt beheert. Micro soft begrijpt het belang van het beschermen van uw gegevens en is van belang om de data centers te beveiligen die uw gegevens bevatten. We hebben een volledige divisie van micro soft besteed aan het ontwerpen, bouwen en gebruiken van de fysieke faciliteiten die ondersteuning bieden voor Azure. Dit team is geïnvesteerd in het onderhouden van geavanceerde fysieke beveiliging.

Micro soft maakt gebruik van een gelaagde benadering van fysieke beveiliging, om het risico te verminderen dat niet-geautoriseerde gebruikers fysieke toegang krijgen tot gegevens en de bronnen van het Data Center. Data Centers die door micro soft worden beheerd, hebben uitgebreide beveiligings lagen: toegang goed keuren op het perimeter netwerk van de faciliteit, in het gebouw en op de vloer van het Data Center. Lagen van fysieke beveiliging zijn:

- **Toegangs aanvraag en goed keuring.** U moet toegang aanvragen voordat u het Data Center aankomt. U moet een geldige zakelijke reden opgeven voor uw bezoek, zoals naleving of controle doeleinden. Alle aanvragen worden goedgekeurd door micro soft-mede werkers. Een nood zaak om toegang te krijgen, houdt in dat het aantal personen dat nodig is voor het volt ooien van een taak in de data centers tot de Maxi maal bewaarde. Nadat micro soft toestemming verleent, heeft een individu alleen toegang tot het discrete gedeelte van het Data Center dat is vereist op basis van de goedgekeurde zakelijke reden. De machtigingen zijn beperkt tot een bepaalde periode en verlopen.

- **Het perimeter netwerk van de faciliteit.** Wanneer u op een Data Center arriveert, moet u een goed gedefinieerd toegangs punt door lopen. Normaal gesp roken worden in hoge omheiningen van staal en beton elke inch van de omtrek toegepast. Er zijn camera's rond de data centers, waarbij een beveiligings team te allen tijde hun Video's bewaken.

- **Start opbouwen.** De start van het Data Center is gewerkt met professionele veiligheids managers die strenge opleidings-en achtergrond controles hebben ondergaan. Deze beveiligings ambtenaren worden ook regel matig het Data Center patrouillet en de Video's van camera's in het Data Center te allen tijde bewaken.

- **Binnen het gebouw.** Nadat u het gebouw hebt ingevoerd, moet u twee ledige verificatie met biometrie door lopen om door te gaan met het Data Center. Als uw identiteit is gevalideerd, kunt u alleen het gedeelte van het Data Center invoeren waaraan u toegang hebt verleend. U kunt er alleen voor de duur van de goedgekeurde tijd blijven.

- **Data Center Floor.** U hebt alleen toegang tot de vloer die u hebt goedgekeurd om in te voeren. U moet een volledige metalen detectie controle door geven. Om het risico te verminderen dat niet-geautoriseerde gegevens het Data Center zonder kennis binnenkomen of verlaten, kunnen alleen goedgekeurde apparaten hun eigen manier doen in de Data Center Floor. Daarnaast kunnen video camera's de voor-en achterkant van elk server rack controleren. Wanneer u de Data Center-verdieping verlaat, moet u het volledige metaal detectie scherm van de hoofd tekst door lopen. Als u het Data Center wilt verlaten, moet u een extra beveiligings scan door lopen.

Micro soft vereist dat de bezoekers badges kunnen overplaatsen bij vertrek van elke micro soft-faciliteit.

## <a name="physical-security-reviews"></a>Fysieke beveiligings beoordelingen
Periodiek voeren we fysieke beveiligings beoordelingen van de faciliteiten uit om ervoor te zorgen dat de data centers op de juiste wijze voldoen aan de vereisten van Azure-beveiliging. Het personeel van de Data Center-hosting provider biedt geen Azure-Service beheer. Mede werkers kunnen zich niet aanmelden bij Azure-systemen en hebben geen fysieke toegang tot de Azure plaatsing room en kooien.

## <a name="data-bearing-devices"></a>Apparaten voor gegevens opslag
Micro soft maakt gebruik van best practice procedures en een gewiste oplossing die compatibel is met [NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Voor harde schijven die niet kunnen worden gewist, gebruiken we een vernietigings proces waardoor het wordt vernietigd en het herstel van de gegevens onmogelijk wordt weer gegeven. Dit vernietigings proces kan worden geintegreerd, shred, pulverize of verbranding. We bepalen de wijze van verwijdering volgens het activa type. De records van de vernietiging worden bewaard.  

## <a name="equipment-disposal"></a>Verwijdering van apparatuur
Bij het einde van de levens duur van een systeem volgen micro soft-werk nemers de rigoureuze procedures voor gegevens verwerking en de verwijdering van hardware om ervoor te zorgen dat de hardware die uw gegevens bevat, niet beschikbaar wordt gesteld aan niet-vertrouwde partijen. We gebruiken een veilige methode voor het wissen van harde schijven die dit ondersteunen. Voor harde schijven die niet kunnen worden gewist, gebruiken we een vernietigings proces dat het station vernietigt en het herstel van de informatie onmogelijk maakt. Dit vernietigings proces kan worden geintegreerd, shred, pulverize of verbranding. We bepalen de wijze van verwijdering volgens het activa type. De records van de vernietiging worden bewaard. Alle Azure-Services gebruiken goedgekeurde beheer Services voor opslag en verwijdering van media.

## <a name="compliance"></a>Naleving
We ontwerpen en beheren de Azure-infra structuur om te voldoen aan een breed scala aan internationale en bedrijfsspecifieke nalevings standaarden, zoals ISO 27001, HIPAA, FedRAMP, SOC 1 en SOC 2. Daarnaast voldoen we aan land-of regio-specifieke standaarden, waaronder Australië IRAP, UK G-Cloud en Singapore MTCS. Strenge controles van derden, zoals die welke door het Britse normen Institute worden uitgevoerd, worden gecontroleerd op de strikte beveiliging van deze standaarden.

Voor een volledige lijst met nalevings standaarden die Azure volgt, raadpleegt u de [nalevings aanbiedingen](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat micro soft doet bij het beveiligen van de Azure-infra structuur:

- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)


