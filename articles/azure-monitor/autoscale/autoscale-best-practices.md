---
title: Aanbevolen procedures voor automatisch schalen
description: Patronen automatisch schalen in azure voor Web Apps, schaal sets voor virtuele machines en Cloud Services
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 9aaf9525f2fedee67a86011e938b8e995ccfe9fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100611001"
---
# <a name="best-practices-for-autoscale"></a>Aanbevolen procedures voor Automatisch schalen
Azure Monitor automatisch schalen is alleen van toepassing op [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/)en [API Management Services](../../api-management/api-management-key-concepts.md).

## <a name="autoscale-concepts"></a>Concepten automatisch schalen

* Een resource kan slechts *één* instelling voor automatisch schalen hebben
* Een instelling voor automatisch schalen kan een of meer profielen hebben en elk profiel kan een of meer regels voor automatisch schalen hebben.
* Met een instelling voor automatisch schalen worden instanties horizon taal geschaald. *Dit komt doordat* de instanties en *in* worden verhoogd door het aantal exemplaren te verminderen.
  Een instelling voor automatisch schalen heeft een maximum-, minimum-en standaard waarde van instanties.
* Met een taak voor automatisch schalen wordt altijd de bijbehorende metriek gelezen om te schalen, en wordt gecontroleerd of de geconfigureerde drempel waarde voor uitschalen of schalen is overschreden. U kunt een lijst met metrische gegevens weer geven waarmee automatisch schalen kan worden geschaald op basis van [Azure monitor automatische schaling van algemene gegevens](autoscale-common-metrics.md).
* Alle drempel waarden worden berekend op instantie niveau. Bijvoorbeeld: ' uitschalen op basis van één instantie wanneer gemiddeld CPU-> 80% wanneer aantal exemplaren 2 ' is, betekent uitschalen wanneer de gemiddelde CPU voor alle exemplaren groter is dan 80%.
* Alle fouten voor automatisch schalen worden vastgelegd in het activiteiten logboek. U kunt vervolgens een waarschuwing voor een [activiteiten logboek](../alerts/activity-log-alerts.md) configureren, zodat u via E-mail, SMS of webhooks kunt worden gewaarschuwd wanneer er een fout automatisch kan worden geschaald.
* Op dezelfde manier worden alle geslaagde schaal acties in het activiteiten logboek geplaatst. U kunt vervolgens een waarschuwing voor een activiteiten logboek configureren, zodat u via e-mail, SMS of webhooks kunt worden gewaarschuwd wanneer er een actie voor automatisch schalen is uitgevoerd. U kunt ook e-mail berichten of webhook-meldingen configureren om op de hoogte te worden gesteld van geslaagde schaal acties via het tabblad meldingen in de instelling voor automatisch schalen.

## <a name="autoscale-best-practices"></a>Aanbevolen procedures voor automatisch schalen

Gebruik de volgende aanbevolen procedures voor automatisch schalen.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Zorg ervoor dat de maximale en minimale waarden verschillen, en dat er voldoende marge tussen deze twee zit

Als u een instelling hebt met minimum = 2, maximum = 2 en het huidige aantal instanties is 2, dan kan er geen schaalactie optreden. Hou voldoende marge tussen het maximum- en minimumaantal instanties, inclusief de waarden. De automatische schaalaanpassing schaalt altijd tussen deze limieten.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Handmatig schalen wordt opnieuw ingesteld door het minimum en maximum voor automatisch schalen

Als u het aantal exemplaren hand matig bijwerkt naar een waarde boven of beneden het maximum, wordt de auto Scale-Engine automatisch ingesteld op het minimum (indien hieronder) of het maximum (indien hierboven). U kunt bijvoorbeeld het bereik instellen tussen 3 en 6. Als u één exemplaar hebt dat wordt uitgevoerd, wordt de engine voor automatisch schalen geschaald naar drie exemplaren tijdens de volgende uitvoering. Als u de schaal hand matig instelt op acht exemplaren, wordt deze in de volgende uitvoering automatisch schalen naar zes instanties geschaald.  Hand matig schalen is tijdelijk, tenzij u de regels voor automatisch schalen ook opnieuw instelt.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Gebruik altijd een combi natie van regel voor uitschalen en schalen die een verhoging en verlaging uitvoert
Als u slechts één deel van de combi natie gebruikt, onderneemt automatisch schalen alleen actie in één richting (uitschalen of in) totdat het maximum of minimum aantal exemplaren van gedefinieerd in het profiel is bereikt. Dit is niet optimaal, maar in het ideale geval kunt u uw resource op momenten van hoog gebruik opschalen om de beschik baarheid te garanderen. Op dezelfde manier kunt u op elk gewenst moment uw resource omlaag schalen, zodat u rekening moet houden met kosten besparingen.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Kies de juiste statistieken voor uw diagnostische metrische gegevens
Voor metrische gegevens over diagnose kunt u het *gemiddelde*, het *minimum*, het *maximum* en het *totaal* kiezen als metrische waarde om op te schalen. De meest voorkomende statistiek is *gemiddelde*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Kies zorgvuldig de drempelwaarden voor alle typen metrische gegevens
We raden u aan om verschillende drempel waarden te kiezen voor uitschalen en schalen op basis van praktische situaties.

We *raden* instellingen voor automatisch schalen, zoals de onderstaande voor beelden, niet aan met dezelfde of vergelijk bare drempel waarden voor en in voor waarden:

* Instanties verhogen met 1 wanneer het aantal threads >= 600
* Exemplaren verlagen met 1 wanneer het aantal threads <= 600

Laten we eens kijken naar een voor beeld van wat kan leiden tot een gedrag dat verwarrend is. Houd rekening met de volgende reeks.

1. Stel dat er twee exemplaren met moeten beginnen en dat het gemiddelde aantal threads per instantie toeneemt op 625.
2. Met automatisch schalen wordt het toevoegen van een derde exemplaar uitgebreid.
3. Vervolgens wordt ervan uitgegaan dat het gemiddelde aantal threads over de verschillende instanties valt op 575.
4. Voordat u omlaag schaalt, probeert automatisch te schatten wat de uiteindelijke status is als deze is geschaald. Bijvoorbeeld: 575 x 3 (aantal huidige instanties) = 1.725/2 (laatste aantal instanties bij omlaag geschaald) = 862,5 threads. Dit betekent dat automatisch schalen onmiddellijk opnieuw moet worden uitgeschaald, zelfs nadat het is geschaald in, als het gemiddelde aantal threads hetzelfde is of zelfs een kleine hoeveelheid heeft. Als de app echter opnieuw wordt geschaald, wordt het hele proces herhaald, waardoor er een oneindige lus ontstaat.
5. Om deze situatie te voor komen (met de term ' gaat en neer '), wordt automatisch schalen niet omlaag geschaald. In plaats daarvan wordt de voor waarde overs Laan en opnieuw geëvalueerd wanneer de taak van de service de volgende keer wordt uitgevoerd. De gaat en neer-status kan veel mensen verwarren omdat automatisch schalen niet lijkt te werken wanneer het gemiddelde aantal threads 575 was.

De schatting tijdens een inschaal is bedoeld om "gaat en neer"-situaties te voor komen, waarbij de acties voor schalen en uitschalen voortdurend teruggaan. Houd dit gedrag in acht wanneer u dezelfde drempel waarden kiest voor uitschalen en in.

We raden u aan een geschikte marge te kiezen tussen het uitschalen en de drempel waarden. Bekijk een voor beeld van de volgende betere combi natie van regels.

* Aantal instanties verhogen met 1 als CPU% >= 80
* Exemplaren verlagen met 1 aantal wanneer CPU% <= 60

In dit geval  

1. Stel dat er twee exemplaren met moeten worden gestart.
2. Als het gemiddelde CPU-percentage voor verschillende instanties 80 is, wordt door automatisch schalen geschaald met het toevoegen van een derde exemplaar.
3. Stel nu dat het CPU-percentage tot 60 is.
4. De scale-in-regel voor automatisch schalen maakt een schatting van de uiteindelijke status als deze moet worden geschaald. Bijvoorbeeld: 60 x 3 (aantal huidige instanties) = 180/2 (laatste aantal instanties bij omlaag geschaald) = 90. De functie voor automatisch schalen kan daarom niet worden geschaald omdat deze nu opnieuw moet worden uitgeschaald. In plaats daarvan wordt de schaal overs Laan.
5. De volgende keer dat de controles automatisch worden uitgevoerd, blijft de CPU tot 50. Er wordt opnieuw een schatting gemaakt van 50 x 3-instantie = 150/2 instanties = 75, wat lager is dan de drempel waarde voor uitschalen van 80. de schaal wordt dus gewijzigd in 2 exemplaren.

> [!NOTE]
> Als de engine voor automatisch schalen detecteert dat gaat en neer kan optreden als gevolg van schalen naar het doel aantal exemplaren, wordt er ook geschaald naar een ander aantal exemplaren tussen het huidige aantal en het aantal doelen. Als gaat en neer niet binnen dit bereik optreedt, wordt de schaal bewerking door automatisch schalen voortgezet met het nieuwe doel.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Overwegingen voor het schalen van drempelwaarden voor speciale metrische gegevens
 De drempel waarde is het gemiddelde aantal berichten dat beschikbaar is op basis van het huidige aantal instanties voor speciale metrische gegevens, zoals de metrische gegevens van de opslag of Service Bus wachtrij lengte. Kies zorgvuldig de drempel waarde voor deze metriek.

Laten we dit zien met een voor beeld om te zorgen dat u het probleem beter begrijpt.

* Aantal instanties verhogen met 1 als aantal opslag wachtrij berichten >= 50
* Exemplaren verlagen met 1 aantal wanneer de wachtrij berichten van de opslag <= 10

Houd rekening met de volgende reeks:

1. Er zijn twee exemplaren van de opslag wachtrij.
2. Berichten blijven beschikbaar en wanneer u de opslag wachtrij controleert, wordt het totale aantal gelezen 50. U kunt ervan uitgaan dat automatisch schalen een uitschaal actie moet starten. Houd er echter rekening mee dat dit nog steeds 50/2 = 25 berichten per exemplaar is. Scale-out vindt dus niet plaats. Voor het eerste uitschalen moet het totale aantal berichten in de opslag wachtrij 100 zijn.
3. Vervolgens wordt ervan uitgegaan dat het totale aantal berichten 100 is.
4. Er wordt een derde opslag wachtrij-exemplaar toegevoegd vanwege een uitschaal actie.  De volgende uitschaal actie wordt pas uitgevoerd als het totale aantal berichten in de wachtrij 150 is, omdat 150/3 = 50.
5. Nu wordt het aantal berichten in de wachtrij kleiner. Met drie instanties wordt de eerste ingrijpende actie uitgevoerd wanneer het totale aantal berichten in alle wacht rijen Maxi maal 30 telt, omdat 30/3 = 10 berichten per instantie, de drempel waarde voor inzoomen.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Overwegingen voor het schalen wanneer meerdere profielen zijn geconfigureerd in een instelling voor automatisch schalen
In een instelling voor automatisch schalen kunt u een standaard profiel kiezen dat altijd wordt toegepast zonder afhankelijkheid van de planning of het tijdstip, of u kunt een terugkerend profiel of profiel kiezen voor een vaste periode met een datum-en tijds bereik.

Wanneer de service automatisch wordt geschaald, wordt de volgende volg orde altijd gecontroleerd:

1. Profiel voor vaste datum
2. Terugkerend profiel
3. Standaard profiel (' Always ')

Als aan een voor waarde voor een profiel wordt voldaan, wordt de volgende profiel voorwaarde hieronder niet door automatisch schalen gecontroleerd. Automatisch schalen kan slechts één profiel per keer verwerken. Dit betekent dat als u ook een verwerkings voorwaarde wilt toevoegen uit een profiel met een lagere laag, u deze regels in het huidige profiel moet toevoegen.

Laten we een voor beeld bekijken:

In de onderstaande afbeelding ziet u een instelling voor automatisch schalen met een standaard Profiel van minimum aantal exemplaren = 2 en maximum aantal exemplaren = 10. In dit voor beeld worden regels geconfigureerd om te worden uitgeschaald wanneer het aantal berichten in de wachtrij groter is dan 10 en moet worden geschaald wanneer het aantal berichten in de wachtrij kleiner is dan drie. De resource kan nu worden geschaald tussen twee en tien instanties.

Daarnaast is er een terugkerende profiel verzameling voor maandag. Deze is ingesteld voor minimum aantal instanties = 3 en maximum aantal exemplaren = 10. Dit betekent op maandag, de eerste keer dat automatisch wordt gecontroleerd op deze voor waarde, als het aantal exemplaren twee is, wordt het geschaald naar het nieuwe minimum van drie. Zolang automatisch schalen blijft zoeken naar deze profiel voorwaarde die overeenkomt met (maandag), worden alleen de op CPU gebaseerde scale-out/in-regels verwerkt die zijn geconfigureerd voor dit profiel. Op dit moment wordt er niet gecontroleerd op de lengte van de wachtrij. Als u echter ook wilt dat de lengte van de wachtrij wordt gecontroleerd, moet u deze regels van het standaard profiel ook in uw maandag profiel toevoegen.

Op dezelfde manier wordt met de functie voor automatisch schalen naar het standaard profiel gecontroleerd of aan de minimum-en maximum voorwaarden wordt voldaan. Als het aantal exemplaren op het moment 12 is, wordt het geschaald naar 10, de Maxi maal toegestane waarde voor het standaard profiel.

![instellingen voor automatisch schalen](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Overwegingen voor het schalen wanneer meerdere regels zijn geconfigureerd in een profiel

Er zijn gevallen waarin het mogelijk is om meerdere regels in een profiel in te stellen. De volgende regels voor automatisch schalen worden gebruikt door de engine voor automatisch schalen wanneer er meerdere regels zijn ingesteld.

Bij *uitschalen worden automatisch* schalen uitgevoerd als aan een regel wordt voldaan.
Bij *schalen naar automatisch* schalen moeten aan alle regels worden voldaan.

Stel dat u de volgende vier regels voor automatisch schalen hebt:

* Als CPU < 30%, schaalt u op 1
* Als geheugen < 50%, inschalen op 1
* Als CPU-> 75%, uitschalen op 1
* Als geheugen > 75%, uitschalen op 1

Daarna gebeurt het volgende:

* Als CPU 76% is en geheugen 50% is, kunnen we uitschalen.
* Als CPU 50% is en geheugen 76% is, kunnen we uitschalen.

Aan de andere kant, als CPU 25% is en het geheugen 51% automatisch schalen **niet** is geschaald. Om in te schalen moet CPU 29% zijn en geheugen 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecteer altijd een veilig standaardaantal exemplaren
Het standaard aantal exemplaren is belang rijk omdat automatisch schalen uw service inschaalt naar dat aantal wanneer er geen metrische gegevens beschikbaar zijn. Selecteer daarom een standaard aantal exemplaren dat veilig is voor uw workloads.

### <a name="configure-autoscale-notifications"></a>Configureer meldingen over automatisch schalen
Automatisch schalen wordt in het activiteiten logboek geplaatst als aan een van de volgende voor waarden wordt voldaan:

* Automatisch schalen geeft een schaal bewerking.
* De service voor automatisch schalen heeft een schaal actie voltooid.
* De service voor automatisch schalen kan geen schaal actie ondernemen.
* Er zijn geen metrische gegevens beschikbaar voor de service voor automatisch schalen om een schaal beslissing te nemen.
* Metrische gegevens zijn beschikbaar (herstel) opnieuw om een schaal beslissing te nemen.
* Met automatisch schalen wordt gaat en neer gedetecteerd en wordt de schaal poging afgebroken. In deze situatie ziet u een logboek type van `Flapping` . Als u dit ziet, moet u nagaan of uw drempel waarden te smal zijn.
* Automatisch schalen detecteert gaat en neer, maar kan nog steeds worden geschaald. In deze situatie ziet u een logboek type van `FlappingOccurred` . Als u dit ziet, heeft de engine voor automatisch schalen geprobeerd om te schalen (bijvoorbeeld van 4 instanties naar 2), maar heeft vastgesteld dat dit tot gevolg heeft dat gaat en neer. In plaats daarvan is de engine voor automatisch schalen geschaald naar een ander aantal instanties (bijvoorbeeld door drie instanties te gebruiken in plaats van 2). Dit heeft geen gaat en neer meer veroorzaakt, waardoor deze is geschaald naar dit aantal instanties.

U kunt ook een waarschuwing voor een activiteiten logboek gebruiken om de status van de engine voor automatisch schalen te controleren. Hier volgen enkele voor beelden [van het maken van een waarschuwing voor een activiteiten logboek om alle bewerkingen voor het automatisch schalen van de engine voor uw abonnement te bewaken](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) of om [een waarschuwing voor een activiteiten logboek te maken voor het bewaken van alle mislukte bewerkingen voor automatisch schalen in-of uitschalen in uw abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Naast het gebruik van waarschuwingen voor het activiteiten logboek kunt u ook e-mail berichten of webhook-meldingen configureren om op de hoogte te worden gesteld van geslaagde schaal acties via het tabblad meldingen in de instelling voor automatisch schalen.

## <a name="next-steps"></a>Volgende stappen
- [Maak een waarschuwing voor een activiteiten logboek om alle bewerkingen voor het automatisch schalen van de engine voor uw abonnement te bewaken.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Een waarschuwing voor een activiteiten logboek maken voor het bewaken van alle mislukte werk schalen voor automatisch schalen in-en uitschalen voor uw abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

