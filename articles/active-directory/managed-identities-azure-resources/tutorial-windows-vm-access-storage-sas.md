---
title: Zelfstudie`:` Beheerde identiteit gebruiken om toegang te krijgen tot Azure Storage met behulp van een SAS-referentie - Azure AD
description: Deze zelfstudie laat zien hoe u een door het systeem toegewezen identiteit voor een virtuele Windows-machine (VM) gebruikt om toegang tot Azure Storage te krijgen met behulp van een SAS-referentie in plaats van een toegangssleutel voor een opslagaccount.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba8c88f040bbd527b0d9f219a81fa090f53c84ed
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590542"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Zelfstudie: Een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Storage via een SAS-referentie

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u een SAS-referentie ophaalt voor Azure Storage met een door het systeem toegewezen identiteit voor een virtuele Windows-machine (VM). Een [service-SAS-referentie](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures), om precies te zijn. 

Een service-SAS biedt de mogelijkheid om beperkte toegang tot objecten in een opslagaccount te verlenen voor een beperkte tijdsduur en een specifieke service (in ons geval de blobservice), zonder een toegangssleutel voor het account beschikbaar te stellen. U kunt een SAS-referentie gebruiken zoals u gewend bent bij opslagbewerkingen, bijvoorbeeld bij het gebruik van de Storage-SDK. Voor deze zelfstudie demonstreren we het uploaden en downloaden van een blob met behulp van de opdrachtregelinterface (CLI) van Azure Storage PowerShell. U leert het volgende:

> [!div class="checklist"]
> * Create a storage account
> * Uw virtuele machine toegang verlenen tot een SAS voor een opslagaccount in Resource Manager 
> * Een toegangstoken ophalen met de identiteit van de virtuele machine, en daarmee de SAS ophalen uit Resource Manager 

## <a name="prerequisites"></a>Vereisten

- Inzicht in beheerde identiteiten. Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](overview.md). 
- Een Azure-account, [meld u aan voor een gratis account](https://azure.microsoft.com/free/).
- 'Eigenaar'-machtigingen voor het juiste bereik (uw abonnement of resourcegroep) om de vereiste stappen voor resource-aanmaak en rolbeheer uit te voeren. Voor hulp bij roltoewijzing gaat u naar [Op rollen gebaseerd toegangsbeheer gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../../role-based-access-control/role-assignments-portal.md).
- U hebt ook een virtuele Windows-machine nodig waarvoor door het systeem toegewezen beheerde identiteiten zijn ingeschakeld.
  - Als u voor deze zelfstudie een virtuele machine moet maken, kunt u dat doen met behulp van het artikel [Een virtuele machine maken waarvoor door het systeem toegewezen identiteiten ingeschakeld zijn](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Create a storage account 

Als u nog geen opslagaccount hebt, maakt u er nu een. U kunt deze stap ook overslaan en de door het systeem toegewezen beheerde identiteit voor uw VM toegang verlenen tot de SAS-referentie van een bestaand opslagaccount. 

1. Klik op de knop **+/Nieuwe service maken** in de linkerbovenhoek van Azure Portal.
2. Klik op **Opslag** en vervolgens op **Opslagaccount**. Het paneel Opslagaccount maken wordt weergegeven.
3. Voer een naam voor het opslagaccount in. U gaat deze gegevens later gebruiken.  
4. **Implementatiemodel** en **Soort account** moeten respectievelijk worden ingesteld op Resource Manager en Algemeen gebruik. 
5. Zorg ervoor dat de waarden van **Abonnement** en **Resourcegroep** overeenkomen met de waarden die u hebt opgegeven bij het maken van de virtuele machine in de vorige stap.
6. Klik op **Create**.

    ![Nieuw opslagaccount maken](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Een blobcontainer in het opslagaccount maken

Later zullen we een bestand uploaden en downloaden naar het nieuwe opslagaccount. Omdat voor bestanden blobopslag nodig is, moeten we een blobcontainer maken waarin het bestand kan worden opgeslagen.

1. Navigeer terug naar het zojuist gemaakte opslagaccount.
2. Klik op de koppeling **Containers** in het linkerpaneel, onder Blob service.
3. Klik op **+ Container** boven aan de pagina om het paneel Nieuwe container deelvenster uit te schuiven.
4. Geef een naam voor de container op, selecteer een toegangsniveau en klik op **OK**. De naam die u hebt opgegeven, wordt verderop in de zelfstudie gebruikt. 

    ![Opslagcontainer maken](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>De door het systeem toegewezen beheerde identiteit voor uw VM toegang verlenen tot het gebruik van een SAS-opslag 

Azure Storage biedt geen systeemeigen ondersteuning voor Azure AD-verificatie.  U kunt echter een beheerde identiteit gebruiken om een opslag-SAS op te halen uit Resource Manager, en vervolgens de SAS gebruiken om toegang tot de opslag te krijgen.  In deze stap verleent u de door het systeem toegewezen beheerde identiteit voor uw VM toegang tot de SAS voor uw opslagaccount.   

1. Navigeer terug naar het zojuist gemaakte opslagaccount.   
2. Klik op de koppeling **Toegangsbeheer (IAM)** in het linkerpaneel.  
3. Klik op **+ Roltoewijzing toevoegen** boven aan de pagina om een nieuwe roltoewijzing voor de VM toe te voegen
4. Stel **Rol** in op 'Inzender voor opslagaccounts', aan de rechterkant van de pagina.  
5. Stel in de volgende vervolgkeuzelijst **Toegang toewijzen aan** de resource in op Virtuele machine.  
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**, en stel **Resourcegroep** in op Alle resourcegroepen.  
7. Kies ten slotte onder **Selecteren** uw virtuele Windows-machine in de vervolgkeuzelijst en klik op **Opslaan**. 

    ![Alt-tekst voor afbeelding](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Azure Resource Manager aanroepen 

Voor de rest van de zelfstudie werken we met uw VM.

In dit gedeelte moet u de PowerShell-cmdlets voor Azure Resource Manager gebruiken.  Als u deze nog niet hebt geïnstalleerd, moet u [de nieuwste versie downloaden](/powershell/azure/) voordat u doorgaat.

1. In Azure Portal navigeert u naar **Virtuele machines**, gaat u naar uw virtuele Windows-machine, klikt u vervolgens boven aan de pagina **Overzicht** op **Verbinden**.
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine. 
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt:
4. Open PowerShell in de externe sessie en gebruik Invoke-WebRequest om een Azure Resource Manager-token op te halen bij de lokaal beheerde identiteit voor het Azure-resources-eindpunt.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > De waarde van de parameter 'resource' moet exact overeenkomen met wat er in Azure AD wordt verwacht. Wanneer u de resource-id van Azure Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde.
    
    Extraheer vervolgens het element 'Content' (inhoud), dat is opgeslagen als een tekenreeks in JSON-indeling (JavaScript Object Notation) in het object $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extraheer vervolgens het toegangstoken uit het antwoord.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Een SAS-referentie ophalen uit Azure Resource Manager om opslagaanroepen te maken 

Gebruik nu PowerShell voor het aanroepen van Resource Manager met behulp van het toegangstoken dat we hebben opgehaald in de vorige sectie, om een SAS-referentie voor opslag op te halen. Als we de SAS-referentie eenmaal hebben, kunnen we opslagbewerkingen aanroepen.

Voor deze aanvraag gebruiken we de volgende HTTP-aanvraagparameters voor het maken van de SAS-referentie:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Deze parameters worden opgenomen in de tekst van de POST-aanvraag voor de SAS-referentie. Zie de [naslaginformatie over REST voor het weergeven van service-SAS](/rest/api/storagerp/storageaccounts/listservicesas) voor meer informatie over de parameters voor het maken van een SAS-referentie.

Converteer de parameters eerst naar JSON en roep vervolgens het `listServiceSas`-eindpunt voor opslag aan om de SAS-referentie te maken:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> De URL is hoofdlettergevoelig, dus gebruik precies dezelfde naam van de resourcegroep als hiervoor, met inbegrip van de hoofdletter 'G' in 'resourceGroups'. 

We kunnen nu de SAS-referentie uit het antwoord ophalen:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Als u de SAS-referentie inspecteert, ziet deze er ongeveer als volgt uit:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Vervolgens maken we een bestand met de naam test.txt. Gebruik de SAS-referentie om te verifiëren bij de cmdlet `New-AzStorageContent`, upload het bestand naar de blobcontainer en download het bestand vervolgens.

```bash
echo "This is a test text file." > test.txt
```

Zorg ervoor dat u eerst de Azure Storage-cmdlets installeert met `Install-Module Azure.Storage`. Upload de blob die u zojuist hebt gemaakt, met behulp van de `Set-AzStorageBlobContent` PowerShell-cmdlet:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Reactie:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

U kunt de blob die u zojuist hebt geüpload, ook downloaden met de `Get-AzStorageBlobContent` PowerShell-cmdlet:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Reactie:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM) te gebruiken om toegang tot Azure Storage te krijgen met behulp van een SAS-referentie.  Zie voor meer informatie over Azure Storage SAS:

> [!div class="nextstepaction"]
>[Shared Access Signatures (SAS) gebruiken](../../storage/common/storage-sas-overview.md)