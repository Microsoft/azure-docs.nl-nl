---
title: Verificatie instellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen en configureren van verificatie voor verschillende resources en werk stromen in Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: ee767cbeafcce604ecb4a79ce0fdf21c70bbb47c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219634"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Verificatie instellen voor Azure Machine Learning-resources en -werkstromen


Meer informatie over het instellen van verificatie voor uw Azure Machine Learning-werk ruimte. Verificatie voor uw Azure Machine Learning-werk ruimte is gebaseerd op __Azure Active Directory__ (Azure AD) voor de meeste zaken. Over het algemeen zijn er drie verificatie werk stromen die u kunt gebruiken bij het maken van verbinding met de werk ruimte:

* __Interactief__: u gebruikt uw account in azure Active Directory om ofwel rechtstreeks te verifiëren, of om een token op te halen dat wordt gebruikt voor verificatie. Interactieve verificatie wordt gebruikt tijdens het _experimenteren en iteratieve ontwikkeling_. Met interactieve verificatie kunt u de toegang tot resources (zoals een webservice) per gebruiker beheren.

* __Service-Principal__: u maakt een Service-Principal-account in azure Active Directory en gebruikt dit om een token te verifiëren of op te halen. Een service-principal wordt gebruikt wanneer u een _geautomatiseerd proces_ nodig hebt om te verifiëren bij de service zonder tussen komst van de gebruiker. Een voor beeld: een script voor continue integratie en implementatie waarmee een model wordt getraind en getest wanneer de trainings code verandert.

* __Beheerde identiteit__: wanneer u de Azure machine learning SDK gebruikt _op een virtuele machine van Azure_, kunt u een beheerde identiteit voor Azure gebruiken. Met deze werk stroom kan de virtuele machine verbinding maken met de werk ruimte met behulp van de beheerde identiteit, zonder dat er referenties worden opgeslagen in de python-code of dat de gebruiker wordt gevraagd om te verifiëren. Azure Machine Learning compute-clusters kunnen ook worden geconfigureerd voor het gebruik van een beheerde identiteit om toegang te krijgen tot de werk ruimte wanneer u een _trainings model_ gebruikt.

> [!IMPORTANT]
> Ongeacht de gebruikte verificatie werk stroom, wordt Azure RBAC (op rollen gebaseerd toegangs beheer) gebruikt om het toegangs niveau (autorisatie) te bereiken dat is toegestaan voor de resources. Een beheer-of automatiserings proces kan bijvoorbeeld toegang hebben tot het maken van een reken instantie, maar deze niet gebruiken, terwijl een Data wetenschapper deze kan gebruiken, maar niet kan verwijderen of maken. Zie [toegang tot Azure machine learning werk ruimte beheren](how-to-assign-roles.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md) maken.
* [Configureer uw ontwikkel omgeving](how-to-configure-environment.md) om de Azure machine learning SDK te installeren, of gebruik een [Azure machine learning Compute-exemplaar](concept-azure-machine-learning-architecture.md#compute-instance) waarbij de SDK al is geïnstalleerd.

## <a name="azure-active-directory"></a>Azure Active Directory

Alle verificatie werk stromen voor uw werk ruimte zijn afhankelijk van Azure Active Directory. Als u gebruikers wilt laten verifiëren met behulp van afzonderlijke accounts, moeten ze accounts hebben in uw Azure AD. Als u service-principals wilt gebruiken, moeten deze bestaan in uw Azure AD. Beheerde identiteiten zijn ook een functie van Azure AD. 

Zie [Wat is Azure Active Directory-verificatie](..//active-directory/authentication/overview-authentication.md)voor meer informatie over Azure AD.

Wanneer u de Azure AD-accounts hebt gemaakt, raadpleegt u [toegang tot Azure machine learning werk ruimte beheren](how-to-assign-roles.md) voor informatie over het verlenen van toegang tot de werk ruimte en andere bewerkingen in azure machine learning.

## <a name="configure-a-service-principal"></a>Een service-principal configureren

Als u een service-principal (SP) wilt gebruiken, moet u eerst de SP maken en toegang verlenen tot uw werk ruimte. Zoals eerder vermeld, wordt op Azure-rollen gebaseerd toegangs beheer (Azure RBAC) gebruikt om de toegang te beheren. u moet dus ook bepalen welke toegangs rechten de SP moeten verlenen.

> [!IMPORTANT]
> Als u een Service-Principal gebruikt, geeft u deze de __Mini maal vereiste toegang voor de taak__ waarvoor deze wordt gebruikt. U kunt bijvoorbeeld geen service principal-eigenaar of Inzender toegang verlenen als u voor alle gebruikt voor het lezen van het toegangs token voor een webimplementatie.
>
> De reden voor het verlenen van de minimale toegang is dat een Service-Principal gebruikmaakt van een wacht woord voor verificatie en dat het wacht woord kan worden opgeslagen als onderdeel van een Automation-script. Als het wacht woord wordt gelekt, en de mini maal vereiste toegang voor een bepaalde taken, minimaliseert het schadelijke gebruik van de SP.

De eenvoudigste manier om een SP te maken en toegang te verlenen tot uw werk ruimte is met behulp van de [Azure cli](/cli/azure/install-azure-cli). Als u een Service-Principal wilt maken en toegang tot uw werk ruimte wilt verlenen, gebruikt u de volgende stappen:

> [!NOTE]
> U moet een beheerder zijn van het abonnement om al deze stappen uit te voeren.

1. Verifiëren bij uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de aanwijzingen op de opdrachtregel volgen. Dit omvat het bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en het invoeren van een autorisatiecode.

    Als u meerdere Azure-abonnementen hebt, kunt u de `az account set -s <subscription name or ID>` opdracht gebruiken om het abonnement in te stellen. Zie [meerdere Azure-abonnementen gebruiken](/cli/azure/manage-azure-subscriptions-azure-cli)voor meer informatie.

    Zie [Aanmelden met Azure cli](/cli/azure/authenticate-azure-cli)voor andere verificatie methoden.

1. De service-principal maken. In het volgende voor beeld wordt een SP **met de naam ml-auth** gemaakt:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    De uitvoer is vergelijkbaar met het volgende. Noteer de `clientId` `clientSecret` velden, en, `tenantId` zoals u deze nodig hebt voor andere stappen in dit artikel.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. De Details voor de Service-Principal ophalen met de `clientId` waarde die is geretourneerd in de vorige stap:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    De volgende JSON is een vereenvoudigd voor beeld van de uitvoer van de opdracht. Noteer het `objectId` veld, omdat u de waarde voor de volgende stap nodig hebt.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Toestaan dat de SP toegang heeft tot uw Azure Machine Learning-werk ruimte. U hebt de naam van uw werk ruimte en de naam van de resource groep nodig voor `-w` `-g` respectievelijk de para meters en. Voor de `--user` para meter gebruikt u de `objectId` waarde uit de vorige stap. `--role`Met de para meter kunt u de Access-rol voor de service-principal instellen. In het volgende voor beeld wordt de SP toegewezen aan de rol **eigenaar** . 

    > [!IMPORTANT]
    > Met de functie voor toegang tot eigenaar kan de Service-Principal vrijwel elke bewerking in uw werk ruimte uitvoeren. Het wordt in dit document gebruikt om te demonstreren hoe toegang wordt verleend. in een productie omgeving raadt micro soft aan de service-principal de minimale toegang te geven die nodig is voor het uitvoeren van de rol die u wilt gebruiken. Zie [toegang tot Azure machine learning werk ruimte beheren](how-to-assign-roles.md)voor meer informatie over het maken van een aangepaste rol met de toegang die nodig is voor uw scenario.

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Deze aanroep produceert geen uitvoer bij geslaagde pogingen.

## <a name="configure-a-managed-identity"></a>Een beheerde identiteit configureren

> [!IMPORTANT]
> Beheerde identiteit wordt alleen ondersteund bij het gebruik van de Azure Machine Learning SDK van een virtuele machine van Azure of met een Azure Machine Learning Compute-Cluster. Het gebruik van een beheerde identiteit met een reken cluster is momenteel beschikbaar als preview-versie.

### <a name="managed-identity-with-a-vm"></a>Beheerde identiteit met een virtuele machine

1. Een [door het systeem toegewezen beheerde identiteit inschakelen voor Azure-resources op de VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. Selecteer in de [Azure Portal](https://portal.azure.com)uw werk ruimte en selecteer vervolgens __Access Control (IAM)__, __functie toewijzing toevoegen__ en selecteer __virtuele machine__ in de vervolg keuzelijst __toegang toewijzen aan__ . Selecteer ten slotte de identiteit van uw virtuele machine.

1. Selecteer de rol die aan deze identiteit moet worden toegewezen. Bijvoorbeeld Inzender of een aangepaste rol. Zie [toegang tot resources beheren](how-to-assign-roles.md)voor meer informatie.

### <a name="managed-identity-with-compute-cluster"></a>Beheerde identiteit met reken cluster

Zie [Managed Identity voor Compute Cluster instellen](how-to-create-attach-compute-cluster.md#managed-identity)voor meer informatie.

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Interactieve verificatie gebruiken

> [!IMPORTANT]
> Interactieve verificatie maakt gebruik van uw browser en vereist cookies (inclusief cookies van derden). Als u cookies hebt uitgeschakeld, wordt er mogelijk een fout bericht weer gegeven zoals ' we kunnen u niet aanmelden '. Deze fout kan ook optreden als u [Azure AD multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)hebt ingeschakeld.

De meeste voor beelden in de documentatie en voor beelden gebruiken interactieve verificatie. Wanneer u de SDK gebruikt, zijn er bijvoorbeeld twee functie aanroepen waarmee automatisch een verificatie stroom op basis van een gebruikers interface wordt gevraagd:

* Bij het aanroepen van de `from_config()` functie wordt de prompt weer gegeven.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    De functie `from_config()` zoekt naar een JSON-bestand met de verbindingsgegevens van de werkruimte.

* Met de `Workspace` constructor om abonnements-, resource groep-en werkruimte gegevens op te geven, wordt u ook gevraagd om interactieve verificatie uit te voeren.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Als u toegang hebt tot meerdere tenants, moet u de klasse wellicht importeren en expliciet definiëren welke Tenant u wilt richten. Als u de constructor aanroept voor `InteractiveLoginAuthentication` , wordt u gevraagd om u aan te melden, vergelijkbaar met de bovenstaande oproepen.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Wanneer u de Azure CLI gebruikt, `az login` wordt de opdracht gebruikt om de CLI-sessie te verifiëren. Zie [aan de slag met Azure cli](/cli/azure/get-started-with-azure-cli)voor meer informatie.

> [!TIP]
> Als u de SDK gebruikt vanuit een omgeving waarin u zich eerder interactief hebt geverifieerd met behulp van Azure CLI, kunt u de `AzureCliAuthentication` klasse gebruiken om te verifiëren bij de werk ruimte met de referenties die in de cache zijn opgeslagen door de cli:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Service-Principal-verificatie gebruiken

Als u de werk ruimte wilt verifiëren vanuit de SDK met behulp van een Service-Principal, gebruikt u de `ServicePrincipalAuthentication` constructor class. Gebruik de waarden die u hebt gekregen bij het maken van de service provider als de para meters. De `tenant_id` para meter wordt toegewezen aan `tenantId` van boven, wordt `service_principal_id` toegewezen aan `clientId` en wordt `service_principal_password` toegewezen aan `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

De `sp` variabele bevat nu een verificatie object dat u rechtstreeks in de SDK gebruikt. Over het algemeen is het een goed idee om de id's/geheimen die hierboven worden gebruikt, op te slaan in omgevings variabelen, zoals in de volgende code wordt weer gegeven. Als u in omgevings variabelen opslaat, voor komt u dat de gegevens per ongeluk in een GitHub-opslag plaats worden gecontroleerd.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Voor automatische werk stromen die worden uitgevoerd in Python en de SDK voornamelijk gebruiken, kunt u dit object in de meeste gevallen gebruiken voor uw verificatie. De volgende code verifieert uw werk ruimte met behulp van het auth-object dat u hebt gemaakt.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Een service-principal van de Azure CLI gebruiken

U kunt een Service-Principal gebruiken voor Azure CLI-opdrachten. Zie [Aanmelden met een Service-Principal](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)voor meer informatie.

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Een Service-Principal gebruiken met de REST API (preview-versie)

De service-principal kan ook worden gebruikt voor de verificatie van de Azure Machine Learning [rest API](/rest/api/azureml/) (preview). U gebruikt de Azure Active Directory [toekennings stroom voor client referenties](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md), waarmee service-to-service-aanroepen kunnen worden uitgevoerd voor headless authenticatie in automatische werk stromen. De voor beelden worden geïmplementeerd met de [ADAL-bibliotheek](../active-directory/azuread-dev/active-directory-authentication-libraries.md) in zowel Python als Node.js, maar u kunt ook een open-source bibliotheek gebruiken die openid connect Connect 1,0 ondersteunt.

> [!NOTE]
> MSAL.js is een nieuwe bibliotheek dan ADAL, maar u kunt geen service-to-service-verificatie uitvoeren met behulp van client referenties met MSAL.js, omdat het hoofd zakelijk een bibliotheek aan de client zijde is die is bedoeld voor interactieve/UI-verificatie die is gekoppeld aan een specifieke gebruiker. We raden u aan ADAL te gebruiken zoals hieronder wordt weer gegeven om automatische werk stromen te bouwen met de REST API.

#### <a name="nodejs"></a>Node.js

Gebruik de volgende stappen om een verificatie token te genereren met behulp van Node.js. Voer uit in uw omgeving `npm install adal-node` . Gebruik vervolgens uw `tenantId` , `clientId` , en `clientSecret` van de service-principal die u in de bovenstaande stappen hebt gemaakt als waarden voor de overeenkomende variabelen in het volgende script.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

De variabele `tokenResponse` is een object dat het token en de gekoppelde meta gegevens bevat, zoals de verloop tijd. Tokens zijn geldig gedurende 1 uur en kunnen worden vernieuwd door dezelfde aanroep opnieuw uit te voeren om een nieuw token op te halen. Het volgende code fragment is een voor beeld van een antwoord.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Gebruik de `accessToken` eigenschap om het verificatie token op te halen. Raadpleeg de [rest API-documentatie](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) voor voor beelden over het gebruik van het token voor het maken van API-aanroepen.

#### <a name="python"></a>Python

Gebruik de volgende stappen om een verificatie token te genereren met behulp van python. Voer uit in uw omgeving `pip install adal` . Gebruik vervolgens uw `tenantId` , `clientId` , en `clientSecret` van de service-principal die u in de bovenstaande stappen hebt gemaakt als waarden voor de juiste variabelen in het volgende script.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

De variabele `token_response` is een woorden lijst die het token en de gekoppelde meta gegevens bevat, zoals de verval tijd. Tokens zijn geldig gedurende 1 uur en kunnen worden vernieuwd door dezelfde aanroep opnieuw uit te voeren om een nieuw token op te halen. Het volgende code fragment is een voor beeld van een antwoord.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Gebruiken `token_response["accessToken"]` om het verificatie token op te halen. Raadpleeg de [rest API-documentatie](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) voor voor beelden over het gebruik van het token voor het maken van API-aanroepen.

#### <a name="java"></a>Java

Haal in Java het Bearer-token op met behulp van een standaard REST-aanroep:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

De voor gaande code zou moeten de uitzonde ringen en andere status codes moeten afhandelen `200 OK` , maar het patroon wordt weer gegeven: 

- Gebruik de client-ID en het geheim om te controleren of uw programma toegang moet hebben
- Uw Tenant-ID gebruiken om op te geven waar `login.microsoftonline.com` moet worden gezocht
- Azure Resource Manager gebruiken als de bron van het autorisatie token

## <a name="use-managed-identity-authentication"></a>Beheerde identiteits verificatie gebruiken

Gebruik de-klasse om te verifiëren bij de werk ruimte van een virtuele machine of een berekenings cluster dat is geconfigureerd met een beheerde identiteit `MsiAuthentication` . In het volgende voor beeld ziet u hoe u deze klasse kunt gebruiken om te verifiëren bij een werk ruimte:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Volgende stappen

* [Geheimen gebruiken in trainingen](how-to-use-secrets-in-runs.md).
* [Verificatie configureren voor modellen die als een webservice worden geïmplementeerd](how-to-authenticate-web-service.md).
* [Een Azure Machine Learning-model gebruiken dat als een webservice is geïmplementeerd](how-to-consume-web-service.md).
