---
title: De werkstroom voor beheerders toestemming configureren - Azure Active Directory | Microsoft Docs
description: Meer informatie over het configureren van een manier voor eindgebruikers om toegang aan te vragen tot toepassingen waarvoor toestemming van de beheerder is vereist.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: iangithinji
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9811c3d1833a02ad3cbaf22b9f0b31fd2da5bb6d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375182"
---
# <a name="configure-the-admin-consent-workflow"></a>De werkstroom voor beheerders toestemming configureren

In dit artikel wordt beschreven hoe u de werkstroomfunctie voor beheerders toestemming inschakelen, waarmee eindgebruikers toegang kunnen aanvragen tot toepassingen waarvoor toestemming van de beheerder is vereist.

Zonder een werkstroom voor beheerdersmachtigingen wordt een gebruiker in een tenant waar toestemming van de gebruiker is uitgeschakeld, geblokkeerd wanneer hij toegang probeert te krijgen tot een app die machtigingen nodig heeft voor toegang tot organisatiegegevens. De gebruiker ziet een algemeen foutbericht waarin staat dat ze niet gemachtigd zijn om toegang te krijgen tot de app en dat ze hun beheerder om hulp moeten vragen. Maar vaak weet de gebruiker niet met wie hij of zij contact moet opnemen, dus hij geeft het op of maakt een nieuw lokaal account in de toepassing. Zelfs wanneer een beheerder een melding krijgt, is er niet altijd een gestroomlijnd proces om de beheerder toegang te geven en gebruikers hiervan op de hoogte te stellen.
 
De werkstroom voor beheerdersgoedkeuring biedt beheerders een veilige manier om toegang te verlenen tot toepassingen waarvoor goedkeuring van de beheerder is vereist. Wanneer een gebruiker toegang probeert te krijgen tot een toepassing, maar geen toestemming kan geven, kan deze een aanvraag voor goedkeuring door de beheerder verzenden. De aanvraag wordt via e-mail verzonden naar beheerders die zijn aangewezen als beoordelaars. Een revisor onderneemt actie op de aanvraag en de gebruiker wordt op de hoogte gesteld van de actie.

Als u aanvragen wilt goedkeuren, moet een revisor een globale beheerder, cloudtoepassingsbeheerder of toepassingsbeheerder zijn. Aan de revisor moet al een van deze beheerdersrollen zijn toegewezen; door ze gewoon aan te geven als revisor, worden hun bevoegdheden niet verhoogd.

## <a name="enable-the-admin-consent-workflow"></a>De werkstroom voor beheerders toestemming inschakelen

De werkstroom voor beheerdersmachtigingen inschakelen en revisoren kiezen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.
2. Klik **boven aan** het navigatiemenu aan de linkerkant op Alle services. De **Azure Active Directory-extensie wordt** geopend.
3. Typ 'Azure Active Directory' in **het zoekvak** en selecteer **het Azure Active Directory** item.
4. Klik in het navigatiemenu op **Bedrijfstoepassingen.** 
5. Selecteer **onder Beheren** de optie **Gebruikersinstellingen.**
6. Stel **onder Toestemmingsaanvragen van beheerder** gebruikers kunnen beheerders toestemming vragen voor apps waar ze geen toestemming voor kunnen **geven** in op **Ja.**

   ![Werkstroominstellingen voor beheerders toestemming configureren](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configureer de volgende instellingen:

   * **Selecteer gebruikers om toestemmingsaanvragen van de beheerder te controleren.** Selecteer revisoren voor deze werkstroom uit een set gebruikers die de rollen Globale beheerder, Cloudtoepassingsbeheerder en Toepassingsbeheerder hebben.
   * **Geselecteerde gebruikers ontvangen e-mailmeldingen voor aanvragen**. U kunt e-mailmeldingen naar de revisoren in- of uitschakelen wanneer een aanvraag wordt ingediend.  
   * **Geselecteerde gebruikers ontvangen vervaldatumherinneringen voor aanvragen.** Hiermee schakelt u e-mailmeldingen voor herinneringen naar de revisoren in of uit wanneer een aanvraag bijna verloopt.  
   * **De toestemmingsaanvraag verloopt na (dagen)**. Geef op hoelang aanvragen geldig blijven.

7. Selecteer **Opslaan**. Het kan een uur duren voordat de functie is ingeschakeld.

> [!NOTE]
> U kunt revisoren voor deze werkstroom toevoegen of verwijderen door de lijst Select **admin consent requests reviewers te** wijzigen. Houd er rekening mee dat een huidige beperking van deze functie is dat revisoren de mogelijkheid kunnen behouden om aanvragen te beoordelen die zijn gemaakt toen ze werden aangewezen als revisor.

## <a name="how-users-request-admin-consent"></a>Hoe gebruikers beheerders toestemming vragen

Nadat de werkstroom voor beheerdersgoedkeuring is ingeschakeld, kunnen gebruikers goedkeuring van de beheerder aanvragen voor een toepassing waar ze geen toestemming voor mogen geven. In de volgende stappen wordt de gebruikerservaring beschreven bij het aanvragen van goedkeuring. 

1. De gebruiker probeert zich aan te melden bij de toepassing.

2. Het **bericht Goedkeuring** vereist wordt weergegeven. De gebruiker geeft een reden op voor toegang tot de app en selecteert vervolgens **Goedkeuring aanvragen.**

   ![Schermopname van het dialoogvenster Goedkeuring vereist, waarin u goedkeuring kunt aanvragen.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Een **verzonden** aanvraagbericht bevestigt dat de aanvraag is verzonden naar de beheerder. Als de gebruiker verschillende aanvragen verzendt, wordt alleen de eerste aanvraag verzonden naar de beheerder.

   ![Schermopname van de bevestiging Verzonden aanvraag.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. De gebruiker ontvangt een e-mailmelding wanneer de aanvraag is goedgekeurd, geweigerd of geblokkeerd. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Controle en actie ondernemen op toestemmingsaanvragen van beheerders

De toestemmingsaanvragen van de beheerder controleren en actie ondernemen:

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) als een van de geregistreerde revisoren van de werkstroom voor beheerdersmachtigingen.
2. Selecteer **Alle services** bovenaan het navigatiemenu aan de linkerkant. De **Azure Active Directory-extensie wordt** geopend.
3. Typ 'Azure Active Directory' in **het zoekvak** en selecteer het **Azure Active Directory** item.
4. Klik in het navigatiemenu op **Bedrijfstoepassingen.**
5. Selecteer **onder Activiteit** de optie **Toestemmingsaanvragen voor beheerders.**

   > [!NOTE]
   > Revisoren zien alleen beheerdersaanvragen die zijn gemaakt nadat ze zijn aangewezen als revisor.

1. Selecteer de toepassing die wordt aangevraagd.
2. Bekijk de details van de aanvraag:  

   * Als u wilt zien wie toegang aanvraagt en waarom, selecteert u **het tabblad Aangevraagd door.**
   * Als u wilt zien welke machtigingen worden aangevraagd door de toepassing, **selecteert u Machtigingen controleren en toestemming geven.**

8. Evalueer de aanvraag en neem de juiste actie:

   * **Keur de aanvraag goed.** Als u een aanvraag wilt goedkeuren, verleent u beheerders toestemming aan de toepassing. Zodra een aanvraag is goedgekeurd, worden alle aanvragende instanties geïnformeerd dat ze toegang hebben gekregen.  
   * **Weiger de aanvraag**. Als u een aanvraag wilt weigeren, moet u een reden verstrekken die aan alle aanvragende aanvragen wordt verstrekt. Zodra een aanvraag wordt geweigerd, krijgen alle aanvragende instanties een melding dat de toegang tot de toepassing is geweigerd. Als u een aanvraag weigert, wordt niet voorkomen dat gebruikers in de toekomst opnieuw beheerders toestemming voor de app vragen.  
   * **Blokkeer de aanvraag**. Als u een aanvraag wilt blokkeren, moet u een reden verstrekken die aan alle aanvragende aanvragen wordt verstrekt. Zodra een aanvraag is geblokkeerd, krijgen alle aanvragende instanties een melding dat de toegang tot de toepassing is geweigerd. Als u een aanvraag blokkeert, wordt er een service-principal-object gemaakt voor de toepassing in uw tenant met een uitgeschakelde status. Gebruikers kunnen in de toekomst geen beheerders toestemming vragen voor de toepassing.
 
## <a name="email-notifications"></a>E-mailmeldingen
 
Indien geconfigureerd, ontvangen alle revisoren e-mailmeldingen wanneer:

* Er is een nieuwe aanvraag gemaakt
* Een aanvraag is verlopen
* Een aanvraag nadert de vervaldatum  
 
Indieners ontvangen e-mailmeldingen wanneer:

* Ze dienen een nieuwe toegangsaanvraag in
* Hun aanvraag is verlopen
* Hun aanvraag is geweigerd of geblokkeerd
* Hun aanvraag is goedgekeurd
 
## <a name="audit-logs"></a>Auditlogboeken 
 
De onderstaande tabel bevat een overzicht van de scenario's en controlewaarden die beschikbaar zijn voor de werkstroom voor beheerders toestemming.

|Scenario  |Controleservice  |Controlecategorie  |Controleactiviteit  |Actor controleren  |Beperkingen van auditlogboek  |
|---------|---------|---------|---------|---------|---------|
|Beheerder die de werkstroom voor de toestemmingsaanvraag inschakelen        |Toegangsbeoordelingen           |UserManagement           |Governancebeleidssjabloon maken          |App-context            |Momenteel kunt u de gebruikerscontext niet vinden            |
|Beheerder die de werkstroom voor de toestemmingsaanvraag uit uitschakelen       |Toegangsbeoordelingen           |UserManagement           |Governancebeleidssjabloon verwijderen          |App-context            |Momenteel kunt u de gebruikerscontext niet vinden           |
|Beheerder die de configuraties van de toestemmingswerkstroom bijwerkt        |Toegangsbeoordelingen           |UserManagement           |Governancebeleidssjabloon bijwerken          |App-context            |Momenteel kunt u de gebruikerscontext niet vinden           |
|Eindgebruiker die een toestemmingsaanvraag voor een beheerder voor een app maakt       |Toegangsbeoordelingen           |Beleid         |Aanvraag maken           |App-context            |Momenteel kunt u de gebruikerscontext niet vinden           |
|Revisoren die een toestemmingsaanvraag van een beheerder goedkeuren       |Toegangsbeoordelingen           |UserManagement           |Alle aanvragen in de bedrijfsstroom goedkeuren          |App-context            |Op dit moment kunt u de gebruikerscontext of de app-id die toestemming van de beheerder heeft gekregen, niet vinden.           |
|Revisoren die een toestemmingsaanvraag van een beheerder weigeren       |Toegangsbeoordelingen           |UserManagement           |Alle aanvragen in de bedrijfsstroom goedkeuren          |App-context            | Op dit moment kunt u de gebruikerscontext van de actor die een aanvraag voor beheerders toestemming heeft geweigerd, niet vinden          |

## <a name="faq"></a>Veelgestelde vragen 

**Ik heb deze werkstroom ingeschakeld, maar wanneer ik de functionaliteit test, waarom kan ik de nieuwe vraag 'Goedkeuring vereist' niet zien zodat ik toegang kan aanvragen?**

Nadat de functie is in geweest, kan het tot 60 minuten duren voordat eindgebruikers de update zien. U kunt controleren of de configuratie correct van kracht is door de **waarde EnableAdminConsentRequests** in de API weer te `https://graph.microsoft.com/beta/settings` geven.

**Waarom zie ik als revisor niet alle aanvragen die in behandeling zijn?**

Revisoren kunnen alleen beheerdersaanvragen zien die zijn gemaakt nadat ze zijn aangewezen als revisor. Dus als u onlangs bent toegevoegd als revisor, ziet u geen aanvragen die zijn gemaakt vóór uw toewijzing.

**Waarom zie ik als revisor meerdere aanvragen voor dezelfde toepassing?**
  
Als een toepassingsontwikkelaar zijn app heeft geconfigureerd voor het gebruik van statische en dynamische toestemming om toegang tot de gegevens van de eindgebruiker aan te vragen, ziet u twee toestemmingsaanvragen van de beheerder. De ene aanvraag vertegenwoordigt de statische machtigingen en de andere de dynamische machtigingen.

**Kan ik als aanvraag de status van mijn aanvraag controleren?**  

Nee, op dit moment kunnen aanmeldders alleen updates ontvangen via e-mailmeldingen.

**Is het als revisor mogelijk om de toepassing goed te keuren, maar niet voor iedereen?**
 
Als u zich zorgen maakt over het verlenen van beheerdersmachtiging en het toestaan van alle gebruikers in de tenant om de toepassing te gebruiken, wordt u aangeraden de aanvraag te weigeren. Verleen vervolgens handmatig beheerderstoewijzing door de toegang tot de toepassing te beperken door gebruikerstoewijzing te vereisen en gebruikers of groepen toe te wijzen aan de toepassing. Zie Methoden voor het toewijzen [van gebruikers en groepen voor meer informatie.](./assign-user-or-group-access-portal.md)

## <a name="next-steps"></a>Volgende stappen

Zie toestemmingskader voor Azure Active Directory voor meer informatie over toestemming [voor toepassingen.](../develop/consent-framework.md)

[Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)

[Machtigingen en toestemming in het Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

[Azure AD in Microsoft Q&A](/answers/topics/azure-active-directory.html)
