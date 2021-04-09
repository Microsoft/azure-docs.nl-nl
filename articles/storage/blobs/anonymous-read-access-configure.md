---
title: Anonieme open bare Lees toegang voor containers en blobs configureren
titleSuffix: Azure Storage
description: Meer informatie over het toestaan of weigeren van anonieme toegang tot BLOB-gegevens voor het opslag account. Stel de instelling voor open bare toegang van de container in om containers en blobs beschikbaar te maken voor anonieme toegang.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: feac7b890c973b1541c5362f860432687082953f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96533873"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Anonieme open bare Lees toegang voor containers en blobs configureren

Azure Storage ondersteunt optionele anonieme lees toegang voor containers en blobs. Anonieme toegang tot uw gegevens is standaard nooit toegestaan. Tenzij u anonieme toegang expliciet inschakelt, moeten alle aanvragen naar een container en de bijbehorende blobs worden geautoriseerd. Wanneer u de instelling van het open bare toegangs niveau van een container configureert om anonieme toegang toe te staan, kunnen clients gegevens in die container lezen zonder de aanvraag te autoriseren.

> [!WARNING]
> Wanneer een container voor open bare toegang is geconfigureerd, kan elke client gegevens in die container lezen. Open bare toegang geeft een potentieel beveiligings risico, dus als u dit niet nodig hebt, raadt micro soft u aan het niet toe te staan voor het opslag account. Zie [anonieme open bare Lees toegang tot containers en blobs voor komen](anonymous-read-access-prevent.md)voor meer informatie.

In dit artikel wordt beschreven hoe u anonieme open bare Lees toegang configureert voor een container en de bijbehorende blobs. Zie [open bare containers en blobs anoniem openen met .net](anonymous-read-access-client.md)voor meer informatie over het anoniem benaderen van BLOB-gegevens vanuit een client toepassing.

## <a name="about-anonymous-public-read-access"></a>Over anonieme open bare Lees toegang

Open bare toegang tot uw gegevens is altijd standaard toegestaan. Er zijn twee afzonderlijke instellingen die van invloed zijn op open bare toegang:

1. **Open bare toegang voor het opslag account toestaan.** Standaard staat een opslag account toe dat een gebruiker de juiste machtigingen heeft om open bare toegang tot een container mogelijk te maken. BLOB-gegevens zijn niet beschikbaar voor open bare toegang, tenzij de gebruiker de extra stap volgt om de open bare toegangs instelling van de container expliciet te configureren.
1. **Configureer de instelling voor open bare toegang van de container.** De open bare toegangs instelling van een container is standaard uitgeschakeld, wat betekent dat autorisatie is vereist voor elke aanvraag aan de container of de gegevens ervan. Een gebruiker met de juiste machtigingen kan de open bare toegangs instelling van een container wijzigen om anonieme toegang alleen in te scha kelen als anonieme toegang is toegestaan voor het opslag account.

De volgende tabel bevat een overzicht van de manier waarop beide instellingen van invloed zijn op open bare toegang voor een container.

| Instelling voor open bare toegang | Open bare toegang is uitgeschakeld voor een container (standaard instelling) | Open bare toegang voor een container is ingesteld op container | Open bare toegang een container is ingesteld op BLOB |
|--|--|--|--|
| Open bare toegang is niet toegestaan voor het opslag account | Geen open bare toegang tot een container in het opslag account. | Geen open bare toegang tot een container in het opslag account. De instelling van het opslag account heeft voor rang op de instelling van de container. | Geen open bare toegang tot een container in het opslag account. De instelling van het opslag account heeft voor rang op de instelling van de container. |
| Open bare toegang is toegestaan voor het opslag account (standaard instelling) | Geen open bare toegang tot deze container (standaard configuratie). | Open bare toegang is toegestaan voor deze container en de bijbehorende blobs. | Open bare toegang is toegestaan voor blobs in deze container, maar niet naar de container zelf. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Open bare Lees toegang voor een opslag account toestaan of weigeren

Een opslag account is standaard zo geconfigureerd dat een gebruiker met de juiste machtigingen open bare toegang tot een container mogelijk maakt. Wanneer open bare toegang is toegestaan, kan een gebruiker met de juiste machtigingen de open bare toegangs instelling van een container wijzigen om anonieme toegang tot de gegevens in die container in te scha kelen. BLOB-gegevens zijn nooit beschikbaar voor open bare toegang, tenzij de gebruiker de extra stap nodig heeft om de open bare toegangs instelling van de container expliciet te configureren.

Houd er rekening mee dat open bare toegang tot een container standaard altijd is uitgeschakeld en moet expliciet worden geconfigureerd om anonieme aanvragen toe te staan. Ongeacht de instelling van het opslag account zijn uw gegevens nooit beschikbaar voor open bare toegang, tenzij een gebruiker met de juiste machtigingen deze extra stap gebruikt om open bare toegang tot de container mogelijk te maken.

Het niet toestaan van open bare toegang voor het opslag account voor komt anonieme toegang tot alle containers en blobs in dat account. Wanneer open bare toegang niet is toegestaan voor het account, is het niet mogelijk om de instelling voor open bare toegang voor een container zo te configureren dat anonieme toegang is toegestaan. Voor een betere beveiliging raadt micro soft u aan om open bare toegang voor uw opslag accounts uit te stellen, tenzij uw scenario vereist dat gebruikers anoniem toegang hebben tot BLOB-resources.

> [!IMPORTANT]
> Het niet toestaan van open bare toegang voor een opslag account heeft voor rang op de instellingen voor open bare toegang voor alle containers in dat opslag account. Wanneer open bare toegang niet is toegestaan voor het opslag account, zullen toekomstige anonieme aanvragen voor dat account mislukken. Voordat u deze instelling wijzigt, moet u weten wat het effect is op client toepassingen die anoniem toegang hebben tot gegevens in uw opslag account. Zie [anonieme open bare Lees toegang tot containers en blobs voor komen](anonymous-read-access-prevent.md)voor meer informatie.

Als u open bare toegang voor een opslag account wilt toestaan of weigeren, configureert u de eigenschap **AllowBlobPublicAccess** van het account. Deze eigenschap is beschikbaar voor alle opslag accounts die zijn gemaakt met het Azure Resource Manager-implementatie model. Zie [overzicht van opslag accounts](../common/storage-account-overview.md)voor meer informatie.

De eigenschap **AllowBlobPublicAccess** is niet standaard ingesteld voor een opslag account en retourneert geen waarde totdat u deze expliciet hebt ingesteld. Het opslag account staat open bare toegang toe wanneer de waarde van de eigenschap **Null** of **True** is.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Ga als volgt te werk om open bare toegang voor een opslag account in de Azure Portal toe te staan of niet toe te staan:

1. Ga in Azure Portal naar uw opslagaccount.
1. Zoek de **configuratie** -instelling onder **instellingen**.
1. Stel **open bare BLOB-toegang** in op **ingeschakeld** of **uitgeschakeld**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Scherm opname van het toestaan of weigeren van open bare BLOB-toegang voor het account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u open bare toegang tot een opslag account met Power shell wilt toestaan of weigeren, installeert u [Azure PowerShell versie 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) of hoger. Configureer vervolgens de eigenschap **AllowBlobPublicAccess** voor een nieuw of bestaand opslag account.

In het volgende voor beeld wordt een opslag account gemaakt en wordt de eigenschap **AllowBlobPublicAccess** expliciet ingesteld op **True**. Vervolgens wordt het opslag account bijgewerkt om de eigenschap **AllowBlobPublicAccess** in te stellen op **False**. In het voor beeld wordt ook de waarde van de eigenschap in elk geval opgehaald. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

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

Als u open bare toegang tot een opslag account met Azure CLI wilt toestaan of weigeren, installeert u Azure CLI versie 2.9.0 of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie. Configureer vervolgens de eigenschap **allowBlobPublicAccess** voor een nieuw of bestaand opslag account.

In het volgende voor beeld wordt een opslag account gemaakt en wordt de eigenschap **allowBlobPublicAccess** expliciet ingesteld op **True**. Vervolgens wordt het opslag account bijgewerkt om de eigenschap **allowBlobPublicAccess** in te stellen op **False**. In het voor beeld wordt ook de waarde van de eigenschap in elk geval opgehaald. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

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

Als u open bare toegang voor een opslag account met een sjabloon wilt toestaan of weigeren, maakt u een sjabloon waarvan de eigenschap **AllowBlobPublicAccess** is ingesteld op **waar** of **Onwaar**. In de volgende stappen wordt beschreven hoe u een sjabloon maakt in de Azure Portal.

1. Kies in het Azure Portal **een resource maken**.
1. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.
1. Kies **Sjabloonimlementatie (implementeren met aangepaste sjablonen) (preview)**, kies **maken** en kies vervolgens **uw eigen sjabloon bouwen in de editor**.
1. Plak in de sjabloon editor in de volgende JSON om een nieuw account te maken en stel de eigenschap **AllowBlobPublicAccess** in op **waar** of **Onwaar**. Vergeet niet om de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden.

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
1. Geef de para meter van de resource groep op en kies vervolgens de knop **evalueren + maken** om de sjabloon te implementeren en een opslag account te maken met de eigenschap **allowBlobPublicAccess** geconfigureerd.

---

> [!NOTE]
> Het niet toestaan van open bare toegang voor een opslag account heeft geen invloed op alle statische websites die worden gehost in dat opslag account. De **$Web** container is altijd openbaar toegankelijk.
>
> Nadat u de instelling voor open bare toegang voor het opslag account hebt bijgewerkt, kan het tot 30 seconden duren voordat de wijziging volledig is door gegeven.

Voor het toestaan of weigeren van open bare blobs is versie 2019-04-01 of hoger van de resource provider Azure Storage vereist. Zie [Azure Storage Resource Provider rest API](/rest/api/storagerp/)voor meer informatie.

In de voor beelden in deze sectie wordt uitgelegd hoe u de eigenschap **AllowBlobPublicAccess** voor het opslag account kunt lezen om te bepalen of open bare toegang momenteel is toegestaan of niet wordt toegestaan. Zie [anonieme open bare toegang herstellen](anonymous-read-access-prevent.md#remediate-anonymous-public-access)voor meer informatie over het controleren of de open bare toegangs instelling van een account is geconfigureerd om anonieme toegang te voor komen.

## <a name="set-the-public-access-level-for-a-container"></a>Het niveau van de open bare toegang instellen voor een container

Als u anonieme gebruikers lees toegang wilt verlenen voor een container en de bijbehorende blobs, moet u eerst open bare toegang toestaan voor het opslag account en vervolgens het open bare toegangs niveau van de container instellen. Als open bare toegang wordt geweigerd voor het opslag account, kunt u open bare toegang voor een container niet configureren.

Wanneer open bare toegang is toegestaan voor een opslag account, kunt u een container configureren met de volgende machtigingen:

- **Geen open bare Lees toegang:** De container en de bijbehorende blobs kunnen alleen worden geopend met een geautoriseerde aanvraag. Deze optie is de standaard waarde voor alle nieuwe containers.
- **Open bare Lees toegang alleen voor blobs:** Blobs in de container kunnen worden gelezen door anonieme aanvragen, maar de container gegevens zijn niet anoniem beschikbaar. Anonieme clients kunnen de blobs in de container niet inventariseren.
- **Open bare Lees toegang voor container en de bijbehorende blobs:** Container-en BLOB-gegevens kunnen worden gelezen door anonieme aanvragen, met uitzonde ring van instellingen voor container machtigingen en meta gegevens van containers. Clients kunnen blobs in de container inventariseren door anonieme aanvragen, maar kunnen containers niet inventariseren binnen het opslag account.

U kunt het niveau van de open bare toegang voor een afzonderlijke BLOB niet wijzigen. Niveau van open bare toegang wordt alleen op container niveau ingesteld. U kunt het open bare toegangs niveau van de container instellen wanneer u de container maakt, of u kunt de instelling bijwerken op een bestaande container.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om het niveau van de open bare toegang voor een of meer bestaande containers in de Azure Portal bij te werken:

1. Navigeer naar het overzicht van het opslag account in de Azure Portal.
1. Selecteer onder **BLOB service** op de menu Blade de optie **containers**.
1. Selecteer de containers waarvoor u het niveau van open bare toegang wilt instellen.
1. Gebruik de knop **toegangs niveau wijzigen** om de instellingen voor open bare toegang weer te geven.
1. Selecteer het gewenste open bare toegangs niveau van de vervolg keuzelijst **openbaar toegangs niveau** en klik op de knop OK om de wijziging toe te passen op de geselecteerde containers.

    ![Scherm afbeelding die laat zien hoe u het niveau van open bare toegang instelt in de portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Wanneer open bare toegang niet is toegestaan voor het opslag account, kan het open bare toegangs niveau van een container niet worden ingesteld. Als u het open bare toegangs niveau van de container probeert in te stellen, ziet u dat de instelling is uitgeschakeld, omdat open bare toegang niet is toegestaan voor het account.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Scherm opname waarin wordt getoond dat het open bare toegangs niveau van de container wordt geblokkeerd wanneer open bare toegang niet is toegestaan":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u het niveau van open bare toegang voor een of meer containers met Power shell wilt bijwerken, roept u de opdracht [set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) aan. Machtig deze bewerking door de account sleutel, een connection string of een Shared Access Signature (SAS) door te geven. De [set container-ACL](/rest/api/storageservices/set-container-acl) -bewerking waarmee het open bare toegangs niveau van de container wordt ingesteld, biedt geen ondersteuning voor autorisatie met Azure AD. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens bewerkingen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie.

In het volgende voor beeld wordt een container gemaakt waarvoor open bare toegang is uitgeschakeld en wordt vervolgens de open bare toegangs instelling van de container bijgewerkt om anonieme toegang tot de container en de blobs toe te staan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

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

Wanneer open bare toegang niet is toegestaan voor het opslag account, kan het open bare toegangs niveau van een container niet worden ingesteld. Als u het open bare toegangs niveau van de container probeert in te stellen, retourneert Azure Storage een fout melding die aangeeft dat open bare toegang niet is toegestaan voor het opslag account.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u het niveau van de open bare toegang voor een of meer containers met Azure CLI wilt bijwerken, roept u de opdracht [AZ storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) aan. Machtig deze bewerking door de account sleutel, een connection string of een Shared Access Signature (SAS) door te geven. De [set container-ACL](/rest/api/storageservices/set-container-acl) -bewerking waarmee het open bare toegangs niveau van de container wordt ingesteld, biedt geen ondersteuning voor autorisatie met Azure AD. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens bewerkingen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie.

In het volgende voor beeld wordt een container gemaakt waarvoor open bare toegang is uitgeschakeld en wordt vervolgens de open bare toegangs instelling van de container bijgewerkt om anonieme toegang tot de container en de blobs toe te staan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

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

Wanneer open bare toegang niet is toegestaan voor het opslag account, kan het open bare toegangs niveau van een container niet worden ingesteld. Als u het open bare toegangs niveau van de container probeert in te stellen, retourneert Azure Storage een fout melding die aangeeft dat open bare toegang niet is toegestaan voor het opslag account.

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Controleer de instelling voor open bare toegang voor een set containers

Het is mogelijk om te controleren welke containers in een of meer opslag accounts zijn geconfigureerd voor open bare toegang door de containers te vermelden en de instelling voor open bare toegang te controleren. Deze benadering is een praktische optie wanneer een opslag account geen groot aantal containers bevat of wanneer u de instelling controleert over een klein aantal opslag accounts. De prestaties kunnen echter afnemen als u probeert een groot aantal containers te inventariseren.

In het volgende voor beeld wordt Power shell gebruikt om de instelling voor open bare toegang op te halen voor alle containers in een opslag account. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Volgende stappen

- [Anonieme open bare Lees toegang voor containers en blobs voor komen](anonymous-read-access-prevent.md)
- [Anoniem toegang krijgen tot open bare containers en blobs met .NET](anonymous-read-access-client.md)
- [Toegang tot Azure Storage autoriseren](../common/storage-auth.md)
