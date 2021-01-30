---
title: 'Snelstartgids: Microsoft Graph aanroepen vanuit een Java-daemon | Azure'
titleSuffix: Microsoft identity platform
description: In deze Quick Start leert u hoe een Java-app een toegangs token kan ophalen en een API aanroept die wordt beveiligd door het micro soft Identity platform-eind punt, met behulp van de eigen identiteit van de app
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 9c6571793d2317097574d0afdc7137b3a3d5ad6d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064523"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Java-Console-app met behulp van de identiteit van de app

In deze Snelstartgids downloadt en voert u een code voorbeeld uit die laat zien hoe een Java-toepassing een toegangs token kan verkrijgen met behulp van de identiteit van de app om de Microsoft Graph-API aan te roepen en een [lijst met gebruikers](/graph/api/user-list) in de map weer te geven. Het codevoorbeeld laat zien hoe een taak of Windows-service zonder toezicht kan worden uitgevoerd met een toepassings-id, in plaats van een gebruikers-id. 

> [!div renderon="docs"]
> ![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze snelstart](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, hebt u het volgende nodig:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 of hoger
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden

> [!div renderon="docs" class="sxs-lookup"]
>
> U hebt twee opties voor het starten van de snelstarttoepassing: Express (optie 1 hieronder) en handmatig (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar het nieuwe deelvenster [Azure-portal, App-registraties](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Ga naar de pagina [App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in het Microsoft-identiteitsplatform voor ontwikkelaars.
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in.
> 1. Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die wordt weergegeven voor gebruikers van de app, zoals `Daemon-console`. Selecteer vervolgens **Registreren** om de toepassing te maken.
> 1. Na het registreren opent u het menu **Certificaten en geheimen**.
> 1. Onder **Clientgeheimen** selecteert u **+ Nieuw clientgeheim**. Geef het clientgeheim een naam en selecteer **Toevoegen**. Kopieer het geheim naar een veilige locatie. U hebt dit nodig voor gebruik in uw code.
> 1. Open nu het menu **API-machtigingen**, selecteer de knop **+ Een geheim toevoegen** en selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer onder het knooppunt **Gebruiker** de optie **User.Read.All** en selecteer vervolgens **Machtigingen toevoegen**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Uw snelstart-app downloaden en configureren
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Om ervoor te zorgen dat het codevoorbeeld voor deze quickstart werkt, moet u een clientgeheim maken en de toepassingstoestemming **User.Read.All** van Graph API toevoegen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-netcore-daemon/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-java-project"></a>Stap 2: uw Java-project downloaden

> [!div renderon="docs"]
> [Het Java daemon-project downloaden](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-java-project"></a>Stap 3: uw Java-project configureren
>
> 1. Pak het zip-bestand uit in een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld **C:\Azure-Samples**.
> 1. Ga naar de submap **msal-client-Credential-Secret**.
> 1. Bewerk **src\main\resources\application.Properties** en vervang de waarden van de velden `AUTHORITY` , `CLIENT_ID` en `SECRET` met het volgende code fragment:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Waar:
>    - `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
>    - `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here`: vervang deze waarde door het clientgeheim dat is gemaakt in stap 1.
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** en **Map-id (tenant-id)** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal. Voor het genereren van een nieuwe sleutel gaat u naar de pagina **Certificaten en geheimen**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Stap 3: toestemming van de beheerder

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Stap 4: toestemming van de beheerder

Als u op dit moment probeert de toepassing uit te voeren, krijgt u de foutmelding *HTTP 403: verboden*: `Insufficient privileges to complete the operation`. Deze fout treedt op, omdat voor elke *alleen-app-toestemming* beheerderstoestemming nodig is, wat betekent dat een globale beheerder van uw map toestemming moet geven aan uw toepassing. Selecteer een van de opties hieronder, afhankelijk van uw rol:

##### <a name="global-tenant-administrator"></a>Globale tenantbeheerder

> [!div renderon="docs"]
> Als u een globale tenantbeheerder bent, gaat u naar de pagina **API-machtigingen** in de registratie van toepassingen (preview) in de Azure-portal en selecteer **Beheerder toestemming verlenen voor {tenantnaam}** (waarbij {tenantnaam} de naam van uw map is).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de pagina **API-machtigingen** en selecteert u **Beheerder toestemming verlenen voor Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ga naar de pagina API-machtigingen]()

##### <a name="standard-user"></a>Standaardgebruiker

Als u een standaardgebruiker van uw tenant bent, moet u een globale beheerder vragen beheerderstoestemming voor uw toepassing te verlenen. Daarvoor verstrekt u de volgende URL aan uw beheerder:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Waar:
>> * `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **Tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Stap 5: De toepassing uitvoeren

U kunt het voor beeld rechtstreeks testen door de Main-methode van ClientCredentialGrant. Java uit te voeren vanaf uw IDE.

Vanuit uw shell of opdrachtregel:

```
$ mvn clean compile assembly:single
```

Hiermee wordt een msal-client-Credential-Secret-1.0.0. jar-bestand gegenereerd in de map/targets. Voer dit uit met behulp van het uitvoer bare Java-bestand zoals hieronder:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Na de uitvoering moet de toepassing de lijst met gebruikers in de geconfigureerde Tenant weer geven.


> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie voor meer informatie over het gebruik van een certificaat [deze instructies](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) in dezelfde github-opslag plaats voor dit voor beeld, maar in de tweede map **msal-client-Credential-Certificate**

## <a name="more-information"></a>Meer informatie

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt om toegang te krijgen tot een API die wordt beveiligd door micro soft Identity platform. Zoals beschreven, worden met deze snelstart tokens aangevraagd met behulp van de eigen identiteit van de toepassing in plaats van gedelegeerde machtigingen. De verificatiestroom die in dit voorbeeld wordt gebruikt, staat bekend als de *[oauth-stroom voor clientreferenties](v2-oauth2-client-creds-grant-flow.md)* . Zie [dit artikel](scenario-daemon-overview.md)voor meer informatie over het gebruik van MSAL Java met daemon-apps.

Voeg MSAL4J toe aan uw toepassing met behulp van Maven of Gradle om uw afhankelijkheden te beheren door de volgende wijzigingen aan te brengen in het bestand pom.xml (Maven) of build.gradle (Gradle) van de toepassing.

In pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

In build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL initialiseren

Voeg een verwijzing toe aan MSAL voor Java door de volgende code toe te voegen bovenaan het bestand waarin u MSAL4J gaat gebruiken:

```Java
import com.microsoft.aad.msal4j.*;
```

Vervolgens initialiseert u MSAL met de volgende code:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Waar: |Beschrijving |
> |---------|---------|
> | `CLIENT_SECRET` | Het clientgeheim is dat voor de toepassing in Azure-portal wordt gemaakt. |
> | `CLIENT_ID` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |
> | `AUTHORITY`    | Het STS-eindpunt voor de gebruiker voor verificatie. Dat is meestal `https://login.microsoftonline.com/{tenant}` voor de openbare cloud, waarbij {tenant} de naam van uw tenant of uw tenant-id is.|

### <a name="requesting-tokens"></a>Tokens aanvragen

Als u een token wilt aanvragen met behulp van de identiteit van de app, gebruikt u de `acquireToken`-methode:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Waar:| Beschrijving |
> |---------|---------|
> | `SCOPE` | De aangevraagde bereiken bevat. Voor vertrouwelijke clients moet hiervoor de indeling worden gebruikt die vergelijkbaar is met `{Application ID URI}/.default` om aan te geven dat de aangevraagde bereiken dezelfde zijn die statisch zijn gedefinieerd in het app-object dat is ingesteld in de Azure-portal (voor Microsoft Graph verwijst `{Application ID URI}` naar `https://graph.microsoft.com`). Voor aangepaste web-API's wordt `{Application ID URI}` gedefinieerd in de sectie **Een API beschikbaar maken** in de registratie van toepassingen (preview) van de Azure-portal. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de landingspagina van het scenario voor meer informatie over daemontoepassingen

> [!div class="nextstepaction"]
> [Een daemon-app die web-API's aanroept](scenario-daemon-overview.md)
