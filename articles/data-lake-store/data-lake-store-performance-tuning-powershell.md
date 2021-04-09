---
title: Afstemming van Azure Data Lake Storage Gen1 prestaties-Power shell
description: Tips voor het verbeteren van de prestaties bij het gebruik van Azure PowerShell met Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: twooley
ms.openlocfilehash: 4ac2bbb21fd1a987d544a536d0f52628824e0bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723793"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Richt lijnen voor het afstemmen van prestaties voor het gebruik van Power shell met Azure Data Lake Storage Gen1

In dit artikel worden de eigenschappen beschreven die u kunt afstemmen om betere prestaties te krijgen wanneer u Power shell gebruikt om met Data Lake Storage Gen1 te werken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Eigenschappen die betrekking hebben op prestaties

| Eigenschap            | Standaard | Beschrijving |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Met deze parameter kunt u het aantal parallelle threads voor het uploaden of downloaden van elk bestand kiezen. Dit nummer vertegenwoordigt het maximum aantal threads dat per bestand kan worden toegewezen, maar u kunt echter wel minder threads ontvangen, afhankelijk van uw scenario (bijvoorbeeld als u een bestand van 1 KB uploadt, krijgt u één thread, zelfs als u voor 20 threads vraagt).  |
| ConcurrentFileCount | 10      | Deze parameter is specifiek bedoeld voor het uploaden en downloaden van mappen. De parameter bepaalt het aantal bestanden dat tegelijk kan worden geüpload of gedownload. Dit nummer vertegenwoordigt het maximum aantal gelijktijdige bestanden dat tegelijkertijd kan worden geüpload of gedownload, maar u kunt een minder gelijktijdigheid krijgen, afhankelijk van uw scenario (bijvoorbeeld als u twee bestanden uploadt, kunt u twee gelijktijdige bestanden uploaden, zelfs als u om 15 vraagt). |

**Voorbeeld:**

Met deze opdracht worden bestanden van Data Lake Storage Gen1 naar het lokale station van de gebruiker gedownload met 20 threads per bestand en 100 gelijktijdige bestanden.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Eigenschaps waarden bepalen

De volgende vraag kunt u wellicht bepalen welke waarde moet worden geboden voor de eigenschappen die betrekking hebben op de prestaties. Hier volgen een aantal richtlijnen.

* **Stap 1: het totale aantal threads bepalen** -begin met het berekenen van het totale aantal threads dat moet worden gebruikt. Als algemene richt lijn moet u zes threads voor elke fysieke kern gebruiken.

    `Total thread count = total physical cores * 6`

    **Voorbeeld:**

    We nemen hier aan dat u de PowerShell-opdrachten uitvoert vanaf een D14-VM met 16 kernen

    `Total thread count = 16 cores * 6 = 96 threads`

* **Stap 2: PerFileThreadCount berekenen** -we berekenen onze PerFileThreadCount op basis van de grootte van de bestanden. Voor bestanden die kleiner zijn dan 2,5 GB, hoeft u deze para meter niet te wijzigen, omdat de standaard waarde van 10 voldoende is. Voor bestanden die groter zijn dan 2,5 GB, moet u 10 threads gebruiken als basis voor de eerste 2,5 GB en één thread toevoegen voor elke extra toename van 256 MB in de bestands grootte. Als u een map kopieert met bestanden van zeer verschillende groottes, kunt u overwegen ze op vergelijkbare grootte te sorteren. Grote verschillen in bestandsgroottes kunnen tot slechtere prestaties leiden. Als het niet mogelijk is om bestanden op grootte te sorteren, dient u de PerFileThreadCount in te stellen op basis van het grootste bestand.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Voorbeeld:**

    Ervan uitgaande dat u 100 bestanden van 1 GB tot 10 GB hebt, gebruiken we de 10 GB als de grootste bestands grootte voor vergelijking, die er ongeveer als volgt uitziet.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Stap 3: Bereken ConcurrentFilecount** : gebruik het totale aantal threads en PerFileThreadCount om ConcurrentFilecount te berekenen op basis van de volgende vergelijking:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Voorbeeld:**

    We gaan uit van de gebruikte voorbeeldwaarden

    `96 = 40 * ConcurrentFileCount`

    De **ConcurrentFileCount** is **2,4**, wat we kunnen afronden naar **2**.

## <a name="further-tuning"></a>Verder afstemmen

Mogelijk is verder afstemmen nodig omdat u werkt met bestanden van uiteenlopende groottes. De voor gaande berekening werkt goed als alle of de meeste bestanden groter en dichter bij het bereik van 10 GB zijn. Als het echter bestanden van zeer uiteenlopende groottes betreft, waarbij veel bestanden kleiner zijn, kunt u de PerFileThreadCount verlagen. Als we de PerFileThreadCount verlagen, kunnen we de ConcurrentFileCount verhogen. Als we ervan uitgaan dat de meeste van onze bestanden kleiner zijn in het bereik van 5 GB, kunnen we de berekening opnieuw uitvoeren:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

**ConcurrentFileCount** wordt dus 96/20, dat wil zeggen 4,8, afgerond op **4**.

U kunt deze instellingen blijven aanpassen door de **PerFileThreadCount** te verhogen of te verlagen, afhankelijk van de verdeling van de bestandsgroottes.

### <a name="limitation"></a>Beperking

* **Het aantal bestanden is lager dan de ConcurrentFileCount**: als het aantal bestanden dat u uploadt lager is dan de **ConcurrentFileCount** die u hebt berekend, dient u de **ConcurrentFileCount** te verlagen tot deze gelijk is met het aantal bestanden. U kunt eventuele overblijvende threads gebruiken om de **PerFileThreadCount** te verhogen.

* **Te veel threads**: als u het aantal threads te veel verhoogt zonder dat u de grootte van uw cluster verhoogt, kan dit tot slechtere prestaties leiden. Er treden mogelijk conflicten op wanneer u van context wisselt op de CPU.

* **Onvoldoende gelijktijdigheid**: als de gelijktijdigheid onvoldoende is, is uw cluster mogelijk te klein. U kunt het aantal knoop punten in het cluster verg Roten, waardoor u gelijktijdig meer in het geding hebt.

* **Beperkingsfouten**: er treden mogelijk beperkingsfouten op als uw gelijktijdigheid te hoog is. Als er beperkingsfouten optreden, dient u uw gelijktijdigheid te verkleinen of contact met ons op te nemen.

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Lake Storage Gen1 gebruiken voor big data vereisten](data-lake-store-data-scenarios.md) 
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

