---
title: Een HPC-cache van Azure maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 50fdab1fa8def1b4e3b9a9d59d3b2593e5526c65
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472037"
---
# <a name="create-an-azure-hpc-cache"></a>Een HPC-cache van Azure maken

Gebruik de Azure Portal of de Azure CLI om uw cache te maken.

![scherm opname van het cache-overzicht in Azure Portal, met de knop maken onderaan](media/hpc-cache-home-page.png)

Klik op de onderstaande afbeelding om een [video demonstratie](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) te bekijken van het maken van een cache en het toevoegen van een opslag doel.

[![Video miniatuur: Azure HPC cache: Setup (Klik om de video pagina te bezoeken)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Basis Details definiëren

![scherm afbeelding van de pagina project details in Azure Portal](media/hpc-cache-create-basics.png)

Selecteer in **Project Details** het abonnement en de resource groep die als host moet fungeren voor de cache.

Stel in **service Details** de naam van de cache en de andere kenmerken in:

* Locatie: Selecteer een van de [ondersteunde regio's](hpc-cache-overview.md#region-availability).
* Virtueel netwerk: u kunt een bestaande selecteren of een nieuw virtueel netwerk maken.
* Subnet: Kies of maak een subnet met ten minste 64 IP-adressen (/24). Dit subnet moet alleen worden gebruikt voor deze Azure HPC-cache-instantie.

## <a name="set-cache-capacity"></a>Cache capaciteit instellen
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Op de pagina **cache** moet u de capaciteit van uw cache instellen. De waarden die u hier instelt, bepalen hoeveel gegevens uw cache kan bevatten en hoe snel deze client aanvragen kunnen worden verwerkt.

De capaciteit is ook van invloed op de kosten van de cache.

Kies de capaciteit door deze twee waarden in te stellen:

* De maximale snelheid van gegevens overdracht voor de cache (door Voer), in GB/seconde
* De hoeveelheid opslag ruimte die is toegewezen voor gegevens in de cache, in TB

Kies een van de beschik bare doorvoer waarden en cache opslag grootten.

Houd er wel voor dat de werkelijke gegevens overdrachts snelheid afhankelijk is van de werk belasting, de netwerk snelheid en het type opslag doelen. Met de waarden die u kiest, stelt u de maximale door Voer voor het hele cache systeem in, maar een aantal dat wordt gebruikt voor overhead taken. Als een client bijvoorbeeld een bestand aanvraagt dat niet al in de cache is opgeslagen, of als het bestand is gemarkeerd als verouderd, gebruikt de cache een deel van de door Voer om het op te halen uit de back-end-opslag.

Met Azure HPC cache kunt u beheren welke bestanden in de cache worden opgeslagen en vooraf worden geladen om het aantal cache treffers te maximaliseren. De cache-inhoud wordt voortdurend beoordeeld en bestanden worden verplaatst naar lange termijn opslag wanneer ze minder vaak worden gebruikt. Kies een cache opslag grootte die de actieve set van werk bestanden kan bevatten, plus extra ruimte voor meta gegevens en andere overhead.

![scherm afbeelding van de pagina cache grootte](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Versleuteling van Azure Key Vault inschakelen (optioneel)

De pagina **schijf versleutelings sleutels** wordt weer gegeven tussen de tabbladen **cache** en **Tags** .<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Als u de versleutelings sleutels wilt beheren die voor uw cache opslag worden gebruikt, geeft u uw Azure Key Vault informatie op de pagina **schijf versleutelings sleutels** . De sleutel kluis moet zich in dezelfde regio en in hetzelfde abonnement bevinden als de cache.

U kunt deze sectie overs Laan als u geen door de klant beheerde sleutels nodig hebt. Azure versleutelt standaard gegevens met door micro soft beheerde sleutels. Lees de [Azure Storage-versleuteling](../storage/common/storage-service-encryption.md) voor meer informatie.

> [!NOTE]
>
> * U kunt geen andere door micro soft beheerde sleutels en door de klant beheerde sleutels wijzigen nadat u de cache hebt gemaakt.
> * Nadat de cache is gemaakt, moet u deze autoriseren voor toegang tot de sleutel kluis. Klik op de knop **versleuteling inschakelen** op de pagina **overzicht** van de cache om versleuteling in te scha kelen. Neem deze stap binnen 90 minuten na het maken van de cache.
> * Na deze autorisatie worden cache schijven gemaakt. Dit betekent dat de initiële tijd voor het maken van de cache kort is, maar dat de cache tien minuten of meer niet kan worden gebruikt nadat u toegang hebt geautoriseerd.

Lees voor een volledige uitleg van het door de klant beheerde sleutel versleutelings proces een door de klant beheerde versleutelings sleutel [gebruiken voor Azure HPC-cache](customer-keys.md).

![scherm afbeelding van de pagina versleutelings sleutels met de selectie ' door de klant beheerd ' geselecteerde en sleutel kluis velden die worden weer gegeven](media/create-encryption.png)

Selecteer door de **klant beheerd** voor het kiezen van door de klant beheerde sleutel versleuteling. De sleutel kluis specificatie velden worden weer gegeven. Selecteer de Azure Key Vault die u wilt gebruiken en selecteer vervolgens de sleutel en versie die u voor deze cache wilt gebruiken. De sleutel moet een 2048-bits RSA-sleutel zijn. U kunt op deze pagina een nieuwe sleutel kluis, sleutel of sleutel versie maken.

Nadat u de cache hebt gemaakt, moet u deze machtigen voor het gebruik van de sleutel kluis-service. Lees [Azure Key Vault versleuteling autoriseren vanuit de cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) voor meer informatie.

## <a name="add-resource-tags-optional"></a>Resource Tags toevoegen (optioneel)

Op de pagina **Tags** kunt u [resource Tags](../azure-resource-manager/management/tag-resources.md) toevoegen aan uw Azure HPC-cache-exemplaar.

## <a name="finish-creating-the-cache"></a>Het maken van de cache volt ooien

Nadat u de nieuwe cache hebt geconfigureerd, klikt u op het tabblad **controleren + maken** . In de portal worden uw selecties gevalideerd en kunt u uw keuzes controleren. Als alles correct is, klikt u op **maken**.

Het maken van de cache duurt ongeveer 10 minuten. U kunt de voortgang volgen in het deel venster meldingen van de Azure Portal.

![scherm opname van pagina's voor het maken van de implementatie van de cache en meldingen in de portal](media/hpc-cache-deploy-status.png)

Wanneer het maken is voltooid, wordt er een melding weer gegeven met een koppeling naar het nieuwe Azure HPC-cache-exemplaar. de cache wordt weer gegeven in de lijst met **resources** van uw abonnement.

![scherm opname van het Azure HPC-cache-exemplaar in Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Als uw cache door de klant beheerde versleutelings sleutels gebruikt, kan de cache worden weer gegeven in de lijst met resources voordat de implementatie status wordt gewijzigd in voltooid. Zodra de status van de cache wacht op de sleutel, kunt u [deze machtigen](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) voor het gebruik **van** de sleutel kluis.

## <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>De cache maken met Azure CLI

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

> [!NOTE]
> De Azure CLI biedt momenteel geen ondersteuning voor het maken van een cache met door de klant beheerde versleutelings sleutels. Gebruik Azure Portal.

Gebruik de opdracht [AZ HPC-cache Create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) om een nieuwe Azure HPC-cache te maken.

Geef deze waarden op:

* Naam van de resource groep in de cache
* Cache naam
* Azure-regio
* Cache-subnet, in deze indeling:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Het cache-subnet heeft ten minste 64 IP-adressen (/24) nodig en kan geen andere resources bevatten.

* Cache capaciteit. Met twee waarden is de maximale door Voer van uw Azure HPC-cache ingesteld:

  * De cache grootte (in GB)
  * De SKU van de virtuele machines die worden gebruikt in de cache-infra structuur

  [AZ HPC-cache sku's lijst](/cli/azure/ext/hpc-cache/hpc-cache/skus) toont de beschik bare sku's en de geldige opties voor cache grootte voor elke SKU. Opties voor cache grootte variëren van 3 TB tot 48 TB, maar slechts enkele waarden worden ondersteund.

  In dit diagram ziet u welke cache grootte en SKU-combi Naties geldig zijn op het moment dat dit document wordt voor bereid (juli 2020).

  | Cachegrootte | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nee          | nee          |
  | 6144 GB    | ja         | ja         | nee          |
  | 12288 GB   | ja         | ja         | ja         |
  | 24576 GB   | nee          | ja         | ja         |
  | 49152 GB   | nee          | nee          | ja         |

  Lees de sectie **cache capaciteit instellen** in het tabblad Portal-instructies voor belang rijke informatie over prijzen, door Voer en de grootte van uw cache op de juiste manier voor uw werk stroom.

Voor beeld van het maken van cache:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Het maken van de cache duurt enkele minuten. Als de opdracht maken is voltooid, wordt de uitvoer als volgt geretourneerd:

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

* Client koppel adressen: gebruik deze IP-adressen wanneer u klaar bent om clients te verbinden met de cache. Lees [de Azure HPC-cache koppelen](hpc-cache-mount.md) voor meer informatie.
* Upgrade status: dit bericht wordt gewijzigd wanneer een software-update wordt uitgebracht. U kunt de [cache software](hpc-cache-manage.md#upgrade-cache-software) hand matig bijwerken op een geschikt tijdstip, of deze wordt na enkele dagen automatisch toegepast.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> De Power shell-module AZ. HPCCache is momenteel beschikbaar als open bare preview. Deze preview-versie wordt geleverd zonder Service Level Agreement. Dit wordt niet aanbevolen voor productieworkloads. Sommige functies worden mogelijk niet ondersteund of hebben mogelijk beperkte mogelijkheden. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="requirements"></a>Vereisten

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell. Als u Cloud Shell gebruikt, raadpleegt u [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie.

> [!IMPORTANT]
> Hoewel de Power shell-module **AZ. HPCCache** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet. Nadat deze Power shell-module algemeen beschikbaar is, zal deze deel uitmaken van toekomstige AZ Power shell-module releases en beschikbaar zijn vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>De cache maken met Azure PowerShell

> [!NOTE]
> Azure PowerShell biedt momenteel geen ondersteuning voor het maken van een cache met door de klant beheerde versleutelings sleutels. Gebruik Azure Portal.

Gebruik de cmdlet [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) om een nieuwe Azure HPC-cache te maken.

Geef de volgende waarden op:

* Naam van de resource groep in de cache
* Cache naam
* Azure-regio
* Cache-subnet, in deze indeling:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Het cache-subnet heeft ten minste 64 IP-adressen (/24) nodig en kan geen andere resources bevatten.

* Cache capaciteit. Met twee waarden is de maximale door Voer van uw Azure HPC-cache ingesteld:

  * De cache grootte (in GB)
  * De SKU van de virtuele machines die worden gebruikt in de cache-infra structuur

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) toont de beschik bare sku's en de geldige opties voor cache grootte voor elke. Opties voor cache grootte variëren van 3 TB tot 48 TB, maar slechts enkele waarden worden ondersteund.

  In dit diagram ziet u welke cache grootte en SKU-combi Naties geldig zijn op het moment dat dit document wordt voor bereid (juli 2020).

  | Cachegrootte | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nee          | nee          |
  | 6144 GB    | ja         | ja         | nee          |
  | 12.288 GB   | ja         | ja         | ja         |
  | 24.576 GB   | nee          | ja         | ja         |
  | 49.152 GB   | nee          | nee          | ja         |

  Lees de sectie **cache capaciteit instellen** in het tabblad Portal-instructies voor belang rijke informatie over prijzen, door Voer en de grootte van uw cache op de juiste manier voor uw werk stroom.

Voor beeld van het maken van cache:

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

Het maken van de cache duurt enkele minuten. Als de opdracht maken is voltooid, wordt de volgende uitvoer geretourneerd:

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

* Client koppel adressen: gebruik deze IP-adressen wanneer u klaar bent om clients te verbinden met de cache. Lees [de Azure HPC-cache koppelen](hpc-cache-mount.md) voor meer informatie.
* Upgrade status: dit bericht wordt gewijzigd wanneer een software-update wordt uitgebracht. U kunt de [cache software](hpc-cache-manage.md#upgrade-cache-software) hand matig bijwerken op een geschikt tijdstip, of deze wordt na enkele dagen automatisch toegepast.

---

## <a name="next-steps"></a>Volgende stappen

Als uw cache wordt weer gegeven in de lijst met **resources** , kunt u door gaan naar de volgende stap.

* [Definieer opslag doelen](hpc-cache-add-storage.md) om uw cache toegang te geven tot uw gegevens bronnen.
* Als u versleutelings sleutels van door de klant beheerde code ring gebruikt, moet u Azure Key Vault versleuteling van de cache van de-overzichts pagina [machtigen](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) om de cache-instellingen te volt ooien. U moet deze stap uitvoeren voordat u opslag kunt toevoegen. Lees door de [klant beheerde versleutelings sleutels gebruiken](customer-keys.md) voor meer informatie.