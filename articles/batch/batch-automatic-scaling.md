---
title: Automatisch schalen rekenknooppunten in een Azure Batch-pool
description: Schakel automatisch schalen in een Cloud groep in om het aantal reken knooppunten in de pool dynamisch aan te passen.
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 06f717e7c3ab8285b494f89c39838af6b0d96c8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381423"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Een automatische formule voor het schalen van reken knooppunten in een batch-pool maken

Azure Batch kunnen Pools automatisch schalen op basis van de para meters die u definieert, zodat u tijd en geld bespaart. Met automatisch schalen voegt batch knoop punten dynamisch toe aan een groep als taak vereisten toenemen en worden reken knooppunten verwijderd naarmate de taak vraag afneemt.

Als u automatisch schalen wilt inschakelen voor een groep reken knooppunten, koppelt u de groep aan een automatisch *geschaalde formule* die u definieert. De batch-service gebruikt de formule voor automatisch schalen om te bepalen hoeveel knoop punten er nodig zijn om uw werk belasting uit te voeren. Deze knoop punten kunnen specifieke knoop punten of [knoop punten met lage prioriteit](batch-low-pri-vms.md)zijn. In batch worden de metrische gegevens van de service vervolgens periodiek gecontroleerd en gebruikt om het aantal knoop punten in de pool aan te passen op basis van uw formule en met een interval dat u definieert.

U kunt automatisch schalen inschakelen wanneer u een pool maakt of Toep assen op een bestaande groep. Met Batch kunt u uw formules evalueren voordat u deze aan groepen toewijst en de status van automatisch schalen uitvoert. Zodra u een groep hebt geconfigureerd met automatisch schalen, kunt u later wijzigingen in de formule aanbrengen.

> [!IMPORTANT]
> Wanneer u een batch-account maakt, kunt u de [pool toewijzings modus](accounts.md)opgeven. Hiermee wordt bepaald of groepen worden toegewezen in een batch-service abonnement (de standaard instelling) of in uw gebruikers abonnement. Als u uw batch-account hebt gemaakt met de standaard configuratie van de batch-service, is uw account beperkt tot een maximum aantal kernen dat kan worden gebruikt voor de verwerking. De batch-service schaalt alleen reken knooppunten tot die kern limiet. Daarom kan de batch-service niet het doel aantal reken knooppunten bereiken dat is opgegeven door een formule voor automatisch schalen. Zie [quota's en limieten voor de Azure batch-service](batch-quota-limit.md) voor meer informatie over het weer geven en uitbreiden van uw account quota's.
>
>Als u uw account hebt gemaakt met de modus gebruikers abonnement, worden uw account shares in het kern quotum voor het abonnement. Zie [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Limieten voor Virtuele Machines) in [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Azure-abonnement en servicelimieten, -quota en -beperkingen) voor meer informatie.

## <a name="autoscale-formulas"></a>Formules automatisch schalen

Een formule voor automatisch schalen is een teken reeks waarde die u definieert die een of meer instructies bevat. De formule voor automatisch schalen wordt toegewezen aan het [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) -element van een groep (batch rest) of de eigenschap [CloudPool. autoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (batch .net). De batch-service gebruikt de formule om het doel aantal reken knooppunten in de pool te bepalen voor het volgende interval voor de verwerking. De formule teken reeks mag niet langer zijn dan 8 KB, kan bestaan uit Maxi maal 100 instructies, gescheiden door punt komma's, en regel einden en opmerkingen kunnen bevatten.

U kunt formules voor automatisch schalen beschouwen als een batch-automatische schaal aanpassing. Formule-instructies zijn vrije expressies die zowel door de service gedefinieerde variabelen (gedefinieerd door de batch-service) als door de gebruiker gedefinieerde variabelen kunnen bevatten. Formules kunnen verschillende bewerkingen uitvoeren op deze waarden met behulp van ingebouwde typen, Opera tors en functies. Een instructie kan bijvoorbeeld de volgende vorm hebben:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formules bevatten doorgaans meerdere instructies voor het uitvoeren van bewerkingen op waarden die zijn verkregen in eerdere instructies. U krijgt bijvoorbeeld eerst een waarde voor `variable1` en geeft deze door aan een functie voor het vullen van het `variable2` volgende:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Neem deze instructies op in uw formule voor automatisch schalen om te komen op het doel aantal reken knooppunten. Specifieke knoop punten en knoop punten met een lage prioriteit hebben elk hun eigen doel instellingen. Een formule voor automatisch schalen kan een doel waarde bevatten voor toegewezen knoop punten, een doel waarde voor knoop punten met een lage prioriteit of beide.

Het doel aantal knoop punten kan hoger of lager of gelijk zijn aan het huidige aantal knoop punten van het type in de groep. Batch evalueert de formule voor automatisch schalen van een pool met een specifieke [Automatische schaal intervallen](#automatic-scaling-interval). Met batch wordt het doel nummer van elk type knoop punt in de pool aangepast aan het getal dat uw formule voor automatisch schalen op het moment van de evaluatie opgeeft.

### <a name="sample-autoscale-formulas"></a>Voor beeld van formules voor automatisch schalen

Hieronder vindt u voor beelden van twee automatisch schalen formules, die kunnen worden aangepast aan de meeste scenario's. De variabelen `startingNumberOfVMs` en `maxNumberofVMs` in de voorbeeld formules kunnen worden aangepast aan uw behoeften.

#### <a name="pending-tasks"></a>Taken in behandeling

Met deze formule voor automatisch schalen wordt de pool in eerste instantie gemaakt met één virtuele machine. De `$PendingTasks` metrische gegevens bepalen het aantal taken dat wordt uitgevoerd of in de wachtrij wordt geplaatst. Met de formule vindt u het gemiddelde aantal taken in de afgelopen 180 seconden dat in behandeling is en stelt u de `$TargetDedicatedNodes` variabele dienovereenkomstig in. De formule zorgt ervoor dat het doel aantal toegewezen knoop punten nooit meer dan 25 Vm's overschrijdt. Wanneer er nieuwe taken worden verzonden, wordt de groep automatisch verg root. Zodra de taken zijn voltooid, worden de Vm's gratis en wordt de groep met de formule voor automatisch schalen kleiner.

Met deze formule worden toegewezen knoop punten geschaald, maar kunnen ook worden aangepast om knoop punten met een lage prioriteit te schalen.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Knoop punten die zijn afgebroken

In dit voor beeld wordt een pool gemaakt die begint met 25 knoop punten met lage prioriteit. Telkens wanneer een knoop punt met een lage prioriteit is afgebroken, wordt het vervangen door een toegewezen knoop punt. Net als bij het eerste voor beeld wordt `maxNumberofVMs` voor komen dat de groep meer dan 25 vm's overschrijdt. Dit voor beeld is handig voor het gebruik van Vm's met lage prioriteit, terwijl u er ook voor zorgt dat alleen een vast aantal preemptions plaatsvindt voor de levens duur van de groep.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Verderop in dit onderwerp vindt [u meer informatie](#example-autoscale-formulas) over [het maken van formules voor automatisch schalen](#write-an-autoscale-formula) .

## <a name="variables"></a>Variabelen

U kunt zowel door de **service gedefinieerde** als door de **gebruiker gedefinieerde** variabelen gebruiken in uw formules voor automatisch schalen.

De door de service gedefinieerde variabelen zijn ingebouwd in de batch-service. Sommige door de service gedefinieerde variabelen zijn lezen/schrijven, en sommige zijn alleen-lezen.

Door de gebruiker gedefinieerde variabelen zijn variabelen die u definieert. In de voorbeeld formule die hierboven wordt weer gegeven, `$TargetDedicatedNodes` `$PendingTasks` zijn de door de service gedefinieerde variabelen, terwijl `startingNumberOfVMs` en zijn door de `maxNumberofVMs` gebruiker gedefinieerde variabelen.

> [!NOTE]
> Door de service gedefinieerde variabelen worden altijd voorafgegaan door een dollar teken ($). Het dollar teken is optioneel voor door de gebruiker gedefinieerde variabelen.

In de volgende tabellen ziet u de variabelen lezen/schrijven en alleen-lezen die zijn gedefinieerd door de batch-service.

### <a name="read-write-service-defined-variables"></a>Door service gedefinieerde variabelen lezen/schrijven

U kunt de waarden van deze door de service gedefinieerde variabelen ophalen en instellen om het aantal reken knooppunten in een pool te beheren.

| Variabele | Beschrijving |
| --- | --- |
| $TargetDedicatedNodes |Het doel aantal toegewezen reken knooppunten voor de pool. Dit wordt opgegeven als een doel omdat een pool mogelijk niet altijd het gewenste aantal knoop punten bereikt. Als het doel aantal toegewezen knoop punten bijvoorbeeld wordt gewijzigd door een evaluatie van automatisch schalen voordat de groep het eerste doel heeft bereikt, kan de groep het doel niet bereiken. <br /><br /> Een pool in een account dat in de batch-service modus is gemaakt, kan het doel niet bereiken als het doel een knoop punt van een batch-account of een kern quotum overschrijdt. Een pool in een account dat is gemaakt in de modus gebruikers abonnement, kan niet het doel bereiken als het doel het gedeelde kern quotum voor het abonnement overschrijdt.|
| $TargetLowPriorityNodes |Het doel aantal reken knooppunten met lage prioriteit voor de groep. Deze opgegeven als doel, omdat een pool mogelijk niet altijd het gewenste aantal knoop punten bereikt. Als het doel aantal knoop punten met een lage prioriteit bijvoorbeeld wordt gewijzigd door een evaluatie van automatisch schalen voordat de groep het eerste doel heeft bereikt, kan de groep het doel niet bereiken. Het doel van een pool kan ook niet worden bereikt als het doel een knoop punt van een batch-account of een kern quotum overschrijdt. <br /><br /> Zie [virtuele machines met lage prioriteit gebruiken met batch](batch-low-pri-vms.md)voor meer informatie over reken knooppunten met lage prioriteit. |
| $NodeDeallocationOption |De actie die wordt uitgevoerd wanneer reken knooppunten uit een pool worden verwijderd. Mogelijke waarden zijn:<ul><li>opnieuw **in wachtrij plaatsen**: de standaard waarde. Hiermee worden taken onmiddellijk beëindigd en weer in de taak wachtrij geplaatst, zodat ze opnieuw worden gepland. Met deze actie zorgt u ervoor dat het doel aantal knoop punten zo snel mogelijk wordt bereikt. Het kan echter minder efficiënt zijn, omdat actieve taken worden onderbroken en vervolgens volledig opnieuw moeten worden gestart. <li>**beëindigen**: Hiermee worden taken onmiddellijk beëindigd en uit de taak wachtrij verwijderd.<li>**taskcompletion**: wacht tot actieve taken zijn voltooid en verwijder vervolgens het knoop punt uit de groep. Gebruik deze optie om te voor komen dat taken worden onderbroken en opnieuw in de wachtrij worden geplaatst, zodat alle werk dat de taak heeft uitgevoerd, wordt verspild.<li>**retaineddata**: er wordt gewacht tot alle lokale taak gegevens die op het knoop punt zijn bewaard, worden opgeruimd voordat het knoop punt uit de pool wordt verwijderd.</ul> |

> [!NOTE]
> De `$TargetDedicatedNodes` variabele kan ook worden opgegeven met behulp van de alias `$TargetDedicated` . Op dezelfde manier `$TargetLowPriorityNodes` kan de variabele worden opgegeven met behulp van de alias `$TargetLowPriority` . Als zowel de volledig benoemde variabele als de bijbehorende alias wordt ingesteld door de formule, heeft de waarde die is toegewezen aan de volledig benoemde variabele prioriteit.

### <a name="read-only-service-defined-variables"></a>Alleen-lezen service gedefinieerde variabelen

U kunt de waarde van deze door de service gedefinieerde variabelen ophalen om aanpassingen te maken die zijn gebaseerd op metrische gegevens van de batch-service.

> [!IMPORTANT]
> Taak release taken zijn momenteel niet opgenomen in variabelen die het aantal taken bieden, zoals $ActiveTasks en $PendingTasks. Afhankelijk van uw formule voor automatisch schalen, kan dit ertoe leiden dat knoop punten worden verwijderd zonder dat er knoop punten beschikbaar zijn voor het uitvoeren van taak release taken.

| Variabele | Beschrijving |
| --- | --- |
| $CPUPercent |Het gemiddelde percentage van CPU-gebruik. |
| $WallClockSeconds |Het aantal seconden dat is verbruikt. |
| $MemoryBytes |Het gemiddelde aantal mega bytes dat wordt gebruikt. |
| $DiskBytes |Het gemiddelde aantal gigabytes dat is gebruikt op de lokale schijven. |
| $DiskReadBytes |Het aantal gelezen bytes. |
| $DiskWriteBytes |Het aantal geschreven bytes. |
| $DiskReadOps |Het aantal uitgevoerde Lees schijf bewerkingen. |
| $DiskWriteOps |Het aantal bewerkingen voor schrijf schijven dat is uitgevoerd. |
| $NetworkInBytes |Het aantal binnenkomende bytes. |
| $NetworkOutBytes |Het aantal uitgaande bytes. |
| $SampleNodeCount |Het aantal reken knooppunten. |
| $ActiveTasks |Het aantal taken dat kan worden uitgevoerd, maar nog niet is uitgevoerd. Dit omvat alle taken in de actieve status en waarvan is voldaan aan de afhankelijkheden. Taken die de actieve status hebben maar waarvan niet is voldaan aan de afhankelijkheden, worden uitgesloten van het $ActiveTasks aantal. Voor een taak met meerdere instanties bevat $ActiveTasks het aantal instanties dat op de taak is ingesteld.|
| $RunningTasks |Het aantal taken in een uitvoerings status. |
| $PendingTasks |De som van $ActiveTasks en $RunningTasks. |
| $SucceededTasks |Het aantal taken dat is voltooid. |
| $FailedTasks |Het aantal mislukte taken. |
| $TaskSlotsPerNode |Het aantal taak sleuven dat kan worden gebruikt voor het uitvoeren van gelijktijdige taken op één reken knooppunt in de pool. |
| $CurrentDedicatedNodes |Het huidige aantal toegewezen reken knooppunten. |
| $CurrentLowPriorityNodes |Het huidige aantal reken knooppunten met lage prioriteit, inclusief alle knoop punten die zijn voor rang. |
| $PreemptedNodeCount | Het aantal knoop punten in de groep die een afgebroken status hebben. |

> [!TIP]
> Deze alleen-lezen service gedefinieerde variabelen zijn *objecten* die verschillende methoden bieden om toegang te krijgen tot gegevens die zijn gekoppeld aan elk. Zie [voorbeeld gegevens verkrijgen](#obtain-sample-data) verderop in dit artikel voor meer informatie.

> [!NOTE]
> Gebruiken `$RunningTasks` bij schalen op basis van het aantal taken dat op een bepaald moment wordt uitgevoerd en `$ActiveTasks` bij het schalen op basis van het aantal taken dat in de wachtrij is geplaatst om te worden uitgevoerd.

## <a name="types"></a>Typen

Formules voor automatisch schalen ondersteunen de volgende typen:

- double
- doubleVec
- doubleVecList
- tekenreeks
- Time Stamp: een samengestelde structuur die de volgende leden bevat:
  - jaar
  - maand (1-12)
  - dag (1-31)
  - weekdag (in de notatie van nummer; bijvoorbeeld 1 voor maandag)
  - uur (in 24-uurs getalnotatie; bijvoorbeeld 13 betekent 1 uur)
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

Deze bewerkingen zijn toegestaan voor de typen die worden vermeld in de vorige sectie.

| Bewerking | Ondersteunde Opera tors | Resultaat type |
| --- | --- | --- |
| dubbele *operator* dubbele |+, -, *, / |double |
| dubbele *operator* TimeInterval |* |timeinterval |
| dubbele *operator* doubleVec |+, -, *, / |doubleVec |
| doubleVec- *operator* doubleVec |+, -, *, / |doubleVec |
| dubbele *operator* TimeInterval |*, / |timeinterval |
| TimeInterval- *operator* TimeInterval |+, - |timeinterval |
| tijds tempel TimeInterval *operator* |+ |tijdstempel |
| tijds tempel *operator* TimeInterval |+ |tijdstempel |
| tijds tempel *operator* time stamp |- |timeinterval |
| dubbele *operator* |-, ! |double |
| TimeInterval *operator* |- |timeinterval |
| dubbele *operator* dubbele |<, <=, = =, >=, >,! = |double |
| teken reeks *operator* |<, <=, = =, >=, >,! = |double |
| tijds tempel *operator* time stamp |<, <=, = =, >=, >,! = |double |
| TimeInterval- *operator* TimeInterval |<, <=, = =, >=, >,! = |double |
| dubbele *operator* dubbele |&&,  &#124;&#124; |double |

Bij het testen van een dubbele met een ternaire operator ( `double ? statement1 : statement2` ), is niet nul **waar** en is nul **False**.

## <a name="functions"></a>Functions

U kunt deze vooraf gedefinieerde **functies** gebruiken voor het definiëren van een formule voor automatisch schalen.

| Functie | Retourtype | Beschrijving |
| --- | --- | --- |
| Gem (doubleVecList) |double |Retourneert de gemiddelde waarde voor alle waarden in de doubleVecList. |
| len (doubleVecList) |double |Retourneert de lengte van de vector die is gemaakt op basis van de doubleVecList. |
| LG (double) |double |Retourneert de logaritme van het logboek grondtal 2 van de dubbele waarde. |
| LG (doubleVecList) |doubleVec |Hiermee wordt het onderdeel-and-log-basis 2 van de doubleVecList geretourneerd. Een VEC (double) moet expliciet worden door gegeven voor de para meter. Anders wordt ervan uitgegaan dat de dubbele LG (double) versie wordt gebruikt. |
| LN (double) |double |Retourneert het natuurlijke logboek van de dubbele waarde. |
| LN (doubleVecList) |doubleVec |Retourneert het natuurlijke logboek van de dubbele waarde. |
| logboek (dubbel) |double |Retourneert de logaritme met grondtal 10 van de dubbele waarde. |
| logboek (doubleVecList) |doubleVec |Hiermee wordt het onderdeel weerstands logboek van de doubleVecList geretourneerd. Een VEC (double) moet expliciet worden door gegeven voor de enkele dubbele para meter. Anders wordt de dubbele logboek versie (double) gebruikt. |
| Max (doubleVecList) |double |Retourneert de maximum waarde in de doubleVecList. |
| min (doubleVecList) |double |Retourneert de minimum waarde in de doubleVecList. |
| norm (doubleVecList) |double |Retourneert de twee-norm van de vector die is gemaakt op basis van de doubleVecList. |
| percentiel (doubleVec v, dubbele p) |double |Retourneert het percentiel element van de vector v. |
| rand() |double |Retourneert een wille keurige waarde tussen 0,0 en 1,0. |
| bereik (doubleVecList) |double |Retourneert het verschil tussen de minimum-en maximum waarden in de doubleVecList. |
| STD (doubleVecList) |double |Retourneert de standaard deviatie van de steek proef van de waarden in de doubleVecList. |
| stoppen () | |Stopt de evaluatie van de expressie voor automatisch schalen. |
| Sum (doubleVecList) |double |Retourneert de som van alle onderdelen van de doubleVecList. |
| time (teken reeks dateTime = "") |tijdstempel |Retourneert het tijds tempel van de huidige tijd als er geen para meters worden door gegeven, of het tijds tempel van de dateTime-teken reeks als die is door gegeven. Ondersteunde dateTime-indelingen zijn W3C-DTF en RFC 1123. |
| Val (doubleVec v, Double i) |double |Retourneert de waarde van het element dat zich op locatie i in vector v bevindt, met een begin index van nul. |

Sommige functies die in de vorige tabel worden beschreven, kunnen een lijst als argument accepteren. De lijst met door komma's gescheiden waarden is een combi natie van *dubbele* en *doubleVec*. Bijvoorbeeld:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

De *doubleVecList* -waarde wordt voor de evaluatie geconverteerd naar één *doubleVec* . Bijvoorbeeld, als `v = [1,2,3]` , wordt aangeroepen, `avg(v)` is gelijk aan aanroepen `avg(1,2,3)` . Aanroepen `avg(v, 7)` is gelijk aan aanroepen `avg(1,2,3,7)` .

## <a name="metrics"></a>Metrische gegevens

U kunt zowel de metrische gegevens van de resource als de taak gebruiken wanneer u een formule definieert. U past het doel aantal toegewezen knoop punten in de pool aan op basis van de metrische gegevens die u ophaalt en evalueert. Zie de sectie met [variabelen](#variables) voor meer informatie over elke metriek.

<table>
  <tr>
    <th>Metrisch</th>
    <th>Beschrijving</th>
  </tr>
  <tr>
    <td><b>Resource</b></td>
    <td><p>Metrische gegevens van resources zijn gebaseerd op de CPU, de band breedte, het geheugen gebruik van reken knooppunten en het aantal knoop punten.</p>
        <p> Deze door de service gedefinieerde variabelen zijn handig voor het maken van aanpassingen op basis van het aantal knoop punten:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Deze door de service gedefinieerde variabelen zijn handig voor het maken van aanpassingen op basis van het resource gebruik van het knoop punt:</p>
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
    <td><p>De metrische gegevens van de taak zijn gebaseerd op de status van taken, zoals actief, in behandeling en voltooid. De volgende door de service gedefinieerde variabelen zijn handig voor het aanpassen van de grootte van een groep op basis van de metrische gegevens van de taak:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Voorbeeld gegevens verkrijgen

De kern bewerking van een formule voor automatisch schalen is het verkrijgen van metrische gegevens voor taken en resources (voor beelden) en het aanpassen van de grootte van de pool op basis van die gegevens. Daarom is het belang rijk om een duidelijk beeld te hebben van de manier waarop automatisch schalen van formules met steek proeven kan communiceren.

### <a name="methods"></a>Methoden

Formules automatisch schalen fungeren voor voor beelden van metrische gegevens die door de batch-service worden verschaft. Een formule verg root of verkleint de pool grootte op basis van de waarden die worden opgehaald. Service gedefinieerde variabelen zijn objecten die methoden bieden om toegang te krijgen tot gegevens die zijn gekoppeld aan dat object. De volgende expressie toont bijvoorbeeld een aanvraag om de laatste vijf minuten van het CPU-gebruik op te halen:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

De volgende methoden kunnen worden gebruikt om voorbeeld gegevens over door de service gedefinieerde variabelen te verkrijgen.

| Methode | Beschrijving |
| --- | --- |
| GetSample() |De `GetSample()` methode retourneert een vector van gegevens voorbeelden.<br/><br/>Een voor beeld is 30 seconden voor metrische gegevens. Met andere woorden: alle voor beelden worden elke 30 seconden opgehaald. Maar zoals hieronder vermeld, is er een vertraging tussen het moment waarop een voor beeld wordt verzameld en wanneer het beschikbaar is voor een formule. Daarom kunnen niet alle voor beelden voor een bepaalde periode beschikbaar zijn voor evaluatie met een formule.<ul><li>`doubleVec GetSample(double count)`: Hiermee geeft u het aantal steek proeven op dat moet worden opgehaald uit de meest recente voor beelden die zijn verzameld. `GetSample(1)` retourneert het laatst beschik bare voor beeld. Voor metrische gegevens, zoals, `$CPUPercent` `GetSample(1)` mag echter niet worden gebruikt, omdat het niet mogelijk is om te weten *Wanneer* het voor beeld is verzameld. Het kan recent zijn of, vanwege systeem problemen, mogelijk veel oudere items zijn. In dergelijke gevallen is het beter om een tijds interval te gebruiken, zoals hieronder wordt weer gegeven.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Hiermee geeft u een tijds bestek op voor het verzamelen van voorbeeld gegevens. U kunt ook het percentage steek proeven opgeven dat beschikbaar moet zijn in het aangevraagde tijds bestek. `$CPUPercent.GetSample(TimeInterval_Minute * 10)`Retourneert bijvoorbeeld 20 voor beelden als alle voor beelden voor de laatste tien minuten aanwezig zijn in de `CPUPercent` geschiedenis. Als de laatste minuut van de geschiedenis niet beschikbaar is, worden er slechts 18 steek proeven geretourneerd. In dit geval `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` zou er een fout kunnen optreden omdat slechts 90 procent van de voor beelden beschikbaar is, maar wel `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` lukken.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Hiermee geeft u een tijds bestek op voor het verzamelen van gegevens, met een begin tijd en een eind tijd. Zoals hierboven vermeld, is er een vertraging tussen het moment waarop een voor beeld wordt verzameld en wanneer het voor een formule beschikbaar wordt. Houd rekening met deze vertraging wanneer u de- `GetSample` methode gebruikt. Zie `GetSamplePercent` hieronder. |
| GetSamplePeriod() |Retourneert de periode van de voor beelden die zijn gemaakt in een historische voorbeeld gegevensset. |
| Aantal () |Retourneert het totale aantal voor beelden in de metrische geschiedenis. |
| HistoryBeginTime() |Retourneert het tijds tempel van het oudste beschik bare gegevens voorbeeld voor de metriek. |
| GetSamplePercent() |Retourneert het percentage steek proeven dat beschikbaar is voor een opgegeven tijds interval. Bijvoorbeeld `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Omdat de `GetSample` methode mislukt als het percentage geretourneerde steek proeven kleiner is dan het `samplePercent` opgegeven aantal, kunt u de `GetSamplePercent` methode gebruiken om eerst te controleren. U kunt vervolgens een alternatieve actie uitvoeren als er onvoldoende steek proeven aanwezig zijn, zonder dat de evaluatie van automatisch schalen wordt onderbroken. |

### <a name="samples"></a>Voorbeelden

De batch-service neemt periodiek voor beelden van metrische gegevens voor taken en resources en maakt deze beschikbaar voor uw formules voor automatisch schalen. Deze voor beelden worden elke 30 seconden door de batch-service vastgelegd. Er is echter meestal een vertraging tussen het moment waarop deze voor beelden werden vastgelegd en wanneer ze beschikbaar worden gesteld aan (en kunnen worden gelezen door) uw formules voor automatisch schalen. Bovendien mogen voor een bepaald interval geen steek proeven worden vastgelegd vanwege factoren zoals netwerk-of andere infrastructuur problemen.

### <a name="sample-percentage"></a>Voorbeeld percentage

Wanneer `samplePercent` wordt door gegeven aan de `GetSample()` methode of de `GetSamplePercent()` methode wordt aangeroepen, verwijst _percent_ naar een vergelijking tussen het totale aantal voor beelden dat is vastgelegd door de batch-service en het aantal voor beelden dat beschikbaar is voor uw formule voor automatisch schalen.

Laten we eens kijken naar een time span van tien minuten als voor beeld. Omdat voor beelden elke 30 seconden worden geregistreerd binnen de time span van 10 minuten, wordt het maximum aantal voor beelden dat door batch is geregistreerd, 20 voor beelden (2 per minuut). Vanwege de inherente latentie van het rapportage mechanisme en andere problemen in azure, kunnen er echter maar 15 voor beelden zijn die beschikbaar zijn voor uw formule voor automatisch schalen om te lezen. Voor die periode van tien minuten is er bijvoorbeeld slechts 75% van het totale aantal geregistreerde steek proeven beschikbaar voor uw formule.

### <a name="getsample-and-sample-ranges"></a>GetSample () en voorbeeld bereik

Met uw formules voor automatisch schalen kunt u uw Pools verg Roten en verkleinen door knoop punten toe te voegen of te verwijderen. Omdat de knoop punten uw geld kosten, moet u ervoor zorgen dat uw formules een intelligente analyse methode gebruiken die is gebaseerd op voldoende gegevens. We raden u aan om een trending-type analyse in uw formules te gebruiken. Dit type verg root of verkleint uw Pools op basis van een reeks verzamelde voor beelden.

Als u dit wilt doen, gebruikt `GetSample(interval look-back start, interval look-back end)` u om een vector van voor beelden te retour neren:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Wanneer de bovenstaande regel door batch wordt geëvalueerd, wordt een aantal steek proeven geretourneerd als een vector met waarden. Bijvoorbeeld:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Wanneer u de vector van voor beelden hebt verzameld, kunt u vervolgens functies als `min()` , gebruiken `max()` en `avg()` zinvolle waarden afleiden van het verzamelde bereik.

Voor extra beveiliging kunt u ervoor zorgen dat een formule-evaluatie mislukt als er minder dan een bepaald steekproef percentage beschikbaar is voor een bepaalde periode. Wanneer u een formule-evaluatie afdwingt, geeft u Batch de opdracht om de formule verder te evalueren als het opgegeven percentage voor beelden niet beschikbaar is. In dit geval wordt er geen wijziging aangebracht in de pool grootte. Als u een vereiste percentage voor steek proeven wilt opgeven voordat de evaluatie slaagt, geeft u dit op als de derde para meter voor `GetSample()` . Hier is een vereiste van 75 procent van de voor beelden opgegeven:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Omdat er een vertraging in de beschik baarheid van de steek proef kan optreden, moet u altijd een tijds bereik opgeven met een begin tijd die ouder is dan één minuut. Het duurt ongeveer een minuut voordat er voor beelden worden door gegeven door het systeem, waardoor de steek proeven in het bereik `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` mogelijk niet beschikbaar zijn. U kunt opnieuw de percentage para meter van gebruiken `GetSample()` om een bepaald steekproef percentage af te dwingen.

> [!IMPORTANT]
> We raden u ten zeerste **aan om te voor komen dat u *alleen* op `GetSample(1)` uw formules voor automatisch schalen moet vertrouwen**. Dit komt doordat `GetSample(1)` de batch-service, "geef ik de laatste steek proef die u hebt ontvangen, niet meer op hoe lang geleden u deze hebt opgehaald." Omdat het slechts één voor beeld is en een ouder voor beeld is, is het mogelijk niet representatief voor de grotere afbeelding van de status van de recente taak of de resource. Als u dat wel doet `GetSample(1)` , moet u ervoor zorgen dat het deel uitmaakt van een grotere instructie en niet het enige gegevens punt waarop uw formule van toepassing is.

## <a name="write-an-autoscale-formula"></a>Een formule voor automatisch schalen schrijven

U maakt een formule voor automatisch schalen met behulp van instructies die gebruikmaken van de bovenstaande onderdelen en combi neren deze instructies vervolgens in een volledige formule. In deze sectie maken we een voor beeld van een formule voor automatisch schalen waarmee u beslissingen kunt nemen over het schalen en aanpassen van de schaal.

Eerst gaan we de vereisten voor de nieuwe formule voor automatisch schalen definiëren. De formule moet:

- Verhoog het doel aantal toegewezen reken knooppunten in een pool als het CPU-gebruik hoog is.
- Verminder het doel aantal toegewezen reken knooppunten in een pool wanneer het CPU-gebruik laag is.
- Beperk altijd het maximum aantal toegewezen knoop punten tot 400.
- Als u het aantal knoop punten vermindert, verwijdert u geen knoop punten waarop taken worden uitgevoerd. Wacht zo nodig totdat de taken zijn voltooid voordat u knoop punten verwijdert.

Met de eerste instructie in de formule wordt het aantal knoop punten tijdens hoog CPU-gebruik verhoogd. Er wordt een instructie gedefinieerd waarmee een door de gebruiker gedefinieerde variabele ( `$totalDedicatedNodes` ) wordt gevuld met een waarde van 110 procent van het huidige doel aantal toegewezen knoop punten, maar alleen als het minimale gemiddelde CPU-gebruik in de laatste 10 minuten hoger is dan 70%. Anders wordt de waarde gebruikt voor het huidige aantal toegewezen knoop punten.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Als u het aantal toegewezen knoop punten tijdens een laag CPU-gebruik wilt verlagen, stelt de volgende instructie in de formule dezelfde `$totalDedicatedNodes` variabele in op 90 procent van het huidige doel aantal toegewezen knoop punten, als gemiddeld CPU-gebruik in de afgelopen 60 minuten minder was dan 20 procent. Anders wordt de huidige waarde van `$totalDedicatedNodes` die in de bovenstaande instructie is ingevuld gebruikt.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nu beperken we het doel aantal toegewezen reken knooppunten tot een maximum van 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Ten slotte zorgen we ervoor dat er geen knoop punten worden verwijderd totdat de taken zijn voltooid.

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
> Indien gewenst kunt u zowel opmerkingen als regel einden in formule teken reeksen toevoegen. Houd er ook rekening mee dat het ontbreken van een punt komma kan leiden tot evaluatie fouten.

## <a name="automatic-scaling-interval"></a>Interval voor automatisch schalen

De batch-service past standaard elke 15 minuten de grootte van een pool aan op basis van de automatisch geschaalde formule. Dit interval kan worden geconfigureerd met behulp van de volgende groeps eigenschappen:

- [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (batch .net)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (rest API)

Het minimale interval is vijf minuten en de maximum waarde is 168 uur. Als er een interval buiten dit bereik is opgegeven, retourneert de batch-service een ongeldige aanvraag (400).

> [!NOTE]
> Automatisch schalen is momenteel niet bedoeld om te reageren op wijzigingen in minder dan een minuut, maar is bedoeld om de grootte van de pool geleidelijk aan te passen wanneer u een werk belasting uitvoert.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Een groep met automatisch schalen maken met batch-Sdk's

Het automatisch schalen van groepen kan worden geconfigureerd met een van [de batch-sdk's](batch-apis-tools.md#azure-accounts-for-batch-development), de batch- [rest API](/rest/api/batchservice/) [batch Power shell-cmdlets](batch-powershell-cmdlets-get-started.md)en de batch- [cli](batch-cli-get-started.md). In deze sectie vindt u voor beelden voor .NET en python.

### <a name="net"></a>.NET

Voer de volgende stappen uit om een pool te maken met automatisch schalen in .NET.

1. Maak de pool met [BatchClient. pool Operations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Stel de eigenschap [CloudPool. AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) in op `true` .
1. Stel de eigenschap [CloudPool. AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) in op uw formule voor automatisch schalen.
1. Beschrijving Stel de eigenschap [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) in (de standaard waarde is 15 minuten).
1. Voer de pool door met [CloudPool. commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) of [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

In het volgende voor beeld wordt een groep met automatisch schalen gemaakt in .NET. Met de formule automatisch schalen van de pool wordt het doel aantal toegewezen knoop punten ingesteld op 5 op maandag en op 1 op elke andere dag van de week. Het [interval voor automatisch schalen](#automatic-scaling-interval) is ingesteld op 30 minuten. In deze en de andere C#-fragmenten in dit artikel `myBatchClient` is een correct geïnitialiseerd exemplaar van de [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) -klasse.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Wanneer u een groep met automatisch schalen maakt, geeft u niet de para meter _targetDedicatedNodes_ op, of de para meter _targetLowPriorityNodes_ op de aanroep van **CreatePool**. Geef in plaats daarvan de eigenschappen **AutoScaleEnabled** en **AutoScaleFormula** op voor de groep. De waarden voor deze eigenschappen bepalen het doel nummer van elk type knoop punt.
>
> Als u hand matig het formaat van een groep met automatische schaal aanpassing wilt wijzigen (bijvoorbeeld met [BatchClient. pool Operations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)), moet u eerst automatisch schalen op de groep uitschakelen en vervolgens het formaat ervan wijzigen.

### <a name="python"></a>Python

Voor het maken van een groep met automatisch schalen met de python-SDK:

1. Een pool maken en de configuratie opgeven.
1. Voeg de groep toe aan de service-client.
1. Schakel automatisch schalen in voor de pool met een formule die u schrijft.

In het volgende voor beeld ziet u deze stappen.

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
> Meer voor beelden van het gebruik van de python-SDK vindt u in de Quick Start- [opslag plaats van batch python](https://github.com/Azure-Samples/batch-python-quickstart) op github.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Automatisch schalen inschakelen voor een bestaande groep

Elke batch-SDK biedt een manier om automatisch schalen in te scha kelen. Bijvoorbeeld:

- [BatchClient. pool Operations. EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (batch .net)
- [Automatisch schalen inschakelen voor een groep](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (rest API)

Wanneer u automatisch schalen op een bestaande groep inschakelt, moet u in het geding blijven:

- Als automatisch schalen op dit moment is uitgeschakeld voor de groep, moet u een geldige formule voor automatisch schalen opgeven wanneer u de aanvraag verzendt. U kunt desgewenst een interval voor automatisch schalen opgeven. Als u geen interval opgeeft, wordt de standaard waarde van 15 minuten gebruikt.
- Als automatisch schalen momenteel is ingeschakeld voor de groep, kunt u een nieuwe formule, een nieuw interval of beide opgeven. U moet ten minste één van deze eigenschappen opgeven.
  - Als u een nieuw interval voor automatisch schalen opgeeft, wordt het bestaande schema gestopt en wordt er een nieuw schema gestart. De begin tijd van de nieuwe planning is het tijdstip waarop de aanvraag om automatisch schalen in te scha kelen is uitgegeven.
  - Als u de formule of het interval voor automatisch schalen weglaat, blijft de batch-service de huidige waarde van deze instelling gebruiken.

> [!NOTE]
> Als u waarden voor de para meters *targetDedicatedNodes* of *targetLowPriorityNodes* van de methode **CreatePool** hebt opgegeven tijdens het maken van de groep in .net, of voor de vergelijk bare para meters in een andere taal, worden deze waarden genegeerd wanneer de formule voor automatisch schalen wordt geëvalueerd.

In dit C#-voor beeld wordt de [batch .net](/dotnet/api/microsoft.azure.batch) -bibliotheek gebruikt om automatisch schalen in te scha kelen voor een bestaande groep.

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

Als u de formule wilt bijwerken op basis van een bestaande groep die automatisch kan worden geschaald, roept u de bewerking aan om automatisch schalen weer in te scha kelen met de nieuwe formule. Als automatisch schalen bijvoorbeeld al is ingeschakeld op `myexistingpool` wanneer de volgende .net-code wordt uitgevoerd, wordt de formule voor automatisch schalen vervangen door de inhoud van `myNewFormula` .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Het interval voor automatisch schalen bijwerken

Als u het interval voor automatisch schalen wilt bijwerken van een bestaande pool met automatisch schalen, roept u de bewerking aan zodat automatisch schalen opnieuw met het nieuwe interval kan worden ingeschakeld. Als u het evaluatie-interval voor automatisch schalen bijvoorbeeld wilt instellen op 60 minuten voor een pool die al automatisch wordt geschaald in .NET, doet u het volgende:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Een formule voor automatisch schalen evalueren

U kunt een Formule evalueren voordat u deze toepast op een groep. Hiermee kunt u de resultaten van de formule testen voordat u deze in productie neemt.

Voordat u een formule voor automatisch schalen kunt evalueren, moet u eerst automatische schaling op de groep inschakelen met een geldige formule, zoals de formule met één regel `$TargetDedicatedNodes = 0` . Gebruik vervolgens een van de volgende opties om de formule te evalueren die u wilt testen:

- [BatchClient. pool Operations. EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) of [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Deze batch .NET-methoden vereisen de ID van een bestaande groep en een teken reeks met de formule voor automatisch schalen die moet worden geëvalueerd.

- [Een formule voor automatisch schalen evalueren](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Geef in deze REST API aanvraag de groeps-ID op in de URI en de formule voor automatisch schalen in het element *autoScaleFormula* van de hoofd tekst van de aanvraag. Het antwoord van de bewerking bevat de fout informatie die kan worden gerelateerd aan de formule.

In dit [batch .net](/dotnet/api/microsoft.azure.batch) -voor beeld wordt een formule voor automatisch schalen geëvalueerd. Als de pool nog niet automatisch schalen gebruikt, wordt het eerst ingeschakeld.

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

Een geslaagde evaluatie van de formule die in dit code fragment wordt weer gegeven, produceert de resultaten die vergelijkbaar zijn met:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Informatie over automatische schaal uitvoeringen ophalen

Om ervoor te zorgen dat uw formule op de verwachte manier wordt uitgevoerd, raden we u aan om regel matig de resultaten van de automatisch schalen uit te voeren die door batch worden uitgevoerd op uw pool. Als u dit wilt doen, kunt u een verwijzing naar de groep ophalen (of vernieuwen) en vervolgens de eigenschappen van de laatste automatische schaal bewerking onderzoeken.

In batch .NET heeft de eigenschap [CloudPool. AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) diverse eigenschappen die informatie geven over de meest recente automatische schaal aanpassing die voor de groep wordt uitgevoerd:

- [AutoScaleRun. time stamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun. resultaten](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun. fout](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

In de REST API wordt met de aanvraag [informatie over een groep ophalen](/rest/api/batchservice/get-information-about-a-pool) informatie over de groep geretourneerd, waaronder de meest recente informatie over automatisch schalen in de eigenschap [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool) .

In het volgende C#-voor beeld wordt de batch .NET-bibliotheek gebruikt om informatie af te drukken over de laatste automatische schaal aanpassing van de groep _myPool_.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Voorbeeld uitvoer van het vorige voor beeld:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Geschiedenis van automatische schaal uitvoering ophalen met de groeps gebeurtenissen voor automatisch schalen
U kunt ook de automatische schaal geschiedenis controleren door een query uit te [PoolAutoScaleEvent](batch-pool-autoscale-event.md). Deze gebeurtenis wordt verzonden door de batch-service om elk exemplaar van het automatisch schalen en uitvoeren van formules vast te leggen. Dit kan handig zijn om mogelijke problemen op te lossen.

Voorbeeld gebeurtenis voor PoolAutoScaleEvent:
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

## <a name="example-autoscale-formulas"></a>Voor beeld van formules voor automatisch schalen

Laten we eens kijken naar een paar formules waarin verschillende manieren worden weer gegeven om de hoeveelheid reken resources in een pool aan te passen.

### <a name="example-1-time-based-adjustment"></a>Voor beeld 1: aanpassing op basis van tijd

Stel dat u de pool grootte wilt aanpassen op basis van de dag van de week en het tijdstip van de dag. In dit voor beeld ziet u hoe u het aantal knoop punten in de pool dienovereenkomstig verhoogt of verlaagt.

De formule verkrijgt eerst de huidige tijd. Als het een weekdag is (1-5) en binnen werk uren (8 uur tot 6 uur), wordt de grootte van de doel groep ingesteld op 20 knoop punten. Anders wordt deze ingesteld op 10 knoop punten.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` kan worden aangepast aan uw lokale tijd zone door toe te voegen `time()` aan het product van `TimeZoneInterval_Hour` en de UTC-offset. Gebruik bijvoorbeeld `$curTime = time() + (-6 * TimeInterval_Hour);` voor Mountain (zomer tijd) (MDT). Houd er rekening mee dat de offset moet worden aangepast aan het begin en het einde van de zomer tijd (indien van toepassing).

### <a name="example-2-task-based-adjustment"></a>Voor beeld 2: aanpassing op basis van een taak

In dit C#-voor beeld wordt de pool grootte aangepast op basis van het aantal taken in de wachtrij. We hebben zowel opmerkingen als regel einden in de formule teken reeksen opgenomen.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>Voor beeld 3: Accounting voor parallelle taken

In dit C#-voor beeld wordt de pool grootte aangepast op basis van het aantal taken. Deze formule houdt ook rekening met de [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) -waarde die is ingesteld voor de groep. Deze aanpak is nuttig in situaties waarin het [uitvoeren van parallelle taken](batch-parallel-node-tasks.md) is ingeschakeld voor uw pool.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Voor beeld 4: een initiële pool grootte instellen

Dit voor beeld toont een C#-voor beeld met een formule voor automatisch schalen waarmee de pool grootte wordt ingesteld op een opgegeven aantal knoop punten voor een eerste periode. Daarna wordt de pool grootte aangepast op basis van het aantal actieve en actieve taken.

Deze formule doet met name het volgende:

- Hiermee stelt u de aanvankelijke pool grootte in op vier knoop punten.
- Past de pool grootte niet aan binnen de eerste tien minuten van de levens cyclus van de pool.
- Na 10 minuten wordt de maximum waarde opgehaald van het aantal actieve en actieve taken in de afgelopen 60 minuten.
  - Als beide waarden 0 zijn (geeft aan dat er in de afgelopen 60 minuten geen taken werden uitgevoerd of actief zijn), wordt de groeps grootte ingesteld op 0.
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

- Meer informatie over hoe u [meerdere taken tegelijk kunt uitvoeren op de reken knooppunten in uw pool](batch-parallel-node-tasks.md). In combi natie met automatisch schalen kan dit helpen de duur van taken te verlagen voor sommige workloads, waardoor u geld bespaart.
- Meer informatie over [het efficiënt uitvoeren van een query op de Azure batch-service](batch-efficient-list-queries.md) voor verdere efficiëntie.
