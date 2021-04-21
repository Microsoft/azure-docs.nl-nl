---
title: Anonieme openbare leestoegang configureren voor containers en blobs
titleSuffix: Azure Storage
description: Meer informatie over het toestaan of niet toestaan van anonieme toegang tot blobgegevens voor het opslagaccount. Stel de instelling openbare toegang van de container in om containers en blobs beschikbaar te maken voor anonieme toegang.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 31812a7b2dddad474ab5cd422a15f6e5368dba5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774625"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Anonieme openbare leestoegang configureren voor containers en blobs

Azure Storage ondersteunt optionele anonieme openbare leestoegang voor containers en blobs. Anonieme toegang tot uw gegevens is standaard nooit toegestaan. Tenzij u anonieme toegang expliciet inschakelen, moeten alle aanvragen naar een container en de blobs worden geautoriseerd. Wanneer u de instelling op openbaar toegangsniveau van een container configureert om anonieme toegang toe te staan, kunnen clients gegevens in die container lezen zonder de aanvraag te autoriseren.

> [!WARNING]
> Wanneer een container is geconfigureerd voor openbare toegang, kan elke client gegevens in die container lezen. Openbare toegang brengt een mogelijk beveiligingsrisico met zich mee, dus als dit niet vereist is voor uw scenario, raadt Microsoft u aan dit niet toe te staan voor het opslagaccount. Zie Anonieme openbare leestoegang tot containers en blobs voorkomen voor [meer informatie.](anonymous-read-access-prevent.md)

In dit artikel wordt beschreven hoe u anonieme openbare leestoegang configureert voor een container en de blobs. Zie Anonieme toegang tot openbare containers en blobs met .NET voor meer informatie over het anoniem openen van blobgegevens vanuit een [clienttoepassing.](anonymous-read-access-client.md)

## <a name="about-anonymous-public-read-access"></a>Over anonieme openbare leestoegang

Openbare toegang tot uw gegevens is standaard altijd verboden. Er zijn twee afzonderlijke instellingen die van invloed zijn op openbare toegang:

1. **Sta openbare toegang voor het opslagaccount toe.** Standaard staat een opslagaccount een gebruiker met de juiste machtigingen toe om openbare toegang tot een container in te stellen. Blobgegevens zijn niet beschikbaar voor openbare toegang, tenzij de gebruiker de extra stap neemt om de instelling voor openbare toegang van de container expliciet te configureren.
1. **Configureer de instelling voor openbare toegang van de container.** De instelling voor openbare toegang van een container is standaard uitgeschakeld, wat betekent dat autorisatie vereist is voor elke aanvraag naar de container of de gegevens ervan. Een gebruiker met de juiste machtigingen kan de instelling voor openbare toegang van een container wijzigen zodat anonieme toegang alleen mogelijk is als anonieme toegang voor het opslagaccount is toegestaan.

In de volgende tabel wordt samengevat hoe beide instellingen samen van invloed zijn op openbare toegang voor een container.

| Instelling voor openbare toegang | Openbare toegang is uitgeschakeld voor een container (standaardinstelling) | Openbare toegang voor een container is ingesteld op Container | Openbare toegang een container is ingesteld op Blob |
|--|--|--|--|
| Openbare toegang is niet toegestaan voor het opslagaccount | Geen openbare toegang tot een container in het opslagaccount. | Geen openbare toegang tot een container in het opslagaccount. De instelling voor het opslagaccount overschrijven de containerinstelling. | Geen openbare toegang tot een container in het opslagaccount. De instelling voor het opslagaccount overschrijven de containerinstelling. |
| Openbare toegang is toegestaan voor het opslagaccount (standaardinstelling) | Geen openbare toegang tot deze container (standaardconfiguratie). | Openbare toegang is toegestaan voor deze container en de blobs. | Openbare toegang is toegestaan voor blobs in deze container, maar niet voor de container zelf. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Openbare leestoegang voor een opslagaccount toestaan of niet toestaan

Standaard is een opslagaccount zo geconfigureerd dat een gebruiker met de juiste machtigingen openbare toegang tot een container kan inschakelen. Wanneer openbare toegang is toegestaan, kan een gebruiker met de juiste machtigingen de instelling voor openbare toegang van een container wijzigen om anonieme openbare toegang tot de gegevens in die container mogelijk te maken. Blobgegevens zijn nooit beschikbaar voor openbare toegang, tenzij de gebruiker de extra stap neemt om de instelling voor openbare toegang van de container expliciet te configureren.

Houd er rekening mee dat openbare toegang tot een container altijd standaard is uitgeschakeld en expliciet moet worden geconfigureerd om anonieme aanvragen toe te staan. Ongeacht de instelling voor het opslagaccount, zijn uw gegevens nooit beschikbaar voor openbare toegang, tenzij een gebruiker met de juiste machtigingen deze extra stap neemt om openbare toegang tot de container in te stellen.

Door openbare toegang voor het opslagaccount niet toe te staan, wordt anonieme toegang tot alle containers en blobs in dat account voorkomen. Wanneer openbare toegang voor het account niet is toegestaan, is het niet mogelijk om de instelling voor openbare toegang voor een container zo te configureren dat anonieme toegang wordt toegestaan. Voor een betere beveiliging raadt Microsoft u aan om openbare toegang voor uw opslagaccounts niet toe te staan, tenzij uw scenario vereist dat gebruikers anoniem toegang krijgen tot blob-resources.

> [!IMPORTANT]
> Door openbare toegang voor een opslagaccount niet toe te staan, worden de instellingen voor openbare toegang voor alle containers in dat opslagaccount overgeslagen. Als openbare toegang voor het opslagaccount niet is toegestaan, mislukken toekomstige anonieme aanvragen voor dat account. Voordat u deze instelling verandert, moet u weten wat de gevolgen zijn voor clienttoepassingen die mogelijk anoniem toegang hebben tot gegevens in uw opslagaccount. Zie Anonieme openbare leestoegang tot containers en blobs voorkomen voor [meer informatie.](anonymous-read-access-prevent.md)

Als u openbare toegang voor een opslagaccount wilt toestaan of niet toestaan, configureert u de eigenschap **AllowBlobPublicAccess** van het account. Deze eigenschap is beschikbaar voor alle opslagaccounts die zijn gemaakt met het Azure Resource Manager implementatiemodel. Zie Overzicht van opslagaccount [voor meer informatie.](../common/storage-account-overview.md)

De **eigenschap AllowBlobPublicAccess** is niet standaard ingesteld voor een opslagaccount en retourneert geen waarde totdat u deze expliciet hebt ingesteld. Het opslagaccount staat openbare toegang toe wanneer de eigenschapswaarde **null** of **true is.**

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u openbare toegang voor een opslagaccount in de Azure Portal wilt toestaan of niet toestaan, volgt u deze stappen:

1. Ga in Azure Portal naar uw opslagaccount.
1. Zoek de **configuratie-instelling** onder **Instellingen**.
1. Stel **Openbare blobtoegang in** op **Ingeschakeld** of **Uitgeschakeld.**

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Schermopname die laat zien hoe u openbare toegang tot blobs voor een account toestaat of niet toestaat":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u openbare toegang voor een opslagaccount met PowerShell wilt toestaan of niet toestaan, installeert [Azure PowerShell versie 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) of hoger. Configureer vervolgens de **eigenschap AllowBlobPublicAccess** voor een nieuw of bestaand opslagaccount.

In het volgende voorbeeld wordt een opslagaccount gemaakt en wordt de eigenschap **AllowBlobPublicAccess** expliciet op **true (waar) gebruikt.** Vervolgens wordt het opslagaccount bijgewerkt om de **eigenschap AllowBlobPublicAccess** in te stellen op **false**. In het voorbeeld wordt ook de eigenschapswaarde in elk geval opgehaald. Vergeet niet om de tijdelijke aanduidingswaarden tussen vierkante haken te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u openbare toegang voor een opslagaccount met Azure CLI wilt toestaan of niet toestaan, installeert u Azure CLI versie 2.9.0 of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie. Configureer vervolgens de **eigenschap allowBlobPublicAccess** voor een nieuw of bestaand opslagaccount.

In het volgende voorbeeld wordt een opslagaccount gemaakt en wordt de **eigenschap allowBlobPublicAccess** expliciet op **true (waar) gebruikt.** Vervolgens wordt het opslagaccount bijgewerkt om de **eigenschap allowBlobPublicAccess** in te stellen op **false**. In het voorbeeld wordt ook de eigenschapswaarde in elk geval opgehaald. Vergeet niet om de tijdelijke aanduidingswaarden tussen vierkante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Sjabloon](#tab/template)

Als u openbare toegang voor een opslagaccount met een sjabloon wilt toestaan of niet toestaan, maakt u een sjabloon met de eigenschap **AllowBlobPublicAccess** ingesteld op **true** of **false.** In de volgende stappen wordt beschreven hoe u een sjabloon maakt in Azure Portal.

1. Kies in Azure Portal de **optie Een resource maken.**
1. In **Marketplace doorzoeken typt** u **sjabloonimplementatie** en drukt u op **ENTER.**
1. Kies Sjabloonimlementatie (implementeren met aangepaste **sjablonen) (preview)**, kies **Maken** en kies vervolgens Uw eigen sjabloon **bouwen in de editor**.
1. Plak in de sjablooneditor de volgende JSON om een nieuw account te maken en stel de eigenschap **AllowBlobPublicAccess** in **op true** of **false.** Vergeet niet om de tijdelijke aanduidingen tussen vierkante haken te vervangen door uw eigen waarden.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Sla de sjabloon op.
1. Geef de resourcegroepparameter op en kies vervolgens de knop Beoordelen **en** maken om de sjabloon te implementeren en een opslagaccount te maken met de eigenschap **allowBlobPublicAccess** geconfigureerd.

---

> [!NOTE]
> Het niet verlenen van openbare toegang voor een opslagaccount heeft geen invloed op statische websites die in dat opslagaccount worden gehost. De **$web** container is altijd openbaar toegankelijk.
>
> Nadat u de instelling voor openbare toegang voor het opslagaccount hebt bijgewerkt, kan het tot 30 seconden duren voordat de wijziging volledig is doorgegeven.

Voor het toestaan of niet toestaan van openbare toegang tot blobs is versie 2019-04-01 of hoger van de Azure Storage resourceprovider vereist. Zie voor meer informatie [Azure Storage Resource Provider REST API](/rest/api/storagerp/).

In de voorbeelden in deze sectie hebt u gezien hoe u de eigenschap **AllowBlobPublicAccess** voor het opslagaccount kunt lezen om te bepalen of openbare toegang momenteel is toegestaan of niet is toegestaan. Zie Anonieme openbare toegang herstellen voor meer informatie over het controleren of de instelling voor openbare toegang van een account is geconfigureerd om anonieme toegang [te voorkomen.](anonymous-read-access-prevent.md#remediate-anonymous-public-access)

## <a name="set-the-public-access-level-for-a-container"></a>Het openbare toegangsniveau voor een container instellen

Als u anonieme gebruikers leestoegang wilt verlenen tot een container en de blobs, moet u eerst openbare toegang voor het opslagaccount toestaan en vervolgens het openbare toegangsniveau van de container instellen. Als openbare toegang voor het opslagaccount wordt geweigerd, kunt u geen openbare toegang voor een container configureren.

Wanneer openbare toegang is toegestaan voor een opslagaccount, kunt u een container configureren met de volgende machtigingen:

- **Geen openbare leestoegang:** De container en de blobs zijn alleen toegankelijk met een geautoriseerde aanvraag. Deze optie is de standaardinstelling voor alle nieuwe containers.
- **Alleen openbare leestoegang voor blobs:** Blobs in de container kunnen worden gelezen door anonieme aanvragen, maar containergegevens zijn niet anoniem beschikbaar. Anonieme clients kunnen de blobs in de container niet opsnoemen.
- **Openbare leestoegang voor container en de blobs:** Container- en blobgegevens kunnen worden gelezen door anonieme aanvragen, met uitzondering van machtigingsinstellingen voor containers en metagegevens van de container. Clients kunnen blobs in de container op basis van anonieme aanvragen opsnoemen, maar kunnen geen containers in het opslagaccount opsnoemen.

U kunt het openbare toegangsniveau voor een afzonderlijke blob niet wijzigen. Openbaar toegangsniveau wordt alleen ingesteld op containerniveau. U kunt het openbare toegangsniveau van de container instellen wanneer u de container maakt, of u kunt de instelling voor een bestaande container bijwerken.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u het openbare toegangsniveau voor een of meer bestaande containers in de Azure Portal wilt bijwerken, volgt u deze stappen:

1. Navigeer naar het overzicht van uw opslagaccount in Azure Portal.
1. Selecteer **onder Blob service** op de menublade de optie **Containers**.
1. Selecteer de containers waarvoor u het openbare toegangsniveau wilt instellen.
1. Gebruik de **knop Toegangsniveau wijzigen** om de instellingen voor openbare toegang weer te geven.
1. Selecteer het gewenste openbare toegangsniveau in **de** vervolgkeuzepagina Openbaar toegangsniveau en klik op de knop OK om de wijziging toe te passen op de geselecteerde containers.

    ![Schermopname die laat zien hoe u een openbaar toegangsniveau in de portal in kunt stellen](./media/anonymous-read-access-configure/configure-public-access-container.png)

Wanneer openbare toegang niet is toegestaan voor het opslagaccount, kan het openbare toegangsniveau van een container niet worden ingesteld. Als u probeert het openbare toegangsniveau van de container in te stellen, ziet u dat de instelling is uitgeschakeld omdat openbare toegang niet is toegestaan voor het account.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Schermopname die laat zien dat het instellen van het openbare toegangsniveau van de container wordt geblokkeerd wanneer openbare toegang niet is toegestaan":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u het openbare toegangsniveau voor een of meer containers wilt bijwerken met PowerShell, roept u de [opdracht Set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) aan. Autoreer deze bewerking door uw accountsleutel, een connection string of een Shared Access Signature (SAS) door te geven. De [bewerking Container-ACL instellen](/rest/api/storageservices/set-container-acl) die het openbare toegangsniveau van de container in stelt, biedt geen ondersteuning voor autorisatie met Azure AD. Zie Machtigingen voor het aanroepen van blob- en [wachtrijgegevensbewerkingen voor meer informatie.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

In het volgende voorbeeld wordt een container gemaakt waarin openbare toegang is uitgeschakeld en wordt vervolgens de instelling voor openbare toegang van de container bijgewerkt om anonieme toegang tot de container en de blobs toe te staan. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

Wanneer openbare toegang niet is toegestaan voor het opslagaccount, kan het openbare toegangsniveau van een container niet worden ingesteld. Als u probeert het openbare toegangsniveau van de container in te stellen, wordt Azure Storage foutbericht weergegeven dat openbare toegang voor het opslagaccount niet is toegestaan.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u het openbare toegangsniveau voor een of meer containers wilt bijwerken met Azure CLI, roept u de [opdracht az storage container set permission](/cli/azure/storage/container#az_storage_container_set_permission) aan. Autoreer deze bewerking door uw accountsleutel, een connection string of een Shared Access Signature (SAS) door te geven. De [bewerking Container-ACL instellen](/rest/api/storageservices/set-container-acl) die het openbare toegangsniveau van de container in stelt, biedt geen ondersteuning voor autorisatie met Azure AD. Zie Machtigingen voor het aanroepen van blob- en [wachtrijgegevensbewerkingen voor meer informatie.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

In het volgende voorbeeld wordt een container gemaakt waarin openbare toegang is uitgeschakeld en wordt vervolgens de instelling voor openbare toegang van de container bijgewerkt om anonieme toegang tot de container en de blobs toe te staan. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

Wanneer openbare toegang niet is toegestaan voor het opslagaccount, kan het openbare toegangsniveau van een container niet worden ingesteld. Als u probeert het openbare toegangsniveau van de container in te stellen, wordt Azure Storage foutbericht weergegeven dat openbare toegang voor het opslagaccount niet is toegestaan.

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>De instelling voor openbare toegang voor een set containers controleren

Het is mogelijk om te controleren welke containers in een of meer opslagaccounts zijn geconfigureerd voor openbare toegang door de containers te bekijken en de instelling voor openbare toegang te controleren. Deze aanpak is een praktische optie wanneer een opslagaccount niet een groot aantal containers bevat of wanneer u de instelling voor een klein aantal opslagaccounts controleert. De prestaties kunnen echter minder zijn als u probeert een groot aantal containers op te snoemen.

In het volgende voorbeeld wordt PowerShell gebruikt om de instelling voor openbare toegang op te halen voor alle containers in een opslagaccount. Vergeet niet om de tijdelijke aanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Volgende stappen

- [Anonieme openbare leestoegang tot containers en blobs voorkomen](anonymous-read-access-prevent.md)
- [Anoniem toegang krijgen tot openbare containers en blobs met .NET](anonymous-read-access-client.md)
- [Toegang tot Azure Storage](../common/storage-auth.md)
