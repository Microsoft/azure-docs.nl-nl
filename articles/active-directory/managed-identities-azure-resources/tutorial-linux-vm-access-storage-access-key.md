---
title: Zelfstudie`:` Toegang krijgen tot Azure Storage via toegangssleutel met beheerde identiteit - Linux - Azure Active Directory
description: Een zelfstudie die u helpt bij het gebruiken van een door het Linux-VM-systeem toegewezen beheerde identiteit voor toegang tot Azure Storage via een toegangssleutel.
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
ms.date: 03/04/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99a199fcc5f43f3710fe2e2fcfe55b7e624987b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317460"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Zelfstudie: een door het Linux-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Storage via een toegangssleutel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegangssleutels voor opslagaccounts kunt ophalen met een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM). U kunt een toegangssleutel voor opslag gebruiken zoals u gewend bent bij opslagbewerkingen, bijvoorbeeld bij het gebruik van de Storage-SDK. Voor deze zelfstudie uploaden en downloaden we blobs met behulp van Azure CLI. U leert het volgende:

> [!div class="checklist"]
> * Uw virtuele machine toegang verlenen tot toegangssleutels voor opslagaccounts in Resource Manager 
> * Een toegangstoken ophalen met de identiteit van de virtuele machine, en daarmee de toegangssleutels voor opslag ophalen uit Resource Manager  

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
2. Klik op de koppeling **Containers** aan de linkerkant, onder Blob service.
3. Klik op **+ Container** boven aan de pagina om het paneel Nieuwe container deelvenster uit te schuiven.
4. Geef een naam voor de container op, selecteer een toegangsniveau en klik op **OK**. De naam die u hebt opgegeven, wordt verderop in de zelfstudie gebruikt. 

    ![Opslagcontainer maken](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>De door het systeem toegewezen beheerde entiteit van de virtuele machine toegang verlenen tot het gebruik van toegangssleutels voor het opslagaccount

In deze stap verleent u de door het systeem toegewezen beheerde identiteit voor uw VM toegang tot de sleutels voor uw opslagaccount.   

1. Navigeer terug naar het zojuist gemaakte opslagaccount.
2. Klik op de koppeling **Toegangsbeheer (IAM)** in het linkerpaneel.  
3. Klik op **+ Roltoewijzing toevoegen** boven aan de pagina om een nieuwe roltoewijzing voor de VM toe te voegen
4. Stel **Rol** in op 'De servicerol Sleuteloperator voor opslagaccounts', aan de rechterkant van de pagina. 
5. Stel in de volgende vervolgkeuzelijst **Toegang toewijzen aan** de resource in op Virtuele machine.  
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**, en stel **Resourcegroep** in op Alle resourcegroepen.  
7. Kies ten slotte onder **Selecteren** uw virtuele Linux-machine in de vervolgkeuzelijst en klik op **Opslaan**. 

    ![Alt-tekst voor afbeelding](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

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
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Toegangssleutels voor opslagaccounts ophalen uit Azure Resource Manager om opslagaanroepen te maken  

Gebruik nu CURL voor het aanroepen van Resource Manager met behulp van het toegangstoken dat we in de vorige sectie hebben opgehaald, om de toegangssleutel voor opslag op te halen. Als we de toegangssleutel voor opslag eenmaal hebben, kunnen we up- en downloadbewerkingen aanroepen. Vervang de parameterwaarden `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` en `<STORAGE ACCOUNT NAME>` door uw eigen waarden. Vervang de waarde `<ACCESS TOKEN>` door het toegangstoken dat u eerder hebt opgehaald:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> De tekst in de voorgaande URL is hoofdlettergevoelig, dus gebruik dienovereenkomstige hoofdletters en kleine letters voor de resourcegroepen. Daarnaast is het belangrijk om te weten dat dit een POST-aanvraag is en niet een GET-aanvraag. Zorg ervoor dat u een waarde doorgeeft voor het vastleggen van een maximale lengte met -d. Deze waarde kan NULL zijn.  

In de CURL-reactie vindt u de lijst met sleutels:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Maak een voorbeeldblobbestand en upload het naar uw blobopslagcontainer. Op een virtuele Linux-machine kunt u dit doen met de volgende opdracht. 

```bash
echo "This is a test file." > test.txt
```

Voer de verificatie uit met de CLI-opdracht `az storage` met behulp van de toegangssleutel voor opslag, en upload het bestand naar de blobcontainer. Voor deze stap moet u [de nieuwste versie van Azure CLI installeren](/cli/azure/install-azure-cli) op de virtuele machine, als u dat nog niet gedaan hebt.
 

```azurecli
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Reactie: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

U kunt het bestand ook downloaden met behulp van Azure CLI en de verificatie uitvoeren met de opslagsleutel voor opslag. 

Aanvraag: 

```azurecli
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Reactie: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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

In deze zelfstudie hebt u geleerd een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM) te gebruiken om toegang tot Azure Storage te krijgen met behulp van een toegangssleutel.  Zie voor meer informatie over Azure Storage-toegangssleutels:

> [!div class="nextstepaction"]
>[De toegangssleutels van uw opslagaccount beheren](../../storage/common/storage-account-create.md)
