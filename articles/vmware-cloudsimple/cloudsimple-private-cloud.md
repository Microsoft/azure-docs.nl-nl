---
title: Azure VMware-oplossing door CloudSimple-private clouds
description: Meer informatie over CloudSimple-concepten en-voor delen, waaronder het volt ooien van de operationele continuïteit van VMware, de compatibiliteit met bestaande hulpprogram ma's, vaardig heden en processen.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140714"
---
# <a name="cloudsimple-private-cloud-overview"></a>Overzicht van CloudSimple Private Cloud

Met CloudSimple worden VMware-workloads binnen enkele minuten getransformeerd en uitgebreid naar open bare Clouds. Met de CloudSimple-service kunt u VMware systeem eigen implementeren op een Azure bare-metal infra structuur. Uw implementatie bevindt zich op Azure-locaties en integreert volledig met de rest van de Azure-Cloud.

De CloudSimple-oplossing biedt volledige VMware-operationele continuïteit. Deze oplossing biedt u de voor delen van de open bare cloud van:

* Elasticiteit
* Innovatie
* Efficiëntie

Met CloudSimple profiteert u van een model voor Cloud verbruik dat uw total cost of ownership verlaagt. Het biedt ook mogelijkheden voor inrichting op aanvraag, betalen per gebruik en capaciteits optimalisatie.

CloudSimple is volledig compatibel met:

* Bestaande hulpprogram ma's
* Vaardigheden
* Processen

Met deze compatibiliteit kunnen uw teams werk belastingen op de Azure-Cloud beheren zonder dat dit soort beleid wordt verstoord:

* Netwerk
* Beveiliging  
* Gegevensbescherming  
* Controleren

CloudSimple beheert de infra structuur en alle benodigde netwerk-en beheer Services. Met de CloudSimple-service kan uw team zich richten op het volgende:

* Bedrijfs waarde
* Toepassingsinrichting
* Bedrijfscontinuïteit
* Ondersteuning
* Beleidsafdwinging

## <a name="private-cloud-environment-overview"></a>Overzicht van de privécloud-omgeving

Een Privécloud is een geïsoleerde VMware-stack die ondersteuning biedt voor:

* ESXi-hosts
* vCenter
* vSAN
* NSX

Persoonlijke Clouds worden beheerd via de CloudSimple-Portal. Ze hebben hun eigen vCenter-Server in een eigen beheer domein.

De stack wordt uitgevoerd op:

* Toegewezen knooppunten
* Geïsoleerde bare metal-hardwareprofielen

Gebruikers gebruiken de stack via systeem eigen VMware-hulpprogram ma's, waaronder:

* vCenter
* NSX Manager

U kunt toegewezen knoop punten implementeren op Azure-locaties. Vervolgens kunt u ze beheren met Azure en CloudSimple. Een Privécloud bestaat uit een of meer vSphere-clusters en elk cluster bevat 3 tot 16 knoop punten.

U kunt een Privécloud maken met behulp van ingekochte, betalen naar gebruik-knoop punten of gereserveerde, toegewezen knoop punten.

U kunt de Privécloud koppelen aan uw on-premises omgeving en het Azure-netwerk met behulp van de volgende verbindingen:

* Beveiligen
* Particuliere VPN
* Azure ExpressRoute

De Privécloud is ontworpen om individuele storings punten te elimineren:

* ESXi-clusters zijn geconfigureerd met een hoge Beschik baarheid van vSphere en hebben een grootte van ten minste één reserve knooppunt voor tolerantie.
* vSAN biedt redundante primaire opslag. vSan vereist ten minste drie knoop punten om bescherming te bieden tegen één fout. U kunt vSAN configureren om hogere toleranties voor grotere clusters te bieden.
* U kunt vCenter-, PSC-en NSX-beheer machines configureren met RAID-10-opslag beleid om te beschermen tegen opslag storingen. vSphere HA beschermt op knoop punten en netwerk fouten.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenario's voor het implementeren van een Privécloud

Hier volgen enkele voor beelden van gebruiks voorbeelden voor de implementatie van een Privécloud.

### <a name="data-center-retirement-or-migration"></a>Buiten gebruik stellen of migreren van data centers

* Krijg extra capaciteit wanneer u de limieten bereikt van uw bestaande Data Center of hardware vernieuwt.
* Voeg de benodigde capaciteit toe in de Cloud en Elimineer het beheer van de hardware vernieuwt.
* Verminder het risico en de kosten voor Cloud migraties vergeleken met tijdrovende conversies of herarchitectuur.
* Gebruik vertrouwde VMware-hulpprogram ma's en-vaardig heden om Cloud migraties te versnellen. Gebruik Azure-Services in de cloud om uw toepassingen in uw tempo te moderniseren.

### <a name="expand-on-demand"></a>Op aanvraag uitvouwen

* Breid uit naar de cloud om te voldoen aan niet-verwachte behoeften, zoals nieuwe ontwikkelings omgevingen of bursts met een seizoen capaciteit.
* Maak op aanvraag nieuwe capaciteit en bewaar deze alleen zolang u dat nodig hebt.
* Verminder uw investering in de voor grond, Versnel snelheid van het inrichten en verminder de complexiteit met dezelfde architectuur en hetzelfde beleid in zowel on-premises als in de Cloud.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Herstel na nood gevallen en virtuele Bureau bladen in de Azure-Cloud

* Externe toegang tot gegevens, apps en Bureau bladen in de Azure-Cloud tot stand brengen. Met verbindingen met een hoge band breedte kunt u snel gegevens uploaden/downloaden om van incidenten te herstellen. Netwerken met lage latentie bieden snelle reactie tijden die gebruikers verwachten van een desktop-app.

* Repliceer al uw beleids regels en netwerken in de Cloud met behulp van de CloudSimple-Portal en vertrouwde VMware-hulpprogram ma's. Replicatie vermindert de inspanning en het risico van het maken en beheren van DR-en VDI-implementaties.

### <a name="high-performance-applications-and-databases"></a>Toepassingen en data bases met hoge prestaties

* Voer uw meest veeleisende workloads uit met de hypergeconvergeerd-architectuur van CloudSimple.
* Voer Oracle, micro soft SQL Server, middleware Systems en no-SQL-data bases met hoge prestaties uit.
* Ervaar de Cloud als uw eigen Data Center met hoge snelheid van 25 Gbps netwerk verbindingen. Met verbindingen met hoge snelheid kunt u hybride apps uitvoeren die on-premises, VMware op Azure en persoonlijke werk belastingen van Azure omvatten, zonder de prestaties te verbeteren.

### <a name="true-hybrid"></a>Waar hybride

* Verdeel DevOps tussen VMware en Azure-Services.
* Optimaliseer VMware-beheer voor Azure-Services en-oplossingen die in al uw workloads kunnen worden toegepast.
* Toegang tot open bare Cloud Services zonder uw Data Center uit te breiden of uw toepassingen opnieuw te ontwerpen.
* Centraliseer identiteiten, Toegangs beheer beleid, logboek registratie en controle voor VMware-toepassingen op Azure.

## <a name="limits"></a>Limieten

De volgende tabel bevat de limieten voor knoop punten van resources van een Privécloud.

| Resource | Limiet |
|----------|-------|
| Minimum aantal knoop punten voor het maken van een Privécloud | 3 |
| Maximum aantal knoop punten in een cluster in een Privécloud | 16 |
| Maximum aantal knoop punten in een Privécloud | 64 |
| Minimum aantal knoop punten op een nieuw cluster | 3 |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een privécloud](create-private-cloud.md)
* Meer informatie over het [configureren van een privécloud](quickstart-create-private-cloud.md)
