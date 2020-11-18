---
title: Venster transformatie in gegevens stroom toewijzen
description: Trans formatie van gegevens stroom venster Azure Data Factory toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/16/2020
ms.openlocfilehash: 8f0d10b6ed69cd31249447b59114c590bdbeb078
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832361"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Venster transformatie in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de venster transformatie definieert u op venster gebaseerde aggregaties van kolommen in uw gegevens stromen. In de opbouw functie voor expressies kunt u verschillende typen aggregaties definiëren die zijn gebaseerd op gegevens of tijd Vensters (SQL OVER component) zoals LEAD, LAG, NTILE, CUMEDIST, RANK, enzovoort. Er wordt een nieuw veld in uw uitvoer gegenereerd dat deze aggregaties bevat. U kunt ook optionele velden voor groeperen op toevoegen.

![Scherm afbeelding toont het venster dat u hebt geselecteerd in het menu.](media/data-flow/windows1.png "Windows 1")

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Verloop
De partitionering van kolom gegevens voor uw venster transformatie instellen. Het equivalent van SQL is de ```Partition By``` in de component over in SQL. Als u een berekening wilt maken of een expressie wilt maken die voor de partitionering moet worden gebruikt, kunt u dit doen door de muis aanwijzer over de kolom naam te bewegen en "berekende kolom" te selecteren.

![Scherm afbeelding toont de instellingen voor het venster op het tabblad boven geselecteerd.](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Sorteren
Een ander deel van de component over is het instellen van de ```Order By``` . Hiermee wordt de volg orde van de sortering van gegevens ingesteld. U kunt ook een expressie maken voor een waarde berekenen in dit kolom veld voor het sorteren.

![Scherm afbeelding toont de venster instellingen op het tabblad sorteren geselecteerd.](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Bereik op
Stel vervolgens het venster kader in op niet-gebonden of gebonden. Als u een niet-gebonden venster kader wilt instellen, stelt u de schuif regelaar in op beide uiteinden. Als u een instelling selecteert tussen de niet-gebonden en de huidige rij, moet u de begin-en eind waarde van de offset instellen. Beide waarden moeten positieve gehele getallen zijn. U kunt de relatieve cijfers of waarden van uw gegevens gebruiken.

De schuif regelaar voor het venster heeft twee waarden die moeten worden ingesteld: de waarden vóór de huidige rij en de waarden na de huidige rij. De begin-en eind offset komen overeen met de twee selecters op de schuif regelaar.

![Scherm afbeelding toont de venster instellingen met het tabblad bereik per geselecteerd.](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Venster kolommen
Gebruik ten slotte de opbouw functie voor expressies om de aggregaties te definiëren die u wilt gebruiken met de gegevens Vensters zoals positie, aantal, MIN, MAX, hoge rang orde, LEAD, vertraging enzovoort.

![Scherm afbeelding toont het resultaat van de venster actie.](media/data-flow/windows7.png "Windows 7")

De volledige lijst met aggregatie-en analytische functies die u kunt gebruiken in de taal van de ADF-gegevens stroom expressie via de opbouw functie voor expressies vindt u hier: https://aka.ms/dataflowexpressions .

## <a name="next-steps"></a>Volgende stappen

Als u op zoek bent naar een eenvoudige groep-op aggregatie, gebruikt u de [cumulatieve trans formatie](data-flow-aggregate.md)
