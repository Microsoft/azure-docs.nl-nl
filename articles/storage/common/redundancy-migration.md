---
title: Wijzigen hoe een opslagaccount wordt gerepliceerd
titleSuffix: Azure Storage
description: Meer informatie over hoe u kunt wijzigen hoe gegevens in een bestaand opslag account worden gerepliceerd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/24/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 228595bf633ef0545a13abe19308e49da82cf75a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844009"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Wijzigen hoe een opslagaccount wordt gerepliceerd

Azure Storage slaat altijd meerdere kopieën van uw gegevens op, zodat deze beschermd zijn tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen en enorme natuur rampen. Redundantie zorgt ervoor dat uw opslag account voldoet aan de [Service Level Agreement (Sla) voor Azure Storage,](https://azure.microsoft.com/support/legal/sla/storage/) zelfs in het geval van fouten.

Azure Storage biedt de volgende typen replicatie:

- Lokaal redundante opslag (LRS)
- Zone-redundante opslag (ZRS)
- Geografisch redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS)
- Geo-zone-redundante opslag (GZRS) of geo-zone-redundante opslag met lees toegang (RA-GZRS)

Zie [Azure Storage redundantie](storage-redundancy.md)voor een overzicht van elk van deze opties.

## <a name="switch-between-types-of-replication"></a>Scha kelen tussen typen replicatie

U kunt een opslag account overschakelen van het ene type replicatie naar elk ander type, maar sommige scenario's zijn eenvoudiger dan andere. Als u geo-replicatie wilt toevoegen aan of verwijderen uit de secundaire regio, kunt u de Azure Portal, Power shell of Azure CLI gebruiken om de replicatie-instelling bij te werken. Als u echter wilt wijzigen hoe gegevens worden gerepliceerd in de primaire regio, door te verplaatsen van LRS naar ZRS of andersom, moet u een hand matige migratie uitvoeren.

De volgende tabel bevat een overzicht van de manier waarop u van elk type replicatie naar een andere kunt overschakelen:

| Draaien | ... naar LRS | ... naar GRS/RA-GRS | ... naar ZRS | ... naar GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... van LRS</b> | N.v.t. | Gebruik Azure Portal, Power shell of CLI om de replicatie-instelling te wijzigen<sup>1</sup> | Een hand matige migratie uitvoeren <br /><br /> OF <br /><br /> Een Live migratie aanvragen | Een hand matige migratie uitvoeren <br /><br /> OF <br /><br /> Schakel eerst over naar GRS/RA-GRS en vraag vervolgens een Livemigratie aan.<sup>1</sup> |
| <b>... van GRS/RA-GRS</b> | Azure Portal, Power shell of CLI gebruiken om de replicatie-instelling te wijzigen | N.v.t. | Een hand matige migratie uitvoeren <br /><br /> OF <br /><br /> Schakel eerst over naar LRS en vraag vervolgens een Livemigratie aan | Een hand matige migratie uitvoeren <br /><br /> OF <br /><br /> Een Live migratie aanvragen |
| <b>... van ZRS</b> | Een hand matige migratie uitvoeren | Een hand matige migratie uitvoeren | N.v.t. | Gebruik Azure Portal, Power shell of CLI om de replicatie-instelling<sup>1, 2</sup> te wijzigen |
| <b>... van GZRS/RA-GZRS</b> | Een hand matige migratie uitvoeren | Een hand matige migratie uitvoeren | Azure Portal, Power shell of CLI gebruiken om de replicatie-instelling te wijzigen | N.v.t. |

<sup>1</sup> maakt een eenmalige uitvulling van kosten.<br />
<sup>2</sup> conversie van ZRS naar GZRS/Ra-GZRS of vice versa wordt niet ondersteund in de volgende REGIO'S: VS Oost 2, VS Oost, Europa-West.

> [!CAUTION]
> Als u een [account-failover](storage-disaster-recovery-guidance.md) hebt uitgevoerd voor uw (RA-) GRS of (RA-) GZRS-account, is het account lokaal redundant in de nieuwe primaire regio na de failover. Livemigratie naar ZRS of GZRS voor een LRS-account dat voortkomt uit een failover, wordt niet ondersteund. Dit geldt zelfs in het geval van zogenaamde failback-bewerkingen. Als u bijvoorbeeld een account-failover van RA-GZRS naar de LRS in de secundaire regio uitvoert en deze vervolgens opnieuw configureert in RA-GRS en een andere account-failover naar de oorspronkelijke primaire regio uitvoert, kunt u geen contact opnemen met de ondersteuning voor de oorspronkelijke Livemigratie naar RA-GZRS in de primaire regio. In plaats daarvan moet u een hand matige migratie uitvoeren naar ZRS of GZRS.

## <a name="change-the-replication-setting"></a>De replicatie-instelling wijzigen

U kunt de Azure Portal, Power shell of Azure CLI gebruiken om de replicatie-instelling voor een opslag account te wijzigen, zolang u niet wijzigt hoe gegevens worden gerepliceerd in de primaire regio. Als u migreert van LRS in de primaire regio naar ZRS in de primaire regio of andersom, moet u een hand matige migratie of een Livemigratie uitvoeren.

Het wijzigen van de manier waarop uw opslag account wordt gerepliceerd, resulteert niet in de tijd voor uw toepassingen.

# <a name="portal"></a>[Portal](#tab/portal)

Voer de volgende stappen uit om de redundantie optie voor uw opslag account te wijzigen in de Azure Portal:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **configuratie** -instelling.
1. Werk de **replicatie** -instelling bij.

![Scherm afbeelding die laat zien hoe u de replicatie optie in de portal wijzigt](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de optie voor redundantie voor uw opslag account wilt wijzigen met Power shell, roept u de opdracht [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan en geeft u de `-SkuName` para meter:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de optie voor redundantie voor uw opslag account wilt wijzigen met Azure CLI, roept u de opdracht [AZ Storage account update](/cli/azure/storage/account#az-storage-account-update) aan en geeft u de `--sku` para meter:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Een hand matige migratie uitvoeren naar ZRS, GZRS of RA-GZRS

Als u wilt wijzigen hoe gegevens in uw opslag account worden gerepliceerd in de primaire regio, door te verplaatsen van LRS naar ZRS of andersom, kunt u ervoor kiezen om een hand matige migratie uit te voeren. Een handmatige migratie biedt meer flexibiliteit dan een livemigratie. U bepaalt de timing van een hand matige migratie, dus gebruik deze optie als u wilt dat de migratie op een bepaalde datum wordt voltooid.

Wanneer u een hand matige migratie uitvoert van LRS naar ZRS in de primaire regio of andersom, kan het doel opslag account geografisch redundant zijn en kan het ook worden geconfigureerd voor lees toegang tot de secundaire regio. U kunt bijvoorbeeld een LRS-account migreren naar een GZRS-of RA-GZRS-account in één stap.

Een hand matige migratie kan leiden tot uitval tijd van toepassingen. Als voor uw toepassing hoge beschikbaarheid is vereist, biedt Microsoft u de mogelijkheid tot het uitvoeren van een livemigratie. Een livemigratie is een in-place migratie zonder uitvaltijd.

Met een hand matige migratie kopieert u de gegevens van uw bestaande opslag account naar een nieuw opslag account dat gebruikmaakt van ZRS in de primaire regio. Als u een hand matige migratie wilt uitvoeren, kunt u een van de volgende opties gebruiken:

- Gegevens kopiëren met behulp van een bestaand hulp programma, zoals AzCopy, een van de Azure Storage-client bibliotheken of een betrouwbaar hulp programma van derden.
- Als u bekend bent met Hadoop of HDInsight, kunt u zowel het bron opslag account als het account voor het account voor de doel opslag koppelen aan uw cluster. Parallelliseren vervolgens het proces voor het kopiëren van gegevens met een hulp programma zoals DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Een Live migratie aanvragen bij ZRS, GZRS of RA-GZRS

Als u uw opslag account wilt migreren van LRS naar ZRS in de primaire regio zonder uitval tijd van toepassingen, kunt u een Livemigratie van micro soft aanvragen. Als u wilt migreren van LRS naar GZRS of RA-GZRS, moet u eerst overschakelen op GRS of RA-GRS en vervolgens een Livemigratie aanvragen. Op dezelfde manier kunt u een Livemigratie aanvragen van GRS of RA-GRS naar GZRS of RA-GZRS. Als u wilt migreren van GRS of RA-GRS naar ZRS, gaat u eerst naar LRS en vraagt u een Livemigratie aan.

Tijdens een Livemigratie kunt u toegang krijgen tot gegevens in uw opslag account zonder verlies van duurzaamheid of Beschik baarheid. De SLA van Azure Storage wordt onderhouden tijdens het migratie proces. Er is geen gegevens verlies gekoppeld aan een Livemigratie. Service-eind punten, toegangs sleutels, hand tekeningen voor gedeelde toegang en andere account opties blijven ongewijzigd na de migratie.

ZRS biedt alleen ondersteuning voor v2-accounts voor algemeen gebruik. Zorg er dus voor dat u uw opslag account bijwerkt voordat u een aanvraag voor een Livemigratie naar ZRS verzendt. Zie [Upgraden naar een algemeen v2-opslagaccount](storage-account-upgrade.md) voor meer informatie. Een opslag account moet gegevens bevatten die via livemigratie moeten worden gemigreerd.

Livemigratie wordt alleen ondersteund voor opslag accounts die gebruikmaken van de replicatie van LRS of GRS. Als uw account gebruikmaakt van RA-GRS, moet u eerst het replicatie type van uw account wijzigen in LRS of GRS voordat u doorgaat. Deze intermediair-stap verwijdert het secundaire alleen-lezen eindpunt dat door RA-GRS vóór de migratie wordt gegeven.

Microsoft verwerkt uw aanvraag voor livemigratie onmiddellijk, maar er is geen garantie wanneer een livemigratie wordt voltooid. Als u uw gegevens vóór een bepaalde datum naar ZRS wilt migreren, wordt u aangeraden een handmatige migratie uit te voeren. In het algemeen geldt dat hoe meer gegevens u voor uw account hebt, hoe langer het duurt om die gegevens te migreren.

U moet een hand matige migratie uitvoeren als:

- U wilt uw gegevens migreren naar een ZRS-opslag account dat zich in een andere regio dan het bron account bevindt.
- Uw opslag account is een Premium-pagina-BLOB of een blok-BLOB-account.
- U wilt gegevens migreren van ZRS naar LRS, GRS of RA-GRS.
- Uw opslag account bevat gegevens in de laag van het archief.

U kunt Live migratie aanvragen via de [ondersteunings portal van Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Selecteer in de portal het opslag account dat u wilt converteren naar ZRS.

1. Selecteer een **nieuwe ondersteunings aanvraag**.
2. Voltooi de **basis beginselen** op basis van uw account gegevens: 
    - **Probleem type**: Selecteer **Technical**.
    - **Service**: Selecteer **Mijn services** en **beheer van opslag accounts**.
    - **Resource**: Selecteer de resource die u wilt converteren naar ZRS.
3. Selecteer **Volgende**.
4. Geef de volgende waarden op voor het **probleem** gedeelte:
    - **Ernst**: behoud de standaard waarde in.
    - **Probleem type**: **gegevens migratie** selecteren.
    - **Categorie**: Selecteer **migreren naar ZRS**.
    - **Titel**: Typ een beschrijvende titel, bijvoorbeeld ZRS- **account migratie**.
    - **Details**: Typ meer details in het vak **Details** , bijvoorbeeld ik wil migreren naar ZRS vanuit [LRS, GRS] in de \_ \_ regio.
5. Selecteer **Volgende**.
6. Controleer of de contact gegevens juist zijn op de Blade **contact gegevens** .
7. Selecteer **Maken**.

Een ondersteunings medewerker neemt contact met u op en geeft u hulp die u nodig hebt.

> [!NOTE]
> Premium-bestands shares (FileStorage-accounts) zijn alleen beschikbaar voor LRS en ZRS.
>
> GZRS-opslag accounts bieden momenteel geen ondersteuning voor de archief laag. Zie [Azure Blob-opslag: dynamische, koele en archief toegangs lagen](../blobs/storage-blob-storage-tiers.md) voor meer informatie.
>
> Managed disks zijn alleen beschikbaar voor LRS en kunnen niet worden gemigreerd naar ZRS. U kunt moment opnamen en installatie kopieën opslaan voor Standard SSD Managed disks op standaard HDD-opslag en [kiezen tussen opties voor LRS en ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Zie [Introduction to Azure Managed disks](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)(Engelstalig) voor meer informatie over de integratie met beschikbaarheids sets.

## <a name="switch-from-zrs-classic"></a>Overschakelen van klassieke ZRS

> [!IMPORTANT]
> Micro soft zal ZRS Classic-accounts op 31 maart 2021 terugboeken en migreren. Meer informatie vindt u voor ZRS Classic-klanten vóór afschaffing.
>
> Nadat ZRS algemeen beschikbaar wordt in een bepaalde regio, kunnen klanten geen ZRS klassieke accounts meer maken van de Azure Portal in die regio. Het gebruik van micro soft power shell en Azure CLI voor het maken van ZRS klassieke accounts is een optie totdat ZRS Classic is afgeschaft. Zie [Azure Storage redundantie](storage-redundancy.md)voor meer informatie over waar ZRS beschikbaar is.

ZRS Classic repliceert asynchroon gegevens tussen data centers binnen een tot twee regio's. Gerepliceerde gegevens zijn mogelijk niet beschikbaar tenzij micro soft failover naar de secundaire initieert. Een klassiek ZRS-account kan niet worden geconverteerd naar of van LRS, GRS of RA-GRS. Klassieke ZRS-accounts bieden ook geen ondersteuning voor metrische gegevens of logboek registratie.

ZRS Classic is alleen beschikbaar voor **blok-blobs** in opslag accounts voor algemeen gebruik (GPv1). Zie [overzicht van Azure Storage-account](storage-account-overview.md)voor meer informatie over opslag accounts.

Gebruik een van de volgende hulpprogram ma's: ZRS, Azure Storage Explorer, Power shell of Azure CLI om de ZRS-account gegevens hand matig te migreren naar of van een klassieke LRS-, GRS-, RA-GRS-of AZCOPY-account. U kunt ook uw eigen migratie oplossing bouwen met een van de Azure Storage-client bibliotheken.

U kunt ook uw klassieke ZRS-opslag account upgraden naar ZRS met behulp van de Azure Portal, Power shell of Azure CLI in regio's waar ZRS beschikbaar is.

# <a name="portal"></a>[Portal](#tab/portal)

Als u een upgrade wilt uitvoeren naar ZRS in de Azure Portal, gaat u naar de **configuratie** -instellingen van het account en kiest u **upgrade**:

![ZRS Classic upgraden naar ZRS in de portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u wilt bijwerken naar ZRS met behulp van Power shell, roept u de volgende opdracht aan:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een upgrade wilt uitvoeren naar ZRS met behulp van Azure CLI, roept u de volgende opdracht aan:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>De kosten voor het wijzigen van de manier waarop gegevens worden gerepliceerd

De kosten voor het wijzigen van de manier waarop gegevens worden gerepliceerd, zijn afhankelijk van het pad naar de conversie. Volg de rang orde van minst naar het duurste Azure Storage redundantie aanbiedingen zijn LRS, ZRS, GRS, RA-GRS, GZRS en RA-GZRS.

Als u bijvoorbeeld *van* LRS naar een ander type replicatie gaat, worden er extra kosten in rekening gebracht, omdat u overstapt naar een geavanceerd redundantie niveau. Als u migreert *naar* GRS of Ra-GRS, worden er kosten in rekening gebracht voor de uitgaande band breedte, omdat uw gegevens (in de primaire regio) worden gerepliceerd naar uw externe secundaire regio. Deze kosten zijn tijdens de eerste Setup eenmalige kosten. Nadat de gegevens zijn gekopieerd, worden er geen verdere migratie kosten in rekening gebracht. Zie [Azure Storage-pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de bandbreedte kosten.

Als u uw opslag account migreert van GRS naar LRS, zijn er geen extra kosten, maar worden uw gerepliceerde gegevens verwijderd van de secundaire locatie.

> [!IMPORTANT]
> Als u uw opslag account migreert van RA-GRS naar GRS of LRS, wordt dat account gefactureerd als RA-GRS voor een extra periode van 30 dagen na de datum waarop deze is geconverteerd.

## <a name="see-also"></a>Zie ook

- [Azure Storage-redundantie](storage-redundancy.md)
- [De eigenschap van de laatste synchronisatie tijd voor een opslag account controleren](last-sync-time-get.md)
- [Geo-redundantie gebruiken om Maxi maal beschik bare toepassingen te ontwerpen](geo-redundant-design.md)
