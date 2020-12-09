---
title: Microsoft Graph-API voor Azure Active Directory Identity Protection
description: Meer informatie over het opvragen van Microsoft Graph risico detecties en bijbehorende informatie uit Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5367e5027bfae2fa3ed7e87a779e50e4048ba608
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861728"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph

Microsoft Graph is het micro soft Unified API-eind punt en de start van [Azure Active Directory Identity Protection](./overview-identity-protection.md) -api's. Er zijn drie Api's die informatie over Risk ante gebruikers en aanmeldingen beschikbaar maken. Met de eerste API, **riskDetection**, kunt u een query uitvoeren op Microsoft Graph voor een lijst van zowel de gebruiker als de aanmelding en de bijbehorende informatie over de detectie. De tweede API, **riskyUsers**, stelt u in staat om Microsoft Graph te zoeken naar informatie over gebruikers identiteits beveiliging die als risico is gedetecteerd. De derde API, **signIn**, stelt u in staat om Microsoft Graph te zoeken naar informatie over Azure AD-aanmeldingen met specifieke eigenschappen die betrekking hebben op risico status, Details en niveau. 

In dit artikel wordt u aan de slag met het maken van verbinding met de Microsoft Graph en het uitvoeren van query's op deze Api's. Zie de [Microsoft Graph-site](https://graph.microsoft.io/) of de specifieke referentie documentatie voor deze api's voor een uitgebreide inleiding, volledige documentatie en toegang tot de Graph Explorer:

* [riskDetection-API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers-API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [signIn-API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Verbinding maken met micro soft Graph

Er zijn vier stappen voor het verkrijgen van toegang tot identiteits beveiligings gegevens via Microsoft Graph:

- [Domein naam ophalen](#retrieve-your-domain-name)
- [Een nieuwe app-registratie maken](#create-a-new-app-registration)
- [API-machtigingen configureren](#configure-api-permissions)
- [Een geldige referentie configureren](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Domein naam ophalen 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).  
1. Blader naar **Azure Active Directory**  >  **aangepaste domein namen**. 
1. Noteer het `.onmicrosoft.com` domein, u hebt deze informatie nodig in een latere stap.

### <a name="create-a-new-app-registration"></a>Een nieuwe app-registratie maken

1. Ga in het Azure Portal naar **Azure Active Directory**  >  **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer de volgende stappen uit op de pagina **maken** :
   1. Typ in het tekstvak **naam** een naam voor de toepassing (bijvoorbeeld: Azure AD-risico detectie-API).
   1. Selecteer onder **ondersteunde account typen** het type accounts dat de api's gaat gebruiken.
   1. Selecteer **Registreren**.
1. Kopieer de **toepassings-id**.

### <a name="configure-api-permissions"></a>API-machtigingen configureren

1. Selecteer de **API-machtigingen** van de **toepassing** die u hebt gemaakt.
1. Klik op de pagina **geconfigureerde machtigingen** in de werk balk aan de bovenkant op **een machtiging toevoegen**.
1. Klik op de pagina **API-toegang toevoegen** op **een API selecteren**.
1. Selecteer **Microsoft Graph** op de pagina **een API selecteren** en klik vervolgens op **selecteren**.
1. Op de pagina **API-machtigingen voor aanvragen** : 
   1. Selecteer **Toepassingsmachtigingen**.
   1. Schakel de selectie vakjes naast `IdentityRiskEvent.Read.All` en in `IdentityRiskyUser.Read.All` .
   1. Selecteer **Machtigingen toevoegen**.
1. **Toestemming van beheerder voor domein verlenen** selecteren 

### <a name="configure-a-valid-credential"></a>Een geldige referentie configureren

1. Selecteer **certificaten & geheimen** uit de **toepassing** die u hebt gemaakt.
1. Onder **client geheimen** selecteert u **Nieuw client geheim**.
   1. Geef het client geheim een **Beschrijving** en stel de verval periode in op basis van het beleid van uw organisatie.
   1. Selecteer **Toevoegen**.

   > [!NOTE]
   > Als u deze sleutel kwijtraakt, gaat u terug naar deze sectie en maakt u een nieuwe sleutel. Bewaar deze sleutel een geheim: iedereen met toegang tot uw gegevens kan.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Verificatie bij Microsoft Graph en query uitvoeren op de API voor detectie van identiteits Risico's

Op dit moment hebt u het volgende nodig:

- De naam van het domein van de Tenant
- De toepassings-ID (client) 
- Het client geheim of-certificaat 

Als u zich wilt verifiëren, verzendt u een post-aanvraag naar `https://login.microsoft.com` met de volgende para meters in de hoofd tekst:

- grant_type:**client_credentials**
- resource `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Als deze aanvraag is voltooid, wordt een verificatie token geretourneerd.  
Maak een header met de volgende para meter om de API aan te roepen:

```
`Authorization`="<token_type> <access_token>"
```

Bij het verifiëren vindt u het token type en toegangs token in het geretourneerde token.

Deze header verzenden als een aanvraag naar de volgende API-URL: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

Het antwoord, indien geslaagd, is een verzameling identiteits risico detecties en gekoppelde gegevens in de OData-JSON-indeling, die kan worden geparseerd en verwerkt zoals u dat wilt.

### <a name="sample"></a>Voorbeeld

In dit voor beeld ziet u het gebruik van een gedeeld geheim dat moet worden geverifieerd. In een productie omgeving wordt het opslaan van geheimen in code in het algemeen afgekeurd. Organisaties kunnen gebruik maken van beheerde identiteiten voor Azure-resources om deze referenties te beveiligen. Zie het artikel [Wat zijn beheerde identiteiten voor Azure-resources](../managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

Hier volgt een voorbeeld code voor het verifiëren en aanroepen van de API met behulp van Power shell.  
Voeg gewoon uw client-ID, de geheime sleutel en het Tenant domein toe.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Query's uitvoeren op de Api's

Deze drie Api's bieden een groot aantal mogelijkheden om informatie op te halen over Risk ante gebruikers en aanmeldingen in uw organisatie. Hieronder vindt u enkele veelvoorkomende use cases voor deze Api's en de bijbehorende voorbeeld aanvragen. U kunt deze query's uitvoeren met behulp van de voorbeeld code hierboven of met [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Alle offline-risico detecties ophalen (riskDetection API)

Met het beleid voor aanmeldings Risico's voor identiteits beveiliging kunt u voor waarden Toep assen wanneer er in realtime een risico wordt gedetecteerd. Maar hoe zit het met detecties die offline zijn gedetecteerd? Als u wilt weten welke detecties offline zijn, en dus het beleid voor aanmeldings Risico's niet hebben geactiveerd, kunt u een query uitvoeren op de riskDetection-API.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Alle gebruikers ophalen die een MFA-Challenge hebben door gegeven die zijn geactiveerd door het beleid voor Risk ante aanmeldingen (riskyUsers-API)

Om inzicht te krijgen in het op risico gebaseerd beleid voor identiteits beveiliging van uw organisatie, kunt u een query uitvoeren voor alle gebruikers die een MFA-Challenge hebben door gegeven die is geactiveerd door een beleid voor Risk ante aanmeldingen. Aan de hand van deze informatie kunt u begrijpen welke gebruikers identiteits beveiliging mogelijk onterecht is gedetecteerd op als risico en welke van uw rechtmatige gebruikers acties uitvoeren die het AI-risico in beslag brengt.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt uw eerste oproep doorgevoerd naar Microsoft Graph!  
Nu kunt u de detectie van identiteits Risico's opvragen en de gegevens gebruiken.

Als u meer wilt weten over Microsoft Graph en hoe u toepassingen bouwt met behulp van de Graph API, raadpleegt u de [documentatie](/graph/overview) en nog veel meer op de [Microsoft Graph-site](https://developer.microsoft.com/graph). 

Zie voor verwante informatie:

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Typen risico detecties gedetecteerd door Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overzicht van Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Hoofdmap van Azure AD Identity Protection-Service](/graph/api/resources/identityprotectionroot)
