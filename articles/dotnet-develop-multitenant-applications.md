---
title: Webtoepassings patroon met meerdere tenants | Microsoft Docs
description: Zoek de architectuur overzichten en ontwerp patronen die beschrijven hoe u een webtoepassing met meerdere tenants in azure implementeert.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.custom: devx-track-dotnet
ms.openlocfilehash: d36a2804519e5728dd068cc6c06ad005244e8c95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95524093"
---
# <a name="multitenant-applications-in-azure"></a>Multitenant-toepassingen in Azure
Een multi tenant-toepassing is een gedeelde bron waarmee ' gebruikers in afzonderlijke tenants ' de toepassing kunnen bekijken alsof ze hun eigen. Een typisch voor beeld van een multi tenant-toepassing is een scenario waarin alle gebruikers van de toepassing van verschillende tenants de gebruikers ervaring kunnen aanpassen, maar anders dezelfde basis vereisten voor het bedrijf hebben. Voor beelden van grote multi tenant-toepassingen zijn Microsoft 365, Outlook.com en visualstudio.com.

Vanuit het oogpunt van een toepassings provider hebben de voor delen van multitenancy vooral betrekking op operationele en kosten efficiency. Eén versie van uw toepassing voldoet aan de behoeften van veel tenants/klanten, waardoor het mogelijk is om systeem beheer taken, zoals bewaking, prestaties afstemmen, software onderhoud en gegevens back-ups, toe te voegen.

Hieronder vindt u een lijst met de meest significante doelen en vereisten van het perspectief van een provider.

* **Inrichting**: u moet nieuwe tenants kunnen inrichten voor de toepassing.  Voor multi tenant-toepassingen met een groot aantal tenants is het doorgaans nodig dit proces te automatiseren door selfservice-inrichting in te scha kelen.
* **Onderhoud**: u moet de toepassing kunnen bijwerken en andere onderhouds taken uitvoeren terwijl er meerdere tenants worden gebruikt.
* **Bewaking**: u moet de toepassing te allen tijde kunnen bewaken om problemen te identificeren en ze op te lossen. Dit omvat het bewaken van de manier waarop elke Tenant de toepassing gebruikt.

Een goed geïmplementeerde multi tenant-toepassing biedt de volgende voor delen voor gebruikers.

* **Isolatie**: de activiteiten van afzonderlijke tenants hebben geen invloed op het gebruik van de toepassing door andere tenants. Tenants hebben geen toegang tot de gegevens van elkaar. Het wordt weer gegeven aan de Tenant alsof ze het exclusieve gebruik van de toepassing hebben.
* **Beschik baarheid**: individuele tenants willen dat de toepassing voortdurend beschikbaar is, mogelijk met garanties die zijn gedefinieerd in een sla. Daarnaast moeten de activiteiten van andere tenants geen invloed hebben op de beschik baarheid van de toepassing.
* **Schaal baarheid**: de toepassing wordt geschaald om te voldoen aan de vraag van afzonderlijke tenants. De aanwezigheid en acties van andere tenants mogen geen invloed hebben op de prestaties van de toepassing.
* **Kosten**: de kosten zijn lager dan het uitvoeren van een specifieke toepassing met één Tenant, omdat multitenancy het delen van resources mogelijk maakt.
* **Aanpassings mogelijkheden**. De mogelijkheid om de toepassing aan te passen voor een individuele Tenant op verschillende manieren, zoals het toevoegen of verwijderen van functies, het wijzigen van kleuren en logo's of zelfs het toevoegen van een eigen code of script.

Kortom, hoewel er veel overwegingen zijn die u moet uitvoeren om een uiterst schaal bare service te bieden, zijn er ook een aantal doel stellingen en vereisten voor veel multi tenant-toepassingen. Sommige zijn mogelijk niet relevant voor specifieke scenario's en het belang van individuele doel stellingen en vereisten verschillen in elk scenario. Als provider van de multi tenant-toepassing hebt u ook doel stellingen en vereisten, zoals, aan de doel stellingen van de Tenant en vereisten, winstgevendheid, facturering, meerdere service niveaus, inrichting, onderhoud en automatisering.

Zie [een toepassing met meerdere tenants in azure hosten][Hosting a Multi-Tenant Application on Azure]voor meer informatie over aanvullende overwegingen voor het ontwerpen van een multi tenant-toepassing. Zie voor informatie over algemene gegevensarchitectuurpatronen van multitenant software as a service (SaaS)-databasetoepassingen, [Ontwerppatronen voor multitenant SaaS-toepassingen met Azure SQL Database](./azure-sql/database/saas-tenancy-app-design-patterns.md). 

Azure biedt een groot aantal functies waarmee u de belangrijkste problemen kunt oplossen die zich voordoen bij het ontwerpen van een multi tenant systeem.

**Zonder**

* Website tenants segmenteren op basis van host headers met of zonder TLS-communicatie
* Website tenants op basis van query parameters
* Webservices in werk rollen
  * Werk rollen die doorgaans gegevens op de back-end van een toepassing verwerken.
  * Webrollen die normaal gesp roken fungeren als front-end voor toepassingen.

**Storage**

Gegevens beheer zoals Azure SQL Database of Azure Storage services zoals de Table service, die services biedt voor de opslag van grote hoeveel heden ongestructureerde gegevens en de Blob service, waarmee services grote hoeveel heden ongestructureerde tekst of binaire gegevens zoals video, audio en afbeeldingen kunnen opslaan.

* Multi tenant-gegevens beveiligen in SQL Database per Tenant SQL Server aanmeldingen.
* Azure-tabellen voor toepassings bronnen gebruiken door een toegangs beleid op container niveau op te geven, kunt u machtigingen aanpassen zonder dat u nieuwe URL'S hoeft uit te geven voor de resources die worden beveiligd met hand tekeningen voor gedeelde toegang.
* Azure-wacht rijen voor toepassings bronnen worden meestal gebruikt voor het verwerken van de verwerking namens tenants, maar kunnen ook worden gebruikt voor het distribueren van werk dat vereist is voor het inrichten of beheren.
* Service Bus wacht rijen voor toepassings bronnen die naar een gedeelde service worden gepusht, kunt u één wachtrij gebruiken waarbij elke Tenant afzender alleen machtigingen heeft (die zijn afgeleid van claims die zijn uitgegeven van ACS) om naar die wachtrij te pushen, terwijl alleen de ontvangers van de service toestemming hebben om de gegevens uit meerdere tenants te halen uit de wachtrij.

**Verbindings-en beveiligings Services**

* Azure Service Bus, een infra structuur voor berichten waarmee toepassingen berichten kunnen uitwisselen op een losjes gekoppelde manier voor betere schaal baarheid en meer flexibiliteit.

**Netwerkservices**

Azure biedt verschillende netwerk services die ondersteuning bieden voor verificatie en om de beheer baarheid van uw gehoste toepassingen te verbeteren. Deze services omvatten het volgende:

* Met Azure Virtual Network kunt u virtuele particuliere netwerken (Vpn's) inrichten en beheren in azure, en deze veilig koppelen aan een on-premises IT-infra structuur.
* Met Virtual Network Traffic Manager kunt u binnenkomend verkeer verdelen over meerdere gehoste Azure-Services, ongeacht of ze worden uitgevoerd in hetzelfde Data Center of in verschillende data centers over de hele wereld.
* Azure Active Directory (Azure AD) is een moderne, op REST gebaseerde service die mogelijkheden voor identiteits-en toegangs beheer biedt voor uw Cloud toepassingen. Het gebruik van Azure AD voor toepassings bronnen biedt een eenvoudige manier om gebruikers te verifiëren en te autoriseren om toegang te krijgen tot uw webtoepassingen en-services, terwijl de verificatie en autorisatie van uw code worden toegestaan.
* Azure Service Bus biedt een beveiligde bericht-en gegevens stroom mogelijkheid voor gedistribueerde en hybride toepassingen, zoals communicatie tussen door Azure gehoste toepassingen en on-premises toepassingen en services, zonder dat hiervoor complexe firewall-en beveiligings infrastructuren zijn vereist. Het gebruik van Service Bus Relay voor toepassings bronnen om toegang te krijgen tot de services die beschikbaar worden gemaakt als eind punten kunnen deel uitmaken van de Tenant (bijvoorbeeld een host buiten het systeem, zoals on-premises), of services die specifiek zijn ingericht voor de Tenant (omdat gevoelige, Tenant-specifieke gegevens over hen worden verplaatst).

**Resources inrichten**

Azure biedt een aantal manieren om nieuwe tenants in te richten voor de toepassing. Voor multi tenant-toepassingen met een groot aantal tenants is het doorgaans nodig dit proces te automatiseren door selfservice-inrichting in te scha kelen.

* Met werk rollen kunt u de inrichting per Tenant-resources inrichten en ongedaan maken (bijvoorbeeld wanneer een nieuwe Tenant zich aanmeldt of annuleert), metrische gegevens verzamelen voor het gebruik van metingen en de schaal beheren volgens een bepaald schema of als reactie op de overschrijding van de drempel waarden van Key Performance Indica tors. Deze zelfde rol kan ook worden gebruikt om updates en upgrades naar de oplossing te pushen.
* Azure-blobs kunnen worden gebruikt voor het inrichten van Compute-of vooraf geïnitialiseerde opslag resources voor nieuwe tenants, waarbij toegangs beleid op container niveau wordt geboden om de compute-service pakketten, VHD-installatie kopieën en andere bronnen te beveiligen.
* Opties voor het inrichten van SQL Database resources voor een Tenant zijn onder andere:
  
  * DDL in scripts of Inge sloten als resources binnen assembly's.
  * SQL Server 2008 R2 DAC-pakketten worden via een programma geïmplementeerd.
  * Kopiëren uit een hoofd referentie database.
  * Data bases importeren en exporteren gebruiken om nieuwe data bases in te richten vanuit een bestand.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: /previous-versions/msp-n-p/hh534480(v=pandp.10)
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716