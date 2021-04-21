---
title: Wijzigen hoe een opslagaccount wordt gerepliceerd
titleSuffix: Azure Storage
description: Meer informatie over het wijzigen van de manier waarop gegevens in een bestaand opslagaccount worden gerepliceerd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb8bbf852803df53c43cef90bd2229bfcddd60d4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766183"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Wijzigen hoe een opslagaccount wordt gerepliceerd

Azure Storage slaat altijd meerdere kopieën van uw gegevens op, zodat deze worden beschermd tegen geplande en ongeplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk- of stroomstoringen en grote natuurrampen. Redundantie zorgt ervoor dat uw opslagaccount voldoet aan de [Sla (Service Level Agreement)](https://azure.microsoft.com/support/legal/sla/storage/) voor Azure Storage zelfs bij storingen.

Azure Storage biedt de volgende typen replicatie:

- Lokaal redundante opslag (LRS)
- Zone-redundante opslag (ZRS)
- Geografisch redundante opslag (GRS) of geografisch redundante opslag met leestoegang (RA-GRS)
- Geografisch zone-redundante opslag (GZRS) of geografisch zone-redundante opslag met leestoegang (RA-GZRS)

Zie redundantie voor een overzicht van [Azure Storage opties.](storage-redundancy.md)

## <a name="switch-between-types-of-replication"></a>Wisselen tussen replicatietypen

U kunt een opslagaccount van het ene type replicatie naar een ander type overstappen, maar sommige scenario's zijn eenvoudiger dan andere. Als u geo-replicatie of leestoegang tot de secundaire regio wilt toevoegen of verwijderen, kunt u de Azure Portal, PowerShell of Azure CLI gebruiken om de replicatie-instelling bij te werken. Als u echter wilt wijzigen hoe gegevens worden gerepliceerd in de primaire regio, door over te gaan van LRS naar ZRS of vice versa, moet u een handmatige migratie uitvoeren.

De volgende tabel bevat een overzicht van hoe u kunt overschakelen van elk type replicatie naar een ander:

| Schakelen | ... naar LRS | ... naar GRS/RA-GRS | ... naar ZRS | ... naar GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... van LRS</b> | N.v.t. | Gebruik Azure Portal, PowerShell of CLI om de replicatie-instelling<sup>1,2 te wijzigen</sup> | Een handmatige migratie uitvoeren <br /><br /> OF <br /><br /> Een livemigratie aanvragen | Een handmatige migratie uitvoeren <br /><br /> OF <br /><br /> Schakel eerst over naar GRS/RA-GRS en vraag vervolgens een livemigratie<sup>aan 1</sup> |
| <b>... van GRS/RA-GRS</b> | Gebruik Azure Portal, PowerShell of CLI om de replicatie-instelling te wijzigen | N.v.t. | Een handmatige migratie uitvoeren <br /><br /> OF <br /><br /> Schakel eerst over naar LRS en vraag vervolgens een livemigratie aan | Een handmatige migratie uitvoeren <br /><br /> OF <br /><br /> Een livemigratie aanvragen |
| <b>... van ZRS</b> | Een handmatige migratie uitvoeren | Een handmatige migratie uitvoeren | N.v.t. | Een livemigratie aanvragen |
| <b>... van GZRS/RA-GZRS</b> | Een handmatige migratie uitvoeren | Een handmatige migratie uitvoeren | Gebruik Azure Portal, PowerShell of CLI om de replicatie-instelling te wijzigen | N.v.t. |

<sup>1</sup> Er worden een een keer kosten in rekening voor het egress-gedrag in rekening brengen.<br />
<sup>2</sup> Migratie van LRS naar GRS wordt niet ondersteund als het opslagaccount blobs in de archieflaag bevat.<br />
<sup>3</sup> Conversie van ZRS naar GZRS/RA-GZRS of omgekeerd wordt niet ondersteund in de volgende regio's: US - oost 2, US - oost, Europa - west.

> [!CAUTION]
> Als u een [account-failover](storage-disaster-recovery-guidance.md) hebt uitgevoerd voor uw (RA-)GRS- of (RA-)GZRS-account, is het account lokaal redundant (LRS) in de nieuwe primaire regio na de failover. Livemigratie naar ZRS of GZRS voor een LRS-account als gevolg van een failover wordt niet ondersteund. Dit geldt zelfs in het geval van zogenaamde failbackbewerkingen. Als u bijvoorbeeld een account-failover van RA-GZRS naar de LRS in de secundaire regio wilt uitvoeren en deze vervolgens opnieuw configureert naar RA-GRS en een andere account-failover naar de oorspronkelijke primaire regio wilt uitvoeren, kunt u geen contact opnemen met de ondersteuning voor de oorspronkelijke livemigratie naar RA-GZRS in de primaire regio. In plaats daarvan moet u een handmatige migratie uitvoeren naar ZRS of GZRS.

## <a name="change-the-replication-setting"></a>De replicatie-instelling wijzigen

U kunt de Azure Portal, PowerShell of Azure CLI gebruiken om de replicatie-instelling voor een opslagaccount te wijzigen, zolang u niet wijzigt hoe gegevens worden gerepliceerd in de primaire regio. Als u migreert van LRS in de primaire regio naar ZRS in de primaire regio of omgekeerd, moet u een handmatige migratie of een livemigratie uitvoeren.

Het wijzigen van de manier waarop uw opslagaccount wordt gerepliceerd, leidt niet tot wisselende tijd voor uw toepassingen.

# <a name="portal"></a>[Portal](#tab/portal)

Als u de redundantieoptie voor uw opslagaccount in de Azure Portal, volgt u deze stappen:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **instelling** Configuratie.
1. Werk de instelling **Replicatie** bij.

![Schermopname die laat zien hoe u de replicatieoptie wijzigt in de portal](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de redundantieoptie voor uw opslagaccount wilt wijzigen met PowerShell, roept u de [opdracht Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan en geeft u de `-SkuName` parameter op:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de redundantieoptie voor uw opslagaccount wilt wijzigen met Azure CLI, roept u de [opdracht az storage account update](/cli/azure/storage/account#az_storage_account_update) aan en geeft u de parameter `--sku` op:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Een handmatige migratie uitvoeren naar ZRS, GZRS of RA-GZRS

Als u wilt wijzigen hoe gegevens in uw opslagaccount worden gerepliceerd in de primaire regio, door over te gaan van LRS naar ZRS of vice versa, kunt u ervoor kiezen om een handmatige migratie uit te voeren. Een handmatige migratie biedt meer flexibiliteit dan een livemigratie. U kunt de timing van een handmatige migratie bepalen, dus gebruik deze optie als u wilt dat de migratie op een bepaalde datum is voltooid.

Wanneer u een handmatige migratie van LRS naar ZRS in de primaire regio of omgekeerd wilt uitvoeren, kan het doelopslagaccount geografisch redundant zijn en kan het ook worden geconfigureerd voor leestoegang tot de secundaire regio. U kunt bijvoorbeeld in één stap een LRS-account migreren naar een GZRS- of RA-GZRS-account.

Een handmatige migratie kan downtime van toepassingen tot gevolg hebben. Als voor uw toepassing hoge beschikbaarheid is vereist, biedt Microsoft u de mogelijkheid tot het uitvoeren van een livemigratie. Een livemigratie is een in-place migratie zonder uitvaltijd.

Met een handmatige migratie kopieert u de gegevens van uw bestaande opslagaccount naar een nieuw opslagaccount dat gebruikmaakt van ZRS in de primaire regio. Als u een handmatige migratie wilt uitvoeren, kunt u een van de volgende opties gebruiken:

- Kopieer gegevens met behulp van een bestaand hulpprogramma, zoals AzCopy, een van de Azure Storage-clientbibliotheken of een betrouwbaar hulpprogramma van derden.
- Als u bekend bent met Hadoop of HDInsight, kunt u zowel het bronopslagaccount als het doelopslagaccount aan uw cluster koppelen. Parallelliseer vervolgens het gegevenskopieerproces met een hulpprogramma zoals DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Een livemigratie aanvragen naar ZRS, GZRS of RA-GZRS

Als u uw opslagaccount zonder uitvaltijd van de toepassing wilt migreren van LRS naar ZRS in de primaire regio, kunt u een livemigratie van Microsoft aanvragen. Als u wilt migreren van LRS naar GZRS of RA-GZRS, schakelt u eerst over naar GRS of RA-GRS en vraagt u vervolgens een livemigratie aan. Op dezelfde manier kunt u een livemigratie aanvragen van GRS of RA-GRS naar GZRS of RA-GZRS. Als u wilt migreren van GRS of RA-GRS naar ZRS, schakelt u eerst over naar LRS en vraagt u vervolgens een livemigratie aan.

Tijdens een livemigratie hebt u zonder verlies van duurzaamheid of beschikbaarheid toegang tot gegevens in uw opslagaccount. De Azure Storage SLA wordt onderhouden tijdens het migratieproces. Er is geen gegevensverlies gekoppeld aan een livemigratie. Service-eindpunten, toegangssleutels, handtekeningen voor gedeelde toegang en andere accountopties blijven na de migratie ongewijzigd.

ZRS ondersteunt alleen v2-accounts voor algemeen gebruik, dus zorg ervoor dat u uw opslagaccount bij werkt voordat u een aanvraag voor een livemigratie naar ZRS indient. Zie [Upgraden naar een algemeen v2-opslagaccount](storage-account-upgrade.md) voor meer informatie. Een opslagaccount moet gegevens bevatten die moeten worden gemigreerd via livemigratie.

Livemigratie wordt alleen ondersteund voor opslagaccounts die gebruikmaken van LRS- of GRS-replicatie. Als uw account RA-GRS gebruikt, moet u eerst het replicatietype van uw account wijzigen in LRS of GRS voordat u doorgaat. Met deze tussenliggende stap wordt het secundaire alleen-lezen eindpunt verwijderd dat vóór de migratie is geleverd door RA-GRS.

Microsoft verwerkt uw aanvraag voor livemigratie onmiddellijk, maar er is geen garantie wanneer een livemigratie wordt voltooid. Als u uw gegevens vóór een bepaalde datum naar ZRS wilt migreren, wordt u aangeraden een handmatige migratie uit te voeren. In het algemeen geldt dat hoe meer gegevens u voor uw account hebt, hoe langer het duurt om die gegevens te migreren.

U moet een handmatige migratie uitvoeren als:

- U wilt uw gegevens migreren naar een ZRS-opslagaccount dat zich in een andere regio bevindt dan het bronaccount.
- Uw opslagaccount is een Premium-pagina-blob- of blok-blob-account.
- U wilt gegevens migreren van ZRS naar LRS, GRS of RA-GRS.
- Uw opslagaccount bevat gegevens in de archieflaag.

U kunt livemigratie aanvragen via Ondersteuning voor Azure [portal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 

> [!IMPORTANT]
> Als u meer dan één opslagaccount wilt migreren, maakt u één ondersteuningsticket en geeft u de namen op van de accounts die u wilt converteren op het **tabblad Details.**

Volg deze stappen om een livemigratie aan te vragen:

1. Navigeer Azure Portal de pagina naar een opslagaccount dat u wilt migreren.
1. Selecteer **onder Ondersteuning en probleemoplossing** de optie Nieuwe **ondersteuningsaanvraag.**
1. Vul het **tabblad Basisinformatie** in op basis van uw accountgegevens:
    - **Type probleem:** selecteer **Technisch.**
    - **Service:** selecteer **Mijn services** en vervolgens **Opslagaccountbeheer.**
    - **Resource:** selecteer een opslagaccount dat u wilt migreren. Als u meerdere opslagaccounts moet opgeven, kunt u dit doen in de **sectie Details.**
    - **Probleemtype:** kies **Gegevensmigratie.**
    - **Subtype probleem:** kies **Migreren naar ZRS, GZRS of RA-GZRS.**

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Schermopname die laat zien hoe u een livemigratie kunt aanvragen - tabblad Basisinformatie":::

1. Selecteer **Next**. Op het **tabblad Oplossingen** kunt u controleren of uw opslagaccounts in aanmerking komen voor migratie.
1. Selecteer **Next**. Als u meer dan één opslagaccount wilt migreren, geeft u op het tabblad **Details** de naam voor elk account op, gescheiden door puntkomma's.

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Schermopname die laat zien hoe u een livemigratie aanvraagt - tabblad Details":::

1. Vul de aanvullende vereiste gegevens in op **het tabblad Details** en selecteer beoordelen en maken **om** uw ondersteuningsticket te controleren en in te dienen. Een ondersteuningspersoon neemt contact met u op om hulp te bieden die u mogelijk nodig hebt.

> [!NOTE]
> Premium-bestands shares (FileStorage-accounts) zijn alleen beschikbaar voor LRS en ZRS.
>
> GZRS-opslagaccounts bieden momenteel geen ondersteuning voor de archieflaag. Zie [Azure Blob-opslag: hot-, cool- en archieftoegangslagen](../blobs/storage-blob-storage-tiers.md) voor meer informatie.
>
> Beheerde schijven zijn alleen beschikbaar voor LRS en kunnen niet worden gemigreerd naar ZRS. U kunt momentopnamen en afbeeldingen voor standaard beheerde SSD-schijven opslaan in standard HDD-opslag en kiezen [tussen LRS- en ZRS-opties.](https://azure.microsoft.com/pricing/details/managed-disks/) Zie Inleiding tot Azure Managed Disks voor meer informatie over integratie met [beschikbaarheidssets.](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)

## <a name="switch-from-zrs-classic"></a>Overstappen van ZRS Classic

> [!IMPORTANT]
> Op 31 maart 2021 worden ZRS Classic-accounts afgeschaft en gemigreerd. Meer details worden vóór de afschaffing verstrekt aan ZRS Classic-klanten.
>
> Nadat ZRS algemeen beschikbaar is in een bepaalde regio, kunnen klanten geen ZRS Classic-accounts meer maken op Azure Portal in die regio. Het gebruik van Microsoft PowerShell en Azure CLI om klassieke ZRS-accounts te maken is een optie totdat ZRS Classic wordt afgeschaft. Zie redundantie voor meer informatie over [waar ZRS Azure Storage beschikbaar is.](storage-redundancy.md)

Met de klassieke ZRS-service worden gegevens asynchroon gerepliceerd tussen datacenters binnen één tot twee regio's. Gerepliceerde gegevens zijn mogelijk niet beschikbaar, tenzij Microsoft een failover naar de secundaire replica initieert. Een klassiek ZRS-account kan niet worden geconverteerd naar of van LRS, GRS of RA-GRS. ZRS Classic-accounts bieden ook geen ondersteuning voor metrische gegevens of logboekregistratie.

ZRS Classic is alleen beschikbaar voor **blok-blobs** in algemeen V1-opslagaccounts (GPv1). Zie Overzicht van Azure-opslagaccounts voor meer [informatie over opslagaccounts.](storage-account-overview.md)

Als u ZRS-accountgegevens handmatig wilt migreren naar of van een LRS-, GRS-, RA-GRS- of ZRS Classic-account, gebruikt u een van de volgende hulpprogramma's: AzCopy, Azure Storage Explorer, PowerShell of Azure CLI. U kunt ook uw eigen migratieoplossing bouwen met een van de Azure Storage clientbibliotheken.

U kunt ook uw klassieke ZRS-opslagaccount upgraden naar ZRS met behulp van de Azure Portal, PowerShell of Azure CLI in regio's waar ZRS beschikbaar is.

# <a name="portal"></a>[Portal](#tab/portal)

Als u wilt upgraden naar ZRS in Azure Portal, gaat u naar de **configuratie-instellingen** van het account en kiest u **Upgrade uitvoeren:**

![ZRS Classic upgraden naar ZRS in de portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u wilt upgraden naar ZRS met behulp van PowerShell, roept u de volgende opdracht aan:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt upgraden naar ZRS met behulp van Azure CLI, roept u de volgende opdracht aan:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Kosten voor het wijzigen van de manier waarop gegevens worden gerepliceerd

De kosten voor het wijzigen van de manier waarop gegevens worden gerepliceerd, zijn afhankelijk van uw conversiepad. De aanbieding voor redundantie van de minst tot de duurste Azure Storage zijn LRS, ZRS, GRS, RA-GRS, GZRS en RA-GZRS.

Als u *bijvoorbeeld* van LRS naar een ander type replicatie gaat, worden er extra kosten in rekening gebracht omdat u naar een geavanceerder redundantieniveau overstapt. Als u *naar* GRS of RA-GRS migreert, worden er kosten in rekening voor de bandbreedte voor het verwijderen van gegevens in rekening brengen omdat uw gegevens (in uw primaire regio) worden gerepliceerd naar uw externe secundaire regio. Deze kosten zijn een een keer bij de eerste installatie. Nadat de gegevens zijn gekopieerd, worden er geen verdere migratiekosten meer in rekening gebracht. Zie de pagina Prijzen voor Azure Storage [meer informatie over bandbreedtekosten.](https://azure.microsoft.com/pricing/details/storage/blobs/)

Als u uw opslagaccount migreert van GRS naar LRS, zijn er geen extra kosten, maar uw gerepliceerde gegevens worden verwijderd van de secundaire locatie.

> [!IMPORTANT]
> Als u uw opslagaccount migreert van RA-GRS naar GRS of LRS, wordt dat account nog 30 dagen na de datum waarop het is geconverteerd gefactureerd als RA-GRS.

## <a name="see-also"></a>Zie ook

- [Azure Storage-redundantie](storage-redundancy.md)
- [Controleer de eigenschap Laatste synchronisatietijd voor een opslagaccount](last-sync-time-get.md)
- [Geo-redundantie gebruiken om toepassingen met hoge beschikbare gegevens te ontwerpen](geo-redundant-design.md)
