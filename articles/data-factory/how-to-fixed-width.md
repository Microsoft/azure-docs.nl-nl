---
title: Tekst bestanden met een vaste lengte verwerken met toewijzing van gegevens stromen in Azure Data Factory
description: Meer informatie over het verwerken van tekst bestanden met een vaste lengte in Azure Data Factory met toewijzing van gegevens stromen.
author: balakreshnan
ms.service: data-factory
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 2297364e8f1a531b4512f2497bc4d99963d61179
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386200"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Tekst bestanden met een vaste lengte verwerken met Data Factory gegevens stromen toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met toewijzing van gegevens stromen in Microsoft Azure Data Factory kunt u gegevens van tekst bestanden met vaste breedte transformeren. In de volgende taak definiëren we een gegevensset voor een tekst bestand zonder scheidings teken en vervolgens subtekenreeks splitsen op basis van ordinale positie.

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer **+ nieuwe pijp lijn** om een nieuwe pijp lijn te maken.

2. Voeg een gegevens stroom activiteit toe die wordt gebruikt voor het verwerken van bestanden met een vaste breedte:

    ![Pijp lijn met vaste breedte](media/data-flow/fwpipe.png)

3. Selecteer in de activiteit gegevens stroom de optie **nieuwe toewijzings gegevens stroom**.

4. Voeg een bron, afgeleide kolom, Select en Sink-trans formatie toe:

    ![Gegevens stroom met vaste breedte](media/data-flow/fw2.png)

5. Configureer de bron transformatie voor het gebruik van een nieuwe gegevensset, die van het tekst type met scheidings tekens is.

6. Stel geen kolom scheidings teken of kopteksten in.

   Nu gaan we de begin punten en lengten van het veld instellen voor de inhoud van dit bestand:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Op het tabblad **projectie** van de bron transformatie ziet u een teken reeks kolom met de naam *Column_1*.

8. Maak een nieuwe kolom in de afgeleide kolom.

9. We geven de kolommen eenvoudige namen zoals *Kol1*.

10. Typ in de opbouw functie voor expressies het volgende:

    ```substring(Column_1,1,4)```

    ![afgeleide kolom](media/data-flow/fwderivedcol1.png)

11. Herhaal stap 10 voor alle kolommen die u wilt parseren.

12. Selecteer het tabblad **controleren** om de nieuwe kolommen te zien die worden gegenereerd:

    ![controleert](media/data-flow/fwinspect.png)

13. Gebruik de Select Transform om een van de kolommen te verwijderen die u niet nodig hebt voor trans formatie:

    ![trans formatie selecteren](media/data-flow/fwselect.png)

14. Sink gebruiken om de gegevens naar een map uit te voeren:

    ![Sink met vaste breedte](media/data-flow/fwsink.png)

    De uitvoer ziet er als volgt uit:

    ![uitvoer met een vaste breedte](media/data-flow/fxdoutput.png)

  De gegevens met een vaste breedte worden nu gesplitst, met vier tekens elk en worden toegewezen aan Kol1, col2, Col3, Col4, enzovoort. Op basis van het vorige voor beeld worden de gegevens in vier kolommen gesplitst.

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met behulp van trans [formaties](concepts-data-flow-overview.md)voor gegevens stromen.
