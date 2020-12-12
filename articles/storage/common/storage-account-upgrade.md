---
title: Upgraden naar een V2-opslagaccount voor algemeen gebruik
titleSuffix: Azure Storage
description: Voer een upgrade uit naar v2-opslag accounts voor algemeen gebruik met de Azure Portal, Power shell of de Azure CLI. Geef een toegangs laag op voor BLOB-gegevens.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 50a0894898dcdc817ee253ad326b88a9bb61b9d6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357361"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgraden naar een V2-opslagaccount voor algemeen gebruik

V2-opslag accounts voor algemeen gebruik ondersteunen de nieuwste functies van Azure Storage en bevatten alle functionaliteit van v1-en Blob Storage-accounts voor algemeen gebruik. V2-accounts voor algemeen gebruik worden aanbevolen voor de meeste opslag scenario's. Bij v2-accounts voor algemeen gebruik worden de laagste capaciteits prijzen per GB voor Azure Storage en de prijzen voor de toonaangevende trans acties geleverd. V2-accounts voor algemeen gebruik ondersteunen de standaard toegangs lagen voor accounts van warme of koelen en lagen op BLOB-niveau tussen warme, koude of archief.

Een upgrade uitvoeren naar een v2-opslag account voor algemeen gebruik vanuit uw opslag accounts voor algemeen gebruik v1 of blobs is eenvoudig. U kunt een upgrade uitvoeren met behulp van de Azure Portal, Power shell of Azure CLI. Er is geen downtime of risico van gegevens verlies dat is gekoppeld aan het upgraden naar een v2-opslag account voor algemeen gebruik. De account upgrade vindt plaats via een eenvoudige Azure Resource Manager bewerking die het account type wijzigt.

> [!IMPORTANT]
> Een upgrade uitvoeren van een v1-of Blob Storage-account voor algemeen gebruik naar algemeen gebruik v2 is permanent en kan niet ongedaan worden gemaakt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Ga naar uw opslagaccount.
3. Klik in de sectie **instellingen** op **configuratie**.
4. Klik onder **Soort account** op **Upgrade**.
5. Typ bij **Upgrade bevestigen** de naam van uw account.
6. Klik onder aan de Blade op **upgrade** .

    ![Soort upgrade-account](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een algemeen v1-account wilt bijwerken naar een v2-account voor algemeen gebruik met Power shell, moet u Power shell eerst bijwerken om de nieuwste versie van de module **AZ. Storage** te gebruiken. Zie [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van PowerShell.

Roep vervolgens de volgende opdracht aan om het account bij te werken, de naam van de resource groep, de naam van het opslag account en de gewenste toegangs laag voor het account te vervangen.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u een algemeen v1-account wilt bijwerken naar een v2-account voor algemeen gebruik met behulp van Azure CLI, moet u eerst de nieuwste versie van Azure CLI installeren. Zie [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI.

Roep vervolgens de volgende opdracht aan om het account bij te werken, de naam van de resource groep, de naam van het opslag account en de gewenste toegangs laag voor het account te vervangen.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Een toegangs laag voor BLOB-gegevens opgeven

V2-accounts voor algemeen gebruik ondersteunen alle Azure Storage-services en gegevens objecten, maar toegangs lagen zijn alleen beschikbaar voor het blok keren van blobs in Blob Storage. Wanneer u een upgrade uitvoert naar een v2-opslag account voor algemeen gebruik, kunt u een standaard account toegangs niveau van warme of koud opgeven. Dit geeft aan dat de standaardlaag uw BLOB-gegevens worden geüpload alsof de afzonderlijke BLOB-Access tier-para meter niet is opgegeven.

Met Blob-toegangs lagen kunt u de meest rendabele opslag kiezen op basis van uw verwachte gebruiks patronen. Blok-blobs kunnen worden opgeslagen in een warme, koele of archief laag. Zie [Azure Blob Storage: warme, cool en archief opslag lagen](../blobs/storage-blob-storage-tiers.md)voor meer informatie over toegangs lagen.

Er wordt standaard een nieuw opslag account gemaakt in de laag Hot Access en een algemeen v1-opslag account kan worden bijgewerkt naar de categorie Hot of cool. Als er geen toegangs niveau voor een account is opgegeven bij een upgrade, wordt het standaard bijgewerkt naar dynamisch. Als u de toegangs laag bekijkt die u voor uw upgrade wilt gebruiken, moet u rekening houden met het huidige scenario voor het gebruik van gegevens. Er zijn twee typische gebruikers scenario's voor het migreren naar een v2-account voor algemeen gebruik:

* U hebt een bestaand v1-opslag account voor algemeen gebruik en wilt een upgrade naar een v2-opslag account voor algemeen gebruik evalueren, met de juiste opslag toegangs laag voor BLOB-gegevens.
* U hebt besloten om een v2-opslag account voor algemeen gebruik of al een te gebruiken en u wilt te evalueren of u de warme of cool Storage Access-laag moet gebruiken voor BLOB-gegevens.

In beide gevallen is de eerste prioriteit het schatten van de kosten voor het opslaan, openen en uitvoeren van uw gegevens die zijn opgeslagen in een v2-opslag account voor algemeen gebruik en om te vergelijken met uw huidige kosten.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Een upgrade uitvoeren van een v1-opslag account naar een v2-account voor algemeen gebruik is gratis. U kunt de gewenste gegevenslaagtoepassing tijdens het upgrade proces opgeven. Als er bij een upgrade geen AccountName is opgegeven, is de standaardlaag van de bijgewerkte account `Hot` . Het wijzigen van de opslag toegangs laag na de upgrade kan echter leiden tot wijzigingen in uw factuur, zodat u de nieuwe gegevenslaagtoepassing tijdens de upgrade kunt opgeven.

Alle opslagaccounts maken gebruik van een prijsmodel voor het opslaan van blobs op basis van laag van elke blob. Als u een opslagaccount gebruikt, zijn de volgende factureringsvoorwaarden van toepassing:

* **Opslag kosten**: naast de hoeveelheid opgeslagen gegevens, zijn de kosten voor het opslaan van gegevens afhankelijk van de opslag toegangs laag. De kosten per GB nemen af als de laag minder dynamisch ('cooler') wordt.

* **Kosten van gegevenstoegang**: de kosten voor gegevenstoegang nemen toe als de laag minder dynamisch ('cooler') wordt. Voor gegevens in de koel-en archief opslag-laag, worden er kosten in rekening gebracht voor de toegang tot gegevens per gigabyte.

* **Transactiekosten**: er gelden kosten per transactie voor alle lagen. Deze kosten nemen toe als de laag minder dynamisch wordt.

* **Kosten voor gegevensoverdracht met geo-replicatie**: deze kosten zijn alleen van toepassing op accounts waarvoor geo-replicatie is geconfigureerd, inclusief GRS en RA-GRS. Kosten voor gegevensoverdracht met geo-replicatie worden in rekening gebracht per GB.

* **Kosten voor uitgaande gegevensoverdracht**: uitgaande gegevensoverdracht (gegevens die buiten een Azure-regio worden overgedragen) worden gefactureerd voor bandbreedtegebruik per GB, net zoals bij opslagaccounts voor algemeen gebruik.

* **De laag voor opslag toegang wijzigen**: het toegangs niveau van de account opslag wijzigen van koud in warme, waarbij de kosten gelijk zijn aan het lezen van alle gegevens in het opslag account. Als u de laag van het account echter wijzigt van dynamisch in Cool, worden er kosten in rekening gebracht die gelijk zijn aan het schrijven van alle gegevens in de cool-laag (alleen GPv2-accounts).

> [!NOTE]
> Zie de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie over het prijsmodel voor opslagaccounts. Zie de pagina [Prijsinformatie voor bandbreedte](https://azure.microsoft.com/pricing/details/data-transfers/) voor meer informatie over de kosten voor uitgaande gegevensoverdracht.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Kosten schatten voor uw huidige gebruiks patronen

Voor een schatting van de kosten voor het opslaan en openen van BLOB-gegevens in een v2-opslag account voor algemeen gebruik in een bepaalde laag, moet u uw bestaande gebruiks patroon evalueren of een schatting maken van het verwachte gebruiks patroon. Doorgaans zijn de volgende gegevens hiervoor van belang:

* Uw gebruik van Blob-opslag, in gigabytes, met inbegrip van:
  * Hoeveel gegevens worden opgeslagen in het opslagaccount?
  * Hoe verandert het gegevensvolume op maandbasis; worden oude gegevens voortdurend vervangen door nieuwe gegevens?

* Het primaire toegangs patroon voor uw Blob Storage-gegevens, met inbegrip van:
  * Hoeveel gegevens worden er van gelezen en naar het opslag account geschreven?
  * Hoeveel Lees bewerkingen versus schrijf bewerkingen worden uitgevoerd op de gegevens in het opslag account?

Om te beslissen over de beste Access-laag voor uw behoeften, kan het nuttig zijn om de capaciteit van uw BLOB-gegevens te bepalen en hoe deze gegevens worden gebruikt. U kunt dit het beste doen door de metrische gegevens voor uw account te controleren.

### <a name="monitoring-existing-storage-accounts"></a>Bewaking van bestaande opslagaccounts

Voor het bewaken van uw bestaande opslagaccounts en het verzamelen van deze gegevens, kunt u gebruikmaken van Azure Opslaganalyse, dat logboekregistratie uitvoert en metrische gegevens biedt voor een opslagaccount. Opslaganalyse kan metrische gegevens opslaan die samengevoegde transactiestatistieken en capaciteitsgegevens bevat over serviceaanvragen voor GPv1- en GPv2-opslagaccounts en Blob Storage-accounts. Deze gegevens worden opgeslagen in bekende tabellen in hetzelfde opslagaccount.

Zie [About Storage Analytics Metrics](../blobs/monitor-blob-storage.md) (Metrische gegevens in opslaganalyse) en [Storage Analytics Metrics Table Schema](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) (Tabelschema van metrische gegevens in opslaganalyse) voor meer informatie

> [!NOTE]
> Blob Storage-accounts geven het service-eindpunt van de tabel alleen weer voor het opslaan en openen van de metrische gegevens voor dat account.

Voor het controleren van het opslagverbruik voor Blob Storage moet u de metrische gegevens over capaciteit inschakelen.
Als dit is ingeschakeld, worden de capaciteitsgegevens van een Blob Storage-serviceaccount dagelijks geregistreerd en vastgelegd als een tabelvermelding die naar de *$MetricsCapacityBlob*-tabel binnen hetzelfde opslagaccount wordt geschreven.

Voor het controleren van gegevenstoegangspatronen voor Blob Storage, moet u de metrische gegevens die per uur worden verzameld voor de transactie inschakelen vanaf de API. Als deze methode is ingeschakeld, worden er elk uur per-API-transacties verzameld en geregistreerd als een tabelvermelding die is naar de *$MetricsHourPrimaryTransactionsBlob*-tabel binnen hetzelfde opslagaccount wordt geschreven. De *$MetricsHourSecondaryTransactionsBlob*-tabel registreert de transacties naar het secundaire eindpunt bij gebruik van RA-GRS-opslagaccounts.

> [!NOTE]
> Als u een algemeen opslagaccount hebt waarin u pagina-blobs en virtuele-machineschijven of wachtrijen, bestanden of tabellen, hebt opgeslagen naast blok- en toevoegblobgegevens, is dit schattingsproces niet van toepassing. De capaciteitsgegevens maken geen onderscheid tussen blok-blobs en andere typen en geven geen capaciteitsgegevens voor andere gegevenstypen. Als u deze typen gebruikt, is een alternatieve methode om te kijken naar de hoeveelheden op uw meest recente factuur.

Als u een goede schatting wilt maken van uw gegevensverbruik en toegangspatroon, raden we u aan voor de metrische gegevens een retentieperiode te kiezen die een goede afspiegeling is van uw normale gebruik en dat als uitgangspunt te nemen. Een optie is de metrische gegevens zeven dagen te bewaren en de gegevens elke week te verzamelen en aan het einde van de maand te analyseren. Een andere optie is de metrische gegevens van de afgelopen 30 dagen te bewaren en deze gegevens aan het einde van deze periode van 30 dagen te verzamelen en te analyseren.

Voor informatie over het inschakelen, verzamelen en weer geven van metrische gegevens, Zie [metrische opslag analyse-metrieken](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Het opslaan, openen en downloaden van analytische gegevens wordt op dezelfde manier in rekening gebracht als normale gebruikersgegevens.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Metrische gegevens over het gebruik inzetten voor het schatten van de kosten

#### <a name="capacity-costs"></a>Capaciteits kosten

De meest recente vermelding in de metrische gegevenstabel voor capaciteit *$MetricsCapacityBlob* met de rijsleutel *'data'* toont de opslagcapaciteit die wordt gebruikt door gebruikersgegevens. De meest recente vermelding in de metrische gegevenstabel voor capaciteit *$MetricsCapacityBlob* met de rijsleutel *'analytics'* toont de opslagcapaciteit die wordt gebruikt door de analyselogboeken.

Deze totale capaciteit die wordt verbruikt door zowel gebruikersgegevens en analyselogboeken (indien ingeschakeld) kunnen vervolgens worden gebruikt om de opslagkosten te schatten voor gegevens in het opslagaccount. Deze manier kan ook worden gebruikt voor het schatten van de opslagkosten in GPv1-opslagaccounts.

#### <a name="transaction-costs"></a>Transactiekosten

Het totaal van *'TotalBillableRequests'*, in alle items voor een API in de metrische gegevenstabel voor transactie, geeft het totale aantal transacties voor die bepaalde API weer. *Bijvoorbeeld*: het totale aantal *GetBlob*-transacties in een bepaalde periode kan worden berekend door het totaal te nemen van het totale aantal factureerbare aanvragen voor alle items met de rijsleutel *user;GetBlob*.

Voor het schatten van de transactiekosten van Blob Storage-accounts moet u de transacties in drie groepen opdelen omdat ze verschillend zijn geprijsd.

* Schrijftransacties zoals *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* en *'CopyBlob'*.
* Verwijdertransacties zoals *'DeleteBlob'* en *'DeleteContainer'*.
* Alle andere transacties.

Voor het schatten van de transactiekosten voor GPv1-opslagaccounts moet u alle transacties verzamelen ongeacht de bewerking/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Gegevenstoegang en overdrachtskosten voor geo-replicatiegegevens

Opslaganalyse biedt geen informatie over de hoeveelheid gegevens die zijn gelezen en geschreven van en naar een opslagaccount, maar deze hoeveelheid kan min of meer worden geschat door te kijken naar de metrische gegevenstabel voor transacties. Het totaal van *'TotalIngress'* in alle items voor een API in de metrische gegevenstabel voor transacties, geeft de totale hoeveelheid inkomende gegevens in bytes voor die bepaalde API weer. Op dezelfde manier geeft het totaal van *'TotalEgress'* de totale hoeveelheid uitgaande gegevens in bytes weer.

Voor het schatten van de kosten voor het openen van gegevens in Blob Storage-accounts moet u de transacties in twee groepen opdelen.

* De hoeveelheid gegevens die is opgehaald van het opslagaccount, kan worden geschat door te kijken naar het totaal van *'TotalEgress'* voor met name de *'GetBlob'*- en *'CopyBlob'*-bewerkingen.

* De hoeveelheid gegevens die wordt geschreven naar het opslagaccount kan worden geschat door te kijken naar het totaal van *'TotalIngress'* voor met name de *'PutBlob'*-, *'PutBlock'*-, *'CopyBlob'*- en *'AppendBlock'*-bewerkingen.

De overdrachtskosten van geo-replicatiegegevens voor Blob Storage-accounts kan ook worden berekend met behulp van de schatting voor de hoeveelheid gegevens die wordt geschreven bij gebruik van een GRS- of RA-GRS-opslagaccount.

> [!NOTE]
> Raadpleeg de veelgestelde vragen met de titel *' wat zijn warme en koude toegangs lagen? '* voor een gedetailleerd voor beeld over het berekenen van de kosten voor het gebruik van de Hot of cool Storage-laag. op de [pagina met prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van opslagaccounts](storage-account-overview.md)
* [Een opslagaccount maken](storage-account-create.md)
* [Een Azure Storage-account naar een andere regio verplaatsen](storage-account-move.md)
* [Een verwijderd opslagaccount herstellen](storage-account-recover.md)