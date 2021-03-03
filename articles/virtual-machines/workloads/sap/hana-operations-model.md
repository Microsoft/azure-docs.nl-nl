---
title: Operations-model van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Operations-model van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9660540c60c6b3a734dc507a6056af9f1f8d6dc9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675552"
---
# <a name="operations-model-and-responsibilities"></a>Bewerkingsmodel en verantwoordelijkheden

De service die wordt meegeleverd met SAP HANA op Azure (grote exemplaren) is afgestemd op Azure IaaS Services. U krijgt een exemplaar van een HANA grote instantie met een geïnstalleerd besturings systeem dat is geoptimaliseerd voor SAP HANA. Net als bij Azure IaaS Vm's, de meeste taken van het beveiligen van het besturings systeem, het installeren van extra software, het installeren van HANA, het besturings systeem en HANA, en het bijwerken van het besturings systeem en HANA is uw verantwoordelijkheid. Micro soft dwingt updates van het besturings systeem of HANA-updates niet voor u af.

![Verantwoordelijkheden van SAP HANA op Azure (grote exemplaren)](./media/hana-overview-architecture/image2-responsibilities.png)

Zoals in het diagram wordt weer gegeven, is SAP HANA op Azure (grote exemplaren) een IaaS-aanbieding met meerdere tenants. De verantwoordelijkheid voor het grootste deel is de grens van het besturings systeem en de infra structuur. Micro soft is verantwoordelijk voor alle aspecten van de service onder de regel van het besturings systeem. U bent zelf verantwoordelijk voor alle aspecten van de service boven de regel. Het besturings systeem is uw verantwoordelijkheid. U kunt de meest actuele on-premises methoden blijven gebruiken voor naleving, beveiliging, toepassings beheer, basis en beheer van besturings systemen. De systemen worden weer gegeven alsof ze zich in het netwerk bevinden.

Deze service is geoptimaliseerd voor SAP HANA, dus zijn er gebieden waar u moet samen werken met micro soft om de onderliggende infrastructuur mogelijkheden te gebruiken voor de beste resultaten.

De volgende lijst bevat meer details over elk van de lagen en uw verantwoordelijkheden:

**Netwerken**: alle interne netwerken voor de grote instantie stempel met SAP Hana. Uw verantwoordelijkheid omvat toegang tot opslag, connectiviteit tussen de instanties (voor uitschalen en andere functies), connectiviteit naar het landschap en connectiviteit met Azure waarbij de SAP-toepassingslaag wordt gehost op Vm's. Het bevat ook een WAN-verbinding tussen Azure-data centers voor het repliceren van nood herstel doeleinden. Alle netwerken worden gepartitioneerd door de Tenant en hebben de kwaliteit van de service toegepast.

**Opslag**: de gevirtualiseerde gepartitioneerde opslag voor alle volumes die nodig zijn voor de SAP Hana servers, en voor moment opnamen. 

**Servers**: de toegewezen fysieke servers voor het uitvoeren van de SAP Hana db's die aan tenants zijn toegewezen. De servers van de klasse type I van de Sku's zijn door hardware abstracted. Met deze typen servers wordt de server configuratie verzameld en onderhouden in profielen, die kunnen worden verplaatst van één fysieke hardware naar een andere fysieke hardware. Een dergelijk (hand matig) verplaatsen van een profiel door bewerkingen kan worden vergeleken met een Azure-service voor herstel. De servers van de klasse-Sku's van type II bieden geen enkele mogelijkheid.

**SDDC**: de beheer software die wordt gebruikt voor het beheren van Data Centers als door software gedefinieerde entiteiten. Hiermee kan micro soft resources groeperen op schaal, Beschik baarheid en prestatie redenen.

**O/S**: het besturings systeem dat u kiest (SuSE Linux of Red Hat Linux) dat wordt uitgevoerd op de-servers. De installatie kopieën van het besturings systeem die u opgeeft, zijn door de afzonderlijke Linux-leverancier naar micro soft verzonden voor het uitvoeren van SAP HANA. U moet een abonnement met de Linux-leverancier hebben voor de specifieke voor SAP HANA geoptimaliseerde installatie kopie. U bent zelf verantwoordelijk voor het registreren van de installatie kopieën bij de leverancier van het besturings systeem. 

Vanaf het punt van overdracht door micro soft bent u verantwoordelijk voor eventuele verdere patches van het Linux-besturings systeem. Deze patches omvatten extra pakketten die nodig kunnen zijn voor een geslaagde SAP HANA installatie en die niet zijn opgenomen door de specifieke Linux-leverancier in hun SAP HANA geoptimaliseerde besturingssysteem installatie kopieën. (Zie de documentatie van de HANA-installatie van SAP en SAP Notes (Engelstalig) voor meer informatie.) 

U bent verantwoordelijk voor patches voor het besturings systeem vanwege storingen of optimalisatie van het besturings systeem en de bijbehorende Stuur Programma's ten opzichte van de specifieke serverhardware. U bent ook verantwoordelijk voor beveiliging of functionele patches van het besturings systeem. 

Uw verantwoordelijkheid omvat ook bewaking en capaciteits planning van:

- CPU-resource verbruik.
- Geheugen gebruik.
- Schijf volumes gerelateerd aan beschik bare ruimte, IOPS en latentie.
- Netwerk volume verkeer tussen HANA grote instanties en de SAP-toepassingslaag.

De onderliggende infra structuur van HANA grote instanties biedt functionaliteit voor het maken van back-ups en het herstellen van het besturingssysteem volume. Het gebruik van deze functionaliteit is ook uw verantwoordelijkheid.

**Middleware**: de SAP Hana-instantie, voornamelijk. Beheer, bewerkingen en controle zijn uw verantwoordelijkheid. U kunt de meegeleverde functionaliteit gebruiken om opslag momentopnamen te gebruiken voor back-up-en herstel bewerkingen en herstel na een nood geval. Deze mogelijkheden worden geboden door de-infra structuur. Uw verantwoordelijkheden omvatten ook het ontwerpen van hoge Beschik baarheid of herstel na nood gevallen met deze mogelijkheden, het gebruik ervan en de bewaking om te bepalen of opslag momentopnamen zijn uitgevoerd.

**Gegevens**: uw gegevens die worden beheerd door SAP Hana, en andere gegevens zoals back-upbestanden die zich op volumes of bestands shares bevinden. Uw verantwoordelijkheden omvatten beschik bare schijf ruimte voor bewaking en het beheren van de inhoud op de volumes. U bent ook verantwoordelijk voor het bewaken van de geslaagde uitvoering van back-ups van schijf volumes en opslag momentopnamen. Het uitvoeren van gegevens replicatie naar nood herstel sites is de verantwoordelijkheid van micro soft.

**Toepassingen:** De SAP-toepassings exemplaren of, in het geval van niet-SAP-toepassingen, de toepassingslaag van die toepassingen. Uw verantwoordelijkheden omvatten implementatie, beheer, bewerkingen en controle van deze toepassingen. U bent verantwoordelijk voor de capaciteits planning van het CPU-resource verbruik, het geheugen verbruik, het Azure Storage verbruik en het gebruik van netwerk bandbreedte in virtuele netwerken. U bent ook verantwoordelijk voor het plannen van de capaciteit van het Resource verbruik van virtuele netwerken naar SAP HANA op Azure (grote exemplaren).

**Wan's**: de verbindingen die u tot stand brengt van on-premises naar Azure-implementaties voor werk belastingen. Alle klanten met HANA grote instanties gebruiken Azure ExpressRoute voor connectiviteit. Deze verbinding maakt geen deel uit van de SAP HANA van de Azure-oplossing (grote exemplaren). U bent zelf verantwoordelijk voor het instellen van deze verbinding.

**Archief**: u kunt liever kopieën van gegevens archiveren met uw eigen methoden in opslag accounts. Voor het archiveren moeten beheer, naleving, kosten en bewerkingen worden uitgevoerd. U bent verantwoordelijk voor het genereren van archief kopieën en back-ups op Azure en het op een compatibele manier op te slaan.

Zie de [Sla voor SAP Hana op Azure (grote exemplaren)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Volgende stappen**
- Raadpleeg de [architectuur van SAP Hana (grote instanties) op Azure](hana-architecture.md)
