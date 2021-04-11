---
title: Beveiligingsscore in Azure Security Center
description: Beschrijving van de beveiligde Score van Azure Security Center en de bijbehorende beveiligings controles
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 2f5d98dd9bf893065f2bf9c37cbec4384d0f7c94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727137"
---
# <a name="secure-score-in-azure-security-center"></a>Beveiligingsscore in Azure Security Center

## <a name="introduction-to-secure-score"></a>Inleiding tot beveiligde Score

Azure Security Center heeft twee hoofd doelen: 

- om inzicht te krijgen in uw huidige beveiligings situatie
- om u te helpen uw beveiliging efficiënt en effectief te verbeteren

De centrale functie in Security Center waarmee u deze doelen kunt bereiken, is een **veilige Score**.

Security Center controleert uw resources, abonnementen en organisatie doorlopend op beveiligingsproblemen. Vervolgens worden alle bevindingen tot één enkele score samengevoegd, zodat u in een oogopslag uw huidige beveiligingssituatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau is.

De beveiligde score wordt weer gegeven op de Azure Portal pagina's als een percentage waarde, maar de onderliggende waarden worden ook duidelijk weer gegeven:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Algemene beveiligde Score zoals weer gegeven in de portal":::

Bekijk de pagina met aanbevelingen van Security Center voor de uitstaande acties die nodig zijn om uw score te verhogen om uw beveiliging te verbeteren. Elke aanbeveling bevat instructies om u te helpen bij het oplossen van het specifieke probleem.

Aanbevelingen zijn onderverdeeld in **beveiligings controles**. Elk besturings element is een logische groep gerelateerde beveiligings aanbevelingen en weerspiegelt uw kwets bare aanvals oppervlakken. Uw score is alleen verbeterd wanneer u *alle* aanbevelingen voor één resource in een besturings element herstelt. Als u wilt zien hoe goed uw organisatie elke afzonderlijke kwets baarheid beveiligt, controleert u de scores voor elk beveiligings beheer.

Zie [hoe uw beveiligde score wordt berekend](secure-score-security-controls.md#how-your-secure-score-is-calculated) hieronder voor meer informatie. 

## <a name="how-your-secure-score-is-calculated"></a>Hoe uw beveiligde score wordt berekend 

De bijdrage van elk beveiligings beheer voor de algehele beveiligde score wordt duidelijk weer gegeven op de pagina aanbevelingen.

[![De verbeterde beveiligde Score introduceert beveiligings controles](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Om alle mogelijke punten voor een beveiligings controle op te halen, moeten al uw resources voldoen aan alle beveiligings aanbevelingen binnen het beveiligings beheer. Security Center heeft bijvoorbeeld meerdere aanbevelingen over hoe u uw beheer poorten kunt beveiligen. U moet deze allemaal herstellen om een verschil te maken met uw beveiligde Score.

Het beveiligings beheer met de naam ' systeem updates Toep assen ' heeft bijvoorbeeld een maximum Score van zes punten, die u in de tooltip kunt zien op de mogelijke verhogings waarde van het besturings element:

[![Het beveiligings beheer ' systeem updates Toep assen '](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

De maximum score voor dit besturings element, het Toep assen van systeem updates, is altijd 6. In dit voor beeld zijn er 50 resources. Daarom delen we de maximale score met 50 en het resultaat is dat elke resource 0,12 punten bijdraagt. 

* **Mogelijke verhoging** (0,12 x 8 beschadigde resources = 0,96): de resterende punten die voor u beschikbaar zijn in het besturings element. Als u alle aanbevelingen in dit besturings element herstelt, neemt uw score toe met 2% (in dit geval 0,96 punten afgerond tot op 1 punt). 
* **Huidige Score** (0,12 x 42 gezonde resources = 5,04): de huidige score voor dit besturings element. Elk besturings element draagt bij aan de totale score. In dit voor beeld draagt het besturings element 5,04 punten met het huidige beveiligde totaal.
* **Maximale score** -het maximum aantal punten dat u kunt krijgen door alle aanbevelingen binnen een besturings element te volt ooien. De maximum score voor een besturings element duidt op de relatieve betekenis van dat besturings element. Gebruik de maximale score waarden om de problemen te sorteren die het eerst moeten worden uitgevoerd. 


### <a name="calculations---understanding-your-score"></a>Berekeningen: uitleg van uw Score

|Metrisch|Formule en voor beeld|
|-|-|
|**Huidige Score van beveiligings beheer**|<br>![Vergelijking voor het berekenen van de Score van een beveiligings controle](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Elk afzonderlijk beveiligings beheer draagt bij aan de beveiligings Score. Elke resource die wordt beïnvloed door een aanbeveling binnen het besturings element, draagt bij aan de huidige Score van het besturings element. De huidige score voor elk besturings element is een meting van de status van de resources *in* het besturings element.<br>![Knop info met de waarden die worden gebruikt bij het berekenen van de huidige Score van het beveiligings beheer](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In dit voor beeld wordt de maximale Score van 6 gedeeld door 78, omdat dat de som is van de in orde zijnde en slechte resources.<br>6/78 = 0,0769<br>Als u wilt vermenigvuldigen met het aantal ongezonde resources (4), resulteert dit in de huidige Score:<br>0,0769 * 4 = **0,31**<br><br>|
|**Beveiligingsscore**<br>Enkel abonnement|<br>![Vergelijking voor het berekenen van de beveiligde Score van een abonnement](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Een beveiligde Score van één abonnement waarbij alle besturings elementen zijn ingeschakeld](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In dit voor beeld is er één abonnement met alle beveiligings controles beschikbaar (een potentiële maximum Score van 60 punten). De score toont 28 punten van een mogelijke 60 en de resterende 32 punten worden weer gegeven in de sectie ' potentiële Score verhogen ' van de beveiligings controles.<br>![Lijst met besturings elementen en de potentiële toename van de Score](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Beveiligingsscore**<br>Meerdere abonnementen|<br>![Vergelijking voor het berekenen van de beveiligde score voor meerdere abonnementen](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Bij het berekenen van de gecombineerde score voor meerdere abonnementen, bevat Security Center een *gewicht* voor elk abonnement. De relatieve gewichten voor uw abonnementen worden bepaald door Security Center op basis van factoren zoals het aantal resources.<br>De huidige score voor elk abonnement wordt berekend op dezelfde manier als voor één abonnement, maar vervolgens wordt het gewicht toegepast, zoals weer gegeven in de vergelijking.<br>Wanneer er meerdere abonnementen worden weer gegeven, evalueert de beveiligde score alle resources binnen het ingeschakelde beleid en worden de gecombineerde impact van elk van de maximale scores van het beveiligings beheer gegroepeerd.<br>![Beveiligde score voor meerdere abonnementen waarbij alle besturings elementen zijn ingeschakeld](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>De gecombineerde score is **geen** gemiddelde. in plaats daarvan is het de geëvalueerde postuur van de status van alle resources in alle abonnementen.<br>Ook als u naar de pagina aanbevelingen gaat en de potentiële punten die beschikbaar zijn, opneemt, zult u merken dat het verschil tussen de huidige Score (24) en de Maxi maal beschik bare Score (60) is.|


### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Welke aanbevelingen zijn opgenomen in de berekeningen van de veilige Score?

Alleen ingebouwde aanbevelingen hebben invloed op de beveiligde Score.

Aanbevelingen die als **Preview** zijn gemarkeerd, zijn niet opgenomen in de berekeningen van uw beveiligde Score. Ze moeten, waar mogelijk, nog steeds worden hersteld. Wanneer de preview-periode is afgelopen, worden ze dus meegerekend in uw score.

Een voorbeeld van een preview-aanbeveling:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Aanbeveling met de preview-markering":::

## <a name="improve-your-secure-score"></a>Uw secure score verbeteren

Als u uw beveiligde score wilt verbeteren, kunt u de aanbevelingen voor beveiliging herstellen in uw lijst met aanbevelingen. U kunt elke aanbeveling hand matig herstellen voor elke resource, of met behulp van de **oplossing voor snel oplossen!** optie (indien beschikbaar) om een herbemiddeling toe te passen voor een aanbeveling voor een groep resources snel. Zie [aanbevelingen herstellen](security-center-remediate-recommendations.md)voor meer informatie.

Een andere manier om uw score te verbeteren en ervoor te zorgen dat uw gebruikers geen resources maken die een negatieve invloed hebben op uw score, is door de opties voor afdwingen en weigeren te configureren voor de relevante aanbevelingen. Meer informatie vindt u in [Onjuiste configuraties voorkomen met de aanbevelingen voor afdwingen/weigeren](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Beveiligings controles en hun aanbevelingen

De volgende tabel bevat de beveiligings opties in Azure Security Center. Voor elk besturings element ziet u het maximum aantal punten dat u aan uw beveiligde Score kunt toevoegen als u *alle* aanbevelingen die worden vermeld in het besturings element herstelt, voor *al* uw resources. 

De set met beveiligings aanbevelingen die worden meegeleverd met Security Center is afgestemd op de beschik bare resources in de omgeving van elke organisatie. De aanbevelingen kunnen verder worden aangepast door [beleid uit te scha kelen](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) en [specifieke bronnen uit te sluiten van een aanbeveling](exempt-resource.md). 
 
We raden u aan elke organisatie de toegewezen Azure Policy-initiatieven zorgvuldig te controleren. 

> [!TIP]
> Zie [werken met beveiligings beleid](tutorial-security-policy.md)voor meer informatie over het controleren en bewerken van uw initiatieven. 

Hoewel het standaard Security Initiative van Security Center is gebaseerd op de best practices en standaarden van de branche, zijn er scenario's waarin de hieronder vermelde ingebouwde aanbevelingen mogelijk niet volledig in uw organisatie passen. Daarom is het soms nood zakelijk om het standaard initiatief te corrigeren, zonder dat dit de veiligheid in gevaar heeft, zodat het wordt afgestemd op het eigen beleid van uw organisatie. industrie normen, regelgevings normen en benchmarks waarmee u moet voldoen.<br><br>
<div class="foo">

<style type="text/css"> . TG {Border-samen vouwen: samen vouwen; rand-afstand: 0;}. TG TD {Border-Color: zwart; rand stijl: effen; rand breedte: 1px; letter type-serie: Arial, Sans-Serif; letter type: 14px; overflow: verborgen; opvulling: 10px 5px; woord-onderbreking: normaal;}. TG th {Border-Color: Black; rand-Style: Solid; rand breedte: 1px; lettertype familie: Arial, Sans-Serif; letter type: 18px; letter type-Weight: normaal; overflow: verborgen; opvulling: 10px 5px; woord-onderbroken: Normal;}. tg. TG: cly1 {tekst uitlijning: links; verticaal uitlijnen: midden}. tg. TG-lboi {Border-Color: overnemen; tekst uitlijning: links; verticaal uitlijnen: Midden} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Veelgestelde vragen over beveiligde scores

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Als ik slechts drie van de vier aanbevelingen in een beveiligings controle adresseer, wordt mijn beveiligde Score gewijzigd?
Nee. Deze wijziging wordt pas doorgevoerd wanneer u alle aanbevelingen voor één resource herstelt. Als u de maximum score voor een besturings element wilt ophalen, moet u alle aanbevelingen voor alle resources herstellen.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Als een aanbeveling niet van toepassing is en deze in het beleid uitschakelt, wordt mijn beveiligings controle vervuld en mijn beveiligde score bijgewerkt?
Ja. We raden u aan om aanbevelingen uit te scha kelen wanneer ze niet van toepassing zijn in uw omgeving. Zie [beveiligings beleid uitschakelen](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)voor instructies over het uitschakelen van een specifieke aanbeveling.

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Als een beveiligings besturings element me nul punten naar mijn beveiligde Score biedt, moet ik dit dan negeren?
In sommige gevallen ziet u een maximum Score van het besturings element die groter is dan nul, maar de impact is nul. Wanneer de incrementele score voor het oplossen van resources verwaarloosbaar is, wordt deze afgerond op nul. Negeer deze aanbevelingen niet omdat ze nog steeds betere beveiliging bieden. De enige uitzonde ring hierop is het besturings element ' extra best practice '. Door deze aanbevelingen te herstellen, wordt uw score niet verhoogd, maar wordt uw algehele beveiliging verbeterd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u een beschrijving van de beveiligde Score en de beveiligings controles die worden geïntroduceerd. Raadpleeg de volgende artikelen voor gerelateerd materiaal:

- [Meer informatie over de verschillende elementen van een aanbeveling](security-center-recommendations.md)
- [Meer informatie over het oplossen van aanbevelingen](security-center-remediate-recommendations.md)
- [De GitHub-hulpprogram ma's voor het werken met een beveiligde Score weer geven](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)


> [!div class="nextstepaction"]
> [Uw beveiligde Score openen en bijhouden](secure-score-access-and-track.md)