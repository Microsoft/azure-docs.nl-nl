---
title: Toegang verlenen tot gegevens met een beheerde identiteit
titleSuffix: Azure Storage
description: Gebruik beheerde identiteiten voor Azure-resources om toegang tot blob- en wachtrijgegevens te autoreren vanuit toepassingen die worden uitgevoerd in Azure-VM's, functie-apps en andere.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2aa6730759a9aa1aaab3156c55bf19e82641b8ea
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739328"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Toegang verlenen tot blob- en wachtrijgegevens met beheerde identiteiten voor Azure-resources

Azure Blob- en Queue Storage ondersteunen Azure Active Directory verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources.](../../active-directory/managed-identities-azure-resources/overview.md) Beheerde identiteiten voor Azure-resources kunnen toegang verlenen tot blob- en wachtrijgegevens met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in virtuele Azure-machines (VM's), functie-apps, virtuele-machineschaalsets en andere services. Door beheerde identiteiten voor Azure-resources samen met Azure AD-verificatie te gebruiken, kunt u voorkomen dat referenties worden opgeslagen bij uw toepassingen die in de cloud worden uitgevoerd.  

In dit artikel wordt beschreven hoe u toegang tot blob- of wachtrijgegevens van een Azure-VM autoreert met behulp van beheerde identiteiten voor Azure-resources. Ook wordt beschreven hoe u uw code in de ontwikkelomgeving test.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een VM inschakelen

Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om toegang te verlenen tot blobs en wachtrijen vanaf uw VM, moet u eerst beheerde identiteiten inschakelen voor Azure-resources op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-clientbibliotheken](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Zie Beheerde identiteiten voor Azure-resources voor meer informatie over [beheerde identiteiten.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Verifiëren met de Azure Identity-bibliotheek

De Azure Identity-clientbibliotheek biedt ondersteuning voor Azure AD-tokenverificatie voor de [Azure SDK.](https://github.com/Azure/azure-sdk) De nieuwste versies van de Azure Storage-clientbibliotheken voor .NET, Java, Python en JavaScript kunnen worden geïntegreerd met de Azure Identity-bibliotheek om een eenvoudige en veilige manier te bieden om een OAuth 2.0-token te verkrijgen voor autorisatie van Azure Storage-aanvragen.

Een voordeel van de Azure Identity-clientbibliotheek is dat u hiermee dezelfde code kunt gebruiken om te verifiëren of uw toepassing wordt uitgevoerd in de ontwikkelomgeving of in Azure. De Azure Identity-clientbibliotheek voor .NET verifieert een beveiligingsprincipaal. Wanneer uw code wordt uitgevoerd in Azure, is de beveiligingsprincipaal een beheerde identiteit voor Azure-resources. In de ontwikkelomgeving bestaat de beheerde identiteit niet, dus de clientbibliotheek verifieert de gebruiker of een service-principal voor testdoeleinden.

Na de authenticatie krijgt de Azure Identity-clientbibliotheek een tokenreferentie. Deze tokenreferentie wordt vervolgens ingekapseld in het serviceclientobject dat u maakt om bewerkingen uit te voeren op Azure Storage. De bibliotheek verwerkt dit naadloos voor u door de juiste tokenreferenties op te zoeken.

Zie Azure Identity-clientbibliotheek voor .NET voor meer informatie over de [Azure Identity-clientbibliotheek voor .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) Zie Azure.Identity Namespace voor referentiedocumentatie voor de [Azure Identity-clientbibliotheek.](/dotnet/api/azure.identity)

### <a name="assign-azure-roles-for-access-to-data"></a>Azure-rollen toewijzen voor toegang tot gegevens

Wanneer een Azure AD-beveiligingsprincipaal toegang probeert te krijgen tot blob- of wachtrijgegevens, moet die beveiligingsprincipaal machtigingen hebben voor de resource. Of de beveiligingsprincipaal nu een beheerde identiteit in Azure is of een Azure AD-gebruikersaccount dat code in de ontwikkelomgeving gebruikt, aan de beveiligingsprincipaal moet een Azure-rol worden toegewezen die toegang verleent tot blob- of wachtrijgegevens in Azure Storage. Zie de sectie Azure-rollen toewijzen voor toegangsrechten **in** Toegang verlenen tot [Azure-blobs](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)en -wachtrijen met behulp van Azure Active Directory voor meer informatie over het toewijzen van machtigingen via Azure RBAC.

> [!NOTE]
> Wanneer u een Azure Storage account maakt, krijgt u niet automatisch machtigingen voor toegang tot gegevens via Azure AD. U moet uzelf expliciet een Azure-rol toewijzen voor Azure Storage. U kunt deze toewijzen op het niveau van uw abonnement, resourcegroep, opslagaccount of container of wachtrij.
>
> Voordat u uzelf een rol voor gegevenstoegang toewijst, hebt u toegang tot gegevens in uw opslagaccount via de Azure Portal, omdat de Azure Portal ook de accountsleutel kan gebruiken voor toegang tot gegevens. Zie Choose how to authorize access to blob data in the Azure Portal (Kiezen hoe u toegang [tot blobgegevens autor](../blobs/authorize-data-operations-portal.md)Azure Portal) voor meer Azure Portal.

### <a name="authenticate-the-user-in-the-development-environment"></a>De gebruiker verifiëren in de ontwikkelomgeving

Wanneer uw code wordt uitgevoerd in de ontwikkelomgeving, wordt verificatie mogelijk automatisch verwerkt of is er mogelijk een browsermelding vereist, afhankelijk van de hulpprogramma's die u gebruikt. Microsoft Visual Studio bijvoorbeeld eenmalige aanmelding (SSO), zodat het actieve Azure AD-gebruikersaccount automatisch wordt gebruikt voor verificatie. Zie Eenmalige aanmelding voor toepassingen voor meer informatie [over SSO.](../../active-directory/manage-apps/what-is-single-sign-on.md)

Andere ontwikkelhulpprogramma's vragen u mogelijk om u aan te melden via een webbrowser.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Een service-principal verifiëren in de ontwikkelomgeving

Als uw ontwikkelomgeving geen ondersteuning biedt voor een aanmelding of aanmelding via een webbrowser, kunt u een service-principal gebruiken om te verifiëren vanuit de ontwikkelomgeving.

#### <a name="create-the-service-principal"></a>De service-principal maken

Als u een service-principal wilt maken met Azure CLI en een Azure-rol wilt toewijzen, roept u de [opdracht az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) aan. Geef een Azure Storage rol voor gegevenstoegang op om toe te wijzen aan de nieuwe service-principal. Geef daarnaast het bereik voor de roltoewijzing op. Zie Ingebouwde Azure-rollen voor meer informatie over de ingebouwde rollen die voor Azure Storage [worden geleverd.](../../role-based-access-control/built-in-roles.md)

Als u niet voldoende machtigingen hebt om een rol toe te wijzen aan de service-principal, moet u mogelijk de accounteigenaar of beheerder vragen om de roltoewijzing uit te voeren.

In het volgende voorbeeld wordt de Azure CLI  gebruikt om een nieuwe service-principal te maken en de rol Gegevenslezer voor opslagblob toe te wijzen met accountbereik

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

De `az ad sp create-for-rbac` opdracht retourneert een lijst met service-principal-eigenschappen in JSON-indeling. Kopieer deze waarden zodat u ze in de volgende stap kunt gebruiken om de benodigde omgevingsvariabelen te maken.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Het kan enkele minuten duren voordat Azure-roltoewijzingen worden doorgegeven.

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

De Azure Identity-clientbibliotheek leest waarden uit drie omgevingsvariabelen tijdens runtime om de service-principal te verifiëren. In de volgende tabel wordt de waarde beschreven die moet worden ingesteld voor elke omgevingsvariabele.

|Omgevingsvariabele|Waarde
|-|-
|`AZURE_CLIENT_ID`|De app-id voor de service-principal
|`AZURE_TENANT_ID`|De Azure AD-tenant-id van de service-principal
|`AZURE_CLIENT_SECRET`|Het wachtwoord dat is gegenereerd voor de service-principal

> [!IMPORTANT]
> Nadat u de omgevingsvariabelen hebt ingesteld, sluit u het consolevenster en opent u het opnieuw. Als u een Visual Studio of een andere ontwikkelomgeving gebruikt, moet u de ontwikkelomgeving mogelijk opnieuw opstarten om de nieuwe omgevingsvariabelen te kunnen registreren.

Zie Identiteit maken voor [Azure-app in de portal voor meer informatie.](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Voorbeeld van .NET-code: Een blok-blob maken

Voeg de volgende `using` instructies toe aan uw code om de Azure Identity- en Azure Storage-clientbibliotheken te gebruiken.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Als u een tokenreferentie wilt opvragen die uw code kan gebruiken om aanvragen voor Azure Storage, maakt u een exemplaar van de [klasse DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) In het volgende codevoorbeeld ziet u hoe u de geverifieerde tokenreferentie op kunt halen en gebruiken om een serviceclientobject te maken en vervolgens de serviceclient kunt gebruiken om een nieuwe blob te uploaden:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
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
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Als u aanvragen wilt autoreren voor blob- of wachtrijgegevens met Azure AD, moet u HTTPS gebruiken voor deze aanvragen.

## <a name="next-steps"></a>Volgende stappen

- [Toegangsrechten voor opslaggegevens beheren met Azure RBAC](./storage-auth-aad-rbac-portal.md).
- [Gebruik Azure AD met opslagtoepassingen](storage-auth-aad-app.md).
- [PowerShell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot blobgegevens](../blobs/authorize-data-operations-powershell.md)
- [Zelfstudie: Toegang tot opslag App Service met behulp van beheerde identiteiten](../../app-service/scenario-secure-app-access-storage.md)
