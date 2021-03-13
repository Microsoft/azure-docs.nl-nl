---
title: Voor beeld van Azure Security Bench Mark Foundation blauw druk-overzicht
description: Overzicht en architectuur van het voor beeld van Azure Security Bench Mark Foundation Blue.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 9630915328b430c409c48e13e0d22f64dbcc99ea
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232066"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Overzicht van het voor beeld van Azure Security Bench Mark Foundation blauw druk

Het voor beeld van Azure Security Bench Mark Foundation Blue bevat een reeks patronen in de basislijn infrastructuur waarmee u een veilige en compatibele Azure-omgeving kunt bouwen. De blauw druk helpt u bij het implementeren van een Cloud architectuur die oplossingen biedt aan scenario's die betrekking hebben op accreditatie-of nalevings vereisten. Deze voor beeld van een basis-blauw druk is een uitbrei ding van de [blauw druk van Azure Security Bench Mark](../azure-security-benchmark.md). Het implementeert en configureert netwerk grenzen, bewaking en andere resources in uitlijning met het beleid en andere Guardrails die zijn gedefinieerd in de [Azure Security-benchmark](../../../../security/benchmarks/index.yml)waarde.

## <a name="architecture"></a>Architectuur

De basis omgeving die is gemaakt met dit blauw druk-voor beeld is gebaseerd op de architectuur-principals van een [hub-en spoke-model](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
De blauw druk implementeert een hub-virtueel netwerk dat algemene en gedeelde bronnen, services en artefacten bevat, zoals Azure Bastion, gateway en Firewall voor connectiviteits-, beheer-en Jump Box-subnetten om extra/optioneel beheer, onderhoud, beheer en connectiviteits infrastructuur te hosten. Een of meer spoke-virtuele netwerken zijn geïmplementeerd voor het hosten van toepassings werkbelastingen, zoals web-en database services. Spoke-virtuele netwerken zijn verbonden met het virtuele netwerk van de hub met behulp van de peering van het virtuele netwerk van Azure voor naadloze en veilige connectiviteit. U kunt extra spoken toevoegen door de voor beeld-blauw drukken opnieuw toe te wijzen of hand matig een virtueel Azure-netwerk te maken en dit te koppelen aan het virtuele netwerk van de hub. Alle externe verbinding met het spoke-netwerk (en) en subnet (en) is geconfigureerd om te routeren via het virtuele netwerk van de hub en, via firewall-, gateway-en beheer Jump boxen.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Diagram voor beeld van Azure Security Bench Mark Foundation Blue-voorbeeld architectuur" border="false":::

Met deze blauw druk worden verschillende Azure-Services geïmplementeerd om een beveiligde, bewaakte, bedrijfs klare basis te bieden. De omgeving bestaat uit de volgende elementen:

- [Azure monitor logboeken](../../../../azure-monitor/logs/data-platform-logs.md) en een Azure Storage-account om ervoor te zorgen dat bron logboeken, activiteiten logboeken, metrische gegevens en netwerk verkeer stromen worden opgeslagen op een centrale locatie voor eenvoudige query's, analyses, archivering en waarschuwingen.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (standaard versie) om beveiliging tegen bedreigingen te bieden voor Azure-resources.
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) in de hub ondersteunen subnetten om een verbinding te maken met een on-premises netwerk, een ingang en uitgangs stack naar/voor Internet connectiviteit en optionele subnetten voor de implementatie van extra administratieve of beheer Services. Virtual Network in de spoke bevat subnetten voor het hosten van toepassings werkbelastingen. Extra subnetten kunnen worden gemaakt na de implementatie naar behoefte ter ondersteuning van toepasselijke scenario's.
- [Azure firewall](../../../../firewall/overview.md) alle uitgaande Internet verkeer te routeren en inkomend Internet verkeer via Jump box in te scha kelen. (Standaard firewall regels blok keren het binnenkomende en uitgaande verkeer van Internet en regels moeten na de implementatie worden geconfigureerd, indien van toepassing.)
- [Netwerk beveiligings groepen](../../../../virtual-network/network-security-group-how-it-works.md) (nsg's) die zijn toegewezen aan alle subnetten (met uitzonde ring van subnetten die eigendom zijn van de service, zoals Azure Bastion, Gateway en Azure firewall), zijn geconfigureerd om al het binnenkomende en uitgaande verkeer van Internet te blok keren.
- [Toepassings beveiligings groepen](../../../../virtual-network/application-security-groups.md) om het groeperen van virtuele machines van Azure in te scha kelen voor het Toep assen van een gemeen schappelijk netwerk beveiligings beleid.
- [Route tabellen](../../../../virtual-network/manage-route-table.md) om al het uitgaande Internet verkeer vanuit subnetten via de firewall te routeren. (Azure Firewall-en NSG-regels moeten na de implementatie worden geconfigureerd om de connectiviteit te openen.)
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) voor het bewaken, diagnosticeren en weer geven van metrische resources in het virtuele Azure-netwerk.
- [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md) om Azure-resources te beschermen tegen DDoS-aanvallen.
- [Azure Bastion](../../../../bastion/bastion-overview.md) om naadloze en veilige connectiviteit te bieden voor een virtuele machine waarvoor geen openbaar IP-adres, een agent of speciale client software nodig is.
- [Azure VPN gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) om versleuteld verkeer in te scha kelen tussen een virtueel Azure-netwerk en een on-premises locatie via het open bare Internet.

> [!NOTE] 
> De Azure Security Bench Mark Foundation bevat een basis architectuur voor werk belastingen. Het architectuur diagram hierboven bevat verschillende theoretische bronnen om het mogelijke gebruik van subnetten te demonstreren. U moet nog steeds werk belastingen implementeren op deze basis architectuur.

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van het voor beeld van Azure Security Bench Mark Foundation Blue gecontroleerd.

> [!div class="nextstepaction"]
> [Azure Security Bench Mark Foundation-blauw druk-stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).