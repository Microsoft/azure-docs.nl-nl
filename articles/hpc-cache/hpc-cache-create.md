---
title: Een Azure HPC Cache
description: Een Azure HPC Cache maken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 02934a1943ef37d282dd2a2e7862c5695bbd6ecb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862701"
---
# <a name="create-an-azure-hpc-cache"></a>Een Azure HPC Cache

Gebruik de Azure Portal of de Azure CLI om uw cache te maken.

![schermopname van cacheoverzicht in Azure Portal, met de knop Maken onderaan](media/hpc-cache-home-page.png)

Klik op de onderstaande afbeelding om een [videodemonstratie te bekijken](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) van het maken van een cache en het toevoegen van een opslagdoel.

[![videominiature: Azure HPC Cache: Instellen (klik om naar de videopagina te gaan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Basisdetails definiëren

![schermopname van de pagina projectdetails in Azure Portal](media/hpc-cache-create-basics.png)

Selecteer **in Projectdetails** het abonnement en de resourcegroep die de cache gaan hosten.

Stel **in Servicedetails** de cachenaam en de volgende andere kenmerken in:

* Locatie: selecteer een van de [ondersteunde regio's.](hpc-cache-overview.md#region-availability)
* Virtueel netwerk: u kunt een bestaand netwerk selecteren of een nieuw virtueel netwerk maken.
* Subnet: kies of maak een subnet met ten minste 64 IP-adressen (/24). Dit subnet mag alleen worden gebruikt voor dit Azure HPC Cache exemplaar.

## <a name="set-cache-capacity"></a>Cachecapaciteit instellen
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Op de **pagina Cache** moet u de capaciteit van uw cache instellen. De waarden die hier worden ingesteld, bepalen hoeveel gegevens uw cache kan bevatten en hoe snel deze clientaanvragen kunnen verwerken.

Capaciteit is ook van invloed op de kosten van de cache.

Kies de capaciteit door deze twee waarden in te stellen:

* De maximale overdrachtssnelheid voor de cache (doorvoer), in GB/seconde
* De hoeveelheid opslag die is toegewezen voor gegevens in de cache, in TB

Kies een van de beschikbare doorvoerwaarden en opslaggrootten voor de cache.

Houd er rekening mee dat de werkelijke gegevensoverdrachtsnelheid afhankelijk is van de werkbelasting, netwerksnelheden en het type opslagdoelen. Met de waarden die u kiest, stelt u de maximale doorvoer voor het hele cachesysteem in, maar sommige worden gebruikt voor overheadtaken. Als een client bijvoorbeeld een bestand aanvraagt dat nog niet in de cache is opgeslagen of als het bestand als verouderd is gemarkeerd, gebruikt uw cache een deel van de doorvoer om het op te halen uit de back-endopslag.

Azure HPC Cache beheert welke bestanden in de cache worden opgeslagen en vooraf worden geladen om de treffers van de cache te maximaliseren. De inhoud van de cache wordt continu geëvalueerd en bestanden worden verplaatst naar langetermijnopslag wanneer ze minder vaak worden gebruikt. Kies een cacheopslaggrootte die de actieve set werkbestanden kan bevatten, plus extra ruimte voor metagegevens en andere overhead.

![schermopname van de pagina voor het wijzigen van de cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Versleuteling Azure Key Vault inschakelen (optioneel)

De **pagina Schijfversleutelingssleutels** wordt weergegeven tussen **de tabbladen Cache** en Tags. <!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Als u de versleutelingssleutels wilt beheren die worden gebruikt voor uw cacheopslag, moet Azure Key Vault op de pagina **Schijfversleutelingssleutels.** De sleutelkluis moet zich in dezelfde regio en in hetzelfde abonnement als de cache.

U kunt deze sectie overslaan als u geen door de klant beheerde sleutels nodig hebt. Azure versleutelt gegevens standaard met door Microsoft beheerde sleutels. Lees [Azure Storage-versleuteling](../storage/common/storage-service-encryption.md) voor meer informatie.

> [!NOTE]
>
> * U kunt niet wisselen tussen door Microsoft beheerde sleutels en door de klant beheerde sleutels nadat u de cache hebt gemaakt.
> * Nadat de cache is gemaakt, moet u deze machtigen voor toegang tot de sleutelkluis. Klik op **de knop Versleuteling** inschakelen op de pagina **Overzicht** van de cache om versleuteling in te stellen. Neem deze stap binnen 90 minuten na het maken van de cache.
> * Cacheschijven worden gemaakt na deze autorisatie. Dit betekent dat de eerste tijd voor het maken van de cache kort is, maar dat de cache pas tien minuten of meer gereed is voor gebruik nadat u de toegang hebt geautoriseerd.

Lees Door de klant beheerde versleutelingssleutels gebruiken voor een volledige uitleg van het versleutelingsproces van door de [klant beheerde Azure HPC Cache.](customer-keys.md)

![schermopname van de pagina met versleutelingssleutels met door de klant beheerde velden en sleutelkluisvelden](media/create-encryption.png)

Selecteer **Door de klant beheerd** om door de klant beheerde sleutelversleuteling te kiezen. De sleutelkluisspecificatievelden worden weergegeven. Selecteer de Azure Key Vault wilt gebruiken en selecteer vervolgens de sleutel en versie die u voor deze cache wilt gebruiken. De sleutel moet een 2048-bits RSA-sleutel zijn. Op deze pagina kunt u een nieuwe sleutelkluis, sleutel of sleutelversie maken.

Nadat u de cache hebt gemaakt, moet u deze machtigen voor het gebruik van de sleutelkluisservice. Lees [Autor Azure Key Vault versleuteling van de cache voor](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) meer informatie.

## <a name="add-resource-tags-optional"></a>Resourcetags toevoegen (optioneel)

Op **de pagina** Tags kunt u [resourcetags toevoegen](../azure-resource-manager/management/tag-resources.md) aan uw Azure HPC Cache exemplaar.

## <a name="finish-creating-the-cache"></a>Het maken van de cache voltooien

Nadat u de nieuwe cache hebt geconfigureerd, klikt u op **het tabblad Beoordelen en** maken. In de portal worden uw selecties gevalideerd en kunt u uw keuzes bekijken. Als alles klopt, klikt u op **Maken.**

Het maken van de cache duurt ongeveer 10 minuten. U kunt de voortgang volgen in het deelvenster Azure Portal meldingen van het systeem.

![schermopname van de pagina's 'Implementatie wordt bezig' en 'meldingen' van cache maken in de portal](media/hpc-cache-deploy-status.png)

Wanneer het maken is voltooien, wordt er een melding weergegeven met een koppeling naar het nieuwe Azure HPC Cache-exemplaar en wordt de cache weergegeven in de **lijst Resources van uw** abonnement.

![schermopname van Azure HPC Cache-exemplaar in Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Als uw cache gebruikmaakt van door de klant beheerde versleutelingssleutels, wordt de cache mogelijk weergegeven in de lijst met resources voordat de implementatiestatus wordt gewijzigd. Zodra de cache de status Wachten op sleutel **heeft,** kunt u deze autor [laten gebruiken](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) voor het gebruik van de sleutelkluis.

## <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>De cache maken met Azure CLI

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

> [!NOTE]
> De Azure CLI biedt momenteel geen ondersteuning voor het maken van een cache met door de klant beheerde versleutelingssleutels. Gebruik Azure Portal.

Gebruik de [opdracht az hpc-cache create](/cli/azure/hpc-cache#az_hpc_cache_create) om een nieuw Azure HPC Cache.

Hier kunt u de volgende waarden op geven:

* Naam van cacheresourcegroep
* Cachenaam
* Azure-regio
* Cachesubnet, in deze indeling:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Het cachesubnet heeft ten minste 64 IP-adressen (/24) nodig en kan geen andere resources huis houden.

* Cachecapaciteit. Met twee waarden wordt de maximale doorvoer van uw Azure HPC Cache:

  * De cachegrootte (in GB)
  * De SKU van de virtuele machines die worden gebruikt in de cache-infrastructuur

  [az hpc-cache skus list toont](/cli/azure/hpc-cache/skus) de beschikbare SKU's en de opties voor de geldige cachegrootte voor elke SKU. Opties voor cachegrootte variëren van 3 TB tot 48 TB, maar slechts enkele waarden worden ondersteund.

  In deze grafiek ziet u welke cachegrootte en SKU-combinaties geldig zijn op het moment dat dit document wordt voorbereid (juli 2020).

  | Cachegrootte | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nee          | nee          |
  | 6144 GB    | ja         | ja         | nee          |
  | 12288 GB   | ja         | ja         | ja         |
  | 24576 GB   | nee          | ja         | ja         |
  | 49152 GB   | nee          | nee          | ja         |

  Lees de **sectie Cachecapaciteit instellen** op het tabblad met portalinstructies voor belangrijke informatie over prijzen, doorvoer en hoe u de grootte van uw cache geschikt kunt maken voor uw werkstroom.

Voorbeeld van het maken van de cache:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Het maken van de cache duurt enkele minuten. Als het maken is geslaagd, retourneert de opdracht uitvoer als deze:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Het bericht bevat nuttige informatie, waaronder de volgende items:

* Client-koppeladressen: gebruik deze IP-adressen wanneer u klaar bent om clients te verbinden met de cache. Lees [De Azure HPC Cache](hpc-cache-mount.md) voor meer informatie.
* Upgradestatus: wanneer er een software-update wordt uitgebracht, wordt dit bericht gewijzigd. U kunt [de cachesoftware handmatig](hpc-cache-manage.md#upgrade-cache-software) bijwerken op een handig moment, anders wordt deze na enkele dagen automatisch toegepast.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> De PowerShell-module Az.HPCCache is momenteel beschikbaar als openbare preview. Deze preview-versie wordt geleverd zonder Service Level Agreement. Dit wordt niet aanbevolen voor productieworkloads. Sommige functies worden mogelijk niet ondersteund of hebben beperkte mogelijkheden. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="requirements"></a>Vereisten

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell. Als u Cloud Shell gebruikt, raadpleegt u [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie.

> [!IMPORTANT]
> Hoewel de **PowerShell-module Az.HPCCache** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet . Nadat deze PowerShell-module algemeen beschikbaar is, maakt deze deel uit van toekomstige releases van Az PowerShell-modules en is deze systeemeigen beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>De cache maken met Azure PowerShell

> [!NOTE]
> Azure PowerShell biedt momenteel geen ondersteuning voor het maken van een cache met door de klant beheerde versleutelingssleutels. Gebruik Azure Portal.

Gebruik de [cmdlet New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) om een nieuw Azure HPC Cache.

Geef deze waarden op:

* Naam van cacheresourcegroep
* Cachenaam
* Azure-regio
* Cachesubnet, in deze indeling:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Het cachesubnet heeft ten minste 64 IP-adressen (/24) nodig en kan geen andere resources huis houden.

* Cachecapaciteit. Met twee waarden wordt de maximale doorvoer van uw Azure HPC Cache:

  * De cachegrootte (in GB)
  * De SKU van de virtuele machines die worden gebruikt in de cache-infrastructuur

  [Get-AzHpcCacheSku toont](/powershell/module/az.hpccache/get-azhpccachesku) de beschikbare SKU's en de opties voor de geldige cachegrootte voor elke SKU. Opties voor cachegrootte variëren van 3 TB tot 48 TB, maar slechts enkele waarden worden ondersteund.

  In deze grafiek ziet u welke cachegrootte en SKU-combinaties geldig zijn op het moment dat dit document wordt voorbereid (juli 2020).

  | Cachegrootte | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nee          | nee          |
  | 6144 GB    | ja         | ja         | nee          |
  | 12.288 GB   | ja         | ja         | ja         |
  | 24.576 GB   | nee          | ja         | ja         |
  | 49.152 GB   | nee          | nee          | ja         |

  Lees de **sectie Cachecapaciteit instellen** op het tabblad Instructies voor de portal voor belangrijke informatie over prijzen, doorvoer en hoe u de grootte van uw cache geschikt kunt maken voor uw werkstroom.

Voorbeeld van het maken van de cache:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

Het maken van de cache duurt enkele minuten. Als het maken is geslaagd, retourneert de opdracht de volgende uitvoer:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Het bericht bevat nuttige informatie, waaronder de volgende items:

* Client-koppeladressen: gebruik deze IP-adressen wanneer u klaar bent om clients te verbinden met de cache. Lees [De Azure HPC Cache](hpc-cache-mount.md) voor meer informatie.
* Upgradestatus: wanneer een software-update wordt uitgebracht, wordt dit bericht gewijzigd. U kunt [cachesoftware handmatig op](hpc-cache-manage.md#upgrade-cache-software) een handig moment upgraden of deze wordt na enkele dagen automatisch toegepast.

---

## <a name="next-steps"></a>Volgende stappen

Nadat uw cache wordt weergegeven in de **lijst Resources,** kunt u naar de volgende stap gaan.

* [Definieer opslagdoelen](hpc-cache-add-storage.md) om uw cache toegang te geven tot uw gegevensbronnen.
* Als u door de klant beheerde [](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) versleutelingssleutels gebruikt, moet u Azure Key Vault versleuteling van de overzichtspagina van de cache toestaan om uw cache-installatie te voltooien. U moet deze stap doen voordat u opslag kunt toevoegen. Lees [Door de klant beheerde versleutelingssleutels gebruiken](customer-keys.md) voor meer informatie.
