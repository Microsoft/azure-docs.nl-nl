---
title: Azure Security Bench Mark v2-netwerk beveiliging
description: Azure Security Bench Mark v2-netwerk beveiliging
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b47f833a3b47dae145aa18eb6eda0c6bd95f6689
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042910"
---
# <a name="security-control-v2-network-security"></a>Beveiligings controle v2: netwerk beveiliging

Netwerk beveiliging heeft betrekking op besturings elementen voor het beveiligen en beveiligen van Azure-netwerken. Dit omvat het beveiligen van virtuele netwerken, het tot stand brengen van particuliere verbindingen, het voor komen en beperken van externe aanvallen en het beveiligen van DNS.

Voor een overzicht van de toepasselijke ingebouwde Azure Policy raadpleegt u [de details van het ingebouwde initiatief Azure Security Bench Mark compliance compliantie: netwerk beveiliging](../../governance/policy/samples/azure-security-benchmark.md#network-security)

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| NS-1 | 9,2, 9,4, 14,1, 14,2, 14,3 | AC-4, CA-3, SC-7 |

Zorg ervoor dat alle virtuele netwerken van Azure een bedrijfs segmentatie principe volgen die afstemt op de bedrijfs Risico's. Elk systeem dat een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtueel netwerk en voldoende is beveiligd met een netwerk beveiligings groep (NSG) en/of Azure Firewall.

Op basis van uw toepassingen en de segmentatie strategie van een onderneming kunt u verkeer beperken of toestaan tussen interne bronnen op basis van de regels voor de netwerk beveiligings groep. Voor specifieke, goed gedefinieerde toepassingen (zoals een app met drie lagen) kan dit een zeer veilige ' weigeren standaard, met uitzonde ring toestaan ' zijn. Dit kan niet goed worden geschaald als er veel toepassingen en eind punten met elkaar communiceren. U kunt Azure Firewall ook gebruiken in omstandigheden waarbij centraal beheer is vereist voor een groot aantal ondernemings segmenten of spokes (in een hub/spoke-topologie).

Gebruik Azure Security Center adaptieve netwerk beveiliging voor het aanbevelen van configuraties voor netwerk beveiligings groepen die poort-en bron-Ip's beperken op basis van de verwijzing naar extern netwerk verkeer regels.

Gebruik Azure Sentinel om het gebruik van verouderde onveilige protocollen, zoals SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, niet-ondertekende LDAP-bindingen en zwakke versleuteling in Kerberos, te detecteren.

- [Een netwerk beveiligings groep met beveiligings regels maken](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall implementeren en configureren](../../firewall/tutorial-firewall-deploy-portal.md)

- [Adaptieve netwerk beveiliging in Azure Security Center](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Sentinel Inveilige protocollen werkmap](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| NS-2 | N.v.t. | CA-3, AC-17, MA-4 |

Gebruik Azure ExpressRoute of het virtuele particuliere netwerk (VPN) van Azure om particuliere verbindingen te maken tussen Azure-data centers en on-premises infra structuur in een co-locatie omgeving. ExpressRoute-verbindingen gaan niet via het open bare Internet en bieden meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. Voor punt-naar-site-VPN en site-naar-site-VPN kunt u on-premises apparaten of netwerken verbinden met een virtueel netwerk met behulp van een combi natie van deze VPN-opties en Azure ExpressRoute.

Als u twee of meer virtuele netwerken in azure met elkaar wilt verbinden, gebruikt u peering op het virtuele netwerk of een privé koppeling. Netwerk verkeer tussen gekoppelde virtuele netwerken is privé en wordt opgeslagen in het Azure-backbone-netwerk.

- [Wat zijn de ExpressRoute-connectiviteits modellen?](../../expressroute/expressroute-connectivity-models.md)

- [Overzicht van Azure VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering op virtueel netwerk](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Toegang tot Azure-services via particulier netwerk tot stand brengen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| NS-3 | 14.1 | AC-4, CA-3, SC-7 |

Gebruik persoonlijke Azure-koppelingen om persoonlijke toegang tot Azure-Services in te scha kelen vanuit uw virtuele netwerken, zonder het Internet te passeren. Gebruik Azure Virtual Network Service-eind punten als Azure private link nog niet beschikbaar is. Azure Virtual Network Service-eind punten bieden beveiligde toegang tot services via een geoptimaliseerde route via het Azure-backbone-netwerk.

Persoonlijke toegang is een extra verdedigings maatregel naast verificatie en verkeers beveiliging die wordt aangeboden door Azure-Services.

- [Persoonlijke Azure-koppeling](../../private-link/private-link-overview.md)

- [Informatie over Virtual Network Service-eind punten](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| NS-4 | 9,5, 12,3, 12,9 | SC-5, SC-7 |

Beveilig Azure-bronnen tegen aanvallen van externe netwerken, waaronder DDoS-aanvallen (Distributed Denial of service), toepassingsspecifieke aanvallen, en ongevraagd en mogelijk schadelijk Internet verkeer. Azure bevat systeem eigen mogelijkheden voor dit:
-   Gebruik Azure Firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. 

-   Gebruik WAF-mogelijkheden (Web Application firewall) in Azure-toepassing gateway, Azure front-deur en Azure Content Delivery Network (CDN) om uw toepassingen, services en Api's te beschermen tegen aanvallen via de toepassingslaag.

-   Bescherm uw assets tegen DDoS-aanvallen door DDoS Standard-beveiliging in te scha kelen op uw virtuele Azure-netwerken.
-   Gebruik Azure Security Center om onjuiste configuratie Risico's te detecteren die zijn gerelateerd aan de bovenstaande.

- [Documentatie over Azure Firewall](../../firewall/index.yml)

- [Azure WAF implementeren](../../web-application-firewall/overview.md)

- [Azure DDoS Protection Standard beheren met de Azure Portal](../../ddos-protection/manage-ddos-protection.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| NS-5 | 12,6, 12,7 | SI-4 |

Gebruik Azure Firewall op bedreigingen gebaseerd filteren om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. Wanneer Payload-inspectie vereist is, kunt u een externe detectie/indringings systeem (ID'S/IP-adressen) van derden implementeren in azure Marketplace met Payload-inspectie mogelijkheden. U kunt ook op host gebaseerde ID'S/IP-adressen of een EDR-oplossing op basis van een host detecteren in combi natie met of in plaats van op het netwerk gebaseerde ID'S/IP-adressen.

Opmerking: als u een wettelijke of andere vereiste hebt voor het gebruik van ID'S/IP'S, moet u ervoor zorgen dat deze altijd is afgestemd op waarschuwingen met hoge kwaliteit voor uw SIEM-oplossing. 

- [Azure Firewall implementeren](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace bevat mogelijkheden van derden-ID'S](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Micro soft Defender voor endpoint-mogelijkheid](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

Vereenvoudig de netwerk beveiligings regels door gebruik te maken van service tags en toepassings beveiligings groepen (Asg's).

Gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de servicetag naam op te geven in het bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

U kunt ook toepassings beveiligings groepen gebruiken om complexe beveiligings configuratie te vereenvoudigen. In plaats van beleid te definiëren op basis van expliciete IP-adressen in netwerk beveiligings groepen, kunt u met toepassings beveiligings groepen netwerk beveiliging configureren als een natuurlijke uitbrei ding van de structuur van een toepassing, zodat u virtuele machines kunt groeperen en netwerk beveiligings beleid kunt definiëren op basis van die groepen.

- [Service Tags begrijpen en gebruiken](../../virtual-network/service-tags-overview.md)

- [Toepassings beveiligings groepen begrijpen en gebruiken](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: secure Domain Name Service (DNS)

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| NS-7 | N.v.t. | SC-20, SC-21 |

Volg de aanbevolen procedures voor DNS-beveiliging om te beperken tegen veelvoorkomende aanvallen zoals Dangling DNS, DNS-versterkingen, DNS-vergiftiging en spoofing, enzovoort.

Als Azure DNS als gezaghebbende DNS-service wordt gebruikt, moet u ervoor zorgen dat DNS-zones en-records worden beveiligd tegen onbedoelde of schadelijke wijzigingen met behulp van Azure RBAC en resource vergrendelingen.

- [Overzicht van Azure DNS](../../dns/dns-overview.md)

- [Implementatie handleiding voor beveiligde Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [Dangling DNS-vermeldingen voor komen en de overname van subdomeinen voor komen](../fundamentals/subdomain-takeover.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
