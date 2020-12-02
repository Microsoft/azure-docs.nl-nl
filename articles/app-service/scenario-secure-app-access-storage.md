---
title: 'Zelfstudie: Web-app krijgt toegang tot opslag met behulp van beheerde identiteiten | Azure'
description: In deze zelfstudie leert u hoe u toegang tot Azure Storage voor een app krijgt met behulp van beheerde identiteiten.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 250e95b33b985aedcc1b1537f57338d29e848451
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96020208"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Zelfstudie: Toegang tot Azure Storage krijgen vanuit een web-app

Leer hoe u voor een web-app (in plaats van een aangemelde gebruiker) die wordt uitgevoerd in Azure App Service, toegang tot Azure Storage krijgt met behulp van beheerde identiteiten.

:::image type="content" alt-text="Diagram dat laat zien hoe u toegang krijgt tot opslag." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

U wilt toegang toevoegen aan het Azure-gegevensvlak (Azure Storage, Azure SQL Database, Azure Key Vault of andere services) vanuit uw web-app. U zou een gedeelde sleutel kunnen gebruiken, maar dan zit u met het probleem van operationele beveiliging en wie het geheim kan maken, implementeren en beheren. Het is ook mogelijk de sleutel in te checken bij GitHub, maar hackers weten hoe ze daarop kunnen scannen. Een veiligere manier om uw web-app toegang tot gegevens te geven, is [beheerde identiteiten](/azure/active-directory/managed-identities-azure-resources/overview) te gebruiken.

Met een beheerde identiteit van Azure Active Directory (Azure AD) kan App Service toegang tot resources krijgen via RBAC (op rollen gebaseerd toegangsbeheer), zonder dat daar app-referenties voor nodig zijn. Nadat een beheerde identiteit aan uw web-app is toegewezen, wordt er in Azure een certificaat gemaakt en gedistribueerd. Mensen hoeven zich geen zorgen te maken over het beheren van geheimen of app-referenties.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een door het systeem toegewezen beheerde identiteit maken in een web-app
> * Een opslagaccount en Azure Blob Storage-container maken
> * Toegang tot opslag krijgen vanuit een web-app met behulp van beheerde identiteiten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een webtoepassing die wordt uitgevoerd in Azure App Service waarvoor de [module App Service-verificatie/-autorisatie is ingeschakeld](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Een beheerde identiteit inschakelen voor een app

Als u uw web-app via Visual Studio maakt en publiceert, was de beheerde identiteit al voor u ingeschakeld in uw app. In uw app-service selecteert u **Identiteit** in het linkerdeelvenster en selecteert u vervolgens **Door het systeem toegewezen**. Verifieer dat **Status** is ingesteld op **Aan**. Als dat niet het geval is, selecteert u **Opslaan** en vervolgens **Ja** om de door het systeem toegewezen beheerde identiteit in te schakelen. Wanneer de beheerde identiteit is ingeschakeld, is de status ingesteld op **Aan** en is de object-id beschikbaar.

:::image type="content" alt-text="Schermopname van de optie door het systeem toegewezen identiteit." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Met deze stap wordt een nieuwe object-id gemaakt, die anders is dan de app-id die in het deelvenster **Verificatie/autorisatie** wordt gemaakt. Kopieer de object-id van de door het systeem toegewezen beheerde identiteit. U hebt deze later nodig.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Een opslagaccount en Blob Storage-container maken

U bent nu klaar om een opslagaccount en Blob Storage-container te maken.

Elk opslagaccount moet behoren tot een Azure-resourcegroep. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resourcegroep maakt.

Een v2-opslagaccount voor algemeen gebruik biedt toegang tot alle services van Azure Storage: blobs, bestanden, wachtrijen, tabellen en schijven. Met de stappen die hier worden uiteengezet, maakt u een v2-opslagaccount voor algemeen gebruik, maar de stappen voor het maken van een ander soort opslagaccount zijn vergelijkbaar.

Blobs in Azure Storage worden georganiseerd in containers. Voordat u later in deze zelfstudie een blob kunt uploaden, moet u een container maken.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u een v2-opslagaccount voor algemeen gebruik wilt maken in Azure Portal, volgt u deze stappen.

1. Selecteer **Alle services** in het menu van Azure Portal. Voer in de lijst met resources **Opslagaccounts** in. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Opslagaccounts**.

1. Selecteer in het venster **Opslagaccounts** dat wordt weergegeven de optie **Toevoegen**.

1. Selecteer het abonnement waarin u het opslagaccount wilt maken.

1. Selecteer in het vervolgkeuzemenu onder het veld **Resourcegroep** de resourcegroep die uw web-app bevat.

1. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en alleen cijfers en kleine letters bevatten.

1. Selecteer een locatie voor uw opslagaccount of gebruik de standaardlocatie.

1. Laat deze velden ingesteld staan op de standaardwaarden:

    |Veld|Waarde|
    |--|--|
    |Implementatiemodel|Resource Manager|
    |Prestaties|Standard|
    |Soort account|StorageV2 (algemeen gebruik v2)|
    |Replicatie|Geografisch redundante opslag met leestoegang (RA-GRS)|
    |Toegangslaag|Dynamisch|

1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.

1. Selecteer **Maken**.

Volg deze stappen om een Blob Storage-container in Azure Storage te maken.

1. Ga naar het nieuwe opslagaccount in Azure Portal.

1. Scrol in het linkermenu voor het opslagaccount naar de sectie **Blob service** en selecteer **Containers**.

1. Selecteer de knop **+ Container**.

1. Typ een naam voor de nieuwe container. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten.

1. Stel het niveau van openbare toegang tot de container in. Het standaardniveau is **Persoonlijk (geen anonieme toegang)**.

1. Selecteer **OK** om de container te maken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voer het volgende script uit om een v2-opslagaccount voor algemeen gebruik en een Blob Storage-container te maken. Geef de naam op van de resourcegroep die uw web-app bevat. Voer een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en alleen cijfers en kleine letters bevatten.

Geef de locatie voor uw opslagaccount op. Voer ```Get-AzLocation | select Location``` uit om een lijst weer te geven met locaties die geldig zijn voor uw abonnement. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer het volgende script uit om een v2-opslagaccount voor algemeen gebruik en een Blob Storage-container te maken. Geef de naam op van de resourcegroep die uw web-app bevat. Voer een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en alleen cijfers en kleine letters bevatten. 

Geef de locatie voor uw opslagaccount op. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten.

In het volgende voorbeeld wordt uw Azure AD-account gebruikt om de bewerking voor het maken van de container te autoriseren. Voordat u de container maakt, moet u de rol Storage Blob Data Contributor aan uzelf toewijzen. Zelfs als u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevensbewerkingen uit te voeren voor het opslagaccount.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Toegang tot het opslagaccount verlenen

U moet uw web-app toegang tot het opslagaccount verlenen voordat u blobs kunt maken, lezen of verwijderen. In een vorige stap hebt u de web-app die in App Service wordt uitgevoerd, geconfigureerd met een beheerde identiteit. Met behulp van Azure RBAC kunt u de beheerde identiteit toegang tot een andere resource geven, net zoals elke beveiligings-principal. De rol Bijdrager voor opslagblobgegevens geeft de web-app (vertegenwoordigd door de door het systeem toegewezen beheerde identiteit) lees-, schrijf- en verwijdertoegang tot de blobcontainer en -gegevens.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga in de [Azure-portal](https://portal.azure.com) naar uw opslagaccount om uw web-app toegang te verlenen. Selecteer **Toegangsbeheer (IAM)** in het linkernavigatievenster en selecteer vervolgens **Roltoewijzingen**. U ziet dan een lijst met de personen die toegang tot het opslagaccount hebben. U wilt nu een roltoewijzing toevoegen aan een robot, de app-service die toegang tot het opslagaccount nodig heeft. Selecteer **Toevoegen** > **Roltoewijzing toevoegen**.

Bij **Rol** selecteert u **Bijdrager voor opslagblobgegevens** om uw web-app leestoegang tot opslagblobs te geven. Bij **Toegang toewijzen aan** selecteert u **App Service**. Bij **Abonnement** selecteert u uw abonnement. Selecteer vervolgens de app-service die u toegang wilt verlenen. Selecteer **Opslaan**.

:::image type="content" alt-text="Schermopname van Roltoewijzing toevoegen." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Uw web-app heeft nu toegang tot uw opslagaccount.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voer het volgende script uit om de rol Bijdrager voor opslagblobgegevens toe te wijzen aan uw web-app (vertegenwoordigd door een door het systeem toegewezen beheerde identiteit) in uw opslagaccount.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer het volgende script uit om de rol Bijdrager voor opslagblobgegevens toe te wijzen aan uw web-app (vertegenwoordigd door een door het systeem toegewezen beheerde identiteit) in uw opslagaccount.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Toegang tot Blob Storage krijgen (.NET)

De klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) wordt gebruikt om een tokenreferentie voor uw code op te halen om aanvragen voor Azure Storage te autoriseren. Maak een exemplaar van de klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), die gebruikmaakt van de beheerde identiteit om tokens op te halen en aan de serviceclient te koppelen. Met het volgende codevoorbeeld wordt de geverifieerde tokenreferentie opgehaald en gebruikt om een serviceclientobject te maken, waarmee een nieuwe blob wordt geüpload.

### <a name="install-client-library-packages"></a>Clientbibliotheekpakketten installeren

Installeer het [NuGet-pakket Blob Storage](https://www.nuget.org/packages/Azure.Storage.Blobs/) om te werken met Blob Storage en installeer het [NuGet-pakket Azure Identity-clientbibliotheek voor .NET](https://www.nuget.org/packages/Azure.Identity/) om te verifiëren met Azure AD-referenties. Installeer de clientbibliotheken met behulp van de opdrachtregelinterface van .NET Core of de Package Manager Console in Visual Studio.

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

Open een opdrachtregel en ga naar de map die uw projectbestand bevat.

Voer de installatieopdrachten uit.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Package Manager](#tab/package-manager)

Open het project of de oplossing in Visual Studio en open de console met behulp van de opdracht **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager Console**.

Voer de installatieopdrachten uit.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Voorbeeld

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met deze zelfstudie en de web-app of bijbehorende resources niet meer nodig hebt, kunt u [de gemaakte resources opschonen](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Een door het systeem toegewezen beheerde identiteit maken
> * Een opslagaccount en Blob Storage-container maken
> * Toegang tot opslag krijgen vanuit een web-app met behulp van beheerde identiteiten

> [!div class="nextstepaction"]
> [App Service krijgt toegang tot Microsoft Graph namens de gebruiker](scenario-secure-app-access-microsoft-graph-as-user.md)
