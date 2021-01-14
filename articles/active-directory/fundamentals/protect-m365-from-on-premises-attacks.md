---
title: Microsoft 365 beveiligen tegen on-premises aanvallen
description: Richt lijnen voor het garanderen van een on-premises aanval heeft geen invloed op Microsoft 365
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
ms.openlocfilehash: 97893dece068dfdde85159f734095401288231d2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201345"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Microsoft 365 beveiligen tegen on-premises aanvallen

Veel klanten verbinden hun particuliere bedrijfs netwerken met Microsoft 365 om hun gebruikers, apparaten en toepassingen te laten profiteren. Er zijn echter veel goed gedocumenteerde manieren waarop deze particuliere netwerken kunnen worden aangetast. Omdat Microsoft 365 fungeert als het "zenuw systeem" voor veel organisaties, is het van cruciaal belang om deze te beschermen tegen inbreuk op de on-premises infra structuur.

In dit artikel leest u hoe u uw systemen kunt configureren om uw Microsoft 365 cloud-omgeving te beschermen tegen lokale inbreuken. We richten zich voornamelijk op de configuratie-instellingen van Azure AD-tenants, de manieren waarop Azure AD-tenants veilig kunnen worden verbonden met on-premises systemen en de compromissen die nodig zijn om uw systemen uit te voeren op manieren die uw Cloud systemen beschermen tegen inbreuk op locatie.

We raden u ten zeerste aan deze richt lijnen te implementeren om uw Microsoft 365 cloud omgeving te beveiligen.
> [!NOTE]
> Dit artikel is in eerste instantie gepubliceerd als een blog bericht. Deze is hier verplaatst voor duurzaamheid en onderhoud. <br>
Als u een offline versie van dit artikel wilt maken, gebruikt u de functie afdrukken naar PDF van uw browser. Kom regel matig terug voor updates.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Primaire bedreigings vectoren van ongeoorloofde on-premises omgevingen


Uw Microsoft 365 voor delen van de cloud omgeving van een uitgebreide bewakings-en beveiligings infrastructuur. Door gebruik te maken van machine learning en Human Intelligence die wereld wijd verkeer verloopt, kunnen aanvallen snel worden gedetecteerd en kunt u in bijna realtime opnieuw configureren. In hybride implementaties die on-premises infra structuur met Microsoft 365 verbinden, delegeren organisaties het vertrouwen in on-premises onderdelen voor kritieke authenticatie-en beheer beslissingen van Directory object status.
Als de on-premises omgeving is aangetast, resulteren deze vertrouwens relaties in de mogelijkheden van kwaadwillende personen om uw Microsoft 365 omgeving in te dringen.

De twee primaire bedreigings vectoren zijn **federatieve vertrouwens relaties** en **account synchronisatie.** Beide vectoren kunnen een aanvaller beheerders toegang geven tot uw Cloud.

* **Federatieve vertrouwens relaties**, zoals SAML-verificatie, worden gebruikt voor het verifiëren van Microsoft 365 via uw on-premises identiteits infrastructuur. Als een SAML-token handtekening certificaat is aangetast, zou de Federatie toestaan dat iedereen met dat certificaat een gebruiker in uw Cloud imiteert. **U wordt aangeraden om de federatieve vertrouwens relaties voor verificatie Microsoft 365 indien mogelijk uit te scha kelen.**

* **Account synchronisatie** kan worden gebruikt voor het wijzigen van bevoegde gebruikers (inclusief hun referenties) of groepen die beheerders bevoegdheden hebben verleend in Microsoft 365. **We raden u aan om ervoor te zorgen dat gesynchroniseerde objecten geen bevoegdheden hebben buiten een gebruiker in Microsoft 365,** hetzij rechtstreeks of via insluiting in vertrouwde rollen of groepen. Zorg ervoor dat deze objecten geen directe of geneste toewijzing hebben in vertrouwde Cloud rollen of-groepen.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Microsoft 365 beveiligen tegen inbreuk op de lokale locatie


Als u de hierboven beschreven bedreigings vectoren wilt aanpakken, raden we u aan de onderstaande principes aan te houden:

![Referentie architectuur voor het beveiligen van Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **Uw Microsoft 365 beheerders accounts volledig isoleren.** Ze moeten worden

    * Gemastered in azure AD.

     * Geverifieerd met multi-factor Authentication (MFA).

     *  Beveiligd met voorwaardelijke toegang van Azure AD.

     *  Alleen toegankelijk via Azure Managed workstations.

Dit zijn accounts met beperkte toegang. **Er mogen zich geen lokale accounts met beheerders bevoegdheden bevinden in Microsoft 365.** Zie dit [overzicht van Microsoft 365 beheerders rollen](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)voor meer informatie.
Zie ook [rollen voor Microsoft 365 in azure Active Directory](../roles/m365-workload-docs.md).

*  **Apparaten beheren vanaf Microsoft 365.** Gebruik Azure AD-deelname en cloud-gebaseerde Mobile Device Management (MDM) om afhankelijkheden te elimineren op uw on-premises infra structuur voor Apparaatbeheer, waardoor apparaten en beveiligings maatregelen kunnen worden aangetast.

* **Er zijn geen on-premises accounts met verhoogde bevoegdheden voor het Microsoft 365.**
    Accounts die toegang hebben tot on-premises toepassingen waarvoor NTLM-, LDAP-of Kerberos-verificatie is vereist, hebben een account in de on-premises identiteits infrastructuur van de organisatie nodig. Zorg ervoor dat deze accounts, met inbegrip van service accounts, niet zijn opgenomen in bevoegde Cloud rollen of-groepen en dat wijzigingen in deze accounts geen invloed kunnen hebben op de integriteit van uw cloud omgeving. Geprivilegieerde on-premises software mag geen invloed kunnen hebben op Microsoft 365 geprivilegieerde accounts of rollen.

*  **Gebruik Azure AD-Cloud verificatie** om afhankelijkheden van uw on-premises referenties te elimineren. Gebruik altijd sterke verificatie, zoals Windows Hello, FIDO, de Microsoft Authenticator of Azure AD MFA.

## <a name="specific-recommendations"></a>Specifieke aanbevelingen


De volgende secties bieden specifieke richt lijnen voor het implementeren van de hierboven beschreven principes.

### <a name="isolate-privileged-identities"></a>Geprivilegieerde identiteiten isoleren


In azure AD zijn gebruikers met geprivilegieerde rollen, zoals beheerders, de basis van vertrouwen om de rest van de omgeving te bouwen en te beheren. Implementeer de volgende procedures om de impact van een inbreuk te minimaliseren.

* Gebruik alleen Cloud accounts voor Azure AD en Microsoft 365 geprivilegieerde rollen. d

* Implementeer [geprivilegieerde toegangs apparaten](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) voor bevoegde toegang om Microsoft 365 en Azure ad te beheren.

*  Implementeer [Azure AD privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) voor Just-in-time (JIT) toegang tot alle Human accounts met geprivilegieerde rollen en verplicht sterke verificatie voor het activeren van rollen.

* Geef beheerders rollen de [minste bevoegdheid om hun taken uit te voeren](../roles/delegate-by-task.md).

* Als u een uitgebreide functie voor het toewijzen van rollen wilt inschakelen, met inbegrip van overdracht en meerdere rollen tegelijk, kunt u overwegen Azure AD-beveiligings groepen of Microsoft 365 groepen (gezamenlijk ' Cloud groepen ') te gebruiken en [toegangs beheer op basis van rollen in te scha kelen](../roles/groups-assign-role.md). U kunt ook [administratieve eenheden](../roles/administrative-units.md) gebruiken om het bereik van rollen te beperken tot een deel van de organisatie.

* Implementeer [toegangs accounts voor nood gevallen](../roles/security-emergency-access.md) en gebruik geen on-premises wachtwoord kluizen om referenties op te slaan.

Zie voor meer informatie [beveiliging van bevoegde toegang](https://aka.ms/SPA), die gedetailleerde richt lijnen voor dit onderwerp bevat. Zie ook [beveiligde toegangs procedures voor beheerders in azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Cloud verificatie gebruiken 

Referenties zijn een primaire aanvals vector. Implementeer de volgende procedures om referenties veiliger te maken.

* [Implementatie van wacht woordloze verificatie](../authentication/howto-authentication-passwordless-deployment.md): Verminder het gebruik van wacht woorden zo veel mogelijk door wacht woordloze referenties te implementeren. Deze referenties worden beheerd en gevalideerd systeem eigen in de Cloud. U kunt kiezen uit:

   * [Windows hello voor bedrijven](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Authenticator-app](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2-beveiligings sleutels](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Implementeer multi-factor Authentication](https://aka.ms/deploymentplans/mfa): richt [meerdere sterke referenties in met Azure AD MFA](../fundamentals/resilience-in-credentials.md). Op die manier hebben toegang tot cloud resources een referentie die wordt beheerd in azure AD naast een on-premises wacht woord dat kan worden bewerkt.

   * Zie [een flexibele toegangs beheer strategie maken met Azure Active Directory](https://aka.ms/resilientaad)voor meer informatie.

**Beperkingen en nadelen**

* Voor het wachtwoord beheer van hybride accounts zijn hybride onderdelen vereist, zoals wacht woord-en wacht woord-write-agenten. Als uw on-premises infra structuur is aangetast, kunnen aanvallers de computers beheren waarop deze agents zich bevinden. Hoewel dit geen inbreuk maakt op uw Cloud infrastructuur, zullen uw Cloud accounts deze onderdelen niet beveiligen tegen inbreuk op de lokale locatie.

*  On-premises accounts die zijn gesynchroniseerd vanaf Active Directory zijn gemarkeerd om nooit te verlopen in azure AD, op basis van de veronderstelling dat het on-premises AD-wachtwoord beleid dit verkleint. Als uw on-premises AD is aangetast en synchronisatie van AD Connect moet worden uitgeschakeld, moet u de optie [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md)instellen.

## <a name="provision-user-access-from-the-cloud"></a>Gebruikers toegang vanuit de Cloud inrichten

Inrichting verwijst naar het maken van gebruikers accounts en-groepen in toepassingen of id-providers.

![Diagram van inrichtings architectuur](media/protect-m365/protect-m365-provision.png)

* **Inrichten van Cloud-HR-apps naar Azure AD:** Hierdoor kan een on-premises inbreuk worden geïsoleerd zonder dat de invoeg toepassing van uw Cloud-apps naar Azure AD kan worden onderbroken.

* **Cloud toepassingen:** Implementeer [Azure AD-App inrichting](../app-provisioning/user-provisioning.md) zo mogelijk in plaats van on-premises inrichtings oplossingen. Hierdoor worden sommige SaaS-apps beschermd tegen schadelijke gebruikers profielen als gevolg van on-premises inbreuken. 

* **Externe identiteiten:** Gebruik [Azure AD B2B-samen werking](../external-identities/what-is-b2b.md).
    Dit verkleint de afhankelijkheid van on-premises accounts voor externe samen werking met partners, klanten en leveranciers. Evalueer zorgvuldig alle directe Federatie met andere id-providers. U kunt het beste B2B-gast accounts op de volgende manieren beperken.

   *  Toegang tot gasten beperken tot navigatie van groepen en andere eigenschappen in de Directory. Gebruik de instellingen voor externe samen werking om de functionaliteit van de gast te beperken tot het lezen van groepen die geen deel uitmaken van. 

    *   Toegang tot de Azure Portal blok keren. U kunt zeldzame nood zakelijke uitzonde ringen maken.  Een beleid voor voorwaardelijke toegang maken dat alle gasten en externe gebruikers bevat en vervolgens [een beleid implementeert om de toegang te blok keren](/azure/role-based-access-control/conditional-access-azure-management). 

* Niet- **verbonden forests:** Gebruik [Azure AD Cloud inrichting](../cloud-provisioning/what-is-cloud-provisioning.md). Op die manier kunt u verbinding maken met niet-verbonden forests, waardoor u geen verbinding met meerdere forests of vertrouwens relaties hoeft te maken, waarmee de impact van een on-premises inbreuk kan worden uitgebreid. * 
 
**Beperkingen en afwegingen:**

* Bij gebruik bij het inrichten van hybride accounts, is de Azure AD vanuit Cloud-systemen van het bedrijf afhankelijk van de on-premises synchronisatie om de gegevens stroom van AD naar Azure AD te volt ooien. Als de synchronisatie wordt onderbroken, zijn er geen nieuwe werknemers records beschikbaar in azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Cloud groepen gebruiken voor samen werking en toegang

Met Cloud groepen kunt u uw samen werking en toegang tot uw on-premises infra structuur afbreken.

* **Samen werking:** Gebruik Microsoft 365 groepen en micro soft teams voor moderne samen werking. On-premises distributie lijsten buiten gebruik stellen en [distributie lijsten upgraden naar Microsoft 365 groepen in Outlook](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Toegang:** Gebruik Azure AD-beveiligings groepen of Microsoft 365 groepen voor het machtigen van toegang tot toepassingen in azure AD.
* **Office 365-licentie verlening:** Gebruik op groepen gebaseerde licentie verlening voor het inrichten van Office 365 met alleen Cloud groepen. Dit is de controle over groepslid maatschap van de on-premises infra structuur.

Eigen aren van groepen die worden gebruikt voor toegang moeten worden beschouwd als bevoorrechte identiteiten om lidmaatschap van de overname van lokale inbreuken te voor komen.
Neem meer over directe manipulatie van groepslid maatschappen on-premises of manipulatie van on-premises kenmerken die van invloed kunnen zijn op het dynamische groepslid maatschap in Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Apparaten beheren vanuit de Cloud


Gebruik Azure AD-mogelijkheden om apparaten veilig te beheren.

-   **Windows 10-werk stations gebruiken:** [Implementeer aan Azure AD gekoppelde](../devices/azureadjoin-plan.md) apparaten met MDM-beleid. Schakel [Windows auto pilot](https://docs.microsoft.com/mem/autopilot/windows-autopilot) in voor een volledig geautomatiseerde inrichtings ervaring.

    -   Reafschaffing van Windows 8,1 en eerdere machines.

    -   Implementeer geen server besturingssysteem computers als werk station.

    -   Gebruik [Microsoft intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) als bron van de autoriteit van alle werk belastingen voor Apparaatbeheer.

-   [**Implementeer geprivilegieerde toegangs apparaten**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) voor bevoegde toegang om Microsoft 365 en Azure ad te beheren.

 ## <a name="workloads-applications-and-resources"></a>Werk belastingen, toepassingen en resources 

-   **On-premises SSO-systemen:** Een on-premises infra structuur voor Federatie-en Web-toegangs beheer afschaft en toepassingen configureren voor het gebruik van Azure AD.  

-   **SaaS-en LOB-toepassingen die ondersteuning bieden voor moderne verificatie protocollen:** [gebruik Azure AD voor eenmalige aanmelding](../manage-apps/what-is-single-sign-on.md). Hoe meer apps u configureert voor het gebruik van Azure AD voor verificatie, het minder risico in het geval van een on-premises inbreuk.


* **Oudere toepassingen** 

   * Verificatie, autorisatie en externe toegang tot oudere toepassingen die geen ondersteuning bieden voor moderne verificatie kunnen via [Azure AD-toepassingsproxy](../manage-apps/application-proxy.md)worden ingeschakeld. Ze kunnen ook worden ingeschakeld via een netwerk-of toepassings bezorgings controller oplossing met behulp van  [beveiligde hybride Access-partner integraties](../manage-apps/secure-hybrid-access.md).   

   * Kies een VPN-leverancier die moderne verificatie ondersteunt en de verificatie integreert met Azure AD. In het geval van veri inbreuk kunt u Azure AD gebruiken om de toegang uit te scha kelen of te blok keren door het VPN uit te scha kelen.

*  **Toepassings-en werkbelasting servers**

   * Toepassingen of resources waarmee de benodigde servers kunnen worden gemigreerd naar Azure IaaS en gebruikmaken van [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (Azure AD DS) om vertrouwen en afhankelijkheid van ad on-premises te koppelen. Om deze ontkoppeling te realiseren, mogen virtuele netwerken die worden gebruikt voor Azure AD DS geen verbinding met bedrijfs netwerken hebben.

   * Volg de richt lijnen van de [referentie lagen](https://aka.ms/TierModel). Toepassings servers worden doorgaans beschouwd als laag 1-assets.

 ## <a name="conditional-access-policies"></a>Beleid voor voorwaardelijke toegang

Gebruik voorwaardelijke toegang van Azure AD om signalen te interpreteren en verificatie beslissingen te nemen op basis van deze. Zie voor meer informatie het [implementatie plan voor voorwaardelijke toegang.](https://aka.ms/deploymentplans/ca)

* [Verouderde verificatie protocollen](../fundamentals/auth-sync-overview.md): Gebruik voorwaardelijke toegang om zoveel mogelijk [verouderde verificatie protocollen te blok keren](../conditional-access/howto-conditional-access-policy-block-legacy.md) . U kunt ook verouderde verificatie protocollen op toepassings niveau uitschakelen met toepassingsspecifieke configuratie.

   * Bekijk specifieke Details voor [Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) en [share point online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implementeer de aanbevolen [identiteits-en toegangs configuraties voor apparaten.](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Als u een versie van Azure AD gebruikt die geen voorwaardelijke toegang bevat, moet u ervoor zorgen dat u de [standaard instellingen voor Azure AD-beveiliging](../fundamentals/concept-fundamentals-security-defaults.md)gebruikt.

   * Zie de [prijs gids voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over de licentie verlening voor Azure AD-functies.

## <a name="monitoring"></a>Controleren 

Nadat u uw omgeving hebt geconfigureerd om uw Microsoft 365 te beschermen tegen een on-premises inbreuk, controleert u de omgeving [proactief](../reports-monitoring/overview-monitoring.md) .
### <a name="scenarios-to-monitor"></a>Te bewaken scenario's

Bewaak de volgende belang rijke scenario's naast scenario's die specifiek zijn voor uw organisatie. U moet bijvoorbeeld proactief de toegang tot uw bedrijfskritische toepassingen en resources bewaken.

* **Verdachte activiteit**: alle [Azure AD-risico gebeurtenissen](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation) moeten worden gecontroleerd op verdachte activiteiten. [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) is systeem eigen geïntegreerd met Azure Security Center.

   * Definieer het netwerk met de [naam locaties](../reports-monitoring/quickstart-configure-named-locations.md) om ruis detecties te voor komen voor op locatie gebaseerde signalen. 
*  **UEBA-waarschuwingen (User entity behaviorion Analytics)** Gebruik UEBA om inzicht te krijgen in anomalie detectie.
   * Microsoft Cloud app-detectie (MCAS) biedt [UEBA in de Cloud](https://docs.microsoft.com/cloud-app-security/tutorial-ueba).

   * U kunt [on-premises UEBA integreren vanuit Azure ATP](https://docs.microsoft.com/defender-for-identity/install-step2). MCAS leest signalen van Azure AD Identity Protection. 

* **Activiteit van accounts voor nood toegang**: toegang tot [accounts met nood](../roles/security-emergency-access.md) toegang moet worden bewaakt en er moeten waarschuwingen worden gemaakt voor onderzoek. Deze bewaking moet het volgende omvatten: 

   * Aanmeldingen. 

   * Referentie beheer. 

   * Alle updates voor groepslid maatschappen. 

   *    Toepassings toewijzingen. 
* **Activiteit van geprivilegieerde rol**: beveiligings [waarschuwingen die zijn gegenereerd door Azure AD PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts)configureren en controleren.
    De directe toewijzing van geprivilegieerde rollen buiten PIM bewaken door waarschuwingen te genereren wanneer een gebruiker rechtstreeks wordt toegewezen.
* **Azure AD-Tenant configuraties**: elke wijziging in Tenant configuraties moet waarschuwingen in het systeem genereren. Dit zijn onder andere, maar zijn niet beperkt tot
  *  Aangepaste domeinen bijwerken  

  * Wijzigingen in de lijst van Azure AD B2B toestaan/blok keren.
  * Azure AD B2B-toegestane id-providers (SAML id via directe Federatie of sociale aanmeldingen).  
  * Wijzigingen in voorwaardelijke toegang of risico beleid 

* **Toepassings-en Service-Principal-objecten**:
   * Nieuwe toepassingen of service-principals waarvoor een beleid voor voorwaardelijke toegang is vereist. 

   * Aanvullende referenties worden toegevoegd aan service-principals.
   * Activiteit van de toestemming van de toepassing. 

* **Aangepaste rollen**:
   * Updates van de definities van aangepaste rollen. 

   * Er zijn nieuwe aangepaste rollen gemaakt. 

### <a name="log-management"></a>Logboek beheer

Definieer een logboek opslag-en bewaar strategie, ontwerp en implementatie om een consistente toolset zoals SIEM-systemen zoals Azure Sentinel, common query's en onderzoek en forensische playbooks te vergemakkelijken.

* **Azure AD-logboeken** Opname logboeken en signalen die worden geproduceerd na consistente aanbevolen procedures, waaronder Diagnostische instellingen, logboek retentie en SIEM opname. De logboek strategie moet de volgende Azure AD-logboeken bevatten:
   * Aanmeldingsactiviteit 

   * Auditlogboeken 

   * Risicogebeurtenissen 

Azure AD biedt [Azure monitor integratie](../reports-monitoring/concept-activity-logs-azure-monitor.md) voor het logboek met aanmeld activiteiten en audit Logboeken. Risico gebeurtenissen kunnen worden opgenomen via [Microsoft Graph-API](https://aka.ms/AzureADSecuredAzure/32b). U kunt [Azure AD-logboeken streamen naar Azure monitor-logboeken](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Beveiligings logboeken van het hybride infrastructuur systeem.** Alle besturingssysteem logboeken van de hybride identiteits infrastructuur moeten worden gearchiveerd en zorgvuldig worden bewaakt als een <br>Systeem op laag 0, gezien de surface area implicaties. Dit omvat: 

   *  Azure AD Connect. [Azure AD Connect Health](https://aka.ms/AzureADSecuredAzure/32e) moet worden geïmplementeerd voor het bewaken van identiteits synchronisatie.

   *  Application proxy-agents 


   * Write-back-agents voor wacht woorden 

   * Wachtwoord beveiliging gateway machines  

   * NPS met de Azure MFA RADIUS-extensie 

## <a name="next-steps"></a>Volgende stappen
* [Tolerantie inbouwen in identiteit- en toegangsbeheer met Azure AD](resilience-overview.md)

* [Veilige externe toegang tot resources](secure-external-access-resources.md) 
* [Al uw apps integreren met Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)
