---
title: 'Zelfstudie: Toegang krijgen tot Azure Storage met een SAS-referentie – Linux – Azure Active Directory'
description: Deze zelfstudie laat zien hoe u een door het systeem toegewezen identiteit voor een virtuele Linux-machine (VM) gebruikt om toegang tot Azure Storage te krijgen met behulp van een SAS-referentie in plaats van een toegangssleutel voor een opslagaccount.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3edc63a1532bb6889fc490e400dbb57e7bce10d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360408"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-identity-to-access-azure-storage-via-a-sas-credential"></a>Zelfstudie: Toegang krijgen tot Azure Storage via SAS-referenties met een door het systeem toegewezen identiteit voor een virtuele Linux-machine (VM)

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u referenties voor een gedeelde handtekening (SAS) ophaalt voor Azure Storage met een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM). Een [service-SAS-referentie](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures), om precies te zijn. 

> [!NOTE]
> De SAS-sleutel die in deze zelfstudie wordt gegenereerd, is niet beperkt tot/gebonden aan de virtuele machine.  

Een service-SAS biedt de mogelijkheid om beperkte toegang tot objecten in een opslagaccount te verlenen voor een beperkte tijdsduur en een specifieke service (in ons geval de blobservice), zonder een toegangssleutel voor het account beschikbaar te stellen. U kunt een SAS-referentie gebruiken zoals u gewend bent bij opslagbewerkingen, bijvoorbeeld bij het gebruik van de Storage-SDK. Voor deze zelfstudie demonstreren we het uploaden en downloaden van een blob met behulp van de opdrachtregelinterface (CLI) van Azure Storage. U leert het volgende:


> [!div class="checklist"]
> * Create a storage account
> * Een blobcontainer in het opslagaccount maken
> * Uw virtuele machine toegang verlenen tot een SAS voor een opslagaccount in Resource Manager 
> * Een toegangstoken ophalen met de identiteit van de virtuele machine, en daarmee de SAS ophalen uit Resource Manager 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Create a storage account 

Als u nog geen opslagaccount hebt, maakt u er nu een.  U kunt deze stap ook overslaan en de door het systeem toegewezen beheerde identiteit voor uw VM toegang verlenen tot de sleutels van een bestaand opslagaccount. 

1. Klik op de knop **+/Nieuwe service maken** in de linkerbovenhoek van Azure Portal.
2. Klik op **Opslag** en vervolgens op **Opslagaccount**. Het paneel Opslagaccount maken wordt weergegeven.
3. Voer in **Naam** een naam voor het opslagaccount in. U gaat deze gegevens later gebruiken.  
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

Azure Storage biedt geen systeemeigen ondersteuning voor Azure AD-verificatie.  U kunt echter een door het systeem toegewezen beheerde identiteit voor uw VM gebruiken om een opslag-SAS op te halen uit Resource Manager, en vervolgens de SAS gebruiken om toegang tot de opslag te krijgen.  In deze stap verleent u de door het systeem toegewezen beheerde identiteit voor uw VM toegang tot de SAS voor uw opslagaccount.   

1. Navigeer terug naar het zojuist gemaakte opslagaccount.
2. Klik op de koppeling **Toegangsbeheer (IAM)** in het linkerpaneel.  
3. Klik op **+ Roltoewijzing toevoegen** boven aan de pagina om een nieuwe roltoewijzing voor de VM toe te voegen
4. Stel **Rol** in op 'Inzender voor opslagaccounts', aan de rechterkant van de pagina. 
5. Stel in de volgende vervolgkeuzelijst **Toegang toewijzen aan** de resource in op Virtuele machine.  
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**, en stel **Resourcegroep** in op Alle resourcegroepen.  
7. Kies ten slotte onder **Selecteren** uw virtuele Linux-machine in de vervolgkeuzelijst en klik op **Opslaan**.  

    ![Alt-tekst voor afbeelding](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Azure Resource Manager aanroepen

Voor de rest van de zelfstudie werken we op de virtuele machine die we eerder hebben gemaakt.

U hebt een SSH-client nodig om deze stappen uit te voeren. Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](/windows/wsl/install-win10) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.

1. In Azure Portal navigeert u naar **Virtuele machines**, gaat u naar uw virtuele Linux-machine, klikt u vervolgens boven aan de pagina **Overzicht** op **Verbinden**. Kopieer de verbindingsreeks voor uw virtuele machine. 
2. Maak verbinding met uw virtuele machine via de SSH-client.  
3. Vervolgens wordt u gevraagd om het **wachtwoord** in te voeren dat u hebt toegevoegd bij het maken van de **Linux-VM**. Als het goed is, bent u daarna aangemeld.  
4. Gebruik CURL om een toegangstoken voor Azure Resource Manager op te halen.  

    Hieronder ziet u de CURL-aanvraag en de reactie voor het toegangstoken:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > In de bovenstaande aanvraag moet de waarde van de parameter 'resource' exact overeenkomen met wat er in Azure AD wordt verwacht. Wanneer u de resource-id van Azure Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde.
    > In het volgende antwoord is het element access_token ingekort.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Een SAS-referentie ophalen uit Azure Resource Manager om opslagaanroepen te maken

Gebruik nu CURL voor het aanroepen van Resource Manager met behulp van het toegangstoken dat we hebben opgehaald in de vorige sectie, om een SAS-referentie voor opslag op te halen. Als we de SAS-referentie eenmaal hebben, kunnen we up- en downloadbewerkingen aanroepen.

Voor deze aanvraag gebruiken we de volgende HTTP-aanvraagparameters voor het maken van de SAS-referentie:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Deze parameters worden opgenomen in de tekst van de POST-aanvraag voor de SAS-referentie. Zie de [naslaginformatie over REST voor het weergeven van service-SAS](/rest/api/storagerp/storageaccounts/listservicesas) voor meer informatie over de parameters voor het maken van een SAS-referentie.

Gebruik de volgende CURL-aanvraag om de SAS-referentie op te halen. Vervang de parameterwaarden `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` en `<EXPIRATION TIME>` door uw eigen waarden. Vervang de waarde `<ACCESS TOKEN>` door het toegangstoken dat u eerder hebt opgehaald:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> De tekst in de voorgaande URL is hoofdlettergevoelig, dus gebruik dienovereenkomstige hoofdletters en kleine letters voor de resourcegroepen. Het is ook belangrijk te weten dat dit een POST-aanvraag is en geen GET-aanvraag.

De SAS-referentie wordt geretourneerd in de CURL-reactie:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Maak een voorbeeldblobbestand en upload het naar uw blobopslagcontainer. Op een virtuele Linux-machine kunt u dit doen met de volgende opdracht. 

```bash
echo "This is a test file." > test.txt
```

Voer de verificatie uit met de CLI-opdracht `az storage` met behulp van de SAS-referentie, en upload het bestand naar de blobcontainer. Voor deze stap moet u [de nieuwste versie van Azure CLI installeren](/cli/azure/install-azure-cli) op de virtuele machine, als u dat nog niet gedaan hebt.

```azurecli
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Reactie: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

U kunt het bestand ook downloaden met behulp van Azure CLI en de verificatie uitvoeren met de SAS-referentie. 

Aanvraag: 

```azurecli
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Reactie: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM) te gebruiken om toegang tot Azure Storage te krijgen met behulp van een SAS-referentie.  Zie voor meer informatie over Azure Storage SAS:

> [!div class="nextstepaction"]
>[Shared Access Signatures (SAS) gebruiken](../../storage/common/storage-sas-overview.md)
