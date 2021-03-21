---
title: Microsoft 365 beveiligen tegen on-premises aanvallen
description: Richt lijnen over hoe u ervoor kunt zorgen dat een on-premises aanval geen invloed heeft op Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609903"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Microsoft 365 beveiligen tegen on-premises aanvallen

Veel klanten verbinden hun particuliere bedrijfs netwerken met Microsoft 365 om hun gebruikers, apparaten en toepassingen te laten profiteren. Deze particuliere netwerken kunnen echter op veel goed gedocumenteerde manieren worden aangetast. Omdat Microsoft 365 fungeert als een soort zenuw systeem voor veel organisaties, is het van essentieel belang om deze te beschermen tegen inbreuk op de on-premises infra structuur.

In dit artikel leest u hoe u uw systemen kunt configureren om uw Microsoft 365 cloud-omgeving te beschermen tegen lokale inbreuken. We richten zich voornamelijk op het volgende: 

- Instellingen voor Tenant configuratie van Azure Active Directory (Azure AD).
- Hoe Azure AD-tenants veilig kunnen worden verbonden met on-premises systemen.
- De voor delen die nodig zijn om uw systemen uit te voeren op manieren om uw Cloud systemen te beschermen tegen lokale inbreuken.

We raden u ten zeerste aan deze richt lijnen te implementeren om uw Microsoft 365 cloud omgeving te beveiligen.
> [!NOTE]
> Dit artikel is in eerste instantie gepubliceerd als een blog bericht. Het is verplaatst naar de huidige locatie voor duurzaamheid en onderhoud.
>
> Als u een offline versie van dit artikel wilt maken, gebruikt u de afdruk-naar-PDF-functie van uw browser. Kom regel matig terug voor updates.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Primaire bedreigings vectoren van ongeoorloofde on-premises omgevingen


Uw Microsoft 365 voor delen van de cloud omgeving van een uitgebreide bewakings-en beveiligings infrastructuur. Met behulp van machine learning en Human Intelligence zoekt Microsoft 365 over wereld wijd verkeer. Het kan snel aanvallen detecteren en u in staat stellen om bijna in realtime opnieuw te configureren. 

In hybride implementaties die on-premises infra structuur met Microsoft 365 verbinden, delegeren organisaties het vertrouwen in on-premises onderdelen voor kritieke authenticatie-en beheer beslissingen van Directory object status.
Helaas, als de on-premises omgeving is aangetast, worden deze vertrouwens relaties de mogelijkheden van een aanvaller om uw Microsoft 365 omgeving in te dringen.

De twee primaire bedreigings vectoren zijn *federatieve vertrouwens relaties* en *account synchronisatie.* Beide vectoren kunnen een aanvaller beheerders toegang geven tot uw Cloud.

* **Federatieve vertrouwens relaties**, zoals SAML-verificatie, worden gebruikt voor het verifiëren van Microsoft 365 via uw on-premises identiteits infrastructuur. Als een SAML-certificaat voor token-ondertekening is aangetast, kan de Federatie iedereen met dat certificaat een gebruiker in uw Cloud imiteren. *U wordt aangeraden om de federatieve vertrouwens relaties voor verificatie Microsoft 365 indien mogelijk uit te scha kelen.*

* **Account synchronisatie** kan worden gebruikt voor het wijzigen van bevoegde gebruikers (inclusief hun referenties) of groepen met beheerders bevoegdheden in Microsoft 365. *We raden u aan om ervoor te zorgen dat gesynchroniseerde objecten geen bevoegdheden hebben buiten een gebruiker in Microsoft 365,* hetzij rechtstreeks of via insluiting in vertrouwde rollen of groepen. Zorg ervoor dat deze objecten geen directe of geneste toewijzing hebben in vertrouwde Cloud rollen of-groepen.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Microsoft 365 beveiligen tegen inbreuk op de lokale locatie


Voor het oplossen van de bedreigings vectoren die eerder zijn beschreven, raden we u aan om te voldoen aan de principes die in het volgende diagram worden geïllustreerd:

![Referentie architectuur voor het beveiligen van Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **Uw Microsoft 365 beheerders accounts volledig isoleren.** Ze moeten zijn:

    * Gemastered in azure AD.

     * Verificatie met multi-factor Authentication.

     *  Beveiligd met voorwaardelijke toegang van Azure AD.

     *  Alleen toegankelijk via door Azure beheerde werk stations.

    Deze beheerders accounts zijn accounts met beperkte toegang. *Geen on-premises accounts moeten beheerders bevoegdheden hebben in Microsoft 365.* 

    Zie het [overzicht van Microsoft 365 beheerders rollen](/microsoft-365/admin/add-users/about-admin-roles)voor meer informatie. Zie ook [rollen voor Microsoft 365 in azure AD](../roles/m365-workload-docs.md).

1. **Apparaten beheren vanaf Microsoft 365.** Gebruik Azure AD-deelname en cloud-gebaseerde Mobile Device Management (MDM) om afhankelijkheden te elimineren op uw on-premises infra structuur voor Apparaatbeheer. Deze afhankelijkheden kunnen apparaat-en beveiligings maatregelen veroorzaken.

1. **Zorg ervoor dat er geen on-premises account is met verhoogde bevoegdheden voor Microsoft 365.**
    Sommige accounts hebben toegang tot on-premises toepassingen waarvoor NTLM-, LDAP-of Kerberos-verificatie is vereist. Deze accounts moeten zich in de on-premises identiteits infrastructuur van de organisatie bestaan. Zorg ervoor dat deze accounts, inclusief service accounts, niet zijn opgenomen in privileged Cloud roles of-groepen. Zorg er ook voor dat wijzigingen in deze accounts geen invloed kunnen hebben op de integriteit van uw cloud omgeving. Geprivilegieerde on-premises software mag geen invloed kunnen hebben op Microsoft 365 geprivilegieerde accounts of rollen.

1. **Gebruik Azure AD-Cloud verificatie** om afhankelijkheden van uw on-premises referenties te elimineren. Gebruik altijd sterke verificatie, zoals Windows Hello, FIDO, Microsoft Authenticator of Azure AD multi-factor Authentication.

## <a name="specific-security-recommendations"></a>Specifieke aanbevelingen voor beveiliging


De volgende secties bieden specifieke richt lijnen voor het implementeren van de hierboven beschreven principes.

### <a name="isolate-privileged-identities"></a>Geprivilegieerde identiteiten isoleren


In azure AD zijn gebruikers met geprivilegieerde rollen, zoals beheerders, de basis van vertrouwen om de rest van de omgeving te bouwen en te beheren. Implementeer de volgende procedures om de gevolgen van een inbreuk te minimaliseren.

* Gebruik alleen Cloud accounts voor Azure AD en Microsoft 365 geprivilegieerde rollen.

* Implementeer [geprivilegieerde toegangs apparaten](/security/compass/privileged-access-devices#device-roles-and-profiles) voor bevoegde toegang om Microsoft 365 en Azure ad te beheren.

*  Implementeer [Azure AD privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) voor Just-in-time (JIT) toegang tot alle accounts met geprivilegieerde rollen. Sterke verificatie vereist voor het activeren van rollen.

* Beheerders rollen bieden waarmee de [Mini maal benodigde bevoegdheden voor vereiste taken](../roles/delegate-by-task.md)worden toegestaan.

* Overweeg het gebruik van Azure AD-beveiligings groepen of-Microsoft 365 groepen om een uitgebreide ervaring voor het toewijzen van rollen in te scha kelen, met inbegrip van delegering en meerdere rollen tegelijk. Deze groepen worden gezamenlijk *Cloud groepen* genoemd. [Schakel ook toegangs beheer op basis van rollen in](../roles/groups-assign-role.md). U kunt [administratieve eenheden](../roles/administrative-units.md) gebruiken om het bereik van rollen te beperken tot een deel van de organisatie.

* Implementeer [toegangs accounts voor nood gevallen](../roles/security-emergency-access.md). Gebruik *geen* on-premises wachtwoord kluizen om referenties op te slaan.

Zie [bevoorrechte toegang beveiligen](/security/compass/overview)voor meer informatie. Zie ook [veilige toegangs procedures voor beheerders in azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Cloud verificatie gebruiken 

Referenties zijn een primaire aanvals vector. Implementeer de volgende procedures om referenties veiliger te maken:

* [Implementeer verificatie met een wacht woord](../authentication/howto-authentication-passwordless-deployment.md). Verminder het gebruik van wacht woorden zo veel mogelijk door wacht woordloze referenties te implementeren. Deze referenties worden beheerd en gevalideerd systeem eigen in de Cloud. Kies uit de volgende verificatie methoden:

   * [Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [De Microsoft Authenticator-app](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2-beveiligings sleutels](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Multi-factor Authentication implementeren](../authentication/howto-mfa-getstarted.md). [Meerdere sterke referenties inrichten met behulp van Azure AD multi-factor Authentication](../fundamentals/resilience-in-credentials.md). Op die manier is voor toegang tot cloud resources een referentie vereist die wordt beheerd in azure AD naast een on-premises wacht woord dat kan worden bewerkt. Zie [een flexibele beheer strategie voor toegangs beheer maken met behulp van Azure AD](./resilience-overview.md)voor meer informatie.

### <a name="limitations-and-tradeoffs"></a>Beperkingen en nadelen

* Voor het wachtwoord beheer van hybride accounts zijn hybride onderdelen vereist, zoals wacht woord-en wacht woord-write-agenten. Als uw on-premises infra structuur is aangetast, kunnen aanvallers de computers beheren waarop deze agents zich bevinden. Dit beveiligings probleem heeft geen gevolgen voor uw Cloud infrastructuur. Maar uw Cloud accounts beveiligen deze onderdelen niet van on-premises inbreuk.

*  On-premises accounts die zijn gesynchroniseerd vanaf Active Directory zijn gemarkeerd om nooit te verlopen in azure AD. Deze instelling wordt doorgaans beperkt door on-premises Active Directory wachtwoord instellingen. Als uw on-premises exemplaar van Active Directory echter wordt aangetast en synchronisatie is uitgeschakeld, moet u de optie [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) instellen op het afdwingen van wachtwoord wijzigingen.

## <a name="provision-user-access-from-the-cloud"></a>Gebruikers toegang vanuit de Cloud inrichten

*Inrichting* verwijst naar het maken van gebruikers accounts en-groepen in toepassingen of id-providers.

![Diagram van inrichtings architectuur.](media/protect-m365/protect-m365-provision.png)

We raden u aan de volgende inrichtings methoden aan te bieden:

* **Inrichten van Cloud-HR-apps naar Azure AD**: met deze inrichting kan een on-premises inbreuk worden geïsoleerd, zonder dat u de fase van uw invoeg toepassing-overschakeling van uw Cloud-apps naar Azure AD hoeft te onderbreken.

* **Cloud toepassingen**: Implementeer, waar mogelijk, de implementatie van [Azure AD-apps](../app-provisioning/user-provisioning.md) in plaats van on-premises inrichtings oplossingen. Met deze methode worden uw SaaS-apps (Software-as-a-Service) beschermd tegen schadelijke hacker-profielen in on-premises inbreuken. 

* **Externe identiteiten**: gebruik [Azure AD B2B-samen werking](../external-identities/what-is-b2b.md).
    Met deze methode wordt de afhankelijkheid beperkt van on-premises accounts voor externe samen werking met partners, klanten en leveranciers. Evalueer zorgvuldig alle directe Federatie met andere id-providers. U kunt het beste B2B-gast accounts op de volgende manieren beperken:

   *  Toegang tot gasten beperken tot navigatie van groepen en andere eigenschappen in de Directory. Gebruik de instellingen voor externe samen werking om te voor komen dat gasten groepen kunnen lezen die geen deel uitmaken van. 

    *   Toegang tot de Azure Portal blok keren. U kunt zeldzame nood zakelijke uitzonde ringen maken.  Maak een beleid voor voorwaardelijke toegang dat alle gasten en externe gebruikers bevat. [Implementeer vervolgens een beleid om de toegang te blok keren](../../role-based-access-control/conditional-access-azure-management.md). 

* Niet- **verbonden forests**: gebruik [Azure AD Cloud inrichting](../cloud-sync/what-is-cloud-sync.md). Met deze methode kunt u verbinding maken met niet-verbonden forests, zodat u niet hoeft te zorgen voor connectiviteit tussen forests of vertrouwens relaties, waardoor het effect van een on-premises inbreuk kan worden uitgebreid. 
 
### <a name="limitations-and-tradeoffs"></a>Beperkingen en nadelen

Bij gebruik bij het inrichten van hybride accounts is het Azure-AD-uit-Cloud-systeem afhankelijk van de on-premises synchronisatie om de gegevens stroom te volt ooien van Active Directory naar Azure AD. Als de synchronisatie wordt onderbroken, zijn er geen nieuwe werknemers records beschikbaar in azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Cloud groepen gebruiken voor samen werking en toegang

Met Cloud groepen kunt u uw samen werking en toegang tot uw on-premises infra structuur afbreken.

* **Samen werking**: gebruik Microsoft 365 groepen en micro soft teams voor moderne samen werking. On-premises distributie lijsten buiten gebruik stellen en [distributie lijsten upgraden naar Microsoft 365 groepen in Outlook](/office365/admin/manage/upgrade-distribution-lists).

* **Toegang**: Azure AD-beveiligings groepen of Microsoft 365 groepen gebruiken om toegang te verlenen tot toepassingen in azure AD.
* **Office 365-licentie verlening**: gebruik op groepen gebaseerde licentie verlening voor het inrichten van Office 365 door gebruik te maken van Cloud groepen. Deze methode is van invloed op het beheer van groepslid maatschap vanuit een on-premises infra structuur.

Eigen aren van groepen die worden gebruikt voor toegang moeten worden beschouwd als bevoorrechte identiteit om lidmaatschap van een lokale inbreuk te voor komen.
Een overname omvat directe manipulatie van groepslid maatschappen on-premises of het bewerken van on-premises kenmerken die van invloed kunnen zijn op het dynamische groepslid maatschap in Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Apparaten beheren vanuit de Cloud


Gebruik Azure AD-mogelijkheden om apparaten veilig te beheren.

-   **Windows 10-werk stations gebruiken**: [Implementeer aan Azure AD gekoppelde](../devices/azureadjoin-plan.md) apparaten met MDM-beleid. Schakel [Windows auto pilot](/mem/autopilot/windows-autopilot) in voor een volledig geautomatiseerde inrichtings ervaring.

    -   Verouderde computers waarop Windows 8,1 en eerder wordt uitgevoerd.

    -   Implementeer geen server besturingssysteem computers als werk station.

    -   Gebruik [Microsoft intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) als bron van de autoriteit voor alle workloads voor Apparaatbeheer.

-   [**Privileged Access-apparaten implementeren**](/security/compass/privileged-access-devices#device-roles-and-profiles): gebruik bevoorrechte toegang om Microsoft 365 en Azure ad te beheren.

## <a name="workloads-applications-and-resources"></a>Werk belastingen, toepassingen en resources 

-   **On-premises SSO-systemen (eenmalige aanmelding)** 

    Een on-premises infra structuur voor Federatie-en Web Access-beheer uitbetalen. Configureer toepassingen voor het gebruik van Azure AD.  

-   **SaaS-en line-of-business-toepassingen (LOB) die ondersteuning bieden voor moderne verificatie protocollen** 

    [Gebruik Azure AD voor SSO](../manage-apps/what-is-single-sign-on.md). Hoe meer apps u configureert voor het gebruik van Azure AD voor verificatie, het minder risico op lokale inbreuken.


* **Oudere toepassingen** 

   * U kunt verificatie, autorisatie en externe toegang inschakelen voor oudere toepassingen die geen ondersteuning bieden voor moderne verificatie. Gebruik [Azure AD-toepassingsproxy](../manage-apps/application-proxy.md). U kunt ze ook inschakelen via een netwerk-of toepassings bezorgings controller oplossing met behulp van [beveiligde hybride Access-partner integraties](../manage-apps/secure-hybrid-access.md).   

   * Kies een VPN-leverancier die ondersteuning biedt voor moderne verificatie. Integreer de verificatie met Azure AD. In een on-premises inbreuk kunt u Azure AD gebruiken om de toegang uit te scha kelen of te blok keren door het VPN uit te scha kelen.

*  **Toepassings-en werkbelasting servers**

   * Toepassingen of resources die vereist zijn voor servers, kunnen worden gemigreerd naar Azure Infrastructure as a Service (IaaS). Gebruik [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) om het vertrouwen en de afhankelijkheid van on-premises exemplaren van Active Directory te koppelen. Zorg ervoor dat virtuele netwerken die worden gebruikt voor Azure AD DS geen verbinding met bedrijfs netwerken hebben om deze ontkoppeling te realiseren.

   * Volg de richt lijnen voor [referentie lagen](/security/compass/privileged-access-access-model#ADATM_BM). Toepassings servers worden doorgaans beschouwd als laag-1-assets.

## <a name="conditional-access-policies"></a>Beleid voor voorwaardelijke toegang

Gebruik voorwaardelijke toegang van Azure AD om signalen te interpreteren en gebruiken om verificatie beslissingen te nemen. Zie voor meer informatie het [implementatie plan voor voorwaardelijke toegang](../conditional-access/plan-conditional-access.md).

* Gebruik voorwaardelijke toegang om zoveel mogelijk [verouderde verificatie protocollen te blok keren](../conditional-access/howto-conditional-access-policy-block-legacy.md) . Daarnaast kunt u verouderde verificatie protocollen op toepassings niveau uitschakelen met behulp van een toepassingsspecifieke configuratie.

   Zie [verouderde verificatie protocollen](../fundamentals/auth-sync-overview.md)voor meer informatie. Of Zie specifieke Details voor [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) en [share point online](/powershell/module/sharepoint-online/set-spotenant).

* Implementeer de aanbevolen [identiteits-en toegangs configuraties voor apparaten](/microsoft-365/security/office-365-security/identity-access-policies).

* Als u een versie van Azure AD gebruikt die geen voorwaardelijke toegang bevat, moet u ervoor zorgen dat u de [standaard instellingen voor Azure AD-beveiliging](../fundamentals/concept-fundamentals-security-defaults.md)gebruikt.

   Zie de [prijs gids voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over de licentie verlening voor Azure AD-functies.

## <a name="monitor"></a>Monitor 

Nadat u uw omgeving hebt geconfigureerd om uw Microsoft 365 te beschermen tegen een on-premises inbreuk, controleert u de omgeving [proactief](../reports-monitoring/overview-monitoring.md) .
### <a name="scenarios-to-monitor"></a>Scenario's om te bewaken

Bewaak de volgende belang rijke scenario's naast scenario's die specifiek zijn voor uw organisatie. U moet bijvoorbeeld proactief de toegang tot uw bedrijfskritische toepassingen en resources bewaken.

* **Verdachte activiteit** 

    Bewaak alle [Azure AD-risico gebeurtenissen](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) voor verdachte activiteiten. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) is systeem eigen geïntegreerd met Azure Security Center.

    Definieer het netwerk met de [naam locaties](../reports-monitoring/quickstart-configure-named-locations.md) om ruis detecties te voor komen voor op locatie gebaseerde signalen. 
*  **UEBA-waarschuwingen (User and entity Behavior Analytics)** 

    Gebruik UEBA om inzicht te krijgen in anomalie detectie.
    * Microsoft Cloud App Security (MCAS) biedt [UEBA in de Cloud](/cloud-app-security/tutorial-ueba).

    * U kunt [on-premises UEBA integreren vanuit Azure Advanced Threat Protection (ATP)](/defender-for-identity/install-step2). MCAS leest signalen van Azure AD Identity Protection. 

* **Activiteit van accounts voor nood toegang** 

    Controleer alle toegang die gebruikmaakt van [accounts voor toegang in nood gevallen](../roles/security-emergency-access.md). Waarschuwingen maken voor onderzoeken. Deze bewaking moet het volgende omvatten: 

   * Aanmeldingen. 

   * Referentie beheer. 

   * Alle updates voor groepslid maatschappen. 

   * Toepassings toewijzingen. 
* **Activiteit van geprivilegieerde rol**

    Beveiligings [waarschuwingen die zijn gegenereerd door Azure AD privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)configureren en controleren.
    De directe toewijzing van geprivilegieerde rollen buiten PIM bewaken door waarschuwingen te genereren wanneer een gebruiker rechtstreeks wordt toegewezen.

* **Azure AD-Tenant configuraties**

    Wijzigingen in de configuratie van de Tenant moeten waarschuwingen in het systeem genereren. Deze wijzigingen omvatten, maar zijn niet beperkt tot:

  *  Aangepaste domeinen bijgewerkt.  

  * Azure AD B2B verandert in allowlists en ip's.

  * Azure AD B2B is gewijzigd in toegestane id-providers (SAML-id-providers via directe Federatie of sociale aanmeldingen).  

  * Wijzigingen in voorwaardelijke toegang of het risico beleid. 

* **Toepassings- en service-principalobjecten**
    
   * Nieuwe toepassingen of service-principals waarvoor een beleid voor voorwaardelijke toegang is vereist. 

   * Referenties zijn toegevoegd aan service-principals.
   * Activiteit van de toestemming van de toepassing. 

* **Aangepaste rollen**
   * Updates voor de definities van aangepaste rollen. 

   * Zojuist gemaakte aangepaste rollen. 

### <a name="log-management"></a>Logboek beheer

Definieer een logboek opslag-en bewaar strategie, het ontwerp en de implementatie om een consistente set hulpprogram ma's te vergemakkelijken. U kunt bijvoorbeeld SIEM-systemen (Security Information and Event Management) beschouwen als Azure Sentinel, common query's en onderzoek en forensische playbooks.

* **Azure AD-logboeken**: gegenereerde logboeken en signalen door de aanbevolen procedures te volgen voor instellingen als diagnostische gegevens, logboek retentie en Siem opname. 

    De logboek strategie moet de volgende Azure AD-logboeken bevatten:
   * Aanmeldingsactiviteit 

   * Auditlogboeken 

   * Risicogebeurtenissen 

    Azure AD biedt [Azure monitor integratie](../reports-monitoring/concept-activity-logs-azure-monitor.md) voor het logboek met aanmeld activiteiten en audit Logboeken. Risico gebeurtenissen kunnen worden opgenomen via de [Microsoft Graph-API](/graph/api/resources/identityriskevent). U kunt [Azure AD-logboeken streamen naar Azure monitor-logboeken](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Beveiligings logboeken van het hybride infrastructuur systeem**: alle logboeken van de hybride identiteits infrastructuur moeten worden gearchiveerd en zorgvuldig worden bewaakt als een laag-0-systeem, vanwege de implicaties van het Opper vlak. De volgende elementen bevatten: 

   *  Azure AD Connect. [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) moet worden geïmplementeerd voor het bewaken van identiteits synchronisatie.

   *  Application proxy-agents 


   * Wacht woord terugschrijvende agents 

   * Wachtwoord beveiliging gateway machines  

   * Network Policy servers (NPSs) die de RADIUS-extensie voor multi-factor Authentication van Azure AD hebben 

## <a name="next-steps"></a>Volgende stappen
* [Ontwikkel flexibiliteit in identiteits-en toegangs beheer met behulp van Azure AD](resilience-overview.md)

* [Veilige externe toegang tot resources](secure-external-access-resources.md) 
* [Al uw apps integreren met Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)