---
title: Gegevens kopiëren-hulp programma Azure Data Factory
description: Bevat informatie over het hulp programma Gegevens kopiëren in Azure Data Factory gebruikers interface
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: yexu
ms.openlocfilehash: 18d61c7f51547a524e64fc44c98e70e0f5bc2752
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372991"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Hulpprogramma voor gegevens kopiëren in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Het hulp programma Azure Data Factory Gegevens kopiëren vereenvoudigt en optimaliseert het proces van het opnemen van gegevens in een Data Lake. Dit is meestal een eerste stap in een end-to-end gegevens integratie scenario.  Het bespaart tijd, vooral wanneer u Azure Data Factory gebruikt om gegevens van een gegevens bron voor de eerste keer op te nemen. Enkele voor delen van het gebruik van dit hulp programma:

- Wanneer u het Azure Data Factory Gegevens kopiëren-hulp programma gebruikt, hebt u geen inzicht in Data Factory definities voor gekoppelde services, gegevens sets, pijp lijnen, activiteiten en triggers. 
- De stroom van Gegevens kopiëren hulp programma is intuïtief voor het laden van gegevens in een Data Lake. Het hulp programma maakt automatisch alle benodigde Data Factory resources om gegevens van de geselecteerde bron gegevens opslag te kopiëren naar het geselecteerde doel/Sink-gegevens archief. 
- Het hulp programma Gegevens kopiëren helpt u bij het valideren van de gegevens die op het moment van ontwerpen worden opgenomen, zodat u eventuele mogelijke fouten aan het begin van zichzelf kunt voor komen.
- Als u complexe bedrijfs logica wilt implementeren om gegevens in een Data Lake te laden, kunt u de Data Factory resources die zijn gemaakt door het Gegevens kopiëren hulp programma nog steeds bewerken met behulp van de ontwerp functie per activiteit in Data Factory gebruikers interface. 

De volgende tabel bevat richt lijnen voor het gebruik van het hulp programma Gegevens kopiëren versus het ontwerpen per activiteit in Data Factory gebruikers interface: 

| Hulpprogramma voor het kopiëren van gegevens | Ontwerpen per activiteit (Kopieer activiteit) |
| -------------- | -------------------------------------- |
| U wilt eenvoudig een taak voor het laden van gegevens maken zonder te leren over Azure Data Factory entiteiten (gekoppelde services, gegevens sets, pijp lijnen, enzovoort) | U wilt complexe en flexibele logica implementeren voor het laden van gegevens in Lake. |
| U wilt snel een groot aantal gegevens artefacten laden in een Data Lake. | U wilt Kopieer activiteiten koppelen met volgende activiteiten voor het opschonen of verwerken van gegevens. |

Als u het hulp programma Gegevens kopiëren wilt starten, klikt u op de tegel **gegevens kopiëren** op de start pagina van uw Data Factory.

![Pagina aan de slag-koppeling naar het hulp programma Gegevens kopiëren](./media/doc-common-process/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuïtieve stroom voor het laden van gegevens in een Data Lake
Met dit hulp programma kunt u eenvoudig gegevens verplaatsen van een groot aantal bronnen naar bestemmingen in enkele minuten met een intuïtieve stroom:  

1. Configureer de instellingen voor de **bron**.
2. Configureer de instellingen voor de **doel locatie**. 
3. **Geavanceerde instellingen** configureren voor de Kopieer bewerking, zoals kolom toewijzing, prestatie-instellingen en fout tolerantie-instellingen. 
4. Geef een **planning** op voor de taak voor het laden van gegevens. 
5. Bekijk een **samen vatting** van de Data Factory entiteiten die moeten worden gemaakt. 
6. **Bewerk** de pijp lijn zo nodig om de instellingen voor de Kopieer activiteit bij te werken. 

   Het hulp programma is ontworpen met big data van het begin, met ondersteuning voor diverse gegevens en object typen. U kunt deze gebruiken om honderden mappen, bestanden of tabellen te verplaatsen. Het hulp programma ondersteunt automatische gegevens weergave, schema-vastleg ging en automatische toewijzing en gegevens filtering.

![Hulpprogramma voor het kopiëren van gegevens](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatische gegevens voorbeeld
U kunt een voor beeld bekijken van een deel van de gegevens uit de geselecteerde brongegevens opslag, zodat u de gegevens kunt valideren die worden gekopieerd. Als de bron gegevens zich in een tekst bestand bevinden, parseert het Gegevens kopiëren-hulp programma bovendien het tekst bestand om automatisch de rij-en kolom scheidings tekens en het schema op te sporen.

![Bestands instellingen](./media/copy-data-tool/file-format-settings.png)

Na de detectie:

![Gedetecteerde Bestands instellingen en voor beeld](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Schema's vastleggen en automatisch toewijzen
Het schema van de gegevens bron is in veel gevallen niet hetzelfde als het schema van de gegevens bestemming. In dit scenario moet u kolommen uit het bron schema toewijzen aan kolommen uit het doel schema.

Het hulp programma Gegevens kopiëren bewaakt en leert uw gedrag wanneer u kolommen tussen de bron-en doel opslag toewijst. Nadat u een of enkele kolommen uit het brongegevens archief hebt gekozen en deze hebt toegewezen aan het doel schema, begint het Gegevens kopiëren-hulp programma met het analyseren van het patroon voor kolom paren die u aan beide zijden hebt gekozen. Vervolgens wordt hetzelfde patroon toegepast op de rest van de kolommen. Daarom ziet u dat alle kolommen zijn toegewezen aan de bestemming op een manier die u net na verschillende klikken hebt.  Als u niet tevreden bent met de keuze van kolom toewijzing die wordt geboden door Gegevens kopiëren tool, kunt u deze negeren en door gaan met het hand matig toewijzen van de kolommen. Ondertussen leert het Gegevens kopiëren-hulp programma het patroon voortdurend te zien en bij te werken, en uiteindelijk bereikt u het juiste patroon voor de kolom toewijzing die u wilt bereiken. 

> [!NOTE]
> Bij het kopiëren van gegevens uit SQL Server of Azure SQL Database naar Azure Synapse Analytics als de tabel niet bestaat in het doel archief, Gegevens kopiëren hulp programma het maken van de tabel automatisch ondersteunt met behulp van het bron schema. 

## <a name="filter-data"></a>Gegevens filteren
U kunt bron gegevens filteren om alleen de gegevens te selecteren die moeten worden gekopieerd naar de Sink-gegevens opslag. Filteren vermindert het volume van de gegevens die moeten worden gekopieerd naar de Sink-gegevens opslag en verhoogt daarom de door Voer van de Kopieer bewerking. Gegevens kopiëren tool biedt een flexibele manier om gegevens in een relationele data base te filteren met behulp van de SQL-query taal of bestanden in een Azure Blob-map. 

### <a name="filter-data-in-a-database"></a>Gegevens in een Data Base filteren
De volgende scherm afbeelding toont een SQL-query om de gegevens te filteren.

![Gegevens in een Data Base filteren](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Gegevens in een Azure Blob-map filteren
U kunt variabelen in het mappad gebruiken om gegevens uit een map te kopiëren. De ondersteunde variabelen zijn: **{Year}**, **{Month}**, **{Day}**, **{Hour}** en **{Minute}**. Bijvoorbeeld: inputfolder/{year}/{month}/{Day}. 

Stel dat u een invoer mappen hebt met de volgende indeling: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klik op de knop **Bladeren** voor **bestand of map**, blader naar een van deze mappen (bijvoorbeeld 2016->03->01->02) en klik op **kiezen**. U ziet 2016/03/01/02 in het tekstvak. 

Vervang vervolgens **2016** door **{Year}**, **03** met **{Month}**, **01** met **{Day}** en **02** met **{Hour}** en druk op de **Tab** -toets. Er moeten vervolg keuzelijsten worden weer gegeven om de indeling voor deze vier variabelen te selecteren:

![Bestand of map filteren](./media/copy-data-tool/filter-file-or-folder.png)

Het hulp programma Gegevens kopiëren genereert para meters met expressies, functies en systeem variabelen die kunnen worden gebruikt om {Year}, {month}, {Day}, {Hour} en {Minute} aan te geven bij het maken van de pijp lijn.

## <a name="scheduling-options"></a>Plannings opties
U kunt de Kopieer bewerking eenmaal of volgens een schema uitvoeren (elk uur, dagelijks, enzovoort). Deze opties kunnen worden gebruikt voor de connectors in verschillende omgevingen, waaronder on-premises, Cloud en lokale bureau blad. 

Een eenmalige Kopieer bewerking maakt het verplaatsen van gegevens van een bron naar een bestemming slechts één keer mogelijk. Dit is van toepassing op gegevens van elke grootte en een ondersteunde indeling. Met de geplande kopie kunt u gegevens kopiëren naar een terugkeer patroon dat u opgeeft. U kunt uitgebreide instellingen (zoals nieuwe poging, time-out en waarschuwingen) gebruiken om de geplande kopie te configureren.

![Plannings opties](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Volgende stappen
Probeer deze zelf studies die gebruikmaken van het Gegevens kopiëren-hulp programma:

- [Snelstartgids: een data factory maken met het Gegevens kopiëren-hulp programma](quickstart-create-data-factory-copy-data-tool.md)
- [Zelf studie: gegevens kopiëren in azure met behulp van het Gegevens kopiëren](tutorial-copy-data-tool.md) 
- [Zelf studie: on-premises gegevens naar Azure kopiëren met behulp van het Gegevens kopiëren](tutorial-hybrid-copy-data-tool.md)
