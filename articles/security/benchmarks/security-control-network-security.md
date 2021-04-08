---
title: Beveiligings beheer van Azure-netwerk beveiliging
description: Azure Security Control-netwerk beveiliging
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cc52d1f6eec5f1aedb0db37b3945f7be6d9f62a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595633"
---
# <a name="security-control-network-security"></a>Beveiligings beheer: netwerk beveiliging

De aanbevelingen voor netwerk beveiliging richten zich op het opgeven van de netwerk protocollen, TCP/UDP-poorten en verbonden netwerk services zijn toegestaan of geweigerd toegang tot Azure-Services.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Klant |

Zorg ervoor dat alle implementaties van Virtual Network subnet een netwerk beveiligings groep hebben die is toegepast op de vertrouwde poorten en bronnen van uw toepassing. Gebruik, indien beschikbaar, persoonlijke eind punten met persoonlijke koppeling om uw Azure-service resources te beveiligen met uw virtuele netwerk door de VNet-identiteit voor de service uit te breiden. Wanneer persoonlijke eind punten en persoonlijke koppeling niet beschikbaar zijn, gebruikt u service-eind punten. Raadpleeg voor specifieke vereisten voor de service de beveiligings aanbeveling voor die specifieke service. 

Als u een specifieke gebruiks voorbeeld hebt, kan aan de vereiste worden voldaan door de implementatie van Azure Firewall.

- [Informatie over Virtual Network Service-eind punten](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Persoonlijke Azure-koppeling](../../private-link/private-link-overview.md)

- [Een Virtual Network maken](../../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall implementeren en configureren](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Klant |

Gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om uw netwerk bronnen in azure te beveiligen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.3 | 9.5 | Klant |

Implementeer Azure Web Application firewall (WAF) voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Schakel de diagnostische instelling voor WAF-en opname Logboeken in een opslag account, Event hub of Log Analytics werk ruimte in.

- [Azure WAF implementeren](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.4 | 12,3 | Klant |

Schakel DDoS Standard Protection in op uw virtuele Azure-netwerken om tegen DDoS-aanvallen te beschermen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende schadelijke IP-adressen te weigeren.

Implementeer Azure Firewall op elke netwerk grenzen van de organisatie met behulp van bedreigings informatie en geconfigureerd voor ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer.

Gebruik Azure Security Center just-in-time-netwerk toegang om Nsg's te configureren om de bloot stelling van eind punten te beperken tot goedgekeurde IP-adressen gedurende een beperkte periode.

Gebruik Azure Security Center adaptieve netwerk beveiliging om NSG-configuraties aan te bevelen die poorten en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie.

- [DDoS-beveiliging configureren](../../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall implementeren](../../firewall/tutorial-firewall-deploy-portal.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../../security-center/azure-defender.md)

- [Meer informatie over Azure Security Center adaptieve netwerk beveiliging](../../security-center/security-center-adaptive-network-hardening.md)

- [Meer informatie over Azure Security Center just-in-time-netwerk Access Control](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.5 | 12,5 | Klant |

Schakel Network Watcher pakket opname in om afwijkende activiteiten te onderzoeken.

- [Network Watcher inschakelen](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.6 | 12,6, 12,7 | Klant |

Selecteer een aanbieding op de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen met Payload-inspectie mogelijkheden.  Als inbraak detectie en/of preventie op basis van Payload-inspectie geen vereiste is, kan Azure Firewall met bedreigings informatie worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren kan verkeer van en naar bekende schadelijke IP-adressen en domeinen Signa lering en weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te weigeren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](../../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1,7 | 12,9, 12,10 | Klant |

Implementeer Azure-toepassing gateway voor webtoepassingen met HTTPS/TLS ingeschakeld voor vertrouwde certificaten.

- [Application Gateway implementeren](../../application-gateway/quick-create-portal.md)

- [Application Gateway configureren voor het gebruik van HTTPS](../../application-gateway/create-ssl-portal.md)

- [De taak verdeling van laag 7 met Azure Web Application-gateways begrijpen](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.8 | 1.5 | Klant |

Gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

U kunt ook toepassings beveiligings groepen gebruiken om complexe beveiligings configuratie te vereenvoudigen. Met behulp van toepassingsbeveiligingsgroepen kunt u netwerkbeveiliging configureren als een natuurlijk verlengstuk van de structuur van een toepassing, waarbij u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van die groepen.

- [Service Tags begrijpen en gebruiken](../../virtual-network/service-tags-overview.md)

- [Toepassings beveiligings groepen begrijpen en gebruiken](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1,9 | 11,1 | Klant |

Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen met Azure Policy.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door het verpakken van sleutel omgevings artefacten, zoals Azure Resources Manager-sjablonen, Azure RBAC-besturings elementen en-beleid, in één definitie van blauw drukken. U kunt de blauw druk Toep assen op nieuwe abonnementen en het beheer en beheer verfijnen met behulp van versies.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Voor beelden Azure Policy voor netwerken](../../governance/policy/samples/built-in-policies.md#network)

- [Een Azure Blueprint maken](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1,10 | 11,2 | Klant |

Gebruik labels voor Nsg's en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network maken](../../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.11 | 11.3 | Klant |

Gebruik Azure-activiteiten logboek om resource configuraties te bewaken en wijzigingen in uw Azure-resources te detecteren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke resources plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: [logboek registratie en controle](security-control-logging-monitoring.md)