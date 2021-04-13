---
title: Gegevens stromen voor toewijzing controleren
description: Toewijzing van gegevens stromen visueel bewaken in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/11/2021
ms.openlocfilehash: 82aba428627cba1a3df26fc67c5da0cde52d368c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309064"
---
# <a name="monitor-data-flows"></a>Gegevens stromen bewaken

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nadat u klaar bent met het maken en opsporen van fouten in uw gegevens stroom, wilt u de gegevens stroom plannen om uit te voeren volgens een schema in de context van een pijp lijn. U kunt de pijp lijn plannen vanuit Azure Data Factory met behulp van triggers. U kunt ook de optie nu activeren van de opbouw functie voor het Azure Data Factory pijp lijn gebruiken om een uitvoering met één uitvoering uit te voeren om uw gegevens stroom in de pijplijn context te testen.

Wanneer u de pijp lijn uitvoert, kunt u de pijp lijn en alle activiteiten in de pijp lijn bewaken, met inbegrip van de activiteit gegevens stroom. Klik op het pictogram monitor in het deel venster aan de linkerkant Azure Data Factory gebruikers interface. U ziet een scherm zoals hieronder. Met de gemarkeerde pictogrammen kunt u inzoomen op de activiteiten in de pijp lijn, met inbegrip van de activiteit gegevens stroom.

![Scherm afbeelding toont pictogrammen die u voor pijp lijnen wilt selecteren voor meer informatie.](media/data-flow/mon001.png "Bewaking gegevensstroom")

U ziet de statistieken op dit niveau en ook de uitvoerings tijden en-status. De uitvoerings-ID op het activiteit niveau wijkt af van de uitvoerings-ID op het pijplijn niveau. De uitvoerings-ID op het vorige niveau is voor de pijp lijn. Als u de bril selecteert, krijgt u gedetailleerde informatie over de uitvoering van de gegevens stroom.

![Scherm afbeelding toont het bril pictogram om details van de uitvoering van de gegevens stroom weer te geven.](media/data-flow/monitoring-details.png "Bewaking gegevensstroom")

Wanneer u zich in de weer gave van het grafische knoop punt bevindt, ziet u een vereenvoudigde alleen-lezen versie van uw gegevens stroom diagram.

![Scherm afbeelding toont de alleen-lezen versie van de grafiek.](media/data-flow/mon003.png "Bewaking gegevensstroom")

Hier volgt een video overzicht van het controleren van de prestaties van uw gegevens stromen vanuit het scherm van de ADF-bewaking:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Uitvoerings plannen voor gegevens stromen weer geven

Wanneer uw gegevens stroom in Spark wordt uitgevoerd, bepaalt Azure Data Factory optimale code paden op basis van het geheel van uw gegevens stroom. Daarnaast kunnen de uitvoerings paden optreden op verschillende scale-out knoop punten en gegevens partities. Daarom vertegenwoordigt de bewakings grafiek het ontwerp van uw stroom, waarbij rekening wordt gehouden met het pad van de uitvoering van de trans formaties. Wanneer u afzonderlijke knoop punten selecteert, ziet u ' groeperingen ' die de code vertegenwoordigen die samen in het cluster is uitgevoerd. De tijds instellingen en aantallen die u ziet, worden weer gegeven in plaats van de afzonderlijke stappen in uw ontwerp.

![Scherm afbeelding toont de pagina voor een gegevens stroom.](media/data-flow/mon004.png "Bewaking gegevensstroom")

* Wanneer u in het venster bewaking de optie ruimte openen selecteert, worden in het onderste deel venster de timing en het aantal rijen weer gegeven voor elke Sink en de trans formaties die leiden tot de Sink-gegevens voor transformatie afkomst.

* Wanneer u afzonderlijke trans formaties selecteert, ontvangt u extra feedback over het rechterdeel venster waarin de partitie statistieken, kolom aantallen, scheefheid (hoe gelijkmatig de gegevens worden verdeeld over verschillende partities) en kurtosis (hoe piekige de gegevens zijn) worden weer gegeven.

* Wanneer u de sink in de knooppunt weergave selecteert, kunt u de kolom afkomst zien. Er zijn drie verschillende methoden die kolommen tijdens de gegevens stroom in de Sink worden verzameld. Dit zijn:

  * Berekend: u gebruikt de kolom voor voorwaardelijke verwerking of binnen een expressie in uw gegevens stroom, maar niet in de Sink
  * Afgeleid: de kolom is een nieuwe kolom die u in de stroom hebt gegenereerd, dat wil zeggen, deze is niet aanwezig in de bron
  * Toegewezen: de kolom afkomstig uit de bron en uw toewijzing is aan een Sink-veld gekoppeld
  * Status van de gegevens stroom: de huidige status van de uitvoering
  * Opstart tijd van het cluster: de hoeveelheid tijd voor het verkrijgen van de JIT Spark-Compute-omgeving voor het uitvoeren van gegevens stromen
  * Aantal trans formaties: hoeveel transformatie stappen worden uitgevoerd in uw stroom
  
![Scherm afbeelding toont de optie vernieuwen.](media/data-flow/monitornew.png "Gegevens stroom bewaking nieuw")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Totale verwerkingstijd voor sinks versus de verwerkingstijd voor transformaties

Elke transformatie fase bevat een totale tijd voor het volt ooien van de voltooiing van elke partitie voor de uitvoerings tijd. Wanneer u op de Sink klikt, ziet u ' sink verwerkings tijd '. Deze tijd omvat het totaal van de transformatie tijd *plus* de I/O-tijd die nodig was om uw gegevens naar uw doel archief te schrijven. Het verschil tussen de verwerkings tijd van de Sink en het totaal van de trans formatie is de I/O-tijd voor het schrijven van de gegevens.

U kunt ook gedetailleerde timing voor elke partitie transformatie stap bekijken als u de JSON-uitvoer van uw gegevens stroom activiteit opent in de bewakings weergave van de ADF-pijp lijn. De JSON bevat tijd voor milliseconden voor elke partitie. de UX-bewakings weergave is een cumulatieve timing van partities die samen worden toegevoegd:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="sink-processing-time"></a>Verwerkings tijd Sink

Wanneer u een Sink-transformatie pictogram in de kaart selecteert, wordt in het deel venster met de rechter muisknop een extra gegevens punt weer gegeven met de naam ' verwerkings tijd na de onderkant. Dit is de hoeveelheid tijd die nodig is om uw taak uit te voeren op het Spark-cluster *nadat* uw gegevens zijn geladen, getransformeerd en geschreven. Deze tijd kan bestaan uit het sluiten van verbindings Pools, het afsluiten van Stuur Programma's, het verwijderen van bestanden, het samen voegen van bestanden, enzovoort. Wanneer u acties in uw stroom uitvoert, zoals ' bestanden verplaatsen ' en ' uitvoer naar één bestand ', ziet u waarschijnlijk een toename in de waarde voor de verwerkings tijd.

* Duur van schrijf fase: de tijd voor het schrijven van de gegevens naar een faserings locatie voor Synapse SQL
* SQL-duur tabel bewerking: de tijd die is besteed aan het verplaatsen van gegevens van tijdelijke tabellen naar de doel tabel
* De duur van de eerste SQL-& na de SQL-duur: de tijd die nodig is voor het uitvoeren van opdrachten
* De duur van de opdrachten voor het & van de opdracht post opdrachten is de tijd die is besteed aan het uitvoeren van een vooraf/post-bewerking voor op bestanden gebaseerde bron/sinks Verplaats of verwijder bijvoorbeeld bestanden na de verwerking.
* Samenvoeg duur: de tijd die is besteed aan het samen voegen van het bestand, het samen voegen van bestanden die worden gebruikt voor op bestanden gebaseerde sinks bij het schrijven naar één bestand of wanneer ' bestands naam als kolom gegevens ' wordt gebruikt. Als er een belang rijke tijd wordt besteed aan deze metrische gegevens, moet u deze opties beter niet gebruiken.
  
## <a name="error-rows"></a>Fout rijen

Het inschakelen van de verwerking van fout rijen in uw gegevens stroom sink wordt weer gegeven in de uitvoer van de bewaking. Wanneer u de Sink instelt op ' rapport geslaagd op fout ', wordt in de bewakings uitvoer het aantal geslaagde en mislukte rijen weer gegeven wanneer u op het knoop punt Sink-bewaking klikt.

![De scherm opname bevat fout rijen.](media/data-flow/error-row-2.png "Bewaking in fout rijen geslaagd")

Wanneer u ' rapport fout bij fout ' selecteert, wordt dezelfde uitvoer alleen weer gegeven in de uitvoer tekst voor activiteiten bewaking. Dit komt doordat de activiteit gegevens stroom een fout retourneert voor de uitvoering en de gedetailleerde bewakings weergave niet beschikbaar is.

![De scherm opname bevat fout rijen in de activiteit.](media/data-flow/error-rows-4.png "Fout bij het controleren van een actierij")

## <a name="monitor-icons"></a>Bewaak pictogrammen

Dit pictogram geeft aan dat de transformatie gegevens al in de cache zijn opgeslagen op het cluster, waardoor de timing en het uitvoerings traject hebben geduurd in het account:

![Scherm afbeelding toont het schijf pictogram.](media/data-flow/mon005.png "Bewaking gegevensstroom")

U ziet ook groene cirkel pictogrammen in de trans formatie. Ze vertegenwoordigen een telling van het aantal sinks dat de gegevens stromen.
