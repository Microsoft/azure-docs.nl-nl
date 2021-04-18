---
title: End-to-end beveiliging van inhoud met behulp van Azure Active Directory
description: In dit artikel leert u hoe u inhoud kunt beveiligen met Azure Media Services en Azure Active Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9c81a9b48ff9fa305385c45266d88deb4047f70f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599482"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Zelfstudie: End-to-end beveiliging van inhoud met behulp van Azure Active Directory

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In deze zelfstudie en het verstrekte spelervoorbeeld kunt u een end-to-end subsysteem voor mediabeveiliging instellen op Azure Media Services (AMS) en Azure Active Directory (AAD) voor het streamen van media-inhoud met alle door AMS ondersteunde DRM-/AES-128-streamingprotocollen, codecs en containerindelingen. Het voorbeeld is algemeen genoeg voor beveiligde toegang tot elke REST API die wordt beveiligd met OAuth 2 via Authorization Code Flow met Proof Key for Code Exchange (PKCE). (De licentieleveringsservice van Azure Media Services is er slechts een van.) Het werkt ook voor Microsoft Graph API en elke aangepaste REST API die is beveiligd met OAuth 2 via Authorization Code Flow. Dit is het aanvullende document bij de [voorbeeldcode](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * De verificatievereisten overwegen
> * Inzicht in de werking van de app krijgen
> * Een back-end resource-app registreren
> * Een client-app registreren
> * Het inhoudssleutelbeleid voor het mediaserviceaccount en het streamingbeleid instellen
> * De speler-app instellen

Als u geen Azure Media Services-abonnement hebt, maakt u een [gratis proefaccount](https://azure.microsoft.com/free/) voor Azure en maakt u vervolgens een Media Services-account.

### <a name="duration"></a>Duur
Als u de vereiste technologie hebt, kunt u de zelfstudie in ongeveer twee uur voltooien.

## <a name="prerequisites"></a>Vereisten

De volgende versies en concepten van de nieuwste technologie worden gebruikt. Het is raadzaam dat u hiermee vertrouwd raak voordat u met deze zelfstudie begint.

### <a name="prerequisite-knowledge"></a>Vereiste kennis

Het is niet vereist, maar u kunt het beste bekend zijn met de volgende concepten voordat u met deze zelfstudie begint:

* Digital Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* Het [inhoudssleutelbeleid](drm-content-key-policy-concept.md) voor AMS met behulp van de AMS API v3, de Azure-portal of het [Azure Media Services Explorer-hulpprogramma (AMSE)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Azure Active Directory v2-eind punten op het [Microsoft Identity Platform](../../active-directory/develop/index.yml)
* Moderne cloudverificatie, zoals [OAuth 2.0 en OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Authorization Code Flow in OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) en waarom PKCE is vereist
  * [Gedelegeerde toestemming vs. toepassingstoestemming](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT-token](../../active-directory/develop/access-tokens.md), de bijbehorende claims en de rollover van de ondertekeningssleutel (opgenomen in het voorbeeld)

### <a name="prerequisite-code-and-installations"></a>Vereiste code en installaties

* De voorbeeldcode. Download de [voorbeeldcode](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* Een installatie van Visual Studio Code. U kunt Visual Studio Code hier downloaden: [https://code.visualstudio.com/download](https://code.visualstudio.com/download).
* Een installatie van Node.js. U kunt Node.js hier downloaden: [https://nodejs.org](https://nodejs.org). NPM is bij de installatie inbegrepen.
* Een [Azure-abonnement](https://azure.microsoft.com/free/).
* Een Azure Media Services-account (AMS).
* @azure/msal-browser v 2.0, een van de leden in de [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) SDK-reeks voor verschillende clientplatformen
* De nieuwste versie van [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples) (opgenomen in het voorbeeld)
* FPS-referenties van Apple als u FairPlay DRM en het toepassingscertificaat wilt gebruiken dat wordt gehost met CORS en toegankelijk is via JavaScript aan de clientzijde.

> [!IMPORTANT]
> In deze zelfstudie wordt .NET gebruikt voor het maken van de beperking van het inhoudssleutelbeleid.  Als u geen .NET-ontwikkelaar bent, maar Node.js.wilt proberen om verbinding te maken met Azure Media Services, raadpleegt u [Connect to Media Services v3 API - Node.js](configure-connect-nodejs-howto.md) (Verbinding maken met de Media Services v3 API - Node.js). Er is ook een Node.js-module beschikbaar voor het automatisch afhandelen van rollover van de sleutel. Zie de [passport-ad-module](https://github.com/AzureAD/passport-azure-ad) van Node.js.

## <a name="consider-the-authentication-and-authorization-requirements"></a>De verificatie- en autorisatievereisten overwegen

Het ontwerpen van het subsysteem brengt enkele uitdagingen met zich mee. Het heeft meerdere veranderende onderdelen, er gelden beperkingen voor client-apps en de rollover van de Azure Active Directory-sleutel vindt om de zes weken plaats.

Voor de toepassing met één pagina (SPA) die in deze zelfstudie wordt gebruikt, is rekening gehouden met de verificatievereisten en de bijbehorende beperkingen. Hierbij:

* Azure Active Directory v2-eindpunten als Azure Active Directory-ontwikkelaarsplatform (v1-eindpunten), die worden gewijzigd in Microsoft Identity Platform (v2-eindpunten).
* Authorization Code Flow omdat de stroom voor impliciete toekenning van OAuth 2 is afgeschaft.
* Een app die onderhevig is aan de volgende beperkingen:
    * Een openbare client kan het clientgeheim niet verbergen.  Alleen voor Authorization Code Flow moet het clientgeheim worden verborgen, dus wordt Authorization Code Flow met PKCE gebruikt.
    * Een browsergebaseerde app die is onderworpen aan een beveiligingssandbox van de browser (CORS/voorbereidende beperking).
    * Een browsergebaseerde app die moderne JavaScript gebruikt en is onderworpen aan JavaScript-beveiligingsbeperkingen (aangepaste toegankelijkheid voor headers, correlatie-id).

## <a name="understand-the-subsystem-design"></a>Inzicht in het ontwerp van het subsysteem krijgen

Het ontwerp van het subsysteem wordt in het volgende diagram weergegeven.  Het heeft drie lagen:

* Back-officelaag (zwart) voor het configureren van het inhoudssleutelbeleid en het publiceren van inhoud voor streaming
* Openbare eindpunten (blauw) die speler-/klantgerichte eindpunten zijn die verificatie, autorisatie, DRM-licentie en versleutelde inhoud bieden
* De speler-app (lichtblauw) waarmee alle onderdelen worden geïntegreerd en
    * de verificatie van gebruikers via Azure Active Directory wordt geregeld.
    * het ophalen van access_token van Azure Active Directory wordt geregeld.
    * manifesten en versleutelde inhoud van AMS/CDN worden opgehaald.
    * een DRM-licentie van Azure Media Services wordt verkregen.
    * ontsleutelen, decoderen en weergeven van inhoud worden geregeld.

![scherm voor JWT tokens parseren](media/aad-ams-content-protection/subsystem.svg)

Lees [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](./architecture-design-multi-drm-system.md) voor meer informatie over het subsysteem.

## <a name="understand-the-single-page-app"></a>Inzicht in de toepassing met één pagina krijgen

De speler-app is een toepassing met één pagina (SPA) die in Visual Studio Code is ontwikkeld met behulp van:

* Node.js met ES 6 JavaScript
* Bètaversie van @azure/msal-browser 2.0
* Azure Media Player SDK
* OAuth 2-stroom op Azure Active Directory v2-eindpunten (Microsoft Identity Platform)

De speler-app (SPA) voert de volgende acties uit:

* Gebruikersverificatie voor eigen gebruikers van de tenant, en gastgebruikers van andere AAD-tenants of MSA-accounts. Gebruikers kunnen nu kiezen om zich aan te melden met een pop-up of een omleiding in de browser (voor browsers die geen pop-ups toestaan, zoals Safari).
* Ophalen van `access_token` via Authorization Code Flow met PKCE.
* Verlenging van `access_token` (tokens die door AAD worden afgegeven zijn 1 uur geldig), waarvoor `refresh_token` ook wordt opgehaald.
* JWT-tokens parseren (zowel `access_token` als `id_token`) voor testen/inspecteren.
* Ophalen van DRM-licenties voor alle drie DRM's of AES-128-inhoudssleutel.
* Het streamen van inhoud via verschillende combinaties van DRM, streaming-protocol en container-indeling. Voor elke combinatie wordt de juiste indelingstekenreeks gegenereerd.
* Ontsleutelen, decoderen en weergeven.
* Microsoft Graph API-aanroepen voor problemen oplossen. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Het scherm voor aanmelden, tokens ophalen, tokens verlengen en tokens weergeven:

 ![Scherm voor aanmelden, tokens ophalen, tokens verlengen en tokens weergeven](media/aad-ams-content-protection/token-acquisition.png)

Het scherm voor JWT-tokens parseren (access_token or id_token):

![Schermopname dat het parseren van JWT-tokens laat zien.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Het scherm voor het testen van beveiligde inhoud met verschillende combinaties van DRM/AES, streamingprotocollen en containerindelingen:

![Schermopname dat het testen laat zien van beveiligde inhoud met verschillende combinaties van DRM/AES, streamingprotocollen en containerindelingen](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Een Azure Active Directory-tenant kiezen

> [!NOTE]
> Hierna wordt aangenomen dat u bent aangemeld bij de Azure-portal en minstens één Azure Active Directory-tenant hebt.

Kies een Azure Active Directory-tenant om voor het end-to-end voorbeeld te gebruiken. U hebt hiervoor twee opties:

* Een bestaande Azure Active Directory-tenant. Een Azure-abonnement moet een Azure Active Directory-tenant hebben, maar een Azure Active Directory-tenant kan door meerdere Azure-abonnementen worden gebruikt.
* Een nieuwe Azure Active Directory-tenant die *niet* wordt gebruikt door een Azure-abonnement. Als u voor een nieuwe tenant kiest, moeten het mediaserviceaccount en de spelervoorbeeld-app in een Azure-abonnement staan dat gebruikmaakt van een afzonderlijke Azure Active Directory-tenant. Dit biedt enige flexibiliteit. U kunt bijvoorbeeld uw eigen Azure Active Directory-tenant gebruiken, maar ook het mediaserviceaccount van de klant in het Azure-abonnement van de klant.

## <a name="register-the-backend-resource-app"></a>De back-end resource-app registreren

1. Ga naar de Azure Active Directory-tenant die u hebt gekozen of gemaakt.
1. Selecteer **Azure Active Directory** in het menu.
1. Selecteer **App-registraties** in het menu.
1. Klik op **+ Nieuwe registratie**.
1. Noem de app *LicenseDeliveryResource2* (waarbij 2 staat voor AAD v2-eindpunten).
1. Selecteer **Alleen accounts in deze organisatiemap (alleen [*de naam van de tenant*] - één tenant)** . Als u toegang tot meerdere tenants wilt inschakelen, selecteert u een van de andere opties voor meerdere tenants.
1. De **omleidings-URI** is optioneel en kan later worden gewijzigd.
1. Klik op **Registreren**. App-registraties wordt weergegeven.
1. Selecteer **Manifest** in het menu. De weergave Manifest wordt weergegeven.
1. Wijzig de waarde van de `accessTokenAcceptedVersion` in *2* (geen aanhalingstekens).
1. Wijzig de waarde van de `groupMembershipClaims` in *"SecurityGroup"* (met aanhalingstekens).
1. Klik op **Opslaan**.
1. Selecteer **Een API beschikbaar maken** in het menu. De weergave Een bereik toevoegen wordt weergegeven. (Azure biedt een URI voor de toepassings-id, maar als u deze wilt wijzigen, kunt u deze bewerken in het veld met de URI voor de toepassings-id.)
1. Klik op **Opslaan en doorgaan**. De weergave wordt gewijzigd. Voer voor elk van de instellingen in de kolom Instelling in de onderstaande tabel de waarde in de kolom Waarde in en klik vervolgens op **Bereik toevoegen**.

| Instelling | Waarde | Beschrijving |
| ------- | ----- | ----------- |
| Naam van bereik | *DRM.License.Delivery* | Hoe het bereik wordt weergegeven wanneer de toegang tot deze API wordt aangevraagd, en in toegangstokens wanneer het bereik is verleend aan een clienttoepassing. Het moet uniek zijn in deze toepassing. Het is een best practice 'resource.operation.constraint' te gebruiken als een patroon om de naam te genereren. |
| Wie kan toestemming verlenen? | *Beheerders en gebruikers* | Hiermee wordt bepaald of gebruikers in mappen waar gebruikerstoestemming is ingeschakeld, toestemming kunnen geven voor dit bereik. |
| Weergavenaam van beheerderstoestemming | *Levering van DRM-licentie* | Hoe het bereik in het toestemmingsscherm wordt genoemd wanneer beheerders er toestemming voor geven. |
| Beschrijving van beheerderstoestemming** | *Resourcebereik van het leveringsback-end van de DRM-licentie* | Een gedetailleerde beschrijving van het bereik dat wordt weergegeven wanneer tenantbeheerders een bereik uitbreiden op het toestemmingsscherm. |
| Weergavenaam van gebruikerstoestemming | *DRM.License.Delivery* | Hoe het bereik in het toestemmingsscherm wordt genoemd wanneer gebruikers er toestemming voor geven. |
| Beschrijving van gebruikerstoestemming | *Resourcebereik van het leveringsback-end van de DRM-licentie* | Dit is een gedetailleerde beschrijving van het bereik dat wordt weergegeven wanneer gebruikers een bereik uitbreiden op het toestemmingsscherm. |
| Status | *Ingeschakeld* | Hiermee wordt bepaald of dit bereik beschikbaar is voor clients om aan te vragen. Stel deze waarde in op Uitgeschakeld voor bereiken die u niet zichtbaar wilt maken voor clients. Alleen uitgeschakelde bereiken kunnen worden verwijderd. We raden u aan om ten minste een week te wachten nadat een scope is uitgeschakeld voordat u deze verwijdert, om ervoor te zorgen dat er geen clients meer gebruik van maken. |

## <a name="register-the-client-app"></a>De client-app registreren

1. Ga naar de Azure Active Directory-tenant die u hebt gekozen of gemaakt.
1. Selecteer **Azure Active Directory** in het menu.
1. Selecteer **App-registraties** in het menu.
1. Klik op **+ Nieuwe registratie**.
1. Geef de client-app een naam, bijvoorbeeld *AMS AAD Content Protection*.
1. Selecteer **Alleen accounts in deze organisatiemap (alleen [*de naam van de tenant*] - één tenant)** . Als u toegang tot meerdere tenants wilt inschakelen, selecteert u een van de andere opties voor meerdere tenants.
1. De **omleidings-URI** is optioneel en kan later worden gewijzigd.
1. Klik op **Registreren**.
1. Selecteer **API-machtigingen** in het menu.
1. Klik op **+ Een machtiging toevoegen**. De weergave API-machtigingen aanvragen wordt geopend.
1. Klik op het tabblad **Mijn API** en selecteer de *LicenseDeliveryResource2*-app die u in de vorige sectie hebt gemaakt.
1. Klik op de DRM-pijl en controleer de *DRM.License.Delivery*-machtiging.
1. Klik op **Machtigingen toevoegen**. De weergave Machtigingen toevoegen wordt gesloten.
1. Selecteer **Manifest** in het menu. De weergave Manifest wordt weergegeven.
1. Zoek de volgende waardeparen en voeg deze toe aan het `replyUrlsWithType`-kenmerk:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > U hebt op dit moment nog geen URL voor uw speler-app.  Als u de app uitvoert vanaf uw localhost-webserver, kunt u alleen het localhost-waardepaar gebruiken. Wanneer u uw speler-app hebt geïmplementeerd, kunt u hier de vermelding toevoegen met de geïmplementeerde URL.  Als u dit vergeet te doen, wordt er een foutbericht weergegeven in de Azure Active Directory-aanmelding.

1. Klik op **Opslaan**.
1. Controleer ten slotte of de configuratie juist is en selecteer **Verificatie**.  De weergave Verificatie wordt geopend. Uw clienttoepassing wordt vermeld als een toepassing met één pagina (SPA), de omleidings-URI wordt weergegeven en het toekenningstype is Authorization Code Flow met PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Het inhoudssleutelbeleid voor het Media Services-account en het streamingbeleid instellen

**In deze sectie wordt ervan uitgegaan dat u een .NET-ontwikkelaar bent en bekend bent met het gebruik van de AMS v3 API.**

> [!NOTE]
> Vanaf de publicatie van dit artikel kunt u de Azure-portal niet meer voor de sleutelbeleidsinstelling van het Media Services-account gebruiken, omdat de portal geen ondersteuning biedt voor het gebruik van een asymmetrische sleutel voor token-ondertekening met OpenID-Config.  Het installatieproces moet Azure Active Directory-rollover van sleutels ondersteunen, omdat het door Azure Active Directory uitgegeven token wordt ondertekend door een asymmetrische sleutel en de sleutel elke zes weken wordt gewijzigd. Daarom wordt in deze zelfstudie gebruikgemaakt van .NET en de AMS v3 API.

Configuratie van het [beleid voor inhoudssleutels](drm-content-key-policy-concept.md) en het [streamingbeleid](stream-streaming-policy-concept.md) voor DRM en AES-128 is van toepassing.  Wijzig de `ContentKeyPolicyRestriction` in het beleid voor inhoudssleutels.

Hieronder vindt u de .NET-code voor het maken van de beperking van het inhoudssleutelbeleid.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Wijzig de waarden voor `ida_AADOpenIdDiscoveryDocument`, `ida_audience` en `ida_issuer` in de bovenstaande code. De waarden voor deze items zoeken in de Azure-portal:

1. Selecteer de AAD-tenant die u eerder hebt gebruikt, klik in het menu op **App-registraties** en klik vervolgens op de link **Eindpunten**.
1. Selecteer en kopieer de waarde in het veld **OpenIdConnect-metagegevensdocument** en plak deze in de code als de waarde `ida_AADOpenIdDiscoveryDocument`.
1. De waarde `ida_audience` is de toepassings-id (client-id) van de geregistreerde app *LicenseDeliveryResource2*.
1. De waarde `ida_issuer` is de URL `https://login.microsoftonline.com/[tenant_id]/v2.0`. Vervang [*tenant_id*] door uw tenant-id.

## <a name="set-up-the-sample-player-app"></a>De speler-voorbeeldapp instellen

Als u dit nog niet hebt gedaan, moet u de app klonen of downloaden vanuit de bronopslagplaats: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

U hebt twee opties voor het instellen van de speler-app:

* Minimale aanpassing (alleen een aantal constante tekenreekswaarden zoals client_id, tenant_id en streaming-URL vervangen), maar u moet Visual Studio Code en Node.js gebruiken.
* Als u liever een andere IDE en ander webplatform gebruikt, zoals ASP.NET Core, kunt u de webpaginabestanden, JavaScript-bestanden en het CSS-bestand in uw project plaatsen, omdat de speler-app zelf geen code op de server gebruikt.

### <a name="option-1"></a>Optie 1

1. Start Visual Studio Code.
1. Om het project te openen, klikt u op Bestand -> Map openen -> blader naar en selecteer de bovenliggende map van het *package.json*-bestand.
1. Open het JavaScript-bestand *public/javascript/constants.js*.
1. Vervang `OAUTH2_CONST.CLIENT_ID` door de `client_id` van uw geregistreerde client-app in de AAD-tenant.  U kunt de `client_id` vinden in het gedeelte Overzicht van de geregistreerde app in de Azure-portal. Opmerking: het gaat om de client-id, niet om de object-id.
1. Vervang `OAUTH2_CONST.TENANT_ID` door de `tenant_id` van uw Azure Active Directory-tenant. U kunt uw `tenant_id` vinden door te klikken op het Azure Active Directory-menu. De tenant_id wordt weergegeven in de sectie Overzicht.
1. Vervang `OAUTH2_CONST.SCOPE` door het bereik dat u hebt toegevoegd aan uw geregistreerde client-app. U kunt het bereik vinden door naar de geregistreerde client-app te navigeren in het menu **App-registraties** en vervolgens uw client-app te selecteren:
    1. Selecteer uw client-app, klik op het menu **API-machtigingen** en selecteer vervolgens het bereik *DRM.License.Delivery* onder de API-machtiging *LicenseDeliveryResource2*. De machtiging moet een indeling hebben zoals *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery*. **Belangrijk:** Bewaar de spatie vóór `offline_access` in `OAUTH2_CONST.SCOPE`.
1. Vervang de twee constante tekenreeksen voor `AMS_CONST`, zoals hieronder wordt weergegeven. De ene is de beveiligde streaming-URL van uw test-asset en de andere is de URL voor het FPS-toepassingscertificaat, als u de FairPlay-testcase wilt toevoegen. Als dat niet het geval is, kunt u deze voor `AMS_CONST.APP_CERT_URL` laten staan. Klik vervolgens op **Opslaan**.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Lokaal testen:

1. Selecteer in Visual Studio Code (VSC) **Weergave** in het hoofdmenu en vervolgens **Terminal**.
1. Als u npm nog niet hebt geïnstalleerd, geeft u bij de opdrachtprompt `npm install` op.
1. Voer `npm start` in bij de opdrachtprompt. (Als npm niet start, probeert u de map te wijzigen in `npmweb` door `cd npmweb` in te voeren bij de opdrachtprompt.)
1. Ga in een browser naar `http://localhost:3000`.

Afhankelijk van de browser die u gebruikt, kiest u de juiste combinatie van DRM/AES, streamingprotocol en containerindeling om te testen na het aanmelden (`access_token` ophalen). Als u in Safari op macOS test, schakelt u de optie Redirect API in, omdat Safari geen pop-ups toestaat. De meeste andere browsers staan pop-ups en omleidingsopties toe.

### <a name="option-2"></a>Optie 2

Als u van plan bent om een andere IDE of ander webplatform, en/of een webserver (zoals IIS uitgevoerd op uw ontwikkelcomputer) te gebruiken, kopieert u de volgende bestanden naar een nieuwe map op uw lokale webserver. Op de onderstaande paden vindt u de door u gedownloade code.

* *views/index.ejs* (wijzig het achtervoegsel naar .html)
* *views/jwt.ejs* (wijzig het achtervoegsel naar .html)
* *views/info.ejs* (wijzig het achtervoegsel naar .html)
* *public/* * (JavaScript-bestanden, CSS, installatiekopieën, zoals hieronder weergegeven)

1. Kopieer de bestanden in de map *view* naar de hoofdmap van de nieuwe map.
1. Kopieer de *mappen* in de map *public* naar de hoofdmap van de nieuwe map.
1. Wijzig de extensies van de `.ejs`-bestanden in `.html`. (Er wordt geen variabele aan de serverzijde gebruikt, zodat u deze veilig kunt wijzigen.)
1. Open *index.html* in VSC (of een andere code-editor) en wijzig de paden `<script>` en `<link>` in de locaties van de bestanden.  Als u de vorige stappen hebt gevolgd, hoeft u alleen de `\` in het pad te verwijderen.  `<script type="text/javascript" src="/javascript/constants.js"></script>` wordt bijvoorbeeld `<script type="text/javascript" src="javascript/constants.js"></script>`.
1. Pas de constanten in het bestand *javascript/constants.js* aan als in optie 1.

## <a name="common-customer-scenarios"></a>Veelvoorkomende klantscenario's

Nu u de zelfstudie hebt voltooid en een werkend subsysteem hebt, kunt u het proberen te wijzigen voor de volgende klantscenario's:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) voor het leveren van licenties via Azure Active Directory-groepslidmaatschap

Tot nu toe kan een gebruiker die zich kan aanmelden, een geldige licentie verkrijgen en de beveiligde inhoud afspelen.

Veel klanten vereisen dat een subset van geverifieerde gebruikers bepaalde inhoud mag bekijken die anderen niet mogen bekijken. Denk aan een klant die basis- en premium-abonnementen biedt voor video-inhoud. Klanten die hebben betaald voor een basisabonnement, zouden geen inhoud moeten kunnen bekijken waarvoor een premium-abonnement nodig is. Hieronder vindt u de extra stappen die nodig zijn om aan deze vereiste te voldoen:

#### <a name="set-up-the-azure-ad-tenant"></a>De Azure Active Directory-tenant instellen

1. Stel twee accounts in uw tenant in. Deze kunnen worden aangeduid als *premium_user* en *basic_user*.
1. Maak een gebruikersgroep en noem deze *PremiumGroup*.
1. Voeg de *premium_user* aan de *PremiumGroup* toe als lid, en voeg de *basic_user* niet toe aan de groep.
1. Noteer de **object-id** van de *PremiumGroup*.

#### <a name="set-up-the-media-services-account"></a>Het Media Services-account instellen

Wijzig `ContentKeyPolicyRestriction` (zoals weergegeven in de sectie hierboven in de installatie van het Media Services-account) door een claim met de naam *groups* toe te voegen, waarbij `ida_EntitledGroupObjectId` de object-id van *PremiumGroup* als waarde heeft:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

De claim *groups* is lid van een [beperkte claimset](../../active-directory/develop/reference-claims-mapping-policy-type.md#claim-sets) in Azure Active Directory.

#### <a name="test"></a>Testen

1. Meld u aan met het *premium_user*-account. U hoort de beveiligde inhoud te kunnen afspelen.
1. Meld u aan met het *basic_user*-account. Er hoort een foutbericht te worden weergegeven waarin staat dat de video is versleuteld, maar er geen sleutel is om deze te ontsleutelen. Als u de gebeurtenissen, fouten en downloads weergeeft met de vervolgkeuzelijst aan de onderkant van de diagnostische overlay van de speler, moet het foutbericht aangeven dat het ophalen van de licentie is mislukt vanwege de ontbrekende claimwaarde voor de groepsclaims in de JWT die is uitgegeven door het Azure Active Directory-tokeneindpunt.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Ondersteuning voor meerdere mediaserviceaccounts (op meerdere abonnementen)

Een klant kan meerdere mediaserviceaccounts op één of meerdere Azure-abonnementen hebben. Een klant kan bijvoorbeeld één mediaserviceaccount hebben voor primaire productie, een andere als secundaire/back-up en nog een voor ontwikkelen/testen.

U hoeft alleen maar dezelfde set parameters te gebruiken als in de sectie (installatie van het mediaserviceaccount) bij het maken van de `ContentKeyPolicyRestriction` in alle mediaserviceaccounts.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Ondersteuning voor een klant en leveranciers en/of dochterondernemingen van die klant op meerdere AAD-tenants

Gebruikers van de oplossing, zoals dochterondernemingen, leveranciers en partners van een klant, kunnen zich in verschillende AAD-tenants bevinden, zoals `mycustomer.com`, `mysubsidiary.com` en `myparther.com`. Hoewel deze oplossing is gebouwd op één specifieke AAD-tenant, zoals `mycustomer.com`, kunt u deze gebruiken voor gebruikers van andere tenants.

Als u `mycustomer.com` gebruikt voor deze oplossing, voegt u een gebruiker van `mypartner.com` als een gastgebruiker toe aan de `mycustomer.com`-tenant. Zorg ervoor dat de `mypartner.com`-gebruiker het gastaccount activeert. Het gastaccount kan afkomstig zijn van een andere AAD-tenant of van een `outlook.com`-account.

De gastgebruikers van `mypartner.com` worden, nadat ze zijn geactiveerd in `mycustomer.com`, nog steeds via hun eigen/oorspronkelijke AAD-tenant `mypartner.com` geverifieerd, maar het `access_token` wordt uitgegeven door `mycustomer.com`.

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Ondersteuning voor een tenant/abonnement van een klant met een installatie in uw abonnement/tenant

U kunt uw installatie gebruiken om beveiligde inhoud te testen op het mediaserviceaccount/abonnement van uw klant. U stelt deze in met een Azure Active Directory-tenant en een mediaserviceaccount in hetzelfde abonnement. Het mediaserviceaccount van de klant zou in het Azure-abonnement van de klant zijn met de eigen Azure Active Directory-tenant.

1. Voeg het account van uw klant toe aan uw tenant als een gastaccount.
1. Bereid samen met uw klant beveiligde inhoud voor in het mediaserviceaccount van uw klant door de drie parameters op te geven zoals vermeld in de sectie installatie van het mediaserviceaccount.

Uw klant kan vervolgens naar uw installatie bladeren, zich aanmelden met het gastaccount en zijn eigen beveiligde inhoud testen. U kunt zich ook aanmelden met uw eigen account en de inhoud van uw klant testen.

Uw voorbeeldoplossing kan worden ingesteld in een Microsoft-tenant met een Microsoft-abonnement of een aangepaste tenant met Microsoft-abonnement. Het Azure Media Services-exemplaar kan afkomstig zijn uit een ander abonnement met de bijbehorende tenant.

## <a name="clean-up-resources"></a>Resources opschonen

> [!WARNING]
> Als u deze toepassing verder niet gaat gebruiken, verwijder dan de resources die u hebt gemaakt tijdens deze zelfstudie. Anders worden er kosten voor in rekening gebracht.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Snelstart: Inhoud versleutelen](drm-encrypt-content-how-to.md)
