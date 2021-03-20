---
title: 'Dubbele rijen verwijderen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module dubbele rijen verwijderen in Azure Machine Learning om mogelijke dubbele items uit een gegevensset te verwijderen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: bf35d08128aa8a3e8f545ed7184866694219f2cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905214"
---
# <a name="remove-duplicate-rows-module"></a>Module dubbele rijen verwijderen

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om potentiële duplicaten uit een gegevensset te verwijderen.

Stel dat uw gegevens er als volgt uitzien en dat er meerdere records worden aangeduid voor patiënten. 

| PatientID | Initialen| Geslacht|Leeftijd|Stoffen|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb|
|4| F.M.| M| 23| Feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| Geen getal| |
|7| F.A.M.| M| Geen getal| |

Het is duidelijk dat dit voor beeld meerdere kolommen bevat met mogelijk dubbele gegevens. Of ze daad werkelijk worden gedupliceerd, is afhankelijk van uw kennis van de gegevens. 

+ U kunt bijvoorbeeld weten dat veel patiënten dezelfde naam hebben. U kunt dubbele waarden niet elimineren met behulp van naam kolommen, alleen de kolom **id** . Op die manier worden alleen de rijen met dubbele ID-waarden gefilterd, ongeacht of de patiënten dezelfde naam hebben of niet.

+ U kunt er ook voor kiezen om dubbele waarden toe te staan in het veld ID en een andere combi natie van bestanden te gebruiken om unieke records te zoeken, zoals de voor naam, achternaam, leeftijd en geslacht.  

Als u de criteria wilt instellen voor of een rij dubbel of niet is, geeft u één kolom of een set kolommen op die moet worden gebruikt als **sleutel**. Twee rijen worden alleen als dubbele waarden beschouwd als **alle** sleutel kolommen gelijk zijn. Als een rij de waarde voor **sleutels** mist, worden deze niet als dubbele rijen beschouwd. Als geslacht en leeftijd bijvoorbeeld zijn ingesteld als sleutels in de bovenstaande tabel, zijn rij 6 en 7 geen dubbele rijen, aangezien de waarde voor de leeftijd ontbreekt.

Wanneer u de module uitvoert, wordt er een gegevensset voor kandidaten gemaakt en wordt een set rijen geretourneerd die geen duplicaten bevat in de opgegeven set kolommen.

> [!IMPORTANT]
> De bron-gegevensset is niet gewijzigd. in deze module wordt een nieuwe gegevensset gemaakt die wordt gefilterd om dubbele waarden uit te sluiten, op basis van de criteria die u opgeeft.

## <a name="how-to-use-remove-duplicate-rows"></a>Dubbele rijen verwijderen gebruiken

1. Voeg de module toe aan de pijp lijn. U kunt de module **dubbele rijen verwijderen** vinden onder **gegevens transformatie**, **bewerken**.  

2. Verbind de gegevensset die u wilt controleren op dubbele rijen.

3. Klik in het deel venster **Eigenschappen** onder **filter expressie voor sleutel kolommen** **selecteren op kolom kiezer starten** om kolommen te kiezen die u wilt gebruiken bij het identificeren van duplicaten.

    In deze context betekent de **sleutel** geen unieke id. Alle kolommen die u selecteert met behulp van de kolom kiezer, worden aangeduid als **sleutel kolommen**. Alle niet-geselecteerde kolommen worden beschouwd als niet-sleutel kolommen. De combi natie van kolommen die u als sleutels selecteert, bepaalt de unieke aanduiding van de records. (Dit kan worden beschouwd als een SQL-instructie die gebruikmaakt van meerdere equals-samen voegingen.)

    Voorbeelden:

    + "Ik wil ervoor zorgen dat de Id's uniek zijn": Kies alleen de ID-kolom.
    + "Ik wil ervoor zorgen dat de combi natie van voor naam, achternaam en ID uniek is": Selecteer alle drie de kolommen.

4. Gebruik het selectie vakje **eerste dubbele rij bewaren** om aan te geven welke rij moet worden geretourneerd wanneer dubbele waarden worden gevonden:

    + Als deze is geselecteerd, wordt de eerste rij geretourneerd en anderen verwijderd. 
    + Als u deze optie uitschakelt, wordt de laatste dubbele rij in de resultaten bewaard en anderen verwijderd. 

5. Verzend de pijp lijn.

6. Als u de resultaten wilt bekijken, klikt u met de rechter muisknop op de module en selecteert u **visualiseren**. 

> [!TIP]
> Als de resultaten moeilijk te begrijpen zijn, of als u wilt voor komen dat sommige kolommen van overweging worden genomen, kunt u kolommen verwijderen met de module [kolommen in gegevensset selecteren](./select-columns-in-dataset.md) .

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 