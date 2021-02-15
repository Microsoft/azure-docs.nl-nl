---
title: 'ML Studio (klassiek): net # aangepaste Neural-netwerken-Azure'
description: 'Syntaxis handleiding voor de specificatie taal net # Neural Networks. Meer informatie over het maken van aangepaste Neural-netwerk modellen in Azure Machine Learning Studio (klassiek).'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 5137b633f66088efbee41b96ba715eb3b18961dc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519249"
---
# <a name="guide-to-net-neural-network-specification-language-for-machine-learning-studio-classic"></a>Hand leiding voor de netwerk specificatie taal net # Neural voor Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Net # is een door micro soft ontwikkelde taal die wordt gebruikt voor het definiëren van complexe Neural-netwerk architecturen, zoals diepe Neural netwerken of convoluties van wille keurige dimensies. U kunt complexe structuren gebruiken om het leren van gegevens zoals afbeeldingen, Video's of audio te verbeteren.

In deze contexten kunt u een specificatie van het net # architectuur gebruiken:

+ Alle Neural-netwerk modules in Microsoft Azure Machine Learning Studio (klassiek): meerdere [klassen Neural netwerk](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [Neural netwerk met twee klassen](/azure/machine-learning/studio-module-reference/two-class-neural-network)en [Neural netwerk regressie](/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neural-netwerk functies in micro soft ML Server: [NeuralNet](/machine-learning-server/r-reference/microsoftml/neuralnet) en [RxNeuralNet](/machine-learning-server/r-reference/microsoftml/rxneuralnet)voor de R-taal en [rx_neural_network](/machine-learning-server/python-reference/microsoftml/rx-neural-network) voor python.


In dit artikel worden de basis concepten en syntaxis beschreven die nodig zijn voor het ontwikkelen van een aangepast Neural-netwerk met behulp van net #:

+ Neural-netwerk vereisten en het definiëren van de primaire onderdelen
+ De syntaxis en tref woorden van de specificatie taal net #
+ Voor beelden van aangepaste Neural-netwerken die zijn gemaakt met net #



## <a name="neural-network-basics"></a>Basis beginselen van Neural-netwerken

Een Neural-netwerk structuur bestaat uit knoop punten die zijn ingedeeld in lagen en gewogen verbindingen (of randen) tussen de knoop punten. De verbindingen zijn richting en elke verbinding heeft een bron knooppunt en een doel knooppunt.

Elke trainable-laag (een verborgen of een uitvoer laag) heeft een of meer **verbindings bundels**. Een verbindings bundel bestaat uit een bronlaag en een specificatie van de verbindingen van die bronlaag. Alle verbindingen in een opgegeven bundel share bron-en doel lagen. In net # wordt een verbindings bundel beschouwd als onderdeel van de doellaag van de bundel.

Net # ondersteunt verschillende soorten verbindings bundels, waarmee u de manier waarop invoer wordt toegewezen aan verborgen lagen kunt aanpassen en aan de uitvoer kunt toewijzen.

De standaard-of standaard bundel is een **volledige bundel**, waarbij elk knoop punt in de bronlaag is verbonden met elk knoop punt in de doellaag.

Daarnaast ondersteunt net # de volgende vier soorten geavanceerde verbindings bundels:

+ **Gefilterde bundels**. U kunt een predikaat definiëren met behulp van de locaties van het bron laag knooppunt en het knoop punt van de doel laag. Knoop punten zijn verbonden wanneer het predicaat True is.

+ **Convolutional bundels**. U kunt kleine groepen van knoop punten in de bronlaag definiëren. Elk knoop punt in de doellaag is verbonden met één groep knoop punten in de bronlaag.

+ **Bundels** bundels en **antwoord-normalisatie pakketten**. Deze zijn vergelijkbaar met convolutional-bundels in dat de gebruiker kleine groepen van knoop punten in de bronlaag definieert. Het verschil is dat het gewicht van de randen in deze bundels niet kan worden getraind. In plaats daarvan wordt een vooraf gedefinieerde functie toegepast op de waarden van het bron knooppunt om de waarde van het doel knooppunt te bepalen.


## <a name="supported-customizations"></a>Ondersteunde aanpassingen

De architectuur van Neural-netwerk modellen die u in Azure Machine Learning Studio (klassiek) maakt, kan uitgebreid worden aangepast met behulp van net #. U kunt:

+ Maak verborgen lagen en beheer het aantal knoop punten in elke laag.
+ Opgeven hoe lagen met elkaar moeten worden verbonden.
+ Definieer speciale connectiviteits structuren, zoals convoluties en gewichts delen.
+ Geef verschillende activerings functies op.

Zie [structure Specification](#structure-specifications)(Engelstalig) voor meer informatie over de syntaxis van de specificatie taal.

Voor voor beelden van het definiëren van Neural-netwerken voor enkele veelvoorkomende machine learning taken, van eenvoudig naar complex, Zie [voor beelden](#examples-of-net-usage).

## <a name="general-requirements"></a>Algemene vereisten

+ Er moet precies één uitvoer laag, ten minste één invoer laag en nul of meer verborgen lagen zijn.
+ Elke laag heeft een vast aantal knoop punten, die conceptueel zijn gerangschikt in een rechthoekige matrix met wille keurige dimensies.
+ Invoer lagen hebben geen gekoppelde getrainde para meters en vertegenwoordigen het punt waar de exemplaar gegevens het netwerk binnenkomen.
+ Trainable Layers (de verborgen en uitvoer lagen) hebben gerelateerde getrainde para meters, ook wel gewichten en bias genoemd.
+ De bron-en doel knooppunten moeten zich in afzonderlijke lagen bedelen.
+ Verbindingen moeten acyclische zijn; met andere woorden, er kan geen keten van verbindingen worden gemaakt met het eerste bron knooppunt.
+ De uitvoer laag kan geen bronlaag van een verbindings bundel zijn.

## <a name="structure-specifications"></a>Structuur specificaties

Een specificatie van een Neural-netwerk structuur bestaat uit drie secties: de **constante declaratie**, de **laag declaratie**, de **verbindings declaratie**. Er is ook een optionele **share declaratie** sectie. De secties kunnen in een wille keurige volg orde worden opgegeven.

## <a name="constant-declaration"></a>Constanten declaratie

Een constante declaratie is optioneel. Het biedt een manier om waarden te definiëren die elders in de Neural-netwerk definitie worden gebruikt. De declaratie-instructie bestaat uit een id gevolgd door een gelijkteken en een waarde-expressie.

Met de volgende instructie wordt bijvoorbeeld een constante gedefinieerd `x` :

`Const X = 28;`

Als u twee of meer constanten tegelijk wilt definiëren, plaatst u de id-namen en waarden tussen accolades en scheidt u deze met behulp van punt komma's. Bijvoorbeeld:

`Const { X = 28; Y = 4; }`

De rechter kant van elke toewijzings expressie kan een geheel getal, een reëel getal, een Booleaanse waarde (waar of onwaar) of een wiskundige expressie zijn. Bijvoorbeeld:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Laag declaratie

De laag declaratie is vereist. Hiermee worden de grootte en de bron van de laag gedefinieerd, inclusief de verbindings bundels en-kenmerken. De instructie Declare begint met de naam van de laag (invoer, verborgen of uitvoer), gevolgd door de afmetingen van de laag (een tuple van positieve gehele getallen). Bijvoorbeeld:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Het product van de dimensies is het aantal knoop punten in de laag. In dit voor beeld zijn er twee dimensies [5, 20], wat betekent dat er 100 knoop punten in de laag zijn.
+ De lagen kunnen in een wille keurige volg orde worden gedeclareerd, met één uitzonde ring: als er meer dan één invoer laag is gedefinieerd, moet de volg orde waarin ze zijn gedeclareerd overeenkomen met de volg orde van de functies in de invoer gegevens.

Gebruik het sleutel woord om op te geven dat het aantal knoop punten in een laag automatisch moet worden bepaald `auto` . Het `auto` sleutel woord heeft verschillende gevolgen, afhankelijk van de laag:

+ In een declaratie van een invoer laag is het aantal knoop punten het aantal functies in de invoer gegevens.
+ In de declaratie van een verborgen laag is het aantal knoop punten het nummer dat is opgegeven door de parameter waarde voor het **aantal verborgen knoop punten**.
+ In een declaratie van een uitvoer laag is het aantal knoop punten 2 voor de classificatie met twee klassen, 1 voor regressie en gelijk aan het aantal uitvoer knooppunten voor de classificatie met meerdere klassen.

Met de volgende netwerk definitie kan de grootte van alle lagen bijvoorbeeld automatisch worden bepaald:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Een laag declaratie voor een trainable laag (de verborgen of uitvoer lagen) kan optioneel de functie output (ook wel een activerings functie genoemd) bevatten, die standaard wordt **sigmoid** voor classificatie modellen en **lineaire** voor regressie modellen. Zelfs als u de standaard waarde gebruikt, kunt u de activerings functie expliciet aangeven, indien gewenst voor de duidelijkheid.

De volgende uitvoer functies worden ondersteund:

+ sigmoid
+ lineaire
+ softmax
+ rlinear
+ wortel
+ wortel
+ srlinear
+ afwezigheid
+ TANH
+ brlinear

De volgende declaratie maakt bijvoorbeeld gebruik van de functie **Softmax** :

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Verbindings declaratie

Onmiddellijk nadat de trainable-laag is gedefinieerd, moet u verbindingen declareren tussen de lagen die u hebt gedefinieerd. De declaratie van de verbindings bundel begint met het tref woord `from` , gevolgd door de naam van de bron-laag van de bundel en het type verbindings bundel dat u wilt maken.

Op dit moment worden er vijf soorten verbindings bundels ondersteund:

+ **Volledige** bundels, aangegeven door het tref woord `all`
+ **Gefilterde** bundels, aangegeven door het tref woord `where` , gevolgd door een predicaat expressie
+ **Convolutional** -bundels, aangegeven door het tref woord `convolve` , gevolgd door de convolutie kenmerken
+ Bundeling van **groepen** , aangeduid door de tref woorden **Max pool** of **gemiddelde Pool**
+ Bundels van **antwoord normalisatie** , aangegeven door de **reactie norm** van het tref woord

## <a name="full-bundles"></a>Volledige bundels

Een volledige verbindings bundel bevat een verbinding van elk knoop punt in de bronlaag naar elk knoop punt in de doellaag. Dit is het standaard type netwerk verbinding.

## <a name="filtered-bundles"></a>Gefilterde bundels

Een gefilterde specificatie van de verbindings bundel bevat een predikaat, een syntactisch, vergelijkbaar met een C# lambda-expressie. In het volgende voor beeld worden twee gefilterde bundels gedefinieerd:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ In het predikaat voor `ByRow` `s` is een para meter voor een index in de rechthoekige matrix van knoop punten van de invoer laag, `Pixels` en `d` is een para meter die een index vertegenwoordigt in de matrix van knoop punten van de verborgen laag `ByRow` . Het type van beide `s` en `d` is een tuple met gehele getallen van lengte twee. In het algemeen worden `s` de bereiken van alle paren met gehele getallen met en en de `0 <= s[0] < 10` `0 <= s[1] < 20` `d` bereiken van alle paren met gehele getallen, met `0 <= d[0] < 10` en `0 <= d[1] < 12` .

+ Aan de rechter kant van de predicaat expressie bevindt zich een voor waarde. In dit voor beeld is voor elke waarde van `s` en `d` zodanig dat de voor waarde waar is, er een rand van het knoop punt van de bron laag naar het knoop punt van de doel laag. Deze filter expressie geeft dus aan dat de bundel een verbinding bevat van het knoop punt `s` dat is gedefinieerd door naar het knoop punt dat is gedefinieerd door `d` in alle gevallen waarbij s [0] gelijk is aan d [0].

Desgewenst kunt u een set gewichten voor een gefilterde bundel opgeven. De waarde voor het kenmerk **Weights** moet een tuple van drijvende-komma waarden zijn met een lengte die overeenkomt met het aantal verbindingen dat door de bundel is gedefinieerd. Standaard worden de gewichten wille keurig gegenereerd.

Gewichts waarden worden gegroepeerd op de doel knooppunt index. Dat wil zeggen, als het eerste doel knooppunt is verbonden met K-bron knooppunten, de eerste `K` elementen van de tuple met **gewichten** de gewichten zijn voor het eerste doel knooppunt, in de volg orde van de bron index. Hetzelfde geldt voor de resterende doel knooppunten.

Het is mogelijk om gewichten rechtstreeks als constante waarden op te geven. Als u bijvoorbeeld het gewicht eerder hebt geleerd, kunt u ze als constanten opgeven met behulp van de volgende syntaxis:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Convolutional bundels

Wanneer de trainings gegevens een homogene structuur hebben, worden convolutional-verbindingen doorgaans gebruikt voor het leren van de gegevens op hoog niveau. Zo kan een ruimtelijke of tijdelijke maat eenheid in afbeeldings-, audio-of video gegevens tamelijk uniform zijn.

Convolutional-bundels maken gebruik van rechthoekige **kernels** die door de afmetingen schuiven. In wezen definieert elke kernel een set gewichten die worden toegepast in lokale groepen, aangeduid als **kernel-toepassingen**. Elke kernel-toepassing komt overeen met een knoop punt in de bronlaag, dat het **centrale knoop punt** wordt genoemd. Het gewicht van een kernel wordt gedeeld tussen verschillende verbindingen. In een convolutional bundel is elke kernel rechthoekig en zijn alle kernel-toepassingen even groot.

Convolutional-bundels ondersteunen de volgende kenmerken:

**InputShape** definieert de dimensionaliteit van de bronlaag voor de doel einden van deze convolutional-bundel. De waarde moet een tuple van positieve gehele getallen zijn. Het product van de gehele getallen moet gelijk zijn aan het aantal knoop punten in de bronlaag, maar anders hoeft het niet overeen te komen met de dimensionaliteit die voor de bronlaag is gedeclareerd. De lengte van deze tuple wordt de **ariteit** waarde voor de convolutional-bundel. Een ariteit verwijst doorgaans naar het aantal argumenten of operanden dat een functie kan uitvoeren.

Voor het definiëren van de vorm en locaties van de kernels, gebruikt u de kenmerken **KernelShape**, **stride**, **padding**, **LowerPad** en **UpperPad**:

+ **KernelShape**: (vereist) Hiermee wordt de dimensionaliteit van elke kernel voor de convolutional-bundel gedefinieerd. De waarde moet een tuple van positieve gehele getallen zijn met een lengte die gelijk is aan de ariteit van de bundel. Elk onderdeel van deze tuple mag niet groter zijn dan het bijbehorende onderdeel van **InputShape**.

+ **Stride**: (optioneel) Hiermee definieert u de verschuivende stap grootte van de convolutie (één stap grootte voor elke dimensie), de afstand tussen de centrale knoop punten. De waarde moet een tuple van positieve gehele getallen zijn met een lengte die de ariteit van de bundel is. Elk onderdeel van deze tuple mag niet groter zijn dan het bijbehorende onderdeel van **KernelShape**. De standaard waarde is een tuple met alle onderdelen die gelijk zijn aan een.

+ **Delen**: (optioneel) Hiermee definieert u het delen van gewichten voor elke dimensie van de convolutie. De waarde kan een enkele Booleaanse waarde zijn of een tuple van Boole-waarden met een lengte die de ariteit van de bundel is. Een enkele Booleaanse waarde is uitgebreid tot een tuple met de juiste lengte en alle onderdelen die gelijk zijn aan de opgegeven waarde. De standaard waarde is een tuple die uit alle werkelijke waarden bestaat.

+ **MapCount**: (optioneel) Hiermee wordt het aantal functie toewijzingen voor de convolutional-bundel gedefinieerd. De waarde kan een enkel positief geheel getal zijn of een tuple van positieve gehele getallen met een lengte die de ariteit van de bundel is. Een enkele integerwaarde wordt uitgebreid naar een tuple van de juiste lengte met de eerste onderdelen die gelijk zijn aan de opgegeven waarde en alle resterende onderdelen die gelijk zijn aan een. De standaard waarde is één. Het totale aantal functie overzichten is het product van de onderdelen van de tuple. De factoring van dit totale aantal over de onderdelen bepaalt hoe de functie waarden worden gegroepeerd in de doel knooppunten.

+ **Gewichten**: (optioneel) Hiermee wordt het oorspronkelijke gewicht voor de bundel gedefinieerd. De waarde moet een tuple van drijvende-komma waarden zijn met een lengte die het aantal kernels maal het aantal kernen per kernel is, zoals verderop in dit artikel wordt beschreven. De standaard gewichten worden wille keurig gegenereerd.

Er zijn twee sets eigenschappen die opvulling bepalen, de eigenschappen sluiten elkaar wederzijds uit:

+ **Opvulling**: (optioneel) Hiermee wordt bepaald of de invoer moet worden aangevuld met een **standaard opvullings schema**. De waarde kan een enkele Booleaanse waarde zijn, of kan een tuple van Boole-waarden zijn met een lengte die de ariteit van de bundel is.

    Een enkele Booleaanse waarde is uitgebreid tot een tuple met de juiste lengte en alle onderdelen die gelijk zijn aan de opgegeven waarde.

    Als de waarde voor een dimensie waar is, wordt de bron logisch opgevuld in die dimensie met cellen met nulwaarden ter ondersteuning van extra kernel-toepassingen, zodat de centrale knoop punten van de eerste en laatste kernels in die dimensie de eerste en laatste knoop punten in die dimensie in de bronlaag zijn. Daarom wordt het aantal "dummy"-knoop punten in elke dimensie automatisch bepaald, zodat er precies `(InputShape[d] - 1) / Stride[d] + 1` de kernels in de opgevulde bronlaag passen.

    Als de waarde voor een dimensie onwaar is, worden de kernels gedefinieerd zodat het aantal knoop punten aan elke zijde die wordt verlaten hetzelfde is (tot een verschil van 1). De standaard waarde van dit kenmerk is een tuple waarbij alle onderdelen gelijk zijn aan false.

+ **UpperPad** en **LowerPad**: (optioneel) bieden meer controle over de hoeveelheid opvulling die moet worden gebruikt. **Belang rijk:** Deze kenmerken kunnen worden gedefinieerd als en alleen als de bovenstaande **opvullings** eigenschap **_niet_*_ is gedefinieerd. De waarden moeten Tuples met een waarde van integers zijn met een lengte van de ariteit van de bundel. Als deze kenmerken zijn opgegeven, worden pop-knoop punten toegevoegd aan de onderste en bovenste uiteinden van elke dimensie van de invoer laag. Het aantal knoop punten dat is toegevoegd aan de onderste en bovenste uiteinden van elke dimensie wordt bepaald door respectievelijk _* LowerPad**[i] en **UpperPad**[i].

    Om ervoor te zorgen dat kernels alleen overeenkomen met "echte" knoop punten en niet naar Dummy-knoop punten, moet aan de volgende voor waarden worden voldaan:
  - Elk onderdeel van **LowerPad** moet strikt kleiner zijn dan `KernelShape[d]/2` .
  - Elk onderdeel van **UpperPad** mag niet groter zijn dan `KernelShape[d]/2` .
  - De standaard waarde van deze kenmerken is een tuple met alle onderdelen die gelijk zijn aan 0.

    Met de instelling **opvulling** = waar kunt u zoveel opvulling gebruiken als nodig is om het ' Center ' van de kernel in de werkelijke invoer te blijven. Hiermee wijzigt u de wiskunde voor het berekenen van de uitvoer grootte. Over het algemeen wordt de uitvoer grootte *D* berekend als `D = (I - K) / S + 1` , waarbij `I` de invoer grootte is, de kernel-grootte is, `K` `S` de stride en is een `/` geheel getal als scheidings teken (afronden naar nul). Als u UpperPad = [1, 1] instelt, is de invoer grootte in `I` feite 29, en dus `D = (29 - 5) / 2 + 1 = 13` . Wanneer **padding** = True is, wordt in wezen echter `I` door gegeven `K - 1` `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` . Door waarden op te geven voor **UpperPad** en **LowerPad** krijgt u veel meer controle over de opvulling dan als u alleen **opvulling** = True instelt.

Raadpleeg de volgende artikelen voor meer informatie over convolutional-netwerken en hun toepassingen:

+ [http://d2l.ai/chapter_convolutional-neural-networks/lenet.html ](http://d2l.ai/chapter_convolutional-neural-networks/lenet.html )
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Bundels van groepen

Een **pooling bundel** past geometrie toe vergelijkbaar met convolutional-connectiviteit, maar gebruikt vooraf gedefinieerde functies voor waarden van het bron knooppunt om de waarde van het doel knooppunt af te leiden. Pooling bundels hebben daarom geen treinloze status (gewicht of bias). Pooling bundels bieden ondersteuning voor alle convolutional-kenmerken behalve **delen**, **MapCount** en **gewichten**.

Normaal gesp roken overlappen de kernels die worden samenvatten door aangrenzende pooling-eenheden niet. Als stride [d] gelijk is aan KernelShape [d] in elke dimensie, is de laag die wordt verkregen de traditionele lokale pooling Layer, die meestal wordt gebruikt in convolutional Neural Networks. Elk doel knooppunt berekent het maximum of het gemiddelde van de activiteiten van de kernel in de bronlaag.

In het volgende voor beeld ziet u een pooling bundel:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ De ariteit van de bundel is 3: de lengte van de Tuples `InputShape` , `KernelShape` en `Stride` .
+ Het aantal knoop punten in de bronlaag is `5 * 24 * 24 = 2880` .
+ Dit is een traditionele lokale pooling-laag, omdat **KernelShape** en **stride** gelijk zijn.
+ Het aantal knoop punten in de doellaag is `5 * 12 * 12 = 1440` .

Zie de volgende artikelen voor meer informatie over het groeperen van lagen:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sectie 3,4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Bundels van antwoord normalisatie

**Antwoord normalisatie** is een lokaal normalisatie schema dat voor het eerst werd geïntroduceerd door Geoffrey Hinton,, et al, in het paper [ImageNet-classificatie met diepe Convolutional Neural Networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Antwoord normalisatie wordt gebruikt om de generalisatie in neural-netten te helpen. Wanneer één neuron wordt geactiveerd op een zeer hoog activerings niveau, onderdrukt een lokale reactie-beveiligingslaag het activerings niveau van de omringende neurons. Dit wordt gedaan met behulp van drie para meters ( `α` , `β` en `k` ) en een convolutional-structuur (of groeps vorm). Elke neuron in de doellaag **y** komt overeen met een neuron **x** in de bronlaag. Het activerings niveau **y** wordt gegeven door de volgende formule, waarbij `f` het activerings niveau van een neuron is en `Nx` de kernel (of de set die de neurons bevat in de groep **x**), zoals gedefinieerd door de volgende convolutional-structuur:

![formule voor convolutional-structuur](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Bundels van antwoord normalisatie ondersteunen alle convolutional-kenmerken behalve **delen**, **MapCount** en **gewichten**.

+ Als de kernel neurons in dezelfde kaart bevat als ***x** _, wordt het normalisatie schema aangeduid als _ * dezelfde kaart normalisatie * *. Als u dezelfde toewijzings normalisatie wilt definiëren, moet de eerste coördinaat in **InputShape** de waarde 1 hebben.

+ Als de kernel neurons in dezelfde ruimtelijke positie bevat als ***x** _, maar de neurons zich in andere kaarten bevinden, wordt het normalisatie-schema de naam _ * genoemd in de vorm van normalisatie * *. Dit type antwoord normalisatie implementeert een vorm van een zijdelingse remming die is geïnspireerd op het type dat in real neurons is gevonden, waardoor er concurrentie wordt gemaakt voor grote activerings niveaus onder neuron uitvoer die op verschillende kaarten zijn berekend. Voor het definiëren van de toewijzings normalisatie moet de eerste coördinaat een geheel getal zijn dat groter is dan één en niet groter is dan het aantal kaarten, en de rest van de coördinaten moet de waarde 1 hebben.

Omdat de bundels van antwoord vragen een vooraf gedefinieerde functie Toep assen op de waarden van het bron knooppunt om de waarde van het doel knooppunt te bepalen, hebben ze geen treinloze status (gewicht of bias).

> [!NOTE]
> De knoop punten in de doellaag komen overeen met neurons die de centrale knoop punten van de kernels zijn. Zo `KernelShape[d]` is bijvoorbeeld een oneven getal `KernelShape[d]/2` dat overeenkomt met het centrale kernel-knoop punt. Als `KernelShape[d]` dit het geval is, is het centrale knoop punt op `KernelShape[d]/2 - 1` . Als daarom `Padding[d]` False is ingesteld, hebben de eerste en laatste `KernelShape[d]/2` knoop punten geen corresponderende knoop punten in de doellaag. Om deze situatie te voor komen, definieert u **opvulling** als [True, True,..., True].

Naast de vier kenmerken die eerder zijn beschreven, worden ook de volgende kenmerken ondersteund door bundels van antwoord normalisatie:

+ **Alfa**: (vereist) Hiermee geeft u een drijvende-komma waarde op die overeenkomt met `α` in de vorige formule.
+ **Bèta**: (vereist) Hiermee geeft u een drijvende-komma waarde op die overeenkomt met `β` in de vorige formule.
+ **Offset**: (optioneel) Hiermee geeft u een drijvende-komma waarde op die overeenkomt met `k` in de vorige formule. De standaard waarde is 1.

In het volgende voor beeld wordt een bundel met antwoord op basis van deze kenmerken gedefinieerd:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ De bronlaag bevat vijf kaarten, elk met een AOF dimensie van 12x12, in totaal 1440 knoop punten.
+ De waarde van **KernelShape** geeft aan dat dit een even laag is voor het toewijzen van een kaart, waarbij de groep een drie hoek rechthoek is.
+ De standaard waarde voor **opvulling** is onwaar, waardoor de doellaag slechts tien knoop punten in elke dimensie heeft. Als u één knoop punt in de doellaag wilt toevoegen dat overeenkomt met elk knoop punt in de bronlaag, voegt u padding = [True, True, True]; en wijzig de grootte van RN1 in [5, 12, 12].

## <a name="share-declaration"></a>Share declaratie

Net # biedt de mogelijkheid om meerdere bundels met gedeeld gewichten te definiëren. De gewichten van de twee bundels kunnen worden gedeeld als de bijbehorende structuren hetzelfde zijn. De volgende syntaxis definieert bundels met gedeeld gewichten:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

De volgende share-declaratie bevat bijvoorbeeld de laag namen, waarmee wordt aangegeven dat beide gewichten en BIASS moeten worden gedeeld:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ De invoer functies zijn gepartitioneerd in twee even grote invoer lagen.
+ De verborgen lagen berekenen vervolgens de functies op een hoger niveau op de twee invoer lagen.
+ De share-declaratie geeft aan dat *H1* en *H2* op dezelfde manier moeten worden berekend dan bij hun respectieve invoer.

U kunt dit ook opgeven met twee afzonderlijke shares-declaraties als volgt:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

U kunt het korte formulier alleen gebruiken wanneer de lagen één bundel bevatten. Over het algemeen is delen alleen mogelijk als de relevante structuur identiek is, wat betekent dat ze dezelfde grootte hebben, dezelfde convolutional geometrie, enzovoort.

## <a name="examples-of-net-usage"></a>Voor beelden van het gebruik van net #

In deze sectie vindt u enkele voor beelden van hoe u net # kunt gebruiken om verborgen lagen toe te voegen, de manier definiëren waarop verborgen lagen met andere lagen communiceren en convolutional-netwerken bouwen.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Een eenvoudig aangepast Neural-netwerk definiëren: Hallo wereld-voor beeld

In dit eenvoudige voor beeld ziet u hoe u een Neural-netwerk model met één verborgen laag maakt.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

In het voor beeld ziet u enkele eenvoudige opdrachten:

+ De eerste regel definieert de invoer laag (met de naam `Data` ). Wanneer u het  `auto` tref woord gebruikt, bevat het Neural-netwerk automatisch alle functie kolommen in de invoer voorbeelden.
+ De tweede regel maakt de verborgen laag. De naam `H` wordt toegewezen aan de verborgen laag, met 200 knoop punten. Deze laag is volledig verbonden met de invoer laag.
+ De derde regel definieert de uitvoer laag (met de naam `Out` ), die 10 uitvoer knooppunten bevat. Als het Neural-netwerk wordt gebruikt voor classificatie, is er één uitvoer knooppunt per klasse. Het tref woord **sigmoid** geeft aan dat de functie output wordt toegepast op de uitvoer laag.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Meerdere verborgen lagen definiëren: computer Vision-voor beeld

In het volgende voor beeld ziet u hoe u een iets complex Neural netwerk met meerdere aangepaste verborgen lagen definieert.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

In dit voor beeld ziet u verschillende functies van de Neural Networks Specification Language:

+ De structuur bevat twee invoer lagen, `Pixels` en `MetaData` .
+ De `Pixels` laag is een bronlaag voor twee verbindings bundels, met doel lagen, `ByRow` en `ByCol` .
+ De lagen `Gather` en `Result` zijn doel lagen in meerdere verbindings bundels.
+ De uitvoer laag, `Result` , is een doellaag in twee verbindings bundels, een met het tweede niveau verborgen laag `Gather` als doellaag en de andere met de invoercel `MetaData` als doellaag.
+ De verborgen lagen `ByRow` en `ByCol` , geeft u gefilterde connectiviteit op met behulp van predicaat expressies. Nauw keuriger is het knoop punt in `ByRow` op [x, y] verbonden met de knoop punten in `Pixels` die de eerste index coördinaat hebben die gelijk is aan de eerste coördinaat van het knoop punt, x. Op dezelfde manier is het knoop punt in `ByCol` op [x, y] verbonden met de knoop punten in `Pixels` die de tweede index coördinaat hebben binnen een van de tweede coördinaat van het knoop punt, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definieer een convolutional-netwerk voor de classificatie van een multi klasse: cijfer herkennings voorbeeld

De definitie van het volgende netwerk is ontworpen voor het herkennen van getallen en illustreert een aantal geavanceerde technieken voor het aanpassen van een Neural-netwerk.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ De structuur heeft één invoer laag, `Image` .
+ Het sleutel woord `convolve` geeft aan dat de lagen met de naam `Conv1` en `Conv2` convolutional lagen zijn. Elk van deze laag declaraties wordt gevolgd door een lijst met de convolutie kenmerken.
+ Het net heeft een derde verborgen laag, `Hid3` die volledig is verbonden met de tweede verborgen laag `Conv2` .
+ De uitvoer laag, `Digit` , is alleen verbonden met de derde verborgen laag, `Hid3` . Het sleutel woord `all` geeft aan dat de uitvoer laag volledig is verbonden `Hid3` .
+ De ariteit van de convolutie is drie: de lengte van de Tuples `InputShape` , `KernelShape` , `Stride` en `Sharing` .
+ Het aantal gewichten per kernel is `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26` . Of `26 * 50 = 1300` .
+ U kunt als volgt de knoop punten in elke verborgen laag berekenen:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Het totale aantal knoop punten kan worden berekend met behulp van de gedeclareerde dimensionaliteit van de laag, [50, 5, 5], als volgt: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Omdat `Sharing[d]` alleen False is voor `d == 0` , is het aantal kernels `MapCount * NodeCount\[0] = 10 * 5 = 50` .

## <a name="acknowledgements"></a>Dankbetuigingen

De net # taal voor het aanpassen van de architectuur van Neural-netwerken is ontwikkeld door micro soft Shon Katzenberger (architect, Machine Learning) en Alexey Kamenev (software engineer, micro soft Research). Het wordt intern gebruikt voor machine learning projecten en toepassingen variërend van afbeeldings detectie tot tekst analyse. Zie [neural-netten in azure machine learning Studio-Introduction to net # (](/archive/blogs/machinelearning/neural-nets-in-azure-ml-introduction-to-net) Engelstalig) voor meer informatie.