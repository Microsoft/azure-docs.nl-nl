---
title: Een herkennings model opgeven-gezicht
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u kunt kiezen welk herkennings model u wilt gebruiken met uw Azure face-toepassing.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: cd4ed992557a3c333919b63cc36757beb2f70454
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433553"
---
# <a name="specify-a-face-recognition-model"></a>Een model voor gezichtsherkenning opgeven

Deze hand leiding laat zien hoe u een gezichts herkennings model kunt opgeven voor gezichts detectie, identificatie en soort gelijke Zoek opdrachten met behulp van de Azure face-service.

De face-service maakt gebruik van machine learning modellen voor het uitvoeren van bewerkingen op menselijke gezichten in installatie kopieën. We blijven de nauw keurigheid van onze modellen verbeteren op basis van feedback van klanten en de voor uitgang van onderzoek en wij leveren deze verbeteringen als model updates. Ontwikkel aars hebben de mogelijkheid om op te geven welke versie van het gezichts herkennings model ze willen gebruiken. ze kunnen het model kiezen dat het beste past bij hun gebruik.

Azure face service heeft vier herkennings modellen beschikbaar. De modellen _recognition_01_ (gepubliceerd 2017), _recognition_02_ (uitgegeven 2019) en _recognition_03_ (uitgegeven 2020) worden continu ondersteund om achterwaartse compatibiliteit te garanderen voor klanten die FaceLists of **PersonGroup** s gebruiken die met deze modellen zijn gemaakt. Een **FaceList** of **Persongroup** maakt altijd gebruik van het herkennings model waarmee het is gemaakt, en nieuwe gezichten worden gekoppeld aan dit model wanneer ze worden toegevoegd. Dit kan niet worden gewijzigd nadat het is gemaakt en klanten het bijbehorende herkennings model moeten gebruiken met de bijbehorende **FaceList** of **PersonGroup**.

U kunt op eigen gemak overstappen op latere erkennings modellen. u moet echter nieuwe FaceLists en PersonGroups maken met het herkennings model van uw keuze.

Het _recognition_04_ model (gepubliceerd 2021) is het meest nauw keurige model dat momenteel beschikbaar is. Als u een nieuwe klant bent, raden we u aan dit model te gebruiken. _Recognition_04_ biedt een verbeterde nauw keurigheid voor de vergelijking van overeenkomsten en de matching van personen. _Recognition_04_ verbetert de herkenning voor Inge schreven gebruikers, de gezichts hoezen (chirurgische maskers, N95 Maskers, doek maskers). U kunt nu veilige en naadloze gebruikers ervaringen bouwen die gebruikmaken van het nieuwste _detection_03_ model om te detecteren of een geregistreerde gebruiker een gezichts cover draagt en vervolgens herkent wie ze zijn met het meest recente _recognition_04_ model. Houd er rekening mee dat elk model onafhankelijk van de andere modellen werkt en dat een betrouwbaarheids drempel voor één model niet is bedoeld om te worden vergeleken in de andere herkennings modellen.

Lees in voor meer informatie over het opgeven van een geselecteerd model in verschillende gezichts bewerkingen, terwijl model conflicten worden voor komen. Als u een ervaren gebruiker bent en wilt bepalen of u moet overschakelen naar het meest recente model, gaat u naar de sectie [verschillende modellen evalueren](#evaluate-different-models) om het nieuwe model te evalueren en resultaten te vergelijken met de huidige gegevensset.


## <a name="prerequisites"></a>Vereisten

U moet bekend zijn met de concepten van detectie en identificatie van AI-gezicht. Als dat niet het geval is, raadpleegt u eerst deze hand leidingen:

* [Concepten van gezichts detectie](../concepts/face-detection.md)
* [Gezichtsherkenningsconcepten](../concepts/face-recognition.md)
* [Gezichten detecteren in een installatie kopie](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Gezichten met het opgegeven model detecteren

Met gezichts detectie worden de visuele bezienswaardigheden van menselijke gezichten geïdentificeerd en worden de locaties van het begrenzingsvak gevonden. Ook worden de functies van het gezicht geëxtraheerd en opgeslagen voor gebruik in identificatie. Al deze informatie vormt de representatie van één gezicht.

Het herkennings model wordt gebruikt wanneer de gezichts functies worden geëxtraheerd, zodat u een model versie kunt opgeven wanneer de detectie bewerking wordt uitgevoerd.

Wanneer u de [Face-detect] API gebruikt, wijst u de model versie toe met de `recognitionModel` para meter. De beschikbare waarden zijn:
* recognition_01
* recognition_02
* recognition_03
* recognition_04


Desgewenst kunt u de para meter _returnRecognitionModel_ (standaard **False**) opgeven om aan te geven of _recognitionModel_ als reactie moet worden geretourneerd. Daarom ziet een aanvraag-URL voor het [gezichts detectie] rest API er als volgt uit:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Als u de-client bibliotheek gebruikt, kunt u de waarde voor toewijzen `recognitionModel` door een teken reeks die de versie vertegenwoordigt door te geven. Als u deze niet hebt toegewezen, wordt een standaard model versie van `recognition_01` gebruikt. Zie het volgende code voorbeeld voor de .NET-client bibliotheek.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Gezichten identificeren met het opgegeven model

De face-service kan gezichts gegevens uit een afbeelding extra heren en deze koppelen aan een **persoons** object (bijvoorbeeld via de aanroep face-API [toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ), en meerdere **persoons** objecten kunnen samen worden opgeslagen in een **PersonGroup**. Vervolgens kan een nieuw gezicht worden vergeleken met een **PersonGroup** (met de aanroep voor het identificeren van het [gezicht] ) en kan de overeenkomende persoon binnen die groep worden geïdentificeerd.

Een **PersonGroup** moet één uniek herkennings model hebben voor alle **personen** en u kunt dit opgeven met behulp van de `recognitionModel` para meter bij het maken van de groep ([PersonGroup-Create] of [LargePersonGroup-Create]). Als u deze para meter niet opgeeft, wordt het oorspronkelijke `recognition_01` model gebruikt. Een groep maakt altijd gebruik van het herkennings model waarmee het is gemaakt, en nieuwe gezichten worden gekoppeld aan dit model wanneer ze eraan worden toegevoegd. Dit kan niet worden gewijzigd nadat het maken van een groep is gemaakt. Als u wilt zien welk model a **PersonGroup** is geconfigureerd met, gebruikt u de [PersonGroup-Get-] API met de para meter _returnRecognitionModel_ ingesteld op **True**.

Zie het volgende code voorbeeld voor de .NET-client bibliotheek.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

In deze code wordt een **PersonGroup** met id `mypersongroupid` gemaakt en ingesteld op het gebruik van het _recognition_02_ model om gezichts functies te extra heren.

U moet in dat geval opgeven welk model moet worden gebruikt bij het detecteren van gezichten om te vergelijken met deze **PersonGroup** (via de detectie-API voor het [gezichts punt] ). Het model dat u gebruikt, moet altijd consistent zijn met de configuratie van de **PersonGroup**. anders mislukt de bewerking vanwege incompatibele modellen.

Er is geen wijziging in de [Face-identify-] API. u hoeft alleen de model versie op te geven in de detectie.

## <a name="find-similar-faces-with-specified-model"></a>Vergelijk bare gezichten zoeken met het opgegeven model

U kunt ook een herkennings model opgeven voor zoek acties op vergelijk bare functies. U kunt de model versie toewijzen bij `recognitionModel` het maken van de lijst face met [FaceList-Create] API of [LargeFaceList-Create]. Als u deze para meter niet opgeeft, `recognition_01` wordt standaard het model gebruikt. In een gezichts lijst wordt altijd het herkennings model gebruikt dat is gemaakt met en er worden nieuwe gezichten gekoppeld aan dit model wanneer ze worden toegevoegd aan de lijst. u kunt dit niet wijzigen nadat het is gemaakt. Als u wilt zien in welk model een gezichts lijst is geconfigureerd, gebruikt u de [FaceList-Get-] API waarbij de para meter _returnRecognitionModel_ is ingesteld op **True**.

Zie het volgende code voorbeeld voor de .NET-client bibliotheek.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

Deze code maakt een lijst met gezichten `My face collection` met de naam, met behulp van het _recognition_04_ model voor het uitpakken van onderdelen. Wanneer u deze gezichts lijst zoekt naar een nieuw gedetecteerd gezicht, moet dat gezicht zijn gedetecteerd ([gezichts detectie]) met behulp van het _recognition_04_ model. Net als in de vorige sectie moet het model consistent zijn.

Er is geen wijziging in het [gezicht-zoek vergelijk bare] API. u geeft alleen de model versie op in detectie.

## <a name="verify-faces-with-specified-model"></a>Gezichten met het opgegeven model controleren

De [Face-verify-] API controleert of twee gezichten deel uitmaken van dezelfde persoon. Er is geen wijziging in de controle-API met betrekking tot herkennings modellen, maar u kunt alleen gezichten vergelijken die met hetzelfde model zijn gevonden.

## <a name="evaluate-different-models"></a>Verschillende modellen evalueren

Als u de prestaties van verschillende herkennings modellen wilt vergelijken met uw eigen gegevens, moet u het volgende doen:
1. Maak vier PersonGroups met behulp van _recognition_01_, _recognition_02_, _recognition_03_ en _recognition_04_ .
1. Gebruik uw afbeeldings gegevens om gezichten te detecteren en ze te registreren bij **personen** in deze vier **PersonGroup** s. 
1. Train uw PersonGroups met behulp van de PersonGroup-Train API.
1. Test met gezicht-identify op alle vier **PersonGroup** s en vergelijk de resultaten.


Als u normaal gesp roken een betrouwbaarheids drempel opgeeft (een waarde tussen nul en één die bepaalt hoe betrouwbaar het model moet zijn om een gezicht te identificeren), moet u mogelijk verschillende drempel waarden voor verschillende modellen gebruiken. Een drempel waarde voor één model is niet bedoeld om te worden gedeeld met een andere, en niet noodzakelijkerwijs dezelfde resultaten produceert.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het herkennings model kunt opgeven voor gebruik met verschillende face service-Api's. Volg vervolgens een Snelstartgids om aan de slag te gaan met gezichts detectie.

* [Gezichts-.NET-SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Gezichts python-SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Gezichts go-SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face - Detecteren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Gezicht-zoek vergelijk bare]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identificeren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face-verifiëren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-maken]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-maken]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc