---
title: Bestanden kopiëren vanuit meerdere containers
description: Meer informatie over het gebruik van een oplossings sjabloon voor het kopiëren van bestanden uit meerdere containers met behulp van Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: ec7af1e81e0b295491420597636c8443f4d36512
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376085"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Meerdere mappen kopiëren met Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt een oplossings sjabloon beschreven waarmee u meerdere Kopieer activiteiten kunt gebruiken om containers of mappen te kopiëren tussen op bestanden gebaseerde winkels, waarbij elke Kopieer activiteit één container of map moet kopiëren. 

> [!NOTE]
> Als u bestanden wilt kopiëren uit één container, is het efficiënter om met het [hulp programma gegevens kopiëren](copy-data-tool.md) een pijp lijn te maken met één Kopieer activiteit. De sjabloon in dit artikel is meer dan u nodig hebt voor dat eenvoudige scenario.

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Met deze sjabloon worden de mappen opgesomd van een bepaalde bovenliggende map in uw bron opslag archief. Vervolgens worden alle mappen naar het doel archief gekopieerd.

De sjabloon bevat drie activiteiten:
- **GetMetadata** scant uw opslag opslag en haalt de lijst met submappen op uit een bepaalde bovenliggende map.
- **Foreach** haalt de lijst met submappen op uit de **GetMetadata** -activiteit en herhaalt vervolgens de lijst en geeft elke map door aan de Kopieer activiteit.
- **Copy** kopieert elke map uit het opslag archief van de bron naar het doel archief.

Met de sjabloon worden de volgende parameters gedefinieerd:
- *SourceFileFolder* maakt deel uit van het pad naar de bovenliggende map van uw gegevens bron archief: *SourceFileFolder/SourceFileDirectory*, waar u een lijst van de submappen kunt ophalen. 
- *SourceFileDirectory* maakt deel uit van het pad naar de bovenliggende map van uw gegevens bron archief: *SourceFileFolder/SourceFileDirectory*, waar u een lijst van de submappen kunt ophalen. 
- *DestinationFileFolder* maakt deel uit van het pad naar de bovenliggende map: *DestinationFileFolder/DestinationFileDirectory* waar de bestanden worden gekopieerd naar uw doel archief. 
- *DestinationFileDirectory* maakt deel uit van het pad naar de bovenliggende map: *DestinationFileFolder/DestinationFileDirectory* waar de bestanden worden gekopieerd naar uw doel archief. 

Als u meerdere containers wilt kopiëren onder hoofd mappen tussen opslag archieven, kunt u alle vier de para meters invoeren als */* . Hierdoor repliceert u alles tussen opslag archieven.

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Ga naar het **bestand containers met meerdere bestanden kopiëren tussen** de sjabloon voor het opslaan van bestanden. Een **nieuwe** verbinding maken met uw bron opslag archief. In het bron opslag archief kunt u bestanden uit meerdere containers kopiëren.

    ![Een nieuwe verbinding maken met de bron](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Maak een **nieuwe** verbinding naar uw doel opslag archief.

    ![Een nieuwe verbinding maken met de bestemming](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecteer **deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. U ziet de pijp lijn, zoals in het volgende voor beeld:

    ![De pijp lijn weer geven](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecteer **debug**, voer de **para meters** in en selecteer **volt ooien**.

    ![De pijplijn uitvoeren](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Bekijk het resultaat.

    ![Bekijk het resultaat](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Volgende stappen

- [Bulksgewijs kopiëren van een Data Base met behulp van een controle tabel met Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)
