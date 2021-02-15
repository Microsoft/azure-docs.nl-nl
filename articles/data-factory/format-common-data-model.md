---
title: Common Data Model-indeling
description: Gegevens transformeren met behulp van het meta gegevens systeem van common data model
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: makromer
ms.openlocfilehash: 45f5334ebee3365c17bfa52c8d47ed75b82bdfa1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387696"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Gemeen schappelijke gegevens model indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Het gegevens systeem van het common data model (CDM) maakt het mogelijk om gegevens en de betekenis ervan eenvoudig te delen tussen toepassingen en bedrijfs processen. Zie het overzicht van [common data model](/common-data-model/) voor meer informatie.

In Azure Data Factory kunnen gebruikers gegevens transformeren van CDM-entiteiten in zowel model.jsop als manifest formulier opgeslagen in [Azure data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) met toewijzing van gegevens stromen. U kunt gegevens in de CDM-indeling ook opvangen met een CDM-entiteits verwijzing waarmee uw gegevens in de gepartitioneerde mappen in de CSV-of Parquet worden ingedeeld. 

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Het common data model is beschikbaar als een [inline-gegevensset](data-flow-source.md#inline-datasets) in het toewijzen van gegevens stromen als een bron en een sink.

> [!NOTE]
> Bij het schrijven van CDM-entiteiten moet er al een bestaande CDM-entiteits definitie (meta gegevens schema) zijn gedefinieerd die als referentie kan worden gebruikt. Met de Sink van de ADF-gegevens stroom wordt dat bestand van de CDM-entiteit gelezen en wordt het schema in uw Sink voor veld toewijzing geïmporteerd.

### <a name="source-properties"></a>Bron eigenschappen

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een CDM-bron. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | Indeling moet `cdm` | ja | `cdm` | indeling |
| Meta gegevens indeling | Waar de entiteits verwijzing naar de gegevens zich bevindt. Als u CDM versie 1,0 gebruikt, kiest u Manifest. Als u vóór 1,0 een CDM-versie gebruikt, kiest u model.jsop. | Yes | `'manifest'` of `'model'` | manifestType |
| Hoofd locatie: container | Container naam van de map CDM | ja | Tekenreeks | System |
| Hoofd locatie: mappad | Locatie van de hoofdmap van de map CDM | ja | Tekenreeks | folderPath |
| Manifest bestand: pad naar entiteit | Mappad van de entiteit binnen de hoofdmap | nee | Tekenreeks | entityPath |
| Manifest bestand: manifest naam | De naam van het manifest bestand. Standaard waarde is ' default '  | Nee | Tekenreeks | manifestnaam |
| Filteren op laatst gewijzigd | Kiezen of bestanden moeten worden gefilterd op basis van het tijdstip waarop deze voor het laatst zijn gewijzigd | nee | Tijdstempel | modifiedAfter <br> modifiedBefore | 
| Gekoppelde schema service | De gekoppelde service waar de verzameling zich bevindt | Ja, als u een manifest gebruikt | `'adlsgen2'` of `'github'` | corpusStore | 
| Container voor entiteits verwijzing | Container verzameling is in | Ja, als u Manifest en verzameling in ADLS Gen2 gebruikt | Tekenreeks | adlsgen2_fileSystem |
| Opslag plaats voor entiteit verwijzing | Naam van de GitHub-opslagplaats | Ja, als u Manifest en verzameling in GitHub gebruikt | Tekenreeks | github_repository |
| Vertakking van entiteits verwijzing | GitHub-opslag plaats vertakking | Ja, als u Manifest en verzameling in GitHub gebruikt | Tekenreeks |  github_branch |
| Map verzameling | de hoofd locatie van de verzameling | Ja, als u een manifest gebruikt | Tekenreeks | corpusPath |
| Verzameling entiteit | Pad naar entiteits verwijzing | ja | Tekenreeks | vennootschap |
| Geen bestanden gevonden | Als deze eigenschap waar is, wordt er geen fout gegenereerd als er geen bestanden worden gevonden | nee | `true` of `false` | ignoreNoFilesFound |

Wanneer u ' entiteits verwijzing ' selecteert in de bron-en Sink-trans formaties, kunt u uit deze drie opties kiezen voor de locatie van de verwijzing naar de entiteit:

* Lokaal maakt gebruik van de entiteit die is gedefinieerd in het manifest bestand dat al wordt gebruikt door ADF
* Als u Aangepast kiest, wordt u gevraagd naar een bestand met een entiteits manifest dat verschilt van de ADF van het manifest bestand.
* Standard maakt gebruik van een verwijzing naar een entiteit van de standaard bibliotheek van CDM-entiteiten die worden onderhouden in ```Github``` .

### <a name="sink-settings"></a>Sink-instellingen

* Wijs naar het CDM-entiteits bestand met de definitie van de entiteit die u wilt schrijven.

![entiteits instellingen](media/data-flow/common-data-model-111.png "Referentie entiteit")

* Definieer het quotapad en de indeling van de uitvoer bestanden die u wilt gebruiken voor het schrijven van uw entiteiten.

![indeling van entiteit](media/data-flow/common-data-model-222.png "Indeling van entiteit")

* Stel de locatie van het uitvoer bestand en de locatie en naam van het manifest bestand in.

![CDM-locatie](media/data-flow/common-data-model-333.png "CDM-locatie")


#### <a name="import-schema"></a>Schema importeren

CDM is alleen beschikbaar als een inline-gegevensset en heeft standaard geen bijbehorend schema. Als u kolom meta gegevens wilt ophalen, klikt u op de knop **schema importeren** op het tabblad **projectie** . Hiermee kunt u verwijzen naar de kolom namen en gegevens typen die door de verzameling zijn opgegeven. Als u het schema wilt importeren, moet een foutopsporingssessie voor [gegevens stromen](concepts-data-flow-debug-mode.md) actief zijn en moet u een bestaand CDM-entiteits definitie bestand hebben om naar te verwijzen.

Wanneer u gegevens stroom kolommen aan entiteits eigenschappen in de Sink-trans formatie wilt toewijzen, klikt u op het tabblad toewijzing en selecteert u schema importeren. ADF leest de verwijzing naar de entiteit waarnaar u in uw Sink-opties wijst, zodat u kunt toewijzen aan het doel-CDM-schema.

![Instellingen voor CDM-Sink](media/data-flow/common-data-model-444.png "CDM-toewijzing")

> [!NOTE]
>  Wanneer u model.jsgebruikt voor het bron type dat afkomstig is van Power BI of het Power platform gegevens stromen, kan het zijn dat het fout verzameling pad is null of leeg is van de bron transformatie. Dit komt waarschijnlijk door het format teren van problemen met het pad van de partitie locatie in de model.jsin het bestand. Voer de volgende stappen uit om dit probleem op te lossen: 

1. Het model.jsbestand openen in een tekst editor
2. Zoek de partities. Locatie-eigenschap 
3. Wijzig ' blob.core.windows.net ' in ' dfs.core.windows.net '
4. De code ring '% 2F ' in de URL naar '/' oplossen
5. Als u gebruik wilt maken van ADF-gegevens stromen, moeten speciale tekens in het pad van het partitie bestand worden vervangen door alfanumerieke waarden, of overschakelen naar Synapse gegevens stromen

### <a name="cdm-source-data-flow-script-example"></a>Voor beeld van CDM-bron gegevensstroom script

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Eigenschappen van Sink

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een CDM-sink. U kunt deze eigenschappen bewerken op het tabblad **instellingen** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | Indeling moet `cdm` | ja | `cdm` | indeling |
| Hoofd locatie: container | Container naam van de map CDM | ja | Tekenreeks | System |
| Hoofd locatie: mappad | Locatie van de hoofdmap van de map CDM | ja | Tekenreeks | folderPath |
| Manifest bestand: pad naar entiteit | Mappad van de entiteit binnen de hoofdmap | nee | Tekenreeks | entityPath |
| Manifest bestand: manifest naam | De naam van het manifest bestand. Standaard waarde is ' default ' | Nee | Tekenreeks | manifestnaam |
| Gekoppelde schema service | De gekoppelde service waar de verzameling zich bevindt | ja | `'adlsgen2'` of `'github'` | corpusStore | 
| Container voor entiteits verwijzing | Container verzameling is in | Ja, als verzameling in ADLS Gen2 | Tekenreeks | adlsgen2_fileSystem |
| Opslag plaats voor entiteit verwijzing | Naam van de GitHub-opslagplaats | Ja, als verzameling in GitHub | Tekenreeks | github_repository |
| Vertakking van entiteits verwijzing | GitHub-opslag plaats vertakking | Ja, als verzameling in GitHub | Tekenreeks |  github_branch |
| Map verzameling | de hoofd locatie van de verzameling | ja | Tekenreeks | corpusPath |
| Verzameling entiteit | Pad naar entiteits verwijzing | ja | Tekenreeks | vennootschap |
| Pad partitioneren | Locatie waar de partitie wordt geschreven | nee | Tekenreeks | partitionPath |
| De map wissen | Als de doelmap vóór het schrijven is gewist | nee | `true` of `false` | afkappen |
| Type indeling | Kiezen om de Parquet-indeling op te geven | nee | `parquet` Indien opgegeven | subformat |
| Kolom scheidings teken | Als u naar DelimitedText schrijft, kolommen beperken | Ja, als u naar DelimitedText schrijft | Tekenreeks | columnDelimiter |
| Eerste rij als koptekst | Als u DelimitedText gebruikt, of de kolom namen worden toegevoegd als koptekst | nee | `true` of `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Script voor beeld van CDM Sink-gegevens stroom

Het gekoppelde gegevensstroom script is:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Volgende stappen

Maak een [bron transformatie](data-flow-source.md) in de toewijzing van gegevens stroom.
