---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de SMOTE-module in Azure Machine Learning om het aantal voor beelden met een laag voor beeld in een gegevensset te verg Roten door gebruik te maken van oversteek proeven.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 501f3e8946023d28d67a33fbbfca661afbc6306d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898271"
---
# <a name="smote"></a>SMOTE

In dit artikel wordt beschreven hoe u de SMOTE-module in Azure Machine Learning Designer kunt gebruiken om het aantal onderaangeduide cases in een gegevensset te verhogen die wordt gebruikt voor machine learning. SMOTE is een betere manier om het aantal zeldzame gevallen te verhogen dan alleen bestaande aanvragen te dupliceren.  

U verbindt de SMOTE-module met een gegevensset die niet in *balans* is. Er zijn verschillende redenen waarom een gegevensset niet in balans kan worden opgenomen. De categorie die u aanwijst, kan bijvoorbeeld zeldzaam zijn in de populatie of de gegevens kunnen lastig zijn te verzamelen. Normaal gesp roken gebruikt u SMOTE als de *klasse* die u wilt analyseren, wordt weer gegeven. 
  
De module retourneert een gegevensset die de oorspronkelijke voor beelden bevat. Er wordt ook een aantal synthetische minderheids monsters geretourneerd, afhankelijk van het percentage dat u opgeeft.  
  
## <a name="more-about-smote"></a>Meer informatie over SMOTE

Synthetische minderheids oversampling techniek (SMOTE) is een statistische techniek voor het verhogen van het aantal cases in uw gegevensset op een evenwichtige manier. De module werkt door nieuwe instanties te genereren op basis van bestaande minderheids gevallen die u als invoer opgeeft. Deze implementatie van SMOTE heeft *geen* invloed op het aantal hoofd gevallen.

De nieuwe exemplaren zijn niet alleen kopieën van bestaande minderheids gevallen. In plaats daarvan neemt het algoritme voor beelden van de *functie ruimte* voor elke doel klasse en de dichtstbijzijnde neighbors. Het algoritme genereert vervolgens nieuwe voor beelden die de kenmerken van de doel Case combi neren met functies van de neighbors. Met deze methode worden de beschik bare functies voor elke klasse verhoogd en worden de voor beelden algemeen uitgebreid.
  
SMOTE neemt de hele gegevensset als invoer, maar het percentage van de minderheids deelnemingen wordt verhoogd. Stel dat u een niet-sluitende gegevensset hebt waarbij slechts 1 procent van de cases de doel waarde A (de klasse minderheid) hebben, en 99 procent van de cases de waarde B hebben. Als u het percentage minderheids deelnemingen wilt verhogen naar twee maal het vorige percentage, voert u **200** in voor **SMOTE percentage** in de eigenschappen van de module.  
  
## <a name="examples"></a>Voorbeelden  

We raden u aan om SMOTE te gebruiken met een kleine gegevensset om te zien hoe het werkt. In het volgende voor beeld wordt de bloeddonatie-gegevensset gebruikt die beschikbaar is in Azure Machine Learning Designer.
  
Als u de gegevensset toevoegt aan een pijp lijn en **visualiseren** selecteert op de uitvoer van de gegevensset, ziet u dat van de 748-rijen of-cases in de gegevensset, 570 cases (76 procent) van klasse 0 en 178 cases (24 procent) van klasse 1. Hoewel dit resultaat niet terribly niet in balans is, staat klasse 1 voor de personen die bloed hebben gedoneerd, dus bevatten deze rijen de *functie ruimte* die u wilt model leren.
 
Als u het aantal cases wilt verhogen, kunt u als volgt de waarde van het **SMOTE percentage** instellen met behulp van veelvouden van 100:

||Klasse 0|Klasse 1|totaal|  
|-|-------------|-------------|-----------|  
|Oorspronkelijke gegevensset<br /><br /> (gelijk aan **SMOTE-percentage**  =  **0**)|570<br /><br /> 76%|178<br /><br /> uur|748|  
|**Percentage SMOTE**  =  **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Percentage SMOTE**  =  **200**|570<br /><br /> 52%|534<br /><br /> 48%|1.104|  
|**Percentage SMOTE**  =  **300**|570<br /><br /> 44%|712<br /><br /> 56%|1.282|  
  
> [!WARNING]
> Het verhogen van het aantal cases met behulp van SMOTE is niet gegarandeerd om nauw keurigere modellen te maken. Probeer pijp lijnen uit met verschillende percentages, verschillende functie sets en verschillende nummers van dichtstbijzijnde neighbors om te zien hoe het toevoegen van aanvragen van invloed is op uw model.  
  
## <a name="how-to-configure-smote"></a>SMOTE configureren
  
1.  Voeg de module SMOTE toe aan de pijp lijn. De module onder de modules voor **gegevens transformatie** vindt u in de categorie **manipulatie** .

2. Verbind de gegevensset die u wilt verhogen. Als u de functie ruimte voor het maken van de nieuwe cases wilt opgeven, met behulp van alleen specifieke kolommen of door een deel ervan uit te sluiten, gebruikt u de module [select columns in dataset](select-columns-in-dataset.md) . U kunt vervolgens de kolommen die u wilt gebruiken, isoleren voordat u SMOTE gebruikt.
  
    Anders is het maken van nieuwe cases via SMOTE gebaseerd op *alle* kolommen die u opgeeft als invoer. Ten minste één kolom van de functie kolommen is numeriek.
  
3.  Controleer of de kolom met het label of de doel klasse is geselecteerd. SMOTE accepteert alleen binaire labels.
  
4.  De SMOTE-module identificeert automatisch de klasse minderheid in de kolom Label en vervolgens worden alle voor beelden voor de klasse minderheid opgehaald. Alle kolommen mogen geen NaN-waarden bevatten.
  
5.  Voer in de optie **percentage SMOTE** een geheel getal in dat het doel percentage van minderheids deelnemingen in de uitvoer gegevensset aangeeft. Bijvoorbeeld:  
  
    - U geeft **0** op. De SMOTE-module retourneert precies dezelfde gegevensset die u als invoer hebt opgegeven. Er worden geen nieuwe minderheids gevallen toegevoegd. In deze gegevensset is de klasse-verhouding niet gewijzigd.  
  
    - U voert **100** in. In de SMOTE-module worden nieuwe minderheids gevallen gegenereerd. Het voegt hetzelfde aantal minderheids gevallen toe als in de oorspronkelijke gegevensset. Omdat SMOTE het aantal hoofd gevallen niet verhoogt, is het gedeelte van de cases van elke klasse gewijzigd.  
  
    - U voert **200** in. De module verdubbelt het percentage minderheids gevallen vergeleken met de oorspronkelijke gegevensset. Dit leidt *niet* tot twee keer zoveel minderheids gevallen als voorheen. In plaats daarvan wordt de grootte van de gegevensset zo verhoogd dat het aantal meerderheids gevallen hetzelfde blijft. Het aantal minderheids gevallen wordt verhoogd tot het overeenkomt met de gewenste percentage waarde.  
  
    > [!NOTE]
    > Gebruik alleen veelvouden van 100 voor het SMOTE-percentage.

6.  Gebruik het **aantal dichtstbijzijnde neighbor** -opties om de grootte te bepalen van de functie ruimte die het SMOTE-algoritme gebruikt voor het bouwen van nieuwe cases. Een dichtstbijzijnde neighbor is een rij met gegevens (een aanvraag) die vergelijkbaar is met een doel aanvraag. De afstand tussen twee gevallen wordt gemeten door de gewogen vectoren van alle functies te combi neren.  
  
    + Door het aantal dichtstbijzijnde buren te verhogen, profiteert u van meer zaken over functies.
    + Door het aantal dichtstbijzijnde neighbors te bewaren, kunt u gebruikmaken van functies die meer lijken op die in de oorspronkelijke steek proef.  
  
7. Voer een waarde in het vak **wille keurig zaad** in als u wilt dat dezelfde resultaten worden uitgevoerd als de uitvoering van dezelfde pijp lijn, met dezelfde gegevens. Anders genereert de module een wille keurige Seed op basis van processor klok waarden wanneer de pijp lijn wordt geïmplementeerd. Het genereren van een wille keurige Seed kan leiden tot enigszins verschillende resultaten voor uitvoeringen.

8. Verzend de pijp lijn.  
  
   De uitvoer van de module is een gegevensset die de oorspronkelijke rijen bevat plus een aantal toegevoegde rijen met minderheids deelnemings.  

## <a name="technical-notes"></a>Technische opmerkingen

+ Wanneer u een model publiceert dat gebruikmaakt van de **SMOTE** -module, verwijdert u **SMOTE** uit de voorspellende pijp lijn voordat deze wordt gepubliceerd als een webservice. De reden hiervoor is dat SMOTE is bedoeld voor het verbeteren van een model tijdens de training, niet voor het scoren. Mogelijk wordt er een fout melding weer geven als een gepubliceerde voorspellende pijp lijn de SMOTE-module bevat.

+ U kunt vaak betere resultaten krijgen als u ontbrekende waarden opschoont of andere trans formaties toepast om gegevens te corrigeren voordat u SMOTE toepast. 

+ Sommige onderzoekers hebben onderzocht of SMOTE effectief is op zeer dimensionale of sparse gegevens, zoals gegevens die worden gebruikt in tekst classificaties of Genomics. Dit document bevat een goed overzicht van de effecten en de theoretische geldigheid van het Toep assen van SMOTE in dergelijke gevallen: [Blagus en LUSA: SMOTE voor grootschalige, gebalanceerde gegevens](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Als SMOTE niet effectief is in uw gegevensset, kunt u onder andere de volgende benaderingen overwegen:
  + Methoden voor het overmeten van de minderheids gevallen of het bemonsteren van de hoofd gevallen.
  + Ensemble-technieken die de kenniser rechtstreeks helpen bij het gebruik van clustering, invallen of adaptief verbeteren.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

