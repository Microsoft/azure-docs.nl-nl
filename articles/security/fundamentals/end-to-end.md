---
title: End-to-end beveiliging in azure | Microsoft Docs
description: Het artikel bevat een overzicht van Azure-Services die u helpen bij het beveiligen en beschermen van uw Cloud bronnen en het detecteren en onderzoeken van bedreigingen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310339"
---
# <a name="end-to-end-security-in-azure"></a>End-to-end beveiliging in azure
Een van de beste redenen om Azure te gebruiken voor uw toepassingen en services is om te profiteren van de uitgebreide reeks beveiligings Programma's en-mogelijkheden. Met deze hulpprogram ma's en mogelijkheden kunt u beveiligde oplossingen maken op het beveiligde Azure-platform. Microsoft Azure biedt vertrouwelijkheid, integriteit en beschik baarheid van klant gegevens, terwijl ook transparante verantwoording wordt ingeschakeld.

In het volgende diagram en de documentatie wordt u gekennismaken met de beveiligings Services in Azure. Deze beveiligings Services helpen u te voldoen aan de beveiligings behoeften van uw bedrijf en uw gebruikers, apparaten, resources, gegevens en toepassingen te beschermen in de Cloud.

## <a name="microsoft-security-services-map"></a>Micro soft Security Services-kaart

Het overzicht van Security Services organiseert Services door de resources die ze beveiligen (kolom). In het diagram worden services ook gegroepeerd in de volgende categorieën (rij):

- Services beveiligen en beveiligen, waarmee u een gelaagde, diep gaande strategie kunt implementeren voor identiteiten, hosts, netwerken en gegevens. Deze verzameling beveiligings Services en-mogelijkheden biedt een manier om uw beveiligings postuur in uw Azure-omgeving te begrijpen en te verbeteren.
- Detecteer bedreigingen: services die verdachte activiteiten identificeren en de dreiging kunnen beperken.
- Onderzoek en reageer – services die logboek registratie gegevens verzamelen zodat u een verdachte activiteit kunt beoordelen en reageren.

Het diagram bevat het Azure Security Bench Mark-programma, een verzameling essentiële beveiligings aanbevelingen die u kunt gebruiken om de services die u gebruikt in azure te beveiligen.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagram met end-to-end beveiligings Services in Azure." border="false":::

## <a name="security-controls-and-baselines"></a>Beveiligings besturings elementen en basis lijnen
Het [Azure Security Bench Mark](../benchmarks/introduction.md) -programma bevat een verzameling essentiële beveiligings aanbevelingen die u kunt gebruiken voor het beveiligen van de services die u in azure gebruikt:

- Beveiligings besturings elementen: deze aanbevelingen zijn in het algemeen van toepassing op uw Azure-Tenant en Azure-Services. Elke aanbeveling identificeert een lijst met belanghebbenden die doorgaans betrokken zijn bij het plannen, goed keuren of implementeren van de Bench Mark.
- Service basislijnen: Hiermee worden de besturings elementen toegepast op afzonderlijke Azure-Services om aanbevelingen te geven over de beveiligings configuratie van die service.

## <a name="secure-and-protect"></a>Beveiligen en beveiligen

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagram van de Azure-Services waarmee u uw Cloud bronnen kunt beveiligen en beveiligen." border="false":::

| Service | Beschrijving |
|------|--------|
| [Azure Security Center](../../security-center/security-center-introduction.md)| Een systeem voor de beveiliging van uniforme infra structuur dat het beveiligings postuur van uw data centers versterkt en een geavanceerde bedreigings beveiliging biedt in uw hybride werk belastingen in de Cloud, ongeacht of deze zich in azure bevinden of niet, en ook on-premises. |
| **Beheer van identiteits &nbsp; & &nbsp; toegang &nbsp;** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| De cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft.  |
|  | [Voorwaardelijke toegang](../../active-directory/conditional-access/overview.md) is het hulp programma dat door Azure AD wordt gebruikt om identiteits signalen samen te brengen, beslissingen te nemen en organisatie beleid af te dwingen. |
|  | [Domain Services](../../active-directory-domain-services/overview.md) is het hulp programma dat door Azure AD wordt gebruikt om beheerde domein services te bieden, zoals domein deelname, groeps beleid, LDAP (Lightweight Directory Access Protocol) en Kerberos/NTLM-verificatie. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) is een service in azure AD waarmee u de toegang tot belang rijke resources in uw organisatie kunt beheren, controleren en bewaken. |
|  | [Multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) is het hulp programma dat door Azure AD wordt gebruikt om de toegang tot gegevens en toepassingen te beveiligen door een tweede vorm van verificatie te vereisen. |
| [Azure AD-identiteitsbeveiliging](../../active-directory/identity-protection/overview-identity-protection.md) | Een hulp programma waarmee organisaties de detectie en het herstel van op identiteit gebaseerde Risico's kunnen automatiseren, Risico's voor de portal kunnen onderzoeken en gegevens van een risico detectie naar hulpprogram ma's van derden kunnen exporteren voor verdere analyse. |
| **Infrastructuur &nbsp; & &nbsp; netwerk** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Een virtuele netwerk gateway die wordt gebruikt voor het verzenden van versleuteld verkeer tussen een virtueel Azure-netwerk en een on-premises locatie via het open bare Internet en om versleuteld verkeer tussen virtuele Azure-netwerken te verzenden via het micro soft-netwerk. |
| [Azure DDoS Protection Standard](../../ddos-protection/ddos-protection-overview.md) | Biedt verbeterde DDoS-beperkings functies voor het beschermen tegen DDoS-aanvallen. Het wordt automatisch afgestemd om uw specifieke Azure-resources in een virtueel netwerk te beveiligen. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Een wereld wijd schaalbaar ingangs punt dat gebruikmaakt van het wereld wijde Edge-netwerk van micro soft om snelle, veilige en zeer schaal bare webtoepassingen te maken. |
| [Azure Firewall](../../firewall/overview.md) | Een beheerde, Cloud service voor netwerk beveiliging die uw Azure Virtual Network-Resources beveiligt. Het is een volledige stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Een beveiligde geheimen opslag voor tokens, wacht woorden, certificaten, API-sleutels en andere geheimen. Key Vault kan ook worden gebruikt voor het maken en beheren van de versleutelings sleutels die worden gebruikt voor het versleutelen van uw gegevens. |
| [Beheerde HSM Key Vault (preview-versie)](../../key-vault/managed-hsm/overview.md) | Een volledig beheerde, Maxi maal beschik bare, met standaarden compatibele Cloud service met één Tenant waarmee u cryptografische sleutels voor uw Cloud toepassingen kunt beveiligen met behulp van FIPS 140-2 level 3-gevalideerde Hsm's. |
| [Azure Private Link](../../private-link/private-link-overview.md) | Met kunt u toegang krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL Database) en Azure gehoste klant-eigendom/partner services via een persoonlijk eind punt in uw virtuele netwerk. |
| [Azure Application Gateway](../../application-gateway/overview.md) | Een geavanceerd webverkeer load balancer waarmee u verkeer naar uw webtoepassingen kunt beheren. Application Gateway kan routeringsbeslissingen nemen op basis van extra kenmerken van een HTTP-aanvraag, bijvoorbeeld URI-pad of hostheaders. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | Een volledig beheerde Enter prise Message Broker met berichten wachtrijen en onderwerpen over publiceren/abonneren. Service Bus wordt gebruikt voor het loskoppelen van toepassingen en services van elkaar. |
| [Web Application Firewall](../../web-application-firewall/overview.md) | Biedt gecentraliseerde beveiliging van uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen. WAF kan worden geïmplementeerd met Azure-toepassing gateway en Azure front-deur. |
| **Data &-toepassing** |  |
| [Azure Backup](../../backup/backup-overview.md) | Biedt eenvoudige, veilige en rendabele oplossingen om back-ups te maken van uw gegevens en deze te herstellen vanuit de Microsoft Azure Cloud. |
| [Versleuteling van Azure Storage-service](../../storage/common/storage-service-encryption.md) | Versleutelt gegevens automatisch voordat deze worden opgeslagen en ontsleutelt automatisch de gegevens wanneer u deze ophaalt. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | Een cloud-gebaseerde oplossing waarmee organisaties documenten en e-mail berichten kunnen detecteren, classificeren en beveiligen door labels op inhoud toe te passen. |
| [API Management](../../api-management/api-management-key-concepts.md) | Een manier om consistente en moderne API-gateways voor bestaande back-end-services te maken. |
| [Azure Confidential Computing](../../confidential-computing/overview.md) | Hiermee kunt u uw gevoelige gegevens isoleren terwijl deze worden verwerkt in de Cloud. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | Uw ontwikkelings projecten profiteren van meerdere lagen van beveiligings-en beheer technologieën, operationele prak tijken en nalevings beleid voor opslag in azure DevOps. |
| **Klant toegang** |  |
| [Externe Azure AD-identiteiten](../../active-directory/external-identities/compare-with-b2c.md) | Met externe identiteiten in Azure AD kunt u personen buiten uw organisatie toegang verlenen tot uw apps en resources, terwijl ze zich aanmelden met de identiteit van hun voorkeur. |
|  | U kunt uw apps en resources delen met externe gebruikers via [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md) -samen werking. |
|  | [Azure AD B2C](../../active-directory-b2c/overview.md) biedt u ondersteuning voor miljoenen gebruikers en miljarden authenticaties per dag, het bewaken en automatisch verwerken van bedreigingen zoals denial-of-service, wacht woord-spray of beveiligings aanvallen. |

## <a name="detect-threats"></a>Bedreigingen detecteren

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagram met de Azure-Services die bedreigingen detecteren." border="false":::

| Service | Beschrijving |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Biedt geavanceerde, intelligente en bescherming van uw Azure-en hybride bronnen en workloads. Het dash board van Azure Defender in Security Center biedt inzicht in en controle over de beveiliging van Cloud werkbelasting functies voor uw omgeving. |
| [Azure Sentinel](../../sentinel/overview.md) | Een schaal bare, Cloud-native, SIEM-oplossing (Security Information Event Management) en via (Security Orchestration Automated Response). Sentinel levert intelligente beveiligings analyses en bedreigings informatie over de hele onderneming, waardoor er één oplossing is voor waarschuwings detectie, zicht baarheid van bedreigingen, proactieve jacht en bedreigings reacties. |
| **Beheer van identiteits &nbsp; & &nbsp; toegang &nbsp;** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | Een Unified, pre-en post-inbreuk op ENTER prise verdediging die systeem eigen coördineert voor detectie, preventie, onderzoek en reacties op eind punten, identiteiten, e-mails en toepassingen om geïntegreerde beveiliging te bieden tegen geavanceerde aanvallen. |
|  | [Micro soft Defender voor eind punt](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) is een Enter prise Endpoint Security platform dat is ontworpen om bedrijfs netwerken te helpen bij het voor komen, detecteren, onderzoeken en reageren op geavanceerde bedreigingen. |
|  | [Micro soft Defender for Identity](https://docs.microsoft.com/defender-for-identity/what-is) is een cloud-gebaseerde beveiligings oplossing die gebruikmaakt van uw on-premises Active Directory signalen voor het identificeren, detecteren en onderzoeken van geavanceerde bedreigingen, gemanipuleerde identiteiten en schadelijke Insider-acties die zijn gericht op uw organisatie. |
| [Azure AD-identiteitsbeveiliging](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Verzendt twee typen e-mail berichten voor automatische meldingen om u te helpen bij het beheren van risico-en risico detecties van gebruikers: gebruikers die risico lopen per e-mail en wekelijks overzichts-e-mail. |
| **Infra structuur & netwerk** |  |
| [Azure Defender voor IoT](../../defender-for-iot/overview.md) | Een uniforme beveiligings oplossing voor het identificeren van IoT/OT-apparaten, beveiligings problemen en bedreigingen. Zo kunt u uw volledige IoT/OT-omgeving beveiligen, of u bestaande IoT/OT-apparaten wilt beveiligen of beveiliging wilt bouwen in nieuwe IoT-innovaties. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Voorziet in hulpprogram ma's voor het bewaken, vaststellen en weer geven van metrische gegevens en het in-of uitschakelen van Logboeken voor bronnen in een virtueel Azure-netwerk. Network Watcher is ontworpen om de netwerk status van IaaS-producten te controleren en te herstellen, zoals virtuele machines, virtuele netwerken, toepassings gateways en load balancers. |
| [Controle logboeken Azure Policy](../../governance/policy/overview.md) | Helpt organisatie standaarden af te dwingen en de naleving op schaal te beoordelen. Azure Policy gebruikt activiteiten logboeken, die automatisch worden ingeschakeld voor het insluiten van gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen. |
| **Data &-toepassing** |  |
| [Azure Defender voor containerregisters](../../security-center/defender-for-container-registries-introduction.md) | Bevat een scanner voor beveiligings problemen voor het scannen van de installatie kopieën in uw op Azure Resource Manager gebaseerde Azure Container Registry-registers en biedt diepere zicht baarheid in de beveiligings problemen van uw installatie kopieën. |
| [Azure Defender voor Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Biedt bedreigingen beveiliging op cluster niveau door uw door AKS beheerde services te bewaken via de logboeken die zijn opgehaald door Azure Kubernetes service (AKS). |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | Een Cloud Access Security Broker (CASB) die actief is op meerdere clouds. De oplossing biedt uitgebreide zichtbaarheid, controle over gegevenstrajecten en geavanceerde analyse om cyberbedreigingen in al uw cloudservices te identificeren en bestrijden. |

## <a name="investigate-and-respond"></a>Onderzoeken en reageren

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagram met de Azure-Services die u helpen bij het onderzoeken en reageren op bedreigingen." border="false":::

| Service | Beschrijving |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Krachtige zoek-en query hulpprogramma's voor het zoeken naar beveiligings Risico's in de gegevens bronnen van uw organisatie. |
| [&nbsp; &nbsp; Logboeken &nbsp; en &nbsp; metrische gegevens van Azure monitor](../../azure-monitor/overview.md) | Biedt een uitgebreide oplossing voor het verzamelen, analyseren en werken op telemetrie in uw Cloud-en on-premises omgevingen. Azure Monitor [verzamelt en](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) integreert gegevens uit verschillende bronnen in een gemeen schappelijk gegevens platform waar het kan worden gebruikt voor analyse, visualisatie en waarschuwingen. |
| **Beheer van identiteits &nbsp; & &nbsp; toegang &nbsp;** |  |
| [Azure &nbsp; ad- &nbsp; rapporten &nbsp; en- &nbsp; bewaking](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Azure AD-rapporten](../../active-directory/reports-monitoring/overview-reports.md) bieden een uitgebreid overzicht van de activiteiten in uw omgeving. |
|  | Met [Azure AD-controle](../../active-directory/reports-monitoring/overview-monitoring.md) kunt u uw Azure AD-activiteiten logboeken naar verschillende eind punten routeren.|
| [Controle geschiedenis van Azure AD PIM](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Toont alle roltoewijzingen en activeringen in de afgelopen 30 dagen voor alle geprivilegieerde rollen. |
| **Data &-toepassing** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Voorziet in hulpprogram ma's om een beter inzicht te krijgen in wat er in uw cloud omgeving gebeurt. |

## <a name="next-steps"></a>Volgende stappen

- Begrijp uw [gedeelde verantwoordelijkheid in de Cloud](shared-responsibility.md).

- Inzicht [in de isolatie opties in de Azure-Cloud](isolation-choices.md) op zowel kwaad aardige als niet-kwaadwillende gebruikers.