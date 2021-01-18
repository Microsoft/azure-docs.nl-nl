---
title: bestand opnemen
description: bestand opnemen
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 01/13/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 08e7463f4657b2ae5d6da1017c14226e97af7605
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165736"
---
### <a name="general-requirements"></a>Algemene vereisten

* Het VNet moet in hetzelfde abonnement en dezelfde regio voorkomen als het Batch-account dat u gebruikt om de pool te maken.

* De pool die van het VNet gebruikmaakt, kan een maximum van 4096 knooppunten bevatten.

* Het subnet dat is opgegeven voor de pool moet voldoende vrije IP-adressen hebben voor het aantal virtuele machines voor de pool, ofwel de som van de `targetDedicatedNodes`- en `targetLowPriorityNodes`-eigenschappen van de pool. Als het subnet onvoldoende vrije IP-adressen heeft, wijst de pool de rekenknooppunten gedeeltelijk toe en wordt een fout weergegeven voor het aanpassen van de grootte.

* Uw Azure Storage-eindpunt moet worden omgezet door alle aangepaste DNS-servers die u beschikbaar maakt voor uw VNet. Vooral URL's met de indeling `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` en `<account>.blob.core.windows.net` moeten kunnen worden omgezet.

* Er kunnen meerdere groepen in hetzelfde VNet of in hetzelfde subnet worden gemaakt (op voorwaarde dat er voldoende adresruimte is). Eén groep kan niet bestaan in meerdere VNets of subnetten.

Aanvullende vereisten voor VNet verschillen, afhankelijk van of de Batch-pool zich in de configuratie van de virtuele machine bevindt of in de configuratie van Cloud Services. Voor nieuwe implementaties van pools in een VNet wordt de configuratie van de virtuele machine aanbevolen.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pools in de configuratie van virtuele machines

**Ondersteunde VNets**: alleen op Azure Resource Manager gebaseerde VNets

**Subnet-ID**: bij het opgeven van het subnet met behulp van de Batch-API's moet u de *resource-id* van het subnet gebruiken. De subnet-id het formulier heeft de vorm van:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**Machtigingen**: controleer of uw beveiligingsbeleid of de vergrendelingen die voor het VNet-abonnement of de resourcegroep gelden, een gebruikersmachtiging beperkt om het VNet te beheren.

**Aanvullende netwerkresources**: Batch kent automatisch extra netwerkresources toe aan de resourcegroep met het VNet.

> [!IMPORTANT]
> Voor elke 100 toegewezen knooppunten of knooppunten met een lage prioriteit kent Batch het volgende toe: één netwerkbeveiligingsgroep, één openbaar IP-adres en één load balancer. De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Voor grote pools moet u mogelijk een verhoging van het quotum aanvragen voor een of meer van deze resources.

#### <a name="network-security-groups-batch-default"></a>Netwerkbeveiligingsgroepen Batch-standaardinstelling

Het subnet moet toestaan dat met binnenkomende communicatie van de Batch-service taken op de rekenknooppunten kunnen worden gepland, en toestaan dat uitgaande communicatie mogelijk is met Azure Storage of andere resources, zoals vereist door uw werkbelasting. Voor pools in de configuratie van de virtuele machine, voegt Batch netwerkbeveiligingsgroepen toe op het niveau van netwerkinterfaces (NIC's) die zijn gekoppeld aan rekenknooppunten. Deze NSG's zijn geconfigureerd met de volgende aanvullende regels:

* Binnenkomend TCP-verkeer op poorten 29876 en 29877 van IP-adressen van de Batch-service die overeenkomen met servicetag `BatchNodeManagement`.
* Binnenkomend TCP-verkeer op poort 22 (Linux-knooppunten) of poort 3389 (Windows-knooppunten) om externe toegang te verlenen. Voor bepaalde typen taken met meerdere instanties in Linux (zoals MPI), moet u ook verkeer via SSH-poort 22 toestaan voor IP-adressen in het subnet met de Batch-rekenknooppunten. Dit kan worden geblokkeerd per NSG-regels op subnetniveau (zie hieronder).
* Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk. Dit kan worden aangepast per NSG-regels op subnetniveau (zie hieronder).
* Uitgaand verkeer op een willekeurige poort naar internet. Dit kan worden aangepast per NSG-regels op subnetniveau (zie hieronder).

> [!IMPORTANT]
> Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als communicatie met de rekenknooppunten in het opgegeven subnet wordt geweigerd door een netwerkbeveiligingsgroep, zet de Batch-service de status van de rekenknooppunten op **Onbruikbaar**. Daarnaast mogen er geen resourcevergrendelingen worden toegepast op resources die door Batch zijn gemaakt. Dit kan er anders toe leiden dat het opschonen van resources als gevolg van door de gebruiker geïnitieerde acties, zoals het verwijderen van een pool, wordt voorkomen.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Netwerkbeveiligingsgroepen: Regels op subnetniveau opgeven

U hoeft geen NSG's op het subnet van het virtuele netwerk op te geven, omdat met Batch eigen NSG's worden geconfigureerd (zie hierboven). Als er een NSG is gekoppeld aan het subnet waarin Batch-rekenknooppunten zijn geïmplementeerd of als u aangepaste NSG-regels wilt toepassen om de standaardinstellingen te overschrijven, moet u deze NSG configureren met ten minste de beveiligingsregels voor binnenkomend en uitgaand verkeer, zoals weergegeven in de volgende tabellen.

Configureer binnenkomend verkeer op poort 3389 (Windows) of 22 (Linux) alleen als u externe toegang tot rekenknooppunten vanaf externe bronnen wilt toestaan. Mogelijk moet u regels voor poort 22 inschakelen op Linux als u ondersteuning nodig hebt voor taken met meerdere instanties met bepaalde MPI-runtimes. Het toestaan van verkeer op deze poorten is niet strikt vereist om de rekenknooppunten van de pool te kunnen gebruiken.

**Inkomende beveiligingsregels**

| IP-adressen van bron | Bronservicetag | Bronpoorten | Doel | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- | --- |
| N.v.t. | `BatchNodeManagement` [Servicetag](../articles/virtual-network/network-security-groups-overview.md#service-tags) (bij gebruik van een regionale variant in dezelfde regio als uw Batch-account) | * | Alle | 29876-29877 | TCP | Toestaan |
| IP-adressen van gebruikersbronnen voor het op afstand verkrijgen van toegang tot rekenknooppunten en/of het subnet van rekenknooppunten voor Linux-taken voor meerdere exemplaren, indien nodig. | N.v.t. | * | Alle | 3389 (Windows), 22 (Linux) | TCP | Toestaan |

> [!WARNING]
> De IP-adressen van de Batch-service kunnen na verloop van tijd worden gewijzigd. Daarom is het raadzaam om de `BatchNodeManagement` servicetag (of regionale variant) te gebruiken voor NSG-regels. Vermijd het vullen van NSG-regels met specifieke IP-adressen voor batchservices.

**Uitgaande beveiligingsregels**

| Bron | Bronpoorten | Doel | Doelservicetag | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- | --- |
| Alle | * | [Servicetag](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `Storage` (bij gebruik van een regionale variant in dezelfde regio als uw Batch-account) | 443 | TCP | Toestaan |

### <a name="pools-in-the-cloud-services-configuration"></a>Pools die zijn gemaakt in de Cloud Services-configuratie

**Ondersteunde VNets**: klassieke VNets

**Subnet-ID**: bij het opgeven van het subnet met behulp van de Batch-API's moet u de *resource-id* van het subnet gebruiken. De subnet-id heeft de vorm van:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**Machtigingen** - de `Microsoft Azure Batch`service-principal moet de `Classic Virtual Machine Contributor` Azure-rol hebben voor het betreffende VNet.

#### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Het subnet moet toestaan dat met binnenkomende communicatie van de Batch-service taken op de rekenknooppunten kunnen worden gepland, en toestaan dat uitgaande communicatie mogelijk is met Azure Storage of andere resources.

U hoeft geen netwerkbeveiligingsgroep op te geven omdat Batch binnenkomende communicatie alleen configureert van Batch IP-adressen naar de poolknooppunten. Als echter aan het opgegeven subnet netwerkbeveiligingsgroepen en/of een firewall zijn gekoppeld, configureert u de binnenkomende en uitgaande beveiligingsregels zoals wordt weergegeven in de volgende tabellen. Als communicatie met de rekenknooppunten in het opgegeven subnet wordt geweigerd door een netwerkbeveiligingsgroep, zet de Batch-service de status van de rekenknooppunten op **Onbruikbaar**.

Configureer binnenkomend verkeer op poort 3389 voor Windows alleen als u RDP-toegang tot poolknooppunten wilt toestaan. Dit is niet vereist om ervoor te zorgen dat de poolknooppunten kunnen worden gebruikt.

**Inkomende beveiligingsregels**

| IP-adressen van bron | Bronpoorten | Doel | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- |
Alle <br /><br />Hoewel dit in feite 'alles toestaan' vereist, past de Batch-service een regel voor toegangsbeheerlijsten toe op het niveau van elk knooppunt, die ervoor zorgt dat alle IP-adressen van niet-Batch-services worden uitgefilterd. | * | Alle | 10100, 20100, 30100 | TCP | Toestaan |
| Optioneel, voor het toestaan van RDP-toegang tot rekenknooppunten. | * | Alle | 3389 | TCP | Toestaan |

**Uitgaande beveiligingsregels**

| Bron | Bronpoorten | Doel | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- |
| Alle | * | Alle | 443  | Alle | Toestaan |