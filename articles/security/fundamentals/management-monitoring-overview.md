---
title: Beveiligings functies voor beheer en bewaking-Microsoft Azure | Microsoft Docs
description: Dit artikel bevat een overzicht van de beveiligings functies en-services die Azure biedt om u te helpen bij het beheer en de bewaking van Azure-Cloud Services en virtuele machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: f87ea1e1c9f43de4e9e0f94d1cd855615a0a880c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712042"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Overzicht van Azure-beveiligings beheer en-bewaking
Dit artikel bevat een overzicht van de beveiligings functies en-services die Azure biedt om u te helpen bij het beheer en de bewaking van Azure-Cloud Services en virtuele machines.

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) biedt gedetailleerde toegang voor Azure-resources. Met behulp van Azure RBAC kunt u alleen mensen de benodigde toegangs rechten geven om hun taken uit te voeren. Met Azure RBAC kunt u er ook voor zorgen dat wanneer mensen de organisatie verlaten, de toegang tot resources in de Cloud verloren gaat.

Meer informatie:

* [Active Directory team blog op Azure RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Met Azure kunt u antimalware-software gebruiken van belang rijke leveranciers van beveiliging, zoals micro soft, Symantec, Trend Micro, McAfee en Kaspersky. Deze software helpt bij het beschermen van uw virtuele machines tegen schadelijke bestanden, adware en andere bedreigingen.

Micro soft antimalware voor Azure Cloud Services en Virtual Machines biedt u de mogelijkheid om een antimalware-agent te installeren voor zowel PaaS-als virtuele machines. Op basis van System Center Endpoint Protection biedt deze functie een on-premises beveiligings technologie in de Cloud.

We bieden ook diep gaande integratie voor de [diepe beveiligings](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) -en [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) -producten van de trend in het Azure-platform. Grondige beveiliging is een antivirus oplossing en SecureCloud is een versleutelings oplossing. Uitgebreide beveiliging wordt binnen Vm's geïmplementeerd via een extensie model. Met behulp van de Azure Portal gebruikers interface en Power shell kunt u ervoor kiezen om diepe beveiliging te gebruiken in nieuwe Vm's die worden geïnstalleerd of bestaande Vm's die al zijn geïmplementeerd.

Symantec Endpoint Protection (SEP) wordt ook ondersteund op Azure. Via Portal-integratie kunt u opgeven dat u SEP wilt gebruiken op een virtuele machine. SEP kan worden geïnstalleerd op een nieuwe virtuele machine via de Azure Portal of kan worden geïnstalleerd op een bestaande virtuele machine via Power shell.

Meer informatie:

* [Deploying Antimalware Solutions on Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/) (Antimalware-oplossingen implementeren op virtuele machines van Azure)
* [Micro soft antimalware voor Azure Cloud Services en Virtual Machines](antimalware.md)
* [Trend Micro diepe Security als een service op een Windows-VM installeren en configureren](/previous-versions/azure/virtual-machines/extensions/trend)
* [Symantec-Endpoint Protection installeren en configureren op een Windows-VM](../../virtual-machines/extensions/symantec.md)
* [Nieuwe antimalware-opties voor het beveiligen van Azure Virtual Machines](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication is een verificatie methode die het gebruik van meer dan één verificatie methode vereist. Er wordt een kritieke tweede beveiligingslaag toegevoegd aan gebruikers aanmeldingen en trans acties.

Multi-Factor Authentication helpt de toegang tot gegevens en toepassingen te beschermen terwijl de vraag naar gebruikers wordt gevergaderd voor een eenvoudig aanmeldings proces. Het biedt sterke authenticatie via een reeks verificatie opties (telefoon oproep, tekst bericht of mobiele app-melding of verificatie code) en OATH-tokens van derden.

Meer informatie:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Wat is Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Hoe Azure AD Multi-Factor Authentication werkt](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

U kunt Azure ExpressRoute gebruiken om uw on-premises netwerken uit te breiden naar de Microsoft Cloud via een speciale particuliere verbinding die wordt vereenvoudigd door een connectiviteits provider. Met ExpressRoute kunt u verbindingen tot stand brengen met micro soft-Cloud Services, zoals Azure, Microsoft 365 en CRM Online. Connectiviteit kan afkomstig zijn van:

* Een wille keurig (IP VPN)-netwerk.
* Een Point-to-Point Ethernet-netwerk.
* Een virtuele cross-verbinding via een connectiviteits provider op een co-locatie-faciliteit.

ExpressRoute-verbindingen gaan niet via het open bare Internet. Ze kunnen meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging bieden dan typische verbindingen via internet.

Meer informatie:

* [Technisch overzicht van ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuele netwerkgateways

VPN-gateways, ook wel virtuele Azure-netwerk gateways genoemd, worden gebruikt voor het verzenden van netwerk verkeer tussen virtuele netwerken en on-premises locaties. Ze worden ook gebruikt voor het verzenden van verkeer tussen meerdere virtuele netwerken in azure (netwerk naar netwerk). VPN-gateways bieden een beveiligde cross-premises connectiviteit tussen Azure en uw infra structuur.

Meer informatie:

* [Over VPN-gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Overzicht van Azure-netwerkbeveiliging](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Soms moeten gebruikers geprivilegieerde bewerkingen uitvoeren in azure-resources of andere SaaS-toepassingen. Dit betekent vaak dat organisaties hen permanente privileged Access bieden in Azure Active Directory (Azure AD).

Dit is een groeiend beveiligings risico voor resources die in de cloud worden gehost, omdat organisaties niet voldoende kunnen controleren wat deze gebruikers doen met hun bevoegde toegang. Als een gebruikers account met bevoorrechte toegang is aangetast, kan dit ook van invloed zijn op de algehele Cloud beveiliging van de organisatie. Azure AD Privileged Identity Management helpt dit risico op te lossen door de blootstellings tijd van bevoegdheden te verlagen en de zicht baarheid van het gebruik te verg Roten.  

Privileged Identity Management introduceert het concept van een tijdelijke beheerder voor een rol of ' just-in-time ' beheerders toegang. Dit type beheerder is een gebruiker die een activerings proces voor die toegewezen rol moet volt ooien. Met het activerings proces wordt de toewijzing van de gebruiker aan een rol in azure AD gewijzigd van inactief in actief, gedurende een opgegeven periode.

Meer informatie:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Aan de slag met Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identiteitsbeveiliging

Azure AD Identity Protection biedt een geconsolideerde weer gave van verdachte aanmeldings activiteiten en mogelijke beveiligings problemen om uw bedrijf te helpen beveiligen. Identiteits beveiliging detecteert verdachte activiteiten voor gebruikers en geprivilegieerde identiteiten (beheerders), op basis van signalen zoals:

* Aanvallen tegen brute kracht.
* Gelekte referenties.
* Aanmeldingen vanaf onbekende locaties en geïnfecteerde apparaten.

Door meldingen en aanbevolen herstel te bieden, helpt identiteits beveiliging de Risico's in realtime te verminderen. De risico ernst van de gebruiker wordt berekend. U kunt beleids regels op basis van Risico's configureren om automatisch de toegang tot toepassingen te helpen beschermen tegen toekomstige bedreigingen.

Meer informatie:

* [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Channel 9: Azure AD en identiteits weergave: preview van identiteits beveiliging](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Beveiligingscentrum

Azure Security Center helpt u bedreigingen te voorkomen, te detecteren en erop te reageren. Security Center biedt u meer inzicht in en controle over de beveiliging van uw Azure-resources, evenals die in uw hybride cloud omgeving. 

Security Center voert doorlopende beveiligings evaluaties van uw verbonden bronnen uit en vergelijkt de configuratie en implementatie van de [Azure Security-Bench Mark](../benchmarks/introduction.md) om gedetailleerde beveiligings aanbevelingen te bieden die zijn afgestemd op uw omgeving.

Security Center helpt u de beveiliging van uw Azure-resources te optimaliseren en te controleren door:

- U kunt beleids regels voor uw Azure-abonnements resources definiëren op basis van:
    - De beveiligings behoeften van uw organisatie.
    - Het type toepassingen of de gevoeligheid van de gegevens in elk abonnement.
    - Industrie-of regelgevings normen of benchmarks die u op uw abonnementen toepast. 
- Bewaken van de status van uw virtuele machines, netwerken en toepassingen van Azure.
- Bieden van een lijst met beveiligings waarschuwingen met prioriteit, inclusief waarschuwingen van geïntegreerde partner oplossingen. Het bevat ook de informatie die u nodig hebt om snel een aanval te onderzoeken en aanbevelingen voor het oplossen ervan.

Meer informatie:

* [Inleiding tot Azure Security Center](../../security-center/security-center-introduction.md)
* [Verbeter uw beveiligde Score in Azure Security Center](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph biedt real-time bedreigings beveiliging in micro soft-producten en-services. Er wordt gebruikgemaakt van geavanceerde analyses waarmee een enorme hoeveelheid bedreigings informatie en beveiligings gegevens worden gekoppeld om inzicht te krijgen in de beveiliging van de organisatie. Micro soft maakt gebruik van geavanceerde analyses: verwerking van meer dan 450.000.000.000 authenticaties per maand, scannen van 400.000.000.000-e-mail berichten voor malware en phishing en het bijwerken van 1.000.000.000 apparaten — om uitgebreidere inzichten te leveren. Deze inzichten kunnen uw organisatie helpen aanvallen snel te detecteren en erop te reageren.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [gedeelde verantwoordelijkheids model](shared-responsibility.md) en de beveiligings taken die door micro soft worden verwerkt en welke taken door u worden uitgevoerd.

Zie voor meer informatie over beveiligings beheer [beveiligings beheer in azure](management.md).