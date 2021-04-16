---
title: Automatisch schalen rekenknooppunten in een Azure Batch-pool
description: Schakel automatisch schalen voor een cloudpool in om het aantal rekenknooppunten in de pool dynamisch aan te passen.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: f70c29f0e8a313233991c7363dc4b8a41a1b1cd5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389363"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Een automatische formule maken voor het schalen van rekenknooppunten in een Batch-pool

Azure Batch kunt pools automatisch schalen op basis van parameters die u definieert, waardoor u tijd en geld bespaart. Met automatisch schalen voegt Batch dynamisch knooppunten toe aan een pool naarmate de vraag naar taken toeneemt en worden rekenknooppunten verwijderd naarmate de vraag naar taken afneemt.

Als u automatisch schalen wilt inschakelen voor een pool met rekenknooppunten, koppelt u de pool aan een *formule voor automatisch schalen* die u definieert. De Batch-service gebruikt de formule voor automatisch schalen om te bepalen hoeveel knooppunten er nodig zijn om uw workload uit te voeren. Deze knooppunten kunnen toegewezen knooppunten of [knooppunten met lage prioriteit zijn.](batch-low-pri-vms.md) Batch controleert vervolgens periodiek de metrische gegevens van de service en gebruikt deze om het aantal knooppunten in de pool aan te passen op basis van uw formule en met een interval dat u definieert.

U kunt automatisch schalen inschakelen wanneer u een pool maakt of deze toepassen op een bestaande pool. Met Batch kunt u uw formules evalueren voordat u ze toewijst aan pools en kunt u de status van automatische schalings runs controleren. Zodra u een pool met automatisch schalen hebt geconfigureerd, kunt u later wijzigingen aanbrengen in de formule.

> [!IMPORTANT]
> Wanneer u een Batch-account maakt, kunt u de pooltoewijzingsmodus opgeven, waarmee wordt bepaald of pools worden toegewezen in een Batch-serviceabonnement (de standaardinstelling) of in uw gebruikersabonnement. [](accounts.md) Als u uw Batch-account hebt gemaakt met de standaardconfiguratie van de Batch-service, is uw account beperkt tot een maximum aantal kernen dat kan worden gebruikt voor verwerking. De Batch-service schaalt rekenknooppunten alleen tot die kernlimiet. Daarom bereikt de Batch-service mogelijk niet het doelaantal rekenknooppunten dat is opgegeven door een formule voor automatisch schalen. Zie [Quota en limieten voor de Azure Batch service](batch-quota-limit.md) voor informatie over het weergeven en verhogen van uw accountquota.
>
>Als u uw account hebt gemaakt met de modus Gebruikersabonnement, deelt uw account het kernquotum voor het abonnement. Zie [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Limieten voor Virtuele Machines) in [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Azure-abonnement en servicelimieten, -quota en -beperkingen) voor meer informatie.

## <a name="autoscale-formulas"></a>Formules automatisch schalen

Een formule voor automatisch schalen is een tekenreekswaarde die u definieert en die een of meer instructies bevat. De formule voor automatisch schalen wordt toegewezen aan het element [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) van een pool (Batch REST) of de eigenschap [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Batch .NET). De Batch-service gebruikt uw formule om het doelaantal rekenknooppunten in de pool te bepalen voor het volgende verwerkingsinterval. De formulereeks mag niet groter zijn dan 8 kB, kan maximaal 100 instructies bevatten die worden gescheiden door puntkomma's en kan regelafbreaks en opmerkingen bevatten.

U kunt formules voor automatisch schalen zien als een 'taal' voor automatisch schalen in Batch. Formule-instructies zijn vrije expressies die zowel door de service gedefinieerde variabelen (gedefinieerd door de Batch-service) als door de gebruiker gedefinieerde variabelen kunnen bevatten. Formules kunnen verschillende bewerkingen op deze waarden uitvoeren met behulp van ingebouwde typen, operators en functies. Een instructie kan bijvoorbeeld de volgende vorm hebben:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formules bevatten over het algemeen meerdere instructies die bewerkingen uitvoeren op waarden die zijn verkregen in eerdere instructies. We verkrijgen bijvoorbeeld eerst een waarde voor en geven deze `variable1` vervolgens door aan een functie om te `variable2` vullen:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Neem deze instructies op in uw formule voor automatisch schalen om een doelaantal rekenknooppunten te bereiken. Toegewezen knooppunten en knooppunten met lage prioriteit hebben elk hun eigen doelinstellingen. Een formule voor automatisch schalen kan een doelwaarde voor toegewezen knooppunten, een doelwaarde voor knooppunten met lage prioriteit of beide bevatten.

Het doelaantal knooppunten kan hoger, lager of gelijk zijn aan het huidige aantal knooppunten van dat type in de pool. Batch evalueert de formule voor automatisch schalen van een pool met een specifiek [automatisch schalingsinterval.](#automatic-scaling-interval) Batch past het doelaantal van elk type knooppunt in de pool aan aan het getal dat uw formule voor automatisch schalen op het moment van de evaluatie aangeeft.

### <a name="sample-autoscale-formulas"></a>Voorbeelden van formules voor automatisch schalen

Hieronder vindt u voorbeelden van twee formules voor automatisch schalen, die voor de meeste scenario's kunnen worden aangepast. De variabelen `startingNumberOfVMs` en in de `maxNumberofVMs` voorbeeldformules kunnen worden aangepast aan uw behoeften.

#### <a name="pending-tasks"></a>Taken in behandeling

Met deze formule voor automatisch schalen wordt de pool in eerste instantie gemaakt met één VM. De `$PendingTasks` metriek definieert het aantal taken dat wordt uitgevoerd of in de wachtrij wordt geplaatst. De formule zoekt het gemiddelde aantal taken in behandeling in de afgelopen 180 seconden en stelt de `$TargetDedicatedNodes` variabele dienovereenkomstig in. De formule zorgt ervoor dat het doelaantal toegewezen knooppunten nooit groter is dan 25 VM's. Wanneer nieuwe taken worden verzonden, wordt de pool automatisch groter. Als de taken zijn voltooid, worden VM's gratis en wordt de pool verkleind met de formule voor automatisch schalen.

Met deze formule worden toegewezen knooppunten geschaald, maar deze kunnen ook worden aangepast om knooppunten met lage prioriteit te schalen.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Verwijderde knooppunten

In dit voorbeeld wordt een pool gemaakt die begint met 25 knooppunten met lage prioriteit. Telkens als een knooppunt met lage prioriteit wordt vereend, wordt het vervangen door een toegewezen knooppunt. Net als in het eerste voorbeeld voorkomt `maxNumberofVMs` de variabele dat de pool 25 VM's overschrijdt. Dit voorbeeld is handig om gebruik te maken van VM's met lage prioriteit en om ervoor te zorgen dat er slechts een vast aantal toe-eigeningen optreedt voor de levensduur van de pool.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Verder in dit onderwerp leert u meer over het maken van [formules](#write-an-autoscale-formula) voor automatisch schalen en ziet u aanvullende voorbeeldformules voor automatisch schalen. [](#example-autoscale-formulas)

## <a name="variables"></a>Variabelen

U kunt zowel **door de service gedefinieerde** als **door de gebruiker gedefinieerde** variabelen gebruiken in uw formules voor automatisch schalen.

De door de service gedefinieerde variabelen zijn ingebouwd in de Batch-service. Sommige door de service gedefinieerde variabelen zijn lezen/schrijven en sommige variabelen zijn alleen-lezen.

Door de gebruiker gedefinieerde variabelen zijn variabelen die u definieert. In de bovenstaande voorbeeldformule zijn `$TargetDedicatedNodes` en door de service `$PendingTasks` gedefinieerde variabelen, terwijl `startingNumberOfVMs` en door de gebruiker `maxNumberofVMs` gedefinieerde variabelen zijn.

> [!NOTE]
> Door de service gedefinieerde variabelen worden altijd voorafgegaan door een dollarteken ($). Voor door de gebruiker gedefinieerde variabelen is het dollarteken optioneel.

De volgende tabellen tonen de variabelen read-write en read-only die zijn gedefinieerd door de Batch-service.

### <a name="read-write-service-defined-variables"></a>Door de lees-schrijfservice gedefinieerde variabelen

U kunt de waarden van deze door de service gedefinieerde variabelen op halen en instellen om het aantal rekenknooppunten in een pool te beheren.

| Variabele | Beschrijving |
| --- | --- |
| $TargetDedicatedNodes |Het doelaantal toegewezen rekenknooppunten voor de pool. Dit wordt opgegeven als een doel, omdat een pool mogelijk niet altijd het gewenste aantal knooppunten bereikt. Als het doelaantal toegewezen knooppunten bijvoorbeeld wordt gewijzigd door een evaluatie van automatisch schalen voordat de pool het eerste doel heeft bereikt, bereikt de pool mogelijk het doel niet. <br /><br /> Een pool in een account dat is gemaakt in de Batch-servicemodus bereikt mogelijk niet het doel als het doel een Batch-account-knooppunt of kernquotum overschrijdt. Een pool in een account dat is gemaakt in de modus gebruikersabonnement bereikt mogelijk niet het doel als het doel het quotum voor gedeelde kernen voor het abonnement overschrijdt.|
| $TargetLowPriorityNodes |Het doelaantal rekenknooppunten met lage prioriteit voor de pool. Dit is opgegeven als een doel omdat een pool mogelijk niet altijd het gewenste aantal knooppunten bereikt. Als het doelaantal knooppunten met lage prioriteit bijvoorbeeld wordt gewijzigd door een evaluatie van automatisch schalen voordat de pool het eerste doel heeft bereikt, bereikt de pool mogelijk het doel niet. Een pool bereikt mogelijk ook het doel niet als het doel een Batch-account-knooppunt of kernquotum overschrijdt. <br /><br /> Zie VM's met lage prioriteit gebruiken met Batch voor meer informatie over rekenknooppunten [met lage prioriteit.](batch-low-pri-vms.md) |
| $NodeDeallocationOption |De actie die optreedt wanneer rekenknooppunten uit een pool worden verwijderd. Mogelijke waarden zijn:<ul><li>**requeue:** de standaardwaarde. Taken worden onmiddellijk beëindigd en weer in de taakwachtrij geplaatst, zodat ze opnieuw worden gepland. Deze actie zorgt ervoor dat het doelaantal knooppunten zo snel mogelijk wordt bereikt. Het kan echter minder efficiënt zijn, omdat alle lopende taken worden onderbroken en vervolgens volledig opnieuw moeten worden opgestart. <li>**terminate:** taken worden onmiddellijk beëindigd en uit de jobwachtrij verwijderd.<li>**taakcompletion:** wacht tot taken die momenteel worden uitgevoerd, zijn uitgevoerd en verwijdert vervolgens het knooppunt uit de pool. Gebruik deze optie om te voorkomen dat taken worden onderbroken en opnieuw in dequequeuie worden uitgevoerd, en alle werkzaamheden die de taak heeft uitgevoerd, te verspillen.<li>**retaineddata:** wacht tot alle lokale, taak behouden gegevens op het knooppunt zijn opgeschoond voordat het knooppunt uit de pool wordt verwijderd.</ul> |

> [!NOTE]
> De `$TargetDedicatedNodes` variabele kan ook worden opgegeven met behulp van de alias `$TargetDedicated` . Op dezelfde manier kan `$TargetLowPriorityNodes` de variabele worden opgegeven met behulp van de alias `$TargetLowPriority` . Als zowel de volledig benoemde variabele als de alias zijn ingesteld door de formule, heeft de waarde die is toegewezen aan de volledig benoemde variabele voorrang.

### <a name="read-only-service-defined-variables"></a>Alleen-lezen service-gedefinieerde variabelen

U kunt de waarde van deze door de service gedefinieerde variabelen krijgen om aanpassingen aan te brengen die zijn gebaseerd op metrische gegevens van de Batch-service.

> [!IMPORTANT]
> Job-releasetaken zijn momenteel niet opgenomen in variabelen die taaktellingen bieden, zoals $ActiveTasks en $PendingTasks. Afhankelijk van uw formule voor automatisch schalen kan dit ertoe leiden dat knooppunten worden verwijderd zonder dat er knooppunten beschikbaar zijn om jobre releasetaken uit te voeren.

| Variabele | Beschrijving |
| --- | --- |
| $CPUPercent |Het gemiddelde percentage cpu-gebruik. |
| $WallClockSeconds |Het aantal seconden dat is verbruikt. |
| $MemoryBytes |Het gemiddelde aantal gebruikte megabytes. |
| $DiskBytes |Het gemiddelde aantal gigabytes dat op de lokale schijven wordt gebruikt. |
| $DiskReadBytes |Het aantal gelezen bytes. |
| $DiskWriteBytes |Het aantal geschreven bytes. |
| $DiskReadOps |Het aantal uitgevoerde leesschijfbewerkingen. |
| $DiskWriteOps |Het aantal uitgevoerde schrijfschijfbewerkingen. |
| $NetworkInBytes |Het aantal binnenkomende bytes. |
| $NetworkOutBytes |Het aantal uitgaande bytes. |
| $SampleNodeCount |Het aantal rekenknooppunten. |
| $ActiveTasks |Het aantal taken dat gereed is om te worden uitgevoerd, maar nog niet wordt uitgevoerd. Dit omvat alle taken die zich in de actieve status en waarvan de afhankelijkheden zijn voldaan. Taken die zich in de actieve status maar waarvan niet is voldaan aan de afhankelijkheden worden uitgesloten van het $ActiveTasks aantal. Voor een taak met meerdere exemplaren $ActiveTasks het aantal instanties dat voor de taak is ingesteld.|
| $RunningTasks |Het aantal taken met de status Actief. |
| $PendingTasks |De som van $ActiveTasks en $RunningTasks. |
| $SucceededTasks |Het aantal taken dat is voltooid. |
| $FailedTasks |Het aantal mislukte taken. |
| $TaskSlotsPerNode |Het aantal taaksleuven dat kan worden gebruikt om gelijktijdige taken uit te voeren op één reken knooppunt in de pool. |
| $CurrentDedicatedNodes |Het huidige aantal toegewezen rekenknooppunten. |
| $CurrentLowPriorityNodes |Het huidige aantal rekenknooppunten met lage prioriteit, inclusief eventuele knooppunten die zijn vereend. |
| $PreemptedNodeCount | Het aantal knooppunten in de pool met een vereende status. |

> [!TIP]
> Deze alleen-lezen service-gedefinieerde variabelen zijn *objecten* die verschillende methoden bieden voor toegang tot de gegevens die aan elk object zijn gekoppeld. Zie Voorbeeldgegevens verkrijgen verder [in](#obtain-sample-data) dit artikel voor meer informatie.

> [!NOTE]
> Gebruik bij het schalen op basis van het aantal taken dat op een bepaald moment wordt uitgevoerd en bij het schalen op basis van het aantal taken dat in de wachtrij staat om `$RunningTasks` `$ActiveTasks` te worden uitgevoerd.

## <a name="types"></a>Typen

Formules voor automatisch schalen ondersteunen de volgende typen:

- double
- doubleVec
- doubleVecList
- tekenreeks
- timestamp: een samengestelde structuur die de volgende leden bevat:
  - jaar
  - maand (1-12)
  - dag (1-31)
  - weekdag (in de notatie van getal; bijvoorbeeld 1 voor maandag)
  - uur (in getalnotatie van 24 uur; 13 betekent bijvoorbeeld 1 PM)
  - minuut (00-59)
  - seconde (00-59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>Operations

Deze bewerkingen zijn toegestaan voor de typen die in de vorige sectie worden vermeld.

| Bewerking | Ondersteunde operators | Resultaattype |
| --- | --- | --- |
| dubbele *operator* |+, -, *, / |double |
| double *operator* timeinterval |* |timeinterval |
| *doubleVec-operator dubbel* |+, -, *, / |doubleVec |
| *doubleVec-operator* doubleVec |+, -, *, / |doubleVec |
| *timeinterval-operator* dubbel |*, / |timeinterval |
| *timeinterval-operator* timeinterval |+, - |timeinterval |
| tijdstempel van *timeinterval-operator* |+ |tijdstempel |
| timetamp *operator* timeinterval |+ |tijdstempel |
| timestamp *operator* timestamp |- |timeinterval |
| *operator* double |-, ! |double |
| *tijdinterval* van operator |- |timeinterval |
| dubbele *operator double* |<, <=, ==, >=, >, != |double |
| *tekenreeksoperator* |<, <=, ==, >=, >, != |double |
| timestamp *operator* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operator* timeinterval |<, <=, ==, >=, >, != |double |
| dubbele *operator double* |&&,  &#124;&#124; |double |

Wanneer u een dubbel test met een ternaire operator ( `double ? statement1 : statement2` ), is nonzero **true** en nul **onwaar.**

## <a name="functions"></a>Functions

U kunt deze vooraf gedefinieerde functies gebruiken **bij** het definiëren van een formule voor automatisch schalen.

| Functie | Retourtype | Beschrijving |
| --- | --- | --- |
| avg(doubleVecList) |double |Retourneert de gemiddelde waarde voor alle waarden in de doubleVecList. |
| len(doubleVecList) |double |Retourneert de lengte van de vector die is gemaakt op de doubleVecList. |
| lg (dubbel) |double |Retourneert de logboekbasis 2 van de dubbele. |
| lg(doubleVecList) |doubleVec |Retourneert het onderdeelgemiddelde logboekbasis 2 van de doubleVecList. Een vec(double) moet expliciet worden doorgegeven voor de parameter . Anders wordt ervan uitgegaan dat de dubbele lg(double)-versie wordt gebruikt. |
| ln(dubbel) |double |Retourneert het natuurlijke logboek van de dubbele. |
| ln(doubleVecList) |doubleVec |Retourneert het natuurlijke logboek van de dubbele. |
| log (dubbel) |double |Retourneert de logboekbasis 10 van de dubbele. |
| log(doubleVecList) |doubleVec |Retourneert het onderdeelgemiddelde logboekbasis 10 van de doubleVecList. Een vec(double) moet expliciet worden doorgegeven voor de enkele dubbele parameter. Anders wordt ervan uitgegaan dat de dubbele logversie wordt aangenomen. |
| max(doubleVecList) |double |Retourneert de maximumwaarde in de doubleVecList. |
| min(doubleVecList) |double |Retourneert de minimumwaarde in de doubleVecList. |
| norm(doubleVecList) |double |Retourneert de twee-norm van de vector die is gemaakt op de doubleVecList. |
| percentiel (doubleVec v, double p) |double |Retourneert het percentielelement van de vector v. |
| rand() |double |Retourneert een willekeurige waarde tussen 0,0 en 1,0. |
| range(doubleVecList) |double |Retourneert het verschil tussen de minimum- en maximumwaarden in de doubleVecList. |
| std(doubleVecList) |double |Retourneert de standaardafwijking van het voorbeeld van de waarden in doubleVecList. |
| stop() | |Stopt de evaluatie van de expressie voor automatisch schalen. |
| sum(doubleVecList) |double |Retourneert de som van alle onderdelen van de doubleVecList. |
| time(string dateTime="") |tijdstempel |Retourneert het tijdstempel van de huidige tijd als er geen parameters zijn doorgegeven, of het tijdstempel van de datum/tijd-tekenreeks als die wordt doorgegeven. Ondersteunde datum/tijd-indelingen zijn W3C-DTF en RFC 1123. |
| val(doubleVec v, double i) |double |Retourneert de waarde van het element dat zich op de locatie i in vector v bevindt, met een beginindex van nul. |

Sommige van de functies die in de vorige tabel worden beschreven, kunnen een lijst als argument accepteren. De door komma's gescheiden lijst is een combinatie van *double* en *doubleVec.* Bijvoorbeeld:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

De *waarde doubleVecList* wordt vóór de evaluatie geconverteerd naar één *doubleVec.* Als u bijvoorbeeld `v = [1,2,3]` aanroept, is aanroepen `avg(v)` gelijk aan het aanroepen `avg(1,2,3)` van . `avg(v, 7)`Aanroepen is gelijk aan het aanroepen van `avg(1,2,3,7)` .

## <a name="metrics"></a>Metrische gegevens

U kunt zowel metrische gegevens voor resources als taken gebruiken bij het definiëren van een formule. U past het doelaantal toegewezen knooppunten in de pool aan op basis van de metrische gegevens die u verkrijgt en evalueert. Zie de sectie Variabelen hierboven voor meer informatie [over](#variables) elk metrisch gegeven.

<table>
  <tr>
    <th>Metrisch</th>
    <th>Beschrijving</th>
  </tr>
  <tr>
    <td><b>Resource</b></td>
    <td><p>Metrische resourcegegevens zijn gebaseerd op de CPU, de bandbreedte, het geheugengebruik van rekenknooppunten en het aantal knooppunten.</p>
        <p> Deze door de service gedefinieerde variabelen zijn handig voor het aanbrengen van aanpassingen op basis van het aantal knooppunt:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Deze door de service gedefinieerde variabelen zijn handig voor het aanbrengen van aanpassingen op basis van het resourcegebruik van knooppunt:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Taak</b></td>
    <td><p>Metrische gegevens voor taken zijn gebaseerd op de status van taken, zoals Actief, In behandeling en Voltooid. De volgende door de service gedefinieerde variabelen zijn handig voor het aanpassen van de grootte van de pool op basis van metrische gegevens van taken:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Voorbeeldgegevens verkrijgen

De kernbewerking van een formule voor automatisch schalen is het verkrijgen van metrische gegevens van taken en resources (voorbeelden) en vervolgens de poolgrootte aanpassen op basis van die gegevens. Daarom is het belangrijk om een duidelijk inzicht te hebben in de manier waarop formules voor automatisch schalen communiceren met voorbeelden.

### <a name="methods"></a>Methoden

Formules voor automatisch schalen worden gebruikt voor voorbeelden van metrische gegevens die worden geleverd door de Batch-service. Met een formule wordt de poolgrootte groter of kleiner op basis van de waarden die worden ontvangen. Door de service gedefinieerde variabelen zijn objecten die methoden bieden voor toegang tot gegevens die aan dat object zijn gekoppeld. De volgende expressie toont bijvoorbeeld een aanvraag om de laatste vijf minuten cpu-gebruik op te halen:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

De volgende methoden kunnen worden gebruikt voor het verkrijgen van voorbeeldgegevens over door de service gedefinieerde variabelen.

| Methode | Beschrijving |
| --- | --- |
| GetSample() |De `GetSample()` methode retourneert een vector van gegevensvoorbeelden.<br/><br/>Een voorbeeld is 30 seconden aan metrische gegevens. Met andere woorden, voorbeelden worden elke 30 seconden verkregen. Maar zoals hieronder wordt vermeld, is er een vertraging tussen het moment waarop een voorbeeld wordt verzameld en wanneer het beschikbaar is voor een formule. Daarom zijn niet alle voorbeelden voor een bepaalde periode beschikbaar voor evaluatie door een formule.<ul><li>`doubleVec GetSample(double count)`: hiermee geeft u het aantal voorbeelden op dat moet worden opgehaald uit de meest recente voorbeelden die zijn verzameld. `GetSample(1)` retourneert het laatst beschikbare voorbeeld. Voor metrische gegevens zoals moet echter niet worden gebruikt, omdat het onmogelijk is om te weten wanneer `$CPUPercent` `GetSample(1)` het voorbeeld is verzameld.  Het kan recent zijn of, vanwege systeemproblemen, het kan veel ouder zijn. In dergelijke gevallen is het beter om een tijdsinterval te gebruiken, zoals hieronder wordt weergegeven.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Hiermee geeft u een tijdsbestek op voor het verzamelen van voorbeeldgegevens. Optioneel wordt ook het percentage voorbeelden opgegeven dat beschikbaar moet zijn binnen het aangevraagde tijdsbestek. Retourneert `$CPUPercent.GetSample(TimeInterval_Minute * 10)` bijvoorbeeld 20 voorbeelden als alle voorbeelden van de afgelopen 10 minuten aanwezig zijn in de `CPUPercent` geschiedenis. Als de laatste minuut van de geschiedenis niet beschikbaar was, zouden er slechts 18 voorbeelden worden geretourneerd. In dit geval `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` mislukt omdat slechts 90 procent van de voorbeelden beschikbaar is, maar `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` wel zou slagen.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Hiermee geeft u een tijdsbestek op voor het verzamelen van gegevens, met zowel een begintijd als een eindtijd. Zoals hierboven vermeld, is er een vertraging tussen het moment waarop een voorbeeld wordt verzameld en het moment waarop het beschikbaar wordt voor een formule. Houd rekening met deze vertraging wanneer u de methode `GetSample` gebruikt. Zie `GetSamplePercent` hieronder. |
| GetSamplePeriod() |Retourneert de periode met voorbeelden die zijn genomen in een historische voorbeeldgegevensset. |
| Count() |Retourneert het totale aantal voorbeelden in de metrische geschiedenis. |
| HistoryBeginTime() |Retourneert het tijdstempel van het oudste beschikbare gegevensvoorbeeld voor de metrische gegevens. |
| GetSamplePercent() |Retourneert het percentage voorbeelden dat beschikbaar is voor een bepaald tijdsinterval. Bijvoorbeeld `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Omdat de methode mislukt als het percentage geretourneerde voorbeelden kleiner is dan de opgegeven, kunt u eerst `GetSample` de methode gebruiken om dit te `samplePercent` `GetSamplePercent` controleren. Vervolgens kunt u een alternatieve actie uitvoeren als er onvoldoende steekproeven aanwezig zijn, zonder de evaluatie van automatisch schalen te stoppen. |

### <a name="samples"></a>Voorbeelden

De Batch-service neemt regelmatig voorbeelden van metrische gegevens voor taken en resources en maakt deze beschikbaar voor uw formules voor automatisch schalen. Deze voorbeelden worden elke 30 seconden vastgelegd door de Batch-service. Er is echter meestal een vertraging tussen het moment waarop deze voorbeelden zijn vastgelegd en wanneer ze beschikbaar worden gesteld voor (en kunnen worden gelezen door) uw formules voor automatisch schalen. Bovendien kunnen voorbeelden niet worden vastgelegd voor een bepaald interval vanwege factoren zoals netwerk- of andere infrastructuurproblemen.

### <a name="sample-percentage"></a>Voorbeeldpercentage

Wanneer wordt doorgegeven aan de methode of de methode wordt aangeroepen, verwijst percentage naar een vergelijking tussen het totale aantal voorbeelden dat door de Batch-service wordt vastgelegd en het aantal voorbeelden dat beschikbaar is voor uw formule voor automatisch `samplePercent` `GetSample()` `GetSamplePercent()` schalen. 

Laten we eens kijken naar een periode van 10 minuten als voorbeeld. Omdat voorbeelden elke 30 seconden binnen die periode van 10 minuten worden vastgelegd, zou het maximum aantal steekproeven dat door Batch wordt vastgelegd, 20 steekproeven (2 per minuut) zijn. Vanwege de inherente latentie van het rapportagemechanisme en andere problemen binnen Azure zijn er echter mogelijk slechts 15 voorbeelden beschikbaar voor uw formule voor automatisch schalen om te lezen. Voor die periode van 10 minuten is bijvoorbeeld slechts 75% van het totale aantal vastgelegde steekproeven beschikbaar voor uw formule.

### <a name="getsample-and-sample-ranges"></a>GetSample() en voorbeeldbereiken

Uw formules voor automatisch schalen worden groter en kleiner door knooppunten toe te voegen of te verwijderen. Omdat knooppunten u geld kosten, moet u ervoor zorgen dat uw formules een intelligente analysemethode gebruiken die is gebaseerd op voldoende gegevens. U wordt aangeraden een trending-type-analyse in uw formules te gebruiken. Dit type groeit en verkleint uw pools op basis van een reeks verzamelde voorbeelden.

Gebruik om dit te doen om `GetSample(interval look-back start, interval look-back end)` een vector van steekproeven te retourneren:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Wanneer de bovenstaande regel wordt geëvalueerd door Batch, retourneert deze een reeks voorbeelden als een vector van waarden. Bijvoorbeeld:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Zodra u de vector van voorbeelden hebt verzameld, kunt u functies zoals , en gebruiken om zinvolle waarden af te leiden `min()` `max()` uit het verzamelde `avg()` bereik.

Voor extra beveiliging kunt u forceer dat een formule-evaluatie mislukt als er minder dan een bepaald voorbeeldpercentage beschikbaar is voor een bepaalde periode. Wanneer u een formule-evaluatie forceren mislukt, instrueert u Batch om de verdere evaluatie van de formule te stoppen als het opgegeven percentage van de voorbeelden niet beschikbaar is. In dit geval wordt er geen wijziging aangebracht in de poolgrootte. Als u een vereist percentage voorbeelden wilt opgeven om de evaluatie te laten slagen, geeft u dit op als de derde parameter voor `GetSample()` . Hier wordt een vereiste van 75 procent van de voorbeelden opgegeven:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Omdat er een vertraging in de beschikbaarheid van het voorbeeld kan zijn, moet u altijd een tijdsbereik opgeven met een begintijd voor terugkijken die ouder is dan één minuut. Het duurt ongeveer één minuut voor voorbeelden zijn doorgegeven via het systeem, zodat voorbeelden in het bereik `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` mogelijk niet beschikbaar zijn. Ook hier kunt u de parameter percentage van gebruiken om `GetSample()` een bepaald vereiste steekproefpercentage af te dwingen.

> [!IMPORTANT]
> We raden u ten zeerste aan **om te voorkomen dat u *alleen* `GetSample(1)` vertrouwt op in uw formules voor automatisch schalen.** Dit komt omdat in wezen tegen de Batch-service zegt: "Geef me het laatste voorbeeld dat u hebt, ongeacht hoe lang `GetSample(1)` geleden u het hebt opgehaald." Aangezien het slechts één voorbeeld is en het een oudere steekproef kan zijn, is het mogelijk niet representatief voor het grotere geheel van de recente taak- of resourcetoestand. Als u wel gebruikt, moet u ervoor zorgen dat deze deel uitmaakt van een grotere instructie en niet het enige gegevenspunt waar uw `GetSample(1)` formule van afhankelijk is.

## <a name="write-an-autoscale-formula"></a>Een formule voor automatisch schalen schrijven

U bouwt een formule voor automatisch schalen door instructies te maken die gebruikmaken van de bovenstaande onderdelen en deze instructies vervolgens combineren tot een volledige formule. In deze sectie maken we een voorbeeldformule voor automatische schaalaanpassing die beslissingen voor schaalaanpassing in de echte wereld kan uitvoeren en aanpassingen kan aanbrengen.

Eerst gaan we de vereisten voor onze nieuwe formule voor automatisch schalen definiëren. De formule moet:

- Verhoog het doelaantal toegewezen rekenknooppunten in een pool als het CPU-gebruik hoog is.
- Verminder het doelaantal toegewezen rekenknooppunten in een pool wanneer het CPU-gebruik laag is.
- Beperk altijd het maximum aantal toegewezen knooppunten tot 400.
- Wanneer u het aantal knooppunten vermindert, verwijdert u geen knooppunten waarop taken worden uitgevoerd; Wacht zo nodig totdat taken zijn voltooid voordat u knooppunten verwijdert.

De eerste instructie in onze formule verhoogt het aantal knooppunten tijdens hoog CPU-gebruik. We definiëren een instructie die een door de gebruiker gedefinieerde variabele ( ) vult met een waarde die 110 procent is van het huidige doelaantal toegewezen knooppunten, maar alleen als het minimale gemiddelde CPU-gebruik in de afgelopen 10 minuten hoger is dan `$totalDedicatedNodes` 70 procent. Anders wordt de waarde gebruikt voor het huidige aantal toegewezen knooppunten.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Om het aantal toegewezen knooppunten te verlagen tijdens een laag CPU-gebruik, stelt de volgende instructie in onze formule dezelfde variabele in op 90 procent van het huidige doelaantal toegewezen knooppunten, als het gemiddelde CPU-gebruik in de afgelopen 60 minuten lager is dan `$totalDedicatedNodes` 20 procent. Anders wordt de huidige waarde van gebruikt `$totalDedicatedNodes` die we in de bovenstaande instructie hebben ingevuld.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nu beperken we het doelaantal toegewezen rekenknooppunten tot maximaal 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Ten slotte zorgen we ervoor dat knooppunten pas worden verwijderd als hun taken zijn voltooid.

```
$NodeDeallocationOption = taskcompletion;
```

Dit is de volledige formule:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Als u wilt, kunt u zowel opmerkingen als regelbreaks opnemen in formulereeksen. Let er ook op dat ontbrekende puntkomma's kunnen leiden tot evaluatiefouten.

## <a name="automatic-scaling-interval"></a>Interval voor automatisch schalen

De Batch-service past standaard elke 15 minuten de grootte van een pool aan op basis van de formule voor automatisch schalen. Dit interval kan worden geconfigureerd met behulp van de volgende pooleigenschappen:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Het minimale interval is vijf minuten en het maximum is 168 uur. Als een interval buiten dit bereik is opgegeven, retourneert de Batch-service de fout Bad Request (400).

> [!NOTE]
> Automatisch schalen is momenteel niet bedoeld om in minder dan een minuut te reageren op wijzigingen, maar is bedoeld om de grootte van uw pool geleidelijk aan te passen wanneer u een workload gaat uitvoeren.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Een pool voor automatisch schalen maken met Batch-SDK's

Automatisch schalen van pool kan worden geconfigureerd met behulp van een van de [Batch SDK's,](batch-apis-tools.md#azure-accounts-for-batch-development)de [Batch REST API](/rest/api/batchservice/) [Batch PowerShell-cmdlets](batch-powershell-cmdlets-get-started.md)en de [Batch CLI.](batch-cli-get-started.md) In deze sectie ziet u voorbeelden voor zowel .NET als Python.

### <a name="net"></a>.NET

Als u een pool wilt maken met automatisch schalen ingeschakeld in .NET, volgt u deze stappen:

1. Maak de pool met [BatchClient.PoolOperations.CreatePool.](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)
1. Stel de [eigenschap CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) in op `true` .
1. Stel de [eigenschap CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) in met uw formule voor automatisch schalen.
1. (Optioneel) Stel de [eigenschap CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) in (de standaardwaarde is 15 minuten).
1. Commit the pool with [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) or [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

In het volgende voorbeeld wordt een pool met automatisch schalen gemaakt in .NET. De formule voor automatisch schalen van de pool stelt het doelaantal toegewezen knooppunten op maandagen in op 5 en op 1 op elke andere dag van de week. Het [interval voor automatisch schalen](#automatic-scaling-interval) is ingesteld op 30 minuten. In dit en de andere C#-codefragmenten in dit artikel is een correct initialiseerde instantie `myBatchClient` van de [BatchClient-klasse.](/dotnet/api/microsoft.azure.batch.batchclient)

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    VirtualMachineConfiguration: new VirtualMachineConfiguration(
                        imageReference: new ImageReference(
                                            publisher: "MicrosoftWindowsServer",
                                            offer: "WindowsServer",
                                            sku: "2019-datacenter-core",
                                            version: "latest"),
                        nodeAgentSkuId: "batch.node.windows amd64");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Wanneer u een pool maakt die automatisch wordt geschaald, geeft u de parameter _targetDedicatedNodes_ of de parameter _targetLowPriorityNodes_ niet op bij de aanroep **van CreatePool**. Geef in plaats daarvan **de eigenschappen AutoScaleEnabled** en **AutoScaleFormula** op in de pool. De waarden voor deze eigenschappen bepalen het doelnummer van elk type knooppunt.
>
> Als u de schaal van een pool met automatische schaalbaarheid handmatig wilt wijzigen (bijvoorbeeld met [BatchClient.PoolOperations.ResizePoolAsync),](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)moet u eerst automatisch schalen voor de pool uitschakelen en vervolgens de schaal wijzigen.

### <a name="python"></a>Python

Voor automatisch schalen ingeschakelde pool maken met de Python-SDK:

1. Maak een pool en geef de configuratie op.
1. Voeg de pool toe aan de serviceclient.
1. Schakel automatisch schalen in voor de pool met een formule die u schrijft.

In het volgende voorbeeld worden deze stappen geïllustreerd.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Meer voorbeelden van het gebruik van de Python SDK vindt u in de [Batch Python Quickstart-opslagplaats](https://github.com/Azure-Samples/batch-python-quickstart) op GitHub.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Automatisch schalen inschakelen voor een bestaande pool

Elke Batch-SDK biedt een manier om automatisch schalen mogelijk te maken. Bijvoorbeeld:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Automatisch schalen inschakelen voor een pool](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Wanneer u automatisch schalen in een bestaande pool inschakelen, moet u rekening houden met het volgende:

- Als automatisch schalen momenteel is uitgeschakeld voor de groep, moet u een geldige formule voor automatisch schalen opgeven wanneer u de aanvraag indient. U kunt eventueel een automatisch schaalinterval opgeven. Als u geen interval opgeeft, wordt de standaardwaarde 15 minuten gebruikt.
- Als automatische schalen momenteel is ingeschakeld voor de pool, kunt u een nieuwe formule, een nieuw interval of beide opgeven. U moet ten minste één van deze eigenschappen opgeven.
  - Als u een nieuw automatisch schaalinterval opgeeft, wordt de bestaande planning gestopt en wordt een nieuwe planning gestart. De begintijd van het nieuwe schema is het tijdstip waarop de aanvraag voor het inschakelen van automatisch schalen is uitgegeven.
  - Als u de formule of het interval voor automatisch schalen weglaten, blijft de Batch-service de huidige waarde van die instelling gebruiken.

> [!NOTE]
> Als u waarden hebt opgegeven voor de parameters *targetDedicatedNodes* of *targetLowPriorityNodes* van de **methode CreatePool** tijdens het maken van de pool in .NET of voor de vergelijkbare parameters in een andere taal, worden deze waarden genegeerd wanneer de formule voor automatisch schalen wordt geëvalueerd.

In dit C#-voorbeeld wordt de [Batch .NET-bibliotheek](/dotnet/api/microsoft.azure.batch) gebruikt om automatisch schalen in te stellen voor een bestaande pool.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Een formule voor automatisch schalen bijwerken

Als u de formule voor een bestaande pool met automatische schaalbewerking wilt bijwerken, roept u de bewerking aan om automatisch schalen opnieuw in te stellen met de nieuwe formule. Als automatisch schalen bijvoorbeeld al is ingeschakeld wanneer de volgende .NET-code wordt uitgevoerd, wordt de formule voor automatisch schalen vervangen door de `myexistingpool` inhoud van `myNewFormula` .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Het interval voor automatisch schalen bijwerken

Als u het evaluatie-interval voor automatisch schalen van een bestaande pool met automatische schaalbewerking wilt bijwerken, roept u de bewerking aan om automatisch schalen opnieuw in te stellen met het nieuwe interval. Als u bijvoorbeeld het evaluatie-interval voor automatisch schalen wilt instellen op 60 minuten voor een pool die al automatisch wordt geschaald in .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Een formule voor automatisch schalen evalueren

U kunt een formule evalueren voordat u deze op een pool gaat toepassen. Hiermee kunt u de resultaten van de formule testen voordat u deze in productie gaat nemen.

Voordat u een formule voor automatisch schalen kunt evalueren, moet u automatisch schalen inschakelen voor de pool met een geldige formule, zoals de formule van één `$TargetDedicatedNodes = 0` regel. Gebruik vervolgens een van de volgende opties om de formule te evalueren die u wilt testen:

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) of [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Voor deze Batch .NET-methoden is de id van een bestaande pool en een tekenreeks met de formule voor automatisch schalen vereist om te evalueren.

- [Een formule voor automatisch schalen evalueren](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Geef in REST API aanvraag de pool-id op in de URI en de formule voor automatisch schalen in het *element autoScaleFormula* van de aanvraag body. Het antwoord van de bewerking bevat foutinformatie die mogelijk betrekking heeft op de formule.

In [dit Batch .NET-voorbeeld](/dotnet/api/microsoft.azure.batch) wordt een formule voor automatisch schalen geëvalueerd. Als de pool nog geen gebruik maakt van automatisch schalen, moet u deze eerst inschakelen.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Een geslaagde evaluatie van de formule die in dit codefragment wordt weergegeven, levert resultaten op die vergelijkbaar zijn met:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Informatie over automatisch schalen

Om ervoor te zorgen dat uw formule werkt zoals verwacht, raden we u aan om regelmatig de resultaten te controleren van de automatisch schalen die Batch voor uw pool uitvoert. U doet dit door een verwijzing naar de pool op te halen (of te vernieuwen) en vervolgens de eigenschappen van de laatste keer automatisch schalen te bekijken.

In Batch .NET heeft de eigenschap [CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) verschillende eigenschappen die informatie bieden over de meest recente automatische schaalrun die op de pool wordt uitgevoerd:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

In de REST API retourneert de aanvraag Voor informatie over een [pool](/rest/api/batchservice/get-information-about-a-pool) informatie over de pool, waaronder de meest recente gegevens over de automatische schalingsuitslag in de eigenschap [autoScaleRun.](/rest/api/batchservice/get-information-about-a-pool)

In het volgende C#-voorbeeld wordt de Batch .NET-bibliotheek gebruikt om informatie af te drukken over de laatste automatische schalen die in de pool _myPool is uitgevoerd._

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Voorbeelduitvoer uit het voorgaande voorbeeld:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Geschiedenis van automatisch schalen op basis van gebeurtenissen voor automatisch schalen van pool
U kunt ook de geschiedenis van automatisch schalen controleren door een query uit te voeren [op PoolAutoScaleEvent.](batch-pool-autoscale-event.md) Deze gebeurtenis wordt door Batch Service uitgevoerd om vast te maken of er formules automatisch worden geëvalueerd en uitgevoerd. Dit kan handig zijn om potentiële problemen op te lossen.

Voorbeeldgebeurtenis voor PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>Voorbeeldformules voor automatisch schalen

Laten we eens kijken naar een aantal formules die verschillende manieren laten zien om de hoeveelheid rekenbronnen in een pool aan te passen.

### <a name="example-1-time-based-adjustment"></a>Voorbeeld 1: Aanpassing op basis van tijd

Stel dat u de poolgrootte wilt aanpassen op basis van de dag van de week en het tijdstip van de dag. In dit voorbeeld ziet u hoe u het aantal knooppunten in de pool dienovereenkomstig verhoogt of verlaagt.

De formule verkrijgt eerst de huidige tijd. Als het een doordeweekse dag (1-5) en binnen werkuren (8:00 tot 18:00 uur) is, wordt de grootte van de doelgroep ingesteld op 20 knooppunten. Anders wordt deze ingesteld op 10 knooppunten.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` kan worden aangepast aan uw lokale tijdzone door toe te `time()` voegen aan het product van en uw `TimeZoneInterval_Hour` UTC-offset. Gebruik bijvoorbeeld voor `$curTime = time() + (-6 * TimeInterval_Hour);` Mountain Daylight Time (MDT). Houd er rekening mee dat de offset moet worden aangepast aan het begin en einde van de zomertijd (indien van toepassing).

### <a name="example-2-task-based-adjustment"></a>Voorbeeld 2: Aanpassing op basis van taken

In dit C#-voorbeeld wordt de poolgrootte aangepast op basis van het aantal taken in de wachtrij. We hebben zowel opmerkingen als regelbreaks opgenomen in de formulereeksen.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Voorbeeld 3: Accounting voor parallelle taken

In dit C#-voorbeeld wordt de poolgrootte aangepast op basis van het aantal taken. Deze formule houdt ook rekening met de [waarde TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) die is ingesteld voor de pool. Deze aanpak is handig in situaties waarin [parallelle taakuitvoering](batch-parallel-node-tasks.md) is ingeschakeld in uw pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Voorbeeld 4: Een initiële poolgrootte instellen

In dit voorbeeld ziet u een C#-voorbeeld met een formule voor automatisch schalen die de poolgrootte in een eerste periode in stelt op een opgegeven aantal knooppunten. Daarna wordt de poolgrootte aangepast op basis van het aantal actieve en actieve taken.

Deze formule doet met name het volgende:

- Hiermee stelt u de grootte van de initiële pool in op vier knooppunten.
- De grootte van de pool wordt niet binnen de eerste tien minuten van de levenscyclus van de pool aangepast.
- Na 10 minuten verkrijgt de maximale waarde van het aantal actieve en actieve taken binnen de afgelopen 60 minuten.
  - Als beide waarden 0 zijn (wat aangeeft dat er in de afgelopen 60 minuten geen taken werden uitgevoerd of actief waren), wordt de poolgrootte ingesteld op 0.
  - Als een van beide waarden groter is dan nul, wordt er geen wijziging aangebracht.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het gelijktijdig uitvoeren van meerdere taken op de rekenknooppunten in uw pool](batch-parallel-node-tasks.md). Naast automatische schalen kan dit helpen om de duur van de taak voor sommige workloads te verlagen, waardoor u geld bespaart.
- Meer informatie over het [efficiënt uitvoeren Azure Batch de service](batch-efficient-list-queries.md) voor verdere efficiëntie.
