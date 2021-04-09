---
title: 'Gesplitste gegevens: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module gegevens splitsen in Azure Machine Learning om een gegevensset in twee verschillende sets te verdelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907822"
---
# <a name="split-data-module"></a>Module voor splitsen van gegevens

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik de module Split data om een gegevensset te verdelen in twee afzonderlijke sets.

Deze module is handig wanneer u gegevens moet scheiden in trainings-en test sets. U kunt ook de manier aanpassen waarop de gegevens worden verdeeld. Sommige opties bieden ondersteuning voor wille keurige gegevens. Andere zijn aangepast aan een bepaald gegevens type of model type.

## <a name="configure-the-module"></a>De module configureren

> [!TIP]
> Lees alle opties voordat u de Splits modus kiest om te bepalen welk type splitsing u nodig hebt.
> Als u de Splits modus wijzigt, worden alle andere opties mogelijk opnieuw ingesteld.

1. Voeg de module **gegevens splitsen** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **gegevens transformatie** in de categorie voor **Beeld en splitsen** .

1. **Splits modus**: Kies een van de volgende modi, afhankelijk van het type gegevens dat u hebt en hoe u deze wilt delen. Elke Splits modus heeft verschillende opties.

   - **Rijen splitsen**: gebruik deze optie als u de gegevens wilt splitsen in twee delen. U kunt opgeven welk percentage van de gegevens in elke splitsing moet worden geplaatst. Standaard worden de gegevens gedeeld 50/50.

     U kunt ook de selectie van rijen in elke groep wille keurig opwaarderen en stratified-steek proeven gebruiken. In stratified-steek proeven moet u één kolom met gegevens selecteren waarvoor u wilt dat waarden gelijkmatig worden verdeeld over de twee resultaat gegevens sets.  

   - **Gesplitste expressie**: Kies deze optie als u de gegevensset wilt verdelen door één kolom te testen op een waarde.

     Als u bijvoorbeeld sentiment analyseert, kunt u controleren op de aanwezigheid van een bepaalde product naam in een tekst veld. U kunt de gegevensset vervolgens onderverdelen in rijen met de doel product naam en-rijen zonder de naam van het doel product.

   - **Relatieve expressie splitsen**: gebruik deze optie wanneer u een voor waarde op een kolom getal wilt Toep assen. Het getal kan een datum/tijd-veld, een kolom met ouderdoms-of dollar bedragen of zelfs een percentage zijn. U kunt bijvoorbeeld uw gegevensset verdelen op basis van de kosten van de items, personen groeperen op leeftijds bereiken of gegevens scheiden door een kalender datum.

### <a name="split-rows"></a>Rijen splitsen

1. Voeg de module [gegevens splitsen](./split-data.md) toe aan uw pijp lijn in de ontwerp functie en verbind de gegevensset die u wilt splitsen.
  
1. Voor de **Splits modus** selecteert u **rijen splitsen**. 

1. **Fractie van rijen in de eerste uitvoer gegevensset**: gebruik deze optie om te bepalen hoeveel rijen er worden weer gegeven in de eerste (linker) uitvoer. Alle andere rijen worden naar de tweede (rechter kant) uitvoer.

   De ratio vertegenwoordigt het percentage rijen dat wordt verzonden naar de eerste uitvoer gegevensset, dus u moet een decimaal getal tussen 0 en 1 invoeren.
     
   Als u bijvoorbeeld **0,75** opgeeft als waarde, wordt de gegevensset gesplitst 75/25. In deze splitsing wordt 75 procent van de rijen naar de eerste uitvoer gegevensset verzonden. De resterende 25 procent wordt naar de tweede uitvoer gegevensset verzonden.
  
1. Selecteer de optie **wille keurig splitsen** als u selectie van gegevens wilt wille keurig in de twee groepen. Dit is de voorkeurs optie bij het maken van gegevens sets voor training en testen.

1. **Wille keurige Seed**: Voer een niet-negatief geheel getal in om de Pseudorandom reeks instanties te starten die moeten worden gebruikt. Deze standaard seeding wordt gebruikt in alle modules die wille keurige getallen genereren. 

   Als u een Seed opgeeft, worden de resultaten gereproduceerd. Als u de resultaten van een splits bewerking moet herhalen, moet u een Seed opgeven voor de generator van wille keurige getallen. Anders wordt de wille keurige Seed standaard ingesteld op **0**, wat betekent dat de initiële seedwaarde wordt opgehaald uit de systeem klok. Als gevolg hiervan kan de verdeling van gegevens enigszins verschillen telkens wanneer u een splitsing uitvoert. 

1. **Stratified splitsen**: Stel deze optie in op **True** om ervoor te zorgen dat de twee uitvoer gegevens sets een representatief voor beeld bevatten van de waarden in de kolom *Strata* of de tabel *stratificatie sleutel*. 

   Met stratified-steek proeven worden de gegevens zo verdeeld dat elke uitvoer gegevensset ongeveer hetzelfde percentage van elke doel waarde ophaalt. U kunt er bijvoorbeeld voor zorgen dat uw trainings-en test sets ongeveer worden gesaldeerd met betrekking tot de uitkomst of voor een andere kolom (zoals gender).

1. Verzend de pijp lijn.


## <a name="select-a-regular-expression"></a>Een reguliere expressie selecteren

1. Voeg de module [gegevens splitsen](./split-data.md) toe aan uw pijp lijn en verbind deze als invoer met de gegevensset die u wilt splitsen.  
  
1. Selecteer voor de **Splits modus** **reguliere expressie splitsen**.

1. Geef in het vak **reguliere expressie** een geldige reguliere expressie op. 
  
   De reguliere expressie moet de python-syntaxis voor reguliere expressies volgen.

1. Verzend de pijp lijn.

   Op basis van de reguliere expressie die u opgeeft, wordt de gegevensset onderverdeeld in twee sets rijen: rijen met waarden die overeenkomen met de expressie en alle resterende rijen. 

De volgende voor beelden laten zien hoe u een gegevensset kunt verdelen met behulp van de optie **reguliere expressie** . 

### <a name="single-whole-word"></a>Eén heel woord 

In dit voor beeld worden alle rijen die de tekst in de kolom bevatten, in de eerste gegevensset geplaatst `Gryphon` `Text` . Er worden andere rijen in de tweede uitvoer van **gesplitste gegevens** geplaatst.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Subtekenreeks

In dit voor beeld wordt gezocht naar de opgegeven teken reeks op een wille keurige positie binnen de tweede kolom van de gegevensset. De positie wordt hier aangegeven door de index waarde van 1. De overeenkomst is hoofdletter gevoelig.

```text
(\1) ^[a-f]
```

De eerste resultaat gegevensset bevat alle rijen waar de index kolom met een van de volgende tekens begint: `a` , `b` , `c` , `d` , `e` , `f` . Alle andere rijen worden omgeleid naar de tweede uitvoer.

## <a name="select-a-relative-expression"></a>Een relatieve expressie selecteren

1. Voeg de module [gegevens splitsen](./split-data.md) toe aan uw pijp lijn en verbind deze als invoer met de gegevensset die u wilt splitsen.
  
1. Voor de **Splits modus** selecteert u **relatieve expressie**.
  
1. Voer in het vak **relationele expressie** een expressie in waarmee een vergelijkings bewerking wordt uitgevoerd op één kolom.

   Voor **numerieke kolom**:
   - De kolom bevat getallen van elk numeriek gegevens type, inclusief gegevens typen voor datum en tijd.
   - De expressie kan naar Maxi maal één kolom naam verwijzen.
   - Gebruik het teken en en `&` voor de bewerking en. Gebruik het sluis teken ( `|` ) voor de or-bewerking.
   - De volgende Opera tors worden ondersteund: `<` , `>` , `<=` , `>=` , `==` , `!=` .
   - U kunt geen bewerkingen groeperen met behulp van `(` en `)` .
   
   Voor de **teken reeks kolom**:
   - De volgende Opera tors worden ondersteund: `==` , `!=` .

1. Verzend de pijp lijn.

   De expressie splitst de gegevensset in twee sets rijen: rijen met waarden die voldoen aan de voor waarde en alle resterende rijen.

De volgende voor beelden laten zien hoe u een gegevensset kunt verdelen met behulp van de **relatieve expressie** optie in de module **Split data** .  

### <a name="calendar-year"></a>Kalenderjaar

Een veelvoorkomend scenario is het verdelen van een gegevensset per jaar. Met de volgende expressie worden alle rijen geselecteerd waarvan de waarden in de kolom `Year` groter zijn dan `2010` .

```text
\"Year" > 2010
```

De datum expressie moet rekening houden met alle datum onderdelen die zijn opgenomen in de gegevens kolom. De notatie van de datums in de gegevens kolom moet consistent zijn. 

In een kolom Date die de notatie gebruikt `mmddyyyy` , moet de expressie er bijvoorbeeld als volgt uitzien:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Kolom index

De volgende expressie laat zien hoe u de kolom index kunt gebruiken om alle rijen in de eerste kolom van de gegevensset te selecteren die waarden bevatten die kleiner zijn dan of gelijk zijn aan 30, maar niet gelijk aan 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
