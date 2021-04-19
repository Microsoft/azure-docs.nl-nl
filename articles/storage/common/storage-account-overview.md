---
title: Overzicht van opslagaccounts
titleSuffix: Azure Storage
description: Meer informatie over de verschillende typen opslagaccounts in Azure Storage. Bekijk accountnaamgeving, prestatielagen, toegangslagen, redundantie, versleuteling, eindpunten en meer.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714196"
---
# <a name="storage-account-overview"></a>Overzicht van opslagaccounts

Een Azure-opslagaccount bevat al uw Azure Storage: blobs, bestanden, wachtrijen, tabellen en schijven. Het opslagaccount biedt een unieke naamruimte voor uw Azure Storage gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Gegevens in uw Azure-opslagaccount zijn duurzaam en zeer beschikbaar, veilig en zeer schaalbaar.

Zie [Een opslagaccount maken](storage-account-create.md) voor meer informatie over het maken van een Azure-opslagaccount.

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

Azure Storage biedt verschillende soorten opslagaccounts. Elk type ondersteunt verschillende functies en heeft een eigen prijsmodel. Neem deze verschillen in overweging voordat u een opslagaccount maakt om het type account te kiezen dat het best bij uw toepassingen past.

In de volgende tabel worden de typen opslagaccounts beschreven die door Microsoft worden aanbevolen voor de meeste scenario's:

| Type opslagaccount | Ondersteunde services | Redundantieopties | Implementatiemodel | Gebruik |
|--|--|--|--|--|
| Standaard algemeen gebruik v2 | Blob, File, Queue, Table en Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Basistype opslagaccount voor blobs, bestanden, wachtrijen en tabellen. Aanbevolen voor de meeste scenario's die gebruikmaken van Azure Storage. |
| Premium blok-blobs<sup>4</sup> | Alleen blok-blobs | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Opslagaccounts met premium-prestatiekenmerken voor blok-blobs en append-blobs. Aanbevolen voor scenario's met hoge transactiesnelheden of scenario's die kleinere objecten gebruiken of een consistente lage opslaglatentie vereisen.<br />[Meer informatie...](../blobs/storage-blob-performance-tiers.md) |
| Premium-bestands shares<sup>4</sup> | Alleen bestands shares | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Opslagaccounts met alleen bestanden met premium-prestatiekenmerken. Aanbevolen voor toepassingen voor ondernemingen of high performance-prestaties.<br />[Meer informatie...](../files/storage-files-planning.md#management-concepts) |
| Premium-pagina-blobs<sup>4</sup> | Alleen pagina-blobs | LRS | Resource Manager<sup>3</sup> | Premium Storage-accounttype alleen voor pagina-blobs.<br />[Meer informatie...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage is een set mogelijkheden die is toegewezen aan big data analytics, gebouwd op Azure Blob Storage. Data Lake Storage wordt alleen ondersteund voor V2-opslagaccounts voor algemeen gebruik met een hiërarchische naamruimte ingeschakeld. Zie Inleiding tot Data Lake Storage Gen2 voor meer [informatie over Data Lake Storage Gen2.](../blobs/data-lake-storage-introduction.md)

<sup>2</sup> Zone-redundante opslag (ZRS) en geografisch zone-redundante opslag (GZRS/RA-GZRS) zijn alleen beschikbaar voor standaard accounts voor algemeen gebruik v2, premium blok-blob en Premium-bestandsdeelaccounts in bepaalde regio's. Zie [Azure Storage-redundantie](storage-redundancy.md) voor meer informatie over opties voor Azure Storage-redundantie.

<sup>3</sup> Azure Resource Manager is het aanbevolen implementatiemodel voor Azure-resources, waaronder opslagaccounts. Zie overzicht van Resource Manager [voor meer informatie.](../../azure-resource-manager/management/overview.md)

<sup>4</sup> Opslagaccounts in een Premium-prestatielaag maken gebruik van SOLID State Disks (SSD's) voor lage latentie en hoge doorvoer.

Verouderde opslagaccounts worden ook ondersteund. Zie Verouderde opslagaccounttypen [voor meer informatie.](#legacy-storage-account-types)

## <a name="storage-account-endpoints"></a>Eindpunten van opslagaccount

Een opslagaccount biedt een unieke naamruimte in Azure voor uw gegevens. Elk object dat u in Azure Storage opslaat, heeft een adres dat uw unieke accountnaam bevat. De combinatie van de accountnaam en het service-eindpunt voor Azure Storage vormen de eindpunten voor uw opslagaccount.

Neem de volgende regels in acht als u het opslagaccount een naam geeft:

- Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.
- De naam van uw opslagaccount moet uniek zijn binnen Azure. Een opslagaccount kan niet dezelfde naam hebben als een ander opslagaccount.

De volgende tabel bevat de indeling van het eindpunt voor elk van de Azure Storage services.

| Opslagservice | Eindpunt |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

Bouw de URL voor toegang tot een object in een opslagaccount door de locatie van het object in het opslagaccount toe te staan aan het eindpunt. De URL voor een blob is bijvoorbeeld vergelijkbaar met:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

U kunt uw opslagaccount ook configureren voor het gebruik van een aangepast domein voor blobs. Zie Configure a [custom domain name for your Azure Storage account (Een aangepaste domeinnaam configureren voor uw Azure Storage-account) voor meer informatie.](../blobs/storage-custom-domain-name.md)  

## <a name="migrating-a-storage-account"></a>Een opslagaccount migreren

De volgende tabel bevat een overzicht en bevat richtlijnen voor het verplaatsen, upgraden of migreren van een opslagaccount:

| Migratiescenario | Details |
|--|--|
| Een opslagaccount verplaatsen naar een ander abonnement | Azure Resource Manager biedt opties voor het verplaatsen van een resource naar een ander abonnement. Zie Resources verplaatsen naar een nieuwe resourcegroep of een [nieuw abonnement voor meer informatie.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Een opslagaccount verplaatsen naar een andere resourcegroep | Azure Resource Manager biedt opties voor het verplaatsen van een resource naar een andere resourcegroep. Zie Resources verplaatsen naar een nieuwe resourcegroep of een [nieuw abonnement voor meer informatie.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Een opslagaccount verplaatsen naar een andere regio | Als u een opslagaccount wilt verplaatsen, maak dan een kopie van uw opslagaccount in een andere regio. Verplaats vervolgens uw gegevens naar dat account met behulp van AzCopy of een ander hulpprogramma naar keuze. Zie Move an Azure Storage account to another region (Een account [Azure Storage verplaatsen naar een andere regio) voor meer informatie.](storage-account-move.md) |
| Upgraden naar een v2-opslagaccount voor algemeen gebruik | U kunt een algemeen v1-opslagaccount of Blob Storage-account upgraden naar een v2-account voor algemeen gebruik. Houd er rekening mee dat deze actie niet ongedaan kan worden gemaakt. Zie [Upgraden naar een algemeen v2-opslagaccount](storage-account-upgrade.md) voor meer informatie. |
| Een klassiek opslagaccount migreren naar Azure Resource Manager | Het Azure Resource Manager implementatiemodel is beter dan het klassieke implementatiemodel op het gebied van functionaliteit, schaalbaarheid en beveiliging. Zie Migratie van opslagaccounts [in](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) Door platform ondersteunde migratie van **IaaS-resources** van klassiek naar Azure Resource Manager Azure Resource Manager voor meer informatie over het migreren van een klassiek opslagaccount naar Azure Resource Manager. |

## <a name="transferring-data-into-a-storage-account"></a>Gegevens overdragen naar een opslagaccount

Microsoft biedt services en hulpprogramma's voor het importeren van uw gegevens van on-premises opslagapparaten of externe cloudopslagproviders. Welke oplossing u gebruikt, is afhankelijk van de hoeveelheid gegevens die u wilt overdragen. Zie overzicht van Azure Storage [migratie voor meer informatie.](storage-migration-overview.md)

## <a name="storage-account-encryption"></a>Opslagaccountversleuteling

Alle gegevens in uw opslagaccount worden automatisch versleuteld aan de servicezijde. Zie Versleuteling voor [data-at-rest](storage-service-encryption.md)voor Azure Storage informatie over versleuteling en sleutelbeheer.

## <a name="storage-account-billing"></a>Facturering voor opslagaccounts

Azure Storage op basis van het gebruik van uw opslagaccount. Alle objecten in een opslagaccount worden samen gefactureerd als een groep. Opslagkosten worden berekend op basis van de volgende factoren:

- **Regio** verwijst naar de geografische regio waarin uw account is gebaseerd.
- **Accounttype** verwijst naar het type opslagaccount dat u gebruikt.
- **Toegangslaag** verwijst naar het gegevensgebruikpatroon dat u hebt opgegeven voor het v2- of blob-opslagaccount voor algemeen gebruik.
- **Capaciteit** verwijst naar hoeveel van uw opslagaccounts u gebruikt voor het opslaan van gegevens.
- **Redundantie** bepaalt hoeveel kopieën van uw gegevens tegelijk worden bewaard en op welke locaties.
- **Transacties** verwijst naar alle lees- en schrijfbewerkingen naar Azure Storage.
- **Uitgaande gegevens** verwijst naar alle gegevens die buiten een Azure-regio zijn overgedragen. Wanneer de gegevens in uw opslagaccount worden geopend door een toepassing die niet wordt uitgevoerd in dezelfde regio, wordt er een bedrag in rekening gebracht voor uitgaande gegevens. Zie [Wat is een Azure-resourcegroep?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) voor informatie over het gebruik van resourcegroepen om uw gegevens en services in dezelfde regio te groeperen om de uitstaande kosten te beperken.

De [Azure Storage pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/) bevat gedetailleerde prijsinformatie op basis van accounttype, opslagcapaciteit, replicatie en transacties. De [prijsinformatie voor gegevensoverdracht bevat](https://azure.microsoft.com/pricing/details/data-transfers/) gedetailleerde prijsinformatie voor uitgaande gegevens. U kunt de [prijscalculator Azure Storage om](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) uw kosten te schatten.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Verouderde opslagaccounttypen

In de volgende tabel worden de typen verouderde opslagaccounts beschreven. Deze accounttypen worden niet aanbevolen door Microsoft, maar kunnen in bepaalde scenario's worden gebruikt:

| Type verouderd opslagaccount | Ondersteunde services | Redundantieopties | Implementatiemodel | Gebruik |
|--|--|--|--|--|
| Standaard algemeen gebruik v1 | Blob, File, Queue, Table en Data Lake Storage | LRS/GRS/RA-GRS | Resource Manager, klassiek | Accounts voor algemeen gebruik v1 hebben mogelijk niet de nieuwste functies of de laagste prijzen per gigabyte. Overweeg het gebruik voor deze scenario's:<br /><ul><li>Voor uw toepassingen is het klassieke Azure-implementatiemodel vereist.</li><li>Uw toepassingen zijn transactie-intensief of gebruiken aanzienlijke geo-replicatiebandbreedte, maar vereisen geen grote capaciteit. In dit geval is algemeen gebruik v1 mogelijk de voordeligste keuze.</li><li>U gebruikt een versie van de Azure Storage REST API die lager is dan 2014-02-14 of een clientbibliotheek met een versie lager dan 4.x, en u kunt uw toepassing niet upgraden.</li></ul> |
| Standard Blob Storage | Blob (alleen blok-blobs en toevoeg-blobs) | LRS/GRS/RA-GRS | Resource Manager | Microsoft raadt u aan om waar mogelijk standaard v2-accounts voor algemeen gebruik te gebruiken. |

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](storage-account-create.md)
- [Upgraden naar een v2-opslagaccount voor algemeen gebruik](storage-account-upgrade.md)
- [Een verwijderd opslagaccount herstellen](storage-account-recover.md)