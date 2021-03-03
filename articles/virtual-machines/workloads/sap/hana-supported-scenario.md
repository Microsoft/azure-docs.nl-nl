---
title: Ondersteunde scenario's voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Scenario's die worden ondersteund en de architectuur Details voor SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a49c55da0bb8018b3faa7c6f70b02e79d9478603
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666689"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Ondersteunde scenario's voor HANA grote instanties
In dit artikel worden de ondersteunde scenario's en de details van de architectuur voor HANA-grote instanties (HLI) beschreven.

>[!NOTE]
>Als uw vereiste scenario niet in dit artikel wordt vermeld, neemt u contact op met het micro soft service management-team om uw vereisten te beoordelen.
Voordat u de HLI-eenheid instelt, valideert u het ontwerp met SAP of uw service-implementatie partner.

## <a name="terms-and-definitions"></a>Termen en definities
Laten we de termen en definities begrijpen die in dit artikel worden gebruikt:

- **Sid**: een systeem-id voor het Hana-systeem
- **HLI**: grote instanties in Hana
- **Dr**: herstel na nood geval
- **Normaal Dr**: een systeem installatie met een speciale resource voor alleen Dr-doel einden
- **Multifunctionele Dr**: een Dr-site systeem dat is geconfigureerd voor het gebruik van een niet-productie omgeving naast een productie-exemplaar dat is geconfigureerd voor een Dr-gebeurtenis 
- **Enkelvoudige sid**: een systeem waarop één exemplaar is geïnstalleerd
- **Multi-sid**: een systeem waarop meerdere exemplaren zijn geconfigureerd; ook wel een MCOS omgeving genoemd
- **HSR**: SAP Hana-systeem replicatie

## <a name="overview"></a>Overzicht
HANA grote instanties bieden ondersteuning voor verschillende architecturen om u te helpen bij het uitvoeren van uw bedrijfs vereisten. In de volgende secties worden de architectuur scenario's en de bijbehorende configuratie gegevens besproken. 

Het afgeleide architectuur ontwerp is uitsluitend gebaseerd op een infrastructuur perspectief en u moet SAP of uw implementatie partners raadplegen voor de HANA-implementatie. Als uw scenario's niet in dit artikel worden vermeld, neemt u contact op met het Microsoft-account team om de architectuur te beoordelen en een oplossing voor u af te leiden.

> [!NOTE]
> Deze architecturen zijn volledig compatibel met het TDI-ontwerp (designed Data Integration) en worden ondersteund door SAP.

In dit artikel worden de details van de twee onderdelen in elke ondersteunde architectuur beschreven:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Elke ingerichte server is vooraf geconfigureerd met sets van Ethernet-interfaces. De Ethernet-interfaces die op elke HLI-eenheid zijn geconfigureerd, worden onderverdeeld in vier typen:

- **A**: wordt gebruikt voor of door client toegang.
- **B**: wordt gebruikt voor communicatie tussen knoop punten. Deze interface is geconfigureerd op alle servers (onafhankelijk van de aangevraagde topologie), maar wordt alleen gebruikt voor scale-out-scenario's.
- **C**: wordt gebruikt voor connectiviteit tussen knoop punten.
- **D**: wordt gebruikt voor de verbinding van knoop punt-naar-iSCSI-apparaat voor STONITH-installatie. Deze interface wordt alleen geconfigureerd wanneer een HSR-installatie wordt aangevraagd.  

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Knoop punt-naar-knoop punt|
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | STONITH |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Knoop punt-naar-knoop punt|
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | STONITH |

U kiest de interface op basis van de topologie die is geconfigureerd op de HLI-eenheid. Bijvoorbeeld: Interface ' B ' is ingesteld voor communicatie tussen knoop punten, wat nuttig is wanneer u een scale-out-topologie hebt geconfigureerd. Deze interface wordt niet gebruikt voor één knoop punt, scale-up configuraties. Raadpleeg de vereiste scenario's (verderop in dit artikel) voor meer informatie over het gebruik van de interface. 

Als dat nodig is, kunt u zelf extra NIC-kaarten definiëren. De configuraties van bestaande Nic's kunnen echter *niet* worden gewijzigd.

>[!NOTE]
>Mogelijk vindt u aanvullende interfaces die fysieke interfaces of bindingen zijn. Houd rekening met alleen de eerder genoemde interfaces voor uw use-case. Andere kunnen worden genegeerd.

De distributie voor eenheden met twee toegewezen IP-adressen moet er als volgt uitzien:

- Ethernet "A" moet een toegewezen IP-adres hebben dat binnen het bereik van de IP-adres groep van de server ligt dat u hebt verzonden naar micro soft. Dit IP-adres moet worden bewaard in de map *bestand/etc/hosts* van het besturings systeem.

- Ethernet ' C ' moet een toegewezen IP-adres hebben dat wordt gebruikt voor communicatie met NFS. Dit adres hoeft *niet* te worden bijgehouden in de map *etc/hosts* om instantie-to-instance verkeer binnen de Tenant toe te staan.

Voor de implementatie van HANA-systeem replicatie of HANA-uitschalen is een Blade configuratie met twee toegewezen IP-adressen niet geschikt. Als u slechts twee toegewezen IP-adressen hebt en u een dergelijke configuratie wilt implementeren, neemt u contact op met SAP HANA op Azure Service Management. Ze kunnen een derde IP-adres in een derde VLAN toewijzen. Voor HANA-eenheden voor grote instanties met drie toegewezen IP-adressen op drie NIC-poorten gelden de volgende gebruiks regels:

- Ethernet "A" moet een toegewezen IP-adres hebben dat buiten het bereik van de IP-adres groep van de server valt dat u hebt verzonden naar micro soft. Dit IP-adres mag niet worden beheerd in de map *etc/hosts* van het besturings systeem.

- Ethernet "B" moet uitsluitend worden bewaard in de map *etc/hosts* voor communicatie tussen de verschillende instanties. Dit zijn de IP-adressen die moeten worden onderhouden in scale-out HANA-configuraties als de IP-adressen die door HANA worden gebruikt voor de configuratie tussen knoop punten.

- Ethernet ' C ' moet een toegewezen IP-adres hebben dat wordt gebruikt voor communicatie met NFS-opslag. Dit type adres kan niet worden bijgehouden in de map *etc/hosts* .

- Ethernet "D" mag alleen worden gebruikt voor toegang tot STONITH-apparaten voor pacemaker. Deze interface is vereist bij het configureren van HANA-systeem replicatie en het maken van een automatische failover van het besturings systeem met behulp van een op SBD gebaseerd apparaat.


### <a name="storage"></a>Storage
Opslag is vooraf geconfigureerd op basis van de aangevraagde topologie. De grootte van het volume en de koppel punten variëren, afhankelijk van het aantal servers, het aantal Sku's en de geconfigureerde topologie. Raadpleeg de vereiste scenario's (verderop in dit artikel) voor meer informatie. Als u meer opslag ruimte nodig hebt, kunt u deze kopen in stappen van 1 TB.

>[!NOTE]
>Het koppel punt/usr/sap/ \<SID> is een symbolische koppeling naar het/Hana/Shared-koppel punt.


## <a name="supported-scenarios"></a>Ondersteunde scenario's

In de architectuur diagrammen in de volgende secties worden de volgende notaties gebruikt:

[![Tabel met architectuur diagrammen](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Dit zijn de ondersteunde scenario's:

* Eén knoop punt met één SID
* MCOS met één knoop punt
* Eén knoop punt met DR (normaal)
* Eén knoop punt met DR (meerdere doel einden)
* HSR met STONITH
* HSR met DR (normaal/Multipurpose) 
* Automatische failover van host (1 + 1) 
* Uitschalen met stand-by
* Uitschalen zonder stand-by
* Uitschalen met DR

## <a name="single-node-with-one-sid"></a>Eén knoop punt met één SID

Deze topologie ondersteunt één knoop punt in een scale-up configuratie met één SID.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knoop punt met één SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID | HANA-installatie | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.

## <a name="single-node-mcos"></a>MCOS met één knoop punt

Deze topologie ondersteunt één knoop punt in een scale-up configuratie met meerdere Sid's.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![MCOS met één knoop punt](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID1 | HANA-installatie voor SID1 | 
|/hana/data/SID1/mnt00001 | Installatie van gegevens bestanden voor SID1 | 
|/hana/log/SID1/mnt00001 | Installatie van logboek bestanden voor SID1 | 
|/hana/logbackups/SID1 | Logboeken voor SID1 opnieuw uitvoeren |
|/hana/shared/SID2 | HANA-installatie voor SID2 | 
|/hana/data/SID2/mnt00001 | Installatie van gegevens bestanden voor SID2 | 
|/hana/log/SID2/mnt00001 | Installatie van logboek bestanden voor SID2 | 
|/hana/logbackups/SID2 | Logboeken voor SID2 opnieuw uitvoeren |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Distributie van de volume grootte is gebaseerd op de grootte van de data base in het geheugen. Zie [overzicht en architectuur](./hana-overview-architecture.md)voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund in een multi-sid-omgeving.

## <a name="single-node-with-dr-using-storage-replication"></a>Eén knoop punt met behulp van opslag replicatie met DR
 
Deze topologie ondersteunt één knoop punt in een scale-up configuratie met een of meer Sid's, waarbij replicatie op basis van opslag naar de DR-site voor een primaire SID wordt ondersteund. In het diagram wordt slechts één-SID systeem op de primaire site weer gegeven, maar worden ook MCOS-systemen ondersteund.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knoop punt met behulp van opslag replicatie met DR](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID | HANA-installatie voor SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren op SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: distributie van de volume grootte is gebaseerd op de grootte van de data base in het geheugen. Zie [overzicht en architectuur](./hana-overview-architecture.md)voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund in een multi-sid-omgeving.
- Op de DR-site: de volumes en koppel punten worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het exemplaar van de productie HANA-instantie in de DR/HLI-eenheid. 
- Op de DR-site: de gegevens, logboek back-ups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover gekoppeld. Zie voor meer informatie [nood herstel procedure failover](./hana-overview-high-availability-disaster-recovery.md).
- Het opstart volume voor de *SKU type I-klasse* wordt gerepliceerd naar het Dr-knoop punt.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Eén knoop punt met DR (Multipurpose) met behulp van storage-replicatie
 
Deze topologie ondersteunt één knoop punt in een scale-up configuratie met een of meer Sid's, waarbij replicatie op basis van opslag naar de DR-site voor een primaire SID wordt ondersteund. In het diagram wordt slechts één-SID-systeem op de primaire site weer gegeven, maar MCOS-systemen (multi-SID) worden ook ondersteund. Op de DR-site wordt de HLI-eenheid gebruikt voor het exemplaar van QA terwijl de productie bewerkingen vanaf de primaire site worden uitgevoerd. Tijdens een DR-failover (of failover-test) wordt het exemplaar van QA op de DR-site offline gezet.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knoop punt met DR (Multipurpose) met behulp van storage-replicatie](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/shared/QA-SID | HANA-installatie voor QA-SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevens bestanden voor QA-SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logboek bestanden voor QA-SID |
|/hana/logbackups/QA-SID | Logboeken opnieuw voor QA-SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: distributie van de volume grootte is gebaseerd op de grootte van de data base in het geheugen. Zie [overzicht en architectuur](./hana-overview-architecture.md)voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund in een multi-sid-omgeving.
- Op de DR-site: de volumes en koppel punten worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het exemplaar van de productie HANA-instantie in de DR/HLI-eenheid. 
- Op de DR-site: de gegevens, logboek back-ups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover gekoppeld. Zie voor meer informatie [nood herstel procedure failover](./hana-overview-high-availability-disaster-recovery.md). 
- Op de DR-site: de gegevens, logboek back-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als ' installatie van QA-instantie ') zijn geconfigureerd voor de installatie van het QA-exemplaar.
- Het opstart volume voor de *SKU type I-klasse* wordt gerepliceerd naar het Dr-knoop punt.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR met STONITH voor hoge Beschik baarheid
 
Deze topologie ondersteunt twee knoop punten voor de configuratie van HANA-systeem replicatie. Deze configuratie wordt alleen ondersteund voor afzonderlijke HANA-instanties op een knoop punt. Dit betekent dat MCOS-scenario's *niet* worden ondersteund.

> [!NOTE]
> Vanaf december 2019 wordt deze architectuur alleen ondersteund voor het SUSE-besturings systeem.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![HSR met STONITH voor hoge Beschik baarheid](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Gebruikt voor STONITH |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Gebruikt voor STONITH |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knoop punt**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op het secundaire knoop punt**|
|/hana/shared/SID | HANA-installatie voor secundaire SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor secundaire SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor secundaire SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor secundaire SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: distributie van de volume grootte is gebaseerd op de grootte van de data base in het geheugen. Zie [overzicht en architectuur](./hana-overview-architecture.md)voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund in een multi-sid-omgeving.
- STONITH: er is een SBD geconfigureerd voor de STONITH-installatie. Het gebruik van STONITH is echter optioneel.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Hoge Beschik baarheid met HSR en DR met opslag replicatie
 
Deze topologie ondersteunt twee knoop punten voor de configuratie van HANA-systeem replicatie. Zowel normale als multifunctionele DRs worden ondersteund. Deze configuraties worden alleen ondersteund voor afzonderlijke HANA-instanties in een knoop punt. Dit betekent dat MCOS-scenario's *niet* worden ondersteund met deze configuraties.

In het diagram wordt een scenario met meerdere doel einden weer gegeven op de DR-site, waarbij de HLI-eenheid wordt gebruikt voor het QA-exemplaar terwijl de productie bewerkingen vanaf de primaire site worden uitgevoerd. Tijdens een DR-failover (of failover-test) wordt het exemplaar van QA op de DR-site offline gezet. 

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Hoge Beschik baarheid met HSR en DR met opslag replicatie](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Gebruikt voor STONITH |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Gebruikt voor STONITH |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knoop punt op de primaire site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op het secundaire knoop punt op de primaire site**|
|/hana/shared/SID | HANA-installatie voor secundaire SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor secundaire SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor secundaire SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor secundaire SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/shared/QA-SID | HANA-installatie voor QA-SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevens bestanden voor QA-SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logboek bestanden voor QA-SID |
|/hana/logbackups/QA-SID | Logboeken opnieuw voor QA-SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: distributie van de volume grootte is gebaseerd op de grootte van de data base in het geheugen. Zie [overzicht en architectuur](./hana-overview-architecture.md)voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund in een multi-sid-omgeving.
- STONITH: er is een SBD geconfigureerd voor de STONITH-installatie. Het gebruik van STONITH is echter optioneel.
- Op de DR-site: *Er zijn twee sets opslag volumes vereist* voor de replicatie van het primaire en het secundaire knoop punt.
- Op de DR-site: de volumes en koppel punten worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het exemplaar van de productie HANA-instantie in de DR/HLI-eenheid. 
- Op de DR-site: de gegevens, logboek back-ups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover gekoppeld. Zie voor meer informatie [nood herstel procedure failover](./hana-overview-high-availability-disaster-recovery.md). 
- Op de DR-site: de gegevens, logboek back-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als ' installatie van QA-instantie ') zijn geconfigureerd voor de installatie van het QA-exemplaar.
- Het opstart volume voor de *SKU type I-klasse* wordt gerepliceerd naar het Dr-knoop punt.


## <a name="host-auto-failover-11"></a>Automatische failover van host (1 + 1)
 
Deze topologie ondersteunt twee knoop punten in een automatische failover-configuratie van een host. Er bevindt zich één knoop punt met een rol Master/Worker en een andere als stand-by. *SAP ondersteunt dit scenario alleen voor S/4 HANA.* Zie voor meer informatie [OSS note 2408419-SAP S/4HANA-ondersteuning voor meerdere knoop punten](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Architectuurdiagram  

![Automatische failover van host (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Communicatie tussen knoop punten |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Communicatie tussen knoop punten |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de Master-en standby-knoop punten**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |



### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Bij stand-by: de volumes en koppel punten worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het HANA-exemplaar op de stand-by-eenheid.
 

## <a name="scale-out-with-standby"></a>Uitschalen met stand-by
 
Deze topologie ondersteunt meerdere knoop punten in een scale-out configuratie. Er is één knoop punt met een rol, een of meer knoop punten met een werk rollen en een of meer knoop punten als stand-by. Er kan echter maar één hoofd knooppunt op één moment zijn.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![Uitschalen met stand-by](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Communicatie tussen knoop punten |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Communicatie tussen knoop punten |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de knoop punten Master, Worker en stand-by**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |


## <a name="scale-out-without-standby"></a>Uitschalen zonder stand-by
 
Deze topologie ondersteunt meerdere knoop punten in een scale-out configuratie. Er bevindt zich één knoop punt met een master-functie en een of meer knoop punten met een worker-rol. Er kan echter maar één hoofd knooppunt op één moment zijn.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![Uitschalen zonder stand-by](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Communicatie tussen knoop punten |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Communicatie tussen knoop punten |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de Master-en worker-knoop punten**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Uitschalen met behulp van de opslag replicatie met DR
 
Deze topologie ondersteunt meerdere knoop punten in een scale-out met een DR. Zowel normale als multifunctionele DRs worden ondersteund. In het diagram wordt alleen DR. 1. U kunt deze topologie aanvragen met of zonder het stand-by-knoop punt.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![Uitschalen met behulp van de opslag replicatie met DR](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Communicatie tussen knoop punten |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Communicatie tussen knoop punten |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knoop punt**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op het DR-knoop punt**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
-  Op de DR-site: de volumes en koppel punten worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het exemplaar van de productie HANA-instantie in de DR/HLI-eenheid. 
- Op de DR-site: de gegevens, logboek back-ups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover gekoppeld. Zie voor meer informatie [nood herstel procedure failover](./hana-overview-high-availability-disaster-recovery.md). 
- Het opstart volume voor de *SKU type I-klasse* wordt gerepliceerd naar het Dr-knoop punt.


## <a name="single-node-with-dr-using-hsr"></a>Eén knoop punt met DR met behulp van HSR
 
Deze topologie ondersteunt één knoop punt in een scale-up configuratie met één SID, met HANA-systeem replicatie naar de DR-site voor een primaire SID. In het diagram wordt slechts één-SID-systeem op de primaire site weer gegeven, maar MCOS-systemen (multi-SID) worden ook ondersteund.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knoop punt met DR met behulp van HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI/HSR |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI/HSR |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd op de HLI-eenheden (primair en DR):

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID | HANA-installatie voor SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren op SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: distributie van de volume grootte is gebaseerd op de grootte van de data base in het geheugen. Zie [overzicht en architectuur](./hana-overview-architecture.md)voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund in een multi-sid-omgeving.
- Het primaire knoop punt wordt met behulp van HANA-systeem replicatie gesynchroniseerd met het DR-knoop punt. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) wordt gebruikt om de ExpressRoute-circuits samen te koppelen om een particulier netwerk tussen uw regionale netwerken te maken.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR van één knoop punt naar DR (kosten geoptimaliseerd) 
 
 Deze topologie ondersteunt één knoop punt in een scale-up configuratie met één SID, met HANA-systeem replicatie naar de DR-site voor een primaire SID. In het diagram wordt slechts één-SID-systeem op de primaire site weer gegeven, maar MCOS-systemen (multi-SID) worden ook ondersteund. Op de DR-site wordt een HLI-eenheid gebruikt voor het exemplaar van QA terwijl de productie bewerkingen vanaf de primaire site worden uitgevoerd. Tijdens een DR-failover (of failover-test) wordt het exemplaar van QA op de DR-site offline gezet.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![HSR van één knoop punt naar DR (kosten geoptimaliseerd)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI/HSR |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI/HSR |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|/hana/shared/QA-SID | HANA-installatie voor QA-SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevens bestanden voor QA-SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logboek bestanden voor QA-SID |
|/hana/logbackups/QA-SID | Logboeken opnieuw voor QA-SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: distributie van de volume grootte is gebaseerd op de grootte van de data base in het geheugen. Zie [overzicht en architectuur](./hana-overview-architecture.md)voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund in een multi-sid-omgeving.
- Op de DR-site: de volumes en koppel punten worden geconfigureerd (gemarkeerd als ' productie-exemplaar op DR-site ') voor de installatie van het exemplaar van HANA-instanties in de DR/HLI-eenheid. 
- Op de DR-site: de gegevens, logboek back-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als ' installatie van QA-instantie ') zijn geconfigureerd voor de installatie van het QA-exemplaar.
- Het primaire knoop punt wordt met behulp van HANA-systeem replicatie gesynchroniseerd met het DR-knoop punt. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) wordt gebruikt om de ExpressRoute-circuits samen te koppelen om een particulier netwerk tussen uw regionale netwerken te maken.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Hoge Beschik baarheid en herstel na nood gevallen met HSR 
 
 Deze topologie ondersteunt twee knoop punten voor de configuratie van de HANA-systeem replicatie voor hoge Beschik baarheid van de lokale regio's. Voor de DR wordt het derde knoop punt bij de DR-regio gesynchroniseerd met de primaire site met behulp van HSR (async-modus). 

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Hoge Beschik baarheid en herstel na nood gevallen met HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI/HSR |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI/HSR |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Op de DR-site: de volumes en koppel punten worden geconfigureerd (gemarkeerd als ' productie-DR-instantie ') voor de installatie van het exemplaar van de productie HANA-instantie in de DR/HLI-eenheid. 
- Het primaire site knooppunt wordt gesynchroniseerd met het DR-knoop punt met behulp van HANA-systeem replicatie. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) wordt gebruikt om de ExpressRoute-circuits samen te koppelen om een particulier netwerk tussen uw regionale netwerken te maken.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Hoge Beschik baarheid en herstel na nood gevallen met HSR (kosten geoptimaliseerd)
 
 Deze topologie ondersteunt twee knoop punten voor de configuratie van de HANA-systeem replicatie voor hoge Beschik baarheid van de lokale regio's. Voor de DR wordt het derde knoop punt bij de DR-regio gesynchroniseerd met de primaire site met behulp van HSR (async-modus), terwijl er al een ander exemplaar (bijvoorbeeld QA) wordt uitgevoerd vanaf het DR-knoop punt. 

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Hoge Beschik baarheid en herstel na nood gevallen met HSR (kosten geoptimaliseerd)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI/HSR |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI/HSR |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|/hana/shared/QA-SID | HANA-installatie voor QA-SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevens bestanden voor QA-SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logboek bestanden voor QA-SID |
|/hana/logbackups/QA-SID | Logboeken opnieuw voor QA-SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Op de DR-site: de volumes en koppel punten worden geconfigureerd (gemarkeerd als ' productie-DR-instantie ') voor de installatie van het exemplaar van de productie HANA-instantie in de DR/HLI-eenheid. 
- Op de DR-site: de gegevens, logboek back-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als ' installatie van QA-instantie ') zijn geconfigureerd voor de installatie van het QA-exemplaar.
- Het primaire site knooppunt wordt gesynchroniseerd met het DR-knoop punt met behulp van HANA-systeem replicatie. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) wordt gebruikt om de ExpressRoute-circuits samen te koppelen om een particulier netwerk tussen uw regionale netwerken te maken.

## <a name="scale-out-with-dr-using-hsr"></a>Uitschalen met DR met behulp van HSR
 
Deze topologie ondersteunt meerdere knoop punten in een scale-out met een DR. U kunt deze topologie aanvragen met of zonder het stand-by-knoop punt. Het primaire site knooppunt wordt gesynchroniseerd met het DR-site knooppunt met behulp van HANA-systeem replicatie (async-modus).


### <a name="architecture-diagram"></a>Architectuurdiagram  

[![Uitschalen met Dr met behulp van HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| Logische NIC-interface | SKU-type | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | ETH0. Tenant | eno1. Tenant | Client-naar-HLI/HSR |
| B | TYPE I | eth2. Tenant | eno3. Tenant | Communicatie tussen knoop punten |
| C | TYPE I | eth1. Tenant | eno2. Tenant | Knoop punt-naar-opslag |
| D | TYPE I | eth4. Tenant | eno4. Tenant | Geconfigureerd maar niet in gebruik |
| A | TYPE II | VLAN\<tenantNo> | team0. Tenant | Client-naar-HLI/HSR |
| B | TYPE II | VLAN\<tenantNo+2> | team0. Tenant + 2 | Communicatie tussen knoop punten |
| C | TYPE II | VLAN\<tenantNo+1> | team0. Tenant + 1 | Knoop punt-naar-opslag |
| D | TYPE II | VLAN\<tenantNo+3> | team0. Tenant + 3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende koppel punten zijn vooraf geconfigureerd:

| Koppel punt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knoop punt**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op het DR-knoop punt**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevens bestanden voor productie-SID | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Op de DR-site: de volumes en koppel punten zijn geconfigureerd voor de installatie van het productie HANA-exemplaar op de DR/HLI-eenheid. 
- Het primaire site knooppunt wordt gesynchroniseerd met het DR-knoop punt met behulp van HANA-systeem replicatie. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) wordt gebruikt om de ExpressRoute-circuits samen te koppelen om een particulier netwerk tussen uw regionale netwerken te maken.


## <a name="next-steps"></a>Volgende stappen
- [Infra structuur en connectiviteit](./hana-overview-infrastructure-connectivity.md) voor Hana grote instanties
- [Hoge Beschik baarheid en herstel na nood](./hana-overview-high-availability-disaster-recovery.md) geval voor Hana grote instanties
