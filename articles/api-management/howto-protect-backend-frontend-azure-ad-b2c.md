---
title: SPA-back-end beveiligen in azure API Management met Active Directory B2C
description: Beveilig een API met OAuth 2,0 door gebruik te maken van Azure Active Directory B2C, Azure API Management en eenvoudige verificatie die moet worden aangeroepen vanuit een Java script SPA met behulp van de PKCE ingeschakeld SPA-verificatie stroom.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: 812b54d10ea3cc3c405f534e36ac66abf3466808
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449285"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Beveiligd-wachtwoord verificatie beveiligen met OAuth 2,0, Azure Active Directory B2C en Azure API Management

In dit scenario ziet u hoe u uw Azure API Management-exemplaar configureert om een API te beveiligen.
We gebruiken de Azure AD B2C SPA-stroom (auth code + PKCE) om een token te verkrijgen, naast API Management om een Azure Functions back-end te beveiligen met behulp van EasyAuth.

## <a name="aims"></a>Ernaar

We gaan nu zien hoe API Management kunnen worden gebruikt in een vereenvoudigd scenario met Azure Functions en Azure AD B2C. U maakt een Java script-app (JS) die een API aanroept, waarmee gebruikers met Azure AD B2C worden aangemeld. Vervolgens gebruikt u de functies validate-JWT, CORS en frequentie limiet van API Management om de back-end-API te beveiligen.

Voor een diep gaande verdediging gebruiken we EasyAuth om het token opnieuw te valideren in de back-end-API en ervoor te zorgen dat API Management de enige service is die de Azure Functions back-end kan aanroepen.

## <a name="what-will-you-learn"></a>Wat leert u

> [!div class="checklist"]
> * Installatie van een app met één pagina en een back-end-API in Azure Active Directory B2C
> * Een Azure Functions back-end-API maken
> * Een Azure Functions-API importeren in azure API Management
> * De API beveiligen in azure API Management
> * De Azure Active Directory B2C autorisatie-eind punten aanroepen via de micro soft Identity platform libraries (MSAL.js)
> * Een HTML/vanille JS-toepassing met één pagina opslaan en deze uitvoeren vanuit een Azure Blob Storage-eind punt

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, hebt u het volgende nodig:

* Een Azure (StorageV2) Algemeen v2-opslag account voor het hosten van de front-end JS-app met één pagina.
* Een Azure API Management-exemplaar (een wille keurige laag werkt, met inbegrip van ' verbruik ', maar bepaalde functies die van toepassing zijn op het volledige scenario zijn niet beschikbaar in deze laag (frequentie limiet per sleutel en toegewezen virtueel IP-adres). deze beperkingen worden indien van toepassing hieronder genoemd.
* Een lege Azure function-app (met de V 3.1 .NET core runtime op basis van een verbruiks abonnement) om de aangeroepen API te hosten
* Een Azure AD B2C-Tenant die is gekoppeld aan een abonnement.

Hoewel u in de praktijk resources in dezelfde regio in de werk belasting van de productie omgeving zou gebruiken, is de implementatie fase niet van belang voor dit artikel.

## <a name="overview"></a>Overzicht

Hier volgt een illustratie van de onderdelen die in gebruik zijn en de stroom ertussen zodra dit proces is voltooid.
![Onderdelen in gebruik en flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Onderdelen in gebruik en flow")

Hier volgt een kort overzicht van de stappen:

1. De Azure AD B2C aanroepen (frontend, API Management) en API-toepassingen met bereiken maken en API-toegang verlenen
1. Het beleid voor registreren en aanmelden maken zodat gebruikers zich kunnen aanmelden met Azure AD B2C 
1. API Management configureren met de nieuwe Azure AD B2C-client-Id's en-sleutels om de autorisatie van OAuth2-gebruikers in de ontwikkelaars console in te scha kelen
1. De functie-API maken
1. De functie-API configureren om EasyAuth in te scha kelen met de nieuwe Azure AD B2C-client-ID en-sleutels en om de APIM-VIP te vergren delen
1. De API-definitie in API Management maken
1. Oauth2 instellen voor de configuratie van de API Management-API
1. Stel het **CORS** -beleid in en voeg het beleid **valideren-JWT** toe om het OAuth-token te valideren voor elke inkomende aanvraag
1. De aanroepende toepassing bouwen om de API te gebruiken
1. Het JS-beveiligd-wachtwoord verificatie-voor beeld uploaden
1. De voorbeeld-JS-client-app configureren met de nieuwe Azure AD B2C-client-ID en-sleutels 
1. De client toepassing testen

   > [!TIP]
   > We gaan zeer enkele stukjes informatie en sleutels vastleggen, maar als we dit document door lopen, kan het handig zijn om een tekst editor te openen om de volgende configuratie-items tijdelijk op te slaan.  
   >
   > B2C BACK-END-CLIENT-ID:  
   > B2C BACK-END-CLIENT GEHEIME SLEUTEL:  
   > B2C BACK-END-API-SCOPE-URI:  
   > B2C-FRONTEND-CLIENT-ID:  
   > EIND PUNT-URI VAN B2C-GEBRUIKERS STROOM:  
   > B2C-BEKEND OPENID CONNECT-EIND PUNT:   
   > B2C-BELEIDS naam: Frontendapp_signupandsignin functie-URL:  
   > APIM API-BASIS-URL: URL VAN HET PRIMAIRE EIND PUNT VOOR OPSLAG:  

## <a name="configure-the-backend-application"></a>De back-end-toepassing configureren

Open de Blade Azure AD B2C in de portal en voer de volgende stappen uit.

1. Het tabblad **app-registraties** selecteren
1. Klik op de knop nieuwe registratie.
1. Kies web in het selectie vakje omleidings-URI.
1. Stel nu de weergave naam in, kies iets uniek en relevant voor de service die wordt gemaakt. In dit voor beeld gebruiken we de naam back-end-toepassing.
1. Tijdelijke aanduidingen gebruiken voor de antwoord-url's, zoals ' https://jwt.ms ' (een micro soft-site met een token-decodering), worden deze url's later bijgewerkt.
1. Zorg ervoor dat u de optie accounts in een id-provider of organisatie Directory (voor het verifiëren van gebruikers met gebruikers stromen) hebt geselecteerd.
1. Schakel voor dit voor beeld het selectie vakje ' toestemming van de beheerder verlenen ' uit omdat we vandaag geen offline_access machtigingen nodig hebben.
1. Klik op 'Registreren'.
1. Registreer de back-end-client-ID voor later gebruik (weer gegeven onder de client-ID van de toepassing).
1. Selecteer het tabblad *certificaten en geheimen* (onder beheren) en klik vervolgens op nieuw client geheim om een verificatie sleutel te genereren (accepteer de standaard instellingen en klik op toevoegen).
1. Als u op toevoegen klikt, kopieert u de sleutel (onder waarde) ergens anders als ' back-end client Secret ': Houd er rekening mee dat dit dialoog venster de enige kans is om deze sleutel te kopiëren.
1. Selecteer nu het tabblad *een API beschikbaar* maken (onder beheren).
1. U wordt gevraagd de AppID-URI in te stellen, de standaard waarde te selecteren en op te nemen.
1. Het bereik ' Hello ' maken en een naam geven voor de functie-API. u kunt de woord groep ' Hello ' gebruiken voor alle opties die kunnen worden ingevoerd, de waarde van de gevulde volledige Scope waarden vastleggen en vervolgens op bereik toevoegen.
1. Ga terug naar de hoofdmap van de Blade Azure AD B2C door de breadcrumb ' Azure AD B2C ' te selecteren in de linkerbovenhoek van de portal.

   > [!NOTE]
   > Azure AD B2C scopes zijn effectief in uw API dat andere toepassingen vanaf hun toepassingen toegang kunnen vragen tot via de Blade API-toegang, waardoor u net zo zojuist toepassings machtigingen hebt gemaakt voor uw opgeroepen API.

## <a name="configure-the-frontend-application"></a>De front-end-toepassing configureren

1. Het tabblad **app-registraties** selecteren
1. Klik op de knop nieuwe registratie.
1. Kies ' single page Application (SPA) ' in het selectie vakje omleidings-URI.
1. Stel nu de weergave naam en de AppID-URI in, kies iets uniek en relevant voor de front-end-toepassing die deze AAD B2C app-registratie zal gebruiken. In dit voor beeld kunt u ' front-end-toepassing ' gebruiken
1. Laat, conform de eerste registratie van de app, de ondersteunde account typen selecteren standaard (verificatie van gebruikers met gebruikers stromen)
1. Tijdelijke aanduidingen gebruiken voor de antwoord-url's, zoals ' https://jwt.ms ' (een micro soft-site met een token-decodering), worden deze url's later bijgewerkt.
1. Zorg ervoor dat het vak toestemming beheerder verlenen is getickt
1. Klik op 'Registreren'.
1. Registreer de client-ID voor de frontend voor later (weer gegeven onder de client-ID van de toepassing).
1. Schakel over naar het tabblad *API-machtigingen* .
1. Verleen toegang tot de back-end-toepassing door te klikken op een machtiging toevoegen en vervolgens ' mijn Api's ', selecteer de back-end-toepassing, selecteer machtigingen, selecteer het bereik dat u hebt gemaakt in de vorige sectie en klik op machtigingen toevoegen.
1. Klik op toestemming van de beheerder verlenen voor {Tenant} en klik op Ja in het pop-updialoogvenster. In deze pop-up wordt de ' frontend-toepassing ' toegestuurd om de machtiging ' Hello ' te gebruiken die is gedefinieerd in de back-end-toepassing die u eerder hebt gemaakt.
1. Alle machtigingen moeten nu worden weer gegeven voor de app als een groen streepje onder de kolom Status

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Een gebruikers stroom voor registreren en aanmelden maken

1. Ga terug naar de hoofdmap van de Blade B2C door de Azure AD B2C brood kruimel te selecteren.
1. Schakel over naar het tabblad Gebruikersstromen (onder beleids regels).
1. Klik op ' nieuwe gebruikers stroom '
1. Kies het type gebruikers stroom registreren en aanmelden, en selecteer aanbevolen en klik vervolgens op maken
1. Geef het beleid een naam en noteer dit voor later. Voor dit voor beeld kunt u ' Frontendapp_signupandsignin ' gebruiken, maar dit wordt voorafgegaan door ' B2C_1_ ' om ' B2C_1_Frontendapp_signupandsignin ' te maken
1. Schakel onder ' id-providers ' en ' lokale accounts ' het selectie vakje e-mail aanmelding (of ' gebruikers-ID aanmelden ' in, afhankelijk van de configuratie van uw B2C-Tenant) en klik op OK. Deze configuratie is omdat we lokale B2C-accounts registreren en niet uitstellen naar een andere ID-provider (zoals een sociale ID-provider) voor het gebruik van het bestaande Social Media-account van een gebruiker.
1. Behoud de standaard instellingen voor MFA en voorwaardelijke toegang.
1. Klik onder ' gebruikers kenmerken en claims ' op ' meer weer geven '... ' Kies vervolgens de claim opties die u uw gebruikers wilt geven en hebben geretourneerd in het token. Controleer ten minste ' weergave naam ' en ' e-mail adres ' om te retour neren, met ' weergave naam ' en ' e-mail adressen ' om terug te gaan (Let op het feit dat u EmailAddress, enkelvoud en vragen om e-mail adressen, meerdere), en klik op OK en klik vervolgens op maken.
1. Klik op de gebruikers stroom die u hebt gemaakt in de lijst en klik vervolgens op de knop gebruikers stroom uitvoeren.
1. Met deze actie opent u de Blade gebruikers stroom uitvoeren, selecteert u de front-end-toepassing, kopieert u het eind punt voor de gebruikers stroom en slaat u het voor later op.
1. Kopieer en sla de koppeling bovenaan op, neem deze op als het ' well-bekende OpenID Connect-configuratie-eind punt voor later gebruik.

   > [!NOTE]
   > Met B2C-beleid kunt u de Azure AD B2C aanmeldings eindpunten beschikbaar maken voor het vastleggen van verschillende gegevens onderdelen en gebruikers op verschillende manieren kunnen aanmelden.
   > 
   > In dit geval hebben we een registratie-of aanmeldings stroom (beleid) geconfigureerd. Dit is ook een duidelijk bekend configuratie-eind punt, in beide gevallen dat ons gemaakte beleid is geïdentificeerd in de URL door de query teken reeks parameter "p =".  
   >
   > Zodra dit is gebeurd, hebt u nu een functionele bedrijfs activiteit voor het identiteits platform van de consument waarmee gebruikers worden ondertekend in meerdere toepassingen.

## <a name="build-the-function-api"></a>De functie-API maken

1. Ga terug naar uw standaard Azure AD-Tenant in de Azure Portal zodat we de items in uw abonnement opnieuw kunnen configureren.
1. Ga naar de Blade functie-apps van de Azure Portal, open uw lege functie-app en klik vervolgens op ' functions ' en op toevoegen.
1. Kies in de weer gegeven flyout ' ontwikkelen in portal ' onder ' Selecteer een sjabloon ' en kies vervolgens ' HTTP trigger ', onder sjabloon Details de naam ' Hallo ' met autorisatie niveau ' function ' en selecteer vervolgens toevoegen.
1. Ga naar de Blade code en test en kopieer de voorbeeld code van hieronder *over de bestaande code* die wordt weer gegeven.
1. Selecteer Opslaan.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!TIP]
   > De c#-script functie code die u zojuist hebt geplakt, registreert eenvoudigweg een regel in de functions-logboeken en retourneert de tekst ' Hallo wereld ' met enkele dynamische gegevens (de datum en tijd).

1. Selecteer integratie in de linker Blade en klik vervolgens op de koppeling http (req) in het vak trigger.
1. Schakel in de vervolg keuzelijst ' geselecteerde HTTP-methoden ' de HTTP POST-methode uit, laat alleen ophalen ingeschakeld en klik vervolgens op opslaan.
1. Ga terug naar het tabblad code + test, klik op functie-URL ophalen, kopieer de URL die wordt weer gegeven en sla deze op voor later.

   > [!NOTE]
   > Met de bindingen die u zojuist hebt gemaakt, wordt gereageerd op anonieme HTTP GET-aanvragen naar de URL die u zojuist hebt gekopieerd ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ). Nu hebben we een schaal bare serverloze https API, die kan leiden tot het retour neren van een zeer eenvoudige nettolading.
   >
   > U kunt de aanroep van deze API vanuit een webbrowser nu testen met uw versie van de URL hierboven die u zojuist hebt gekopieerd en opgeslagen. U kunt ook de query teken reeks parameters "? code = secretkey" van de URL verwijderen en opnieuw testen om te bewijzen dat Azure Functions een 401-fout retourneert.

## <a name="configure-and-secure-the-function-api"></a>De functie-API configureren en beveiligen

1. Twee extra gebieden in de functie-app moeten worden geconfigureerd (autorisatie en netwerk beperkingen).
1. Laten we eerst verificatie/autorisatie configureren, dus ga terug naar de hoofd Blade van de functie-app via de breadcrumb.
1. Selecteer vervolgens ' verificatie/autorisatie ' (onder instellingen).
1. Schakel de functie voor App Service verificatie in.
1. De actie die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd, moet worden ingesteld op aanmelden met Azure Active Directory.
1. Kies bij verificatie providers de optie Azure Active Directory.
1. Kies Geavanceerd in de schakel optie voor de beheer modus.
1. Plak de client-ID van de back-end-app (van Azure AD B2C) in het vak client-ID
1. Plak het bekende open-id-configuratie-eind punt van het beleid registreren en aanmelden in het vak URL van de certificaat verlener (deze configuratie is eerder vastgelegd).
1. Klik op geheim weer geven en plak het client geheim van de back-end in het desbetreffende vak.
1. Selecteer OK. Hiermee gaat u terug naar de Blade voor het selecteren van de identiteits provider/het scherm.
1. Zorg ervoor dat het [token archief](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#token-store) is ingeschakeld onder Geavanceerde instellingen (standaard).
1. Klik op opslaan (in de linkerbovenhoek van de Blade).

   > [!IMPORTANT]
   > Nu is uw functie-API geïmplementeerd en moeten 401-reacties worden gegenereerd als de juiste JWT niet is opgegeven als een autorisatie: Bearer en moet gegevens retour neren wanneer er een geldige aanvraag wordt ingediend.  
   > U hebt extra ingrijpende beveiliging in EasyAuth toegevoegd door de optie aanmelden met Azure AD te configureren voor het afhandelen van niet-geverifieerde aanvragen. Houd er rekening mee dat hiermee het gedrag van niet-geautoriseerde aanvragen wordt gewijzigd tussen de back-end-functie-app en front-end-wachtwoord verificatie als EasyAuth een omleiding van 302 naar AAD verzendt in plaats van een 401 niet-geautoriseerde reactie, wij zullen dit corrigeren door API Management later te gebruiken.  
   >
   > Er is nog geen IP-beveiliging toegepast. Als u een geldig sleutel-en OAuth2-token hebt, kunt u dit op elk gewenst punt aanroepen. het is dan ook belang rijk dat alle aanvragen via API Management worden afgedwongen.  
   > 
   > Als u gebruikmaakt van een APIM verbruiks laag, [is er geen toegewezen Azure-API Management virtueel IP-adres](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) dat kan worden weer geven met de toegangs beperkingen van de functies. In de standaard-SKU van Azure API Management en boven [de VIP bevindt zich één Tenant en voor de levens duur van de resource](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). Voor de laag van Azure API Management verbruik kunt u uw API-aanroepen vergren delen via de gedeelde geheime functie sleutel in het gedeelte van de URI dat u hierboven hebt gekopieerd. Voor de verbruiks laag-stap 12-17 hieronder zijn ook niet van toepassing.

1. Sluit de Blade verificatie/autorisatie 
1. Open de *blade API management van de portal* en open vervolgens *uw exemplaar*.
1. Registreer de privé-VIP die wordt weer gegeven op het tabblad Overzicht.
1. Ga terug naar de *blade Azure functions van de portal* en open vervolgens *uw exemplaar* opnieuw.
1. Selecteer netwerken en selecteer vervolgens toegangs beperkingen configureren.
1. Klik op regel toevoegen en voer in de notatie xx. xx. xx. xx/32 het VIP in dat is gekopieerd in stap 3.
1. Als u wilt door gaan met de functies Portal en de onderstaande optionele stappen wilt uitvoeren, moet u uw eigen open bare IP-adres of CIDR-bereik ook toevoegen.
1. Zodra er een vermelding is toegestaan in de lijst, voegt Azure een regel voor impliciete weigering toe om alle andere adressen te blok keren.

U moet de met CIDR opgemaakte blokken met adressen toevoegen aan het paneel IP-beperkingen. Wanneer u één adres wilt toevoegen, zoals het API Management VIP, moet u dit toevoegen met de notatie xx. xx. xx. xx/32.

   > [!NOTE]
   > De functie-API kan nu niet worden aangeroepen vanaf elke andere locatie dan via API Management of uw adres.

1. Open de *blade API Management* en open vervolgens *uw exemplaar*.
1. Selecteer de Blade Api's (onder Api's).
1. Kies in het deel venster een nieuwe API toevoegen de optie functie-app en selecteer vervolgens ' volledig ' boven aan de pop-up.
1. Klik op Bladeren, kies de functie-app die u als host voor de API in en klik op selecteren. Klik vervolgens nogmaals op selecteren.
1. Geef de API een naam en beschrijving voor het interne gebruik van API Management en voeg deze toe aan het ' onbeperkt ' product.
1. Kopieer en noteer de API ' basis-URL ' en klik op maken.
1. Klik op het tabblad instellingen en schakel onder abonnement-switch het selectie vakje ' abonnement vereist ' uit omdat we de OAuth JWT-token in dit geval zullen gebruiken om de limiet te bepalen. Houd er rekening mee dat als u gebruikmaakt van de laag verbruik, dit nog steeds vereist is in een productie omgeving. 

   > [!TIP]
   > Als de verbruiks laag van APIM wordt gebruikt, is het onbeperkte product niet beschikbaar als out-of-Box. Ga in plaats daarvan naar "producten" onder "Api's" en druk op toevoegen.  
   > Typ "onbeperkt" als de product naam en-beschrijving en selecteer de API die u zojuist hebt toegevoegd vanuit de bijschriften "+" Api's in de linkerbenedenhoek van het scherm. Schakel het selectie vakje gepubliceerd in. Laat de rest als standaard. Klik ten slotte op de knop maken. Hierdoor is het ' onbeperkt ' product gemaakt en toegewezen aan uw API. U kunt uw nieuwe product later aanpassen.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>De juiste instellingen voor het opslag eindpunt configureren en vastleggen

1. Open de Blade opslag accounts in de Azure Portal 
1. Selecteer het account dat u hebt gemaakt en selecteer de Blade ' statische website ' in de sectie instellingen (als u geen optie voor een statische website ziet, controleert u of u een v2-account hebt gemaakt).
1. Stel de functie voor statische webhosting in op ingeschakeld en stel de naam van het index document in op index.html en klik vervolgens op opslaan.
1. Noteer de inhoud van het primaire eind punt voor later, omdat deze locatie waar de frontend-site wordt gehost.

   > [!TIP]
   > U kunt het herschrijven van Azure Blob Storage + CDN of de Azure App Service voor het hosten van de statische website-hosting functie van het beveiligd-op-een Blob Storage, een standaard container bieden om statische webinhoud/HTML/JS/CSS van Azure Storage te gebruiken en een standaard pagina voor ons voor nul werk af te leiden.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Het **CORS** en **Validate-JWT-** beleid instellen

> De volgende secties moeten worden gevolgd, ongeacht de APIM-laag die wordt gebruikt. De URL van het opslag account is afkomstig uit het opslag account dat u beschikbaar hebt gemaakt op basis van de vereisten boven aan dit artikel.
1. Ga naar de Blade API management van de portal en open uw exemplaar.
1. Selecteer Api's en selecteer vervolgens alle Api's.
1. Klik onder ' binnenkomende verwerking ' op de knop code weergave ' </> ' om de beleids editor weer te geven.
1. Bewerk de sectie binnenkomend en plak de onderstaande XML, zodat deze er als volgt uitziet.
1. Vervang de volgende para meters in het beleid
1. {PrimaryStorageEndpoint} (Het ' primaire opslag eindpunt ' dat u in de vorige sectie hebt gekopieerd), {b2cpolicy-well-OpenID Connect} (het ' well-bekende OpenID Connect-configuratie-eind punt ' dat u eerder hebt gekopieerd) en {back-end-API-Application-client-id} (de B2C-toepassings-ID voor de **back-end-API**) met de juiste waarden eerder opgeslagen.
1. Als u gebruikmaakt van de verbruiks laag van API Management, moet u beide beleids regels voor frequentie limieten verwijderen. dit beleid is niet beschikbaar wanneer u de verbruiks laag van Azure API Management gebruikt.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Azure API Management kan nu reageren op cross-Origin-aanvragen van uw Java script-SPA-apps en voert beperkingen, het beperken van de frequentie en vooraf valideren van het JWT-verificatie token dat wordt door gegeven voordat de aanvraag wordt doorgestuurd naar de functie-API.
   > 
   > Gefeliciteerd, u hebt nu Azure AD B2C, API Management en Azure Functions samen werken om een API te publiceren, beveiligen en gebruiken!

   > [!TIP]
   > Als u de laag met het API Management verbruik gebruikt en u het JWT-onderwerp of het inkomende IP-adres niet beperkt (de oproep frequentie beperken door het sleutel beleid wordt momenteel niet ondersteund voor de laag verbruik), kunt u de limiet voor de aanroep frequentie [beperken.](./api-management-access-restriction-policies.md#LimitCallRate)  
   > Zoals in dit voor beeld een Java script-toepassing met één pagina is, gebruiken we de API Management sleutel alleen voor tarieven voor het beperken en factureren. De daad werkelijke autorisatie en verificatie worden verwerkt door Azure AD B2C en worden ingekapseld in de JWT, die twee maal wordt gevalideerd, eenmaal per API Management en vervolgens door de back-end Azure-functie.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Het Java script-voor beeld-beveiligd-wachtwoord uploaden naar statische opslag

1. Selecteer op de Blade opslag account de Blade containers uit de sectie BLOB-service en klik op de $web container die wordt weer gegeven in het rechterdeel venster.
1. Sla de onderstaande code op in een lokaal bestand op uw computer als index.html en upload het bestand index.html naar de $web container.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Blader naar het primaire eind punt van de statische website dat u eerder in de laatste sectie hebt opgeslagen.

   > [!NOTE]
   > Gefeliciteerd, u hebt zojuist een Java script-app met één pagina geïmplementeerd voor het Azure Storage van statische inhouds hosting.  
   > Omdat de JS-app nog niet is geconfigureerd met uw Azure AD B2C Details: de pagina werkt nog niet als u deze opent.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>De Java script-beveiligd-wachtwoord verificatie voor Azure AD B2C configureren

1. Nu weten we wat alles is: we kunnen het beveiligd-wachtwoord verificatie configureren met het juiste API Management API-adres en de juiste Azure AD B2C toepassing/client-Id's.
1. Ga terug naar de Blade Azure Portal opslag 
1. Selecteer ' containers ' (onder instellingen) 
1. Selecteer de container ' $web ' in de lijst
1. index.html-BLOB selecteren in de lijst 
1. Klik op bewerken 
1. Werk de verificatie waarden in het gedeelte msal-configuratie bij zodat deze overeenkomen met de *front-end-* toepassing die u eerder hebt geregistreerd bij B2C. Gebruik de code opmerkingen voor tips over hoe de configuratie waarden moeten worden weer geven.
De waarde van de *Authority* moet de volgende indeling hebben:-https://{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}. onmicrosoft. com}/{signupandsigninpolicyname} als u de namen van de voor beelden hebt gebruikt en uw B2C-Tenant ' Contoso ' heet, zou u de instantie kunnen verwachten https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin .
1. Stel de API-waarden zo in dat deze overeenkomen met uw back-end-adres (de API-basis-URL die u eerder hebt vastgelegd en de b2cScopes-waarden eerder zijn vastgelegd voor de *back-end-toepassing*).
1. Klik op Opslaan

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>De omleidings-Uri's voor de Azure AD B2C frontend-app instellen

1. Open de Blade Azure AD B2C en navigeer naar de toepassings registratie voor de Java script-frontend-toepassing.
1. Klik op ' Uri's omleiden ' en verwijder de tijdelijke aanduiding ' ' die u https://jwt.ms eerder hebt opgegeven.
1. Voeg een nieuwe URI toe voor het primaire (opslag)-eind punt (min de afsluitende slash).

   > [!NOTE]
   > Deze configuratie resulteert in een client van de front-end-toepassing die een toegangs token ontvangt met de juiste claims van Azure AD B2C.  
   > Het beveiligd-wachtwoord verificatie kan dit als Bearer-token toevoegen aan de https-header in de aanroep van de back-end-API.  
   > 
   > API Management vooraf het token, de frequentie limiet voor het aantal aanroepen naar het eind punt valideren door het onderwerp van de JWT die is uitgegeven door de Azure-ID (de gebruiker) en het IP-adres van de aanroeper (afhankelijk van de servicelaag van API Management, zie de bovenstaande opmerking) voordat u de aanvraag doorstuurt naar de ontvangende Azure function-API, waarbij u de beveiligings sleutel functions  
   > Het beveiligd-wachtwoord verificatie bericht wordt weer gegeven in de browser.
   >
   > *Gefeliciteerd, u hebt Azure AD B2C, Azure API Management Azure Functions Azure App Service autorisatie geconfigureerd om te werken in perfecte harmonieën!*

Nu we een eenvoudige app met een eenvoudige beveiligde API hebben, gaan we deze testen.

## <a name="test-the-client-application"></a>De client toepassing testen

1. Open de URL van de voor beeld-app die u hebt genoteerd in het opslag account dat u eerder hebt gemaakt.
1. Klik in de rechter bovenhoek op aanmelden. Klik op deze pagina om uw Azure AD B2C registreren of aanmelden profiel in te stellen.
1. De app moet u de naam van uw B2C-profiel.
1. Klik nu op API aanroepen en de pagina moet worden bijgewerkt met de waarden die terug worden verzonden vanuit uw beveiligde API.
1. Als u *herhaaldelijk* op de knop API aanroepen klikt en u uitvoert in de laag Developer of boven aan API Management, moet u er rekening mee houden dat uw oplossing de frequentie van de API kan beperken en dat deze functie moet worden gerapporteerd in de app met een geschikt bericht.

## <a name="and-were-done"></a>En we zijn klaar

De bovenstaande stappen kunnen worden aangepast en bewerkt om veel verschillende manieren van Azure AD B2C met API Management mogelijk te maken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Active Directory en OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Bekijk meer [Video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API management.
* Zie [wederzijdse verificatie van certificaten](api-management-howto-mutual-certificates.md)voor andere manieren om uw back-end-service te beveiligen.
* [Een API Management service-exemplaar maken](get-started-create-service-instance.md).
* [Uw eerste API beheren](import-and-publish.md).
