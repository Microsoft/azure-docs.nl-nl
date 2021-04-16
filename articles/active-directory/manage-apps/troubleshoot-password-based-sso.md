---
title: Problemen met een aanmelding op basis van een wachtwoord in Azure Active Directory
description: Problemen oplossen met een Azure AD-app die is geconfigureerd voor een een aanmelding op basis van een wachtwoord.
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 2d9bcbdcb292f33d4b1f9dd1d0f6779d8d6cdd49
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376474"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Problemen oplossen met eenmalige aanmelding op basis van wachtwoord in Azure Active Directory

Als u eenmalige aanmelding (SSO) op basis van wachtwoorden wilt gebruiken in Mijn apps, moet de browserextensie zijn geïnstalleerd. De extensie wordt automatisch gedownload wanneer u een app selecteert die is geconfigureerd voor eenmalige aanmelding op basis van een wachtwoord. Zie help voor Mijn apps portal voor meer informatie over het gebruik [Mijn apps het perspectief van eindgebruikers.](../user-help/my-apps-portal-end-user-access.md)

## <a name="my-apps-browser-extension-not-installed"></a>Mijn apps browserextensie niet geïnstalleerd
Zorg ervoor dat de browserextensie is geïnstalleerd. Zie Plan an [Azure Active Directory Mijn apps deployment (Een Azure Active Directory Mijn apps implementeren) voor meer informatie.](my-apps-deployment-plan.md) 

## <a name="single-sign-on-not-configured"></a>Een aanmelding is niet geconfigureerd
Zorg ervoor dat een aanmelding op basis van een wachtwoord is geconfigureerd. Zie Configure [password-based single sign-on (Een aanmelding op basis van wachtwoorden configureren) voor meer informatie.](configure-password-single-sign-on-non-gallery-applications.md)

## <a name="users-not-assigned"></a>Gebruikers niet toegewezen
Zorg ervoor dat de gebruiker is toegewezen aan de app. Zie Een gebruiker of groep toewijzen aan [een app voor meer informatie.](assign-user-or-group-access-portal.md)

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Referenties zijn ingevuld, maar deze worden niet verzonden via de extensie

Dit probleem doet zich meestal voor als de leverancier van de toepassing de aanmeldingspagina onlangs heeft gewijzigd om een veld toe te voegen, een id heeft gewijzigd die wordt gebruikt voor het detecteren van de velden voor gebruikersnaam en wachtwoord, of heeft gewijzigd hoe de aanmeldingservaring werkt voor de toepassing. Gelukkig kan Microsoft in veel gevallen samenwerken met leveranciers van toepassingen om deze problemen snel op te lossen.

Hoewel Microsoft technologieën heeft om automatisch te detecteren wanneer integraties worden breekt, is het mogelijk niet mogelijk om de problemen meteen te vinden of kan het even duren voordat de problemen zijn opgelost. In het geval dat een van deze integraties niet goed werkt, opent u een ondersteuningscase zodat deze zo snel mogelijk kan worden opgelost.

**Als u contact hebt met** de leverancier van deze toepassing, stuurt u ze naar onze manier zodat Microsoft met hen kan samenwerken om hun toepassing systeemeigen te integreren met Azure Active Directory. U kunt de leverancier naar De toepassing [in de](../develop/v2-howto-app-gallery-listing.md) galerie Azure Active Directory verzenden om ze op weg te helpen.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Referenties zijn ingevuld en verzonden, maar de pagina geeft aan dat de referenties onjuist zijn

Probeer eerst de volgende dingen om dit probleem op te lossen:

- Moet de gebruiker zich eerst rechtstreeks aanmelden bij **de toepassingswebsite** met de referenties die voor de gebruiker zijn opgeslagen.

  * Als aanmelden [werkt,](https://myapps.microsoft.com/) laat u de gebruiker op de knop Referenties bijwerken klikken op de **toepassingstegel** **in** de sectie **Apps** van Mijn apps om deze bij te werken naar de meest recente bekende werkende gebruikersnaam en het meest recente werkende wachtwoord.

  * Als u of een andere beheerder de referenties voor deze gebruiker heeft toegewezen, gaat u naar de toepassingstoewijzing van de gebruiker  of groep door te navigeren naar het tabblad Gebruikers **&** Groepen van de toepassing, de toewijzing te selecteren en op de knop Referenties bijwerken te klikken.

- Als de gebruiker zijn eigen referenties heeft toegewezen, moet de gebruiker controleren of het wachtwoord niet is verlopen **in** de toepassing en zo **ja,** het verlopen wachtwoord bijwerken door zich rechtstreeks aan te melden bij de toepassing.

  * Nadat het wachtwoord in de toepassing is bijgewerkt, vraagt u de gebruiker om te klikken op de knop Referenties bijwerken op de **toepassingstegel** **in** de sectie **Apps** van [Mijn apps](https://myapps.microsoft.com/) om deze bij te werken naar de meest recente bekende werkende gebruikersnaam en wachtwoord.

  * Als u of een andere beheerder de referenties voor deze gebruiker heeft toegewezen, gaat u naar de toepassingstoewijzing van de gebruiker  of groep door te navigeren naar het tabblad Gebruikers **&** Groepen van de toepassing, de toewijzing te selecteren en op de knop Referenties bijwerken te klikken.

- Zorg ervoor dat Mijn apps browserextensie wordt uitgevoerd en ingeschakeld in de browser van uw gebruiker.

- Zorg ervoor dat uw gebruikers zich niet proberen aan te melden bij de toepassing vanuit Mijn apps in **de incognito-, inPrivate-** of privémodus . De Mijn apps-extensie wordt niet ondersteund in deze modi.

Als de vorige suggesties niet werken, kan het zijn dat er een wijziging is opgetreden aan de toepassingszijde die de integratie van de toepassing met Azure AD tijdelijk heeft verbroken. Dit kan bijvoorbeeld gebeuren wanneer de leverancier van de toepassing een script op de pagina introduceert dat zich anders gedraagt voor handmatige versus geautomatiseerde invoer, waardoor geautomatiseerde integratie, zoals onze eigen, wordt stuk geschakeld. Gelukkig kan Microsoft in veel gevallen samenwerken met leveranciers van toepassingen om deze problemen snel op te lossen.

Hoewel Microsoft technologieën heeft om automatisch te detecteren wanneer toepassingsintegraties worden breekt, is het mogelijk niet mogelijk om de problemen meteen te vinden of kan het enige tijd duren voordat de problemen zijn opgelost. Wanneer een integratie niet goed werkt, kunt u een ondersteuningscase openen om deze zo snel mogelijk op te lossen. 

Bovendien, als u contact hebt met de leverancier van deze **toepassing,**  stuurt u ze naar ons zodat we met hen kunnen samenwerken om hun toepassing systeemeigen te integreren met Azure Active Directory. U kunt de leverancier naar De toepassing [in de](../develop/v2-howto-app-gallery-listing.md) galerie Azure Active Directory verzenden om ze op weg te helpen.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Controleer of de aanmeldingspagina van de toepassing onlangs is gewijzigd of dat er een extra veld is vereist

Als de aanmeldingspagina van de toepassing drastisch is gewijzigd, worden de integraties soms niet meer gebruikt. Een voorbeeld hiervan is wanneer een leverancier van een toepassing een aanmeldingsveld, een captcha of meervoudige verificatie toevoegt aan hun ervaringen. Gelukkig kan Microsoft in veel gevallen samenwerken met leveranciers van toepassingen om deze problemen snel op te lossen.

Hoewel Microsoft technologieën heeft om automatisch te detecteren wanneer toepassingsintegraties worden breekt, is het mogelijk niet mogelijk om de problemen meteen te vinden of kan het enige tijd duren voordat de problemen zijn opgelost. Wanneer een integratie niet goed werkt, kunt u een ondersteuningscase openen om deze zo snel mogelijk op te lossen. 

Bovendien, als u contact hebt met de leverancier van deze **toepassing,**  stuurt u ze naar ons zodat we met hen kunnen samenwerken om hun toepassing systeemeigen te integreren met Azure Active Directory. U kunt de leverancier naar De toepassing in de galerie Azure Active Directory [verzenden om](../develop/v2-howto-app-gallery-listing.md) aan de slag te gaan.

## <a name="capture-sign-in-fields-for-an-app"></a>Aanmeldingsvelden voor een app vastleggen

Het vastleggen van aanmeldingsveld wordt alleen ondersteund voor aanmeldingspagina's met HTML-indeling. Het wordt niet ondersteund voor niet-standaard aanmeldingspagina's, zoals aanmeldingspagina's die gebruikmaken van Adobe Flash of andere niet-HTML-technologieën.

Er zijn twee manieren om aanmeldingsvelden vast te leggen voor uw aangepaste apps:

- **Automatisch vastleggen van aanmeldingsvelden werkt** goed voor de meeste aanmeldingspagina's met HTML-indeling, als deze gebruikmaken van bekende *DIV-ID's* voor de velden gebruikersnaam en wachtwoord. De HTML op de pagina wordt afgeschroot om DIV-ID's te vinden die aan bepaalde criteria voldoen. Deze metagegevens worden opgeslagen zodat deze later opnieuw kunnen worden afgespeeld naar de app.

- **Handmatig vastleggen van aanmeldingsvelden wordt** gebruikt als de leverancier van de app de invoervelden voor aanmelden *niet labelt.* Handmatig vastleggen wordt ook gebruikt als de *leverancier meerdere velden wekt die* niet automatisch kunnen worden gedetecteerd. Azure Active Directory (Azure AD) kan gegevens opslaan voor zoveel velden als op de aanmeldingspagina, als u weet waar deze velden zich op de pagina staan.

Als automatisch vastleggen van aanmeldingsveld niet werkt, probeert u over het algemeen de handmatige optie.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Aanmeldingsvelden voor een app automatisch vastleggen

Volg deze stappen om eenmalige aanmelding op basis van wachtwoorden te configureren met behulp van automatisch vastleggen van aanmeldingsveld:
1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.
2. Selecteer in het navigatiedeelvenster aan de linkerkant **Alle services om** de Azure AD-extensie te openen.
3. Typ **Azure Active Directory** in het filterzoekvak en selecteer vervolgens **Azure Active Directory**.
4. Selecteer **Bedrijfstoepassingen** in het navigatiedeelvenster van Azure AD.
5. Selecteer **Alle toepassingen om** een lijst met uw apps weer te geven.
   > [!NOTE]
   > Als u de want-app niet ziet, gebruikt u het **besturingselement Filter** bovenaan de **lijst Alle** toepassingen. Stel de **optie** Tonen in op Alle toepassingen.
6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.
7. Nadat de app is geladen, **selecteert u Een aanmelding** in het navigatiedeelvenster aan de linkerkant.
8. Selecteer **Aanmeldingsmodus op basis van** een wachtwoord.
9. Voer de **aanmeldings-URL** in. Dit is de URL van de pagina waar gebruikers hun gebruikersnaam en wachtwoord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldingsvelden zichtbaar zijn op de pagina voor de URL die u op geeft.*
10. Selecteer **Opslaan**.
    De pagina wordt automatisch afgeschroot voor de invoervakken gebruikersnaam en wachtwoord. U kunt nu Azure AD gebruiken om veilig wachtwoorden naar die app te verzenden met behulp van de Mijn apps browserextensie.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Aanmeldingsvelden voor een app handmatig vastleggen

Als u aanmeldingsvelden handmatig wilt vastleggen, moet u de Mijn apps browserextensie hebben geïnstalleerd. Bovendien kan uw browser niet worden uitgevoerd in de *modusPrivate,* *incognito* of *privé.*

Als u eenmalige aanmelding op basis van een wachtwoord voor een app wilt configureren met behulp van handmatige aanmeldingsveldopname, volgt u deze stappen:
1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.
2. Selecteer in het navigatiedeelvenster aan de linkerkant **Alle services om** de Azure AD-extensie te openen.
3. Typ **Azure Active Directory** in het zoekvak en selecteer vervolgens **Azure Active Directory**.
4. Selecteer **Bedrijfstoepassingen** in het navigatiedeelvenster van Azure AD.
5. Selecteer **Alle toepassingen om** een lijst met uw apps weer te geven.
   > [!NOTE] 
   > Als u de want-app niet ziet, gebruikt u het **besturingselement Filter** bovenaan de **lijst Alle** toepassingen. Stel de **optie** Tonen in op Alle toepassingen.
6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.
7. Nadat de app is geladen, **selecteert u Een aanmelding** in het navigatiedeelvenster aan de linkerkant.
8. Selecteer **Aanmeldingsmodus op basis van** wachtwoord.
9. Voer de **aanmeldings-URL** in. Dit is de pagina waar gebruikers hun gebruikersnaam en wachtwoord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldingsvelden zichtbaar zijn op de pagina voor de URL die u op geeft.*
10. Selecteer **Configure *&lt; appname &gt;* Password Single Sign-on Settings**.
11. Selecteer **De aanmeldingsvelden handmatig detecteren.**
14. Selecteer **OK**.
15. Selecteer **Opslaan**.
16. Volg de instructies voor het gebruik van Mijn apps.


## <a name="troubleshoot-problems"></a>Problemen oplossen

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Ik krijg de foutmelding 'We couldn't find any sign-in fields at that URL' (Kan geen aanmeldingsvelden vinden op die URL)

U krijgt dit foutbericht wanneer automatische detectie van aanmeldingsvelden mislukt. Probeer handmatige detectie van aanmeldingsveld om het probleem op te lossen. Zie de [sectie Aanmeldingsvelden handmatig vastleggen voor een toepassing](#manually-capture-sign-in-fields-for-an-app) van dit artikel.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Ik krijg de foutmelding 'Kan configuratie voor een aanmelding niet opslaan'

In zeldzame gevallen mislukt het bijwerken van de configuratie voor eenmalige aanmelding. U kunt dit probleem oplossen door de configuratie opnieuw op te slaan.

Als u de fout blijft krijgen, opent u een ondersteuningscase. Neem de informatie op die wordt beschreven in de secties Meldingsdetails van de [portal](#view-portal-notification-details) weergeven en Meldingsdetails verzenden naar een ondersteuningstechnicus voor [hulpsecties](#send-notification-details-to-a-support-engineer-to-get-help) van dit artikel.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Ik kan aanmeldingsvelden voor mijn app niet handmatig detecteren

U kunt het volgende gedrag observeren wanneer handmatige detectie niet werkt:
- Het handmatige vast leggen lijkt te werken, maar de vastgelegde velden zijn niet juist.
- De juiste velden worden niet gemarkeerd wanneer het vast leggen wordt uitgevoerd.
- Tijdens het vastleggen gaat u zoals verwacht naar de aanmeldingspagina van de app, maar er gebeurt niets.
- Handmatig vastleggen lijkt te werken, maar eenmalige aanmelding wordt niet weergegeven wanneer gebruikers naar de app navigeren vanuit Mijn apps.

Als u een van deze problemen ervaart, doet u het volgende:
- Zorg ervoor dat u de nieuwste versie van de Mijn apps browserextensie *hebt geïnstalleerd en ingeschakeld.*
- Zorg ervoor dat uw browser zich tijdens het vastleggen niet  in *de incognito-,* *inPrivate-* of privémodus heeft. De Mijn apps-extensie wordt niet ondersteund in deze modi.
- Zorg ervoor dat uw gebruikers zich niet proberen aan te melden bij de app vanuit Mijn apps in *de incognito-,* *inPrivate-* of *privémodus.*
- Probeer het handmatig vast te leggen opnieuw. Zorg ervoor dat de rode markeringen over de juiste velden zijn.
- Als het handmatig vastleggen lijkt te stoppen of als de aanmeldingspagina niet reageert, probeert u het handmatige vast leggen opnieuw. Maar deze keer drukt u na het voltooien van het proces op de toets F12 om de ontwikkelaarsconsole van uw browser te openen. Selecteer  het consoletabblad. Typ **window.location=' de *&lt; aanmeldings-URL &gt;*** die u hebt opgegeven bij het configureren van de app en druk op Enter. Dit dwingt een paginaomleiding af die het vastleggen beëindigt en de vastgelegde velden op slaat.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Ik kan geen andere gebruiker toevoegen aan mijn app voor eenmalige aanmelding op basis van een wachtwoord

Eenmalige aanmelding op basis van een wachtwoord heeft een limiet van 48 gebruikers. Er geldt dus een limiet van 48 sleutels voor gebruikersnaam-wachtwoordparen per app.
Als u extra gebruikers wilt toevoegen, kunt u het volgende doen:
-   Extra exemplaar van de app toevoegen
-   Verwijder eerst gebruikers die de app niet meer gebruiken

## <a name="request-support"></a>Ondersteuning aanvragen 
Als er een foutbericht wordt weergegeven bij het instellen van eenmalige aanmelding en het toewijzen van gebruikers, opent u een ondersteuningsticket. Neem zoveel mogelijk van de volgende informatie op:

-   Correlatiefout-id
-   UPN (e-mailadres van gebruiker)
-   TenantID
-   Browsertype
-   Tijdzone en tijd/tijdsbestek waarop de fout is opgetreden
-   Fiddler-traceringen

### <a name="view-portal-notification-details"></a>Details van portalmeldingen weergeven

Volg deze stappen om de details van een portalmelding te bekijken:
1. Selecteer het **meldingenpictogram** (het belpictogram) in de rechterbovenhoek van de Azure Portal.
2. Selecteer een melding met de *status* Fout. (Ze hebben een rode "!".)
   > [!NOTE]
   > U kunt geen meldingen selecteren met de status *Geslaagd* *of* Wordt uitgevoerd.
3. Het **deelvenster Meldingsdetails** wordt geopend. Lees de informatie voor meer informatie over het probleem.
5. Als u nog steeds hulp nodig hebt, deelt u de informatie met een ondersteuningstechnicus of de productgroep. Selecteer het **kopieerpictogram** rechts van het vak **Fout** kopiëren om de meldingsdetails te kopiëren die u wilt delen.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Meldingsdetails verzenden naar een ondersteuningstechnicus om hulp te krijgen

Het is belangrijk dat u *alle* details die in deze sectie worden vermeld, deelt met ondersteuning, zodat ze u snel kunnen helpen. Als u deze wilt opnemen, kunt u een schermopname maken of **Fout kopiëren selecteren.**

In de volgende informatie wordt uitgelegd wat elk meldingsitem betekent en worden voorbeelden gegeven.

#### <a name="essential-notification-items"></a>Essentiële meldingsitems

- **Titel:** de beschrijvende titel van de melding.

   Voorbeeld: *Toepassingsproxy-instellingen*

- **Beschrijving:** wat er is gebeurd als gevolg van de bewerking.

   Voorbeeld: *De ingevoerde interne URL wordt al gebruikt door een andere toepassing.*

- **Meldings-id:** de unieke id van de melding.

    Voorbeeld: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Clientaanvraag-id:** de specifieke aanvraag-id die uw browser heeft gemaakt.

    Voorbeeld: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Tijdstempel UTC:** het tijdstempel van wanneer de melding heeft plaatsgevonden, in UTC.

    Voorbeeld: *2017-03-23T19:50:43.7583681Z*

- **Interne transactie-id:** de interne id die wordt gebruikt om de fout in onze systemen op te zoeken.

    Voorbeeld: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN:** de gebruiker die de bewerking heeft uitgevoerd.

    Voorbeeld: *tperkins \@ f128.info*

- **Tenant-id:** de unieke id van de tenant waar de gebruiker die de bewerking heeft uitgevoerd lid van is.

    Voorbeeld: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Gebruikersobject-id:** de unieke id van de gebruiker die de bewerking heeft uitgevoerd.

    Voorbeeld: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Gedetailleerde meldingsitems

- **Weergavenaam:**(kan leeg zijn) een meer gedetailleerde weergavenaam voor de fout.

    Voorbeeld: *Instellingen voor toepassingsproxy*

- **Status:** de specifieke status van de melding.

    Voorbeeld: *Mislukt*

- **Object-id:**(kan leeg zijn) de object-id waarop de bewerking is uitgevoerd.

   Voorbeeld: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Details:** de gedetailleerde beschrijving van wat er is gebeurd als gevolg van de bewerking.

    Voorbeeld: *Interne URL ' ' is ongeldig omdat deze al in gebruik <https://bing.com/> is.*

- **Kopieerfout:** hiermee kunt  u het kopieerpictogram  rechts van het tekstvak Fout kopiëren selecteren om de meldingsdetails te kopiëren voor ondersteuning.

    Voorbeeld:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Een My Apps-implementatie plannen](my-apps-deployment-plan.md)
