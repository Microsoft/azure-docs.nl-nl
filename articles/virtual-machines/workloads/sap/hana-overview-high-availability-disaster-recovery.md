---
title: Hoge Beschik baarheid en herstel na nood gevallen van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Hoge Beschik baarheid tot stand brengen en nood herstel plannen voor SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e1fd4d53a642052c55bbe5d31bf80133ae89d35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675460"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA Large Instances hoge Beschik baarheid en herstel na nood geval op Azure 

>[!IMPORTANT]
>Deze documentatie is geen vervanging van de documentatie voor SAP HANA beheer of SAP-opmerkingen. Het is van groot belang dat de lezer een solide kennis heeft van en ervaring heeft met SAP HANA beheer en-bewerkingen, met name met de onderwerpen over back-ups maken, herstellen, hoge Beschik baarheid en herstel na nood gevallen.

Het is belang rijk dat u de stappen en processen die in uw omgeving worden uitgevoerd en met uw HANA-versies en-releases uitoefent. Sommige processen die in deze documentatie worden beschreven, zijn vereenvoudigd voor een beter inzicht en zijn niet bedoeld om te worden gebruikt als gedetailleerde stappen voor de uiteindelijke bewerkings handleidingen. Als u bewerkings handleidingen wilt maken voor uw configuraties, moet u uw processen testen en uitoefenen en de processen vastleggen die betrekking hebben op uw specifieke configuraties. 


Hoge Beschik baarheid en herstel na nood gevallen (DR) zijn belang rijke aspecten van het uitvoeren van uw essentiële SAP HANA op de Azure-server (grote exemplaren). Het is belang rijk om te werken met SAP, uw systeem integrator of micro soft om de juiste strategieën voor hoge Beschik baarheid en herstel na nood gevallen te ontwikkelen en implementeren. Het is ook belang rijk om rekening te houden met de Recovery Point Objective (RPO) en de beoogde herstel tijd, die specifiek zijn voor uw omgeving.

Micro soft ondersteunt enkele SAP HANA mogelijkheden voor hoge Beschik baarheid met HANA grote instanties. Deze mogelijkheden omvatten:

- **Opslag replicatie**: de mogelijkheid van het opslag systeem om alle gegevens te repliceren naar een andere Hana grote instantie stempel in een andere Azure-regio. SAP HANA werkt onafhankelijk van deze methode. Deze functionaliteit is het standaard mechanisme voor herstel na nood gevallen dat wordt aangeboden voor HANA grote instanties.
- **Hana-systeem replicatie**: de [replicatie van alle gegevens in SAP Hana](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) naar een afzonderlijk SAP Hana systeem. De doel stelling voor herstel tijd wordt met regel matige tussen pozen geminimaliseerd door gegevens replicatie. SAP HANA ondersteunt asynchrone, synchrone in-Memory en synchrone modi. De synchrone modus wordt alleen gebruikt voor SAP HANA systemen die zich binnen hetzelfde Data Center bevinden of kleiner zijn dan 100 km. Met het huidige ontwerp van HANA grote instantie-stem pels kan HANA-systeem replicatie alleen worden gebruikt voor hoge Beschik baarheid binnen één regio. Voor HANA-systeem replicatie is een reverse proxy-of routerings onderdeel van een derde partij vereist voor nood herstel configuraties in een andere Azure-regio. 
- **Automatische failover van host**: een lokale oplossing voor fout herstel voor SAP Hana die een alternatief is voor Hana-systeem replicatie. Als het hoofd knooppunt niet beschikbaar is, configureert u een of meer stand-by SAP HANA knoop punten in de Scale-outmodus en wordt SAP HANA automatisch een failover naar een knoop punt voor stand-by.

SAP HANA op Azure (grote instanties) wordt aangeboden in twee Azure-regio's in vier geopolitieke gebieden (VS, Australië, Europa en Japan). Twee regio's binnen een geopolitieke regio die als host van HANA grote instantie stempels zijn verbonden met afzonderlijke toegewezen netwerk circuits. Deze worden gebruikt voor het repliceren van opslag momentopnamen om nood herstel methoden te bieden. De replicatie is standaard niet ingesteld voor klanten die de functionaliteit voor herstel na nood gevallen best Ellen. Opslag replicatie is afhankelijk van het gebruik van moment opnamen van opslag voor HANA grote instanties. Het is niet mogelijk om een Azure-regio te kiezen als een DR-regio die zich in een ander geopolitieke gebied bevindt. 

De volgende tabel bevat de momenteel ondersteunde methoden en combi Naties voor hoge Beschik baarheid en nood herstel:

| Het scenario dat wordt ondersteund in HANA grote instanties | Optie hoge Beschik baarheid | Optie voor nood herstel | Opmerkingen |
| --- | --- | --- | --- |
| Eén knooppunt | Niet beschikbaar. | Specifieke DR-installatie.<br /> DR-installatie voor meerdere doel einden. | |
| Automatische failover van host: uitschalen (met of zonder stand-by)<br /> inclusief 1 + 1 | Mogelijk met de stand-by maken van de actieve rol.<br /> HANA bepaalt de functie-Switch. | Specifieke DR-installatie.<br /> DR-installatie voor meerdere doel einden.<br /> DR-synchronisatie met behulp van opslag replicatie. | HANA-volume sets zijn gekoppeld aan alle knoop punten.<br /> De DR-site moet hetzelfde aantal knoop punten hebben. |
| HANA-systeem replicatie | Mogelijk met een primaire of secundaire installatie.<br /> Secundair wordt verplaatst naar de primaire rol in een failover-case.<br /> HANA-systeem replicatie en failover van besturings systeem beheer. | Specifieke DR-installatie.<br /> DR-installatie voor meerdere doel einden.<br /> DR-synchronisatie met behulp van opslag replicatie.<br /> DR met behulp van HANA-systeem replicatie is nog niet mogelijk zonder onderdelen van derden. | Er wordt een afzonderlijke set schijf volumes aan elk knoop punt gekoppeld.<br /> Alleen schijf volumes van de secundaire replica in de productie site worden gerepliceerd naar de nood herstel locatie.<br /> Er is één set volumes vereist op de DR-site. | 

Een specifieke DR-installatie is waar de HANA-eenheid voor grote instanties in de DR-site niet wordt gebruikt voor het uitvoeren van een andere werk belasting of een niet-productie systeem. De eenheid is passief en wordt alleen geïmplementeerd als een nood failover wordt uitgevoerd. Deze installatie is echter geen voorkeurs keuze voor veel klanten.

Raadpleeg [HLI ondersteunde scenario's](hana-supported-scenario.md) voor meer informatie over de opslag indeling en Ethernet-gegevens voor uw architectuur.

> [!NOTE]
> [SAP Hana MCOD-implementaties](https://launchpad.support.sap.com/#/notes/1681092) (meerdere Hana-instanties op één eenheid) als bedekkings scenario's werken met de ha-en Dr-methoden die in de tabel worden weer gegeven. Een uitzonde ring is het gebruik van HANA-systeem replicatie met een automatische failover-cluster op basis van pacemaker. Een dergelijk geval ondersteunt slechts één HANA-exemplaar per eenheid. Voor [SAP Hana MDC](https://launchpad.support.sap.com/#/notes/2096000) -implementaties werkt alleen op niet-opslag gebaseerde ha en Dr-methoden als er meerdere tenants worden geïmplementeerd. Als er één Tenant is geïmplementeerd, zijn alle vermelde methoden geldig.  

Een DR-installatie voor meerdere doel einden is waar de HANA-eenheid voor grote instanties op de DR-site een niet-productiewerk belasting uitvoert. Als nood geval is, sluit u het niet-productie systeem af, koppelt u de volume sets met opslag-gerepliceerd (extra) en start u vervolgens het exemplaar van de productie HANA. De meeste klanten die de functie voor nood herstel in de HANA-situatie gebruiken, gebruiken deze configuratie. 


Meer informatie over SAP HANA hoge Beschik baarheid vindt u in de volgende SAP-artikelen: 

- [White Paper SAP HANA hoge Beschik baarheid](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA-beheer handleiding](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Academy video op SAP HANA systeem replicatie SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP-ondersteunings Opmerking #1999880: veelgestelde vragen over SAP HANA systeem replicatie](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP-ondersteunings Opmerking #2165547: SAP HANA back-ups maken en herstellen binnen SAP HANA systeem replicatie omgeving](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP-ondersteunings Opmerking #1984882: SAP HANA systeem replicatie gebruiken voor hardware Exchange met minimale/nul downtime](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Netwerk overwegingen voor herstel na nood gevallen met HANA grote instanties

Als u wilt profiteren van de functionaliteit voor herstel na nood gevallen van HANA grote instanties, moet u de netwerk verbinding met de twee Azure-regio's ontwerpen. U hebt een Azure ExpressRoute-circuit verbinding nodig van on-premises in uw belangrijkste Azure-regio en een ander circuit verbinding van on-premises naar uw regio voor nood herstel. Deze maat regel behandelt een situatie waarin een probleem is opgetreden in een Azure-regio, waaronder een MSEE-locatie (micro soft Enter prise edge router).

Als tweede meting kunt u alle Azure Virtual Networks verbinden die verbinding maken met SAP HANA op Azure (grote exemplaren) in een regio in een ExpressRoute-circuit dat in de andere regio HANA grote instanties verbindt. Met deze *Cross Connect*-services die worden uitgevoerd op een virtueel Azure-netwerk in regio 1 kunnen verbinding maken met Hana grote instantie-eenheden in regio 2 en de andere manier. Deze meting verhelpt een geval waarbij slechts één van de MSEE locaties die verbinding maken met uw on-premises locatie met Azure offline gaat.

In de volgende afbeelding ziet u een flexibele configuratie voor nood herstel cases:

![Optimale configuratie voor herstel na nood gevallen](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Andere vereisten met HANA grote exemplaren opslag replicatie voor nood herstel

Naast de voor gaande vereisten voor een nood herstel installatie met HANA grote instanties moet u het volgende doen:

- Bestel SAP HANA op Azure (grote instanties) Sku's van dezelfde grootte als uw productie-Sku's en implementeer deze in de nood herstel regio. In de huidige klant implementaties worden deze instanties gebruikt om niet-productie HANA-instanties uit te voeren. Deze configuraties worden ' DR- *Setup van meerdere doel einden*' genoemd.   
- Bestel extra opslag ruimte op de DR-site voor elk van uw SAP HANA op Azure (grote instanties) Sku's die u wilt herstellen op de site voor nood herstel. Als u extra opslag aanschaft, kunt u de opslag volumes toewijzen. U kunt de volumes die het doel zijn van de opslag replicatie, toewijzen vanuit de Azure-regio van uw productie in de Azure-regio voor herstel na nood gevallen.
- In het geval waar u de HSR-configuratie hebt ingesteld en u de replicatie op basis van opslag naar de DR-site hebt ingesteld, moet u extra opslag ruimte kopen op de DR-site, zodat de gegevens van de primaire en secundaire knoop punten worden gerepliceerd naar de DR-site.

  **Volgende stappen**
- Raadpleeg [back-up en herstel](hana-backup-restore.md).













