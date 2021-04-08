---
title: big data catalogiseren in Azure Data Catalog
description: Instructies voor het markeren van patronen voor het gebruik van Azure Data Catalog met big data gegevens bronnen, waaronder Azure Blob Storage, Azure Data Lake en Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 3e898542c25f6ed1a9e6c811ada47220733abb12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674662"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>big data catalogiseren in Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introductie

**Microsoft Azure Data Catalog** is een volledig beheerde Cloud service die fungeert als registratie systeem en detectie systeem voor zakelijke gegevens bronnen. Het helpt mensen bij het detecteren, begrijpen en gebruiken van gegevens bronnen, en helpt organisaties bij het verkrijgen van meer waarde dan hun bestaande gegevens bronnen, waaronder big data.

**Azure Data Catalog** ondersteunt de registratie van Azure Storage blobs en directory's, evenals Hadoop HDFS-bestanden en-mappen. De semi-gestructureerde aard van deze gegevens bronnen biedt een grote flexibiliteit. Om de meeste waarde te verkrijgen om ze te registreren bij **Azure Data Catalog**, moeten gebruikers overwegen hoe de gegevens bronnen worden georganiseerd.

## <a name="directories-as-logical-data-sets"></a>Directory's als logische gegevens sets

Een gemeen schappelijk patroon voor het organiseren van big data bronnen is het behandelen van directory's als logische gegevens sets. Directory's op het hoogste niveau worden gebruikt voor het definiëren van een gegevensset, terwijl submappen partities definiëren en de bestanden die ze bevatten, de gegevens zelf opslaan.

Een voor beeld van dit patroon is:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

In dit voor beeld vehicle_maintenance_events en location_tracking_events logische gegevens sets vertegenwoordigen. Elk van deze mappen bevat gegevens bestanden die zijn ingedeeld per jaar en maand in submappen. Elk van deze mappen zou mogelijk honderden of duizenden bestanden kunnen bevatten.

In dit patroon is het niet zinvol om afzonderlijke bestanden met **Azure Data Catalog** te registreren. Registreer in plaats daarvan de mappen die de gegevens sets vertegenwoordigen die zinvol zijn voor gebruikers die met de gegevens werken.

## <a name="reference-data-files"></a>Referentie gegevens bestanden

Een complementair patroon is het opslaan van referentie gegevens sets als afzonderlijke bestanden. Deze gegevens sets kunnen worden beschouwd als de ' kleine ' kant van big data en zijn vaak vergelijkbaar met dimensies in een analytisch gegevens model. Referentie gegevens bestanden bevatten records die worden gebruikt om de context te bieden voor het meren deel van de gegevens bestanden die elders in het big data archief worden opgeslagen.

Een voor beeld van dit patroon is:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Wanneer een analist of gegevens wetenschapper werkt met de gegevens in de grotere mapstructuren, kunnen de gegevens in deze referentie bestanden worden gebruikt om meer gedetailleerde informatie te geven over entiteiten waarnaar wordt verwezen door de naam of ID in de grotere gegevensset.

In dit patroon is het zinvol om de afzonderlijke referentie gegevensbestand bestanden met **Azure Data Catalog** te registreren. Elk bestand vertegenwoordigt een gegevensset, en elke naam kan afzonderlijk worden aantekend en gedetecteerd.

## <a name="alternate-patterns"></a>Alternatieve patronen

De patronen die in de voor gaande sectie zijn beschreven, zijn twee mogelijke manieren waarop een big data archief kan worden georganiseerd, maar elke implementatie verschilt. Ongeacht hoe uw gegevens bronnen zijn gestructureerd, kunt u bij het registreren van big data bronnen met **Azure Data Catalog** zich richten op het registreren van de bestanden en mappen die de gegevens sets vertegenwoordigen die van waarde zijn voor anderen binnen uw organisatie. Het registreren van alle bestanden en mappen kan de catalogus opruimen, zodat gebruikers moeilijker kunnen vinden wat ze nodig hebben.

## <a name="summary"></a>Samenvatting

Het registreren van gegevens bronnen met **Azure Data Catalog** maakt het gemakkelijker om ze te detecteren en te begrijpen. Door de big data-bestanden en-mappen die logische gegevens sets vertegenwoordigen, te registreren en aantekeningen te maken, kunt u gebruikers helpen de big data bronnen te vinden die ze nodig hebben.
