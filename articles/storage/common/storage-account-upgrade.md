---
title: Upgraden naar een v2-opslagaccount voor algemeen gebruik
titleSuffix: Azure Storage
description: Upgrade naar v2-opslagaccounts voor algemeen gebruik met behulp van Azure Portal, PowerShell of de Azure CLI. Geef een toegangslaag op voor blobgegevens.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 786eb981acd61d952f95f89a7d90e4f732f3cda4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887644"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgraden naar een v2-opslagaccount voor algemeen gebruik

V2-opslagaccounts voor algemeen gebruik ondersteunen de nieuwste Azure Storage functies en bevatten alle functionaliteit van algemeen v1- en Blob Storage-accounts. V2-accounts voor algemeen gebruik worden aanbevolen voor de meeste opslagscenario's. V2-accounts voor algemeen gebruik bieden de laagste capaciteitsprijzen per gigabyte voor Azure Storage en concurrerende transactieprijzen in de branche. V2-accounts voor algemeen gebruik bieden ondersteuning voor standaardaccounttoegangslagen van hot- of cool- en blob-lagen tussen hot, cool of archive.

Upgraden naar een v2-opslagaccount voor algemeen gebruik vanuit uw accounts voor algemeen gebruik v1 of Blob Storage is eenvoudig. U kunt een upgrade uitvoeren met Azure Portal, PowerShell of Azure CLI. Er is geen downtime of het risico op gegevensverlies bij een upgrade naar een v2-opslagaccount voor algemeen gebruik. De accountupgrade vindt plaats via een Azure Resource Manager bewerking die het accounttype wijzigt.

> [!IMPORTANT]
> Het upgraden van een algemeen v1- of Blob Storage-account naar algemeen gebruik v2 is permanent en kan niet ongedaan worden gemaakt.

> [!NOTE]
> Hoewel Microsoft voor de meeste scenario's algemeen v2-accounts aanbeveelt, blijft Microsoft ondersteuning bieden voor accounts voor algemeen gebruik v1 voor nieuwe en bestaande klanten. U kunt v1-opslagaccounts voor algemeen gebruik maken in nieuwe regio's wanneer Azure Storage beschikbaar is in deze regio's. Microsoft is momenteel niet van plan ondersteuning voor accounts voor algemeen gebruik v1 af te nemen en zal ten minste één jaar vooraf melden voordat een van de Azure Storage wordt afgeschaft. Microsoft blijft beveiligingsupdates leveren voor v1-accounts voor algemeen gebruik, maar er wordt geen nieuwe functieontwikkeling verwacht voor dit accounttype.
>
> Voor nieuwe Azure-regio's die na 1 oktober 2020 online zijn gekomen, zijn de prijzen voor accounts voor algemeen gebruik v1 gewijzigd en komen deze overeen met de prijzen voor accounts voor algemeen gebruik v2 in deze regio's. De prijzen voor v1-accounts voor algemeen gebruik in Azure-regio's die vóór 1 oktober 2020 bestonden, zijn niet gewijzigd. Zie de pagina met prijzen voor algemene v1-accounts in een specifieke regio voor Azure Storage prijsinformatie. Kies uw regio en selecteer vervolgens naast **Prijsaanbiedingen** de optie **Overige.**

## <a name="upgrade-an-account"></a>Een account upgraden

Als u een algemeen v1- of Blob Storage-account wilt upgraden naar een v2-account voor algemeen gebruik, gebruikt u Azure Portal, PowerShell of Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Ga naar uw opslagaccount.
3. Klik in **de sectie** Instellingen op **Configuratie.**
4. Klik onder **Soort account** op **Upgrade**.
5. Typ bij **Upgrade bevestigen** de naam van uw account.
6. Klik **onderaan** de blade op Upgraden.

    ![Soort account upgraden](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een v1-account voor algemeen gebruik wilt upgraden naar een v2-account voor algemeen gebruik met behulp van PowerShell, moet u eerst PowerShell bijwerken om de nieuwste versie van de **Az.Storage-module te** gebruiken. Zie [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van PowerShell.

Roep vervolgens de volgende opdracht aan om het account bij te upgraden, door de naam van de resourcegroep, de naam van het opslagaccount en de gewenste accounttoegangslaag te vervangen.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een v1-account voor algemeen gebruik wilt upgraden naar een v2-account voor algemeen gebruik met behulp van Azure CLI, installeert u eerst de nieuwste versie van Azure CLI. Zie [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI.

Roep vervolgens de volgende opdracht aan om het account bij te upgraden, door de naam van de resourcegroep, de naam van het opslagaccount en de gewenste accounttoegangslaag te vervangen.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Een toegangslaag voor blobgegevens opgeven

Accounts voor algemeen gebruik v2 ondersteunen alle Azure-opslagservices en -gegevensobjecten, maar toegangslagen zijn alleen beschikbaar voor blok-blobs in Blob Storage. Wanneer u een upgrade naar een v2-opslagaccount voor algemeen gebruik hebt uitgevoerd, kunt u een standaardaccounttoegangslaag van hot of cool opgeven. Dit geeft aan dat de standaardlaag uw blobgegevens worden geüpload alsof de parameter voor de afzonderlijke blobtoegangslaag niet is opgegeven.

Met blob-toegangslagen kunt u de meest rendabele opslag kiezen op basis van uw verwachte gebruikspatronen. Blok-blobs kunnen worden opgeslagen in een hot-, cool- of archieflaag. Zie Azure [Blob-opslag: Hot-, Cool-](../blobs/storage-blob-storage-tiers.md)en Archive Storage-lagen voor meer informatie over toegangslagen.

Standaard wordt een nieuw opslagaccount gemaakt in de hot-toegangslaag en kan een algemeen v1-opslagaccount worden bijgewerkt naar de opslaglaag voor 'hot' of 'cool' account. Als er geen accounttoegangslaag is opgegeven bij de upgrade, wordt deze standaard bijgewerkt naar hot. Als u wilt verkennen welke toegangslaag u voor uw upgrade wilt gebruiken, kunt u uw huidige scenario voor gegevensgebruik overwegen. Er zijn twee typische gebruikersscenario's voor migratie naar een v2-account voor algemeen gebruik:

* U hebt een bestaand v1-opslagaccount voor algemeen gebruik en wilt een upgrade evalueren naar een v2-opslagaccount voor algemeen gebruik, met de juiste opslagtoegangslaag voor blobgegevens.
* U hebt besloten om een v2-opslagaccount voor algemeen gebruik te gebruiken of u hebt er al een en wilt evalueren of u de toegangslaag voor 'hot' of 'cool' opslag moet gebruiken voor blobgegevens.

In beide gevallen is de eerste prioriteit het schatten van de kosten voor het opslaan, openen en gebruiken van uw gegevens die zijn opgeslagen in een v2-opslagaccount voor algemeen gebruik en deze te vergelijken met uw huidige kosten.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Het upgraden van een v1-opslagaccount naar een v2-account voor algemeen gebruik is gratis. U kunt tijdens het upgradeproces de gewenste accountlaag opgeven. Als er geen accountlaag is opgegeven bij de upgrade, is de standaardaccountlaag van het bijgewerkte account `Hot` . Het wijzigen van de opslagtoegangslaag na de upgrade kan echter leiden tot wijzigingen in uw factuur. Het wordt daarom aanbevolen om tijdens de upgrade de nieuwe accountlaag op te geven.

Alle opslagaccounts maken gebruik van een prijsmodel voor het opslaan van blobs op basis van laag van elke blob. Als u een opslagaccount gebruikt, zijn de volgende factureringsvoorwaarden van toepassing:

* **Opslagkosten:** naast de hoeveelheid opgeslagen gegevens, variëren de kosten voor het opslaan van gegevens, afhankelijk van de opslagtoegangslaag. De kosten per GB nemen af als de laag minder dynamisch ('cooler') wordt.

* **Kosten van gegevenstoegang**: de kosten voor gegevenstoegang nemen toe als de laag minder dynamisch ('cooler') wordt. Voor gegevens in de cool- en archive storage-toegangslaag worden kosten in rekening gebracht per gigabyte aan gegevenstoegang voor leesgegevens.

* **Transactiekosten**: er gelden kosten per transactie voor alle lagen. Deze kosten nemen toe als de laag minder dynamisch wordt.

* **Kosten voor gegevensoverdracht met geo-replicatie**: deze kosten zijn alleen van toepassing op accounts waarvoor geo-replicatie is geconfigureerd, inclusief GRS en RA-GRS. Kosten voor gegevensoverdracht met geo-replicatie worden in rekening gebracht per GB.

* **Kosten voor uitgaande gegevensoverdracht**: uitgaande gegevensoverdracht (gegevens die buiten een Azure-regio worden overgedragen) worden gefactureerd voor bandbreedtegebruik per GB, net zoals bij opslagaccounts voor algemeen gebruik.

* **De opslagtoegangslaag wijzigen:** als u de toegangslaag voor de accountopslag wilt wijzigen van Cool in Hot, worden kosten in rekening brengen die gelijk zijn aan het lezen van alle bestaande gegevens in het opslagaccount. Als u de toegangslaag van het account echter wilt wijzigen van 'hot' naar 'cool', worden er kosten in rekening gesteld die gelijk zijn aan het schrijven van alle gegevens naar de cool-laag (alleen GPv2-accounts).

> [!NOTE]
> Zie de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie over het prijsmodel voor opslagaccounts. Zie de pagina [Prijsinformatie voor bandbreedte](https://azure.microsoft.com/pricing/details/data-transfers/) voor meer informatie over de kosten voor uitgaande gegevensoverdracht.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Kosten schatten voor uw huidige gebruikspatronen

Als u een schatting wilt maken van de kosten voor het opslaan en openen van blobgegevens in een v2-opslagaccount voor algemeen gebruik in een bepaalde laag, evalueert u uw bestaande gebruikspatroon of benadert u het verwachte gebruikspatroon. Doorgaans zijn de volgende gegevens hiervoor van belang:

* Uw blobopslagverbruik, in gigabytes, waaronder:
  * Hoeveel gegevens worden opgeslagen in het opslagaccount?
  * Hoe verandert het gegevensvolume op maandbasis; worden oude gegevens voortdurend vervangen door nieuwe gegevens?

* Het primaire toegangspatroon voor uw Blob Storage-gegevens, waaronder:
  * Uit hoeveel gegevens worden gegevens gelezen en naar het opslagaccount geschreven?
  * Hoeveel leesbewerkingen versus schrijfbewerkingen worden uitgevoerd op de gegevens in het opslagaccount?

Als u wilt bepalen wat de beste toegangslaag voor uw behoeften is, kan het handig zijn om de capaciteit van uw blobgegevens te bepalen en hoe die gegevens worden gebruikt. U kunt dit het beste doen door te kijken naar de metrische bewakingsgegevens voor uw account.

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

Zie Metrische gegevens van Opslaganalyse voor meer informatie over het inschakelen, verzamelen en weergeven van metrische [gegevens.](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> Het opslaan, openen en downloaden van analytische gegevens wordt op dezelfde manier in rekening gebracht als normale gebruikersgegevens.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Metrische gegevens over het gebruik inzetten voor het schatten van de kosten

#### <a name="capacity-costs"></a>Capaciteitskosten

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
> Voor een gedetailleerder voorbeeld over het berekenen van de kosten voor het gebruik van de hot- of cool storage-toegangslaag, bekijkt u de veelgestelde vragen 'Wat zijn hot- en cool-toegangslagen en hoe moet ik bepalen welke u wilt *gebruiken?'* op de [pagina met prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van opslagaccounts](storage-account-overview.md)
* [Een opslagaccount maken](storage-account-create.md)
* [Een account Azure Storage verplaatsen naar een andere regio](storage-account-move.md)
* [Een verwijderd opslagaccount herstellen](storage-account-recover.md)
