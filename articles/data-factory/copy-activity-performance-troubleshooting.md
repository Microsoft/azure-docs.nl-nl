---
title: Prestaties van de Kopieer activiteit oplossen
description: Meer informatie over het oplossen van problemen met de prestaties van Kopieer activiteiten in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/07/2021
ms.openlocfilehash: 203782ef2d95cd2b47082f630fa12531a110d49e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933926"
---
# <a name="troubleshoot-copy-activity-performance"></a>Prestaties van de Kopieer activiteit oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u problemen met de Kopieer activiteit prestaties oplost in Azure Data Factory. 

Nadat u een Kopieer activiteit hebt uitgevoerd, kunt u de statistieken voor resultaten en prestaties van de uitvoer in de weer gave [controle activiteiten kopiëren](copy-activity-monitoring.md) verzamelen. Hier volgt een voorbeeld.

![Details van de uitvoering van de Kopieer activiteit controleren](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Tips voor het afstemmen van prestaties

In sommige gevallen ziet u, wanneer u een Kopieer activiteit uitvoert in Data Factory, aan de bovenkant **' tips voor het afstemmen van prestaties '** , zoals weer gegeven in het bovenstaande voor beeld. De tips geven u de knel punt die wordt geïdentificeerd door ADF voor deze specifieke Kopieer uitvoering, samen met suggesties voor het verhogen van de door Voer van de Kopieer activiteit. Probeer de wijziging opnieuw aan te brengen en voer de kopie opnieuw uit.

Als referentie bieden de tips voor het afstemmen van prestaties momenteel suggesties voor de volgende gevallen:

| Categorie              | Tips voor het afstemmen van prestaties                                      |
| --------------------- | ------------------------------------------------------------ |
| Specifiek gegevens archief   | Gegevens laden in **Azure Synapse Analytics**: suggesties voor het gebruik van een poly base-of Copy-instructie als deze niet wordt gebruikt. |
| &nbsp;                | Gegevens kopiëren van/naar **Azure SQL database**: wanneer DTU onder een hoog gebruik komt, wordt er Voorst Ellen om een upgrade naar een hogere laag te maken. |
| &nbsp;                | Kopiëren van gegevens van/naar **Azure Cosmos DB**: Wanneer ru onder een hoog gebruik komt, wordt een upgrade naar grotere ru Voorst Ellen. |
|                       | Gegevens kopiëren uit **SAP-tabel**: bij het kopiëren van grote hoeveel heden gegevens kunt u gebruikmaken van de partitie optie van de SAP-connector om parallelle belasting in te scha kelen en het maximale partitie nummer te verhogen. |
| &nbsp;                | Gegevens opnemen van **Amazon Redshift**: suggesties voor het gebruik van Unload als dit niet wordt gebruikt. |
| Beperking van gegevens opslag | Als er tijdens het kopiëren een aantal lees-en schrijf bewerkingen worden beperkt door het gegevens archief, wordt er Voorst Ellen om de toegestane aanvraag snelheid voor het gegevens archief te controleren en te verhogen, of om de gelijktijdige werk belasting te verminderen. |
| Integration runtime  | Als u een **zelf-hostend Integration runtime (IR)** gebruikt en de Kopieer activiteit lang in de wachtrij wacht totdat de IR beschik bare bron heeft om te worden uitgevoerd, kunt u de infra rood uitschalen. |
| &nbsp;                | Als u een **Azure Integration runtime** gebruikt dat zich in een niet-optimale regio bevindt die resulteert in langzame Lees-en schrijf bewerkingen, wordt het configureren voor het gebruik van een IR in een andere regio Voorst Ellen. |
| Fouttolerantie       | Als u fout tolerantie configureert en incompatibele rijen overs laat, worden de prestaties van de bron-en Sink-gegevens voor komen. |
| Gefaseerde kopie           | Als de gefaseerde kopie is geconfigureerd, maar niet nuttig is voor uw combi natie van source-sink, kunt u deze verwijderen. |
| Hervatten                | Wanneer Kopieer activiteit wordt hervat vanaf het laatste fout punt, wordt de nieuwe instelling voor de DIU niet van kracht nadat u de instelling DIU na de oorspronkelijke uitvoering hebt gewijzigd. |

## <a name="understand-copy-activity-execution-details"></a>Details van de uitvoering van de Kopieer activiteit begrijpen

In de details van de uitvoering en de duur onder aan de weer gave controle activiteit kopiëren worden de belangrijkste fasen van uw Kopieer activiteit beschreven (Zie het voor beeld aan het begin van dit artikel). Dit is vooral handig bij het oplossen van problemen met de Kopieer prestaties. Het knel punt van de Kopieer uitvoering is de versie met de langste duur. Raadpleeg de volgende tabel in de definitie van elke fase en leer hoe u de [Kopieer activiteit in azure IR oplost](#troubleshoot-copy-activity-on-azure-ir) en hoe u de [Kopieer activiteit op zelf-hostende IR oplost](#troubleshoot-copy-activity-on-self-hosted-ir) met deze informatie.

| Fase           | Beschrijving                                                  |
| --------------- | ------------------------------------------------------------ |
| Wachtrij           | De verstreken tijd totdat de Kopieer activiteit daad werkelijk wordt gestart op de Integration runtime. |
| Script vooraf kopiëren | De verstreken tijd tussen de Kopieer activiteit, te beginnen bij de IR-en kopieer activiteit, waarbij het vooraf kopiëren van het script in de Sink-gegevens opslag wordt voltooid. Toep assen wanneer u het script voor het vooraf kopiëren van de data base-sinks configureert, bijvoorbeeld wanneer u gegevens schrijft in Azure SQL Database opschonen voordat nieuwe gegevens worden gekopieerd. |
| Overdragen        | De verstreken tijd tussen het einde van de vorige stap en de IR waarbij alle gegevens van de bron naar de Sink worden overgebracht. <br/>Let op de substappen onder overdracht parallel uitvoeren en sommige bewerkingen worden nu niet weer gegeven, zoals het parseren/genereren van de bestands indeling.<br><br/>- **Tijd tot eerste byte:** De verstreken tijd tussen het einde van de vorige stap en de tijd waarop de IR de eerste byte van de brongegevens opslag ontvangt. Is van toepassing op bronnen die niet op bestanden zijn gebaseerd.<br>- **Bron van vermelding:** De hoeveelheid tijd die is besteed aan het inventariseren van bron bestanden of gegevens partities. Deze laatste is van toepassing wanneer u partitie opties voor database bronnen configureert, bijvoorbeeld wanneer u gegevens kopieert uit data bases zoals Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Lezen van Bron:** De hoeveelheid tijd die is besteed aan het ophalen van gegevens uit de brongegevens opslag.<br/>- **Schrijven naar Sink:** De hoeveelheid tijd die is besteed aan het schrijven van gegevens naar het sink-gegevens archief. Houd er rekening mee dat sommige connectors op het moment niet beschikken over deze metrische gegevens, zoals Azure Cognitive Search, Azure Data Explorer, Azure Table Storage, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Sales Force/Sales Service Cloud. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Problemen met Kopieer activiteit op Azure IR oplossen

Volg de [stappen voor het afstemmen](copy-activity-performance.md#performance-tuning-steps) van de prestaties om de prestatie test voor uw scenario te plannen en uit te voeren. 

Als de prestaties van de Kopieer activiteit niet voldoen aan uw verwachting, kunt u bij het oplossen van problemen met één Kopieer activiteit op Azure Integration Runtime de tips voor het [afstemmen van prestaties](#performance-tuning-tips) in de weer gave controle van kopiëren weer geven door de suggestie toe te passen en het opnieuw te proberen. Als dat niet het geval is, begrijpt u de [Details van de Kopieer activiteit](#understand-copy-activity-execution-details), controleert u welke fase de **langste** tijd heeft en past u de onderstaande instructies toe om de Kopieer prestaties te verhogen:

- **' Pre-Copy-script ' heeft lange duur:** dit betekent dat het vooraf gekopieerde script dat wordt uitgevoerd op de Sink-data base, lang duurt. De opgegeven vooraf gekopieerde script logica afstemmen om de prestaties te verbeteren. Als u meer hulp nodig hebt bij het verbeteren van het script, neemt u contact op met uw database team.

- **"Overdrachts tijd tot eerste byte" heeft langdurige duur** van het werk: Dit betekent dat uw bron query lang duurt om gegevens te retour neren. De query of server controleren en optimaliseren. Als u meer hulp nodig hebt, neemt u contact op met uw Data Store-team.

- **"Overdrachts bron" heeft langdurige lange werk duur**: Dit betekent dat het inventariseren van bron bestanden of gegevens partities van de bron database traag is.
  - Als u gegevens uit een op een bestand gebaseerde bron kopieert en u **Joker teken filter** gebruikt voor het pad naar de map of de bestands naam (of `wildcardFolderPath` `wildcardFileName` ) of het **filter Laatst gewijzigd tijd** ( `modifiedDatetimeStart` of `modifiedDatetimeEnd` ) gebruikt, ziet u dat filter zou leiden tot een Kopieer activiteit met alle bestanden onder de opgegeven map naar client zijde en vervolgens het filter Toep assen. Dergelijke bestands inventarisatie kan het knel punt worden, vooral wanneer slechts een kleine set bestanden voldoet aan de filter regel.

    - Controleer of u [bestanden kunt kopiëren op basis van een bestandspad of naam in datum-gepartitioneerd](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Op die manier wordt geen last gegeven van de vermelding van de bron zijde.

    - Controleer of u in plaats daarvan het systeem eigen filter van gegevens archief kunt gebruiken, met name voor **voegsel** voor Amazon S3/Azure Blob/Azure file storage en '**listAfter/listBefore**' voor ADLS gen1. Deze filters zijn een filter aan de gegevens opslag aan de server zijde en hebben veel betere prestaties.

    - Overweeg om één grote gegevensset te splitsen in meerdere kleinere gegevens sets, en laat die Kopieer taken gelijktijdig uitvoeren elk deel van de gegevens. U kunt dit doen met lookup/GetMetadata + ForEach + copy. Raadpleeg voor het [kopiëren van bestanden uit meerdere containers](solution-template-copy-files-multiple-containers.md) of [het migreren van gegevens van Amazon S3 naar ADLS Gen2](solution-template-migration-s3-azure.md) oplossings sjablonen als algemeen voor beeld.

  - Controleer of ADF een beperkings fout op de bron heeft gerapporteerd of als uw gegevens archief de status hoog gebruik heeft. Als dat het geval is, verkleint u uw workloads in het gegevens archief of neemt u contact op met de beheerder van uw gegevens opslag om de beperkings limiet of beschik bare resource te verg Roten.

  - Gebruik Azure IR in dezelfde of dicht bij de opslag regio van uw bron gegevens.

- **"Overdracht-lezen van bron" heeft langdurige duur van het werk**: 

  - De gegevens voor het laden van een connector-specifieke best practice aannemen, indien van toepassing. Bij het kopiëren van gegevens uit [Amazon Redshift](connector-amazon-redshift.md), configureert u bijvoorbeeld dat Redshift uit het geheugen moet worden verwijderd.

  - Controleer of ADF een beperkings fout op de bron heeft gerapporteerd of dat uw gegevens archief intensief wordt gebruikt. Als dat het geval is, verkleint u uw workloads in het gegevens archief of neemt u contact op met de beheerder van uw gegevens opslag om de beperkings limiet of beschik bare resource te verg Roten.

  - Controleer uw Kopieer bron en Sink-patroon: 

    - Als uw Kopieer patroon meer dan 4 gegevens integratie-eenheden (DIUs) ondersteunt-Raadpleeg [deze sectie over de](copy-activity-performance-features.md#data-integration-units) Details. u kunt het beste de DIUs verhogen om betere prestaties te krijgen. 

    - Als dat niet het geval is, kunt u één grote gegevensset splitsen in verschillende kleinere gegevens sets, en deze Kopieer taken gelijktijdig uitvoeren elk deel van de gegevens. U kunt dit doen met lookup/GetMetadata + ForEach + copy. Raadpleeg het [kopiëren van bestanden uit meerdere containers](solution-template-copy-files-multiple-containers.md), [het migreren van gegevens van Amazon S3 naar ADLS Gen2](solution-template-migration-s3-azure.md)of [bulksgewijs kopiëren met een controle tabel](solution-template-bulk-copy-with-control-table.md) oplossings sjablonen als algemeen voor beeld.

  - Gebruik Azure IR in dezelfde of dicht bij de opslag regio van uw bron gegevens.

- **' Overdracht-schrijven naar Sink ' heeft langdurige werk duur**:

  - De gegevens voor het laden van een connector-specifieke best practice aannemen, indien van toepassing. Als u bijvoorbeeld gegevens kopieert naar [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md), gebruikt u de instructie poly base of Copy. 

  - Controleer of ADF een beperkings fout op Sink rapporteert of als uw gegevens archief intensief wordt gebruikt. Als dat het geval is, verkleint u uw workloads in het gegevens archief of neemt u contact op met de beheerder van uw gegevens opslag om de beperkings limiet of beschik bare resource te verg Roten.

  - Controleer uw Kopieer bron en Sink-patroon: 

    - Als uw Kopieer patroon meer dan 4 gegevens integratie-eenheden (DIUs) ondersteunt-Raadpleeg [deze sectie over de](copy-activity-performance-features.md#data-integration-units) Details. u kunt het beste de DIUs verhogen om betere prestaties te krijgen. 

    - Als u de [parallelle kopieën](copy-activity-performance-features.md)geleidelijk wilt afstemmen, moet u er rekening mee houden dat te veel parallelle kopieën zelfs de prestaties kunnen nadelig beïnvloeden.

  - Gebruik Azure IR in dezelfde of dicht bij de opslag regio voor het sink-gegevens archief.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Problemen met de Kopieer activiteit op zelf-hostende IR oplossen

Volg de [stappen voor het afstemmen](copy-activity-performance.md#performance-tuning-steps) van de prestaties om de prestatie test voor uw scenario te plannen en uit te voeren. 

Als de Kopieer prestaties niet voldoen aan uw verwachting, kunt u bij het oplossen van problemen met één Kopieer activiteit op Azure Integration Runtime, als u de tips voor het [afstemmen van prestaties](#performance-tuning-tips) ziet in de weer gave controle kopiëren, pas de suggestie toe en probeer het opnieuw. Als dat niet het geval is, begrijpt u de [Details van de Kopieer activiteit](#understand-copy-activity-execution-details), controleert u welke fase de **langste** tijd heeft en past u de onderstaande instructies toe om de Kopieer prestaties te verhogen:

- **' Wachtrij ' heeft een lange duur:** het betekent dat de Kopieer activiteit lang wacht in de wachtrij totdat uw zelf-hostende IR de resource heeft om uit te voeren. Controleer de IR-capaciteit en het gebruik en [schaal omhoog of](create-self-hosted-integration-runtime.md#high-availability-and-scalability) omlaag op basis van uw werk belasting.

- **"Overdrachts tijd tot eerste byte" heeft langdurige duur** van het werk: Dit betekent dat uw bron query lang duurt om gegevens te retour neren. De query of server controleren en optimaliseren. Als u meer hulp nodig hebt, neemt u contact op met uw Data Store-team.

- **"Overdrachts bron" heeft langdurige lange werk duur**: Dit betekent dat het inventariseren van bron bestanden of gegevens partities van de bron database traag is.

  - Controleer of de zelf-hostende IR-computer een lage latentie heeft om verbinding te maken met de brongegevens opslag. Als uw bron zich in azure bevindt, kunt u [Dit hulp programma](http://www.azurespeed.com/Azure/Latency) gebruiken om de latentie van de zelf-hostende IR-computer te controleren naar de Azure-regio, hoe minder het is.

  - Als u gegevens uit een op een bestand gebaseerde bron kopieert en u **Joker teken filter** gebruikt voor het pad naar de map of de bestands naam (of `wildcardFolderPath` `wildcardFileName` ) of het **filter Laatst gewijzigd tijd** ( `modifiedDatetimeStart` of `modifiedDatetimeEnd` ) gebruikt, ziet u dat filter zou leiden tot een Kopieer activiteit met alle bestanden onder de opgegeven map naar client zijde en vervolgens het filter Toep assen. Dergelijke bestands inventarisatie kan het knel punt worden, vooral wanneer slechts een kleine set bestanden voldoet aan de filter regel.

    - Controleer of u [bestanden kunt kopiëren op basis van een bestandspad of naam in datum-gepartitioneerd](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Op die manier wordt geen last gegeven van de vermelding van de bron zijde.

    - Controleer of u in plaats daarvan het systeem eigen filter van gegevens archief kunt gebruiken, met name voor **voegsel** voor Amazon S3/Azure Blob/Azure file storage en '**listAfter/listBefore**' voor ADLS gen1. Deze filters zijn een filter aan de gegevens opslag aan de server zijde en hebben veel betere prestaties.

    - Overweeg om één grote gegevensset te splitsen in meerdere kleinere gegevens sets, en laat die Kopieer taken gelijktijdig uitvoeren elk deel van de gegevens. U kunt dit doen met lookup/GetMetadata + ForEach + copy. Raadpleeg voor het [kopiëren van bestanden uit meerdere containers](solution-template-copy-files-multiple-containers.md) of [het migreren van gegevens van Amazon S3 naar ADLS Gen2](solution-template-migration-s3-azure.md) oplossings sjablonen als algemeen voor beeld.

  - Controleer of ADF een beperkings fout op de bron heeft gerapporteerd of als uw gegevens archief de status hoog gebruik heeft. Als dat het geval is, verkleint u uw workloads in het gegevens archief of neemt u contact op met de beheerder van uw gegevens opslag om de beperkings limiet of beschik bare resource te verg Roten.

- **"Overdracht-lezen van bron" heeft langdurige duur van het werk**: 

  - Controleer of de zelf-hostende IR-computer een lage latentie heeft om verbinding te maken met de brongegevens opslag. Als uw bron zich in azure bevindt, kunt u [Dit hulp programma](http://www.azurespeed.com/Azure/Latency) gebruiken om de latentie van de zelf-hostende IR-computer te controleren naar de Azure-regio's, hoe minder het is.

  - Controleer of de zelf-hostende IR-computer voldoende inkomende band breedte heeft om de gegevens efficiënt te lezen en te verzenden. Als uw brongegevens archief zich in azure bevindt, kunt u [Dit hulp programma](https://www.azurespeed.com/Azure/Download) gebruiken om de download snelheid te controleren.

  - Controleer de door de zelf-hostende IR-trend en het geheugen gebruik in Azure Portal-> de overzichts pagina data factory->. Overweeg om [IR omhoog/uit te schalen](create-self-hosted-integration-runtime.md#high-availability-and-scalability) als het CPU-gebruik hoog is of het beschik bare geheugen laag is.

  - De gegevens voor het laden van een connector-specifieke best practice aannemen, indien van toepassing. Bijvoorbeeld:

    - Bij het kopiëren van gegevens uit [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source)en [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), kunt u opties voor gegevens partities inschakelen om gegevens parallel te kopiëren.

    - Bij het kopiëren van de gegevens van [HDFS](connector-hdfs.md), configureert u voor het gebruik van DistCp.

    - Bij het kopiëren van gegevens uit [Amazon Redshift](connector-amazon-redshift.md)configureert u voor het gebruik van RedShift uit het geheugen.

  - Controleer of ADF een beperkings fout op de bron heeft gerapporteerd of dat uw gegevens archief intensief wordt gebruikt. Als dat het geval is, verkleint u uw workloads in het gegevens archief of neemt u contact op met de beheerder van uw gegevens opslag om de beperkings limiet of beschik bare resource te verg Roten.

  - Controleer uw Kopieer bron en Sink-patroon: 

    - Als u gegevens uit gegevens archieven met partitie opties kopieert, kunt u overwegen om de [parallelle kopieën](copy-activity-performance-features.md)geleidelijk af te stemmen. Houd er rekening mee dat te veel parallelle kopieën zelfs de prestaties kunnen nadelig beïnvloeden.

    - Als dat niet het geval is, kunt u één grote gegevensset splitsen in verschillende kleinere gegevens sets, en deze Kopieer taken gelijktijdig uitvoeren elk deel van de gegevens. U kunt dit doen met lookup/GetMetadata + ForEach + copy. Raadpleeg het [kopiëren van bestanden uit meerdere containers](solution-template-copy-files-multiple-containers.md), [het migreren van gegevens van Amazon S3 naar ADLS Gen2](solution-template-migration-s3-azure.md)of [bulksgewijs kopiëren met een controle tabel](solution-template-bulk-copy-with-control-table.md) oplossings sjablonen als algemeen voor beeld.

- **' Overdracht-schrijven naar Sink ' heeft langdurige werk duur**:

  - De gegevens voor het laden van een connector-specifieke best practice aannemen, indien van toepassing. Als u bijvoorbeeld gegevens kopieert naar [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md), gebruikt u de instructie poly base of Copy. 

  - Controleer of de zelf-hostende IR-computer een lage latentie heeft om verbinding te maken met het sink-gegevens archief. Als uw Sink zich in azure bevindt, kunt u [Dit hulp programma](http://www.azurespeed.com/Azure/Latency) gebruiken om de latentie van de zelf-hostende IR-computer te controleren naar de Azure-regio, hoe minder het is.

  - Controleer of de zelf-hostende IR-machine voldoende uitgaande band breedte heeft om de gegevens efficiënt over te dragen en te schrijven. Als uw Sink-gegevens archief zich in azure bevindt, kunt u [Dit hulp programma](https://www.azurespeed.com/Azure/UploadLargeFile) gebruiken om de upload snelheid te controleren.

  - Controleer of de door de zelf-hostende IR-trend van CPU en geheugen gebruik in Azure Portal > de overzichts pagina data factory->. Overweeg om [IR omhoog/uit te schalen](create-self-hosted-integration-runtime.md#high-availability-and-scalability) als het CPU-gebruik hoog is of het beschik bare geheugen laag is.

  - Controleer of ADF een beperkings fout op Sink rapporteert of als uw gegevens archief intensief wordt gebruikt. Als dat het geval is, verkleint u uw workloads in het gegevens archief of neemt u contact op met de beheerder van uw gegevens opslag om de beperkings limiet of beschik bare resource te verg Roten.

  - Als u de [parallelle kopieën](copy-activity-performance-features.md)geleidelijk wilt afstemmen, moet u er rekening mee houden dat te veel parallelle kopieën mogelijk zelfs de prestaties nadelig beïnvloeden.


## <a name="connector-and-ir-performance"></a>Connector-en IR-prestaties

In deze sectie vindt u een aantal richt lijnen voor probleem oplossing voor het oplossen van problemen met connector type of Integration runtime.

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>De uitvoerings tijd van de activiteit is afhankelijk van Azure IR VS Azure VNet IR

De uitvoerings tijd van de activiteit varieert wanneer de gegevensset is gebaseerd op verschillende Integration Runtime.

- **Symptomen**: als u de vervolg keuzelijst gekoppelde service in de gegevensset in-of uitschakelt, worden dezelfde pijplijn activiteiten uitgevoerd, maar heeft dit drastische verschillende uitvoerings tijden. Wanneer de gegevensset is gebaseerd op het Integration Runtime beheerde Virtual Network, neemt het gemiddeld meer dan twee minuten in beslag om de uitvoering te volt ooien, maar duurt het ongeveer 20 seconden om te volt ooien wanneer dit is gebaseerd op de standaard Integration Runtime.

- **Oorzaak**: als u de details van de pijplijn uitvoeringen wilt controleren, kunt u zien dat de langzame pijp lijn wordt uitgevoerd op beheerde VNet (Virtual Network) IR, terwijl het normale wordt uitgevoerd op Azure IR. Het ontwerp van beheerde VNet-IR neemt langer tijd in beslag dan Azure IR omdat er niet meer dan één reken knooppunt per data factory wordt gereserveerd, waardoor elke Kopieer activiteit een warme periode van twee minuten heeft, en deze gebeurtenis voornamelijk wordt uitgevoerd op de VNet-koppeling in plaats van Azure IR.

    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>Lage prestaties bij het laden van gegevens in Azure SQL Database

- **Symptomen**: het kopiëren van gegevens in naar Azure SQL database is langzaam.

- **Oorzaak**: de hoofd oorzaak van het probleem wordt meestal geactiveerd door de bottleneck van Azure SQL database-zijde. Hier volgen enkele mogelijke oorzaken:

    - Azure SQL Database laag is niet hoog genoeg.

    - Het DTU-gebruik Azure SQL Database bijna 100%. U kunt [de prestaties bewaken](../azure-sql/database/monitor-tune-overview.md) en overwegen om de Azure SQL database-laag bij te werken.

    - De indexen zijn niet juist ingesteld. Verwijder alle indexen voordat de gegevens worden geladen en maak deze opnieuw nadat het laden is voltooid.

    - WriteBatchSize is niet groot genoeg om de grootte van de schemarij ruimte aan te passen. Probeer de eigenschap voor het probleem te verg Roten.

    - In plaats van bulksgewijs inkrimpen, wordt opgeslagen procedure gebruikt, die naar verwachting de prestaties verergert. 


### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Time-out of langzame prestaties bij het parseren van een groot Excel-bestand

- **Symptomen**:

    - Wanneer u een Excel-gegevensset maakt en een schema importeert vanuit verbinding/archief, voor beeld van gegevens, lijst of werk bladen vernieuwen, kunt u een time-outfout opvragen als het Excel-bestand groot is.

    - Wanneer u de Kopieer activiteit gebruikt om gegevens te kopiëren van een groot Excel-bestand (>= 100 MB) naar een ander gegevens archief, kan dit leiden tot trage prestaties of OOM probleem.

- **Oorzaak**: 

    - Voor bewerkingen zoals het importeren van schema, het bekijken van gegevens en het weer geven van werk bladen in Excel-gegevensset, is de time-out 100 s en statisch. Voor een groot Excel-bestand kunnen deze bewerkingen niet worden voltooid binnen de time-outwaarde.

    - Met de ADF Copy-activiteit wordt het hele Excel-bestand in het geheugen gelezen en vervolgens vindt het opgegeven werk blad en de cellen om gegevens te lezen. Dit gedrag is te wijten aan het gebruik van de onderliggende SDK.

- **Oplossing**: 

    - Voor het importeren van schema kunt u een kleiner voorbeeld bestand genereren, een subset van het oorspronkelijke bestand, en vervolgens schema importeren uit voorbeeld bestand kiezen in plaats van schema importeren vanuit verbinding/archief.

    - Voor het werk blad overzichten kunt u in de vervolg keuzelijst voor het werk blad op bewerken klikken en in plaats daarvan de blad naam/index invoeren.

    - Als u een groot Excel-bestand (>100 MB) wilt kopiëren naar een ander archief, kunt u gebruikmaken van de Excel-bron gegevens stroom die door sport streaming wordt gelezen en beter presteert.
    
## <a name="other-references"></a>Andere verwijzingen

Hier vindt u de prestatie bewaking en het afstemmen van verwijzingen voor een aantal ondersteunde gegevens archieven:

* Azure Blob-opslag: [schaal baarheid en prestatie doelen voor Blob Storage](../storage/blobs/scalability-targets.md) en de [controle lijst voor prestaties en schaal baarheid voor Blob Storage](../storage/blobs/storage-performance-checklist.md).
* Azure-tabel opslag: [schaal baarheid en prestatie doelen voor](../storage/tables/scalability-targets.md) de [controle lijst voor tabel opslag en prestaties en schaal baarheid voor tabel opslag](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: u kunt [de prestaties bewaken](../azure-sql/database/monitor-tune-overview.md) en het DTU-percentage (data base Trans Action Unit) controleren.
* Azure Synapse Analytics: de functionaliteit wordt gemeten in data warehouse units (Dwu's). Zie [reken kracht beheren in azure Synapse Analytics (overzicht)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [prestatie niveaus in azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server: [prestaties bewaken en afstemmen](/sql/relational-databases/performance/monitor-and-tune-for-performance).
* On-premises Bestands server: [prestaties afstemmen voor bestands servers](/previous-versions//dn567661(v=vs.85)).

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Handleiding voor prestaties en schaalbaarheid van kopieeractiviteit](copy-activity-performance.md)
- [Functies voor het optimaliseren van de activiteit prestaties](copy-activity-performance-features.md)
- [Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure](data-migration-guidance-overview.md)
- [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)
