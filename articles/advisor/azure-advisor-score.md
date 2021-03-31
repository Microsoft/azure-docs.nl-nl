---
title: Azure-workloads optimaliseren met Advisor-Score
description: Gebruik Azure Advisor Score om optimaal te profiteren van Azure.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630119"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Azure-workloads optimaliseren met Advisor-Score

## <a name="introduction-to-advisor-score"></a>Inleiding tot Advisor-Score

Azure Advisor biedt best practice aanbevelingen voor uw workloads. Deze aanbevelingen zijn persoonlijk en kunnen actie ondernemen om u te helpen:

* Verbeter de postuur van uw workloads en Optimaliseer uw Azure-implementaties.
* Proactief voor komen van belangrijkste problemen door aanbevolen procedures te volgen.
* Evalueer uw Azure-workloads met de vijf pijlers van het [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).

Als kern functie van Advisor kan de Advisor-score u helpen om deze doel stellingen effectief en efficiënt te bereiken.

Om optimaal te profiteren van Azure, is het belang rijk om te begrijpen waar u zich bevindt in de optimalisatie van uw workload. U moet weten welke services of bronnen goed worden verbruikt en wat niet. Daarnaast wilt u weten hoe u uw acties op basis van aanbevelingen kunt priori teren om het resultaat te maximaliseren.

Het is ook belang rijk om de voortgang bij te houden en te rapporteren die u in deze optimalisatie reis gaat maken. Met Advisor Score kunt u al deze dingen eenvoudig doen met de nieuwe gamificatietoepassing-ervaring.

Als uw persoonlijke Cloud consultant Azure Advisor uw telemetrie van het gebruik en de resource configuratie doorlopend beoordelen om te controleren op best practices voor de branche. In Advisor worden de bevindingen vervolgens samengevoegd tot één Score. Met deze score kunt u in één oogopslag zien of u de benodigde stappen neemt om betrouw bare, veilige en kostenbesparende oplossingen te bouwen.

De Advisor-Score bestaat uit een algemene score, die verder kan worden opgesplitst in vijf categorie scores. Een score voor elke categorie Advisor vertegenwoordigt de vijf pijlers van het Well-Architected Framework.

U kunt de voortgang volgen die u in de loop van de tijd maakt door uw totale score en categorie score te bekijken met dagelijkse, wekelijkse en maandelijkse trends. U kunt ook benchmarks instellen om u te helpen bij het bereiken van uw doel stellingen.

 ![Scherm opname van de Advisor-Score pagina.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Een Advisor-Score interpreteren

Advisor geeft uw totale Advisor-Score en een uitsplitsing van Advisor-categorieën weer, in procenten. Een Score van 100% in een categorie betekent dat al uw resources die worden beoordeeld door Advisor, voldoen aan de aanbevolen procedures die door Advisor worden aanbevolen. Aan het andere uiteinde van het spectrum betekent een Score van 0% dat geen van uw resources die zijn beoordeeld door Advisor de aanbevelingen van Advisor volgen. Met behulp van deze score korrels kunt u eenvoudig de volgende stroom bereiken:

* Met **Advisor Score** kunt u de basis lijn van uw werk belasting of abonnementen bepalen op basis van een Advisor-Score. U kunt ook de historische trends zien om inzicht te krijgen in wat uw trend is.
* **Score per categorie** voor elke aanbeveling vertelt u welke openstaande aanbevelingen uw score optimaal zullen verbeteren. Deze waarden zijn zowel het gewicht van de aanbeveling als het voor speld gemak van de implementatie. Deze factoren helpen u ervoor te zorgen dat u de meeste waarde kunt verkrijgen met uw tijd. Ze helpen u ook om prioriteiten te stellen.
* De **categorie Score-impact** voor elke aanbeveling helpt u bij het bepalen van uw herstel acties voor elke categorie.

De bijdrage van elke aanbeveling aan uw categorie score wordt duidelijk weer gegeven op de pagina **Advisor Score** van de Azure Portal. U kunt elke categorie Score verhogen op basis van het percentage punt dat wordt vermeld in de kolom **potentiële Score verhoging** . Deze waarde weerspiegelt zowel het gewicht van de aanbeveling binnen de categorie als het voor speld gemak van de implementatie om de mogelijk eenvoudigste taken te verhelpen. Als u zich richt op de aanbevelingen met de grootste invloed op de score, kunt u de meest voor uitgang van tijd maken.

![Scherm afbeelding waarin de impact van de Advisor-score wordt weer gegeven.](./media/advisor-score-2.png)

Als er aanbevelingen voor Advisor niet relevant zijn voor een afzonderlijke resource, kunt u deze aanbevelingen uitstellen of negeren. Ze worden uitgesloten van de score berekening bij de volgende vernieuwing. Advisor gebruikt deze invoer ook als aanvullende feedback om het model te verbeteren.

## <a name="how-is-an-advisor-score-calculated"></a>Hoe wordt een Advisor-score berekend?

In Advisor worden de scores van uw categorie en uw totale Advisor-score als percentages weer gegeven. Een Score van 100% in een categorie betekent dat al uw resources, *beoordeeld door Advisor*, voldoen aan de aanbevolen procedures die door Advisor worden aanbevolen. Aan het andere uiteinde van het spectrum betekent een Score van 0% dat geen van uw resources, beoordeeld door Advisor, de aanbevelingen van Advisor volgt.

**Elk van de vijf categorieën heeft een hoogst mogelijke Score van 100.** Uw totale Advisor-score wordt berekend als een som van elke toepasselijke categorie Score, gedeeld door de som van de hoogst mogelijke Score van alle toepasselijke categorieën. Voor de meeste abonnementen betekent Advisor dat de Score van elke categorie wordt toegevoegd en gedeeld door 500. Maar *elke categorie score wordt alleen berekend als u resources gebruikt die door Advisor zijn beoordeeld*.

### <a name="advisor-score-calculation-example"></a>Voor beeld van Advisor score berekening

* **Score voor één abonnement:** Dit voor beeld is het eenvoudige gemiddelde van alle Advisor categorie scores voor uw abonnement. Als de Advisor categorie scores zijn- **cost** = 73, **betrouw baarheid** = 85, **operationele uitmuntendheid** = 77 en **prestaties** = 100, zou de Advisor-Score (73 + 85 + 77 + 100)/(4x100) = 0,84% of 84% zijn.
* **Score voor meerdere abonnementen:** Wanneer er meerdere abonnementen zijn geselecteerd, worden de totale gegenereerde Advisorische scores gewogen als geaggregeerde categorie scores. Hier wordt elke categorie Score van Advisor geaggregeerd op basis van bronnen die worden verbruikt door abonnementen. Nadat Advisor de gewogen geaggregeerde categorie scores heeft, voert Advisor een eenvoudige gemiddelde berekening uit om u een algemene score voor abonnementen te geven.

### <a name="scoring-methodology"></a>Score methodologie

De berekening van de Advisor-Score kan in vier stappen worden samenvatten:

1. Advisor berekent de *verkoop kosten van betrokken resources*. Deze resources bevinden zich in uw abonnementen die ten minste één aanbeveling hebben in Advisor.
1. Advisor berekent de *kosten van de geraamde resources*. Deze resources zijn de functies die door Advisor worden bewaakt, of ze wel of geen aanbevelingen hebben.
1. Voor elk type aanbeveling berekent Advisor de *goede resource verhouding*. Deze verhouding is de kosten voor de detail handel van beïnvloede resources gedeeld door de kosten van de geraamde resources.
1. Advisor past drie extra gewichten toe op de goede resource verhouding van elke categorie:

   * Aanbevelingen met meer impact worden gewogen zwaarder dan aanbevelingen met een lagere impact.
   * Resources met lange, specifieke aanbevelingen tellen meer bij aan uw score.
   * Resources die u uitstelt of negeert in Advisor, worden volledig verwijderd uit uw score berekening.

Dit model wordt door Advisor toegepast op het niveau van een Advisor-categorie om een Advisor-score voor elke categorie op te geven. **Security** maakt gebruik van een [veilig Score](../security-center/secure-score-security-controls.md#introduction-to-secure-score) model. Een eenvoudig gemiddelde geeft de laatste Advisor-Score.

## <a name="advisor-score-faqs"></a>Veelgestelde vragen over Advisor-Score

### <a name="how-often-is-my-score-refreshed"></a>Hoe vaak is mijn score vernieuwd?

Uw score wordt minstens eenmaal per dag vernieuwd.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Waarom hebben sommige aanbevelingen de lege waarde '-' in de kolom Categorie Score impact?

Advisor bevat niet onmiddellijk nieuwe aanbevelingen of aanbevelingen met recente wijzigingen in het score model. Na een korte evaluatie periode zijn ze meestal enkele weken opgenomen in de score.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Waarom is de kosten score hoger voor sommige aanbevelingen, zelfs als ze een lagere mogelijke besparing hebben?

Uw **kosten** Score weerspiegelt uw mogelijke besparingen van geweinig gebruikte resources en het voor speld gemak voor het implementeren van deze aanbevelingen. Extra gewicht wordt bijvoorbeeld toegepast op resources die invloed hebben op een langere periode, zelfs als de mogelijke besparingen lager zijn.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Waarom heb ik geen score voor een of meer categorieën of abonnementen?

Advisor genereert alleen een score voor de categorieën en abonnementen die resources hebben die door Advisor zijn beoordeeld.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Wat gebeurt er als een aanbeveling niet relevant is?

Als u een aanbeveling van Advisor negeert, wordt deze wegge laten uit de berekening van uw score. Bij het negeren van aanbevelingen wordt de kwaliteit van aanbevelingen ook verbeterd door Advisor.

### <a name="why-did-my-score-change"></a>Waarom is mijn score gewijzigd?

U kunt de score wijzigen als u betrokken resources herstelt door de aanbevolen procedures voor Advisor te nemen. Als u of iemand met machtigingen voor uw abonnement nieuwe resources heeft gewijzigd of gemaakt, ziet u mogelijk ook schommelingen in uw score. Uw score is gebaseerd op een verhouding van de kosten die invloed hebben op de resources ten opzichte van de totale kosten van alle resources.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Hoe berekent Advisor de kosten van resources voor een abonnement?

Advisor gebruikt de betalen naar gebruik-tarieven die zijn gepubliceerd op [Azure-prijzen](https://azure.microsoft.com/pricing/). Deze tarieven zijn niet van toepassing op eventuele kortingen. De tarieven worden vervolgens vermenigvuldigd met het aantal gebruik op de laatste dag dat de resource is toegewezen. Als u de kortingen weglaat van de berekening van de resource kosten, worden de Advisor-scores vergeleken met abonnementen, tenants en inschrijvingen waarbij kortingen kunnen variëren.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Moet ik de aanbevelingen in Advisor bekijken om punten voor mijn score op te halen?

Nee. Uw score geeft aan of u aanbevolen procedures voor Advisor aanbeveelt, zelfs als u deze best practices proactief doorneemt en nooit uw aanbevelingen in Advisor bekijkt.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>Maakt de Score methodologie onderscheid tussen werk belastingen voor productie en ontwikkelings testen?

Nee, niet voor nu. Maar u kunt aanbevelingen voor afzonderlijke resources negeren als deze resources worden gebruikt voor ontwikkeling en testen en de aanbevelingen niet van toepassing zijn.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Kan ik de scores van een abonnement met 100 resources en een abonnement met 100.000 resources vergelijken?

De Score methodologie is ontworpen voor het beheren van het aantal resources voor een combi natie van abonnementen en services. Abonnementen met minder resources kunnen een hogere of lagere score hebben dan abonnementen met meer resources.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Wat betekent dat wanneer ik ' binnenkort ' wordt weer geven in de kolom Score impact?

Dit bericht geeft aan dat de aanbeveling nieuw is en we werken eraan om deze naar het Advisor-score model te brengen. Nadat deze nieuwe aanbeveling in aanmerking komt voor een score berekening, ziet u de waarde voor de score voor uw aanbeveling.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Is mijn score afhankelijk van wat ik besteed aan Azure?

Nee. De score is niet noodzakelijkerwijs een reflectie van de hoeveelheid die u besteedt. Onnodige uitgaven resulteren in een lagere **kosten** Score.

## <a name="access-advisor-score"></a>Access Advisor-Score

De Advisor-Score bevindt zich in de open bare preview in de Azure Portal. Bekijk in het linkerdeel venster, onder de sectie **Advisor** , **Advisor Score**.

![Scherm opname van het toegangs punt voor de Advisor-Score.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:

* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen van Advisor met betrekking tot kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
