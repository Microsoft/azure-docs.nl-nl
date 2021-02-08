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
ms.openlocfilehash: 196b80a704b8a270a4cbb7d3505d5f9be1e23479
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820314"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Java-Console-app met behulp van de identiteit van de app

In deze Snelstartgids downloadt en voert u een code voorbeeld uit die laat zien hoe een Java-toepassing een toegangs token kan krijgen met behulp van de identiteit van de app om de Microsoft Graph-API aan te roepen en een [lijst met gebruikers](/graph/api/user-list) in de map weer te geven. Het codevoorbeeld laat zien hoe een taak of Windows-service zonder toezicht kan worden uitgevoerd met een toepassings-id, in plaats van een gebruikers-id. 

> [!div renderon="docs"]
> ![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze snelstart](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, hebt u het volgende nodig:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 of hoger
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden

> [!div renderon="docs" class="sxs-lookup"]
>
> U hebt twee opties voor het starten van de snelstarttoepassing: Express (optie 1 hieronder) en handmatig (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart-ervaring <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">Azure-portal - App-registraties</a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Selecteer onder **beheren** de optie  **certificaten & geheimen**.
> 1. Selecteer onder **Clientgeheimen** de optie **Nieuw clientgeheim**. Voer een naam in en selecteer vervolgens **Toevoegen**. Noteer de waarde voor het geheim op een veilige locatie, voor gebruik in een latere stap.
> 1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**. Selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer onder het knooppunt **Gebruiker** de optie **User.Read.All**. Selecteer vervolgens **Machtigingen toevoegen**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>De Quick Start-app downloaden en configureren
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Stap 1: de toepassing configureren in Azure Portal
> Om ervoor te zorgen dat het codevoorbeeld voor deze quickstart werkt, moet u een clientgeheim maken en de toepassingstoestemming **User.Read.All** van Graph API toevoegen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-netcore-daemon/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-java-project"></a>Stap 2: het Java-project downloaden

> [!div renderon="docs"]
> [Het Java daemon-project downloaden](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>Stap 3: het Java-project configureren
>
> 1. Pak het zip-bestand uit in een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld *C:\Azure-Samples*.
> 1. Ga naar de submap **msal-client-Credential-Secret**.
> 1. Bewerk *src\main\resources\application.Properties* en vervang de waarden van de velden `AUTHORITY` , `CLIENT_ID` en `SECRET` met het volgende code fragment:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Waar:
>    - `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
>    - `Enter_the_Tenant_Id_Here` -Vervang deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld contoso.Microsoft.com).
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
> Als u een globale Tenant beheerder bent, gaat u naar de pagina met **API-machtigingen** in **App-registraties** in de Azure Portal en selecteert u **toestemming van de beheerder toekennen voor {Tenant naam}** (waarbij {Tenant naam} de naam van uw directory is).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de pagina **API-machtigingen** en selecteert u **beheerder toestemming geven voor Enter_the_Tenant_Name_Here**.
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
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie voor meer informatie over het gebruik van een certificaat [deze instructies](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) in dezelfde github-opslag plaats voor dit voor beeld, maar in de tweede map **msal-client-Credential-Certificate**.

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
> | `SCOPE` | De aangevraagde bereiken bevat. Voor vertrouwelijke clients moet dit de indeling gebruiken die lijkt op `{Application ID URI}/.default` om aan te geven dat de bereiken die worden aangevraagd, statisch zijn gedefinieerd in het app-object dat is ingesteld in de Azure Portal (voor Microsoft Graph, `{Application ID URI}` punten `https://graph.microsoft.com` ). Voor aangepaste web-Api's `{Application ID URI}` wordt gedefinieerd onder de sectie **een API beschikbaar** maken in **app-registraties** in azure Portal.|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over daemon-toepassingen de landings pagina van het scenario.

> [!div class="nextstepaction"]
> [Een daemon-app die web-API's aanroept](scenario-daemon-overview.md)
