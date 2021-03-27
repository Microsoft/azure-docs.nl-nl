---
title: Wiskundige bewerking toepassen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module math bewerking Toep assen in Azure Machine Learning om een wiskundige bewerking toe te passen op kolom waarden in een pijp lijn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: cc2afb2b29c69e9687dbd3b194b6a1626d4131b3
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625191"
---
# <a name="apply-math-operation"></a>Wiskundige bewerking toepassen

In dit artikel wordt een module van Azure Machine Learning Designer beschreven.

Gebruik de bewerking math Toep assen om berekeningen te maken die worden toegepast op numerieke kolommen in de invoer gegevensset. 

Wiskundige bewerkingen zijn onder andere reken kundige functies, trigonometrische functies, Afrondings functies en speciale functies die worden gebruikt in data Science, zoals gamma-en fout functies.  

Nadat u een bewerking hebt gedefinieerd en de pijp lijn hebt uitgevoerd, worden de waarden toegevoegd aan uw gegevensset. Afhankelijk van hoe u de module configureert, kunt u het volgende doen:

+ De resultaten toevoegen aan uw gegevensset (handig bij het controleren van het resultaat van de bewerking).
+ Kolom waarden vervangen door de nieuwe, berekende waarden.
+ Een nieuwe kolom genereren voor de resultaten en niet de oorspronkelijke gegevens weer geven. 

Zoek naar de bewerking die u nodig hebt in de volgende categorieën:  

- [Basic](#basic-math-operations)  
  
     De functies in de categorie **Basic** kunnen worden gebruikt voor het bewerken van één waarde of een kolom met waarden. U kunt bijvoorbeeld de absolute waarde van alle getallen in een kolom ophalen of de vierkantswortel van elke waarde in een kolom berekenen.  
  
-   [Vergelijken](#comparison-operations)  
  
      De functies in de categorie **vergelijken** worden allemaal gebruikt voor de vergelijking: u kunt een combi natie van de waarden in twee kolommen vergelijken, maar u kunt ook elke waarde in een kolom met een opgegeven constante vergelijkt. U kunt bijvoorbeeld kolommen vergelijken om te bepalen of waarden in twee gegevens sets hetzelfde zijn. U kunt ook een constante, zoals een Maxi maal toegestane waarde, gebruiken om uitschieters in een numerieke kolom te vinden.  
  
-   [Bewerkingen](#arithmetic-operations)  
  
     De categorie **bewerkingen** bevat eenvoudige wiskundige functies: optellen, aftrekken, vermenigvuldigen en delen. U kunt werken met kolommen of constanten. U kunt bijvoorbeeld de waarde in kolom A toevoegen aan de waarde in kolom B. U kunt ook een constante aftrekken, zoals een eerder berekend gemiddelde, van elke waarde in kolom A.  
  
-   [Afronden](#rounding-operations)  
  
     De **Afrondings** categorie omvat diverse functies voor het uitvoeren van bewerkingen, zoals afronding, plafond, vloer en afkap ping, op verschillende nauwkeurigheids niveaus. U kunt het nauwkeurigheids niveau voor zowel decimale als gehele getallen opgeven.  
  
-   [Specifiek](#special-math-functions)  
  
     De **speciale** categorie bevat wiskundige functies die vooral worden gebruikt in gegevens wetenschap, zoals elliptische integralen en de functie Gaussiaans.  
  
-   [Trigonometrische](#trigonometric-functions)  
  
     De **trigonometrische** categorie bevat alle standaard trigonometrische functies. U kunt radialen bijvoorbeeld converteren naar graden of reken functies zoals tangens in radialen of graden.
     Deze functies zijn unaire, wat inhoudt dat ze één kolom met waarden als invoer hebben, de trigonometrische functie Toep assen en een kolom met waarden retour neren als resultaat. Zorg ervoor dat de invoer kolom het juiste type is en het juiste type waarden bevat voor de opgegeven bewerking.   

## <a name="how-to-configure-apply-math-operation"></a>De opdracht wiskunde Toep assen configureren  

Voor de **Apply math Operation** -module is een gegevensset vereist met ten minste één kolom die alleen getallen bevat. De getallen kunnen afzonderlijk of doorlopend zijn, maar moeten een numeriek gegevens type zijn, geen teken reeks.

U kunt dezelfde bewerking Toep assen op meerdere numerieke kolommen, maar alle kolommen moeten zich in dezelfde gegevensset bevindt. 

Elk exemplaar van deze module kan slechts één type bewerking tegelijk uitvoeren. Als u complexe wiskundige bewerkingen wilt uitvoeren, moet u mogelijk meerdere exemplaren van de module **Math bewerking Toep assen** .  
  
1.  Voeg de module **Math bewerking Toep assen** toe aan uw pijp lijn.

1. Verbind een gegevensset die ten minste één numerieke kolom bevat.  

1.  Selecteer een of meer bron kolommen waarop de berekening moet worden uitgevoerd.   
  
    - Een kolom die u kiest, moet een numeriek gegevens type zijn. 
    - Het gegevens bereik moet geldig zijn voor de geselecteerde wiskundige bewerking. Anders treedt er een fout of NaN (geen getal) resultaat op. Bijvoorbeeld: ln (-1,0) is een ongeldige bewerking en resulteert in een waarde van `NaN` .
  
1.  Selecteer **categorie** om het **type** wiskundige bewerking te selecteren dat moet worden uitgevoerd.
    
1. Kies een specifieke bewerking in de lijst in die categorie.
  
1.  Stel aanvullende para meters in die vereist zijn voor elk type bewerking.  
  
1.  Gebruik de optie **uitvoer modus** om aan te geven hoe u de wiskundige bewerking wilt genereren: 

    - **Toevoegen**. Alle kolommen die als invoer worden gebruikt, worden opgenomen in de uitvoer gegevensset, plus er wordt één aanvullende kolom toegevoegd die de resultaten van de wiskundige bewerking bevat.
    - **InPlace**. De waarden in de kolommen die als invoer worden gebruikt, worden vervangen door de nieuwe berekende waarden. 
    - **ResultOnly**. Er wordt één kolom geretourneerd met de resultaten van de wiskundige bewerking.
  
1.  Verzend de pijp lijn.  
  
## <a name="results"></a>Resultaten

Als u de resultaten genereert met de opties **Append** of **ResultOnly** , geven de kolom koppen van de geretourneerde gegevensset de bewerking en de gebruikte kolommen aan. Als u bijvoorbeeld twee kolommen vergelijkt met behulp van de operator **equals** , ziet de resultaten er als volgt uit:  
  
-   **Is gelijk aan (Col2_Col1)**, wat aangeeft dat u col2 op Kol1 hebt getest.  
-   **Is gelijk aan (Col2_ $10)**, wat aangeeft dat u kolom 2 hebt vergeleken met de constante 10.  

Zelfs als u de optie **in plaats** gebruikt, worden de bron gegevens niet verwijderd of gewijzigd. de kolom in de oorspronkelijke gegevensset is nog steeds beschikbaar in de ontwerp functie. Als u de oorspronkelijke gegevens wilt bekijken, kunt u verbinding maken met de module [kolommen toevoegen](add-columns.md) en deze koppelen aan de uitvoer van de **bewerking wiskunde Toep assen**.  
    
## <a name="basic-math-operations"></a>Eenvoudige wiskundige bewerkingen 

De functies in de categorie **Basic** nemen meestal één waarde uit een kolom, voeren de vooraf gedefinieerde bewerking uit en retour neren een enkele waarde. Voor sommige functies kunt u een constante of een kolomset opgeven als een tweede argument.  
  
 Azure Machine Learning ondersteunt de volgende functies in de categorie **basis** :  

### <a name="abs"></a>Abs

Retourneert de absolute waarde van de geselecteerde kolommen.  
  
### <a name="atan2"></a>Atan2

Retourneert een inverse tangens met vier kwadranten.  

Selecteer de kolommen die de punt coördinaten bevatten. Voor het tweede argument, dat overeenkomt met de x-coördinaat, kunt u ook een constante opgeven.  

Komt overeen met de functie BOOGTAN2 in MATLAB.  

### <a name="conj"></a>Conj

Retourneert de geconjugeerde voor de waarden in de geselecteerde kolom.  

### <a name="cuberoot"></a>CubeRoot

Hiermee berekent u de derdemachts wortel voor de waarden in de geselecteerde kolom.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Berekent de dubbele faculteit voor waarden in de geselecteerde kolom. De dubbele faculteit is een uitbrei ding van de normale faculteits functie en wordt aangeduid als `x!!` .  

### <a name="eps"></a>EPS

Retourneert de grootte van de tussen ruimte tussen de huidige waarde en het volgende hoogste getal met dubbele precisie. Komt overeen met de EPS-functie in MATLAB.  
  
### <a name="exp"></a>Exp

Retourneert e verheven tot de macht van de waarde in de geselecteerde kolom. Deze functie is hetzelfde als de Excel EXP-functie.  

### <a name="exp2"></a>Exp2

Retourneert de base-2 exponentiële waarde van de argumenten, oplossing voor y = x * 2<sup>t</sup> , waarbij t een kolom met waarden die exponenten bevatten.  

Selecteer in  **kolom set** de kolom die de exponent waarden t bevat.

Voor **Exp2** kunt u een tweede argument x opgeven. Dit kan een constante of een andere kolom met waarden zijn. Geef bij **type tweede argument** aan of u de vermenigvuldiger x als constante wilt opgeven, of een waarde in een kolom.  

Als u bijvoorbeeld een kolom met de waarden {0,1,2,3,4,5} voor de vermenigvuldiger en de exponent selecteert, retourneert de functie {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Retourneert de negatieve exponent voor waarden in de geselecteerde kolom.  

### <a name="factorial"></a>Faculteit
Retourneert de Faculteit voor waarden in de geselecteerde kolom.  

### <a name="hypotenuse"></a>Hypotenuse
Berekent de hypotenuse voor een drie hoek waarin de lengte van één zijde wordt opgegeven als een kolom met waarden en de lengte van de tweede zijde wordt opgegeven als een constante of als twee kolommen.  

### <a name="ln"></a>Ln

Retourneert de natuurlijke logaritme voor de waarden in de geselecteerde kolom.  

### <a name="lnplus1"></a>LnPlus1

Retourneert de natuurlijke logaritme plus één voor de waarden in de geselecteerde kolom.  

### <a name="log"></a>Logboek

Retourneert het logboek van de waarden in de geselecteerde kolom, op basis van het opgegeven grondtal.  

U kunt de basis waarde (het tweede argument) opgeven als constante of door een andere kolom met waarden te selecteren.  

### <a name="log10"></a>Log10

Retourneert de grondtal 10-logaritme waarden voor de geselecteerde kolom.  

### <a name="log2"></a>Log2

Retourneert de grondtal 2-logaritme voor de geselecteerde kolom.  

### <a name="nthroot"></a>NthRoot
Retourneert het ne hoofd van de waarde, met behulp van een n die u opgeeft.  

Selecteer de kolommen waarvoor u de basis wilt berekenen met behulp van de optie **kolomset** .  

Selecteer in **tweede type argument** een andere kolom die de hoofdmap bevat of geef een constante op om te gebruiken als basis.  

Als het tweede argument een kolom is, wordt elke waarde in de kolom gebruikt als de waarde n voor de corresponderende rij. Als het tweede argument een constante is, typt u de waarde voor n in het tekstvak **tweede argument** .
### <a name="pow"></a>Pow

Berekent X verheven tot de macht van Y voor elk van de waarden in de geselecteerde kolom.  

Selecteer eerst de kolommen die de **basis** bevatten, die een float moet zijn met behulp van de optie **kolomset** .  

In **tweede argument type** selecteert u de kolom die de exponent bevat of geeft u een constante op die als exponent moet worden gebruikt.  

Als het tweede argument een kolom is, wordt elke waarde in de kolom gebruikt als exponent voor de corresponderende rij. Als het tweede argument een constante is, typt u de waarde voor de exponent in het tekstvak **tweede argument** .  

### <a name="sqrt"></a>Sqrt

Retourneert de vierkantswortel van de waarden in de geselecteerde kolom.  

### <a name="sqrtpi"></a>SqrtPi

Voor elke waarde in de geselecteerde kolom vermenigvuldigt u de waarde met pi en retourneert vervolgens de vierkantswortel van het resultaat.  

### <a name="square"></a>Square

Kwadraten de waarden in de geselecteerde kolom.  

## <a name="comparison-operations"></a>Vergelijkings bewerkingen  

Gebruik de vergelijkings functies in Azure Machine Learning Designer op elk gewenst moment dat u twee sets waarden voor elkaar moet testen. Stel dat u in een pijp lijn deze vergelijkings bewerkingen moet uitvoeren:  

- Een kolom met waarschijnlijkheids scores op basis van een drempel waarde evalueren.
- Bepaal of twee sets resultaten hetzelfde zijn. Voor elke rij die afwijkt, voegt u een onwaare vlag toe die kan worden gebruikt voor verdere verwerking of filtering.  

### <a name="equalto"></a>EqualTo

Retourneert waar als de waarden gelijk zijn.  

### <a name="greaterthan"></a>GreaterThan

Retourneert waar als de waarden in de **kolomset** groter zijn dan de opgegeven constante, of groter zijn dan de overeenkomende waarden in de kolom vergelijking.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Retourneert waar als de waarden in de **kolomset** groter zijn dan of gelijk zijn aan de opgegeven constante, of groter dan of gelijk zijn aan de overeenkomende waarden in de vergelijkings kolom.  

### <a name="lessthan"></a>LessThan

Retourneert waar als de waarden in de **kolomset** kleiner zijn dan de opgegeven constante, of kleiner zijn dan de overeenkomende waarden in de kolom vergelijking.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Retourneert waar als de waarden in de **kolomset** kleiner zijn dan of gelijk zijn aan de opgegeven constante, of kleiner dan of gelijk zijn aan de overeenkomende waarden in de kolom comparison.  

### <a name="notequalto"></a>NotEqualTo

Retourneert waar als de waarden in de **kolomset** niet gelijk zijn aan de kolom constant of comparison en retourneert False als ze gelijk zijn.  

### <a name="pairmax"></a>PairMax

Retourneert de waarde die groter is: de waarde in de **kolomset** of de waarde in de kolom constant of vergelijkend.  

### <a name="pairmin"></a>PairMin

Retourneert de waarde die lager is: de waarde in de **kolomset** of de waarde in de kolom constante of vergelijking  
  
##  <a name="arithmetic-operations"></a>Rekenkundige bewerkingen   

Bevat de eenvoudige reken kundige bewerkingen: optellen en aftrekken, delen en vermenigvuldigen.  Omdat de meeste bewerkingen binair zijn, moet u eerst de bewerking kiezen en vervolgens de kolom of getallen kiezen die u in de eerste en tweede argumenten wilt gebruiken.

De volg orde voor het delen en aftrekken is als volgt: 
- Aftrekken (Arg1_Arg2) = Arg1-ARG 2
- Deling (Arg1_Arg2) = Arg1/ARG 2

In de volgende tabel ziet u enkele voor beelden

Bewerking|Num1|Num2|Resultaten kolom|Resultaat waarde|
----|----|----|----|----
|Optellen|1|5|Toevoegen (Num2_Num1)| 6|
|Vermenigvuldigen|1|5|Meerdere (Num2_Num1)|5|
|Aftrekking|5|1|Aftrekken (Num2_Num1)|4|
|Aftrekking|0|1|Aftrekken (Num2_Num1)|-1|
|Afdeling|5|1|Delen (Num2_Num1)|5|
|Afdeling|1|0|Delen (Num2_Num1)|Oneindig|

### <a name="add"></a>Toevoegen

Geef de bron kolommen op met behulp van de **kolomset** en voeg vervolgens aan deze waarden een getal toe dat is opgegeven in het **tweede argument**.  

Als u de waarden in twee kolommen wilt toevoegen, kiest u een kolom of kolommen met **kolom sets** en kiest u vervolgens een tweede kolom met een **tweede argument**.  

### <a name="divide"></a>Delen

Hiermee worden de waarden in de **kolom ingesteld** op basis van een constante of de kolom waarden die zijn gedefinieerd in het **tweede argument**.  Met andere woorden, u kiest eerst de deler en vervolgens het deeltal. De uitvoer waarde is het quotiënt.

### <a name="multiply"></a>Vermenigvuldigen

Vermenigvuldigt de waarden in de kolom die zijn **ingesteld** op basis van de opgegeven constante of kolom waarden.  

### <a name="subtract"></a>Aftrekken

Geef de kolom met waarden op die u wilt gebruiken (de *aftrek getal*) door een andere kolom te kiezen met behulp van de **kolom set** optie. Geef vervolgens het aantal op dat moet worden afgetrokken ( *aftrekker*) met behulp van de tweede vervolg keuzelijst voor **argumenten** . U kunt een constante of een kolom met waarden kiezen.

##  <a name="rounding-operations"></a>Afrondings bewerkingen 

Azure Machine Learning Designer ondersteunt diverse Afrondings bewerkingen. Voor veel bewerkingen moet u de hoeveelheid precisie opgeven die moet worden gebruikt bij het afronden. U kunt een statisch nauwkeurigheids niveau gebruiken dat is opgegeven als een constante, of u kunt een dynamische precisie waarde Toep assen die is verkregen van een kolom met waarden.  

- Als u een constante gebruikt, stelt u het **type precisie** in op **constant** en typt u het aantal cijfers als een geheel getal in het tekstvak **constante precisie** . Als u een niet-geheel getal typt, wordt er door de module geen fout gegenereerd, maar de resultaten kunnen onverwacht zijn.  

- Als u een andere precisie waarde voor elke rij in uw gegevensset wilt gebruiken, stelt u het **type precisie** in op **kolomset** en kiest u vervolgens de kolom die de juiste precisie waarden bevat.  

### <a name="ceiling"></a>Ceiling

Retourneert het plafond voor de waarden in de **kolomset**.  

### <a name="ceilingpower2"></a>CeilingPower2

Retourneert het kwadratische plafond voor de waarden in de **kolomset**.  

### <a name="floor"></a>Floor

Retourneert de vloer voor de waarden in de **kolomset** naar de opgegeven precisie.  

### <a name="mod"></a>Mod

Retourneert het gedeelte van de waarden in de **kolomset**, naar de opgegeven precisie.  

### <a name="quotient"></a>Quotiënt

Retourneert het gedeelte van de waarden in de **kolomset**, naar de opgegeven precisie.  

### <a name="remainder"></a>Rest

Retourneert de rest van de waarden in de **kolomset**.  

### <a name="rounddigits"></a>RoundDigits

Retourneert de waarden in de **kolomset**, afgerond met de 4/5-regel naar het opgegeven aantal cijfers.  

### <a name="rounddown"></a>RoundDown

Retourneert de waarden in de **kolomset**, naar beneden afgerond op het opgegeven aantal cijfers.  

### <a name="roundup"></a>RoundUp

Retourneert de waarden in de **kolomset**, naar boven afgerond op het opgegeven aantal cijfers.  

### <a name="toeven"></a>ToEven

Retourneert de waarden in de **kolomset**, afgerond op het dichtstbijzijnde volledige, even getal.  

### <a name="toodd"></a>ToOdd

Retourneert de waarden in de **kolomset**, afgerond op het dichtstbijzijnde volledige, oneven getal.  

### <a name="truncate"></a>Truncate

De waarden in de **kolomset** worden afgekapt door alle cijfers te verwijderen die niet zijn toegestaan door de opgegeven precisie.  
  
## <a name="special-math-functions"></a>Speciale wiskundige functies

Deze categorie bevat gespecialiseerde wiskundige functies die vaak worden gebruikt in data Science. Tenzij anders vermeld, is de functie Unair en retourneert de opgegeven berekening voor elke waarde in de geselecteerde kolom of kolommen.  

### <a name="beta"></a>Bèta

Retourneert de waarde van de bèta functie van Euler.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Retourneert de waarde van de onvolledige elliptische integraal.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Retourneert de waarde van de volledige elliptische integraal (K).  

### <a name="erf"></a>Fout

Retourneert de waarde van de fout functie.  

De functie Error (ook wel de fout functie Gauss genoemd) is een speciale functie van de sigmoid-vorm die in de kans wordt gebruikt om de verspreiding te beschrijven.  

### <a name="erfc"></a>Erfc

Retourneert de waarde van de fout functie complementair.  

`Erfc` is gedefinieerd als 1 – fout (x).  

### <a name="erfscaled"></a>ErfScaled

Retourneert de waarde van de functie voor geschaalde fouten.  

De geschaalde versie van de functie Error kan worden gebruikt om reken kundige negatieve flow te voor komen.  

### <a name="erfinverse"></a>ErfInverse

Retourneert de waarde van de inverse `erf` functie.  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Retourneert de waarde van de exponentiële integraal ei.  

### <a name="gamma"></a>Gamma

Retourneert de waarde van de gamma-functie.  

### <a name="gammaln"></a>Gamma

Retourneert de natuurlijke logaritme van de gamma-functie.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Retourneert de waarde van de normale, onvolledige gamma functie.  

Deze functie gebruikt een tweede argument, dat kan worden voorzien van een constante of een kolom met waarden.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Retourneert de waarde van de omgekeerde, geregelde, onvolledige gamma functie.  

Deze functie gebruikt een tweede argument, dat kan worden voorzien van een constante of een kolom met waarden.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Retourneert de waarde van de normale, onvolledige gamma functie.  

Deze functie gebruikt een tweede argument, dat kan worden voorzien van een constante of een kolom met waarden.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Retourneert de waarde van de inverse geregulare gegeneraliseerde, onvolledige gamma-functie.

Deze functie gebruikt een tweede argument, dat kan worden voorzien van een constante of een kolom met waarden.  

### <a name="polygamma"></a>Polygamma

Retourneert de waarde van de functie polygamma.  

Deze functie gebruikt een tweede argument, dat kan worden voorzien van een constante of een kolom met waarden.

##  <a name="trigonometric-functions"></a>Trigonometrische functies 

Deze categorie iIncludes het meren deel van de belangrijkste trigonometrische en inverse trigonometrische functies. Alle trigonometrische functies zijn Unair en vereisen geen aanvullende argumenten.  

### <a name="acos"></a>Acos

Berekent de arccosinus voor de kolom waarden.  

### <a name="acosdegree"></a>AcosDegree

Berekent de arccosinus van de kolom waarden, in graden.  

### <a name="acosh"></a>Acosh

Berekent de hyperbolische arccosinus van de kolom waarden.  

### <a name="acot"></a>Acot

Berekent de boog cotangens van de kolom waarden.  

### <a name="acotdegrees"></a>AcotDegrees

Berekent de boog cotangens van de kolom waarden, in graden.  

### <a name="acoth"></a>Acoth

Berekent de hyperbolische boog cotangens van de kolom waarden.  

### <a name="acsc"></a>Acsc

Berekent de arccosecant van de kolom waarden.  

### <a name="acscdegrees"></a>AcscDegrees

Berekent de arccosecant van de kolom waarden, in graden.  
### <a name="asec"></a>Asec

Berekent de arcsecant van de kolom waarden.  

### <a name="asecdegrees"></a>AsecDegrees

Berekent de arcsecant van de kolom waarden, in graden.  

### <a name="asech"></a>Asech

Berekent de hyperbolische arcsecant van de kolom waarden.  

### <a name="asin"></a>Asin

Berekent de boog sinus van de kolom waarden.  

### <a name="asindegrees"></a>AsinDegrees

Berekent de boog sinus van de kolom waarden, in graden.  

### <a name="asinh"></a>Asinh

Hiermee wordt de hyperbolische boog voor de kolom waarden berekend.  

### <a name="atan"></a>Atan

Berekent de arctangens van de kolom waarden.  

### <a name="atandegrees"></a>AtanDegrees

Berekent de arctangens van de kolom waarden, in graden.  

### <a name="atanh"></a>Atanh

Berekent de hyperbolische arctangens van de kolom waarden.  

### <a name="cos"></a>Cos

Berekent de cosinus van de kolom waarden.  

### <a name="cosdegrees"></a>CosDegrees

Berekent de cosinus voor de kolom waarden, in graden.  

### <a name="cosh"></a>Cosh

Berekent de hyperbolische cosinus voor de kolom waarden.  

### <a name="cot"></a>Cot

Hiermee berekent u de cotangens voor de kolom waarden.  

### <a name="cotdegrees"></a>CotDegrees

Berekent de cotangens voor de kolom waarden, in graden.  

### <a name="coth"></a>Coth
Berekent de hyperbolische cotangens voor de kolom waarden.  

### <a name="csc"></a>CSC

Hiermee wordt de cosecans voor de kolom waarden berekend.  

### <a name="cscdegrees"></a>CscDegrees

Berekent de cosecans voor de kolom waarden, in graden.  

### <a name="csch"></a>Csch

Hiermee wordt de hyperbolische cosecans voor de kolom waarden berekend.  

### <a name="degreestoradians"></a>DegreesToRadians

Converteert graden naar radialen.  

### <a name="sec"></a>Seconde

Berekent de secans van de kolom waarden.  

### <a name="asecdegrees"></a>aSecDegrees

Berekent de secans voor de kolom waarden, in graden.  

### <a name="asech"></a>aSech

Berekent de secans hyperbolicus van de kolom waarden.  

### <a name="sign"></a>Teken

Retourneert het teken van de kolom waarden.  

### <a name="sin"></a>Sin

Berekent de sinus van de kolom waarden.  

### <a name="sinc"></a>Sinc

Berekent de sinus waarde van de kolom waarden.  

### <a name="sindegrees"></a>SinDegrees

Berekent de sinus voor de kolom waarden, in graden.  

### <a name="sinh"></a>Sinh

Berekent de hyperbolische sinus van de kolom waarden.  

### <a name="tan"></a>Tan

Berekent de tangens van de kolom waarden.  

### <a name="tandegrees"></a>TanDegrees

Berekent de tangens voor het argument, in graden.  

### <a name="tanh"></a>Tanh

Berekent de hyperbolische tangens van de kolom waarden.  
  
## <a name="technical-notes"></a>Technische opmerkingen

Wees voorzichtig wanneer u meer dan één kolom als de tweede operator selecteert. De resultaten zijn eenvoudig te begrijpen als de bewerking eenvoudig is, zoals het toevoegen van een constante aan alle kolommen. 

Stel dat uw gegevensset meerdere kolommen bevat en u de gegevensset aan zichzelf toevoegt. In de resultaten wordt elke kolom als volgt aan zichzelf toegevoegd:  
  
|Num1|Num2|Num3|Toevoegen (Num1_Num1)|Toevoegen (Num2_Num2)|Toevoegen (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Als u complexere berekeningen wilt uitvoeren, kunt u meerdere exemplaren van de **reken kundige bewerking** aan elkaar koppelen. U kunt bijvoorbeeld twee kolommen toevoegen door één instantie van de **wiskundige bewerking Toep assen** te gebruiken en vervolgens een andere instantie van de **reken kundige bewerking Toep assen** om de som te delen door een constante om het gemiddelde te verkrijgen.  
  
U kunt ook een van de volgende modules gebruiken om alle berekeningen tegelijk uit te voeren, met behulp van SQL, R of python-script:
 
+ [R-Script uitvoeren](execute-r-script.md)
+ [Python-script uitvoeren](execute-python-script.md)
+ [SQL-transformatie toepassen](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
