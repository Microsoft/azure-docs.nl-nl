---
title: Beveilig SPA-back-end met OAuth 2,0 door gebruik te maken van Azure Active Directory B2C en Azure API Management.
description: Beveilig een API met OAuth 2,0 door gebruik te maken van Azure Active Directory B2C, Azure API Management en eenvoudige verificatie die moet worden aangeroepen vanuit een Java script SPA.
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
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new, devx-track-js
ms.openlocfilehash: b157200b03bdc89e00bfa5c8264d78baf24875c1
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609119"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Beveiligd-wachtwoord verificatie beveiligen met OAuth 2,0, Azure Active Directory B2C en Azure API Management

In dit scenario ziet u hoe u uw Azure API Management-exemplaar configureert om een API te beveiligen.
We gebruiken het OAuth 2,0-protocol met Azure AD B2C, naast API Management om een Azure Functions back-end te beveiligen met behulp van EasyAuth.

## <a name="aims"></a>Ernaar
We gaan nu zien hoe API Management kunnen worden gebruikt in een vereenvoudigd scenario met Azure Functions en Azure AD B2C. U maakt een Java script-app (JS) waarmee een API wordt aangeroepen, waarmee de gebruiker zich aanmeldt met Azure AD B2C. Vervolgens gebruikt u de validatie-JWT-beleids functies van API Management om de back-end-API te beveiligen.

Voor een diep gaande verdediging gebruiken we vervolgens EasyAuth om het token opnieuw te valideren in de back-end-API.

## <a name="prerequisites"></a>Vereisten
Als u de stappen in dit artikel wilt volgen, hebt u het volgende nodig:
* Een Azure (StorageV2) Algemeen v2-opslag account voor het hosten van de front-end JS-app met één pagina
* Een Azure API Management-exemplaar 
* Een lege Azure function-app (waarop de v2 .NET core-runtime wordt uitgevoerd op een Windows-verbruiks abonnement) om de aangeroepen API te hosten
* Een Azure AD B2C-Tenant, gekoppeld aan een abonnement 

Hoewel u in de praktijk resources in dezelfde regio in de werk belasting van de productie omgeving zou gebruiken, is de implementatie fase niet van belang voor dit artikel.

## <a name="overview"></a>Overzicht
Hier volgt een illustratie van de onderdelen die in gebruik zijn en de stroom ertussen zodra dit proces is voltooid.
![Onderdelen in gebruik en flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Onderdelen in gebruik en flow")

Hier volgt een kort overzicht van de stappen:

1. De Azure AD B2C aanroepen (frontend, API Management) en API-toepassingen met bereiken maken en API-toegang verlenen
1. Het beleid voor registreren of aanmelden maken zodat gebruikers zich kunnen aanmelden met Azure AD B2C 
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

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C configureren 
Open de Blade Azure AD B2C in de portal en voer de volgende stappen uit.
1. Selecteer het tabblad **toepassingen** 
1. Klik op de knop toevoegen en maak drie toepassingen voor de volgende doel einden
   * De frontend-client.
   * De back-end-functie-API.
   * Beschrijving Het API Management ontwikkelaars Portal (tenzij u Azure API Management uitvoert in de laag verbruik, verderop in dit scenario).
1. Stel WebApp/Web API in voor alle drie toepassingen en stel impliciete stroom toestaan in op Ja voor alleen de frontend-client.
1. Stel nu de App-ID-URI in, kies iets uniek en relevant voor de service die wordt gemaakt.
1. Tijdelijke aanduidingen voor de antwoord-url's gebruiken, bijvoorbeeld https://localhost , zullen deze url's later worden bijgewerkt.
1. Klik op maken en herhaal stap 2-5 voor elk van de drie bovenstaande apps, noteer de AppID-URI, naam en toepassings-ID voor later gebruik voor alle drie de apps.
1. Open de API Management ontwikkelaars Portal-toepassing in de lijst met toepassingen en selecteer het tabblad *sleutels* (onder algemeen) en klik vervolgens op sleutel genereren om een verificatie sleutel te genereren.
1. Nadat u op Opslaan hebt geklikt, noteert u de sleutel ergens veilig voor later gebruik: Houd er rekening mee dat deze locatie de enige kans is om deze sleutel weer te geven en te kopiëren.
1. Selecteer nu het tabblad *gepubliceerde bereiken* (onder API-toegang)
1. Maak een bereik en geef een naam voor de functie-API op en noteer het bereik en de volledige waarde van het bereik. Klik vervolgens op opslaan.
   > [!NOTE]
   > Azure AD B2C scopes zijn effectief in uw API dat andere toepassingen vanaf hun toepassingen toegang kunnen vragen tot via de Blade API-toegang, waardoor u net zo zojuist toepassings machtigingen hebt gemaakt voor uw opgeroepen API.
1. Open de andere twee toepassingen en zoek vervolgens op het tabblad *API-toegang* .
1. Verleen hen toegang tot het back-end-API-bereik en de standaard die al aanwezig was (' toegang tot het profiel van de gebruiker ').
1. Genereer hen een belang rijke sleutel door het tabblad *sleutels* onder algemeen te selecteren om een verificatie sleutel te genereren en deze sleutels op een later tijdstip te registreren.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Een gebruikers stroom voor registreren of aanmelden maken
1. Teruggaan naar de hoofdmap (of overzicht) van de Blade Azure AD B2C 
1. Selecteer vervolgens ' Gebruikersstromen (Policies) ' en klik op ' nieuwe gebruikers stroom '.
1. Kies het type gebruikers stroom registreren en aanmelden
1. Geef het beleid een naam en noteer dit voor later.
1. Klik vervolgens onder ' id-providers ' op ' aanmelden bij gebruikers-ID ' (dit kan zeggen ' e-mail registratie ') en klik op OK. 
1. Klik onder ' gebruikers kenmerken en claims ' op ' meer weer geven '... ' Kies vervolgens de claim opties die u uw gebruikers wilt geven en hebben geretourneerd in het token. Controleer ten minste de weergave naam en het e-mail adres voor het verzamelen en retour neren, klik op OK en klik vervolgens op maken.
1. Selecteer het beleid dat u hebt gemaakt in de lijst en klik vervolgens op de knop gebruikers stroom uitvoeren.
1. Met deze actie opent u de Blade gebruikers stroom uitvoeren, selecteert u de frontend-toepassing en noteert u het adres van het b2clogin.com-domein dat wordt weer gegeven onder de vervolg keuzelijst voor ' domein selecteren '.
1. Klik bovenaan op de koppeling om het ' bekende OpenID Connect-configuratie-eind punt ' te openen en noteer de authorization_endpoint-en token_endpoint waarden en de waarde van de koppeling zelf als het bekende OpenID Connect-configuratie-eind punt.

   > [!NOTE]
   > Met B2C-beleid kunt u de Azure AD B2C aanmeldings eindpunten beschikbaar maken voor het vastleggen van verschillende gegevens onderdelen en gebruikers op verschillende manieren kunnen aanmelden. In dit geval hebben we een registratie-of aanmeldings eindpunt geconfigureerd, die een goed bekend configuratie-eind punt had, die specifiek is gemaakt met de para meter p = van de URL.
   > 
   > Zodra dit is voltooid, beschikt u nu over functionele bedrijven voor het identiteits platform van de consument waarmee gebruikers worden ondertekend in meerdere toepassingen. 
   > Als u op de knop gebruikers stroom uitvoeren klikt, kunt u hier klikken (om door te gaan met het registreren of aanmelden) en een idee krijgen van wat het in de praktijk zal doen, maar de omleidings stap aan het einde zal mislukken omdat de app nog niet is geïmplementeerd.

## <a name="build-the-function-api"></a>De functie-API maken
1. Ga terug naar uw standaard Azure AD-Tenant in de Azure Portal zodat de items in uw abonnement opnieuw kunnen worden geconfigureerd 
1. Ga naar de Blade functie-apps van de Azure Portal, open uw lege functie-app en maak een nieuwe In-Portal de webhook + API-functie via de Snelstartgids.
1. Plak de voorbeeld code hieronder in run. CSX over de bestaande code die wordt weer gegeven.

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

   > [!NOTE]
   > De c#-script functie code die u zojuist hebt geplakt, registreert eenvoudigweg een regel in de functions-logboeken en retourneert de tekst ' Hallo wereld ' met enkele dynamische gegevens (de datum en tijd).

3. Selecteer ' integreren ' op de Blade links en selecteer vervolgens ' Geavanceerde editor ' in de rechter bovenhoek van het deel venster.
4. Plak de voorbeeld code hieronder over de bestaande JSON.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Ga terug naar het tabblad HttpTrigger1, klik op functie-URL ophalen en kopieer de URL die wordt weer gegeven.

   > [!NOTE]
   > Met de bindingen die u zojuist hebt gemaakt, wordt gereageerd op anonieme HTTP GET-aanvragen naar de URL die u zojuist hebt gekopieerd. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) We hebben nu een schaal bare serverloze https API, waarmee een zeer eenvoudige nettolading kan worden geretourneerd.
   > U kunt nu de aanroep van deze API vanuit een webbrowser met behulp van de bovenstaande URL testen. u kunt ook het geheime deel van de URL verwijderen en bewijzen dat Azure Functions een 401-fout retourneert.

## <a name="configure-and-secure-the-function-api"></a>De functie-API configureren en beveiligen
1. Twee extra gebieden in de functie-app moeten worden geconfigureerd (verificatie en netwerk beperkingen).
1. Laten we eerst verificatie/autorisatie configureren, dus klik op de naam van de functie-app (naast het &lt; &gt; pictogram Z-functies) om de pagina overzicht weer te geven.
1. Selecteer vervolgens het tabblad platform functies en selecteer verificatie/autorisatie.
1. Schakel de functie voor App Service verificatie in.
1. Kies bij verificatie providers de optie Azure Active Directory en kies Geavanceerd in de schakel optie voor de beheer modus.
1. Plak de toepassings-ID van de back-end-functie-API (van Azure AD B2C naar het vak client-ID)
1. Plak het bekende open-id-configuratie-eind punt van het registratie-of aanmeldings beleid in het vak URL van de uitgever (deze configuratie is eerder vastgelegd).
1. Selecteer OK.
1. Stel in dat de actie moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd vervolg keuzelijst ' Meld u aan met Azure Active Directory ' en klik vervolgens op opslaan.

   > [!NOTE]
   > Nu is uw functie-API geïmplementeerd en moeten 401-antwoorden worden gegenereerd als de juiste sleutel niet is opgegeven en moet er gegevens worden geretourneerd wanneer een geldige aanvraag wordt ingediend.
   > U hebt extra ingrijpende beveiliging in EasyAuth toegevoegd door de optie aanmelden met Azure AD te configureren voor het afhandelen van niet-geverifieerde aanvragen. Houd er rekening mee dat hiermee het gedrag van niet-geautoriseerde aanvragen wordt gewijzigd tussen de back-end-functie-app en front-end-wachtwoord verificatie als EasyAuth een omleiding van 302 naar AAD verzendt in plaats van een 401 niet-geautoriseerde reactie, wij zullen dit corrigeren door API Management later te gebruiken.
   > Er is nog geen IP-beveiliging toegepast. Als u een geldig sleutel-en OAuth2-token hebt, kunt u dit op elk gewenst punt aanroepen. het is dan ook belang rijk dat alle aanvragen via API Management worden afgedwongen.
   > Als u de laag API Management verbruik gebruikt, kunt u deze vergren deling niet via VIP uitvoeren omdat er geen toegewezen statisch IP-adres voor die laag is. u moet hiervoor de methode gebruiken om uw API-aanroepen te vergren delen via de gedeelde functie sleutel van het geheim, zodat de stappen 11-13 niet mogelijk is.

1. Sluit de Blade verificatie/autorisatie 
1. Selecteer netwerken en selecteer vervolgens toegangs beperkingen
1. Vergrendel vervolgens de toegestane Ip's van de functie-app naar het API Management exemplaar-VIP. Deze VIP wordt weer gegeven in de sectie API Management-overzicht van de portal.
1. Als u wilt door gaan met de functies Portal en de onderstaande optionele stappen wilt uitvoeren, moet u uw eigen open bare IP-adres of CIDR-bereik ook toevoegen.
1. Zodra er een vermelding is toegestaan in de lijst, voegt Azure een regel voor impliciete weigering toe om alle andere adressen te blok keren. 

U moet de met CIDR opgemaakte blokken met adressen toevoegen aan het paneel IP-beperkingen. Wanneer u één adres wilt toevoegen, zoals de API Management VIP, moet u dit toevoegen in de notatie xx. xx. xx. xx.

   > [!NOTE]
   > De functie-API kan nu niet worden aangeroepen vanaf elke andere locatie dan via API Management of uw adres.
   
## <a name="import-the-function-app-definition"></a>De definitie van de functie-app importeren
1. Open de *blade API Management* en open vervolgens *uw exemplaar*.
1. Selecteer de Blade Api's in het gedeelte API Management van uw exemplaar.
1. Kies in het deel venster een nieuwe API toevoegen de optie functie-app en selecteer vervolgens ' volledig ' boven aan de pop-up.
1. Klik op Bladeren, kies de functie-app die u als host voor de API in en klik op selecteren.
1. Geef de API een naam en beschrijving voor het interne gebruik van API Management en voeg deze toe aan het ' onbeperkt ' product.
1. Zorg ervoor dat u de basis-URL noteert voor later gebruik en klik vervolgens op maken.

## <a name="configure-oauth2-for-api-management"></a>Oauth2 voor API Management configureren

1. Selecteer vervolgens de Blade OAuth 2,0 op het tabblad Beveiliging en klik op toevoegen.
1. Geef waarden op voor de *weergave naam* en *Beschrijving* voor het toegevoegde OAuth-eind punt (deze waarden worden weer gegeven in de volgende stap als een Oauth2-eind punt).
1. U kunt een wille keurige waarde invoeren in de URL voor de registratie pagina van de client, omdat deze waarde niet wordt gebruikt.
1. Controleer het toekennings type *impliciete auth* en zorg ervoor dat het toekennings type voor de autorisatie code is ingeschakeld.
1. Ga naar de velden *autorisatie* en *token* -eind punt en voer de waarden in die u hebt vastgelegd op basis van het XML-document met bekende configuratie eerder.
1. Schuif naar beneden en vul de *para meter* ' resource ' in met de back-end function API-client-id uit de registratie van de Azure AD B2C-app
1. Selecteer Client Referenties, stel de client-ID in op de App-ID van de ontwikkelaars console-app. Sla deze stap over als u het verbruiks API Management model gebruikt.
1. Stel het client geheim in op de sleutel die u eerder hebt vastgelegd. Sla deze stap over als u het verbruiks API Management model gebruikt.
1. Registreer tot slot de redirect_uri van de autorisatie code toekenning van API Management voor later gebruik.

## <a name="set-up-oauth2-for-your-api"></a>Oauth2 instellen voor uw API
1. Uw API wordt aan de linkerkant van de portal weer gegeven in de sectie alle Api's, opent u de API door erop te klikken.
1. Selecteer het tabblad instellingen.
1. Werk uw instellingen bij door OAuth 2,0 te selecteren op het keuze rondje gebruikers autorisatie.
1. Selecteer de OAuth-server die u eerder hebt gedefinieerd.
1. Schakel het selectie vakje bereik overschrijven in en voer het bereik in dat u eerder hebt vastgelegd voor de back-end-API-aanroep.

   > [!NOTE]
   > Nu hebben we een API Management-instantie die weet hoe u toegangs tokens van Azure AD B2C kunt verkrijgen om aanvragen te autoriseren en de configuratie van de Oauth2-Azure Active Directory B2C begrijpt.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Het **CORS** en **Validate-JWT-** beleid instellen

> De volgende secties moeten worden gevolgd, ongeacht de APIM-laag die wordt gebruikt. 

1. Ga terug naar het tabblad ontwerpen en kies alle Api's en klik vervolgens op de knop code weergave om de beleids editor weer te geven.
1. Bewerk de sectie binnenkomend en plak de onderstaande XML, zodat deze er als volgt uitziet.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Bewerk de URL van de OpenID Connect-config zodat deze overeenkomt met uw bekende Azure AD B2C-eind punt voor het registreren of aanmelden in het beleid.
1. Bewerk de claim waarde zodat deze overeenkomt met de geldige toepassings-ID, ook wel bekend als een client-ID voor de back-end-API-toepassing en sla op.

   > [!NOTE]
   > API Management kan nu reageren op cross-Origin-aanvragen voor JS-wachtwoord verificatie-apps en er worden beperkingen toegepast, de frequentie beperking en vooraf validatie van het JWT-verificatie token dat wordt door gegeven voordat de aanvraag wordt doorgestuurd naar de functie-API.

   > [!NOTE]
   > De volgende sectie is optioneel en is niet van toepassing op de laag **verbruik** , die geen ondersteuning biedt voor de ontwikkelaars Portal.
   > Als u de ontwikkelaars Portal niet wilt gebruiken of niet kunt gebruiken omdat u de laag verbruik gebruikt, slaat u deze stap over en gaat u direct naar [' de Java script Spa maken om de API te gebruiken '](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>Beschrijving De ontwikkelaars Portal configureren

1. Open de Blade Azure AD B2C en ga naar de registratie van de toepassing voor de ontwikkelaars Portal
1. Stel de antwoord-URL in op het item dat u hebt genoteerd toen u de redirect_uri van de autorisatie code toekenning in API Management eerder hebt geconfigureerd.

   Nu de OAuth 2,0-gebruikers autorisatie is ingeschakeld op de `Echo API` , haalt de ontwikkelaars console een toegangs token voor de gebruiker op voordat de API wordt aangeroepen.

1. Blader naar een wille keurige bewerking onder `Echo API` in de ontwikkelaars Portal en selecteer **proberen** de ontwikkelaars console te openen.
1. Noteer een nieuw item in het gedeelte **autorisatie** , dat overeenkomt met de autorisatie server die u zojuist hebt toegevoegd.
1. Selecteer **autorisatie code** in de vervolg keuzelijst autorisatie en u wordt gevraagd u aan te melden bij de Azure AD-Tenant. Als u al bent aangemeld met het account, wordt u mogelijk niet gevraagd.
1. Na een geslaagde aanmelding wordt een `Authorization: Bearer` header toegevoegd aan de aanvraag, met een toegangs token van Azure AD B2C gecodeerd in base64. 
1. Selecteer **verzenden** en u kunt de API aanroepen.

   > [!NOTE]
   > API Management kan nu tokens verkrijgen voor de ontwikkelaars Portal om uw API te testen en kan inzicht krijgen in de definitie en de juiste test pagina weer te geven in de ontwikkelaars Portal.

1. Klik op de Blade overzicht van de API Management Portal op ontwikkelaars Portal om u aan te melden als beheerder van de API.
1. U en andere geselecteerde gebruikers van uw API kunnen deze testen en aanroepen vanuit een-console.
1. Selecteer Products en kies onbeperkt. Kies vervolgens de API die we eerder hebben gemaakt en klik op ' Probeer het '.
1. Verberg de API-abonnements sleutel en kopieer deze ergens veilig samen met de aanvraag-URL die u later nodig hebt.
1. Selecteer ook impliciet, in de vervolg keuzelijst OAuth-verificatie en u moet deze mogelijk verifiëren met een pop-up.
1. Klik op verzenden. als dat niet het geval is, moet uw functie-app reageren met een Hello-bericht via API Management met een 200 OK-bericht en een bepaalde JSON.

   > [!NOTE]
   > Gefeliciteerd, u hebt nu Azure AD B2C, API Management en Azure Functions samen werken om een API te publiceren, beveiligen en gebruiken. Mogelijk hebt u opgemerkt dat de API zich in feite twee keer bevindt met deze methode, eenmaal met de API Management OCP-abonnements sleutel-header en eenmaal met de autorisatie: Bearer-JWT.
   > U zou het volgende moeten doen, omdat dit voor beeld een Java script-toepassing met één pagina is, gebruiken we de API Management sleutel alleen voor tarieven voor het beperken en factureren van aanvragen.
   > De daad werkelijke autorisatie en verificatie worden verwerkt door Azure AD B2C en worden ingekapseld in de JWT, die twee maal wordt gevalideerd, eenmaal door API Management en vervolgens op Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>De Java script-beveiligd-wachtwoord verificatie maken om de API te gebruiken
1. Open de Blade opslag accounts in de Azure Portal 
1. Selecteer het account dat u hebt gemaakt en selecteer de Blade ' statische website ' in de sectie instellingen (als u geen optie voor een statische website ziet, controleert u of u een v2-account hebt gemaakt).
1. Stel de functie voor statische webhosting in op ingeschakeld en stel de naam van het index document in op index.html en klik vervolgens op opslaan.
1. Noteer de inhoud van het primaire eind punt, aangezien deze locatie waar de front-end-site wordt gehost. 

   > [!NOTE]
   > U kunt het herschrijven van Azure Blob Storage + CDN of Azure App Service-maar Blob Storage het onderdeel van de statische website-hosting van de host biedt ons een standaard container die gebruikmaakt van statische webinhoud/HTML/JS/CSS van Azure Storage en een standaard pagina voor ons voor nul werk afleidt.

## <a name="upload-the-js-spa-sample"></a>Het JS-beveiligd-wachtwoord verificatie-voor beeld uploaden
1. Selecteer op de Blade opslag account de Blade ' blobs ' in de sectie BLOB-service en klik op de $web container die wordt weer gegeven in het rechterdeel venster.
1. Sla de onderstaande code op in een lokaal bestand op uw computer als index.html en upload het bestand index.html naar de $web container.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},
                    url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Blader naar het primaire eind punt van de statische website dat u eerder in de laatste sectie hebt opgeslagen.

   > [!NOTE]
   > Gefeliciteerd, u hebt zojuist een Java script-app met één pagina geïmplementeerd op Azure Storage omdat de JS-app niet is geconfigureerd met uw sleutels voor de API of als u de JS-app hebt geconfigureerd met uw Azure AD B2C-Details, maar de pagina niet werkt als u deze opent.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>De JS-beveiligd-wachtwoord verificatie voor Azure AD B2C configureren
1. Nu weten we wat alles is: we kunnen het beveiligd-wachtwoord verificatie configureren met het juiste API Management API-adres en de juiste Azure AD B2C toepassing/client-Id's
1. Ga terug naar de Blade Azure Portal opslag en klik op index.html en kies vervolgens BLOB bewerken 
1. Werk de verificatie gegevens bij zodat deze overeenkomen met de front-end-toepassing die u eerder hebt geregistreerd in B2C. Let op dat de waarden van de b2cScopes voor de API-back-end zijn.
1. De webApi-sleutel en API-URL vindt u in het deel venster API Management test voor de API-bewerking.
1. Maak een APIM-abonnements sleutel per kop naar het API Management terug naar de Blade API Management, selecteer abonnementen en klik vervolgens op abonnement toevoegen en sla de record op. Als u op het weglatings teken (...) naast de gemaakte rij klikt, kunt u de sleutels weer geven zodat u de primaire sleutel kan kopiëren.
1. Het moet er ongeveer uitzien als de onderstaande code:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Op Opslaan klikken

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>De omleidings-Uri's voor de Azure AD B2C frontend-app instellen
1. Open de Blade Azure AD B2C en ga naar de registratie van de toepassing voor de Java script-frontend toepassing
1. Stel de omleidings-URL in die u hebt genoteerd toen u eerder het primaire eind punt van de statische website hebt ingesteld

   > [!NOTE] 
   > Deze configuratie resulteert in een client van de front-end-toepassing die een toegangs token ontvangt met de juiste claims van Azure AD B2C.
   > Het beveiligd-wachtwoord verificatie kan dit als Bearer-token toevoegen aan de https-header in de aanroep van de back-end-API.
   > API Management wordt vooraf de token, de frequentie limiet voor het aantal aanroepen voor het eind punt gevalideerd door de abonnee sleutel voordat de aanvraag wordt door gegeven aan de ontvangende Azure function-API.
   > Het beveiligd-wachtwoord verificatie bericht wordt weer gegeven in de browser.

   > *Gefeliciteerd, u hebt Azure AD B2C, Azure API Management Azure Functions Azure App Service autorisatie geconfigureerd om te werken in perfecte harmonieën!*

   > [!NOTE]
   > Nu we een eenvoudige app met een eenvoudige beveiligde API hebben, gaan we deze testen.

## <a name="test-the-client-application"></a>De client toepassing testen
1. Open de URL van de voor beeld-app die u hebt genoteerd in het opslag account dat u eerder hebt gemaakt
1. Klik in de rechter bovenhoek op aanmelden. Klik op deze pagina om uw Azure AD B2C registreren of aanmelden profiel in te stellen.
1. Het aanmeldings teken in de sectie ' aangemeld als ' van het scherm wordt ingevuld op basis van uw JWT.
1. Klik nu op ' call Web API ' en de pagina moet worden bijgewerkt met de waarden die worden teruggestuurd vanuit uw beveiligde API.

## <a name="and-were-done"></a>En we zijn klaar
De bovenstaande stappen kunnen worden aangepast en bewerkt om veel verschillende manieren van Azure AD B2C met API Management mogelijk te maken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Azure Active Directory en OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Bekijk meer [Video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API management.
* Zie [wederzijdse verificatie van certificaten](api-management-howto-mutual-certificates.md)voor andere manieren om uw back-end-service te beveiligen.
* [Een API Management service-exemplaar maken](get-started-create-service-instance.md).
* [Uw eerste API beheren](import-and-publish.md).
