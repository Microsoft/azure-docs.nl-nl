---
title: Verbinding maken met Azure Data Factory
description: In dit artikel wordt beschreven hoe u Azure Data Factory en Azure controle sfeer liggen verbindt om gegevens afkomst bij te houden.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/24/2021
ms.openlocfilehash: c9f2a21a1183637ec4648868cccd6f343b003f0c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026620"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Verbinding maken met Azure Data Factory en Azure controle sfeer liggen

In dit document worden de stappen beschreven die nodig zijn om een Azure Data Factory-account te verbinden met een Azure controle sfeer liggen-account om de gegevens afkomst bij te houden. Het document krijgt ook de details van het bereik van de dekking en ondersteunde afkomst-patronen.

## <a name="view-existing-data-factory-connections"></a>Bestaande Data Factory verbindingen weer geven

Meerdere Azure-gegevens fabrieken kunnen verbinding maken met één Azure controle sfeer liggen Data Catalog om afkomst-gegevens te pushen. Met de huidige limiet kunt u tien Data Factory accounts tegelijk vanuit het controle sfeer liggen-beheer centrum verbinden. Ga als volgt te werk om de lijst met Data Factory accounts weer te geven die zijn verbonden met uw controle sfeer liggen-Data Catalog:

1. Selecteer **beheer centrum** in het navigatie deel venster aan de linkerkant.
2. Selecteer bij **externe verbindingen** **Data Factory verbinding**.
3. De lijst met Data Factory verbindingen wordt weer gegeven.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Scherm opname met een data factory lijst met verbindingen." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Let op de verschillende waarden voor verbindings **status**:

    - **Verbonden**: de Data Factory is verbonden met de gegevens catalogus.
    - De **verbinding is verbroken**: de Data Factory heeft toegang tot de catalogus, maar is verbonden met een andere catalogus. Als gevolg hiervan worden gegevens afkomst niet automatisch gerapporteerd aan de catalogus.
    - **CannotAccess**: de huidige gebruiker heeft geen toegang tot de Data Factory, dus de verbindings status is onbekend.
 >[!Note]
 >Als u de Data Factory verbindingen wilt weer geven, moet u een van de controle sfeer liggen-rollen toewijzen:
 >- Inzender
 >- Eigenaar
 >- Lezer
 >- Beheerder van gebruikerstoegang

## <a name="create-new-data-factory-connection"></a>Nieuwe Data Factory verbinding maken

>[!Note]
>Als u de Data Factory verbindingen wilt toevoegen of verwijderen, moet u een van de controle sfeer liggen-rollen toewijzen:
>- Eigenaar
>- Beheerder van gebruikerstoegang
>
> Daarnaast moeten gebruikers de data factory ' eigenaar ' of ' Inzender ' zijn. 

Volg de onderstaande stappen om een bestaand Data Factory accounts te koppelen aan uw controle sfeer liggen-Data Catalog.

1. Selecteer **beheer centrum** in het navigatie deel venster aan de linkerkant.
2. Selecteer bij **externe verbindingen** **Data Factory verbinding**.
3. Selecteer op de pagina **Data Factory verbinding** de optie **Nieuw**.

4. Selecteer uw Data Factory-account in de lijst en selecteer **OK**. U kunt ook filteren op abonnements naam om uw lijst te beperken.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Data Factory verbinding maakt." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Sommige Data Factory instanties worden mogelijk uitgeschakeld als de data factory al is verbonden met het huidige controle sfeer liggen-account, of als de data factory geen beheerde identiteit heeft.

    Er wordt een waarschuwings bericht weer gegeven als een van de geselecteerde gegevens fabrieken al is verbonden met een ander controle sfeer liggen-account. Als u OK selecteert, wordt de Data Factory verbinding met het andere controle sfeer liggen-account verbroken. Er zijn geen aanvullende bevestigingen vereist.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Scherm opname met waarschuwing voor het verbreken van Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>We bieden nu ondersteuning voor het toevoegen van niet meer dan 10 gegevens fabrieken tegelijk. Als u meer dan 10 gegevens fabrieken tegelijk wilt toevoegen, kunt u een ondersteunings ticket indienen.

### <a name="how-does-the-authentication-work"></a>Hoe werkt de verificatie?

Wanneer een controle sfeer liggen-gebruiker een Data Factory registreert waartoe hij of zij toegang heeft, gebeurt het volgende in de back-end:

1. De **beheerde identiteit Data Factory** wordt toegevoegd aan de controle sfeer liggen RBAC-rol: **controle sfeer liggen data curator**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Scherm opname met Azure Data Factory MSI." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. De Data Factory-pijp lijn moet opnieuw worden uitgevoerd, zodat de afkomst-meta gegevens terug kunnen worden gepusht naar controle sfeer liggen.
3. Uitvoering na de Data Factory de meta gegevens worden gepusht naar controle sfeer liggen.

### <a name="remove-data-factory-connections"></a>data factory verbindingen verwijderen
Ga als volgt te werk om een data factory verbinding te verwijderen:

1. Selecteer op de pagina **Data Factory verbinding** de knop **verwijderen** naast een of meer Data Factory verbindingen.
2. Selecteer **bevestigen** in het pop-upvenster om de geselecteerde Data Factory verbindingen te verwijderen.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Scherm afbeelding die laat zien hoe u gegevens fabrieken selecteert om verbinding te verwijderen." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-integration-runtime-to-collect-lineage"></a>Een zelf-hostende Integration Runtime configureren voor het verzamelen van afkomst

Afkomst voor de Data Factory Kopieer activiteit is beschikbaar voor on-premises gegevens archieven zoals SQL-data bases. Als u een zelf-hostende Integration runtime uitvoert voor het verplaatsen van gegevens met Azure Data Factory en u afkomst wilt vastleggen in azure controle sfeer liggen, moet u ervoor zorgen dat de versie 5,0 of hoger is. Zie [een zelf-hostende Integration runtime maken en configureren](../data-factory/create-self-hosted-integration-runtime.md)voor meer informatie over zelf-hostende Integration runtime.

## <a name="supported-azure-data-factory-activities"></a>Ondersteunde Azure Data Factory activiteiten

Azure controle sfeer liggen legt runtime-afkomst vast van de volgende Azure Data Factory activiteiten:

- [Gegevens kopiëren](../data-factory/copy-activity-overview.md)
- [Gegevensstroom](../data-factory/concepts-data-flow-overview.md)
- [SSIS-pakket uitvoeren](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> Azure controle sfeer liggen daalt afkomst als de bron of het doel een niet-ondersteund gegevens opslag systeem gebruikt.

De integratie tussen Data Factory en controle sfeer liggen ondersteunt alleen een subset van de gegevens systemen die door Data Factory worden ondersteund, zoals beschreven in de volgende secties.

### <a name="data-factory-copy-activity-support"></a>Ondersteuning voor het kopiëren van activiteiten Data Factory

| Gegevensarchief | Ondersteund | 
| ------------------- | ------------------- | 
| Azure Blob Storage | Ja |
| Azure Cognitive Search | Ja | 
| Azure Cosmos DB (SQL-API) \* | Ja | 
| API van Azure Cosmos DB voor MongoDB \* | Ja |
| Azure Data Explorer \* | Ja | 
| Azure Data Lake Storage Gen1 | Ja | 
| Azure Data Lake Storage Gen2 | Ja | 
| Azure data base for Maria DB \* | Ja | 
| Azure Database for MySQL \* | Ja | 
| Azure Database for PostgreSQL \* | Ja |
| Azure File Storage | Ja | 
| Azure SQL Database \* | Ja | 
| Met Azure SQL beheerd exemplaar \* | Ja | 
| Azure Synapse Analytics \* | Ja | 
| Azure Table Storage | Ja |
| Amazon S3 | Ja | 
| Onderdelen \* | Ja | 
| SAP ECC \* | Ja |
| SAP-tabel | Ja |
| SQL Server \* | Ja | 
| Teradata \* | Ja |

*\* Azure controle sfeer liggen biedt momenteel geen ondersteuning voor een query of opgeslagen procedure voor afkomst of scans. Afkomst is beperkt tot tabel-en weergave bronnen.*

> [!Note]
> De functie afkomst heeft bepaalde prestatie overhead in Data Factory Kopieer activiteit. Voor degenen die data factory verbindingen in controle sfeer liggen in te stellen, kunt u controleren of bepaalde Kopieer taken langer duren. Meestal is de impact geen te verwaarlozen. Neem contact op met ondersteuning met een tijd vergelijking als de Kopieer taken aanzienlijk langer duren dan normaal.

#### <a name="known-limitations-on-copy-activity-lineage"></a>Bekende beperkingen voor kopieer activiteit afkomst

Als u de volgende functies van de Kopieer activiteit gebruikt, wordt de afkomst nog niet ondersteund:

- Gegevens naar Azure Data Lake Storage Gen1 kopiëren met een binaire indeling.
- Kopieer gegevens naar Azure Synapse Analytics met de poly base-of kopieer instructie.
- Compressie-instelling voor binaire, gescheiden tekst-, Excel-, JSON-en XML-bestanden.
- Opties voor de bron partitie voor Azure SQL Database, Azure SQL Managed instance, Azure Synapse Analytics, SQL Server en SAP Table.
- De detectie optie voor de bron partitie voor archieven op basis van bestanden.
- Kopieer gegevens naar Sink op basis van een bestand met de instelling maximum aantal rijen per bestand.
- Voeg extra kolommen toe tijdens het kopiëren.

In aanvulling op afkomst wordt het schema voor gegevensassets (weer gegeven in het tabblad Asset-> schema) gerapporteerd voor de volgende connectors:

- CSV-en Parquet-bestanden in Azure Blob, Azure File Storage, ADLS Gen1, ADLS Gen2 en Amazon S3
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed instance, Azure Synapse Analytics, SQL Server, Teradata

### <a name="data-factory-data-flow-support"></a>Ondersteuning voor Data Factory gegevens stroom

| Gegevensarchief | Ondersteund |
| ------------------- | ------------------- | 
| Azure Blob Storage | Ja |
| Azure Data Lake Storage Gen1 | Ja |
| Azure Data Lake Storage Gen2 | Ja |
| Azure SQL Database \* | Ja |
| Azure Synapse Analytics \* | Ja |

*\* Azure controle sfeer liggen biedt momenteel geen ondersteuning voor een query of opgeslagen procedure voor afkomst of scans. Afkomst is beperkt tot tabel-en weergave bronnen.*

### <a name="data-factory-execute-ssis-package-support"></a>Ondersteuning voor het uitvoeren van SSIS-pakketten Data Factory

| Gegevensarchief | Ondersteund |
| ------------------- | ------------------- |
| Azure Blob Storage | Ja |
| Azure Data Lake Storage Gen1 | Ja |
| Azure Data Lake Storage Gen2 | Ja |
| Azure File Storage | Ja |
| Azure SQL Database \* | Ja |
| Met Azure SQL beheerd exemplaar \*| Ja |
| Azure Synapse Analytics \* | Ja |
| SQL Server \* | Ja |

*\* Azure controle sfeer liggen biedt momenteel geen ondersteuning voor een query of opgeslagen procedure voor afkomst of scans. Afkomst is beperkt tot tabel-en weergave bronnen.*

> [!Note]
> Azure Data Lake Storage Gen2 is nu overal algemeen beschikbaar. We raden aan vandaag nog hierop over te stappen. Zie de [productpagina](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/) voor meer informatie.

## <a name="supported-lineage-patterns"></a>Ondersteunde afkomst-patronen

Er zijn verschillende patronen van afkomst die Azure controle sfeer liggen ondersteunt. De gegenereerde afkomst-gegevens zijn gebaseerd op het type bron en Sink dat is gebruikt in de Data Factory activiteiten. Hoewel Data Factory ondersteuning biedt voor meer dan 80 bron en sinks, ondersteunt Azure controle sfeer liggen alleen een subset, zoals wordt vermeld in [ondersteunde Azure Data Factory-activiteiten](#supported-azure-data-factory-activities).

Zie aan de [slag met afkomst](catalog-lineage-user-guide.md#get-started-with-lineage)om Data Factory te configureren voor het verzenden van afkomst-gegevens.

Enkele extra manieren om informatie te vinden in de weer gave afkomst zijn onder andere:

- Op het tabblad **afkomst** houdt u de muis aanwijzer op shapes om aanvullende informatie over het element in de tooltip te bekijken.
- Selecteer het knoop punt of de rand om het activa type te zien waarvan het deel uitmaakt of om te scha kelen tussen activa.
- Kolommen van een gegevensset worden weer gegeven aan de linkerkant van het tabblad **afkomst** . Zie [DataSet column afkomst](catalog-lineage-user-guide.md#dataset-column-lineage)voor meer informatie over afkomst op kolom niveau.

### <a name="data-lineage-for-11-operations"></a>Data afkomst voor 1:1-bewerkingen

Het meest voorkomende patroon voor het vastleggen van gegevens afkomst, is het verplaatsen van gegevens van één invoer gegevensset naar één uitvoer gegevensset, met een proces tussen.

Een voor beeld van dit patroon is het volgende:

- 1 bron/invoer: *klant* (SQL-tabel)
- 1 Sink/uitvoer: *Customer1.csv* (Azure-Blob)
- 1 proces: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory-Kopieer activiteit)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Scherm opname van de afkomst voor een-op-een Data Factory Kopieer bewerking." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Gegevens verplaatsing met 1:1 afkomst en Joker tekens ondersteunen

Een ander algemeen scenario voor het vastleggen van afkomst, gebruikt een Joker teken om bestanden te kopiëren van een enkele invoer gegevensset naar een enkele uitvoer gegevensset. Met het Joker teken kan de Kopieer activiteit worden vergeleken met meerdere bestanden voor het kopiëren met behulp van een gemeen schappelijk gedeelte van de bestands naam. Azure controle sfeer liggen legt afkomst op bestands niveau vast voor elk afzonderlijk bestand dat door de bijbehorende Kopieer activiteit wordt gekopieerd.

Een voor beeld van dit patroon is het volgende:

* Bron/invoer: *CustomerCall \* . csv* (ADLS Gen2 pad)
* Sink/output: *CustomerCall \* . CSV* (Azure Blob-bestand)
* 1 proces: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory-Kopieer activiteit)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Scherm afbeelding met afkomst voor een-op-een-Kopieer bewerking met ondersteuning voor joker tekens." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Verplaatsing van gegevens met n:1 afkomst

U kunt gegevens stroom activiteiten gebruiken om gegevens bewerkingen uit te voeren zoals samen voegen, toevoegen, enzovoort. U kunt meer dan één bron-gegevensset gebruiken om een doel gegevensset te maken. In dit voor beeld legt Azure controle sfeer liggen afkomst op bestands niveau vast voor afzonderlijke invoer bestanden naar een SQL-tabel die deel uitmaakt van een gegevens stroom activiteit.

Een voor beeld van dit patroon is het volgende:

* 2 bronnen/invoer: *Customer.csv*, *Sales. Parquet* (ADLS Gen2 pad)
* 1 Sink/uitvoer: *Bedrijfs gegevens* (Azure SQL-tabel)
* 1 proces: *DataFlowBlobsToSQL* (Data Factory gegevens stroom activiteit)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Scherm opname van de afkomst voor een n tot een D F-gegevens stroom bewerking." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Afkomst voor resource sets

Een resource set is een logisch object in de catalogus dat veel partitie bestanden in de onderliggende opslag voor stelt. Zie [informatie over resource sets](concept-resource-sets.md)voor meer informatie. Wanneer Azure controle sfeer liggen afkomst van de Azure Data Factory vastlegt, worden de regels toegepast voor het normaliseren van de afzonderlijke partitie bestanden en het maken van één logisch object.

In het volgende voor beeld wordt een Azure Data Lake Gen2-resourceset geproduceerd vanuit een Azure-Blob:

* 1 bron/invoer: *werk nemer \_management.csv* (Azure-Blob)
* 1 Sink/output: *werk nemer \_management.csv* (Azure data Lake gen 2)
* 1 proces: *CopyBlobToAdlsGen2 \_ RS* (Data Factory Kopieer activiteit)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Scherm opname met de afkomst voor een resourceset." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers handleiding voor catalogus afkomst](catalog-lineage-user-guide.md)
- [Koppeling naar de Azure-gegevens share voor afkomst](how-to-link-azure-data-share.md)
