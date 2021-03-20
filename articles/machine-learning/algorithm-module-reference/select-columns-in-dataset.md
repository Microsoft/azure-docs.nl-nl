---
title: 'Kolommen selecteren in DataSet: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module select columns in dataset in Azure Machine Learning om een subset van kolommen te kiezen die moeten worden gebruikt in downstream-bewerkingen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 7926d5dd33df5538713f8de152dbbace2bc29864
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890289"
---
# <a name="select-columns-in-dataset-module"></a>Kolommen in de module gegevensset selecteren

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een subset van kolommen te kiezen die moeten worden gebruikt in downstream-bewerkingen. De-module verwijdert de kolommen niet fysiek uit de gegevensset van de bron. in plaats daarvan wordt er een subset met kolommen gemaakt, zoals een database *weergave* of *projectie*.

Deze module is handig als u de beschik bare kolommen voor een stroomafwaartse bewerking wilt beperken of als u de grootte van de gegevensset wilt beperken door overbodige kolommen te verwijderen.

De kolommen in de gegevensset worden uitgevoerd in dezelfde volg orde als in de oorspronkelijke gegevens, zelfs als u deze in een andere volg orde opgeeft.

## <a name="how-to-use"></a>Gebruik

Deze module heeft geen para meters. U gebruikt de kolom kiezer om de kolommen te kiezen die u wilt opnemen of uitsluiten.

### <a name="choose-columns-by-name"></a>Kolommen op naam kiezen

De module bevat meerdere opties voor het kiezen van kolommen op naam: 

+ Filteren en zoeken

    Klik op de optie **op naam** .

    Als u een gegevensset hebt verbonden die al is gevuld, wordt een lijst met beschik bare kolommen weer gegeven. Als er geen kolommen worden weer gegeven, moet u mogelijk upstream-modules uitvoeren om de kolom lijst weer te geven.

    Als u de lijst wilt filteren, typt u in het zoekvak. Als u bijvoorbeeld de letter `w` in het zoekvak typt, wordt de lijst gefilterd om de kolom namen weer te geven die de letter bevatten `w` .

    Selecteer kolommen en klik op de pijl naar rechts om de geselecteerde kolommen te verplaatsen naar de lijst in het rechterdeel venster.

    + Als u een doorlopend bereik van kolom namen wilt selecteren, drukt u op **Shift + Klik**.
    + Als u afzonderlijke kolommen wilt toevoegen aan de selectie, drukt u op **CTRL + klik**.

    Klik op de knop voor het selectie vakje om op te slaan en te sluiten.

+ Namen gebruiken in combi natie met andere regels

    Klik op de optie **with Rules** .
    
    Kies een regel, bijvoorbeeld om kolommen van een specifiek gegevens type weer te geven.

    Klik vervolgens op afzonderlijke kolommen van het type op naam om ze toe te voegen aan de selectie lijst.

+ Typ of plak een door komma's gescheiden lijst met kolom namen

    Als uw gegevensset breed is, is het eenvoudiger om indexen of gegenereerde lijsten met namen te gebruiken in plaats van de kolommen afzonderlijk te selecteren. Ervan uitgaande dat u de lijst van tevoren hebt voor bereid:

    1. Klik op de optie **with Rules** . 
    2. Selecteer **geen kolommen**, selecteer  **insluiten** en klik in het tekstvak met het rode uitroep teken. 
    3. Plak of typ een door komma's gescheiden lijst met eerder gevalideerde kolom namen. U kunt de module niet opslaan als een kolom een ongeldige naam heeft. Zorg er dus voor dat u de namen van tevoren controleert.
    
    U kunt deze methode ook gebruiken om een lijst met kolommen op te geven met behulp van de index waarden. 

### <a name="choose-by-type"></a>Kiezen op type

Als u de optie **with Rules** gebruikt, kunt u meerdere voor waarden Toep assen op de kolom selecties. Zo moet u mogelijk alleen functie kolommen van een numeriek gegevens type ophalen.

MET de optie **begin met** wordt het begin punt bepaald en is het belang rijk om de resultaten te weten. 

+ Als u de optie **alle kolommen** selecteert, worden alle kolommen toegevoegd aan de lijst. Vervolgens moet u de optie **uitsluiten** gebruiken om kolommen te *verwijderen* die voldoen aan bepaalde voor waarden. 

    U kunt bijvoorbeeld beginnen met alle kolommen en vervolgens kolommen op naam of op type verwijderen.

+ Als u de optie **geen kolommen** selecteert, wordt de lijst met kolommen leeg gelaten. Vervolgens geeft u voor waarden op om kolommen toe te *voegen* aan de lijst. 

    Als u meerdere regels toepast, is elke voor waarde **additief**. Stel bijvoorbeeld dat u begint met geen kolommen en voeg vervolgens een regel toe om alle numerieke kolommen op te halen. In de gegevensset voor auto Mobile-prijzen resulteert dat in 16 kolommen. Vervolgens klikt u op het- **+** teken om een nieuwe voor waarde toe te voegen en selecteert u **alle functies** toevoegen. De resulterende gegevensset bevat alle numerieke kolommen, plus alle functie kolommen, met inbegrip van sommige kolommen met teken reeks functies.

### <a name="choose-by-column-index"></a>Kiezen op kolom index

De kolom index verwijst naar de volg orde van de kolom in de oorspronkelijke gegevensset.

+ Kolommen worden opeenvolgend genummerd vanaf 1.  
+ Als u een bereik van kolommen wilt ophalen, gebruikt u een koppel teken. 
+ Open-end specificaties zoals `1-` of `-3` zijn niet toegestaan.
+ Dubbele index waarden (of kolom namen) zijn niet toegestaan en kunnen resulteren in een fout.

Als uw gegevensset bijvoorbeeld ten minste acht kolommen bevat, kunt u in een van de volgende voor beelden plakken om meerdere niet-aaneengesloten kolommen te retour neren: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

het laatste voor beeld resulteert niet in een fout. Er wordt echter slechts één exemplaar van de kolom geretourneerd `4` .



### <a name="change-order-of-columns"></a>Volg orde van kolommen wijzigen

De optie **dubbele waarden toestaan en kolom volgorde in selectie behouden** begint met een lege lijst en voegt kolommen toe die u opgeeft op naam of index. In tegens telling tot andere opties, waarmee altijd kolommen worden geretourneerd in hun ' natuurlijke volg orde ', wordt met deze optie de kolommen in de volg orde weer gegeven waarin u de naam van de kolom maakt of vermeldt. 

In een gegevensset met de kolommen Kol1, col2, Col3 en Col4 kunt u bijvoorbeeld de volg orde van de kolommen ongedaan maken en kolom 2 verlaten door een van de volgende lijsten op te geven:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 