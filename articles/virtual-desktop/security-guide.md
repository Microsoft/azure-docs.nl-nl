---
title: Aanbevolen procedures voor de beveiliging van virtuele Windows-Bureau bladen-Azure
description: Aanbevolen procedures voor het beveiligen van uw virtuele Windows-bureau blad-omgeving.
author: heidilohr
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc39c1e43580c5cf302e85664783dba4509889af
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842631"
---
# <a name="security-best-practices"></a>Aanbevolen procedures voor beveiliging

Virtueel bureau blad van Windows is een beheerde virtueel bureau blad-service met veel beveiligings mogelijkheden om uw organisatie veilig te houden. In een Windows-implementatie op virtueel bureau blad beheert micro soft delen van de services namens de klant. De service heeft veel ingebouwde geavanceerde beveiligings functies, zoals reverse Connect, waarmee het risico wordt gereduceerd waarbij externe Bureau bladen toegankelijk zijn vanaf elke locatie.

In dit artikel worden extra stappen beschreven die u als beheerder kunt gebruiken om de Windows-implementaties van virtuele Bureau bladen van uw klanten te beveiligen.

## <a name="security-responsibilities"></a>Beveiligings verantwoordelijkheden

Waarbij Cloud Services afwijkt van de traditionele on-premises Virtual Desktop Infrastructures (VDIs), is hoe ze beveiligings verantwoordelijkheden verwerken. Een voor beeld: in een traditionele on-premises VDI is de klant verantwoordelijk voor alle aspecten van de beveiliging. In de meeste Cloud Services worden deze verantwoordelijkheden echter gedeeld tussen de klant en het bedrijf.

Wanneer u Windows virtueel bureau blad gebruikt, is het belang rijk om te begrijpen dat hoewel sommige onderdelen al zijn beveiligd voor uw omgeving, u andere gebieden zelf moet configureren om te voldoen aan de beveiligings behoeften van uw organisatie.

Hier vindt u de beveiligings behoeften die u nodig hebt voor de implementatie van uw Windows-virtueel bureau blad:

| Beveiligings behoeften | Is de klant hiervoor verantwoordelijk? |
|---------------|:-------------------------:|
|Identiteit|Yes|
|Gebruikers apparaten (Mobile en PC)|Yes|
|App-beveiliging|Yes|
|Session Host-besturings systeem|Yes|
|Implementatie configuratie|Yes|
|Netwerk besturings elementen|Yes|
|Besturings vlak voor virtualisatie|Nee|
|Fysieke hosts|Nee|
|Fysiek netwerk|Nee|
|Fysiek Data Center|Nee|

De beveiligings behoeften die de klant niet verantwoordelijk is voor worden verwerkt door micro soft.

## <a name="azure-security-best-practices"></a>Best practices voor Azure-beveiliging

Virtueel bureau blad van Windows is een service onder Azure. Als u de beveiliging van uw Windows-implementatie voor virtueel bureau blad wilt maximaliseren, moet u er ook voor zorgen dat u de omliggende Azure-infra structuur en het beheer vlak beveiligt. Als u uw infra structuur wilt beveiligen, moet u rekening houden met de manier waarop Windows virtueel bureau blad aansluit op uw grotere Azure-ecosysteem. Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](../security/fundamentals/best-practices-and-patterns.md)voor meer informatie over het Azure-ecosysteem.

In deze sectie worden aanbevolen procedures beschreven voor het beveiligen van uw Azure-ecosysteem.

### <a name="enable-azure-security-center"></a>Azure Security Center inschakelen

U wordt aangeraden Azure Security Center Standard in te scha kelen voor abonnementen, virtuele machines, sleutel kluizen en opslag accounts.

Met Azure Security Center Standard kunt u het volgende doen:

* Beveiligings problemen beheren.
* Naleving van algemene frameworks zoals PCI evalueren.
* De algehele beveiliging van uw omgeving versterken.

Zie [uw Azure-abonnement op Security Center Standard voorbereiden](../security-center/security-center-get-started.md)voor meer informatie.

### <a name="improve-your-secure-score"></a>Uw veiligheidsscore verbeteren

Een beveiligde Score biedt aanbevelingen en best practice advies voor het verbeteren van de beveiliging van uw organisatie. Aan de hand van deze aanbevelingen kunt u kiezen welke u het belangrijkst vindt, en met de opties voor snel oplossen kunt u snel potentiële beveiligings problemen oplossen. Deze aanbevelingen worden ook na verloop van tijd bijgewerkt, zodat u altijd op de hoogte bent van de beste manieren om de beveiliging van uw omgeving te hand haven. Zie [uw beveiligde Score verbeteren in azure Security Center](../security-center/security-center-secure-score.md)voor meer informatie.

## <a name="windows-virtual-desktop-security-best-practices"></a>Aanbevolen procedures voor de beveiliging van Windows Virtual Desktop

Virtueel bureau blad van Windows heeft veel ingebouwde beveiligings controles. In deze sectie vindt u meer informatie over beveiligings controles waarmee u uw gebruikers en gegevens veilig kunt blijven gebruiken.

### <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication vereisen

Als u multi-factor Authentication vereist voor alle gebruikers en beheerders in het virtuele Windows-bureau blad, wordt de beveiliging van uw volledige implementatie verbeterd. Zie voor meer informatie [Azure AD multi-factor Authentication inschakelen voor virtueel bureau blad van Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Voorwaardelijke toegang inschakelen

Als u [voorwaardelijke toegang](../active-directory/conditional-access/best-practices.md) inschakelt, kunt u Risico's beheren voordat u gebruikers toegang verleent tot uw virtuele Windows-desktop omgeving. Wanneer u bepaalt welke gebruikers toegang moeten verlenen, raden we u aan om ook te bepalen wie de gebruiker is, hoe ze zich aanmelden en welk apparaat ze gebruiken.

### <a name="collect-audit-logs"></a>Audit logboeken verzamelen

Als u controle logboek verzameling inschakelt, kunt u de activiteiten van gebruikers en beheerders met betrekking tot het virtuele bureau blad van Windows bekijken. Enkele voor beelden van belang rijke audit logboeken:

-   [Azure-activiteitenlogboek](../azure-monitor/platform/activity-log-collect.md)
-   [Azure Active Directory activiteiten logboek](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Sessie-hosts](../azure-monitor/platform/agent-windows.md)
-   [Diagnostische logboek van Windows virtueel bureau blad](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault logboeken](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>RemoteApps gebruiken

Bij het kiezen van een implementatie model kunt u externe gebruikers toegang bieden tot volledige virtuele Bureau bladen of alleen toepassingen selecteren. Externe toepassingen of RemoteApps bieden een naadloze ervaring als de gebruiker werkt met apps op hun virtuele bureau blad. RemoteApps verminderen het risico door de gebruiker alleen te laten werken met een subset van de externe computer die door de toepassing wordt weer gegeven.

### <a name="monitor-usage-with-azure-monitor"></a>Gebruik controleren met Azure Monitor

Controleer het gebruik en de beschik baarheid van uw Windows Virtual Desktop-service met [Azure monitor](https://azure.microsoft.com/services/monitor/). Overweeg [service Health Alerts](../service-health/alerts-activity-log-service-notifications.md) te maken voor de virtueel-bureaublad service van Windows om meldingen te ontvangen wanneer er een gebeurtenis is die invloed heeft op de service.

## <a name="session-host-security-best-practices"></a>Aanbevolen procedures voor de beveiliging van sessies

Sessie-hosts zijn virtuele machines die worden uitgevoerd binnen een Azure-abonnement en een virtueel netwerk. De algehele beveiliging van uw Windows-Desktop implementatie is afhankelijk van de beveiligings opties die u op uw sessie-hosts hebt geplaatst. In deze sectie worden aanbevolen procedures beschreven voor het beveiligen van uw sessie-hosts.

### <a name="enable-endpoint-protection"></a>Endpoint Protection inschakelen

Als u uw implementatie wilt beveiligen tegen bekende schadelijke software, wordt u aangeraden Endpoint Protection in te scha kelen op alle sessie-hosts. U kunt Windows Defender anti virus of een programma van derden gebruiken. Zie [implementatie handleiding voor Windows Defender anti virus in een VDI-omgeving](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus)voor meer informatie.

Voor profiel oplossingen zoals FSLogix of andere oplossingen die VHD-bestanden koppelen, raden we u aan om VHD-bestands extensies uit te sluiten.

### <a name="install-an-endpoint-detection-and-response-product"></a>Een eind punt detectie-en-antwoord product installeren

We raden u aan een product voor het detecteren en beantwoorden van eind punten te installeren om geavanceerde detectie-en reactie mogelijkheden te bieden. Voor Server besturingssystemen waarop [Azure Security Center](../security-center/security-center-services.md) is ingeschakeld, zal het installeren van een EDR-product Defender ATP implementeren. Voor client besturingssystemen kunt u [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) of een product van derden implementeren voor die eind punten.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Evaluaties van dreigingen en beveiligings problemen inschakelen

Het identificeren van software problemen die in besturings systemen en toepassingen bestaan, is van cruciaal belang om uw omgeving veilig te houden. Azure Security Center kan u helpen bij het identificeren van probleem vlekken via evaluatie van beveiligings problemen voor Server besturingssystemen. U kunt ook Defender ATP gebruiken. Dit biedt een bedreiging en beveiligings beheer voor besturings systemen op het bureau blad. U kunt producten van derden ook gebruiken als u dit niet hebt gebogen, maar we raden u aan om Azure Security Center en Defender ATP te gebruiken.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Patch voor software beveiligings problemen in uw omgeving

Zodra u een beveiligings probleem hebt geïdentificeerd, moet u het bijwerken. Dit geldt ook voor virtuele omgevingen, met inbegrip van de actieve besturings systemen, de toepassingen die erin worden geïmplementeerd en de installatie kopieën van waaruit u nieuwe machines maakt. Volg de communicatie van uw leveranciers patch meldingen en pas tijdig patches toe. U wordt aangeraden maandelijks patches voor uw basis installatie kopieën uit te voeren om ervoor te zorgen dat nieuwe geïmplementeerde machines zo veilig mogelijk zijn.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Maxi maal beleid voor inactieve tijd en ontkoppeling instellen

Gebruikers afmelden wanneer ze inactief zijn voor het behouden van resources en voor komt dat toegang door onbevoegde gebruikers. U wordt aangeraden time-outs te verdelen over de gebruikers productiviteit en het resource gebruik. Voor gebruikers die communiceren met stateless toepassingen, moet u rekening houden met een agressief beleid voor het uitschakelen van machines en het bewaren van resources. Het verbreken van langlopende toepassingen die blijven worden uitgevoerd als een gebruiker niet actief is, zoals een simulatie of CAD-rendering, kan het werk van de gebruiker onderbreken en kan zelfs de computer opnieuw opstarten.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Scherm vergrendelingen instellen voor niet-actieve sessies

U kunt ongewenste systeem toegang voor komen door virtueel bureau blad van Windows zodanig te configureren dat het scherm van de computer wordt vergrendeld tijdens de niet-actieve periode en dat verificatie wordt vereist om het te ontgrendelen.

### <a name="establish-tiered-admin-access"></a>Gelaagde beheerders toegang instellen

U wordt aangeraden uw gebruikers geen toegang tot virtuele Bureau bladen te geven. Als u software pakketten nodig hebt, raden wij u aan deze beschikbaar te stellen via hulpprogram ma's voor configuratie beheer, zoals micro soft Endpoint Manager. In een omgeving met meerdere sessies raden wij u aan gebruikers niet rechtstreeks software te installeren.

### <a name="consider-which-users-should-access-which-resources"></a>Bedenk welke gebruikers toegang moeten hebben tot welke bronnen

U kunt sessie-hosts beschouwen als een uitbrei ding van uw bestaande bureau blad-implementatie. U wordt aangeraden de toegang tot netwerk bronnen op dezelfde manier te beheren als andere Bureau bladen in uw omgeving, zoals het gebruik van netwerk segmentatie en filteren. Sessie-hosts kunnen standaard verbinding maken met elke bron op internet. Er zijn verschillende manieren waarop u verkeer kunt beperken, met inbegrip van Azure Firewall, virtuele netwerk apparaten of proxy's. Als u het verkeer wilt beperken, moet u ervoor zorgen dat u de juiste regels toevoegt zodat Windows virtueel bureau blad correct kan werken.

### <a name="manage-office-pro-plus-security"></a>Office Pro Plus-beveiliging beheren

Naast het beveiligen van uw sessie-hosts is het belang rijk dat u ook de toepassingen die erin worden uitgevoerd, beveiligt. Office Pro Plus is een van de meest voorkomende toepassingen die worden geïmplementeerd in sessie-hosts. Voor het verbeteren van de beveiliging van Office-implementatie raden we u aan om de [Security Policy Advisor](/DeployOffice/overview-of-security-policy-advisor) voor Microsoft 365-apps voor bedrijven te gebruiken. Dit hulp programma identificeert beleids regels die u kunt Toep assen op uw implementatie voor meer beveiliging. Security Policy Advisor beveelt ook beleids regels aan op basis van de impact op uw veiligheid en productiviteit.

### <a name="other-security-tips-for-session-hosts"></a>Andere beveiligings tips voor sessie-hosts

Door de mogelijkheden van het besturings systeem te beperken, kunt u de beveiliging van uw sessie-hosts versterken. Hier volgen enkele dingen die u kunt doen:

- Omleiding van apparaten beheren door stations, printers en USB-apparaten omleiden naar het lokale apparaat van een gebruiker in een extern bureau blad-sessie. We raden u aan uw beveiligings vereisten te evalueren en te controleren of deze functies moeten worden uitgeschakeld.

- Beperk de toegang tot Windows Verkenner door lokale en externe stationstoewijzingen te verbergen. Zo wordt voor komen dat gebruikers ongewenste informatie over systeem configuratie en gebruikers detecteren.

- Vermijd directe RDP-toegang tot sessie-hosts in uw omgeving. Als u direct RDP-toegang voor beheer of probleem oplossing nodig hebt, kunt u [just-in-time](../security-center/security-center-just-in-time.md) -toegang inschakelen om de mogelijke kwets baarheid op een sessiehost te beperken.

- Ken gebruikers beperkte machtigingen toe wanneer ze toegang krijgen tot lokale en externe bestands systemen. U kunt machtigingen beperken door ervoor te zorgen dat uw lokale en externe bestands systemen Access Control List met mini maal privilege gebruiken. Op deze manier hebben gebruikers alleen toegang tot wat ze nodig hebben en kunnen ze geen essentiële resources wijzigen of verwijderen.

- Voor komen dat ongewenste software wordt uitgevoerd op sessie-hosts. U kunt de app-kluis inschakelen voor extra beveiliging op sessie hosts, zodat alleen de apps die u toestaat op de host kunnen worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zie [multi-factor Authentication instellen](set-up-mfa.md)voor meer informatie over het inschakelen van multi-factor Authentication.