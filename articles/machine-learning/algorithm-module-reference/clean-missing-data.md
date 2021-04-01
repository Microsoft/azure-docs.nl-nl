---
title: 'Ontbrekende gegevens wissen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module clean missing data in Azure Machine Learning voor het verwijderen, vervangen of afleiden van ontbrekende waarden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 5b7943b2026d640ae7e5d119e165bd752ae2fe7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898819"
---
# <a name="clean-missing-data-module"></a>Ontbrekende gegevens module wissen

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Met deze module kunt u ontbrekende waarden verwijderen, vervangen of afleiden. 

Gegevens wetenschappers controleren vaak gegevens over ontbrekende waarden en voeren verschillende bewerkingen uit om de gegevens te herstellen of nieuwe waarden in te voegen. Het doel van dergelijke reinigings bewerkingen is om te voor komen dat er problemen ontstaan door ontbrekende gegevens die kunnen optreden bij het trainen van een model. 

Deze module ondersteunt meerdere typen bewerkingen voor het opschonen van ontbrekende waarden, waaronder:

+ Ontbrekende waarden vervangen door een tijdelijke aanduiding, gemiddelde of andere waarde
+ Rijen en kolommen met ontbrekende waarden volledig verwijderen
+ Uitstel waarden op basis van statistische methoden


Als u deze module gebruikt, wordt de bron-gegevensset niet gewijzigd. In plaats daarvan wordt er een nieuwe gegevensset in uw werk ruimte gemaakt die u in de volgende werk stroom kunt gebruiken. U kunt ook de nieuwe, gereinigde gegevensset opslaan voor hergebruik.

In deze module wordt ook een definitie uitgevoerd van de trans formatie die wordt gebruikt om de ontbrekende waarden op te schonen. U kunt deze trans formatie opnieuw gebruiken voor andere gegevens sets die hetzelfde schema hebben, met behulp van de module [trans formatie Toep assen](./apply-transformation.md) .  

## <a name="how-to-use-clean-missing-data"></a>Schone ontbrekende gegevens gebruiken

Met deze module kunt u een reinigings bewerking definiëren. U kunt ook de opschoon bewerking opslaan, zodat u deze later kunt Toep assen op nieuwe gegevens. Zie de volgende secties voor het maken en opslaan van een reinigings proces: 
 
+ [Ontbrekende waarden vervangen](#replace-missing-values)
  
+ [Een reinigings transformatie Toep assen op nieuwe gegevens](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> De reinigings methode die u voor het verwerken van ontbrekende waarden gebruikt, kan de resultaten aanzienlijk beïnvloeden. We raden u aan om met verschillende methoden te experimenteren. Houd rekening met zowel de reden voor het gebruik van een bepaalde methode als de kwaliteit van de resultaten.

### <a name="replace-missing-values"></a>Ontbrekende waarden vervangen  

Telkens wanneer u de module  [clean Missing Data](./clean-missing-data.md) toepast op een set gegevens, wordt dezelfde reinigings bewerking toegepast op alle kolommen die u selecteert. Als u verschillende kolommen met verschillende methoden wilt opschonen, gebruikt u daarom afzonderlijke exemplaren van de module.

1.  Voeg de module [clean Missing Data](./clean-missing-data.md) toe aan de pijp lijn en verbind de gegevensset met ontbrekende waarden.  
  
2.  **Als u kolommen wilt opschonen**, kiest u de kolommen die de ontbrekende waarden bevatten die u wilt wijzigen. U kunt meerdere kolommen kiezen, maar u moet dezelfde vervangings methode gebruiken in alle geselecteerde kolommen. Normaal gesp roken moet u de teken reeks kolommen en numerieke kolommen afzonderlijk opschonen.

    Als u bijvoorbeeld wilt controleren op ontbrekende waarden in alle numerieke kolommen:

    1. Selecteer de module **clean Missing Data** en klik op **kolom bewerken** in het rechter paneel van de module.

    3. Voor **opnemen** selecteert u **kolom typen** in de vervolg keuzelijst en selecteert u vervolgens **Numeriek**. 
  
    Elke opschonings-of vervangings methode die u kiest, moet van toepassing zijn op **alle** kolommen in de selectie. Als de gegevens in een kolom incompatibel zijn met de opgegeven bewerking, retourneert de module een fout en stopt de pijp lijn.
  
3.  Geef voor de **minimale waarde** van de vereiste voor de ondergrens het minimale aantal ontbrekende waarden op dat vereist is voor de bewerking die moet worden uitgevoerd.  
  
    U gebruikt deze optie in combi natie met de **Maxi maal ontbrekende waarde ratio** om de voor waarden te definiëren waaronder een reinigings bewerking wordt uitgevoerd op de gegevensset. Als er te veel of te weinig rijen zijn met ontbrekende waarden, kan de bewerking niet worden uitgevoerd. 
  
    Het getal dat u invoert, vertegenwoordigt de **verhouding** van ontbrekende waarden voor alle waarden in de kolom. Standaard is de minimale waarde voor de eigenschap **quoted ontbreekt** ingesteld op 0. Dit betekent dat ontbrekende waarden worden opgeschoond, zelfs als er slechts één ontbrekende waarde is. 

    > [!WARNING]
    > Aan deze voor waarde moet elke kolom worden voldaan om de opgegeven bewerking toe te passen. Stel dat u drie kolommen hebt geselecteerd en vervolgens de minimale verhouding van ontbrekende waarden instelt op .2 (20%), maar er in slechts één kolom 20% waarden ontbreken. In dit geval is de opschoon bewerking alleen van toepassing op de kolom met meer dan 20% ontbrekende waarden. Daarom worden de andere kolommen niet gewijzigd.
    > 
    > Als u twijfelt of ontbrekende waarden zijn gewijzigd, selecteert u de optie voor het **genereren van een ontbrekende waarde indicator kolom**. Er wordt een kolom toegevoegd aan de gegevensset om aan te geven of elke kolom voldoet aan de opgegeven criteria voor het minimum-en maximum aantal bereiken.  
  
4. Geef voor de **maximale waarde** voor de ontbrekende hoeveelheid een maximum aantal ontbrekende waarden op dat aanwezig kan zijn voor de bewerking die moet worden uitgevoerd.   
  
    Het is bijvoorbeeld mogelijk dat u de vervanging van de waarde alleen wilt uitvoeren als 30% of minder van de rijen ontbrekende waarden bevat, maar laat de waarden ongewijzigd als er meer dan 30% rijen ontbrekende waarden bevatten.  
  
    U definieert het getal als de verhouding van ontbrekende waarden voor alle waarden in de kolom. Standaard is de **maximale waarde voor ontbrekende waarden** ingesteld op 1. Dit betekent dat ontbrekende waarden worden opgeschoond, zelfs als 100% van de waarden in de kolom ontbreken.  
  
   
  
5. Voor de **reinigings modus** selecteert u een van de volgende opties voor het vervangen of verwijderen van ontbrekende waarden:  
  
  
    + **Aangepaste vervangings waarde**: gebruik deze optie om een tijdelijke aanduiding voor waarden (zoals 0 of n.v.t.) op te geven die van toepassing is op alle ontbrekende waarde. De waarde die u opgeeft als vervanging moet compatibel zijn met het gegevens type van de kolom.
  
    + **Vervangen door gemiddelde**: berekent het gemiddelde van de kolom en gebruikt het gemiddelde als de vervangings waarde voor elke ontbrekende waarde in de kolom.  
  
        Is alleen van toepassing op kolommen met gegevens typen integer, Double of Boolean.  
  
    + **Vervangen door mediaan**: berekent de kolom mediaan waarde en gebruikt de mediaan waarde als vervanging voor een ontbrekende waarde in de kolom.  
  
        Is alleen van toepassing op kolommen met het gegevens type integer of double. 
  
    + **Vervangen door modus**: berekent de modus voor de kolom en gebruikt de modus als de vervangings waarde voor elke ontbrekende waarde in de kolom.  
  
        Is van toepassing op kolommen met het gegevens type integer, double, Boolean of categorische. 
  
    + **Hele rij verwijderen**: Hiermee verwijdert u alle rijen in de gegevensset met een of meer ontbrekende waarden. Dit is handig als de ontbrekende waarde kan worden beschouwd als wille keurig ontbrekend.  
  
    + **Hele kolom verwijderen**: Hiermee verwijdert u alle kolommen in de gegevensset met een of meer ontbrekende waarden volledig.  
  
    
  
6. De waarde voor het **vervangen** van de optie is beschikbaar als u de optie **aangepaste vervangings waarde** hebt geselecteerd. Typ een nieuwe waarde die moet worden gebruikt als de vervangings waarde voor alle ontbrekende waarden in de kolom.  
  
    Houd er rekening mee dat u deze optie alleen kunt gebruiken in kolommen met het gehele getal, de waarde double, Booleaans of teken reeks.
  
7. **Indicator kolom voor ontbrekende waarde genereren**: Selecteer deze optie als u een bepaalde indicatie wilt uitvoeren van de vraag of de waarden in de kolom voldoen aan de criteria voor het schoonmaken van ontbrekende waarden. Deze optie is met name handig wanneer u een nieuwe reinigings bewerking instelt en er zeker van wilt zijn dat deze goed werkt.
  
8. Verzend de pijp lijn.

### <a name="results"></a>Resultaten

De module retourneert twee uitvoer:  

-   **Opgeschoonde gegevensset**: een gegevensset die bestaat uit de geselecteerde kolommen, waarbij ontbrekende waarden worden verwerkt zoals opgegeven, samen met een indicator kolom, als u die optie hebt geselecteerd.  

    Kolommen die niet zijn geselecteerd voor het opschonen, zijn ook ' door gegeven '.  
  
-  **Reinigings transformatie**: een gegevens transformatie die wordt gebruikt voor het opschonen, die in uw werk ruimte kan worden opgeslagen en later op nieuwe gegevens kan worden toegepast.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Een opgeslagen opschonings bewerking Toep assen op nieuwe gegevens  

Als u vaak reinigings bewerkingen wilt herhalen, kunt u het beste uw recept opslaan voor het opschonen van gegevens als een *trans formatie*. Het opslaan van een reinigings transformatie is met name handig als u regel matig opnieuw moet importeren en vervolgens gegevens met hetzelfde schema wilt opschonen.  
      
1.  Voeg de module [trans formatie Toep assen](./apply-transformation.md) toe aan uw pijp lijn.  
  
2.  Voeg de gegevensset die u wilt reinigen toe en verbind de gegevensset met de juiste invoer poort.  
  
3.  Vouw de groep **trans formaties** uit in het linkerdeel venster van de ontwerp functie. Zoek de opgeslagen trans formatie en sleep deze naar de pijp lijn.  

4.  Verbind de opgeslagen trans formatie met de linker invoer poort van [Apply trans formatie](./apply-transformation.md). 

    Wanneer u een opgeslagen trans formatie toepast, kunt u de kolommen waarop de trans formatie wordt toegepast, niet selecteren. Dat komt doordat de trans formatie al is gedefinieerd en automatisch wordt toegepast op de kolommen die zijn opgegeven in de oorspronkelijke bewerking.

    Stel dat u een trans formatie hebt gemaakt voor een subset met numerieke kolommen. U kunt deze trans formatie Toep assen op een gegevensset van gemengde kolom typen zonder een fout te verhogen, omdat de ontbrekende waarden alleen worden gewijzigd in de overeenkomende numerieke kolommen.

6.  Verzend de pijp lijn.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 