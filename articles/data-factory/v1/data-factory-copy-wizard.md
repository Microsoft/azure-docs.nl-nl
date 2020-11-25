---
title: Gegevens eenvoudig kopiëren met de wizard kopiëren-Azure
description: Meer informatie over het gebruik van de wizard voor het Data Factory kopiëren om gegevens te kopiëren van ondersteunde gegevens bronnen naar Sinks.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 28169e43d0e6949a16cc56c7e7d5d91d6db1ef57
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003035"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Gegevens eenvoudig kopiëren of verplaatsen met Azure Data Factory wizard kopiëren
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md). 


De wizard kopiëren Azure Data Factory is het proces van het opnemen van gegevens, meestal een eerste stap in een scenario met een end-to-end gegevens integratie. Wanneer u de wizard kopiëren van Azure Data Factoryt, hoeft u geen JSON-definities te begrijpen voor gekoppelde services, gegevens sets en pijp lijnen. Nadat u alle stappen in de wizard hebt voltooid, maakt de wizard echter automatisch een pijp lijn om gegevens van de geselecteerde gegevens bron naar de geselecteerde bestemming te kopiëren. Daarnaast helpt de wizard kopiëren u bij het valideren van de gegevens die worden opgenomen op het moment van ontwerpen, waardoor veel van uw tijd wordt bespaard, vooral wanneer u gegevens voor de eerste keer opneemt uit de gegevens bron. Als u de wizard kopiëren wilt starten, klikt u op de tegel **gegevens kopiëren** op de start pagina van uw Data Factory.

![De wizard Kopiëren](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Een intuïtieve wizard voor het kopiëren van gegevens
Met deze wizard kunt u eenvoudig gegevens van een groot aantal bronnen naar bestemmingen verplaatsen binnen enkele minuten. Nadat u de wizard hebt door lopen, wordt er automatisch een pijp lijn met een Kopieer activiteit voor u gemaakt samen met afhankelijke Data Factory entiteiten (gekoppelde services en gegevens sets). Er zijn geen extra stappen vereist om de pijp lijn te maken.   

![Gegevensbron selecteren](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Zie het artikel [zelf studie kopiëren wizard](data-factory-copy-data-wizard-tutorial.md) voor stapsgewijze instructies voor het maken van een voorbeeld pijplijn voor het kopiëren van gegevens van een Azure-Blob naar een Azure SQL database tabel. 
> 
> 

De wizard is ontworpen met big data in het begin. Het is eenvoudig en efficiënt om Data Factory pijp lijnen te schrijven waarmee honderden mappen, bestanden of tabellen worden verplaatst met behulp van de Gegevens kopiëren wizard. De wizard ondersteunt de volgende drie functies: automatische gegevens voorbeeld, schema's vastleggen en toewijzen en gegevens filteren. 

## <a name="automatic-data-preview"></a>Automatische gegevens voorbeeld
Met de wizard kopiëren kunt u een deel van de gegevens van de geselecteerde gegevens bron controleren, zodat u kunt controleren of de gegevens de juiste gegevens zijn die u wilt kopiëren. Als de bron gegevens zich in een tekst bestand bevinden, parseert de wizard kopiëren ook het tekst bestand om de rij-en kolom scheidings tekens en het schema automatisch te leren. 

![Pagina Instellingen bestandsindelingen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schema's vastleggen en toewijzen
Het schema van invoer gegevens komt mogelijk niet overeen met het schema van de uitvoer gegevens in sommige gevallen. In dit scenario moet u kolommen uit het bron schema toewijzen aan kolommen uit het doel schema. 

De wizard kopiëren wijst automatisch kolommen in het bron schema toe aan kolommen in het doel schema. U kunt de toewijzingen negeren met behulp van de vervolg keuzelijsten (of) Hiermee geeft u op of een kolom moet worden overgeslagen bij het kopiëren van de gegevens.   

![Schema toewijzing](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Gegevens filteren
Met de wizard kunt u Bron gegevens filteren om alleen de gegevens te selecteren die moeten worden gekopieerd naar het gegevens archief doel/sink. Filteren vermindert het volume van de gegevens die moeten worden gekopieerd naar de Sink-gegevens opslag en verhoogt daarom de door Voer van de Kopieer bewerking. Het biedt een flexibele manier om gegevens in een relationele data base te filteren met behulp van de SQL-query taal (of) bestanden in een Azure Blob-map door gebruik te maken van [Data Factory-functies en-variabelen](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filteren van gegevens in een Data Base
In het voor beeld gebruikt de SQL-query de `Text.Format` functie en de `WindowStart` variabele. 

![Expressies valideren](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filteren van gegevens in een Azure Blob-map
U kunt variabelen in het mappad gebruiken om gegevens te kopiëren uit een map die wordt bepaald tijdens runtime op basis van [systeem variabelen](data-factory-functions-variables.md#data-factory-system-variables). De ondersteunde variabelen zijn: **{Year}**, **{Month}**, **{Day}**, **{Hour}**, **{Minute}** en **{Custom}**. Voor beeld: inputfolder/{year}/{month}/{Day}.

Stel dat u een invoer mappen hebt met de volgende indeling:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klik op de knop **Bladeren** voor **bestand of map**, blader naar een van deze mappen (bijvoorbeeld 2016->03->01->02) en klik op **kiezen**. U ziet `2016/03/01/02` in het tekstvak. Vervang nu **2016** door **{Year}**, **03** met **{Month}**, **01** met **{Day}** en **02** met **{Hour}** en druk op tab. Er moeten vervolg keuzelijsten worden weer gegeven om de indeling voor deze vier variabelen te selecteren:

![Systeem variabelen gebruiken](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Zoals in de volgende scherm afbeelding wordt weer gegeven, kunt u ook een **aangepaste** variabele en [ondersteunde opmaak teken reeksen](/dotnet/standard/base-types/custom-date-and-time-format-strings)gebruiken. Als u een map met die structuur wilt selecteren, gebruikt u eerst de knop **Bladeren** . Vervang vervolgens een waarde door **{Custom}** en druk op TAB om het tekstvak weer te geven waarin u de notatie teken reeks kunt invoeren.     

![Aangepaste variabele gebruiken](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Ondersteuning voor diverse typen gegevens en objecten
Met de wizard kopiëren kunt u op efficiënte wijze honderden mappen, bestanden of tabellen verplaatsen.

![Tabellen selecteren waaruit de gegevens moeten worden gekopieerd](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Plannings opties
U kunt de Kopieer bewerking eenmaal of volgens een schema uitvoeren (elk uur, dagelijks, enzovoort). Beide opties kunnen worden gebruikt voor de breedte van de connectors op alle locaties op locatie, in de Cloud en lokaal bureau blad.

Een eenmalige Kopieer bewerking maakt het verplaatsen van gegevens van een bron naar een bestemming slechts één keer mogelijk. Dit is van toepassing op gegevens van elke grootte en een ondersteunde indeling. Met de geplande kopie kunt u gegevens kopiëren naar een voorgeschreven terugkeer patroon. U kunt uitgebreide instellingen (zoals nieuwe poging, time-out en waarschuwingen) gebruiken om de geplande kopie te configureren.

![Plannings eigenschappen](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Volgende stappen
Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle beschrijving van het gebruik van de wizard voor het kopiëren van Data Factory om een pijp lijn te maken met de Kopieer activiteit.