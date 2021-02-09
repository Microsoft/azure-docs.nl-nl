---
title: Azure Active Directory mijn apps-configuratie plannen
description: Plannings handleiding om mijn apps in uw organisatie effectief te gebruiken.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 10e548eb87b7ac4254fa916f804a6710252be7fc
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830716"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Azure Active Directory mijn apps-configuratie plannen

> [!NOTE]
> Dit artikel is bedoeld voor IT-professionals die de configuratie van de portal mijn apps van uw organisatie moeten plannen. 
>
> **Zie voor documentatie voor de eind gebruiker [Aanmelden en apps starten vanuit de portal mijn apps](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (Azure AD) mijn apps is een webgebaseerde portal voor het starten en beheren van apps. De pagina mijn apps geeft gebruikers één plek om hun werk te starten en alle toepassingen te zoeken waartoe ze toegang hebben. Gebruikers hebben toegang tot mijn apps op [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Waarom mijn apps configureren?

De portal mijn apps is standaard beschikbaar voor gebruikers en kan niet worden uitgeschakeld. Het is belang rijk om deze zo te configureren dat ze de best mogelijke ervaring hebben en dat de portal nuttig blijft. 

Alle toepassingen in de lijst Azure Active Directory zakelijke toepassingen worden weer gegeven wanneer aan de volgende voor waarden wordt voldaan:

* De zichtbaarheids eigenschap voor de app is ingesteld op waar. 

* De app wordt toegewezen aan een wille keurige gebruiker of groep. Deze wordt weer gegeven voor toegewezen gebruikers.

Door de portal te configureren, zorgt u ervoor dat de juiste personen de juiste apps eenvoudig kunnen vinden.

 
### <a name="how-is-the-my-apps-portal-used"></a>Hoe wordt de portal voor mijn apps gebruikt?

Gebruikers hebben toegang tot de portal mijn apps om het volgende te doen:

* Ontdek en toegang tot alle Azure AD-Connected-toepassingen van hun organisatie waartoe ze toegang hebben.

   * Het is raadzaam om ervoor te zorgen dat apps worden geconfigureerd voor eenmalige aanmelding (SSO) om gebruikers de beste ervaring te bieden.

* Vraag toegang aan tot nieuwe apps die zijn geconfigureerd voor Self-service.

* Maak persoonlijke verzamelingen van apps.

* Beheer de toegang tot apps voor anderen wanneer ze de rol van groeps eigenaar of gedelegeerd besturings element hebben toegewezen voor de groep die wordt gebruikt om toegang te verlenen aan de toepassing (en).

Beheerders kunnen het volgende configureren:

* [Ervaringen van toestemming](../manage-apps/configure-user-consent.md)  , inclusief Service voorwaarden.

* [Aanvragen voor de detectie en toegang van self-service toepassingen](../manage-apps/access-panel-manage-self-service-access.md).

* [Verzamelingen toepassingen](../manage-apps/access-panel-collections.md).

* Toewijzing van pictogrammen aan toepassingen

* Gebruiks vriendelijke namen voor toepassingen

* Bedrijfs huisstijl weer gegeven in mijn apps

 

## <a name="plan-consent-configuration"></a>Configuratie van toestemming plannen

Er zijn twee soorten toestemming: gebruikers toestemming en toestemming voor apps die toegang hebben tot gegevens.

![Scherm opname van de configuratie van de toestemming](./media/my-apps-deployment-plan/my-apps-consent.png)

### <a name="user-consent-for-applications"></a>Gebruikers toestemming voor toepassingen 

Gebruikers of beheerders moeten toestemming geven voor de gebruiks voorwaarden en het privacybeleid van de toepassing. U moet beslissen of gebruikers of alleen Administrators toestemming kunnen geven voor toepassingen. **Als uw bedrijfs regels zijn toegestaan, kunt u het beste de toestemming van de beheerder gebruiken om de controle over de toepassingen in uw Tenant te behouden**.

Als u toestemming van de beheerder wilt gebruiken, moet u een globale beheerder van de organisatie zijn en moeten de toepassingen:

* Geregistreerd in uw organisatie.

* In een andere Azure AD-organisatie zijn geregistreerd en eerder aan ten minste één gebruiker is gezonden.

Als u wilt toestaan dat gebruikers toestemming geven, moet u beslissen of u wilt dat ze op een wille keurige app of alleen onder specifieke omstandigheden.

Zie [configureren hoe eind gebruikers toestemming geven voor een toepassing in azure Active Directory](../manage-apps/configure-user-consent.md) voor meer informatie.

### <a name="group-owner-consent-for-apps-accessing-data"></a>Toestemming van groeps eigenaar voor apps die toegang krijgen tot gegevens

Bepaal of de eigen aren van de Azure AD-beveiligings groepen of M365-groepen kunnen toestaan dat toepassingen toegang hebben tot gegevens voor de groepen waarvan ze eigenaar zijn. U kunt niet toestaan, alle groeps eigenaren toestaan of alleen een subset van groeps eigenaren toestaan.

Zie [machtigingen voor groeps toestemming configureren](../manage-apps/configure-user-consent-groups.md)voor meer informatie.

Configureer vervolgens de instellingen voor de toestemming van uw [gebruikers-en groeps eigenaar](https://portal.azure.com/) in de Azure Portal.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Laat uw gebruikers proactief weten hoe en wanneer hun ervaring wordt gewijzigd en hoe u zo nodig ondersteuning krijgt.

Hoewel mijn apps doorgaans geen gebruikers problemen maken, is het belang rijk om voor te bereiden. Maak hand leidingen en een lijst met alle resources voor uw ondersteunings personeel voordat u het start.

#### <a name="communications-templates"></a>Communicatie sjablonen

Micro soft biedt [aanpas bare sjablonen voor e-mails en andere communicatie](https://aka.ms/APTemplates) voor mijn apps. U kunt deze assets aanpassen voor gebruik in andere communicatie kanalen die geschikt zijn voor uw bedrijfs cultuur.

 

## <a name="plan-your-sso-configuration"></a>Uw SSO-configuratie plannen

Het is raadzaam om SSO in te scha kelen voor alle apps in de portal mijn apps zodat gebruikers een naadloze ervaring hebben zonder dat ze hun referenties hoeven in te voeren.

Azure AD ondersteunt meerdere SSO-opties. 

* Zie [Opties voor eenmalige aanmelding in azure AD](sso-options.md)voor meer informatie.

* Voor meer informatie over het gebruik van Azure AD als een id-provider voor een app raadpleegt u de Quick Start- [serie op toepassings beheer](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Gebruik, indien mogelijk, federatieve SSO

Voor de beste gebruikers ervaring met de pagina mijn apps begint u met de integratie van Cloud toepassingen die beschikbaar zijn voor federatieve SSO (OpenID Connect Connect of SAML). Met federatieve SSO kunnen gebruikers een consistente ervaring met één klik hebben voor het starten van apps en is het handiger om meer robuust te zijn in configuratie beheer.

Voor meer informatie over het configureren van SaaS-toepassingen (Software as a Service) voor SSO raadpleegt u het [SaaS SSO-implementatie plan]. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Overwegingen voor speciale SSO-omstandigheden

> [!TIP]
> Gebruik voor een betere gebruikers ervaring federatieve SSO met Azure AD (OpenID Connect Connect/SAML) wanneer een toepassing dit ondersteunt, in plaats van SSO-en ADFS-aanmelding op basis van wacht woorden.

Als u zich wilt aanmelden bij SSO-toepassingen op basis van een wacht woord of voor toepassingen die worden gebruikt door Azure AD-toepassingsproxy, moeten gebruikers de beveiligde aanmeldings extensie voor mijn apps installeren en gebruiken. Gebruikers wordt gevraagd de uitbrei ding te installeren wanneer ze de op wacht woord gebaseerde SSO-of toepassings proxy toepassing voor het eerst starten. 

![Scherm opname van](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Zie voor gedetailleerde informatie over de uitbrei ding de [browser uitbreiding van mijn apps installeren](../user-help/my-apps-portal-end-user-access.md).

Als u deze toepassingen moet integreren, moet u een mechanisme definiëren om de uitbrei ding op schaal te implementeren met [ondersteunde browsers](../user-help/my-apps-portal-end-user-access.md). Een aantal opties:

* [Door de gebruiker gestuurde down load en configuratie voor Chrome, Firefox, micro soft Edge of IE](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager voor Internet Explorer](https://docs.microsoft.com/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Met de uitbrei ding kunnen gebruikers elke app vanuit de zoek balk starten, toegang krijgen tot recent gebruikte toepassingen en een koppeling hebben met de pagina mijn apps.

![Scherm opname van de zoek opdracht voor extensies van mijn apps](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Mobile Access plannen

Voor toepassingen die gebruikmaken van op wacht woord gebaseerde SSO of toegang hebben via [Microsoft Azure AD toepassings proxy](../manage-apps/application-proxy.md), moet u micro soft Edge Mobile gebruiken. Voor andere toepassingen kan elke mobiele browser worden gebruikt. 

### <a name="linked-sso"></a>Gekoppelde SSO

Toepassingen kunnen worden toegevoegd met behulp van de optie gekoppelde SSO. U kunt een toepassings tegel configureren die wordt gekoppeld aan de URL van uw bestaande webtoepassing. Met gekoppelde SSO kunt u beginnen met het omleiden van gebruikers naar de portal mijn apps zonder alle toepassingen naar Azure AD SSO te migreren. U kunt geleidelijk overschakelen naar door Azure AD SSO geconfigureerde toepassingen zonder de gebruikers ervaring te onderbreken.

## <a name="plan-the-user-experience"></a>De gebruikers ervaring plannen

Standaard worden alle toepassingen waartoe de gebruiker toegang heeft en alle toepassingen die zijn geconfigureerd voor Self-service detectie weer gegeven in het deel venster mijn apps van de gebruiker. Voor veel organisaties kan dit een zeer grote lijst zijn, die burdensome kan worden als ze niet zijn georganiseerd

### <a name="plan-my-apps-collections"></a>Verzamelingen van mijn apps plannen

Elke Azure AD-toepassing waartoe een gebruiker toegang heeft, wordt weer gegeven in mijn apps in de verzameling alle apps. Gebruik verzamelingen om gerelateerde toepassingen te groeperen en op een afzonderlijk tabblad te presen teren, zodat u ze eenvoudiger kunt vinden. U kunt bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke taak rollen, taken, projecten, enzovoort. 

Eind gebruikers kunnen hun ervaring ook aanpassen door

* Maken van hun eigen app-verzamelingen.

* [App-verzamelingen verbergen en opnieuw ordenen](access-panel-collections.md).

![Scherm opname van selfservice configuratie](./media/my-apps-deployment-plan/collections.png)

Er is een optie om apps te verbergen in de portal mijn apps, terwijl u nog steeds toegang vanaf andere locaties, zoals de Microsoft 365 Portal, toestaat. Meer informatie: [een toepassing verbergen voor gebruikers ervaring in azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Alleen 950-apps waartoe een gebruiker toegang heeft, kunnen worden geopend via mijn apps. Dit geldt ook voor apps die zijn verborgen door de gebruiker of de beheerder. 

### <a name="plan-self-service-group-management-membership"></a>Lidmaatschap van self-service groeps beheer plannen

U kunt gebruikers in staat stellen hun eigen beveiligings groepen of Microsoft 365 groepen te maken en te beheren in azure AD. De eigenaar van de groep kan lidmaatschaps aanvragen goed keuren of weigeren en het beheer van groepslid maatschap delegeren. Self-service groeps beheer functies zijn niet beschikbaar voor beveiligings groepen met e-mail functionaliteit of distributie lijsten.

Als u wilt plannen voor een self-service groepslid maatschap, bepaalt u of u wilt dat alle gebruikers in uw organisatie groepen kunnen maken en beheren of alleen een subset van gebruikers. Als u een subset van gebruikers toestaat, moet u een groep instellen waaraan deze personen worden toegevoegd. 

Zie [self-service groeps beheer instellen in azure Active Directory](../enterprise-users/groups-self-service-management.md) voor meer informatie over het inschakelen van deze scenario's.

### <a name="plan-self-service-application-access"></a>De toegang van self-service toepassingen plannen

U kunt gebruikers in staat stellen om toegang te krijgen tot toepassingen via het deel venster mijn apps. Om dit te doen, moet u eerst 

* Self-service groeps beheer inschakelen

* app voor eenmalige aanmelding inschakelen

* een groep maken voor toegang tot toepassingen

![Scherm opname van mijn apps self-service configuratie](./media/my-apps-deployment-plan/my-apps-self-service.png)

Wanneer gebruikers toegang aanvragen, vragen ze om toegang te krijgen tot de onderliggende groep en kunnen groeps eigenaren toestemming geven om het groepslid maatschap en de toegang tot toepassingen te beheren. Goedkeurings werk stromen zijn beschikbaar voor expliciete goed keuring voor toegang tot toepassingen. Gebruikers die goed keurders zijn, ontvangen meldingen binnen de portal mijn apps wanneer er een aanvraag voor toegang tot de toepassing in behandeling is.

## <a name="plan-reporting-and-auditing"></a>Rapportage en controle plannen

Azure AD biedt [rapporten die technische en zakelijke inzichten bieden]... /reports-monitoring/overview-reports.md). Werk samen met de eigen aren van zakelijke en technische toepassingen om eigenaar te worden van deze rapporten en ze regel matig te gebruiken. De volgende tabel bevat enkele voor beelden van typische rapportage scenario's.

| Voorbeeld| beheer risico's.| Productiviteit verhogen| Governance en naleving |
| - | - | - | -|
| Rapporttypen| Toepassings machtigingen en-gebruik| Activiteit voor het inrichten van accounts| Controleren wie toegang heeft tot de toepassingen |
| Mogelijke acties| Toegang controleren; machtigingen intrekken| Eventuele inrichtings fouten herstellen| Toegang intrekken |


Azure AD houdt de meeste controle gegevens gedurende 30 dagen. De gegevens zijn beschikbaar via de Azure-beheer portal of-API die u kunt downloaden naar uw analyse systemen.

#### <a name="auditing"></a>Controleren

Audit logboeken voor toegang tot toepassingen zijn 30 dagen beschikbaar. Als uw organisatie een langere retentie vereist, exporteert u de logboeken naar een SIEM-hulp programma (Security Information Event and Management), zoals Splunk of ArcSight.

Voor controle-, rapportage-en nood herstel back-ups Documenteer de vereiste Download frequentie, het doel systeem en wie verantwoordelijk is voor het beheren van elke back-up. Mogelijk hebt u geen afzonderlijke back-ups van controle en rapportage nodig. Uw back-up voor herstel na nood gevallen moet een afzonderlijke entiteit zijn.

## <a name="validate-your-deployment"></a>Uw implementatie valideren

Zorg ervoor dat de implementatie van mijn apps uitgebreid is getest en dat er een terugdraai plan is.

Voer de volgende tests uit met apparaten in bedrijfs eigendom en persoonlijke apparaten. Deze test cases moeten ook overeenkomen met uw zakelijke gebruiks voorbeelden. Hieronder volgen enkele gevallen op basis van typische technische scenario's. Voeg anderen specifiek toe aan uw behoeften.

#### <a name="application-sso-access-test-case-examples"></a>Case-voor beelden van Application SSO Access testen:


| Business Case| Verwacht resultaat |
| - | - |
| Gebruiker meldt zich aan bij de portal mijn apps| De gebruiker kan zich aanmelden en hun toepassingen bekijken |
| Gebruiker start een federatieve SSO-toepassing| De gebruiker wordt automatisch aangemeld bij de toepassing |
| De gebruiker start een SSO-toepassing voor wacht woord voor de eerste keer| De gebruiker moet de uitbrei ding mijn apps installeren |
| Gebruiker start een SSO-toepassing voor een wacht woord een volgende keer| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit Microsoft 365 Portal| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit de Managed Browser| De gebruiker wordt automatisch aangemeld bij de toepassing |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Case-voor beelden van de self-service voor toepassingen testen


| Business Case| Verwacht resultaat |
| - | - |
| Gebruiker kan lidmaatschap van de toepassing beheren| Gebruiker kan leden toevoegen/verwijderen die toegang hebben tot de app |
| Gebruiker kan de toepassing bewerken| Gebruiker kan de beschrijving en referenties voor wacht woord-SSO-toepassingen bewerken |


### <a name="rollback-steps"></a>Stappen voor ongedaan maken

Het is belang rijk om te plannen wat u moet doen als uw implementatie niet volgens de planning wordt uitgevoerd. Als de SSO-configuratie mislukt tijdens de implementatie, moet u weten hoe u [SSO-problemen oplost](../hybrid/tshoot-connect-sso.md) en de gevolgen voor uw gebruikers vermindert. In uitzonderlijke omstandigheden is het mogelijk om [SSO terug te draaien](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Uw implementatie beheren

Gebruik de rol met de minst privileged om een vereiste taak in Azure Active Directory uit te voeren. [Bekijk de verschillende beschik bare rollen](../roles/permissions-reference.md) en kies de juiste functie om uw behoeften voor elke persoon voor deze toepassing op te lossen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en worden verwijderd nadat de implementatie is voltooid.

| Persona's| Rollen| Azure AD-rol |
| - | - | - |
| Helpdesk beheerder| Ondersteuning voor laag 1| Geen |
| Identiteits beheerder| Configureren en fouten opsporen wanneer de problemen invloed hebben op Azure AD| Globale beheerder |
| Toepassings beheerder| Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen| Geen |
| Infrastructuur beheerders| Eigenaar certificaat rollover| Globale beheerder |
| Bedrijfs eigenaar/belanghebbende| Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen| Geen |


U kunt [privileged Identity Management](../privileged-identity-management/pim-configure.md) gebruiken om uw rollen te beheren om extra controle, controle en toegangs beoordeling te bieden voor gebruikers met mapmachtigingen.

## <a name="next-steps"></a>Volgende stappen

[Een implementatie van Azure AD Multi-Factor Authentication plannen](../authentication/howto-mfa-getstarted.md)

[Een implementatie van een toepassingsproxy plannen](application-proxy-deployment-plan.md)

 
