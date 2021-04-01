---
title: 'Partitie en voor beeld: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de partitie-en voorbeeld module in Azure Machine Learning om steek proeven op een gegevensset uit te voeren of om partities te maken op basis van uw gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a1f0a0dff4eb8a1aad0cd5e142959a636827a541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898484"
---
# <a name="partition-and-sample-module"></a>Partitie en voorbeeld module

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik de partitie-en voorbeeld module om steek proeven op een gegevensset uit te voeren of om partities te maken op basis van uw gegevensset.

Steek proeven zijn een belang rijk hulp middel in machine learning omdat u hiermee de grootte van een gegevensset kunt reduceren en dezelfde verhouding van waarden behouden. Deze module biedt ondersteuning voor verschillende gerelateerde taken die belang rijk zijn in machine learning: 

- Gegevens delen in meerdere subsecties van dezelfde grootte. 

  U kunt de partities voor kruis validatie gebruiken of aanvragen toewijzen aan wille keurige groepen.

- Het scheiden van gegevens in groepen en het werken met gegevens uit een specifieke groep. 

  Wanneer u wille keurig cases aan verschillende groepen toewijst, moet u mogelijk de functies wijzigen die aan slechts één groep zijn gekoppeld.

- Proef. 

  U kunt een percentage van de gegevens extra heren, wille keurige steek proeven Toep assen of een kolom kiezen die u wilt gebruiken om de gegevensset te verdelen en stratified-steek proeven uit te voeren op waarden.

- Een kleinere gegevensset maken voor het testen. 

  Als u veel gegevens hebt, wilt u mogelijk alleen de eerste *n* rijen gebruiken tijdens het instellen van de pijp lijn en vervolgens overschakelen naar het gebruik van de volledige gegevensset wanneer u het model bouwt. U kunt ook steek proeven gebruiken om een kleinere gegevensset te maken voor gebruik in ontwikkeling.

## <a name="configure-the-module"></a>De module configureren

Deze module biedt ondersteuning voor de volgende methoden voor het verdelen van uw gegevens in partities of voor steek proeven. Kies eerst de methode en stel vervolgens aanvullende opties in die voor de methode vereist zijn.

- Head
- Steekproeven
- Aan vouwen toewijzen
- Vouw kiezen

### <a name="get-top-n-rows-from-a-dataset"></a>BOVENSTE N rijen uit een gegevensset ophalen

Gebruik deze modus om alleen de eerste *n* rijen op te halen. Deze optie is handig als u een pijp lijn wilt testen op een klein aantal rijen en u niet wilt dat de gegevens op enigerlei wijze worden verdeeld of gesampled.

1. Voeg de **partitie-en voorbeeld** module toe aan uw pijp lijn in de interface en verbind de gegevensset.  

1. **Partitie of voorbeeld modus**: Stel deze optie in op **kop**.

1. **Aantal rijen dat moet worden geselecteerd**: Voer het aantal rijen in dat moet worden geretourneerd.

   Het aantal rijen moet een niet-negatief geheel getal zijn. Als het aantal geselecteerde rijen groter is dan het aantal rijen in de gegevensset, wordt de hele gegevensset geretourneerd.

1. Verzend de pijp lijn.

De module voert een enkele gegevensset uit die alleen het opgegeven aantal rijen bevat. De rijen worden altijd gelezen vanaf de bovenkant van de gegevensset.

### <a name="create-a-sample-of-data"></a>Een voor beeld van gegevens maken

Deze optie ondersteunt eenvoudige wille keurige steek proeven of stratified wille keurige steek proeven. Het is handig als u een kleinere representatieve voorbeeld gegevensset wilt maken voor het testen.

1. Voeg de **partitie-en voorbeeld** module toe aan uw pijp lijn en verbind de gegevensset.

1. **Partitie of voorbeeld modus**: Stel deze optie in op  **steek proeven**.

1. **Sampling frequentie**: Voer een waarde in tussen 0 en 1. met deze waarde wordt het percentage rijen van de bron-gegevensset opgegeven dat moet worden opgenomen in de uitvoer gegevensset.

   Als u bijvoorbeeld slechts de helft van de oorspronkelijke gegevensset wilt, voert u `0.5` in om aan te geven dat de sampling frequentie 50 procent moet zijn.

   De rijen van de invoer gegevensset worden in een wille keurige volg orde geplaatst en op basis van de opgegeven verhouding in de uitvoer gegevensset ingevoegd.

1. **Wille keurig zaad voor bemonstering**: Voer eventueel een geheel getal in dat moet worden gebruikt als een Seed-waarde.

   Deze optie is belang rijk als u wilt dat de rijen op dezelfde manier worden gedeeld als elke keer. De standaard waarde is **0**, wat betekent dat een begin Seed wordt gegenereerd op basis van de systeem klok. Deze waarde kan leiden tot enigszins verschillende resultaten telkens wanneer u de pijp lijn uitvoert.

1. **Stratified splitsen voor steek proeven**: Selecteer deze optie als het belang rijk is dat de rijen in de gegevensset gelijkmatig zijn verdeeld over een bepaalde sleutel kolom vóór de steek proef.

   Voor een **stratificatie sleutel kolom voor steek proeven** selecteert u één *Strata kolom* die moet worden gebruikt bij het delen van de gegevensset. De rijen in de gegevensset worden vervolgens als volgt onderverdeeld:

   1. Alle invoer rijen worden gegroepeerd (stratified) door de waarden in de opgegeven kolom Strata.

   1. Rijen worden in elke groep in een andere volg orde geplaatst.

   1. Elke groep wordt selectief toegevoegd aan de uitvoer gegevensset om te voldoen aan de opgegeven verhouding.


1. Verzend de pijp lijn.

   Met deze optie voert de module een enkele gegevensset uit die een representatieve steek proef van de gegevens bevat. Het resterende, niet-bemonsterde gedeelte van de gegevensset wordt niet uitgevoerd. 

## <a name="split-data-into-partitions"></a>Gegevens in partities splitsen

Gebruik deze optie als u de gegevensset wilt verdelen over subsets van de gegevens. Deze optie is ook handig als u een aangepast aantal vouwen wilt maken voor kruis validatie of als u rijen wilt splitsen in verschillende groepen.

1. Voeg de **partitie-en voorbeeld** module toe aan uw pijp lijn en verbind de gegevensset.

1. Voor **partitie of voorbeeld modus** selecteert **u toewijzen aan vouwen**.

1. **Vervanging gebruiken in partitioneren**: Selecteer deze optie als u wilt dat de geplaatste rij wordt weer gegeven in de pool met rijen voor een mogelijke hergebruik. Als gevolg hiervan kan dezelfde rij worden toegewezen aan verschillende vouwen.

   Als u geen vervanging gebruikt (de standaard optie), wordt de rij met het voor beeld niet weer gegeven in de pool met rijen voor een mogelijke hergebruik. Als gevolg hiervan kan elke rij slechts aan één vouw worden toegewezen.

1. **Wille keurige splitsing**: Selecteer deze optie als u wilt dat rijen wille keurig worden toegewezen aan vouwen.

   Als u deze optie niet selecteert, worden de rijen door de Round-Robin methode aan vouwen.

1. **Wille keurig zaad**: Voer eventueel een geheel getal in dat moet worden gebruikt als seed-waarde. Deze optie is belang rijk als u wilt dat de rijen op dezelfde manier worden gedeeld als elke keer. Anders is de standaard waarde **0** betekent dat een wille keurige begin Seed wordt gebruikt.

1. **Geef de partitioner-methode** op: Geef aan hoe u wilt dat gegevens worden verdeeld over elke partitie, met behulp van de volgende opties:

   - **Gelijkmatige partitie**: gebruik deze optie om een gelijk aantal rijen in elke partitie te plaatsen. Als u het aantal uitvoer partities wilt opgeven, geeft u een geheel getal op in het vak **Geef het aantal gevouwen vouwingen op dat gelijkmatig moet worden gesplitst** .

   - **Partitie met aangepaste verhoudingen**: gebruik deze optie om de grootte van elke partitie op te geven als een lijst met door komma's gescheiden waarden.

     Stel dat u drie partities wilt maken. De eerste partitie bevat 50 procent van de gegevens. De overige twee partities bevatten elk 25 procent van de gegevens. Voer in het vak **lijst met verhoudingen gescheiden door een komma** de volgende getallen in: **0,5,. 25,. 25**.

     De som van alle partitie grootten moet precies 1 zijn.

     Als u getallen opgeeft die Maxi maal *1* zijn, wordt er een extra partitie gemaakt om de resterende rijen te bewaren. Als u bijvoorbeeld de waarden **.2** en **.3** opgeeft, wordt er een derde partitie gemaakt om het resterende 50 procent van alle rijen te bewaren.
     
     Als u getallen opgeeft die Maxi maal *1* zijn, treedt er een fout op wanneer u de pijp lijn uitvoert.

1. **Stratified splitsen**: Selecteer deze optie als u wilt dat de rijen worden stratified wanneer deze worden gesplitst en kies vervolgens de _kolom Strata_.

1. Verzend de pijp lijn.

   Met deze optie worden meerdere gegevens sets in de module uitgevoerd. De gegevens sets worden gepartitioneerd op basis van de regels die u hebt opgegeven.

### <a name="use-data-from-a-predefined-partition"></a>Gegevens van een vooraf gedefinieerde partitie gebruiken  

Gebruik deze optie wanneer u een gegevensset hebt onderverdeeld in meerdere partities en nu elke partitie op zijn beurt wilt laden voor verdere analyse of verwerking.

1. Voeg de **partitie en de voorbeeld** module toe aan de pijp lijn.

1. Koppel de module aan de uitvoer van een eerder exemplaar van de **partitie en** het voor beeld. Dat exemplaar moet de optie **toewijzen aan vouwen** hebben gebruikt om een aantal partities te genereren.

1. **Partitie of voorbeeld modus**: Selecteer **vouw vouwen**.

1. **Geef op met welke vouw moet worden steek proef**: Selecteer een te gebruiken partitie door de index ervan op te geven. Partitie-indexen zijn op 1 gebaseerd. Als u de gegevensset bijvoorbeeld in drie delen hebt verdeeld, hebben de partities de indices 1, 2 en 3.

   Als u een ongeldige index waarde invoert, wordt een fout in de ontwerp fase gegenereerd: ' fout 0018: gegevensset bevat ongeldige gegevens '.

   Naast het groeperen van de gegevensset door vouwen, kunt u de gegevensset in twee groepen scheiden: een doel vouwen en alle andere. Als u dit wilt doen, voert u de index van één Vouw in en selecteert u vervolgens de optie selectie **complement van de geselecteerde vouw** om alles op te halen, behalve de gegevens in de opgegeven vouwen.

1. Als u met meerdere partities werkt, moet u meer exemplaren van de **partitie en voorbeeld** module toevoegen voor het afhandelen van elke partitie.

   Bijvoorbeeld, de **partitie en de voorbeeld** module in de tweede rij zijn ingesteld om **te worden toegewezen aan de vouwen** en de module in de derde rij is ingesteld op **vouw vouwen**.   

   ![Partitie en voor beeld](./media/module/partition-and-sample.png)

1. Verzend de pijp lijn.

   Met deze optie voert de module een enkele gegevensset uit die alleen de rijen bevat die aan de vouwen zijn toegewezen.

> [!NOTE]
>  U kunt de gevouwen ontwerps niet rechtstreeks weer geven. Ze zijn alleen aanwezig in de meta gegevens.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 