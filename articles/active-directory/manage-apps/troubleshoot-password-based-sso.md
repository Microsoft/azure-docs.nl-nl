---
title: Problemen oplossen met eenmalige aanmelding op basis van wacht woorden in Azure Active Directory
description: Los problemen op met een Azure AD-app die is geconfigureerd voor eenmalige aanmelding op basis van wacht woorden.
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: fdbbc6c53e43d3ecc9fd4ecb563bd7ec798f8888
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257575"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Problemen oplossen met eenmalige aanmelding op basis van wachtwoord in Azure Active Directory

Als u eenmalige aanmelding (SSO) op basis van wacht woorden in mijn apps wilt gebruiken, moet de browser extensie zijn geïnstalleerd. De extensie wordt automatisch gedownload wanneer u een app selecteert die is geconfigureerd voor SSO op basis van een wacht woord. Zie [de Help van mijn apps-Portal](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over het gebruik van mijn apps vanuit het oogpunt van eind gebruikers.

## <a name="my-apps-browser-extension-not-installed"></a>De browser extensie van mijn apps is niet geïnstalleerd
Zorg ervoor dat de browser uitbreiding is geïnstalleerd. Zie [een Azure Active Directory implementatie van mijn apps plannen](my-apps-deployment-plan.md)voor meer informatie. 

## <a name="single-sign-on-not-configured"></a>Eenmalige aanmelding is niet geconfigureerd
Zorg ervoor dat de eenmalige aanmelding op basis van wacht woorden is geconfigureerd. Zie [eenmalige aanmelding op basis van wacht woorden configureren](configure-password-single-sign-on-non-gallery-applications.md)voor meer informatie.

## <a name="users-not-assigned"></a>Gebruikers niet toegewezen
Zorg ervoor dat de gebruiker is toegewezen aan de app. Zie [een gebruiker of groep toewijzen aan een app](assign-user-or-group-access-portal.md)voor meer informatie.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Referenties zijn ingevuld, maar deze worden niet verzonden via de extensie

Dit probleem treedt doorgaans op als de leverancier van de toepassing onlangs een veld heeft gewijzigd, een id heeft gewijzigd die wordt gebruikt voor het detecteren van de gebruikers naam en het wacht woord, of het wijzigen van de manier waarop de aanmeldings ervaring werkt voor de toepassing. Gelukkig kan micro soft in veel gevallen samen werken met leveranciers van toepassingen om deze problemen snel op te lossen.

Micro soft heeft technologieën om automatisch te detecteren wanneer integraties zijn verbroken. het is mogelijk dat u de problemen niet meteen kunt vinden of dat de problemen enige tijd duren om op te lossen. Als een van deze integraties niet correct werkt, opent u een ondersteunings aanvraag zodat deze zo snel mogelijk kan worden opgelost.

**Als u contact opneemt met de leverancier van deze toepassing, kunt u** ze onze manier sturen zodat micro soft ermee kan samen werken om hun toepassing systeem eigen te integreren met Azure Active Directory. U kunt de leverancier verzenden naar de [vermelding uw toepassing in de galerie met Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md) om ze te starten.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Referenties zijn ingevuld en verzonden, maar de pagina geeft aan dat de referenties onjuist zijn

Voer de volgende stappen uit om dit probleem op te lossen:

- Laat de gebruiker eerst proberen **zich rechtstreeks aan te melden bij de toepassings website** met de referenties die voor hen zijn opgeslagen.

  * Als de gebruiker zich aanmeldt, klikt u op de knop **referenties bijwerken** op de **tegel toepassing** in de sectie **apps** van [mijn apps](https://myapps.microsoft.com/) om ze bij te werken naar de nieuwste bekende werk gebruikers naam en het meest recente wacht woord.

  * Als u of een andere beheerder de referenties voor deze gebruiker heeft toegewezen, zoekt u de toepassings toewijzing van de gebruiker of groep door te navigeren naar het tabblad **gebruikers & groepen** van de toepassing, de toewijzing te selecteren en op de knop **referenties bijwerken** te klikken.

- Als de gebruiker zijn of haar eigen referenties heeft toegewezen, moet de gebruiker controleren of het **wacht woord niet is verlopen in de toepassing** . als dat het geval is, werkt u het **verlopen wacht woord** bij door u rechtstreeks aan te melden bij de toepassing.

  * Wanneer het wacht woord in de toepassing is bijgewerkt, vraagt u de gebruiker om te klikken op de knop **referenties bijwerken** op de **tegel toepassing** in het gedeelte **apps** van [mijn apps](https://myapps.microsoft.com/) om ze bij te werken naar de nieuwste bekende werk gebruikers naam en het meest recente wacht woord.

  * Als u of een andere beheerder de referenties voor deze gebruiker heeft toegewezen, zoekt u de toepassings toewijzing van de gebruiker of groep door te navigeren naar het tabblad **gebruikers & groepen** van de toepassing, de toewijzing te selecteren en op de knop **referenties bijwerken** te klikken.

- Zorg ervoor dat de browser uitbreiding mijn apps wordt uitgevoerd en is ingeschakeld in de browser van uw gebruiker.

- Zorg ervoor dat uw gebruikers zich niet bij de toepassing aanmelden vanuit mijn apps in **incognito, InPrivate of private**. De uitbrei ding mijn apps wordt niet ondersteund in deze modus.

Als de vorige suggesties niet werken, is het mogelijk dat er een wijziging is opgetreden aan de kant van de toepassing die de integratie van de toepassing met Azure AD tijdelijk heeft verbroken. Dit kan bijvoorbeeld gebeuren wanneer de leverancier van de toepassing een script op de pagina maakt dat anders werkt voor hand matig en geautomatiseerd invoeren, waardoor automatische integratie, zoals onze eigen, kan worden onderbroken. Gelukkig kan micro soft in veel gevallen samen werken met leveranciers van toepassingen om deze problemen snel op te lossen.

Micro soft heeft technologieën om automatisch te detecteren wanneer een toepassings integratie is verbroken. het is mogelijk dat u de problemen niet meteen kunt vinden of dat de problemen enige tijd in beslag nemen. Wanneer een integratie niet correct werkt, kunt u een ondersteunings aanvraag openen om deze zo snel mogelijk vast te krijgen. 

Daarnaast kunt **u, als u in contact komt met de leverancier van deze toepassing,** **hen onze manier sturen** zodat we met hen kunnen samen werken om hun toepassing systeem eigen te integreren met Azure Active Directory. U kunt de leverancier verzenden naar de [vermelding uw toepassing in de galerie met Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md) om ze te starten.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Controleren of de aanmeldings pagina van de toepassing onlangs is gewijzigd of een extra veld vereist

Als de aanmeldings pagina van de toepassing drastisch is gewijzigd, is het mogelijk dat onze integraties worden verbroken. Een voor beeld hiervan is wanneer een toepassings leverancier een aanmeld veld, een CAPTCHA of multi-factor Authentication toevoegt aan hun ervaringen. Gelukkig kan micro soft in veel gevallen samen werken met leveranciers van toepassingen om deze problemen snel op te lossen.

Micro soft heeft technologieën om automatisch te detecteren wanneer een toepassings integratie is verbroken. het is mogelijk dat u de problemen niet meteen kunt vinden of dat de problemen enige tijd in beslag nemen. Wanneer een integratie niet correct werkt, kunt u een ondersteunings aanvraag openen om deze zo snel mogelijk vast te krijgen. 

Daarnaast kunt **u, als u in contact komt met de leverancier van deze toepassing,** **hen onze manier sturen** zodat we met hen kunnen samen werken om hun toepassing systeem eigen te integreren met Azure Active Directory. U kunt de leverancier verzenden naar de [vermelding uw toepassing in de galerie met Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md) om ze te starten.

## <a name="capture-sign-in-fields-for-an-app"></a>Aanmeldings velden vastleggen voor een app

Het vastleggen van het registratie veld wordt alleen ondersteund voor aanmeldings pagina's met HTML-functionaliteit. Het wordt niet ondersteund voor niet-standaard aanmeldings pagina's, zoals die voor Adobe Flash of andere technologieën zonder HTML-functionaliteit.

Er zijn twee manieren om aanmeldings velden voor uw aangepaste apps vast te leggen:

- **Automatische registratie van het registratie veld** werkt goed met de meeste HTML-aanmeldings pagina's, *als ze bekende div-id's gebruiken* voor de velden gebruikers naam en wacht woord. De HTML-code op de pagina wordt opgevallen om DIV-Id's te vinden die overeenkomen met bepaalde criteria. Die meta gegevens worden opgeslagen zodat deze later opnieuw kunnen worden afgespeeld naar de app.

- **Hand matige registratie van** het registratie veld wordt gebruikt als de leverancier *van de app geen label heeft voor de aanmeldings invoer velden*. Hand matige vastleg ging wordt ook gebruikt als de leverancier *meerdere velden rendert die niet automatisch kunnen worden gedetecteerd*. Met Azure Active Directory (Azure AD) kunnen gegevens worden opgeslagen voor net zoveel velden als op de aanmeldings pagina, als u het weet waar deze velden zich op de pagina bevinden.

Over het algemeen kunt u de optie hand matig gebruiken als het vastleggen van automatische aanmeldings velden niet werkt.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Aanmeldings velden automatisch vastleggen voor een app

Voer de volgende stappen uit om eenmalige aanmelding op basis van een wacht woord te configureren met behulp van automatische registratie van het registratie veld:
1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.
2. Selecteer in het navigatie deel venster aan de linkerkant **alle services** om de Azure AD-extensie te openen.
3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.
4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.
5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.
   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de lijst **alle toepassingen** . Stel de optie **weer geven** in op alle toepassingen.
6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.
7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster aan de linkerkant.
8. Selecteer **aanmeldings modus op basis van wacht woorden** .
9. Voer de **aanmeldings-URL** in. Dit is de URL van de pagina waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldings velden op de pagina worden weer gegeven voor de URL die u opgeeft*.
10. Selecteer **Opslaan**.
    De pagina wordt automatisch uitgewisseld voor de invoer vakken gebruikers naam en wacht woord. U kunt Azure AD nu gebruiken voor het veilig verzenden van wacht woorden naar die app met behulp van de browser uitbreiding mijn apps.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Aanmeldings velden hand matig vastleggen voor een app

Als u hand matig aanmeldings velden wilt vastleggen, moet de browser extensie mijn apps zijn geïnstalleerd. Uw browser kan ook niet worden uitgevoerd in de modus *InPrivate*, *incognito* of *Private* .

Voer de volgende stappen uit om SSO op basis van een wacht woord te configureren voor een app door gebruik te maken van hand matige registratie van het registratie veld:
1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.
2. Selecteer in het navigatie deel venster aan de linkerkant **alle services** om de Azure AD-extensie te openen.
3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.
4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.
5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.
   > [!NOTE] 
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de lijst **alle toepassingen** . Stel de optie **weer geven** in op alle toepassingen.
6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.
7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster aan de linkerkant.
8. Selecteer **aanmeldings modus op basis van wacht woorden** .
9. Voer de **aanmeldings-URL** in. Dit is de pagina waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldings velden op de pagina worden weer gegeven voor de URL die u opgeeft*.
10. Selecteer **instellingen *&lt; &gt;* voor wacht woord voor eenmalige aanmelding configureren**.
11. Selecteer **aanmeldings velden hand matig detecteren**.
14. Selecteer **OK**.
15. Selecteer **Opslaan**.
16. Volg de instructies voor het gebruik van mijn apps.


## <a name="troubleshoot-problems"></a>Problemen oplossen

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Ik krijg de fout ' kan geen aanmeldings velden vinden bij die URL '

Dit fout bericht wordt weer gegeven wanneer automatische detectie van aanmeldings velden mislukt. Om het probleem op te lossen, kunt u hand matig de detectie van het aanmeldings veld proberen. Zie de [aanmeldings velden hand matig vastleggen voor een toepassings](#manually-capture-sign-in-fields-for-an-app) sectie van dit artikel.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Ik krijg de fout ' kan de configuratie voor eenmalige aanmelding niet opslaan '

Zelden: het bijwerken van de SSO-configuratie mislukt. Sla de configuratie opnieuw op om dit probleem op te lossen.

Als u de fout blijft ontvangen, opent u een ondersteunings aanvraag. Neem de informatie op die wordt beschreven in de details van de [Portal-melding weer geven](#view-portal-notification-details) en [Verzend meldings gegevens naar een ondersteunings technicus om Help](#send-notification-details-to-a-support-engineer-to-get-help) -onderwerpen van dit artikel te verkrijgen.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Ik kan de aanmeldings velden voor mijn app niet hand matig detecteren

U kunt de volgende problemen waarnemen wanneer hand matige detectie niet werkt:
- Het hand matige vastleg proces leek te werken, maar de vastgelegde velden zijn niet juist.
- De juiste velden worden niet gemarkeerd wanneer het vastleg proces wordt uitgevoerd.
- Tijdens het vastleggen gaat u naar de aanmeldings pagina van de app zoals verwacht, maar er gebeurt niets.
- Hand matige vastleg ging lijkt te werken, maar SSO gebeurt niet wanneer gebruikers naar de app vanuit mijn apps navigeren.

Als u een van deze problemen ondervindt, doet u het volgende:
- Zorg ervoor dat de nieuwste versie van de browser extensie mijn apps is *geïnstalleerd en ingeschakeld*.
- Zorg ervoor dat uw browser tijdens het vastleggen geen *incognito*, *InPrivate* of *Private* modus heeft. De uitbrei ding mijn apps wordt niet ondersteund in deze modus.
- Zorg ervoor dat uw gebruikers zich niet bij de app aanmelden vanuit mijn apps in *incognito*, *InPrivate* of *Private*.
- Probeer het hand matige vastleg proces opnieuw. Zorg ervoor dat de rode markeringen zich in de juiste velden bevinden.
- Als het hand matige vastleg proces niet meer reageert of als de aanmeldings pagina niet reageert, kunt u het hand matige vastleg proces opnieuw proberen. Maar deze keer, na het volt ooien van het proces, drukt u op de F12-toets om de ontwikkelaars console van uw browser te openen. Selecteer het tabblad **console** . Typ **Window. Location = "*&lt; de aanmeldings-URL die u hebt opgegeven bij het configureren van &gt; de app*"** en druk op ENTER. Hiermee wordt een omleiding van pagina's afgedwongen waarmee het proces wordt beëindigd en worden de opgenomen velden opgeslagen.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Ik kan geen andere gebruiker toevoegen aan mijn op wacht woord gebaseerde SSO-app

SSO-app op basis van wacht woorden heeft een limiet van 48 gebruikers. Daarom heeft het een limiet van 48 sleutels voor de combi natie van gebruikers naam en wacht woord per app.
Als u extra gebruikers wilt toevoegen, kunt u het volgende doen:
-   Een extra exemplaar van de app toevoegen
-   Gebruikers verwijderen die de app het eerst niet meer gebruiken

## <a name="request-support"></a>Ondersteuning aanvragen 
Als er een fout bericht wordt weer gegeven wanneer u SSO instelt en gebruikers toewijst, opent u een ondersteunings ticket. Voeg zo veel mogelijk van de volgende gegevens toe:

-   ID correlatie fout
-   UPN (e-mail adres van gebruiker)
-   TenantID
-   Browsertype
-   Tijd zone en tijd/tijd-frame wanneer de fout is opgetreden
-   Fiddler traceringen

### <a name="view-portal-notification-details"></a>Details van portal melding weer geven

Voer de volgende stappen uit om de details van een portal melding te bekijken:
1. Selecteer het pictogram **meldingen** (de Bell) in de rechter bovenhoek van de Azure Portal.
2. Selecteer een melding waarin een *fout* status wordt weer gegeven. (Ze hebben een rode '! '.)
   > [!NOTE]
   > U kunt geen meldingen selecteren die de status *geslaagd* of *in uitvoering* hebben.
3. Het deel venster met de **meldings Details** wordt geopend. Lees de informatie voor meer informatie over het probleem.
5. Als u nog hulp nodig hebt, kunt u de informatie delen met een ondersteunings technicus of de product groep. Selecteer het **Kopieer** pictogram rechts van het vak **Kopieer fout** om de meldings details te kopiëren om te delen.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Verzend Details van de melding naar een ondersteunings technicus om hulp te krijgen

Het is belang rijk dat u *alle* details die in deze sectie worden vermeld, deelt met ondersteuning zodat u snel aan de slag kunt. Als u deze wilt vastleggen, kunt u een scherm opname maken of een **Kopieer fout** selecteren.

De volgende informatie legt uit wat elk meldings item betekent en biedt voor beelden.

#### <a name="essential-notification-items"></a>Essentiële meldings items

- **Titel**: de beschrijvende titel van de melding.

   Voor beeld: *proxy-instellingen* van de toepassing

- **Beschrijving**: wat is er gebeurd als gevolg van de bewerking.

   Voor beeld: de *ingevoerde interne URL wordt al gebruikt door een andere toepassing.*

- **Meldings-id**: de unieke id van de melding.

    Voor beeld: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Client aanvraag-id**: de specifieke aanvraag-id die uw browser heeft gemaakt.

    Voor beeld: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Tijds tempel UTC**: de tijds tempel van wanneer de melding is opgetreden, in UTC.

    Voor beeld: *2017-03-23T19:50:43.7583681 z*

- **Interne trans actie-id**: de interne id die wordt gebruikt om de fout op onze systemen op te zoeken.

    Voor beeld: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: de gebruiker die de bewerking heeft uitgevoerd.

    Voor beeld: *tperkins \@ f128.info*

- **Tenant-id**: de unieke id van de Tenant waarvan de gebruiker die de bewerking heeft uitgevoerd, lid is.

    Voor beeld: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Gebruikers object-id**: de unieke id van de gebruiker die de bewerking heeft uitgevoerd.

    Voor beeld: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Gedetailleerde meldings items

- **Weergave naam**: (mag leeg zijn) een meer gedetailleerde weergave naam voor de fout.

    Voor beeld: *proxy-instellingen* van de toepassing

- **Status**: de specifieke status van de melding.

    Voor beeld: *mislukt*

- **Object-id**: (kan leeg zijn) de object-id waarmee de bewerking is uitgevoerd.

   Voor beeld: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Details**: de gedetailleerde beschrijving van wat er is gebeurd als gevolg van de bewerking.

    Voor beeld: *interne URL ' <https://bing.com/> ' is ongeldig omdat deze al in gebruik is.*

- **Kopieer fout**: Hiermee kunt u het **Kopieer pictogram** rechts van het tekstvak **Kopieer fout** selecteren om de details van de melding te kopiëren om de ondersteuning te helpen.

    Hierbij   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Een My Apps-implementatie plannen](my-apps-deployment-plan.md)
