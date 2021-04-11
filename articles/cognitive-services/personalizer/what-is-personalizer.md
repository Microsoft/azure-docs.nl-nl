---
title: Wat is Personalizer?
description: Personalizer is een cloudservice waarmee u de beste ervaring kunt kiezen om aan uw gebruikers te laten zien, waarbij u leert van hun realtime gedrag.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, machine learning
ms.openlocfilehash: b2577502907b69e134651c93ab7a98fc51e9aaa6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169246"
---
# <a name="what-is-personalizer"></a>Wat is Personalizer?

Azure Personalizer is een cloudservice waarmee u voor uw toepassingen het beste inhoudsitem kunt selecteren om voor uw gebruikers weer te geven. U kunt de Personalizer-service gebruiken om te bepalen welk product aan gebruikers moet worden voorgesteld of om de optimale positie voor een advertentie te bepalen. Nadat de inhoud is weergegeven aan de gebruiker, bewaakt de uw toepassing de reactie van de gebruiker en wordt er een score gerapporteerd aan de Personalizer-service. Dit garandeert continue verbetering van het machine learning-model en het vermogen van Personalizer om het beste inhoudsitem te selecteren op basis van de contextinformatie die het ontvangt.

> [!TIP]
> Inhoud bestaat uit verschillende informatie-eenheden, zoals tekst, afbeeldingen, URL's, e-mailadressen of andere items die u wilt selecteren van en wilt weergeven aan uw gebruikers.

Deze documentatie bevat de volgende artikel typen:  

* [**Quick**](quickstart-personalizer-sdk.md) starts zijn aan de slag-instructies die u helpen bij het maken van aanvragen voor de service.  
* [**Hand leidingen**](how-to-settings.md) bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.  
* [**Concepten**](how-personalizer-works.md) geven uitgebreide uitleg over de service functionaliteit en-functies.  
* [**Zelf studies**](tutorial-use-personalizer-web-app.md) zijn meer gidsen die laten zien hoe u de service kunt gebruiken als onderdeel in bredere zakelijke oplossingen.  

Voordat u aan de slag gaat, kunt u [personaler proberen met deze interactieve demo](https://personalizationdemo.azurewebsites.net/).

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hoe selecteert Personalizer het beste inhoudsitem?

Personalizer gebruikt **bekrachtigend leren** om het beste item (_actie_) te selecteren op basis van gezamenlijk gedrag en scores toe te kennen aan alle gebruikers. Acties zijn de inhoudsitems, zoals nieuwsartikelen, specifieke films of producten.

Met de **Positie**-aanroep wordt het actie-item in combinatie met de functies van de actie en contextfuncties gebruikt om het bovenste actie-item te selecteren:

* **Acties met functies**: inhoudsitems met specifieke functies voor elk item
* **Contextfuncties**: functies van uw gebruikers, hun context of hun omgeving wanneer ze uw app gebruiken

Met de Positie-aanroep wordt de id van elk inhoudsitem, __actie__, geretourneerd om in het veld **Beloningsactie-id** aan de gebruiker te tonen.

De __actie__ die aan de gebruiker wordt weergegeven, wordt gekozen aan de hand van machine learning-modellen die de totale hoeveelheid beloningen over een bepaalde periode proberen te maximaliseren.

### <a name="sample-scenarios"></a>Voorbeeldscenario's

Bekijk een aantal scenario's waarin Personalizer kan worden gebruikt om de beste inhoud te selecteren om aan een gebruiker weer te geven.

|Inhoudstype|Acties (met functies)|Contextfuncties|Geretourneerde beloningsactie-id<br>(deze inhoud weergeven)|
|--|--|--|--|
|Nieuwslijst|a. `The president...` (binnenlands nieuws, politiek, [tekst])<br>b. `Premier League ...` (wereldnieuws, sport, [tekst, afbeelding, video])<br> c. `Hurricane in the ...` (regionaal nieuws, weer, [tekst, afbeelding]|Het apparaat waarvan het nieuws wordt afgelezen<br>Maand of seizoen<br>|een `The president...`|
|Lijst met films|1. `Star Wars` (1977, [actie, avontuur, fantasy], George Lucas)<br>2. `Hoop Dreams` (1994, [documentaire, sport], Steve James<br>3. `Casablanca` (1942, [romantiek, drama, oorlog], Michael Curtiz)|Het apparaat waarop de film wordt bekeken<br>schermgrootte<br>Type gebruiker<br>|3. `Casablanca`|
|Lijst met producten|i. `Product A` (3 kg, $$$$, bezorgen binnen 24 uur)<br>ii. `Product B` (20 kg, $$, verzending binnen 2 weken met douane)<br>iii. `Product C` (3 kg, $$$, bezorgen binnen 48 uur)|Apparaat waarop de gebruiker onlinewinkels bezoekt<br>Uitgavecategorie van gebruiker<br>Maand of seizoen|ii. `Product B`|

Personalizer gebruikt bekrachtigend leren om de beste actie te selecteren. Dit wordt ook wel de _beloningsactie-id_ genoemd. Voor het machine learning-model wordt het volgende gebruikt: 

* Een getraind model: eerder ontvangen informatie van de Personalizer-service die wordt gebruikt om het machine learning-model te verbeteren
* Huidige gegevens: specifieke acties met functies en contextfuncties

## <a name="when-to-use-personalizer"></a>Wanneer kan ik Personalizer gebruiken

De **Positie**-[API](https://go.microsoft.com/fwlink/?linkid=2092082) van Personalizer wordt steeds aangeroepen wanneer in uw toepassing inhoud wordt getoond. Dit wordt een **gebeurtenis** genoemd, voorzien van een _gebeurtenis-id_.

De **belonings**-[API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) van Personalizer kan in real time worden aangeroepen of later, zodat het beter bij uw infrastructuur past. U bepaalt de beloningsscore op basis van uw zakelijke behoeften. De beloningsscore ligt tussen 0 en 1. Dat kan één waarde zijn, zoals 1 voor goed en 0 voor slecht, of een getal dat wordt geproduceerd door een algoritme dat u maakt voor uw bedrijfsdoelen en metrische gegevens.

## <a name="content-requirements"></a>Inhoudsvereisten

Gebruik Personalizer wanneer uw inhoud:

* Heeft een beperkte set acties of items (maximaal ~50) om uit te kiezen in elk gepersonaliseerde gebeurtenis. [Gebruik een aanbevelingsengine](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) om de lijst elke keer dat u Rang aanroep in de Personalizer-service in te korten tot 50 items als u een grotere lijst hebt.
* Informatie bevat waarmee de inhoud die u wilt classificeren, wordt beschreven: _acties met functies_ en _contextfuncties_.
* Minimaal 1000 aan inhoud gerelateerde gebeurtenissen per dag nodig heeft om Personalizer te activeren. Als Personalizer niet de minimaal vereiste hoeveelheid verkeer ontvangt, duurt het langer om met de service het beste inhoudsitem te bepalen.

Omdat voor Personalizer collectieve gegevens in bijna realtime worden gebruikt om het beste inhoudsitem te retourneren, wordt het volgende niet uitgevoerd met de service:
* Gebruikersprofielgegevens bewaren en beheren
* De voorkeuren of geschiedenis van afzonderlijke gebruikers aan logboeken toevoegen
* Opgeschoonde en gelabelde inhoud vereisen

## <a name="how-to-design-for-and-implement-personalizer"></a>Ontwerpen voor Personalizer en Personalizer implementeren

1. [Ontwerp](concepts-features.md) en plan voor inhoud, **_acties_** en **_context_**. Bepaal het beloningsalgoritme voor de **_beloningsscore_**.
1. Elke [Personalizer-resource](how-to-settings.md) die u maakt, wordt als één leerlus beschouwd. De lus ontvangt zowel de Positie- als de Beloningsaanroep voor die inhoud of gebruikerservaring.

    |Resourcetype| Doel|
    |--|--|
    |[Leerlingmodus](concept-apprentice-mode.md) `E0`|Train het Personalizer-model zonder dat dit gevolgen heeft voor uw bestaande toepassing. Implementeer vervolgens het onlineleergedrag naar een productieomgeving|
    |Standard, `S0`|Onlineleergedrag in een productieomgeving|
    |Gratis, `F0`| Onlineleergedrag proberen in een niet-productieomgeving|

1. Voeg Personalizer toe aan uw toepassing, website of systeem:
    1. Voeg een **Positie**-aanroep toe aan Personalizer in uw toepassing, website of systeem om het beste _inhoudsitem_ te bepalen voordat de gebruiker de inhoud te zien krijgt.
    1. Toon gebruikers het beste _inhoudsitem_. Dit item is de geretourneerde _beloningsactie-id_.
    1. Pas _bedrijfslogica_ toe op verzamelde informatie over het gedrag van de gebruiker om de **beloningsscore** te bepalen, zoals:

    |Gedrag|Berekende beloningsscore|
    |--|--|
    |De gebruiker heeft het beste _inhoudsitem_ (beloningsactie-id) geselecteerd|**1**|
    |De gebruiker heeft andere inhoud geselecteerd|**0**|
    |De gebruiker heeft zijn of haar acties gepauzeerd en doelloos rondgekeken voordat hij of zij het beste _inhoudsitem_ (beloningsactie-id) selecteerde|**0,5**|

    1. Een **beloningsaanroep** toevoegen om een beloningsscore tussen 0 en 1 te verzenden
        * Direct na weergave van uw inhoud
        * Of op een later moment in een offlinesysteem
    1. [Evalueer uw lus](concepts-offline-evaluation.md) met een offline-evaluatie na een bepaalde gebruiksperiode. Met een offline-evaluatie kunt u de effectiviteit van de Personalizer-service testen en beoordelen zonder uw code te hoeven wijzigen of de gebruikerservaring aan te tasten.

## <a name="reference"></a>Naslaginformatie 

* [Personalizer C#/.NET-SDK](/dotnet/api/overview/azure/cognitiveservices/client/personalizer)
* [Personalizer Go-SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Personalizer JavaScript-SDK](/javascript/api/@azure/cognitiveservices-personalizer/)
* [Personalizer Python-SDK](/python/api/overview/azure/cognitiveservices/personalizer)
* [REST-API's](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zo werkt Personalizer](how-personalizer-works.md)
> [Wat is bekrachtigend leren?](concepts-reinforcement-learning.md)
