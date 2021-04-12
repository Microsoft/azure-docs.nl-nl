---
title: De beheerder-werk stroom voor het beheer van toestemming configureren-Azure Active Directory | Microsoft Docs
description: Informatie over het configureren van een manier voor eind gebruikers om toegang aan te vragen voor toepassingen waarvoor beheerders toestemming nodig is.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e01a3b1538c6bf3030e5c8f129606adaecffda60
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105320"
---
# <a name="configure-the-admin-consent-workflow"></a>De beheerder toestemming werk stroom configureren

In dit artikel wordt beschreven hoe u de werk stroom functie voor het beheer van toestemming inschakelt, waarmee eind gebruikers toegang kunnen vragen tot toepassingen waarvoor beheerders toestemming nodig is.

Zonder een beheerder toestemming werk stroom, een gebruiker in een Tenant waarbij toestemming van de gebruiker is uitgeschakeld, wordt geblokkeerd wanneer ze proberen toegang te krijgen tot apps die machtigingen nodig hebben om toegang te krijgen tot de organisatie gegevens. De gebruiker ziet een algemeen fout bericht met de melding dat ze niet zijn gemachtigd om toegang te krijgen tot de app en ze moeten hun beheerder vragen om hulp. Maar vaak is de gebruiker niet bekend met wie u contact moet opnemen, zodat ze een nieuw lokaal account in de toepassing kunnen opgeven of maken. Zelfs wanneer een beheerder een melding krijgt, is er geen gestroomlijnd proces om de beheerder te helpen bij het verlenen van toegang en het melden van de gebruikers.
 
De beheerder toestemming werk stroom biedt beheerders een veilige manier om toegang te verlenen tot toepassingen waarvoor de beheerder goed keuring nodig heeft. Wanneer een gebruiker toegang probeert te krijgen tot een toepassing, maar geen toestemming kan geven, kan deze een aanvraag indienen voor goed keuring door de beheerder. De aanvraag wordt via e-mail verzonden naar beheerders die zijn aangewezen als revisors. Een revisor onderneemt actie voor de aanvraag en de gebruiker wordt op de hoogte gesteld van de actie.

Voor het goed keuren van aanvragen moet een revisor een globale beheerder, Cloud toepassings beheerder of toepassings beheerder zijn. De revisor moet al een van deze beheerders rollen hebben toegewezen. Als u deze niet aanwijst als revisor, worden de bevoegdheden niet verhoogd.

## <a name="enable-the-admin-consent-workflow"></a>De beheerder toestemming werk stroom inschakelen

De beheerder toestemming werk stroom inschakelen en revisoren kiezen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.
2. Klik op **alle services** boven aan het navigatie menu aan de linkerkant. De **uitbrei ding Azure Active Directory** wordt geopend.
3. In het zoekvak filteren typt u '**Azure Active Directory**' en selecteert u **het Azure Active Directory** item.
4. Klik in het navigatie menu op **bedrijfs toepassingen**. 
5. Selecteer onder **beheren** de optie **gebruikers instellingen**.
6. Stel gebruikers in het geval van **beheerders toestemming aanvragen** in **om de beheerder in te stellen om toestemming te vragen aan apps die niet mogen instemmen** op **Ja**.

   ![Werk stroom instellingen voor de beheerder toestemming configureren](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configureer de volgende instellingen:

   * **Selecteer gebruikers om aanvragen voor beheerders toestemmingen te bekijken**. Selecteer controleurs voor deze werk stroom uit een set gebruikers die de rollen globale beheerder, Cloud toepassings beheerder en toepassings beheerder hebben.
   * **Geselecteerde gebruikers ontvangen e-mail meldingen voor aanvragen**. E-mail meldingen naar de revisoren in-of uitschakelen wanneer een aanvraag wordt ingediend.  
   * **Geselecteerde gebruikers ontvangen herinneringen voor de verval datum van aanvragen**. E-mail meldingen voor herinneringen aan de revisoren in-of uitschakelen wanneer een aanvraag bijna verloopt.  
   * De **aanvraag voor toestemming verloopt na (dagen)**. Opgeven hoelang aanvragen geldig blijven.

7. Selecteer **Opslaan**. Het kan een uur duren voordat de functie is ingeschakeld.

> [!NOTE]
> U kunt revisoren voor deze werk stroom toevoegen of verwijderen door de lijst **beheerders toestemming aanvragen selecteren** te wijzigen. Houd er rekening mee dat de huidige beperking van deze functie is dat de controleurs de mogelijkheid tot het controleren van aanvragen die zijn ingediend als revisor, kunnen bewaren.

## <a name="how-users-request-admin-consent"></a>Hoe gebruikers toestemming van een beheerder aanvragen

Nadat de beheerder toestemming werk stroom is ingeschakeld, kunnen gebruikers goed keuring van de beheerder aanvragen voor een toepassing die niet is gemachtigd om toestemming te geven. De volgende stappen beschrijven de gebruikers ervaring bij het aanvragen van goed keuring. 

1. De gebruiker probeert zich aan te melden bij de toepassing.

2. Het bericht **goed keuring is vereist** wordt weer gegeven. De gebruiker typt een reden voor het verkrijgen van toegang tot de app en selecteert vervolgens **goed keuring aanvragen**.

   ![Scherm afbeelding toont het dialoog venster goed keuring vereist, waarin u goed keuring kunt aanvragen.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Een bericht **verzonden aanvragen** bevestigt dat de aanvraag is ingediend bij de beheerder. Als de gebruiker meerdere aanvragen verzendt, wordt alleen de eerste aanvraag verzonden naar de beheerder.

   ![Scherm afbeelding toont de bevestiging dat de aanvraag is verzonden.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. De gebruiker ontvangt een e-mail melding wanneer de aanvraag is goedgekeurd, geweigerd of geblokkeerd. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Controleren en actie ondernemen op aanvragen voor het beheer van toestemming

De beheerders toestemmings aanvragen controleren en actie ondernemen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een van de geregistreerde revisoren van de werk stroom voor het beheer van toestemming.
2. Selecteer **alle services** boven aan het navigatie menu aan de linkerkant. De **uitbrei ding Azure Active Directory** wordt geopend.
3. In het zoekvak filteren typt u '**Azure Active Directory**' en selecteert u het **Azure Active Directory** item.
4. Klik in het navigatie menu op **bedrijfs toepassingen**.
5. Selecteer onder **activiteit** de optie **beheerders toestemming aanvragen**.

   > [!NOTE]
   > Revisoren krijgen alleen beheerders aanvragen te zien die zijn gemaakt nadat ze zijn aangewezen als revisor.

1. Selecteer de toepassing die wordt aangevraagd.
2. Bekijk de details van de aanvraag:  

   * Als u wilt zien wie toegang vraagt en waarom, selecteert u het tabblad **aangevraagd door** .
   * Selecteer **controle machtigingen en toestemming** om te zien welke machtigingen door de toepassing worden aangevraagd.

8. Evalueer de aanvraag en onderneem de juiste actie:

   * **De aanvraag goed keuren**. Als u een aanvraag wilt goed keuren, moet u de beheerder toestemming geven voor de toepassing. Zodra een aanvraag is goedgekeurd, worden alle aanvragers hiervan op de hoogte gesteld dat ze toegang hebben gekregen.  
   * **De aanvraag wordt geweigerd**. Als u een aanvraag wilt weigeren, moet u een reden opgeven die aan alle aanvragers wordt verstrekt. Zodra een aanvraag is geweigerd, worden alle aanvragers hiervan op de hoogte gesteld dat ze geen toegang tot de toepassing hebben. Als u een aanvraag weigert, kunnen gebruikers zich in de toekomst niet meer aanmelden bij de app.  
   * **De aanvraag blok keren**. Als u een aanvraag wilt blok keren, moet u een reden opgeven die aan alle aanvragers wordt verstrekt. Zodra een aanvraag is geblokkeerd, worden alle aanvragers hiervan op de hoogte gesteld dat ze geen toegang tot de toepassing hebben. Als u een aanvraag blokkeert, wordt een Service-Principal-object voor de toepassing in de Tenant met de status uitgeschakeld gemaakt. Gebruikers kunnen in de toekomst geen beheerder om toestemming vragen voor de toepassing.
 
## <a name="email-notifications"></a>E-mailmeldingen
 
Indien geconfigureerd, worden alle revisoren e-mail meldingen ontvangen wanneer:

* Er is een nieuwe aanvraag gemaakt
* Een aanvraag is verlopen
* De verval datum van een aanvraag is bijna bereikt  
 
Aanvragers ontvangen e-mail meldingen wanneer:

* Ze verzenden een nieuwe aanvraag voor toegang
* Hun aanvraag is verlopen
* Hun aanvraag is geweigerd of geblokkeerd
* Hun aanvraag is goedgekeurd
 
## <a name="audit-logs"></a>Auditlogboeken 
 
In de onderstaande tabel ziet u een overzicht van de scenario's en controle waarden die beschikbaar zijn voor de beheerder toestemming werk stroom.

|Scenario  |Audit service  |Controlecategorie  |Controle activiteit  |Actor controleren  |Beperkingen van het controle logboek  |
|---------|---------|---------|---------|---------|---------|
|Beheerder die de werk stroom voor de toestemming aanvraag inschakelt        |Toegangsbeoordelingen           |UserManagement           |Governance-beleids sjabloon maken          |App-context            |U kunt de gebruikers context momenteel niet vinden            |
|Beheerder schakelt de werk stroom voor de toestemming aanvraag uit       |Toegangsbeoordelingen           |UserManagement           |Governance-beleids sjabloon verwijderen          |App-context            |U kunt de gebruikers context momenteel niet vinden           |
|Beheerder de configuraties voor de toestemming werk stroom bij te werken        |Toegangsbeoordelingen           |UserManagement           |Beleids sjabloon governance bijwerken          |App-context            |U kunt de gebruikers context momenteel niet vinden           |
|Eind gebruiker heeft een aanvraag voor een beheerders vergunning gemaakt voor een app       |Toegangsbeoordelingen           |Beleid         |Aanvraag maken           |App-context            |U kunt de gebruikers context momenteel niet vinden           |
|Revisoren die een aanvraag voor beheerders toestemming goed keuren       |Toegangsbeoordelingen           |UserManagement           |Alle aanvragen goed keuren in bedrijfs stroom          |App-context            |Op dit moment kunt u de gebruikers context of de App-ID die door de beheerder toestemming is verleend, niet vinden.           |
|Revisoren die een aanvraag voor beheerders toestemming weigeren       |Toegangsbeoordelingen           |UserManagement           |Alle aanvragen goed keuren in bedrijfs stroom          |App-context            | Momenteel kunt u de gebruikers context niet vinden van de actor die een aanvraag voor een beheerder heeft geweigerd          |

## <a name="faq"></a>Veelgestelde vragen 

**Ik heb deze werk stroom ingeschakeld, maar bij het testen van de functionaliteit kan ik de nieuwe vraag ' goed keuring is vereist ' niet zien zodat ik toegang kan aanvragen?**

Nadat de functie is ingeschakeld, kan het tot 60 minuten duren voordat eind gebruikers de update zien. U kunt controleren of de configuratie correct is uitgevoerd door de waarde **EnableAdminConsentRequests** in de API weer te geven `https://graph.microsoft.com/beta/settings` .

**Als revisor, waarom kan ik niet alle aanvragen zien die in behandeling zijn?**

Revisoren kunnen alleen beheerders aanvragen zien die zijn gemaakt nadat ze zijn aangewezen als revisor. Als u onlangs als revisor hebt toegevoegd, ziet u geen aanvragen die zijn gemaakt voor de toewijzing.

**Als revisor, waarom worden er meerdere aanvragen voor dezelfde toepassing weer gegeven?**
  
Als een toepassings ontwikkelaar de app heeft geconfigureerd voor het gebruik van statische en dynamische toestemming voor het aanvragen van toegang tot de gegevens van de eind gebruiker, worden er twee aanvragen voor beheerders toestemming weer gegeven. De ene aanvraag vertegenwoordigt de statische machtigingen en de andere voor de dynamische machtigingen.

**Als aanvrager kan ik de status van mijn aanvraag controleren?**  

Nee, voor de aanvragers kunnen alleen updates worden ontvangen via e-mail meldingen.

**Als revisor is het mogelijk om de toepassing goed te keuren, maar niet voor iedereen?**
 
Als u zich zorgen maakt over het verlenen van beheerders toestemming en het toestaan van alle gebruikers in de Tenant om de toepassing te gebruiken, kunt u het beste de aanvraag weigeren. Ken de beheerder vervolgens hand matig toestemming door de toegang tot de toepassing te beperken door de gebruikers toewijzing te vereisen en gebruikers of groepen toe te wijzen aan de toepassing. Zie [methoden voor het toewijzen van gebruikers en groepen](./assign-user-or-group-access-portal.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory instemming-Framework](../develop/consent-framework.md)voor meer informatie over het inzenden van toepassingen.

[Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)

[Machtigingen en toestemming in het micro soft Identity-platform](../develop/v2-permissions-and-consent.md)

[Azure AD op micro soft Q&A](/answers/topics/azure-active-directory.html)
