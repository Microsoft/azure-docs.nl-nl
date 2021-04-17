---
title: Configuratie Azure Active Directory Mijn apps plannen
description: Planningshandleiding voor het effectief gebruiken Mijn apps in uw organisatie.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: baselden
ms.openlocfilehash: 777daecc119a158f11d865489e4eb497c3bc7899
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376593"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Configuratie Azure Active Directory Mijn apps plannen

> [!NOTE]
> Dit artikel is bedoeld voor IT-professionals die de configuratie van de portal van hun organisatie Mijn apps plannen. 
>
> Zie Aanmelden en apps starten vanuit de Mijn apps portal voor **[documentatie voor eindgebruikers.](../user-help/my-apps-portal-end-user-access.md)**

Azure Active Directory (Azure AD) Mijn apps is een webportal voor het starten en beheren van apps. De Mijn apps biedt gebruikers één plek om hun werk te starten en alle toepassingen te vinden waarvoor ze toegang hebben. Gebruikers hebben toegang Mijn apps op [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Redenen om een Mijn apps

De Mijn apps portal is standaard beschikbaar voor gebruikers en kan niet worden uitgeschakeld. Het is belangrijk om deze zo te configureren dat ze de best mogelijke ervaring hebben en de portal nuttig blijft. 

Elke toepassing in de lijst Azure Active Directory bedrijfstoepassingen wordt weergegeven wanneer aan beide van de volgende voorwaarden wordt voldaan:

* De zichtbaarheids-eigenschap voor de app is ingesteld op true. 

* De app wordt toegewezen aan elke gebruiker of groep. Deze wordt weergegeven voor toegewezen gebruikers.

Het configureren van de portal zorgt ervoor dat de juiste mensen gemakkelijk de juiste apps kunnen vinden.

 
### <a name="how-is-the-my-apps-portal-used"></a>Hoe wordt de Mijn apps portal gebruikt?

Gebruikers hebben toegang tot Mijn apps portal voor het volgende:

* Detecteer en krijg toegang tot alle met Azure AD verbonden toepassingen van hun organisatie waar ze toegang toe hebben.

   * Het is het beste om ervoor te zorgen dat apps zijn geconfigureerd voor eenmalige aanmelding (SSO) om gebruikers de beste ervaring te bieden.

* Vraag toegang aan tot nieuwe apps die zijn geconfigureerd voor selfservice.

* Persoonlijke verzamelingen van apps maken.

* Toegang tot apps voor anderen beheren wanneer de rol van groepseigenaar of gedelegeerd beheer is toegewezen aan de groep die wordt gebruikt om toegang te verlenen tot de toepassing(en).

Beheerders kunnen het volgende configureren:

* [Toestemmingservaringen,](../manage-apps/configure-user-consent.md)  waaronder servicevoorwaarden.

* [Detectie- en toegangsaanvragen voor selfservice-toepassingen.](../manage-apps/access-panel-manage-self-service-access.md)

* [Verzamelingen van toepassingen](../manage-apps/access-panel-collections.md).

* Toewijzing van pictogrammen aan toepassingen

* Gebruikersvriendelijke namen voor toepassingen

* Huisstijl weergegeven op Mijn apps

 

## <a name="plan-consent-configuration"></a>Toestemmingsconfiguratie plannen

### <a name="user-consent-for-applications"></a>Toestemming van de gebruiker voor toepassingen 

Voordat een gebruiker zich kan aanmelden bij een toepassing en de toepassing toegang kan krijgen tot de gegevens van uw organisatie, moet een gebruiker of beheerder de toepassing machtigingen verlenen. U kunt configureren of toestemming van de gebruiker is toegestaan en onder welke voorwaarden. **Microsoft raadt u aan om alleen toestemming van gebruikers toe te staan voor toepassingen van geverifieerde uitgevers.**

Zie Configureren hoe eindgebruikers toestemming [geven voor toepassingen voor meer informatie](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Toestemming van groepseigenaar voor apps die toegang hebben tot gegevens

Groeps- en teameigenaren kunnen toepassingen, zoals toepassingen die zijn gepubliceerd door externe leveranciers, autoreren voor toegang tot de gegevens van uw organisatie die aan een groep zijn gekoppeld. Zie [Resourcespecifieke toestemming in Microsoft Teams](https://docs.microsoft.com/microsoftteams/resource-specific-consent) voor meer informatie. 

U kunt configureren of u deze functie wilt toestaan of uitschakelen.

Zie Machtigingen voor groeps toestemming [configureren voor meer informatie.](../manage-apps/configure-user-consent-groups.md)

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is essentieel voor het succes van elke nieuwe service. Informeer uw gebruikers proactief hoe en wanneer hun ervaring zal veranderen en hoe ze indien nodig ondersteuning kunnen krijgen.

Hoewel Mijn apps meestal geen gebruikersproblemen creëert, is het belangrijk om u voor te bereiden. Maak vóór de lancering handleidingen en een lijst met alle resources voor uw ondersteuningsmedewerkers.

#### <a name="communications-templates"></a>Communicatiesjablonen

Microsoft biedt [aanpasbare sjablonen voor e-mailberichten en andere communicatie](https://aka.ms/APTemplates) voor Mijn apps. U kunt deze assets aanpassen voor gebruik in andere communicatiekanalen, waar van toepassing op uw bedrijfscultuur.

 

## <a name="plan-your-sso-configuration"></a>Uw configuratie voor eenmalige aanmelding plannen

Het is het beste als SSO is ingeschakeld voor alle apps in de Mijn apps-portal, zodat gebruikers een naadloze ervaring hebben zonder dat ze hun referenties hoeven in te voeren.

Azure AD ondersteunt meerdere opties voor eenmalige aanmelding. 

* Zie Opties voor een [een aanmelding in Azure AD voor meer informatie.](sso-options.md)

* Zie de [Quickstart-reeks](../manage-apps/view-applications-portal.md)over toepassingsbeheer voor meer informatie over het gebruik van Azure AD als id-provider voor een app.

### <a name="use-federated-sso-if-possible"></a>Federatief eenmalige aanmelding gebruiken, indien mogelijk

Voor de beste gebruikerservaring met de Mijn apps begint u met de integratie van cloudtoepassingen die beschikbaar zijn voor federatief SSO (OpenID Connect of SAML). Met federatief SSO kunnen gebruikers een consistente ervaring met één klik hebben op verschillende startoppervlakken voor apps en zijn ze vaak robuuster in configuratiebeheer.

Zie het [SaaS SSO-implementatieplan] voor meer informatie over het configureren van uw SaaS-toepassingen (Software as a Service) voor eenmalige aanmelding. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Overwegingen voor speciale SSO-omstandigheden

> [!TIP]
> Voor een betere gebruikerservaring gebruikt u Federatief eenmalige aanmelding met Azure AD (OpenID Connect/SAML) wanneer een toepassing dit ondersteunt, in plaats van eenmalige aanmelding en ADFS op basis van wachtwoorden.

Als u zich wilt aanmelden bij op wachtwoorden gebaseerde SSO-toepassingen of bij toepassingen die worden gebruikt door Azure AD toepassingsproxy, moeten gebruikers de extensie Mijn apps secure sign-in installeren en gebruiken. Gebruikers wordt gevraagd de extensie te installeren wanneer ze de op wachtwoorden gebaseerde eenmalige aanmelding of eenmalige toepassingsproxy starten. 

![Schermopname van](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Zie Installing Mijn apps browser extension [(De browserextensie Mijn apps) voor gedetailleerde informatie over de extensie.](../user-help/my-apps-portal-end-user-access.md)

Als u deze toepassingen moet integreren, moet u een mechanisme definiëren om de extensie op schaal te implementeren met [ondersteunde browsers.](../user-help/my-apps-portal-end-user-access.md) Een aantal opties:

* [Door de gebruiker gestuurd downloaden en configureren voor Chrome, Firefox, Microsoft Edge of IE](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager voor Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Met de extensie kunnen gebruikers elke app starten vanuit de zoekbalk, toegang zoeken tot recent gebruikte toepassingen en een koppeling naar de Mijn apps openen.

![Schermopname van het zoeken naar extensies voor mijn apps](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Mobiele toegang plannen

Voor toepassingen die gebruikmaken van eenmalige aanmelding op basis van een wachtwoord of die worden gebruikt [Microsoft Azure AD toepassingsproxy,](../manage-apps/application-proxy.md)moet u Microsoft Edge gebruiken. Voor andere toepassingen kan elke mobiele browser worden gebruikt. 

### <a name="linked-sso"></a>Gekoppelde eenmalige aanmelding

Toepassingen kunnen worden toegevoegd met behulp van de optie Gekoppelde eenmalige aanmelding. U kunt een toepassingstegel configureren die is koppelingen naar de URL van uw bestaande webtoepassing. Met gekoppelde eenmalige aanmelding kunt u gebruikers naar de Mijn apps-portal leiden zonder alle toepassingen te migreren naar eenmalige aanmelding van Azure AD. U kunt geleidelijk over op toepassingen die zijn geconfigureerd met eenmalige aanmelding van Azure AD zonder de gebruikerservaring te verstoren.

## <a name="plan-the-user-experience"></a>De gebruikerservaring plannen

Standaard worden alle toepassingen waarvoor de gebruiker toegang heeft en alle toepassingen die zijn geconfigureerd voor selfservicedetectie, weergegeven in het deelvenster Mijn apps gebruiker. Voor veel organisaties kan dit een zeer grote lijst zijn, die lastig kan worden als deze niet is georganiseerd

### <a name="plan-my-apps-collections"></a>Verzamelingen Mijn apps plannen

Elke Azure AD-toepassing waarvoor een gebruiker toegang heeft, wordt weergegeven op Mijn apps in de verzameling Alle apps. Gebruik verzamelingen om gerelateerde toepassingen te groeperen en weer te geven op een afzonderlijk tabblad, zodat ze gemakkelijker te vinden zijn. U kunt bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke taakrollen, taken, projecten, en meer. 

Eindgebruikers kunnen hun ervaring ook aanpassen door

* Hun eigen app-verzamelingen maken.

* [App-verzamelingen verbergen en opnieuw rangschikken.](access-panel-collections.md)

![Schermopname van selfserviceconfiguratie](./media/my-apps-deployment-plan/collections.png)

Er is een optie om apps te verbergen in de Mijn apps-portal, terwijl toegang vanaf andere locaties, zoals de Microsoft 365 portal, nog steeds Microsoft 365 is. Meer informatie: [Een toepassing verbergen voor de gebruikerservaring in Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Slechts 950 apps waarvoor een gebruiker toegang heeft, zijn toegankelijk via Mijn apps. Dit omvat apps die worden verborgen door de gebruiker of de beheerder. 

### <a name="plan-self-service-group-management-membership"></a>Lidmaatschap van selfservicegroepsbeheer plannen

U kunt gebruikers in staat stellen om hun eigen beveiligingsgroepen of Microsoft 365 maken en beheren in Azure AD. De eigenaar van de groep kan lidmaatschapsaanvragen goedkeuren of weigeren en het beheer van groepslidmaatschap delegeren. Selfservice voor groepsbeheerfuncties zijn niet beschikbaar voor beveiligingsgroepen of distributielijsten met e-mail.

Als u een selfservicegroepslidmaatschap wilt plannen, moet u bepalen of u alle gebruikers in uw organisatie toestaat om groepen of slechts een subset van gebruikers te maken en te beheren. Als u een subset van gebruikers toestaat, moet u een groep instellen waaraan deze personen worden toegevoegd. 

Zie [Selfservice voor groepsbeheer instellen in Azure Active Directory](../enterprise-users/groups-self-service-management.md) voor meer informatie over het inschakelen van deze scenario's.

### <a name="plan-self-service-application-access"></a>Selfservice voor toegang tot toepassingen plannen

U kunt gebruikers in staat stellen om toepassingen te ontdekken en om toegang aan te vragen via het Mijn apps toegangsvenster. Als u dit wilt doen, moet u eerst 

* selfservicegroepsbeheer inschakelen

* app inschakelen voor eenmalige aanmelding

* een groep maken voor toegang tot toepassingen

![Schermafbeelding van Mijn apps selfserviceconfiguratie](./media/my-apps-deployment-plan/my-apps-self-service.png)

Wanneer gebruikers toegang aanvragen, vragen ze toegang tot de onderliggende groep en kunnen groepseigenaren machtigingen delegeren voor het beheren van het groepslidmaatschap en dus toegang tot toepassingen. Goedkeuringswerkstromen zijn beschikbaar voor expliciete goedkeuring voor toegang tot toepassingen. Gebruikers die goedkeurders zijn, ontvangen meldingen in de Mijn apps portal wanneer er een aanvraag in behandeling is voor toegang tot de toepassing.

## <a name="plan-reporting-and-auditing"></a>Rapportage en controle plannen

Azure AD biedt [rapporten die technische en zakelijke inzichten bieden].. /reports-monitoring/overview-reports.md). Werk samen met eigenaren van zakelijke en technische toepassingen om het eigendom van deze rapporten op te nemen en deze regelmatig te gebruiken. De volgende tabel bevat enkele voorbeelden van typische rapportagescenario's.

| Voorbeeld| beheer risico's.| Productiviteit verhogen| Governance en naleving |
| - | - | - | -|
| Rapporttypen| Toepassingsmachtigingen en -gebruik| Account inrichten-activiteit| Controleren wie toegang heeft tot de toepassingen |
| Mogelijke acties| Toegang controleren; machtigingen intrekken| Inrichtingsfouten oplossen| Toegang intrekken |


Azure AD bewaart de meeste controlegegevens 30 dagen. De gegevens zijn beschikbaar via de Azure-beheerportal of API, die u kunt downloaden naar uw analysesystemen.

#### <a name="auditing"></a>Controleren

Auditlogboeken voor toegang tot toepassingen zijn 30 dagen beschikbaar. Als uw organisatie langere retentie vereist, exporteert u de logboeken naar een SIEM-hulpprogramma (Security Information Event and Management), zoals Splunk of ArcSight.

Voor back-ups voor controle, rapportage en herstel na noodherstel documenteert u de vereiste downloadfrequentie, wat het doelsysteem is en wie verantwoordelijk is voor het beheren van elke back-up. Mogelijk hebt u geen afzonderlijke controle- en rapportageback-ups nodig. Uw back-up na noodherstel moet een afzonderlijke entiteit zijn.

## <a name="validate-your-deployment"></a>Uw implementatie valideren

Zorg ervoor Mijn apps implementatie grondig is getest en dat er een terugdraaiend plan is.

De volgende tests uitvoeren met zowel apparaten in bedrijfs- als persoonlijke apparaten. Deze testgevallen moeten ook uw zakelijke gebruiksgevallen weerspiegelen. Hieronder volgen enkele voorbeelden op basis van typische technische scenario's. Voeg andere toe die specifiek zijn voor uw behoeften.

#### <a name="application-sso-access-test-case-examples"></a>Voorbeelden van toegangscases voor eenmalige aanmelding van toepassingen:


| Bedrijfscase| Verwacht resultaat |
| - | - |
| Gebruiker meldt zich aan bij de Mijn apps portal| Gebruikers kunnen zich aanmelden en hun toepassingen bekijken |
| Gebruiker start een federatief SSO-toepassing| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start voor het eerst een toepassing voor eenmalige aanmelding met een wachtwoord| De gebruiker moet de extensie Mijn apps installeren |
| Gebruiker start een wachtwoord-SSO-toepassing een volgende keer| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit Microsoft 365-portal| De gebruiker wordt automatisch aangemeld bij de toepassing |
| De gebruiker start een app vanuit de Managed Browser| De gebruiker wordt automatisch aangemeld bij de toepassing |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Voorbeelden van testcases voor selfservicemogelijkheden voor toepassingen


| Business case| Verwacht resultaat |
| - | - |
| Gebruiker kan lidmaatschap van de toepassing beheren| Gebruiker kan leden toevoegen of verwijderen die toegang hebben tot de app |
| Gebruiker kan de toepassing bewerken| De gebruiker kan de beschrijving van de toepassing en de referenties voor wachtwoordtoepassingen voor eenmalige aanmelding bewerken |


### <a name="rollback-steps"></a>Stappen voor terugdraaien

Het is belangrijk om te plannen wat u moet doen als uw implementatie niet volgens de planning verloopt. Als de configuratie van eenmalige aanmelding mislukt tijdens de implementatie, moet u weten hoe u problemen met [eenmalige](../hybrid/tshoot-connect-sso.md) aanmelding kunt oplossen en de gevolgen voor uw gebruikers kunt beperken. In extreme omstandigheden moet u mogelijk eenmalige aanmelding [terugdraaien.](plan-sso-deployment.md)

## <a name="manage-your-implementation"></a>Uw implementatie beheren

Gebruik de minst bevoorrechte rol om een vereiste taak binnen de Azure Active Directory. [Bekijk de verschillende rollen die beschikbaar zijn en](../roles/permissions-reference.md) kies de juiste rol om aan uw behoeften voor elke persona voor deze toepassing te voldoen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en verwijderd nadat de implementatie is voltooid.

| Persona's| Rollen| Azure AD-rol |
| - | - | - |
| Helpdeskbeheerder| Ondersteuning op laag 1| Geen |
| Identiteitsbeheerder| Configureren en fouten opsporen wanneer problemen van invloed zijn op Azure AD| Globale beheerder |
| Toepassingsbeheerder| Attestation van gebruikers in toepassing, configuratie voor gebruikers met machtigingen| Geen |
| Infrastructuurbeheerders| Eigenaar van certificaatoverrol| Globale beheerder |
| Bedrijfseigenaar/belanghebbende| Attestation van gebruikers in toepassing, configuratie voor gebruikers met machtigingen| Geen |


U kunt deze [Privileged Identity Management](../privileged-identity-management/pim-configure.md) om uw rollen te beheren om extra controle, controle en toegangsbeoordeling te bieden voor gebruikers met directorymachtigingen.

## <a name="next-steps"></a>Volgende stappen

[Een implementatie van Azure AD Multi-Factor Authentication plannen](../authentication/howto-mfa-getstarted.md)

[Een implementatie van een toepassingsproxy plannen](application-proxy-deployment-plan.md)

