---
title: Bestanden verplaatsen tussen opslag op basis van bestanden
description: Meer informatie over hoe u een oplossings sjabloon gebruikt om bestanden te verplaatsen tussen opslag op basis van een bestand met behulp van Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: c88f2d25046ee017fccd2cee6e951be72d4dda91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361941"
---
# <a name="move-files-with-azure-data-factory"></a>Bestanden verplaatsen met Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

De ADF Copy-activiteit heeft ingebouwde ondersteuning voor het scenario ' verplaatsen ' bij het kopiëren van binaire bestanden tussen opslag archieven.  De manier om deze in te scha kelen, is door ' deleteFilesAfterCompletion ' in te stellen als waar in de Kopieer activiteit. Als u dit doet, worden bestanden uit de gegevens bron opslag verwijderd nadat de taak is voltooid. 

In dit artikel wordt een oplossings sjabloon beschreven als een andere benadering van de flexibele controle stroom van ADF en een Kopieer activiteit en het verwijderen van activiteiten om hetzelfde scenario te bereiken. Een van de veelvoorkomende scenario's voor het gebruik van deze sjabloon: bestanden worden doorlopend verplaatst naar een opslagmap in uw bron archief. Door een schema trigger te maken, kunt u deze bestanden regel matig van de bron naar het doel archief verplaatsen.  Op basis van de manier waarop ADF-pijp lijn ' bestanden verplaatsen ' krijgt, worden de bestanden opgehaald uit de map voor de overloop, worden ze gekopieerd naar een andere map in het doel archief en worden de bestanden vervolgens verwijderd uit de map voor het land van het bron archief.

> [!NOTE]
> Houd er rekening mee dat deze sjabloon is ontworpen om bestanden te verplaatsen in plaats van mappen te verplaatsen.  Als u de map wilt verplaatsen door de gegevensset zodanig te wijzigen dat deze alleen een mappad bevat en vervolgens met behulp van de Kopieer activiteit en activiteit verwijderen om te verwijzen naar dezelfde gegevensset die een map vertegenwoordigt, moet u zeer voorzichtig zijn. Dit komt omdat u ervoor moet zorgen dat er geen nieuwe bestanden in de map arriveren tussen het kopiëren en het verwijderen van de bewerking. Als er nieuwe bestanden binnenkomen in de map op het moment dat u de Kopieer activiteit zojuist hebt voltooid, maar de Delete-activiteit niet is gesterd, is het mogelijk dat de Delete-activiteit dit nieuwe aankomende bestand verwijdert dat nog niet naar de bestemming is gekopieerd. dit doet u door de volledige map te verwijderen.

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Met deze sjabloon worden de bestanden uit uw op bron bestand gebaseerde opslag opgehaald. Vervolgens wordt elk van deze verplaatst naar het doel archief.

De sjabloon bevat vijf activiteiten:
- **GetMetadata** haalt de lijst met objecten op, inclusief de bestanden en submappen uit uw map in de bron Store. De objecten worden niet recursief opgehaald. 
- **Filter** filter de objecten lijst van de **GetMetadata** -activiteit om alleen de bestanden te selecteren. 
- **Foreach** haalt de lijst met bestanden op uit de **filter** activiteit en herhaalt vervolgens de lijst en geeft elk bestand door aan de activiteit kopiëren en verwijderen.
- **Copy** kopieert één bestand van de bron naar het doel archief.
- Met **verwijderen** wordt hetzelfde bestand uit het bron archief verwijderd.

De sjabloon definieert vier para meters:
- *SourceStore_Location* is het mappad van het bron archief waaruit u bestanden wilt verplaatsen. 
- *SourceStore_Directory* is het pad naar de submap van de bron opslag waaruit u bestanden wilt verplaatsen.
- *DestinationStore_Location* is het mappad van het doel archief waarnaar u bestanden wilt verplaatsen. 
- *DestinationStore_Directory* is het pad naar de submap van het doel archief waarnaar u bestanden wilt verplaatsen.

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Ga naar de sjabloon **bestanden verplaatsen** . Selecteer bestaande verbinding of maak een **nieuwe** verbinding met de bron bestands opslag waarvan u bestanden wilt verplaatsen. Houd er rekening mee dat **DataSource_Folder** en **DataSource_File** verwijzen naar dezelfde verbinding als de bron bestands opslag.

    ![Een nieuwe verbinding maken met de bron](media/solution-template-move-files/move-files1.png)

2. Selecteer bestaande verbinding of maak een **nieuwe** verbinding met uw doel bestands archief waarnaar u bestanden wilt verplaatsen.

    ![Een nieuwe verbinding maken met de bestemming](media/solution-template-move-files/move-files2.png)

3. Selecteer **deze sjabloon tabblad gebruiken** .
    
4. U ziet de pijp lijn, zoals in het volgende voor beeld:

    ![De pijp lijn weer geven](media/solution-template-move-files/move-files4.png)

5. Selecteer **debug**, voer de **para meters** in en selecteer **volt ooien**.   De para meters zijn het pad naar de map waarnaar u bestanden wilt verplaatsen en het mappad waarnaar u bestanden wilt verplaatsen. 

    ![De pijplijn uitvoeren](media/solution-template-move-files/move-files5.png)

6. Bekijk het resultaat.

    ![Bekijk het resultaat](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Volgende stappen

- [Nieuwe en gewijzigde bestanden kopiëren met behulp van LastModifiedDate met Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)