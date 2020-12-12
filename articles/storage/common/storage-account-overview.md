---
title: Overzicht van opslagaccounts
titleSuffix: Azure Storage
description: Lees een overzicht van opslag accounts in Azure Storage. Bekijk de naamgeving van accounts, prestatie lagen, toegangs lagen, redundantie, versleuteling, eind punten en meer.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 2c9c4cd643e2e4b89f9a7d8f44a6569d0dde2b37
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357378"
---
# <a name="storage-account-overview"></a>Overzicht van opslagaccounts

Een Azure-opslag account bevat al uw Azure Storage gegevens objecten: blobs, bestanden, wacht rijen, tabellen en schijven. Het opslag account biedt een unieke naam ruimte voor uw Azure Storage gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Gegevens in uw Azure Storage-account zijn duurzaam en Maxi maal beschikbaar, veilig en zeer schaalbaar.

Zie [Een opslagaccount maken](storage-account-create.md) voor meer informatie over het maken van een Azure-opslagaccount.

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>V2-accounts voor algemeen gebruik

V2-opslag accounts voor algemeen gebruik ondersteunen de nieuwste functies van Azure Storage en bevatten alle functionaliteit van v1-en Blob Storage-accounts voor algemeen gebruik. Bij v2-accounts voor algemeen gebruik worden de laagste capaciteits prijzen per GB voor Azure Storage en de prijzen voor de toonaangevende trans acties geleverd. V2-opslag accounts voor algemeen gebruik ondersteunen deze Azure Storage services:

- Blobs (alle typen: blok keren, toevoegen, pagina)
- Data Lake Gen2
- Files
- Disks
- Wachtrijen
- Tabellen

> [!NOTE]
> Micro soft raadt u aan om voor de meeste scenario's een v2-opslag account voor algemeen gebruik te gebruiken. U kunt eenvoudig een algemeen v1-of Blob Storage-account bijwerken naar een v2-account voor algemeen gebruik zonder uitval tijd en hoeft u geen gegevens te kopiëren.
>
> Zie voor meer informatie over het uitvoeren van een upgrade naar een v2-account voor algemeen gebruik [upgraden naar een algemeen v2-opslag account](storage-account-upgrade.md).

V2-opslag accounts voor algemeen gebruik bieden meerdere toegangs lagen voor het opslaan van gegevens op basis van uw gebruiks patronen. Zie [toegangs lagen voor blok-BLOB-gegevens](#access-tiers-for-block-blob-data)voor meer informatie.

### <a name="general-purpose-v1-accounts"></a>V1-accounts voor algemeen gebruik

V1-opslag accounts voor algemeen gebruik bieden toegang tot alle Azure Storage-services, maar hebben mogelijk niet de nieuwste functies of de laagste prijzen per gigabyte. V1-opslag accounts voor algemeen gebruik ondersteunen deze Azure Storage services:

- Blobs (alle typen)
- Files
- Disks
- Wachtrijen
- Tabellen

Micro soft adviseert v2-accounts voor algemeen gebruik voor de meeste scenario's. U kunt voor deze scenario's algemene v1-accounts gebruiken:

- Voor uw toepassingen is het klassieke Azure-implementatie model vereist. V2-accounts voor algemeen gebruik en Blob Storage-accounts ondersteunen alleen het implementatie model van Azure Resource Manager.

- Uw toepassingen zijn transactie intensief of gebruiken aanzienlijke band breedte met geo-replicatie, maar vereisen geen grote capaciteit. In dit geval is algemeen-doel v1 de meest economische keuze.

- U gebruikt een versie van de [Storage services-rest API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) die ouder is dan 2014-02-14 of een client bibliotheek met een lagere versie dan 4. x. U kunt de toepassing niet bijwerken.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage-accounts

Een BlockBlobStorage-account is een gespecialiseerd opslag account in de laag Premium-prestaties voor het opslaan van ongestructureerde object gegevens als blok-blobs of toevoeg-blobs. Vergeleken met v2-en BlobStorage-accounts voor algemeen gebruik bieden BlockBlobStorage-accounts een lage, consistente latentie en hogere transactie tarieven.

BlockBlobStorage-accounts ondersteunen momenteel geen lagen op dynamische, koele of archief toegangs lagen. Dit type opslag account biedt geen ondersteuning voor pagina-blobs,-tabellen of-wacht rijen.

### <a name="filestorage-accounts"></a>FileStorage-accounts

Een FileStorage-account is een gespecialiseerd opslag account dat wordt gebruikt voor het opslaan en maken van Premium-bestands shares. Dit type opslag account ondersteunt bestanden, maar geen blok-blobs, toevoeg-blobs, pagina-blobs, tabellen of wacht rijen.

FileStorage-accounts bieden unieke prestatie gerichte kenmerken, zoals IOPS-bursting. Zie de sectie [opslag lagen voor bestands shares](../files/storage-files-planning.md#storage-tiers) in de hand leiding voor het plannen van bestanden voor meer informatie over deze kenmerken.

## <a name="naming-storage-accounts"></a>Naamgeving van opslag accounts

Neem de volgende regels in acht als u het opslagaccount een naam geeft:

- Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.
- De naam van uw opslagaccount moet uniek zijn binnen Azure. Een opslagaccount kan niet dezelfde naam hebben als een ander opslagaccount.

## <a name="performance-tiers"></a>Prestatielagen

Afhankelijk van het type opslag account dat u maakt, kunt u kiezen tussen Standard-en Premium-prestatie lagen.

### <a name="general-purpose-storage-accounts"></a>Opslagaccounts voor algemeen gebruik

Opslag accounts voor algemeen gebruik kunnen worden geconfigureerd voor een van de volgende prestatie lagen:

- Een standaard prestatie niveau voor het opslaan van blobs, bestanden, tabellen, wacht rijen en schijven van virtuele machines van Azure. Zie [schaalbaarheids doelen voor standaard opslag accounts](scalability-targets-standard-account.md)voor meer informatie over schaalbaarheids doelen voor standaard opslag accounts.
- Een Premium-prestatie niveau voor het opslaan van niet-beheerde schijven van virtuele machines. Micro soft raadt aan gebruik te maken van beheerde schijven met virtuele Azure-machines in plaats van niet-beheerde schijven. Zie [schaalbaarheids doelen voor Premium-pagina-Blob Storage-accounts](../blobs/scalability-targets-premium-page-blobs.md)voor meer informatie over de schaalbaarheids doelen voor de laag Premium-prestaties.

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage-opslag accounts

BlockBlobStorage-opslag accounts bieden een Premium-prestatie-laag voor het opslaan van blok-blobs en toevoeg-blobs. Zie [schaalbaarheids doelen voor Premium Block Blob Storage-accounts](../blobs/scalability-targets-premium-block-blobs.md)voor meer informatie.

### <a name="filestorage-storage-accounts"></a>FileStorage-opslag accounts

FileStorage-opslag accounts bieden een Premium-prestatie niveau voor Azure-bestands shares. Zie [Azure files schaal baarheid en prestatie doelen](../files/storage-files-scale-targets.md)voor meer informatie.

## <a name="access-tiers-for-block-blob-data"></a>Toegangs lagen voor blok-BLOB-gegevens

Azure Storage biedt verschillende opties voor het openen van blok-BLOB-gegevens op basis van gebruiks patronen. Elke toegangs categorie in Azure Storage is geoptimaliseerd voor een bepaald patroon van gegevens gebruik. Door de juiste toegangs laag voor uw behoeften te selecteren, kunt u uw blok-blobgegevens op de voordeligste manier opslaan.

De beschik bare toegangs lagen zijn:

- De **warme** Access-laag. Deze laag is geoptimaliseerd voor veelvuldige toegang tot objecten in het opslag account. Het verkrijgen van toegang tot gegevens in de warme laag is de meest rendabel, terwijl de opslag kosten hoger zijn. Nieuwe opslag accounts worden standaard in de warme laag gemaakt.
- De laag van de **coolbar** . Deze laag is geoptimaliseerd voor het opslaan van grote hoeveel heden gegevens die niet regel matig worden geopend en die gedurende ten minste 30 dagen worden opgeslagen. Het opslaan van gegevens in de cool-laag is rendabeler, maar de toegang tot die gegevens kan duurder zijn dan de toegang tot gegevens in de warme laag.
- De **archief** laag. Deze laag is alleen beschikbaar voor afzonderlijke blok-blobs. De archief laag is geoptimaliseerd voor gegevens die een aantal uur van het ophalen van de latentie kunnen verdragen en die ten minste 180 dagen in de archief laag blijven. De archief laag is de meest rendabele optie voor het opslaan van gegevens. Het is echter wel duurder om toegang te krijgen tot gegevens in de warme of coole lagen.

Als er een wijziging is in het gebruiks patroon van uw gegevens, kunt u op elk gewenst moment scha kelen tussen deze toegangs lagen. Zie [Azure Blob Storage: warme, cool en archief toegangs lagen](../blobs/storage-blob-storage-tiers.md)voor meer informatie over toegangs lagen.

> [!IMPORTANT]
> Als u de toegangs laag voor een bestaand opslag account of BLOB wijzigt, kunnen er extra kosten in rekening worden gebracht. Zie de sectie facturering van het [opslag account](#storage-account-billing)voor meer informatie.

## <a name="redundancy"></a>Redundantie

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Versleuteling

Alle gegevens in uw opslag account worden versleuteld aan de kant van de service. Zie [Azure Storage-service versleuteling voor Data-at-rest](storage-service-encryption.md)voor meer informatie over versleuteling.

## <a name="storage-account-endpoints"></a>Eindpunten van opslagaccount

Een opslagaccount biedt een unieke naamruimte in Azure voor uw gegevens. Elk object dat u in Azure Storage opslaat, heeft een adres dat uw unieke accountnaam bevat. De combinatie van de accountnaam en het service-eindpunt voor Azure Storage vormen de eindpunten voor uw opslagaccount.

Als uw opslag account voor algemeen gebruik bijvoorbeeld *mystorageaccount* heet, zijn de standaard eindpunten voor dat account:

- Blob-opslag: `https://*mystorageaccount*.blob.core.windows.net`
- Tabel opslag: `https://*mystorageaccount*.table.core.windows.net`
- Wachtrij opslag: `https://*mystorageaccount*.queue.core.windows.net`
- Azure Files: `https://*mystorageaccount*.file.core.windows.net`
- Azure Data Lake Storage Gen2: `https://*mystorageaccount*.dfs.core.windows.net` (maakt gebruik [van het ABFS-stuur programma dat specifiek is geoptimaliseerd voor Big Data](../blobs/data-lake-storage-introduction.md#key-features-of-data-lake-storage-gen2).)

> [!NOTE]
> Blok-Blob en Blob Storage-accounts bieden alleen het Blob service-eind punt.

Maak de URL voor toegang tot een object in een opslag account door de locatie van het object in het opslag account toe te voegen aan het eind punt. Een blobadres kan bijvoorbeeld de volgende indeling hebben: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

U kunt uw opslag account ook configureren voor het gebruik van een aangepast domein voor blobs. Zie [een aangepaste domein naam configureren voor uw Azure Storage-account](../blobs/storage-custom-domain-name.md)voor meer informatie.  

## <a name="control-access-to-account-data"></a>Toegang tot account gegevens beheren

De gegevens in uw account zijn standaard alleen beschikbaar voor u, de eigenaar van het account. U bepaalt wie toegang heeft tot uw gegevens en welke machtigingen ze hebben.

Elke aanvraag die aan uw opslag account wordt gedaan, moet worden geautoriseerd. Op het niveau van de service moet de aanvraag een geldige *autorisatie* -header bevatten. Deze header bevat met name alle informatie die nodig is voor de service om de aanvraag te valideren voordat deze wordt uitgevoerd.

U kunt met behulp van de volgende benaderingen toegang tot de gegevens in uw opslag account verlenen:

- **Azure Active Directory:** Gebruik de referenties van Azure Active Directory (Azure AD) om een gebruiker, groep of een andere identiteit te verifiëren voor toegang tot Blob-en wachtrij gegevens. Als de verificatie van een identiteit is geslaagd, retourneert Azure AD een token dat wordt gebruikt voor het autoriseren van de aanvraag bij Azure Blob-opslag of-wachtrij opslag. Zie [toegang tot Azure Storage verifiëren met behulp van Azure Active Directory](storage-auth-aad.md)voor meer informatie.
- **Gedeelde sleutel autorisatie:** Gebruik de toegangs sleutel voor uw opslag account om een connection string te maken dat uw toepassing tijdens runtime gebruikt om toegang te krijgen tot Azure Storage. De waarden in de connection string worden gebruikt om de *autorisatie* -header te maken die wordt door gegeven aan Azure Storage. Zie [Azure Storage-verbindings reeksen configureren](storage-configure-connection-string.md)voor meer informatie.
- **Shared Access Signature:** Een Shared Access Signature (SAS) is een token dat gedelegeerde toegang tot resources in uw opslag account toestaat. Het SAS-token Kapselt alle benodigde informatie in om een aanvraag voor het Azure Storage van de URL te autoriseren. Wanneer u een SAS maakt, kunt u opgeven welke machtigingen de SAS verleent aan een bron en het interval waarover de machtigingen geldig zijn. Een SAS-token kan worden ondertekend met Azure AD-referenties of met een gedeelde sleutel. Zie [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)voor meer informatie.

> [!NOTE]
> Het verifiëren van gebruikers of toepassingen die gebruikmaken van Azure AD-referenties biedt een superieure beveiliging en gebruiks gemak ten opzichte van andere autorisatie methoden. U kunt de verificatie van de gedeelde sleutel blijven gebruiken met uw toepassingen, maar met Azure AD wordt de nood zaak om uw account toegangs sleutel op te slaan met uw code. U kunt ook door gaan met het gebruik van Shared Access signatures (SAS) om nauw keurige toegang tot resources in uw opslag account te verlenen, maar Azure AD biedt soort gelijke mogelijkheden zonder de behoefte aan het beheer van SAS-tokens of een probleem bij het intrekken van een aangetaste SAS.
>
> Micro soft raadt u aan gebruik te maken van Azure AD-autorisatie voor uw Azure Storage Blob-en wachtrij toepassingen wanneer dat mogelijk is.

## <a name="copying-data-into-a-storage-account"></a>Gegevens kopiëren naar een opslag account

Micro soft biedt hulpprogram ma's en bibliotheken voor het importeren van uw gegevens van on-premises opslag apparaten of Cloud-opslag providers van derden. Welke oplossing u gebruikt, is afhankelijk van het aantal gegevens dat u wilt overbrengen.

Wanneer u een upgrade uitvoert naar een v2-account voor algemeen gebruik van een v1-of Blob-opslag account voor algemeen gebruik, worden uw gegevens automatisch gemigreerd. Micro soft adviseert dit traject voor het upgraden van uw account. Als u echter besluit gegevens te verplaatsen van een algemeen v1-account naar een Blob Storage-account, migreert u uw gegevens hand matig met behulp van de hulpprogram ma's en bibliotheken die hieronder worden beschreven.

### <a name="azcopy"></a>AzCopy

AzCopy is een Windows-opdrachtregelprogramma dat is  ontworpen voor het high-performance kopiëren van gegevens van en naar Azure Storage. U kunt AzCopy gebruiken om gegevens te kopiëren naar een Blob Storage-account vanuit een bestaand opslag account voor algemeen gebruik of om gegevens van on-premises opslag apparaten te uploaden. Zie [gegevens overdragen met het AzCopy-hulp programma van Command-Line](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie.

### <a name="data-movement-library"></a>Bibliotheek voor gegevensverplaatsing

De Azure Storage-bibliotheek voor gegevensverplaatsing voor .NET is gebaseerd op het basisframework voor gegevensverplaatsing voor AzCopy. De bibliotheek is ontworpen voor high-performance, betrouwbare en eenvoudige gegevensoverdracht vergelijkbaar met AzCopy. U kunt de gegevens verplaatsings bibliotheek gebruiken om de AzCopy-functies systeem eigen te benutten. Zie [Azure Storage-bibliotheek voor gegevens verplaatsing voor .net](https://github.com/Azure/azure-storage-net-data-movement) voor meer informatie

### <a name="rest-api-or-client-library"></a>REST-API of clientbibliotheek

U kunt een aangepaste toepassing maken om uw gegevens te migreren van een v1-opslag account voor algemeen gebruik naar een Blob Storage-account. Gebruik een van de Azure-client bibliotheken of de Azure Storage services-REST API. Azure Storage biedt uitgebreide clientbibliotheken voor meerdere talen en platforms, zoals  .NET, Java, C++, Node.JS, PHP, Ruby en Python. De clientbibliotheken bieden geavanceerde mogelijkheden, zoals pogingslogica, logboekregistratie en parallelle uploads. U kunt ook rechtstreeks met de REST API ontwikkelen. Deze kan worden aangeroepen in elke taal waarin HTTP-/HTTPS-verzoeken kunnen worden gemaakt.

Zie [Azure Storage Services rest API Reference](/rest/api/storageservices/)(Engelstalig) voor meer informatie over de Azure Storage rest API.

> [!IMPORTANT]
> Blobs die aan de clientzijde zijn versleuteld, bevatten versleutelingsgerelateerde metagegevens die samen met de blob zijn opgeslagen. Als u een blob met versleuteling aan de clientzijde kopieert, zorg er dan voor dat bij het kopiëren de blobmetagegevens behouden blijven, en dan met name de versleutelingsgerelateerde metagegevens. Als u een blob kopieert zonder versleutelingsgerelateerde metagegevens, kan de inhoud van de blob niet meer worden opgehaald. Zie [Azure Storage-versleuteling aan de clientzijde](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over versleutelingsgerelateerde metagegevens.

## <a name="storage-account-billing"></a>Facturering voor opslagaccounts

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](storage-account-create.md)
- [Een blok-blob-opslagaccount maken](../blobs/storage-blob-create-account-block-blob.md)
- [Upgraden naar een V2-opslagaccount voor algemeen gebruik](storage-account-upgrade.md)
- [Een verwijderd opslagaccount herstellen](storage-account-recover.md)