---
title: Overzicht van opslagaccounts
titleSuffix: Azure Storage
description: Meer informatie over de verschillende typen opslag accounts in Azure Storage. Bekijk de naamgeving van accounts, prestatie lagen, toegangs lagen, redundantie, versleuteling, eind punten en meer.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5cf43310c68c8446b9465a39d85f84c8273a68d8
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051221"
---
# <a name="storage-account-overview"></a>Overzicht van opslagaccounts

Een Azure-opslag account bevat al uw Azure Storage gegevens objecten: blobs, bestanden, wacht rijen, tabellen en schijven. Het opslag account biedt een unieke naam ruimte voor uw Azure Storage gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Gegevens in uw Azure Storage-account zijn duurzaam en Maxi maal beschikbaar, veilig en zeer schaalbaar.

Zie [Een opslagaccount maken](storage-account-create.md) voor meer informatie over het maken van een Azure-opslagaccount.

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

Azure Storage biedt verschillende soorten opslagaccounts. Elk type ondersteunt verschillende functies en heeft een eigen prijsmodel. Neem deze verschillen in overweging voordat u een opslagaccount maakt om het type account te kiezen dat het best bij uw toepassingen past. De typen opslagaccounts zijn:

- **Algemeen v2-accounts**: Eenvoudig opslagaccounttype voor blobs, bestanden, wachtrijen en tabellen. Aanbevolen voor de meeste scenario's die gebruikmaken van Azure Storage.
- **Algemeen v1-accounts**: Verouderd accounttype voor blobs, bestanden, wachtrijen en tabellen. Gebruik, indien mogelijk, algemeen v2-accounts.
- **BlockBlobStorage-accounts**: Opslagaccounts met Premium-prestatiekenmerken voor blok-blobs en toevoeg-blobs. Aanbevolen voor scenario's met hoge transactiesnelheden of scenario's die kleinere objecten gebruiken of een consistente lage opslaglatentie vereisen.
- **FileStorage-accounts**: Opslagaccounts voor alleen bestanden met Premium-prestatie kenmerken. Aanbevolen voor toepassingen voor ondernemingen of high performance-prestaties.
- **BlobStorage-accounts**: Verouderde opslagaccounts voor alleen blobs. Gebruik, indien mogelijk, algemeen v2-accounts.

In de volgende tabel worden de typen opslag accounts, de services die worden ondersteund en de ondersteunde implementatie modellen voor elk account type beschreven:

| Type opslagaccount | Ondersteunde services | Redundantie opties | Implementatie model<sup>1</sup> |
|--|--|--|--|
| Algemeen v2 | BLOB, bestand, wachtrij, tabel, schijf en Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Resource Manager |
| Algemeen v1 | Blob, bestand, wachtrij, tabel en schijf | LRS, GRS, RA-GRS | Resource Manager, klassiek |
| BlockBlobStorage | Blob (alleen blok-blobs en toevoeg-blobs) | LRS, ZRS<sup>3</sup> | Resource Manager |
| FileStorage | Alleen bestanden | LRS, ZRS<sup>3</sup> | Resource Manager |
| BlobStorage | Blob (alleen blok-blobs en toevoeg-blobs) | LRS, GRS, RA-GRS | Resource Manager |

<sup>1</sup>Het wordt aanbevolen het Azure Resource Manager-implementatiemodel te gebruiken. Opslagaccounts die gebruikmaken van het klassieke implementatiemodel, kunnen in sommige locaties nog steeds worden gemaakt; bestaande klassieke accounts blijven ondersteund worden. Zie voor meer informatie [Azure Resource Manager versus klassieke implementatie: inzicht in implementatiemodellen en de status van uw resources](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup> Azure Data Lake Storage Gen2 is een set mogelijkheden die is toegewezen aan big data Analytics, gebouwd op Azure Blob-opslag. Data Lake Storage Gen2 wordt alleen ondersteund voor algemeen v2-opslagaccounts waarvoor hiërarchische naamruimte is ingeschakeld. Zie [Inleiding tot Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) voor meer informatie over Data Lake Storage Gen2.

<sup>3</sup> Zone-redundante opslag (ZRS) en geo-zone-redundante opslag (GZRS/RA-GZRS) zijn alleen beschikbaar voor standaard v2-, BlockBlobStorage-en FileStorage-accounts voor algemeen gebruik in bepaalde regio's. Zie [Azure Storage-redundantie](storage-redundancy.md) voor meer informatie over opties voor Azure Storage-redundantie.

### <a name="storage-account-redundancy"></a>Redundantie van opslag account

De volgende redundantieopties zijn beschikbaar voor een opslagaccount:

- **Lokaal redundante opslag (LRS)**: een eenvoudige, voordelige redundantie strategie. Gegevens worden synchroon drie keer binnen één fysieke locatie in de primaire regio gekopieerd.
- **Zone-redundante opslag (ZRS)**: redundantie voor scenario's die hoge Beschik baarheid vereisen. Gegevens worden synchroon gekopieerd naar drie Azure-beschikbaarheidszones in de primaire regio.
- **Geografisch redundante opslag (GRS)**: Kruis regionale redundantie om te beschermen tegen regionale storingen. Gegevens worden synchroon drie keer in de primaire regio gekopieerd en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch redundante opslag met leestoegang (RA-GRS) in.
- **Geo-zone-redundante opslag (GZRS)**: redundantie voor scenario's waarbij hoge Beschik baarheid en maximale duurzaamheid zijn vereist. Gegevens worden synchroon gekopieerd naar drie Azure-beschikbaarheidszones in de primaire regio en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch zone-redundante opslag met lees toegang (RA-GZRS) in.

Zie [Azure Storage-redundantie](storage-redundancy.md) voor meer informatie over opties voor redundantie in Azure Storage.

### <a name="general-purpose-v2-accounts"></a>V2-accounts voor algemeen gebruik

V2-opslag accounts voor algemeen gebruik ondersteunen de nieuwste functies van Azure Storage en bevatten alle functionaliteit van v1-en Blob Storage-accounts voor algemeen gebruik. Bij v2-accounts voor algemeen gebruik worden de laagste capaciteits prijzen per GB voor Azure Storage en de prijzen voor de toonaangevende trans acties geleverd. V2-opslag accounts voor algemeen gebruik ondersteunen deze Azure Storage services:

- Blobs (alle typen: blok keren, toevoegen, pagina)
- Data Lake Gen2
- Bestanden
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
- Bestanden
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

Afhankelijk van het type opslag account dat u maakt, kunt u kiezen tussen Standard-en Premium-prestatie lagen. De volgende tabel bevat een overzicht van de prestatie lagen die beschikbaar zijn voor welk type opslag account.

| Type opslagaccount | Ondersteunde prestatielagen |
|--|--|
| Algemeen v2 | Standard, Premium<sup>1</sup> |
| Algemeen v1 | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standard |

<sup>1</sup> Premium-prestaties voor algemeen gebruik v2 en algemeen v1-accounts zijn alleen beschikbaar voor schijven en pagina-blobs. Premium-prestaties voor blok- en toevoeg-blobs zijn alleen beschikbaar voor BlockBlobStorage-accounts. Premium-prestaties voor bestanden zijn alleen beschikbaar voor FileStorage-accounts.

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

In de volgende tabel ziet u welke toegangs lagen er beschikbaar zijn voor blobs in elk type opslag account.

| Type opslagaccount | Ondersteunde toegangslagen |
|--|--|
| Algemeen v2 | Warm, koud, archief<sup>1</sup> |
| Algemeen v1 | N.v.t. |
| BlockBlobStorage | N.v.t. |
| FileStorage | N.v.t. |
| BlobStorage | Warm, koud, archief<sup>1</sup> |

<sup>1</sup> archief opslag en lagen op BLOB-niveau bieden alleen ondersteuning voor blok-blobs. De archieflaag is alleen beschikbaar op het niveau van een afzonderlijke blob, niet op opslagaccountniveau. Zie [toegangs lagen voor Azure Blob Storage-hot, cool en Archive](../blobs/storage-blob-storage-tiers.md)voor meer informatie.

> [!IMPORTANT]
> Als u de toegangs laag voor een bestaand opslag account of BLOB wijzigt, kunnen er extra kosten in rekening worden gebracht. Zie [facturering van opslag accounts](#storage-account-billing)voor meer informatie.

## <a name="encryption"></a>Versleuteling

Alle gegevens in uw opslag account worden versleuteld aan de kant van de service. Zie [Azure Storage-service versleuteling voor Data-at-rest](storage-service-encryption.md)voor meer informatie over versleuteling.

## <a name="storage-account-endpoints"></a>Eindpunten van opslagaccount

Een opslagaccount biedt een unieke naamruimte in Azure voor uw gegevens. Elk object dat u in Azure Storage opslaat, heeft een adres dat uw unieke accountnaam bevat. De combinatie van de accountnaam en het service-eindpunt voor Azure Storage vormen de eindpunten voor uw opslagaccount.

De volgende tabel geeft een lijst van de eind punten voor elk van de Azure Storage services.

| Opslag service | Eindpunt |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

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

Azure Storage facturen op basis van het gebruik van uw opslag account. Alle objecten in een opslagaccount worden samen gefactureerd als een groep. Opslagkosten worden berekend op basis van de volgende factoren:

- **Regio** verwijst naar de geografische regio waarin uw account is gebaseerd.
- **Accounttype** verwijst naar het type opslagaccount dat u gebruikt.
- **Toegangslaag** verwijst naar het gegevensgebruikpatroon dat u hebt opgegeven voor het v2- of blob-opslagaccount voor algemeen gebruik.
- De **capaciteit** verwijst naar het gedeelte van het opslag account dat u gebruikt om gegevens op te slaan.
- **Replicatie** bepaalt hoeveel kopieën van uw gegevens gelijktijdig worden bewaard en op welke locaties.
- **Transacties** verwijst naar alle lees- en schrijfbewerkingen naar Azure Storage.
- **Uitgaande gegevens** verwijst naar alle gegevens die buiten een Azure-regio zijn overgedragen. Wanneer de gegevens in uw opslagaccount worden geopend door een toepassing die niet wordt uitgevoerd in dezelfde regio, wordt er een bedrag in rekening gebracht voor uitgaande gegevens. Zie [Wat is een Azure-resourcegroep?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) voor informatie over het gebruik van resourcegroepen om uw gegevens en services in dezelfde regio te groeperen om de uitstaande kosten te beperken.

Op de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) vindt u gedetailleerde prijsinformatie op basis van accounttype, opslagcapaciteit, replicatie en transacties. In [Prijsinformatie over gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/) vindt u gedetailleerde informatie over de prijzen voor uitgaande gegevens. Met de [Prijscalculator van Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) kunt u een schatting maken van uw kosten.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](storage-account-create.md)
- [Een blok-blob-opslagaccount maken](../blobs/storage-blob-create-account-block-blob.md)
- [Upgraden naar een V2-opslagaccount voor algemeen gebruik](storage-account-upgrade.md)
- [Een verwijderd opslagaccount herstellen](storage-account-recover.md)