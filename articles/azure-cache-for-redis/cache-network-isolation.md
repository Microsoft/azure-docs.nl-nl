---
title: Azure-cache voor redis-netwerk isolatie opties
description: In dit artikel leert u hoe u de beste oplossing voor netwerk isolatie kunt bepalen voor uw behoeften. We gaan de basis beginselen van Azure private link, Azure Virtual Network (VNet)-injectie door nemen en Azure Firewall regels met hun voor delen en beperkingen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621515"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure-cache voor redis-netwerk isolatie opties 
In dit artikel leert u hoe u de beste oplossing voor netwerk isolatie kunt bepalen voor uw behoeften. We gaan de basis beginselen van Azure private link, Azure Virtual Network (VNet)-injectie door nemen en Azure Firewall regels met hun voor delen en beperkingen.  

## <a name="azure-private-link-public-preview"></a>Persoonlijke Azure-koppeling (open bare preview) 
Persoonlijke Azure-koppeling biedt persoonlijke connectiviteit vanuit een virtueel netwerk naar Azure PaaS Services. Het vereenvoudigt de netwerk architectuur en beveiligt de verbinding tussen de eind punten in azure door de bloot stelling van gegevens aan het open bare Internet te elimineren. 

### <a name="advantages"></a>Voordelen
* Ondersteund in Basic, Standard en Premium Azure cache voor redis-exemplaren. 
* Met behulp van een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md)kunt u verbinding maken met een Azure-cache-exemplaar vanuit uw virtuele netwerk via een persoonlijk eind punt, waaraan een persoonlijk IP-adres is toegewezen in een subnet binnen het virtuele netwerk. In dit geval zijn de cache-exemplaren beschikbaar in zowel het VNet als openbaar.  
* Zodra een persoonlijk eind punt is gemaakt, kan de toegang tot het open bare netwerk worden beperkt via de `publicNetworkAccess` vlag. Deze markering is standaard ingesteld op `Disabled` , zodat alleen toegang tot persoonlijke koppelingen wordt toegestaan. U kunt de waarde instellen op `Enabled` of `Disabled` met een patch-aanvraag. Zie [Azure-cache voor redis met Azure private link (preview)](cache-private-link.md)voor meer informatie. 
* Alle externe cache afhankelijkheden hebben geen invloed op de NSG-regels van het VNet.

### <a name="limitations"></a>Beperkingen 
* Netwerk beveiligings groepen (NSG) zijn uitgeschakeld voor privé-eind punten. Als er echter andere bronnen op het subnet aanwezig zijn, wordt NSG Enforcement toegepast op deze resources.
* Geo-replicatie, firewall regels, ondersteuning voor de portal console, meerdere eind punten per geclusterde cache, persistentie naar Firewall en door VNet geïnjecteerde caches worden nog niet ondersteund. 
* Als u verbinding wilt maken met een geclusterde cache, `publicNetworkAccess` moet deze worden ingesteld op `Disabled` en kan er slechts één particuliere eindpunt verbinding zijn.

> [!NOTE]
> Wanneer u een persoonlijk eind punt toevoegt aan een cache-exemplaar, wordt al het redis verkeer naar het persoonlijke eind punt verplaatst vanwege de DNS.
> Zorg ervoor dat vorige firewall regels vóór worden aangepast.  
>
>

## <a name="azure-virtual-network-injection"></a>Azure-Virtual Network injectie 
VNet is de fundamentele bouw steen voor uw particuliere netwerk in Azure. Met VNet kunnen veel Azure-resources veilig communiceren met elkaar, het internet en on-premises netwerken. VNet is net als een traditioneel netwerk dat u in uw eigen Data Center zou uitvoeren, maar met de voor delen van de infra structuur, schaal, Beschik baarheid en isolatie van Azure. 

### <a name="advantages"></a>Voordelen
* Wanneer een Azure-cache voor redis-exemplaar is geconfigureerd met een VNet, is het niet openbaar adresseerbaar en is deze alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet.  
* Wanneer VNet wordt gecombineerd met een beperkt NSG-beleid, vermindert dit het risico van gegevens exfiltration. 
* VNet-implementatie biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor redis, evenals subnetten, Toegangs beheer beleid en andere functies om de toegang verder te beperken. 
* Geo-replicatie wordt ondersteund. 

### <a name="limitations"></a>Beperkingen
* Door VNet geïnjecteerde caches zijn alleen beschikbaar voor Premium Azure-cache voor redis. 
* Wanneer u een door VNet geïnjecteerde cache gebruikt, moet u uw VNet openen om afhankelijkheden zoals Crl's/PKI, azure, Azure Storage, Azure Monitor en meer op te slaan in de cache.  


## <a name="azure-firewall-rules"></a>Azure Firewall regels
[Azure firewall](../firewall/overview.md) is een beheerde, Cloud service voor netwerk beveiliging die uw Azure VNet-resources beveiligt. Het is een volledig stateful firewall als een service met ingebouwde hoge Beschik baarheid en een onbeperkte schaal baarheid van de Cloud. U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken.  

### <a name="advantages"></a>Voordelen
* Wanneer de firewall regels zijn geconfigureerd, kunnen alleen client verbindingen van de opgegeven IP-adresbereiken verbinding maken met de cache. Verbindingen van Azure cache voor redis-bewakings systemen zijn altijd toegestaan, zelfs als de firewall regels zijn geconfigureerd. NSG-regels die u definieert, zijn ook toegestaan.  

### <a name="limitations"></a>Beperkingen
* Firewall regels kunnen worden gebruikt in combi natie met door VNet geïnjecteerde caches, maar nog geen persoonlijke eind punten. 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het configureren van een door [VNet geïnjecteerde cache voor een Premium Azure-cache voor een redis-exemplaar](cache-how-to-premium-vnet.md).  
* Meer informatie over het configureren [van firewall regels voor alle Azure-cache voor redis-lagen](cache-configure.md#firewall). 
* Meer informatie over het [configureren van persoonlijke eind punten voor alle Azure-cache voor redis-lagen](cache-private-link.md).