---
title: Azure Batch Services verifiëren met Azure Active Directory
description: Batch ondersteunt Azure AD voor verificatie vanuit de batch-service. Lees hoe u zich op een van de volgende twee manieren kunt verifiëren.
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 685b84f1e628ea67689d3de8bf64c9641edba6fc
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920505"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Batch-service oplossingen verifiëren met Active Directory

Azure Batch ondersteunt verificatie met [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD is de multi tenant-Cloud service van micro soft voor het beheren van Directory-en identiteits beheer. Azure zelf gebruikt Azure AD voor de verificatie van klanten, service beheerders en gebruikers van de organisatie.

Wanneer u Azure AD-verificatie gebruikt met Azure Batch, kunt u op een van de volgende twee manieren verifiëren:

- Door **geïntegreerde verificatie** te gebruiken om een gebruiker te verifiëren die met de toepassing communiceert. Een toepassing die gebruikmaakt van geïntegreerde verificatie, verzamelt de referenties van een gebruiker en gebruikt deze referenties om de toegang tot batch-resources te verifiëren.
- Door gebruik te maken van een **Service-Principal** voor het verifiëren van een toepassing zonder toezicht. Een Service-Principal definieert het beleid en de machtigingen voor een toepassing om de toepassing te vertegenwoordigen bij het uitvoeren van toegang tot resources tijdens runtime.

Zie de [Azure Active Directory-documentatie](../active-directory/index.yml)voor meer informatie over Azure AD.

## <a name="endpoints-for-authentication"></a>Eind punten voor verificatie

Als u batch-toepassingen met Azure AD wilt verifiëren, moet u een aantal bekende eind punten in uw code toevoegen.

### <a name="azure-ad-endpoint"></a>Azure AD-eind punt

Het eind punt van de basis-CA van Azure AD is:

`https://login.microsoftonline.com/`

Voor verificatie met Azure AD gebruikt u dit eind punt samen met de Tenant-ID (Directory-ID). Met de Tenant-ID wordt de Azure AD-Tenant geïdentificeerd die moet worden gebruikt voor verificatie. Volg de stappen die worden beschreven in [de Tenant-id ophalen voor uw Azure Active Directory](#get-the-tenant-id-for-your-active-directory)om de Tenant-id op te halen:

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> Het Tenant-specifieke eind punt is vereist wanneer u verifieert met behulp van een service-principal.
>
> Het Tenant-specifieke eind punt is optioneel wanneer u verifieert met behulp van geïntegreerde verificatie, maar wordt aanbevolen. U kunt echter ook het gemeen schappelijke eind punt van Azure AD gebruiken. Het algemene eind punt biedt een algemene interface voor het verzamelen van referenties wanneer er geen specifieke Tenant is opgegeven. Het algemene eind punt is `https://login.microsoftonline.com/common` .

Zie [Verificatie versus autorisatie]()voor meer informatie over Azure AD-eind punten. /active-directory/develop/authentication-vs-authorization.md).

### <a name="batch-resource-endpoint"></a>Batch-resource-eind punt

Gebruik het **Azure batch resource-eind punt** om een token te verkrijgen voor het verifiëren van aanvragen voor de batch-service:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Uw toepassing registreren bij een Tenant

De eerste stap bij het gebruik van Azure AD voor verificatie is het registreren van uw toepassing in een Azure AD-Tenant. Als u uw toepassing registreert, kunt u de Azure [Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) aanroepen vanuit uw code. De ADAL biedt een API voor verificatie met Azure AD vanuit uw toepassing. Het registreren van uw toepassing is vereist, ongeacht of u een geïntegreerde verificatie of een Service-Principal wilt gebruiken.

Wanneer u uw toepassing registreert, geeft u informatie over uw toepassing op in azure AD. Azure AD biedt vervolgens een toepassings-ID (ook wel een *client-id* genoemd) die u gebruikt om uw toepassing te koppelen aan Azure ad tijdens runtime. Zie voor meer informatie over de toepassings-ID [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Als u uw batch-toepassing wilt registreren, volgt u de stappen in de sectie **een toepassing registreren** in [Quick Start: een toepassing registreren bij het micro soft Identity-platform](../active-directory/develop/quickstart-register-app.md). Als u uw toepassing registreert als een systeem eigen toepassing, kunt u een geldige URI voor de **omleidings-URI** opgeven. Het hoeft geen echt eind punt te zijn.

Nadat u uw toepassing hebt geregistreerd, ziet u de toepassings-ID:

![Scherm afbeelding van de toepassings-ID die wordt weer gegeven in de Azure Portal.](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>De Tenant-ID voor uw Active Directory ophalen

Met de Tenant-ID wordt de Azure AD-Tenant geïdentificeerd waarmee verificatie services voor uw toepassing worden geleverd. Voer de volgende stappen uit om de Tenant-ID op te halen:

1. Selecteer uw Active Directory in de Azure Portal.
1. Selecteer **Eigenschappen**.
1. Kopieer de GUID-waarde die is gegeven voor de **map-id**. Deze waarde wordt ook wel de Tenant-ID genoemd.

![Scherm opname van de map-ID in de Azure Portal.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Geïntegreerde verificatie gebruiken

Als u verificatie met geïntegreerde verificatie wilt uitvoeren, moet u de toepassings machtigingen verlenen om verbinding te maken met de API van de batch-service. Met deze stap kan uw toepassing aanroepen naar de API van de batch-service met Azure AD verifiëren.

Nadat u uw toepassing hebt geregistreerd, voert u de volgende stappen uit in de Azure Portal om deze toegang tot de batch-service te verlenen:

1. Kies in het Azure Portal **alle services** en selecteer vervolgens **app-registraties**.
1. Zoek de naam van uw toepassing in de lijst met app-registraties.
1. Selecteer de toepassing en selecteer **API-machtigingen**.
1. Selecteer in de sectie **API-machtigingen** de optie **een machtiging toevoegen**.
1. In **een API selecteren zoekt u** naar de batch-API. Zoek deze tekenreeksen totdat u de API hebt gevonden:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** is de id voor de Batch-API.
1. Wanneer u de batch-API hebt gevonden, selecteert u deze en kiest u **selecteren**.
1. In **machtigingen selecteren** selecteert u het selectie vakje naast **toegang tot Azure batch service** en selecteert u vervolgens **machtigingen toevoegen**.

De sectie **API-machtigingen** geeft nu aan dat uw Azure AD-toepassing toegang heeft tot zowel Microsoft Graph als de API van de batch-service. Er worden automatisch machtigingen verleend aan Microsoft Graph wanneer u uw app voor het eerst registreert bij Azure AD.

## <a name="use-a-service-principal"></a>Een service-principal gebruiken

Als u een toepassing wilt verifiëren die zonder toezicht wordt uitgevoerd, gebruikt u een service-principal. Nadat u uw toepassing hebt geregistreerd, voert u de volgende stappen uit in de Azure Portal om een service-principal te configureren:

1. Vraag een geheim aan uw toepassing.
1. Toegangs beheer op basis van rollen (Azure RBAC) toewijzen aan uw toepassing.

### <a name="request-a-secret-for-your-application"></a>Een geheim aanvragen voor uw toepassing

Wanneer uw toepassing wordt geverifieerd met een Service-Principal, verzendt deze zowel de toepassings-ID als een geheim naar Azure AD. U moet de geheime sleutel maken en kopiëren voor gebruik vanuit uw code.

Volg deze stappen in Azure Portal:

1. Kies in het Azure Portal **alle services**. Selecteer **app-registraties**.
1. Selecteer uw toepassing in de lijst met app-registraties.
1. Selecteer de toepassing en selecteer vervolgens **certificaten & geheimen**. Selecteer in de sectie **client geheimen** de optie **Nieuw client geheim**.
1. Als u een geheim wilt maken, voert u een beschrijving in voor het geheim. Selecteer vervolgens een verloop tijd voor het geheim van één jaar, twee jaar of geen verval datum.
1. Selecteer **toevoegen** om het geheim te maken en weer te geven. Kopieer de geheime waarde naar een veilige locatie, omdat u deze niet meer kunt openen nadat u de pagina verlaat.

### <a name="assign-azure-rbac-to-your-application"></a>Azure RBAC toewijzen aan uw toepassing

Als u een Service-Principal wilt verifiëren, moet u Azure RBAC toewijzen aan uw toepassing. Volg deze stappen:

1. Navigeer in het Azure Portal naar het batch-account dat door uw toepassing wordt gebruikt.
1. Selecteer **Access Control (IAM)** in het gedeelte **instellingen** van het batch-account.
1. Selecteer het tabblad **roltoewijzingen** .
1. Selecteer **Roltoewijzing toevoegen**.
1. Kies in de vervolg keuzelijst **functie** de rol *Inzender* of *lezer* voor uw toepassing. Zie [aan de slag met op rollen gebaseerd toegangs beheer van Azure in de Azure Portal](../role-based-access-control/overview.md)voor meer informatie over deze rollen.
1. Voer in het veld **selecteren** de naam van uw toepassing in. Selecteer uw toepassing in de lijst en selecteer vervolgens **Opslaan**.

Uw toepassing moet nu worden weer gegeven in de instellingen voor toegangs beheer waaraan een Azure-rol is toegewezen.

![Een Azure-rol toewijzen aan uw toepassing](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Een aangepaste rol toewijzen

Een aangepaste rol geeft een gedetailleerde machtiging voor een gebruiker voor het verzenden van taken, taken en meer. Dit biedt de mogelijkheid om te voor komen dat gebruikers bewerkingen uitvoeren die van invloed zijn op kosten, zoals het maken van Pools of het wijzigen van knoop punten.

U kunt een aangepaste rol gebruiken om machtigingen te verlenen aan een Azure AD-gebruiker,-groep of-service-principal voor de volgende Azure RBAC-bewerkingen:

- Microsoft.BatCH/batchAccounts/Pools/schrijven
- Microsoft.BatCH/batchAccounts/Pools/verwijderen
- Microsoft.BatCH/batchAccounts/Pools/lezen
- Microsoft.BatCH/batchAccounts/jobSchedules/write
- Microsoft.BatCH/batchAccounts/jobSchedules/verwijderen
- Microsoft.BatCH/batchAccounts/jobSchedules/lezen
- Microsoft.BatCH/batchAccounts/Jobs/schrijven
- Microsoft.BatCH/batchAccounts/Jobs/verwijderen
- Microsoft.BatCH/batchAccounts/Jobs/lezen
- Microsoft.BatCH/batchAccounts/certificaten/schrijven
- Microsoft.BatCH/batchAccounts/certificaten/verwijderen
- Microsoft.BatCH/batchAccounts/certificaten/lezen
- Microsoft.BatCH/batchAccounts/Read (voor elke Lees bewerking)
- Microsoft.BatCH/batchAccounts/Listkeys ophalen/Action (voor een wille keurige bewerking)

Aangepaste rollen zijn voor gebruikers die zijn geverifieerd door Azure AD, niet met de referenties van het batch-account (gedeelde sleutel). Houd er rekening mee dat de referenties van het batch-account volledige machtiging bieden voor het batch-account. Houd er ook rekening mee dat taken die gebruikmaken van de groeps beleidsniveau machtigingen vereisen.

> [!NOTE]
> Bepaalde roltoewijzingen moeten worden opgegeven in het actie veld, terwijl anderen moeten worden opgegeven in het veld DataAction. Zie bewerkingen voor Azure- [resource providers](../role-based-access-control/resource-provider-operations.md#microsoftbatch)voor meer informatie.

Hier volgt een voor beeld van een aangepaste roldefinitie:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Zie [aangepaste rollen in azure](../role-based-access-control/custom-roles.md)voor meer informatie over het maken van een aangepaste rol.

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>De Tenant-ID voor uw Azure Active Directory ophalen

Met de Tenant-ID wordt de Azure AD-Tenant geïdentificeerd waarmee verificatie services voor uw toepassing worden geleverd. Voer de volgende stappen uit om de Tenant-ID op te halen:

1. Selecteer uw Active Directory in de Azure Portal.
1. Selecteer **Eigenschappen**.
1. Kopieer de GUID-waarde die is gegeven voor de **map-id**. Deze waarde wordt ook wel de Tenant-ID genoemd.

![De map-ID kopiëren](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Codevoorbeelden

De code voorbeelden in deze sectie laten zien hoe u met Azure AD verifieert met geïntegreerde verificatie en met een service-principal. De meeste van deze code voorbeelden gebruiken .NET, maar de concepten zijn vergelijkbaar voor andere talen.

> [!NOTE]
> Een Azure AD-verificatie token verloopt na één uur. Wanneer u een lang bewaard **BatchClient** -object gebruikt, wordt u aangeraden een token op te halen uit ADAL op elke aanvraag om ervoor te zorgen dat u altijd een geldig token hebt.
>
> Als u dit wilt doen in .NET, schrijft u een methode die het token uit Azure AD ophaalt en geeft u deze methode door aan een **BatchTokenCredentials** -object als gemachtigde. De gemachtigde methode wordt aangeroepen voor elke aanvraag bij de batch-service om ervoor te zorgen dat er een geldig token wordt gegeven. ADAL-tokens worden standaard in de cache opgeslagen, zodat een nieuw token alleen wordt opgehaald uit Azure AD als dat nodig is. Zie voor meer informatie over tokens in azure AD [beveiligings tokens](../active-directory/develop/security-tokens.md).

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Code voorbeeld: Azure AD Integrated-verificatie gebruiken met batch .NET

Als u wilt verifiëren met geïntegreerde verificatie vanuit batch .NET, verwijst u naar het [Azure batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) -pakket en het [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -pakket.

Neem de volgende `using` instructies op in uw code:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Raadpleeg het Azure AD-eind punt in uw code, inclusief de Tenant-ID. Volg de stappen die worden beschreven in [de Tenant-id ophalen voor uw Azure Active Directory](#get-the-tenant-id-for-your-active-directory)om de Tenant-id op te halen:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Naslag het eind punt voor de batch-Service Resource:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referentie voor uw batch-account:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Geef de toepassings-ID (client-ID) op voor uw toepassing. De toepassings-ID is beschikbaar via de registratie van uw app in de Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Kopieer ook de omleidings-URI die u hebt opgegeven als u uw toepassing hebt geregistreerd als een systeem eigen toepassing. De omleidings-URI die is opgegeven in de code moet overeenkomen met de omleidings-URI die u hebt opgegeven tijdens het registreren van de toepassing:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Schrijf een call back-methode om het verificatie token van Azure AD te verkrijgen. De **GetAuthenticationTokenAsync** call back methode die hier wordt weer gegeven, roept ADAL aan om een gebruiker te verifiëren die met de toepassing werkt. De **AcquireTokenAsync** -methode die door ADAL wordt verstrekt, vraagt de gebruiker om zijn referenties en de toepassing wordt uitgevoerd zodra de gebruiker deze heeft verstrekt (tenzij de referenties al in de cache zijn opgeslagen):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Maak een **BatchTokenCredentials** -object dat de gemachtigde als para meter gebruikt. Gebruik deze referenties om een **BatchClient** -object te openen. U kunt dat **BatchClient** -object gebruiken voor volgende bewerkingen voor de batch-service:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Code voorbeeld: een Azure AD-Service-Principal gebruiken met batch .NET

Als u wilt verifiëren met een Service-Principal vanuit batch .NET, verwijst u naar het [Azure batch .net](https://www.nuget.org/packages/Azure.Batch/) -pakket en het [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -pakket.

Neem de volgende `using` instructies op in uw code:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Raadpleeg het Azure AD-eind punt in uw code, inclusief de Tenant-ID. Wanneer u een Service-Principal gebruikt, moet u een Tenant-specifiek eind punt opgeven. Volg de stappen die worden beschreven in [de Tenant-id ophalen voor uw Azure Active Directory](#get-the-tenant-id-for-your-active-directory)om de Tenant-id op te halen:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Naslag het eind punt voor de batch-Service Resource:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referentie voor uw batch-account:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Geef de toepassings-ID (client-ID) op voor uw toepassing. De toepassings-ID is beschikbaar via de registratie van uw app in de Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Geef de geheime sleutel op die u hebt gekopieerd uit de Azure Portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Schrijf een call back-methode om het verificatie token van Azure AD te verkrijgen. De **GetAuthenticationTokenAsync** call back methode die hier wordt weer gegeven, roept ADAL aan voor verificatie zonder toezicht:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Maak een **BatchTokenCredentials** -object dat de gemachtigde als para meter gebruikt. Gebruik deze referenties om een **BatchClient** -object te openen. Gebruik vervolgens dat **BatchClient** -object voor volgende bewerkingen voor de batch-service:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Code voorbeeld: een Azure AD-Service-Principal gebruiken met batch python

Als u wilt verifiëren met een Service-Principal vanuit batch python, installeert en raadpleegt u de [Azure-batch](https://pypi.org/project/azure-batch/) -en [Azure-algemene](https://pypi.org/project/azure-common/) modules.

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Wanneer u een Service-Principal gebruikt, moet u de Tenant-ID opgeven. Volg de stappen die worden beschreven in [de Tenant-id ophalen voor uw Azure Active Directory](#get-the-tenant-id-for-your-active-directory)om de Tenant-id op te halen:

```python
TENANT_ID = "<tenant-id>"
```

Naslag het eind punt voor de batch-Service Resource:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Referentie voor uw batch-account:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Geef de toepassings-ID (client-ID) op voor uw toepassing. De toepassings-ID is beschikbaar via de registratie van uw app in de Azure Portal:

```python
CLIENT_ID = "<application-id>"
```

Geef de geheime sleutel op die u hebt gekopieerd uit de Azure Portal:

```python
SECRET = "<secret-key>"
```

Een **ServicePrincipalCredentials** -object maken:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Gebruik de referenties van de Service-Principal om een **BatchServiceClient** -object te openen. Gebruik vervolgens dat **BatchServiceClient** -object voor volgende bewerkingen voor de batch-service.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [Azure Active Directory-documentatie](../active-directory/index.yml). Gedetailleerde voor beelden waarin wordt getoond hoe u ADAL kunt gebruiken, zijn beschikbaar in de [Azure code samples](https://azure.microsoft.com/resources/samples/?service=active-directory) -bibliotheek.
- Meer informatie over [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) en [hoe u een Azure AD-toepassing en Service-Principal maakt die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md).
- Meer informatie over het [verifiëren van oplossingen voor batch beheer met Active Directory](batch-aad-auth-management.md).
- Voor een python-voor beeld van het maken van een batch-client die is geverifieerd met behulp van een Azure AD-token, zie de [implementatie Azure batch aangepaste installatie kopie met een python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) -voorbeeld script.

