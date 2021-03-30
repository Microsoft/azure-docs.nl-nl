---
title: Azure Information System-onderdelen en-grenzen
description: Dit artikel bevat een algemene beschrijving van de architectuur en het beheer van Microsoft Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727203"
---
# <a name="azure-information-system-components-and-boundaries"></a>Azure Information System-onderdelen en-grenzen
Dit artikel bevat een algemene beschrijving van de Azure-architectuur en-beheer. De Azure-systeem omgeving bestaat uit de volgende netwerken:

- Microsoft Azure productie netwerk (Azure-netwerk)
- Micro soft-bedrijfs netwerk (Corpnet)

Afzonderlijke IT-teams zijn verantwoordelijk voor bewerkingen en onderhoud van deze netwerken.

## <a name="azure-architecture"></a>Azure-architectuur
Azure is een platform en infra structuur voor cloud computing voor het bouwen, implementeren en beheren van toepassingen en services via een netwerk van data centers. Micro soft beheert deze data centers. Op basis van het aantal resources dat u opgeeft, maakt Azure virtuele machines (Vm's) op basis van de benodigde resource behoeften. Deze Vm's worden uitgevoerd op een Azure-Hyper Visor, die is ontworpen voor gebruik in de Cloud en is niet toegankelijk voor het publiek.

Op elk knoop punt van de fysieke Azure-server bevindt zich een Hyper Visor die rechtstreeks via de hardware wordt uitgevoerd. De Hyper Visor deelt een knoop punt op in een variabele aantal gast-Vm's. Elk knoop punt heeft ook één hoofd-VM die het hostbesturingssysteem uitvoert. Windows Firewall is ingeschakeld op elke virtuele machine. U definieert welke poorten adresseerbaar zijn door het service definitie bestand te configureren. Deze poorten zijn de enige open en adresseer bare, intern of extern. Al het verkeer en de toegang tot de schijf en het netwerk worden hersteld door de Hyper Visor en het hoofd besturingssysteem.

Op de host-laag voert Azure-Vm's een aangepaste en harde versie van de meest recente Windows-Server uit. Azure gebruikt een versie van Windows Server die alleen de onderdelen bevat die nodig zijn voor het hosten van Vm's. Dit verbetert de prestaties en vermindert de kwets baarheid voor aanvallen. De computer grenzen worden afgedwongen door de Hyper Visor, wat niet afhankelijk is van de beveiliging van het besturings systeem.

### <a name="azure-management-by-fabric-controllers"></a>Azure-beheer door infrastructuur controllers

In Azure worden Vm's die worden uitgevoerd op fysieke servers (Blades/knoop punten) gegroepeerd in clusters van ongeveer 1000. De virtuele machines worden onafhankelijk beheerd door een uitschaalbaar en redundant platform software onderdeel, de zogenaamde infrastructuur controller (FC).

Elke FC beheert de levens cyclus van toepassingen die worden uitgevoerd in het cluster en houdt de status van de hardware onder het beheer van de apparaten bij. Er worden Autonomic-bewerkingen uitgevoerd, zoals reincarnating-VM-instanties op gezonde servers wanneer wordt vastgesteld dat een server is mislukt. Het FC voert ook bewerkingen voor toepassings beheer uit, zoals het implementeren, bijwerken en schalen van toepassingen.

Het Data Center is onderverdeeld in clusters. Clusters isoleren fouten op het niveau FC en voor komen dat bepaalde klassen fouten van invloed zijn op servers buiten het cluster waarin ze optreden. FCs die een bepaald Azure-cluster bedient, worden gegroepeerd in een FC-cluster.

### <a name="hardware-inventory"></a>Hardware-inventaris

De FC bereidt een inventaris van de hardware-en netwerk apparaten van Azure voor tijdens het Boots trap configuratie proces. Nieuwe hardware en netwerk onderdelen die de Azure-productie omgeving invoeren, moeten het Boots trap configuratie proces volgen. De FC is verantwoordelijk voor het beheren van de volledige inventaris die wordt vermeld in het datacenter.xml-configuratie bestand.

### <a name="fc-managed-operating-system-images"></a>FC-beheerde installatie kopieën van besturings systemen

Het team van het besturings systeem bevat installatie kopieën, in de vorm van virtuele harde schijven, geïmplementeerd op alle host-en gast-Vm's in de Azure-productie omgeving. Het team maakt deze basis installatie kopieën door middel van een geautomatiseerd offline bouw proces. De basis installatie kopie is een versie van het besturings systeem waarin de kernel en andere kern onderdelen zijn gewijzigd en geoptimaliseerd ter ondersteuning van de Azure-omgeving.

Er zijn drie typen installatie kopieën van het besturings systeem die door een infra structuur worden beheerd:

- Host: een aangepast besturings systeem dat wordt uitgevoerd op virtuele machines op de host.
- Systeem eigen: een systeem eigen besturings systeem dat wordt uitgevoerd op tenants (bijvoorbeeld Azure Storage). Dit besturings systeem heeft geen Hyper Visor.
- Gast: een gast besturingssysteem dat wordt uitgevoerd op gast-Vm's.

De host en de systeem eigen FC-beheerde besturings systemen zijn ontworpen voor gebruik in de Cloud en zijn niet openbaar toegankelijk.

#### <a name="host-and-native-operating-systems"></a>Host en systeem eigen besturings systemen

Host en systeem eigen zijn beveiligde installatie kopieën van besturings systemen die als host fungeren voor de Fabric-agents en worden uitgevoerd op een reken knooppunt (wordt uitgevoerd als eerste VM op het knoop punt) en opslag knooppunten. Het voor deel van het gebruik van geoptimaliseerde basis installatie kopieën van de host en de systeem eigen, is dat de surface area die door Api's worden weer gegeven of ongebruikte onderdelen vermindert. Deze kunnen hoge beveiligings Risico's opleveren en de footprint van het besturings systeem verg Roten. Besturings systemen met een verminderde footprint bevatten alleen de onderdelen die nodig zijn voor Azure.

#### <a name="guest-operating-system"></a>Gastbesturingssysteem

Interne Azure-onderdelen die worden uitgevoerd op Vm's van gast besturingssystemen, hebben geen mogelijkheid om Remote Desktop Protocol uit te voeren. Eventuele wijzigingen in de basislijn configuratie-instellingen moeten worden doorgevoerd in het proces voor wijzigings-en release beheer.

## <a name="azure-datacenters"></a>Azure-datacentrums
Het Microsoft Cloud Infrastructure and Operations (MCIO)-team beheert de fysieke infra structuur en Datacenter faciliteiten voor alle micro soft-onlineservices. MCIO is hoofd zakelijk verantwoordelijk voor het beheer van de fysieke en omgevings controles binnen de data centers en het beheren en ondersteunen van buiten-perimeter netwerk apparaten (zoals Edge-routers en Datacenter routers). MCIO is ook verantwoordelijk voor het instellen van de bare minimale serverhardware op racks in het Data Center. Klanten hebben geen directe interactie met Azure.

## <a name="service-management-and-service-teams"></a>Service beheer en service teams
Verschillende technische groepen, ook wel service teams genoemd, beheren de ondersteuning van de Azure-service. Elk service team is verantwoordelijk voor een gebied van ondersteuning voor Azure. Elk service team moet een technicus 24x7 beschikbaar maken om fouten in de service te onderzoeken en op te lossen. Service teams hebben standaard geen fysieke toegang tot de hardware die in azure wordt uitgevoerd.

De service teams zijn:

- Toepassings platform
- Azure Active Directory
- Azure Compute
- Azure net
- Cloud Engineering Services
- ISSD: beveiliging
- Multi-factor Authentication
- SQL Database
- Storage

## <a name="types-of-users"></a>Typen gebruikers
Werk nemers (of aannemers) van micro soft worden beschouwd als interne gebruikers. Alle andere gebruikers worden als externe gebruikers beschouwd. Alle interne gebruikers van Azure hebben hun werknemers status gecategoriseerd met een gevoeligheids niveau waarmee hun toegang tot klant gegevens (toegang of geen toegang) wordt gedefinieerd. Gebruikers rechten voor Azure (machtiging voor autorisatie nadat verificatie plaatsvindt) worden beschreven in de volgende tabel:

| Rol | Intern of extern | Gevoeligheids niveau | Geautoriseerde bevoegdheden en functies | Toegangs type
| --- | --- | --- | --- | --- |
| Azure Data Center-Engineer | Intern | Geen toegang tot klant gegevens | De fysieke beveiliging van de lokalen beheren. U voert patrouilles in en uit op het Data Center en bewaakt alle toegangs punten. Escort in en uit het Data Center bepaalde niet-gewiste personeel die algemene services leveren (zoals restauratie of reiniging) of het werk in het Data Center. Routine bewaking en onderhoud van netwerkhardware uitvoeren. Voer incident beheer en verwerkings herstel uit met behulp van verschillende hulpprogram ma's. Routine bewaking en onderhoud van de fysieke hardware in de data centers uitvoeren. Toegang tot de omgeving op aanvraag vanuit eigen aren van eigenschappen. U kunt Forensische-onderzoeken uitvoeren, incident rapporten vastleggen en verplichte beveiligings training en beleids vereisten vereisen. Operationele eigendom en onderhoud van essentiële beveiligings Programma's, zoals scanners en logboek verzameling. | Permanente toegang tot de omgeving. |
| Azure incident sorteren (Rapid Response Engineers) | Intern | Toegang tot klant gegevens | Beheer de communicatie tussen MCIO-, ondersteunings-en engineering teams. Sorteren platform incidenten, implementatie problemen en service aanvragen. | Just-in-time-toegang tot de omgeving, met beperkte permanente toegang tot niet-klant systemen. |
| Implementatie-engineers van Azure | Intern | Toegang tot klant gegevens | Implementeer en upgrade platform onderdelen, software en geplande configuratie wijzigingen in de ondersteuning van Azure. | Just-in-time-toegang tot de omgeving, met beperkte permanente toegang tot niet-klant systemen. |
| Ondersteuning voor Azure-klant uitval (Tenant) | Intern | Toegang tot klant gegevens | Fout opsporing en diagnose van platform storingen en-fouten voor afzonderlijke reken-tenants en Azure-accounts. Fouten analyseren. Kritiek oplossingen voor het platform of de klant en technische verbeteringen voor de ondersteuning van het apparaat. | Just-in-time-toegang tot de omgeving, met beperkte permanente toegang tot niet-klant systemen. |
| Azure live site-technici (bewakings technici) en incident | Intern | Toegang tot klant gegevens | De platform status diagnosticeren en beperken met diagnostische hulpprogram ma's. Oplossingen voor volume Stuur Programma's, reparatie van items die het gevolg zijn van storingen en helpen bij het herstellen van bewerkingen voor onderbrekingen. | Just-in-time-toegang tot de omgeving, met beperkte permanente toegang tot niet-klant systemen. |
|Azure-klanten | Extern | N.v.t. | N.v.t. | N.v.t. |

Azure gebruikt unieke id's voor het verifiëren van organisatorische gebruikers en klanten (of processen die optreden namens organisatie gebruikers). Dit geldt voor alle assets en apparaten die deel uitmaken van de Azure-omgeving.

### <a name="azure-internal-authentication"></a>Interne Azure-verificatie

De communicatie tussen de interne onderdelen van Azure wordt beveiligd met TLS-versleuteling. In de meeste gevallen zijn de X. 509-certificaten zelf ondertekend. Certificaten met verbindingen die buiten het Azure-netwerk toegankelijk kunnen zijn, zijn een uitzonde ring, evenals certificaten voor de FCs. FCs heeft certificaten uitgegeven door een micro soft-certificerings instantie (CA) die wordt ondersteund door een vertrouwde basis certificerings instantie. Hierdoor kunnen open bare FC-sleutels eenvoudig worden doorgevoerd. Daarnaast gebruiken micro soft-ontwikkel hulpprogramma's open bare FC-sleutels. Wanneer ontwikkel aars nieuwe toepassings installatie kopieën indienen, worden de installatie kopieën versleuteld met een open bare FC-sleutel om Inge sloten geheimen te beveiligen.

### <a name="azure-hardware-device-authentication"></a>Verificatie van Azure-hardware

De FC onderhoudt een set referenties (sleutels en/of wacht woorden) die worden gebruikt om zichzelf te verifiëren op diverse hardwareapparaten onder het beheer ervan. Micro soft gebruikt een systeem om toegang tot deze referenties te voor komen. Met name de Trans Port, persistentie en het gebruik van deze referenties is ontworpen om te voor komen dat Azure-ontwikkel aars, beheerders en back-upservices en mede werkers toegang hebben tot gevoelige, vertrouwelijke of persoonlijke informatie.

Micro soft maakt gebruik van versleuteling op basis van de open bare sleutel van de Master identiteit van FC. Dit gebeurt tijdens de installatie van FC en FC herconfiguratie tijden, om de referenties over te dragen die worden gebruikt voor toegang tot apparaten in het netwerk. Wanneer de-FC de referenties nodig heeft, worden deze door de FC opgehaald en ontsleuteld.

### <a name="network-devices"></a>Netwerk apparaten

Het Azure-netwerk team configureert netwerk service accounts zodat een Azure-client kan worden geverifieerd bij netwerk apparaten (routers, switches en load balancers).

## <a name="secure-service-administration"></a>Beveiligd Service beheer
Azure Operations-personeel is verplicht om beveiligde beheer werkstations (zagen) te gebruiken. Klanten kunnen vergelijk bare besturings elementen implementeren met behulp van privileged Access workstations. Met zagen gebruikt administratief personeel een afzonderlijk toegewezen beheerders account dat gescheiden is van het standaard gebruikers account van de gebruiker. De zaag bouwt voort op die account scheidings praktijk door een betrouwbaar werk station te bieden voor die gevoelige accounts.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat micro soft doet bij het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)
