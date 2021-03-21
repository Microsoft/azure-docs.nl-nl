---
title: U-SQL-scripts uitbreiden met python in Azure Data Lake Analytics
description: Meer informatie over het uitvoeren van python-code in U-SQL-scripts met Azure Data Lake Analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.custom: devx-track-python
ms.openlocfilehash: d81e529d7073e8200c5a1d1fa8f51504b1dc9259
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218439"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>U-SQL-scripts uitbreiden met Python-code in Azure Data Lake Analytics

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u controleren of de python-uitbrei dingen zijn geïnstalleerd in uw Azure Data Lake Analytics-account.

* Navigeer naar u Data Lake Analytics account in de Azure Portal
* Klik in het linkermenu onder **aan** de slag op **voorbeeld scripts**
* Klik op **U-SQL-uitbrei dingen installeren** en vervolgens op **OK**

## <a name="overview"></a>Overzicht

Met python-extensies voor U-SQL kunnen ontwikkel aars zeer parallelle uitvoering van python-code uitvoeren. In het volgende voor beeld ziet u de basis stappen:

* Gebruik de `REFERENCE ASSEMBLY` instructie om python-extensies in te scha kelen voor het U-SQL-script
* De `REDUCE` bewerking voor het partitioneren van de invoer gegevens op een sleutel gebruiken
* De python-uitbrei dingen voor U-SQL bevatten een ingebouwde versmaller ( `Extension.Python.Reducer` ) waarmee python-code wordt uitgevoerd op elk hoek punt dat aan de verlaagings eenheid wordt toegewezen
* Het U-SQL-script bevat de Inge sloten python-code die een functie heeft aangeroepen `usqlml_main` die een Panda data frame als invoer accepteert en een Panda data frame als uitvoer retourneert.

```usql
REFERENCE ASSEMBLY [ExtPython];
DECLARE @myScript = @"
def get_mentions(tweet):
    return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )
def usqlml_main(df):
    del df['time']
    del df['author']
    df['mentions'] = df.tweet.apply(get_mentions)
    del df['tweet']
    return df
";
@t  =
    SELECT * FROM
       (VALUES
           ("D1","T1","A1","@foo Hello World @bar"),
           ("D2","T2","A2","@baz Hello World @beer")
       ) AS date, time, author, tweet );
@m  =
    REDUCE @t ON date
    PRODUCE date string, mentions string
    USING new Extension.Python.Reducer(pyScript:@myScript);
OUTPUT @m
    TO "/tweetmentions.csv"
    USING Outputters.Csv();
```

## <a name="how-python-integrates-with-u-sql"></a>Hoe python integreert met U-SQL

### <a name="datatypes"></a>Gegevens typen

* Teken reeks-en numerieke kolommen van U-SQL worden geconverteerd als-tussen Panda en U-SQL
* U-SQL-nullen worden geconverteerd naar en van Panda `NA` waarden

### <a name="schemas"></a>Schema 's

* Index vectoren in Pandas worden niet ondersteund in U-SQL. Alle invoer gegevens frames in de python-functie hebben altijd een 64-bits numerieke index van 0 tot het aantal rijen min 1.
* U-SQL-gegevens sets mogen geen dubbele kolom namen bevatten
* U-SQL gegevens sets kolom namen die geen teken reeksen zijn.

### <a name="python-versions"></a>Python-versies

Alleen python 3.5.1 (compiled for Windows) wordt ondersteund.

### <a name="standard-python-modules"></a>Standaard Python-modules

Alle Standard python-modules zijn opgenomen.

### <a name="additional-python-modules"></a>Aanvullende python-modules

Naast de standaard python-bibliotheken zijn er diverse veelgebruikte python-bibliotheken opgenomen:

* Pandas
* numpy
* numexpr

### <a name="exception-messages"></a>Uitzonderings berichten

Momenteel wordt een uitzonde ring in Python-code weer gegeven als algemene vertex fout. In de toekomst wordt het python-uitzonderings bericht weer gegeven in de U-SQL-taak fout berichten.

### <a name="input-and-output-size-limitations"></a>Limieten voor invoer en uitvoer

Aan elk hoek punt is een beperkte hoeveelheid geheugen toegewezen. Deze limiet is momenteel 6 GB voor een AU. Omdat de invoer-en uitvoer DataFrames in het geheugen van de python-code moeten bestaan, kan de totale grootte van de invoer en uitvoer niet groter zijn dan 6 GB.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-venster functies gebruiken voor Azure Data Lake Analytics taken](./data-lake-analytics-u-sql-get-started.md)
* [Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken](data-lake-analytics-data-lake-tools-for-vscode.md)