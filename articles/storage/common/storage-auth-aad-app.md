---
title: Een token van Azure AD verkrijgen voor het machtigen van aanvragen van een client toepassing
titleSuffix: Azure Storage
description: Gebruik Azure Active Directory om te verifiëren vanuit een client toepassing, verschaf een OAuth 2,0-token en machtig aanvragen voor Azure Blob Storage en Queue Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d6a152096ce4e16849542c26d1c7a675a972b89
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779070"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Een token van Azure AD verkrijgen voor het machtigen van aanvragen van een client toepassing

Een belang rijk voor deel van het gebruik van Azure Active Directory (Azure AD) met Azure Blob Storage of de wachtrij opslag is dat uw referenties niet meer in uw code hoeven te worden opgeslagen. In plaats daarvan kunt u een OAuth 2,0-toegangs token aanvragen bij het micro soft Identity-platform. Azure AD verifieert de beveiligingsprincipal (een gebruiker, groep of Service-Principal) die de toepassing uitvoert. Als de verificatie slaagt, retourneert Azure AD het toegangs token voor de toepassing en kan de toepassing vervolgens het toegangs token gebruiken om aanvragen voor Azure Blob Storage of de wachtrij opslag te autoriseren.

In dit artikel wordt beschreven hoe u uw systeem eigen toepassing of webtoepassing configureert voor verificatie met het micro soft Identity-platform met behulp van een voorbeeld toepassing die beschikbaar is voor downloaden. De voorbeeld toepassing bevat .NET, maar andere talen gebruiken een vergelijk bare methode. Zie [overzicht van micro soft Identity platform](../../active-directory/develop/v2-overview.md)voor meer informatie over het micro soft-identiteits platform.

Zie [toegang tot Azure Active Directory webtoepassingen verlenen met behulp van de oauth 2,0 code Grant flow](../../active-directory/develop/v2-oauth2-auth-code-flow.md)voor een overzicht van de OAuth 2,0-code subsidie stroom.

## <a name="about-the-sample-application"></a>Over de voorbeeld toepassing

De voorbeeld toepassing biedt een end-to-end-ervaring die laat zien hoe u een webtoepassing configureert voor verificatie met Azure AD in een lokale ontwikkel omgeving. Als u de voorbeeld toepassing wilt bekijken en uitvoeren, moet u deze eerst klonen of downloaden via [github](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Volg vervolgens de stappen in het artikel om een registratie van Azure-apps te configureren en de toepassing voor uw omgeving bij te werken.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Een rol toewijzen aan een Azure AD-beveiligings-principal

Als u een beveiligingsprincipal van uw Azure Storage-toepassing wilt verifiëren, moet u eerst Azure RBAC-instellingen (op rollen gebaseerd toegangs beheer) voor die beveiligingsprincipal configureren. Azure Storage definieert ingebouwde rollen die machtigingen voor containers en wacht rijen omvatten. Wanneer de Azure-rol wordt toegewezen aan een beveiligingsprincipal, wordt die beveiligingsprincipal toegang verleend tot die bron. Zie [toegangs rechten voor Azure Blob en wachtrij gegevens beheren met Azure RBAC](./storage-auth-aad-rbac-portal.md)voor meer informatie.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-Tenant

De eerste stap bij het gebruik van Azure AD om toegang tot opslag resources te autoriseren, is het registreren van uw client toepassing met een Azure AD-Tenant vanuit het [Azure Portal](https://portal.azure.com). Wanneer u uw client toepassing registreert, geeft u informatie over de toepassing op in azure AD. Azure AD biedt vervolgens een client-ID (ook wel een *toepassings-id* genoemd) die u gebruikt om uw toepassing te koppelen aan Azure ad tijdens runtime. Zie voor meer informatie over de client-ID [toepassings-en Service-Principal-objecten in azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). Als u uw Azure Storage-toepassing wilt registreren, volgt u de stappen in [Quick Start: een toepassing registreren bij het micro soft Identity-platform](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). 

De volgende afbeelding toont algemene instellingen voor het registreren van een webtoepassing. Houd er rekening mee dat in dit voor beeld de omleidings-URI is ingesteld op `http://localhost:5000/signin-oidc` voor het testen van de voorbeeld toepassing in de ontwikkel omgeving. U kunt deze instelling later wijzigen onder de **verificatie** -instelling voor uw geregistreerde toepassing in de Azure portal:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Scherm afbeelding die laat zien hoe u uw opslag toepassing registreert bij Azure AD":::

> [!NOTE]
> Als u uw toepassing registreert als een systeem eigen toepassing, kunt u een geldige URI voor de **omleidings-URI** opgeven. Voor systeem eigen toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidings-URI een geldige URI zijn, omdat deze de URL specificeert waarnaar de tokens worden opgegeven.

Nadat u uw toepassing hebt geregistreerd, ziet u de toepassings-ID (of client-ID) onder **instellingen**:

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Scherm opname met de client-ID":::

Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-register-app.md)voor meer informatie over het registreren van een toepassing met Azure AD.

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Uw geregistreerde app-machtigingen verlenen aan Azure Storage

Verleen vervolgens de machtigingen van uw toepassing om Azure Storage-Api's aan te roepen. Met deze stap kan uw toepassing aanvragen voor Azure Storage met Azure AD autoriseren.

1. Selecteer op de pagina **API-machtigingen** voor uw geregistreerde toepassing **een machtiging toevoegen**.
1. Selecteer **Azure Storage** op het tabblad **micro soft-api's** .
1. In het deel venster **API-machtigingen voor aanvragen** , onder **welk type machtigingen uw toepassing vereist?**, moet u nagaan of het beschik bare machtigings type **gedelegeerde machtigingen** is. Deze optie is standaard voor u geselecteerd.
1. Schakel onder **machtigingen** het selectie vakje in naast **user_impersonation** en selecteer vervolgens de knop **machtigingen toevoegen** .

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Scherm opname met machtigingen voor de opslag-API":::

1. Verleen vervolgens toestemming van de beheerder voor deze machtigingen door te klikken op **toestemming van beheerder geven voor de standaard directory**.

In het deel venster **API-machtigingen** ziet u nu dat uw geregistreerde Azure AD-toepassing toegang heeft tot de Microsoft Graph-en Azure Storage-api's en dat toestemming wordt verleend voor de standaard directory. Er worden automatisch machtigingen verleend aan Microsoft Graph wanneer u uw app voor het eerst registreert bij Azure AD.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Scherm afbeelding met API-machtigingen voor de geregistreerde app":::

### <a name="create-a-client-secret"></a>Een clientgeheim maken

De toepassing heeft een client geheim nodig om de identiteit ervan te bewijzen wanneer een token wordt aangevraagd. Voer de volgende stappen uit om het client geheim toe te voegen:

1. Ga naar de registratie van uw app in de Azure Portal.
1. Selecteer de instelling **certificaten & geheimen** .
1. Klik onder **client geheimen** op **Nieuw client geheim** om een nieuw geheim te maken.
1. Geef een beschrijving op voor het geheim en kies het gewenste verloop interval.
1. Kopieer de waarde van het nieuwe geheim direct naar een veilige locatie. De volledige waarde wordt slechts één keer weer gegeven.

    ![Scherm opname van client geheim](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Impliciete toekennings stroom inschakelen

Configureer vervolgens impliciete toekennings stroom voor uw toepassing. Volg deze stappen:

1. Ga naar de registratie van uw app in de Azure Portal.
1. Selecteer de **verificatie** -instelling in de sectie **beheren** .
1. Schakel in de sectie **impliciete toekenning** het selectie vakje in om id-tokens in te scha kelen, zoals wordt weer gegeven in de volgende afbeelding:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Scherm afbeelding die laat zien hoe instellingen voor impliciete toekennings stroom worden ingeschakeld":::

## <a name="client-libraries-for-token-acquisition"></a>Client bibliotheken voor het verkrijgen van tokens

Nadat u uw toepassing hebt geregistreerd en de machtiging hebt verleend om toegang te krijgen tot gegevens in Azure Blob-opslag of wachtrij opslag, kunt u code toevoegen aan uw toepassing om een beveiligingsprincipal te verifiëren en een OAuth 2,0-token te verkrijgen. Als u het token wilt verifiëren en verkrijgen, kunt u een van de [micro soft-identiteits platform verificatie bibliotheken](../../active-directory/develop/reference-v2-libraries.md) of een andere open-source-bibliotheek gebruiken die ondersteuning biedt voor openid connect Connect 1,0. Uw toepassing kan vervolgens het toegangs token gebruiken om een aanvraag voor Azure Blob-opslag of wachtrij opslag te autoriseren.

Zie de sectie [verificatie stromen](../../active-directory/develop/msal-authentication-flows.md) van de documentatie van [micro soft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) voor een lijst met scenario's waarvoor het verkrijgen van tokens wordt ondersteund.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Bekende waarden voor verificatie met Azure AD

Als u een beveiligingsprincipal met Azure AD wilt verifiëren, moet u een aantal bekende waarden in uw code toevoegen.

### <a name="azure-ad-authority"></a>Azure AD-instantie

Voor de open bare cloud van micro soft is de basis-Azure AD-instantie als volgt, waarbij *Tenant-id* uw Active Directory Tenant-id (of directory-id) is:

`https://login.microsoftonline.com/<tenant-id>/`

Met de Tenant-ID wordt de Azure AD-Tenant geïdentificeerd die moet worden gebruikt voor verificatie. Dit wordt ook wel de Directory-ID genoemd. Als u de Tenant-ID wilt ophalen, gaat u naar de **overzichts** pagina voor de registratie van uw app in de Azure Portal en kopieert u de waarde daar.

### <a name="azure-storage-resource-id"></a>Resource-ID Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-code voorbeeld: een blok-Blob maken

Het code voorbeeld laat zien hoe u een toegangs token van Azure AD ophaalt. Het toegangs token wordt gebruikt om de opgegeven gebruiker te verifiëren en vervolgens een aanvraag voor het maken van een blok-BLOB te autoriseren. Als u dit voor beeld wilt gebruiken, volgt u eerst de stappen die in de voor gaande secties worden beschreven.

Als u het token wilt aanvragen, hebt u de volgende waarden nodig van de registratie van uw app:

- De naam van uw Azure AD-domein. Haal deze waarde op op de pagina **overzicht** van uw Azure Active Directory.
- De Tenant-ID (of map). Haal deze waarde op op de pagina **overzicht** van de app-registratie.
- De client-ID (of toepassing). Haal deze waarde op op de pagina **overzicht** van de app-registratie.
- De URI van de omleiding van de client. Haal deze waarde op uit de **verificatie** -instellingen voor de registratie van uw app.
- De waarde van het client geheim. Haal deze waarde op van de locatie waarnaar u deze eerder hebt gekopieerd.

### <a name="create-a-storage-account-and-container"></a>Een opslag account en een container maken

Als u het code voorbeeld wilt uitvoeren, maakt u een opslag account in hetzelfde abonnement als uw Azure Active Directory. Maak vervolgens een container in dat opslag account. Met de voorbeeld code wordt een blok-Blob in deze container gemaakt.

Wijs vervolgens expliciet de rol **Storage BLOB data Inzender** toe aan het gebruikers account waaronder u de voorbeeld code gaat uitvoeren. Zie [de Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](storage-auth-aad-rbac-portal.md)voor instructies over het toewijzen van deze functie in de Azure Portal.

> [!NOTE]
> Wanneer u een Azure Storage-account maakt, worden er niet automatisch machtigingen toegewezen om toegang te krijgen tot gegevens via Azure AD. U moet uzelf expliciet een Azure-rol toewijzen voor Azure Storage. U kunt deze toewijzen op het niveau van uw abonnement, resource groep, opslag account of container of wachtrij.
>
> Voordat u een rol toewijst voor toegang tot gegevens, kunt u via de Azure Portal toegang krijgen tot gegevens in uw opslag account, omdat de Azure Portal ook de account sleutel kan gebruiken voor toegang tot gegevens. Zie [kiezen hoe u de toegang tot BLOB-gegevens in de Azure Portal autoriseren](../blobs/authorize-data-operations-portal.md)voor meer informatie.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Een webtoepassing maken die de toegang tot Blob Storage machtigt met Azure AD

Wanneer uw toepassing toegang heeft tot Azure Storage, gebeurt dit namens de gebruiker, dat wil zeggen dat BLOB-of wachtrij bronnen worden geopend met de machtigingen van de gebruiker die is aangemeld. Als u dit code voorbeeld wilt uitproberen, hebt u een webtoepassing nodig die de gebruiker vraagt om zich aan te melden met een Azure AD-identiteit. U kunt uw eigen app maken of de voorbeeld toepassing van micro soft gebruiken.

Een voltooide voor beeld-webtoepassing waarmee een token wordt opgehaald en wordt gebruikt voor het maken van een BLOB in Azure Storage is beschikbaar op [github](https://aka.ms/aadstorage). Het bekijken en uitvoeren van het voltooide voor beeld kan nuttig zijn bij het leren van de code voorbeelden. Zie de sectie [weer geven en het voltooide voor beeld uitvoeren](#view-and-run-the-completed-sample)voor instructies over het uitvoeren van het voltooide voor beeld.

#### <a name="add-references-and-using-statements"></a>Referenties toevoegen en instructies gebruiken  

Installeer de Azure Storage-client bibliotheek vanuit Visual Studio. Selecteer in het menu **Tools** de optie **NuGet Package Manager** en vervolgens **Package Manager Console**. Typ de volgende opdrachten in het console venster om de benodigde pakketten te installeren vanuit de Azure Storage-client bibliotheek voor .NET:

# <a name="net-v12-sdk"></a>[.NET V12-SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Voeg vervolgens de volgende using-instructies toe aan het HomeController.cs-bestand:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET V11-SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Voeg vervolgens de volgende using-instructies toe aan het HomeController.cs-bestand:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Een blok-Blob maken

Voeg het volgende code fragment toe om een blok-BLOB te maken. Vergeet niet om waarden tussen punt haken te vervangen door uw eigen waarden:

# <a name="net-v12-sdk"></a>[.NET V12-SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET V11-SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Als u BLOB-en wachtrij bewerkingen wilt autoriseren met een OAuth 2,0-token, moet u HTTPS gebruiken.

In het bovenstaande voor beeld verwerkt de .NET-client bibliotheek de autorisatie van de aanvraag om de blok-BLOB te maken. Azure Storage client bibliotheken voor andere talen, wordt ook de autorisatie van de aanvraag voor u afgehandeld. Als u echter een Azure Storage bewerking aanroept met een OAuth-token met behulp van de REST API, moet u de **autorisatie** -header samen stellen met het OAuth-token.

Als u BLOB-en Queue-service bewerkingen wilt aanroepen met OAuth-toegangs tokens, geeft u het toegangs token in de **autorisatie** -header door middel van het **Bearer** -schema en geeft u een service versie van 2017-11-09 of hoger op, zoals wordt weer gegeven in het volgende voor beeld:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Een toegangstoken ophalen uit Azure AD

Voeg vervolgens een methode toe die een token aanvraagt uit Azure AD namens de gebruiker. Met deze methode wordt het bereik gedefinieerd waarvoor machtigingen moeten worden toegekend. Zie [machtigingen en toestemming in het micro soft Identity platform-eind punt](../../active-directory/develop/v2-permissions-and-consent.md)voor meer informatie over machtigingen en bereiken.

Gebruik de resource-ID om het bereik te maken waarvoor het token moet worden verkregen. In het voor beeld wordt het bereik gemaakt met behulp van de resource-ID in combi natie met het ingebouwde `user_impersonation` bereik. Dit geeft aan dat het token wordt aangevraagd namens de gebruiker.

Houd er rekening mee dat u de gebruiker mogelijk moet presen teren met een interface die de gebruiker in staat stelt om toestemming te geven om de tokens in hun naam aan te vragen:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Toestemming is het proces van een gebruiker die toestemming verleent voor toegang tot beveiligde resources voor hun naam. Het micro soft Identity-platform biedt ondersteuning voor incrementele toestemming, wat inhoudt dat een beveiligingsprincipal in eerste instantie een minimale set machtigingen kan aanvragen en zo nodig machtigingen voor de tijd kan toevoegen. Wanneer uw code een toegangs token aanvraagt, geeft u het bereik van machtigingen op dat uw app nodig heeft. Zie [incrementele en dynamische toestemming](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)voor meer informatie over incrementele toestemming.

## <a name="view-and-run-the-completed-sample"></a>Het voltooide voor beeld weer geven en uitvoeren

Als u de voorbeeld toepassing wilt uitvoeren, moet u deze eerst klonen of downloaden via [github](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Werk vervolgens de toepassing bij zoals beschreven in de volgende secties.

### <a name="provide-values-in-the-settings-file"></a>Waarden opgeven in het instellingen bestand

Werk de *appsettings.jsvoor* bestand met uw eigen waarden als volgt bij:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>"
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Het opslag account en de container naam bijwerken

Werk in het *HomeController.cs* -bestand de URI bij die verwijst naar de blok-blob om de naam van uw opslag account en container te gebruiken, waarbij u de waarden tussen punt haken vervangt door uw eigen waarden:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Volgende stappen

- [Microsoft-identiteitsplatform](../../active-directory/develop/index.yml)
- [Toegangs rechten voor opslag gegevens beheren met Azure RBAC](./storage-auth-aad-rbac-portal.md)
- [Toegang tot blobs en wacht rijen verifiëren met Azure Active Directory en beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md)