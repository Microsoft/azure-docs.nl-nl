---
title: Een opslagaccount maken
titleSuffix: Azure Storage
description: Meer informatie over het maken van een opslagaccount voor het opslaan van blobs, bestanden, wachtrijen en tabellen. Een Azure-opslagaccount biedt een unieke naamruimte in Microsoft Azure voor het lezen en schrijven van uw gegevens.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cb5caeb7f75834a317b222392c6e827185cfac00
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714344"
---
# <a name="create-a-storage-account"></a>Een opslagaccount maken

Een Azure-opslagaccount bevat al uw Azure Storage: blobs, bestanden, wachtrijen en tabellen. Het opslagaccount biedt een unieke naamruimte voor uw Azure Storage gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Zie Overzicht van opslagaccounts voor meer informatie [over Azure-opslagaccounts.](storage-account-overview.md)

In dit artikel leert u hoe u een opslagaccount maakt met behulp van [de sjabloon Azure Portal](https://portal.azure.com/), [Azure PowerShell](/powershell/azure/), [Azure CLI](/cli/azure)of een [Azure Resource Manager.](../../azure-resource-manager/management/overview.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Geen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een Azure-opslagaccount wilt maken met PowerShell, moet u ervoor zorgen dat u de [Az PowerShell-module](https://www.powershellgallery.com/packages/Az), versie 0.7 of hoger hebt geïnstalleerd. Zie [Introducing the Azure PowerShell Az module (Inleiding tot de Az Azure PowerShell module) voor meer informatie.](/powershell/azure/new-azureps-module-az)

Voer de volgende opdracht uit om uw huidige versie te vinden:

```powershell
Get-InstalledModule -Name "Az"
```

Zie Install Azure PowerShell module (Installatie van Azure PowerShell module) Azure PowerShell te installeren of [upgraden.](/powershell/azure/install-az-ps)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt u aanmelden bij Azure en Azure CLI-opdrachten uitvoeren. Dit kan op twee manieren:

- U kunt CLI-opdrachten uitvoeren vanuit Azure Portal, in Azure Cloud Shell.
- U kunt de CLI installeren en CLI-opdrachten lokaal uitvoeren.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op **Cloud Shell** knop In het menu in de rechterbovenhoek van de Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Met de knop wordt een interactieve shell start die u kunt gebruiken om de stappen uit te voeren die in dit artikel worden beschreven:

[![Schermopname van het Cloud Shell in de portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

U kunt Azure CLI ook lokaal installeren en gebruiken. Voor de voorbeelden in dit artikel is Azure CLI versie 2.0.4 of hoger vereist. Voer `az --version` uit om de geïnstalleerde versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

# <a name="template"></a>[Sjabloon](#tab/template)

Geen.

---

Meld u daarna aan bij Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Meld u aan bij de [Azure-portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Meld u aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm om u te verifiëren.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Meld u Azure Cloud Shell aan bij de Azure Portal om de [Azure Portal.](https://portal.azure.com)

Voer de opdracht [az login](/cli/azure/reference-index#az-login) uit om u aan te melden bij de lokale installatie van de CLI:

```azurecli-interactive
az login
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Een opslagaccount is een Azure Resource Manager resource. Resource Manager is de implementatie- en beheerservice voor Azure. Zie [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor meer informatie.

Elke Resource Manager resource, inclusief een Azure-opslagaccount, moet deel uitmaken van een Azure-resourcegroep. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In deze how-to ziet u hoe u een nieuwe resourcegroep maakt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u een Azure-opslagaccount wilt maken met Azure Portal, volgt u deze stappen:

1. Selecteer opslagaccounts in het menu aan de **linkerkant van** de portal om een lijst met uw opslagaccounts weer te geven.
1. Selecteer op **de pagina Opslagaccounts** de **optie Nieuw.**

Opties voor uw nieuwe opslagaccount zijn ingedeeld in tabbladen op **de pagina Een opslagaccount** maken. In de volgende secties worden alle tabbladen en de opties beschreven.

### <a name="basics-tab"></a>Tabblad Basisbeginselen

Geef op **het** tabblad Basisinformatie de essentiële informatie op voor uw opslagaccount. Nadat u  het tabblad Basisbeginselen hebt voltooid, kunt u ervoor kiezen om uw nieuwe opslagaccount verder aan te passen door opties in te stellen op de andere tabbladen. U kunt ook **Controleren en** maken selecteren om de standaardopties te accepteren en door te gaan met het valideren en maken van het account.

In de volgende tabel worden de velden op het tabblad **Basisinformatie** beschreven.

| Sectie | Veld | Vereist of optioneel | Description |
|--|--|--|--|
| Projectgegevens | Abonnement | Vereist | Selecteer het abonnement voor het nieuwe opslagaccount. |
| Projectgegevens | Resourcegroep | Vereist | Maak een nieuwe resourcegroep voor dit opslagaccount of selecteer een bestaande. Zie Resourcegroepen [voor meer informatie.](../../azure-resource-manager/management/overview.md#resource-groups) |
| Exemplaardetails | Naam van het opslagaccount | Vereist | Kies een unieke naam voor uw opslagaccount. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. |
| Exemplaardetails | Region | Vereist | Selecteer de juiste regio voor uw opslagaccount. Zie Regio's en Beschikbaarheidszones [in Azure voor meer informatie.](../../availability-zones/az-overview.md)<br /><br />Niet alle regio's worden ondersteund voor alle typen opslagaccounts of redundantieconfiguraties. Zie [Redundantie in Azure Storage](storage-redundancy.md) voor meer informatie.<br /><br />De keuze van de regio kan gevolgen hebben voor de facturering. Zie Facturering voor [opslagaccounts voor meer informatie.](storage-account-overview.md#storage-account-billing) |
| Exemplaardetails | Prestaties | Vereist | Selecteer **Standaardprestaties** voor algemeen v2-opslagaccounts (standaard). Dit type account wordt aanbevolen door Microsoft voor de meeste scenario's. Zie Typen opslagaccounts [voor meer informatie.](storage-account-overview.md#types-of-storage-accounts)<br /><br />Selecteer **Premium voor** scenario's die een lage latentie vereisen. Nadat u **Premium hebt** geselecteerd, selecteert u het type Premium Storage-account dat u wilt maken. De volgende typen Premium Storage-accounts zijn beschikbaar: <ul><li>[Blok-blobs](../blobs/storage-blob-performance-tiers.md)</li><li>[Bestandsshares](../files/storage-files-planning.md#management-concepts)</li><li>[Pagina-blobs](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Exemplaardetails | Redundantie | Vereist | Selecteer de gewenste redundantieconfiguratie. Niet alle redundantieopties zijn beschikbaar voor alle typen opslagaccounts in alle regio's. Zie redundantie voor meer informatie over [redundantieconfiguraties Azure Storage redundantie.](storage-redundancy.md)<br /><br />Als u een geografisch redundante configuratie (GRS of GZRS) selecteert, worden uw gegevens gerepliceerd naar een datacenter in een andere regio. Voor leestoegang tot gegevens in de secundaire regio selecteert u Leestoegang tot gegevens beschikbaar maken in het geval van **regionale onbeschikbaarheid.** |

In de volgende afbeelding ziet u een standaardconfiguratie voor een nieuw opslagaccount.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Schermopname van een standaardconfiguratie voor een nieuw opslagaccount - tabblad Basisinformatie":::

### <a name="advanced-tab"></a>Tabblad Geavanceerd

Op het **tabblad** Geavanceerd kunt u aanvullende opties configureren en de standaardinstellingen voor uw nieuwe opslagaccount wijzigen. Sommige van deze opties kunnen ook worden geconfigureerd nadat het opslagaccount is gemaakt, terwijl andere moeten worden geconfigureerd op het moment dat het wordt gemaakt.

In de volgende tabel worden de velden op het **tabblad Geavanceerd** beschreven.

| Sectie | Veld | Vereist of optioneel | Description |
|--|--|--|--|
| Beveiliging | Beveiligde overdracht inschakelen | Optioneel | Schakel beveiligde overdracht in om te vereisen dat binnenkomende aanvragen naar dit opslagaccount alleen worden gedaan via HTTPS (standaard). Aanbevolen voor optimale beveiliging. Zie Require [secure transfer to ensure secure connections (Veilige overdracht vereisen om beveiligde verbindingen te garanderen) voor meer informatie.](storage-require-secure-transfer.md) |
| Beveiliging | Infrastructuurversleuteling inschakelen | Optioneel | Infrastructuurversleuteling is standaard niet ingeschakeld. Schakel infrastructuurversleuteling in om uw gegevens te versleutelen op zowel serviceniveau als infrastructuurniveau. Zie Een opslagaccount maken met infrastructuurversleuteling ingeschakeld [voor dubbele versleuteling van gegevens voor meer informatie.](infrastructure-encryption-enable.md) |
| Beveiliging | Openbare blobtoegang inschakelen | Optioneel | Wanneer deze instelling is ingeschakeld, kan een gebruiker met de juiste machtigingen anonieme openbare toegang tot een container in het opslagaccount inschakelen (standaard). Als u deze instelling uit uitschakelen, wordt alle anonieme openbare toegang tot het opslagaccount voorkomen. Zie Anonieme openbare leestoegang tot containers en [blobs voorkomen voor meer informatie.](../blobs/anonymous-read-access-prevent.md)<br> <br> Het inschakelen van openbare toegang tot blobs maakt blobgegevens niet beschikbaar voor openbare toegang, tenzij de gebruiker de extra stap neemt om de instelling voor openbare toegang van de container expliciet te configureren. |
| Beveiliging | Toegang tot opslagaccountsleutels inschakelen (preview) | Optioneel | Wanneer deze instelling is ingeschakeld, kunnen clients aanvragen naar het opslagaccount autor toestemming geven met behulp van de toegangssleutels voor het account of een Azure Active Directory-account (Azure AD) (standaard). Het uitschakelen van deze instelling voorkomt autorisatie met de toegangssleutels van het account. Zie Autorisatie van gedeelde sleutels voorkomen voor een [Azure Storage-account (preview) voor meer informatie.](shared-key-authorization-prevent.md) |
| Beveiliging | Minimale TLS-versie | Vereist | Selecteer de minimale versie van Transport Layer Security (TLS) voor binnenkomende aanvragen naar het opslagaccount. De standaardwaarde is TLS-versie 1.2. Als deze waarde is ingesteld op de standaardwaarde, worden binnenkomende aanvragen die worden gedaan met TLS 1.0 of TLS 1.1 geweigerd. Zie Enforce a minimum required version of Transport Layer Security (TLS) for requests to a storage account (Een minimaal vereiste versie van [TLS (TLS) afdwingen voor aanvragen voor een opslagaccount voor meer informatie.](transport-layer-security-configure-minimum-version.md) |
| Data Lake Storage Gen2 | Hiërarchische naamruimte inschakelen | Optioneel | Als u dit opslagaccount wilt gebruiken voor Azure Data Lake Storage Gen2 workloads, configureert u een hiërarchische naamruimte. Zie Introduction [to Azure Data Lake Storage Gen2 (Inleiding tot](../blobs/data-lake-storage-introduction.md)Azure Data Lake Storage Gen2. |
| Blob Storage | Netwerkbestands share (NFS) v3 inschakelen (preview) | Optioneel | NFS v3 biedt linux-bestandssysteemcompatibiliteit op de schaal van objectopslag, zodat Linux-clients een container in Blob Storage kunnen mounten vanaf een virtuele Azure-machine (VM) of een on-premises computer. Zie Network [File System (NFS) 3.0 protocol support in Azure Blob Storage (preview) (Ondersteuning voor network file system (NFS) 3.0-protocol in Azure Blob Storage (preview) voor meer informatie.](../blobs/network-file-system-protocol-support.md) |
| Blob Storage | Toegangslaag | Vereist | Met blob-toegangslagen kunt u blobgegevens op de meest rendabele manier opslaan op basis van gebruik. Selecteer de hot-laag (standaard) voor veelgebruikte gegevens. Selecteer de cool-laag voor gegevens die niet regelmatig worden gebruikt. Zie Toegangslagen voor [Azure Blob Storage- hot, cool en archiveren voor meer informatie.](../blobs/storage-blob-storage-tiers.md) |
| Azure Files | Grote bestandsshares inschakelen | Optioneel | Alleen beschikbaar voor Premium Storage-accounts voor bestands shares. Zie Standaardbestands shares inschakelen voor een [bereik van maximaal 100 TiB voor meer informatie.](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) |
| Tabellen en wachtrijen | Ondersteuning voor door de klant beheerde sleutels inschakelen | Optioneel | Als u ondersteuning wilt inschakelen voor door de klant beheerde sleutels voor tabellen en wachtrijen, moet u deze instelling selecteren op het moment dat u het opslagaccount maakt. Zie Een account maken dat door de klant beheerde sleutels voor tabellen en wachtrijen ondersteunt voor [meer informatie.](account-encryption-key-create.md) |

### <a name="networking-tab"></a>Tabblad Netwerken

Op het **tabblad Netwerken** kunt u instellingen voor netwerkconnectiviteit en routeringsvoorkeuren configureren voor uw nieuwe opslagaccount. Deze opties kunnen ook worden geconfigureerd nadat het opslagaccount is gemaakt.

In de volgende tabel worden de velden op het **tabblad Netwerken** beschreven.

| Sectie | Veld | Vereist of optioneel | Description |
|--|--|--|--|
| Netwerkverbinding | Verbindingsmethode | Vereist | Binnenkomend netwerkverkeer wordt standaard doorgeleid naar het openbare eindpunt voor uw opslagaccount. U kunt opgeven dat verkeer moet worden gerouteerd naar het openbare eindpunt via een virtueel Azure-netwerk. U kunt ook privé-eindpunten configureren voor uw opslagaccount. Zie Use [private endpoints for Azure Storage (Privé-eindpunten gebruiken voor Azure Storage).](storage-private-endpoints.md) |
| Netwerkroutering | Routeringsvoorkeur | Vereist | De voorkeur voor netwerkroutering geeft aan hoe netwerkverkeer wordt gerouteerd naar het openbare eindpunt van uw opslagaccount van clients via internet. Een nieuw opslagaccount maakt standaard gebruik van Microsoft-netwerkroutering. U kunt er ook voor kiezen om netwerkverkeer via de POP te leiden die het dichtst bij het opslagaccount ligt, waardoor de netwerkkosten kunnen worden verlaagd. Zie Network routing [preference for Azure Storage (Netwerkrouteringsvoorkeur voor Azure Storage.](network-routing-preference.md) |

### <a name="data-protection-tab"></a>Tabblad Gegevensbescherming

Op het **tabblad Gegevensbeveiliging** kunt u opties voor gegevensbeveiliging configureren voor blobgegevens in uw nieuwe opslagaccount.  Deze opties kunnen ook worden geconfigureerd nadat het opslagaccount is gemaakt. Zie Overzicht van gegevensbescherming voor een overzicht Azure Storage opties [voor gegevensbeveiliging in uw account.](../blobs/data-protection-overview.md)

In de volgende tabel worden de velden op het tabblad **Gegevensbeveiliging** beschreven.

| Sectie | Veld | Vereist of optioneel | Description |
|--|--|--|--|
| Herstel | Herstel naar een bepaald tijdstip inschakelen voor containers | Optioneel | Herstel naar een bepaald tijdstip biedt bescherming tegen onbedoelde verwijdering of beschadiging doordat u blok-blobgegevens naar een eerdere status kunt herstellen. Zie Herstel naar een bepaald [tijdstip voor blok-blobs voor meer informatie.](../blobs/point-in-time-restore-overview.md)<br /><br />Als u herstel naar een bepaald tijdstip inschakelen, kunt u ook blobversies, blobs voor het verwijderen van blobs en de blob-wijzigingsfeed inschakelen. Deze vereiste functies kunnen een kostenimpact hebben. Zie Prijzen en facturering [voor](../blobs/point-in-time-restore-overview.md#pricing-and-billing) herstel naar een bepaald tijdstip voor meer informatie. |
| Herstel | Voorlopig verwijderen inschakelen voor blobs | Optioneel | Met de optie Voor het verwijderen van blobs wordt een afzonderlijke blob, momentopname of versie beschermd tegen onbedoeld verwijderen of overschrijven door de verwijderde gegevens in het systeem gedurende een opgegeven bewaarperiode te behouden. Tijdens de retentieperiode kunt u een object dat is verwijderd herstellen naar de status op het moment dat het werd verwijderd. Zie Voor meer informatie Soft delete for blobs (Soft [delete voor blobs).](../blobs/soft-delete-blob-overview.md)<br /><br />Microsoft raadt aan om voor uw opslagaccounts blobs in te stellen en een minimale bewaarperiode van zeven dagen in te stellen. |
| Herstel | Soft Delete inschakelen voor containers (preview) | Optioneel | Met de container voor het verwijderen van containers worden een container en de inhoud beschermd tegen onbedoeld verwijderen door de verwijderde gegevens in het systeem te onderhouden voor een opgegeven bewaarperiode. Tijdens de retentieperiode kunt u een container die is verwijderd, herstellen naar de status op het moment dat deze werd verwijderd. Zie Voor meer informatie Soft [delete for containers (preview) (Zacht verwijderen voor containers (preview) ).](../blobs/soft-delete-container-overview.md)<br /><br />Microsoft raadt u aan om voor uw opslagaccounts de mogelijkheid te gebruiken voor het inschakelen van de tijdelijke opslagaccounts en een minimale bewaarperiode van zeven dagen in te stellen. |
| Herstel | Soft Delete inschakelen voor bestands shares | Optioneel | Door de verwijderde gegevens in het systeem te bewaren voor een opgegeven bewaarperiode, beschermt u een bestands share en de inhoud ervan tegen onbedoeld verwijderen. Tijdens de bewaarperiode kunt u een zacht verwijderde bestands share herstellen naar de status op het moment dat deze werd verwijderd. Zie Onopzettelijk verwijderen van [Azure-bestands shares voorkomen voor meer informatie.](../files/storage-files-prevent-file-share-deletion.md)<br /><br />Microsoft raadt aan om voor alle werkbelastingen een zachte Azure Files in te stellen en een minimale bewaarperiode van zeven dagen in te stellen. |
| Tracering | Versieversies voor blobs inschakelen | Optioneel | Bij blobversies wordt de status van een blob in een eerdere versie automatisch opgeslagen wanneer de blob wordt overschreven. Zie [Blobversies voor meer informatie.](../blobs/versioning-overview.md)<br /><br />Microsoft raadt aan blobversies in te stellen voor optimale gegevensbeveiliging voor het opslagaccount. |
| Tracering | Blob-wijzigingsfeed inschakelen | Optioneel | De blob-wijzigingenfeed bevat transactielogboeken van alle wijzigingen in alle blobs in uw opslagaccount, evenals de metagegevens. Zie Ondersteuning voor de [wijzigingsfeed in](../blobs/storage-blob-change-feed.md)Azure Blob Storage . |

### <a name="tags-tab"></a>Tabblad Tags

Op het **tabblad Tags** kunt u de tags Resource Manager om uw Azure-resources te organiseren. Zie Resources, [resourcegroepen](../../azure-resource-manager/management/tag-resources.md)en abonnementen taggen voor logische organisatie voor meer informatie.

### <a name="review--create-tab"></a>Tabblad Beoordelen en maken

Wanneer u naar het tabblad **Beoordelen en maken** navigeert, voert Azure de validatie uit op de opslagaccountinstellingen die u hebt gekozen. Als de validatie is verlopen, kunt u doorgaan met het maken van het opslagaccount.

Als de validatie mislukt, geeft de portal aan welke instellingen moeten worden gewijzigd.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een v2-opslagaccount voor algemeen gebruik wilt maken met PowerShell, maakt u eerst een nieuwe resourcegroep door de [opdracht New-AzResourceGroup aan te](/powershell/module/az.resources/new-azresourcegroup) roepen:

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Als u niet zeker weet welke regio u moet opgeven voor de parameter `-Location`, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```azurepowershell-interactive
Get-AzLocation | select Location
```

Maak vervolgens een standaard v2-opslagaccount voor algemeen gebruik met geografisch redundante opslag met leestoegang (RA-GRS) met behulp van de [opdracht New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Houd er rekening mee dat de naam van uw opslagaccount uniek moet zijn in Azure. Vervang daarom de tijdelijke aanduiding tussen vierkante haken door uw eigen unieke waarde:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Als u een hiërarchische naamruimte wilt inschakelen voor het opslagaccount om [Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)neemt u de parameter op voor de aanroep van de `-EnableHierarchicalNamespace $True` opdracht **New-AzStorageAccount.**

In de volgende tabel ziet u welke waarden moeten worden gebruikt voor de parameters en om een bepaald type opslagaccount met `-SkuName` `-Kind` de gewenste redundantieconfiguratie te maken.

| Type opslagaccount | Ondersteunde redundantieconfiguraties | Waarde voor de parameter -Kind | Mogelijke waarden voor de parameter -SkuName | Ondersteunt hiërarchische naamruimte |
|--|--|--|--|--|
| Standaard algemeen gebruik v2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS/ Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Premium blok-blobs | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Premiumbestandsshares | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | No |
| Premium-pagina-blobs | LRS | StorageV2 | Premium_LRS | No |
| Verouderde standaard algemeen gebruik v1 | LRS/GRS/ RA-GRS | Storage | Standard_LRS/ Standard_GRS / Standard_RAGRS | No |
| Verouderde blobopslag | LRS/GRS/ RA-GRS | BlobStorage | Standard_LRS/ Standard_GRS / Standard_RAGRS | No |

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een v2-opslagaccount voor algemeen gebruik wilt maken met Azure CLI, maakt u eerst een nieuwe resourcegroep door de [opdracht az group create aan te](/cli/azure/group#az_group_create) roepen.

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Als u niet zeker weet welke regio u moet opgeven voor de parameter `--location`, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Maak vervolgens een standaard v2-opslagaccount voor algemeen gebruik met geografisch redundante opslag met leestoegang met behulp van [de opdracht az storage account create.](/cli/azure/storage/account#az_storage_account_create) Houd er rekening mee dat de naam van uw opslagaccount uniek moet zijn in Azure. Vervang daarom de tijdelijke aanduiding tussen vierkante haken door uw eigen unieke waarde:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Als u een hiërarchische naamruimte voor het opslagaccount wilt gebruiken [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), neemt u de parameter op in de aanroep van de `--enable-hierarchical-namespace true` opdracht az storage account **create.** Voor het maken van een hiërarchische naamruimte is Azure CLI versie 2.0.79 of hoger vereist.

In de volgende tabel ziet u welke waarden moeten worden gebruikt voor de parameters en om een bepaald type opslagaccount met `-sku` de gewenste redundantieconfiguratie te `-kind` maken.

| Type opslagaccount | Ondersteunde redundantieconfiguraties | Waarde voor de parameter -kind | Mogelijke waarden voor de parameter -sku | Ondersteunt hiërarchische naamruimte |
|--|--|--|--|--|
| Standaard algemeen gebruik v2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS/ Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Premium blok-blobs | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Premiumbestandsshares | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | No |
| Premium-pagina-blobs | LRS | StorageV2 | Premium_LRS | No |
| Verouderde standaard algemeen gebruik v1 | LRS / GRS / RA-GRS | Storage | Standard_LRS/ Standard_GRS / Standard_RAGRS | No |
| Verouderde blobopslag | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS/ Standard_GRS / Standard_RAGRS | No |

# <a name="template"></a>[Sjabloon](#tab/template)

U kunt een Azure PowerShell of Azure CLI gebruiken om een Resource Manager te implementeren om een opslagaccount te maken. De sjabloon die in dit artikel wordt gebruikt, is afkomstig [uit Azure Resource Manager quickstartsjablonen.](https://azure.microsoft.com/resources/templates/101-storage-account-create/) Als u de scripts wilt uitvoeren, **selecteert u Proberen om** de Azure Cloud Shell. Plak het script door met de rechtermuisknop op de shell te klikken en **Plakken** te selecteren.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Deze sjabloon fungeert alleen als voorbeeld. Er zijn veel opslagaccountinstellingen die niet zijn geconfigureerd als onderdeel van deze sjabloon. Als u bijvoorbeeld een [Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)wijzigt u deze sjabloon door de eigenschap van het object in `isHnsEnabledad` te stellen op `StorageAccountPropertiesCreateParameters` `true` .

Zie voor meer informatie over het wijzigen van deze sjabloon of het maken van nieuwe sjabloon:

- [Azure Resource Manager documentatie](../../azure-resource-manager/index.yml).
- [Verwijzing naar opslagaccountsjabloon](/azure/templates/microsoft.storage/allversions).
- [Aanvullende voorbeelden van opslagaccountsjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

## <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

Als u een opslagaccount verwijdert, wordt het hele account verwijderd, inclusief alle gegevens in het account, en kan het niet ongedaan worden gemaakt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigeer naar het opslagaccount in [Azure Portal](https://portal.azure.com).
1. Klik op **Verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u het opslagaccount wilt verwijderen, gebruikt [u de opdracht Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u het opslagaccount wilt verwijderen, gebruikt u [de opdracht az storage account delete:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Sjabloon](#tab/template)

Als u het opslagaccount wilt verwijderen, gebruikt u Azure PowerShell of Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

U kunt ook de resourcegroep verwijderen, waardoor het opslagaccount en alle andere resources in die resourcegroep worden verwijderd. Zie Resourcegroep en resources verwijderen voor meer informatie over het verwijderen [van een resourcegroep.](../../azure-resource-manager/management/delete-resource-group.md)

> [!WARNING]
> Het is niet mogelijk om een verwijderd opslagaccount te herstellen of om inhoud terug te halen die het account vóór verwijdering bevatte. Zorg ervoor dat u een back-up maakt van alles dat u wilt opslaan, voordat u het account verwijdert. Dit geldt ook voor alle resources in het account: als u blobs, tabellen, wachtrijen of bestanden verwijdert, worden deze permanent verwijderd.
>
> Als u probeert een opslagaccount te verwijderen dat is gekoppeld aan een virtuele machine van Azure, ziet u mogelijk een foutbericht dat het account nog in gebruik is. Zie Problemen oplossen bij het verwijderen van opslagaccounts voor hulp bij het oplossen [van deze fout.](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van opslagaccounts](storage-account-overview.md)
- [Upgraden naar een v2-opslagaccount voor algemeen gebruik](storage-account-upgrade.md)
- [Een opslagaccount verplaatsen naar een andere regio](storage-account-move.md)
- [Een verwijderd opslagaccount herstellen](storage-account-recover.md)