---
title: DTDL-modellen beheren
titleSuffix: Azure Digital Twins
description: Zie een model maken, bewerken en verwijderen in azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: be8f4ba8552521d25992d7c3a405dc2628fbbb85
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560815"
---
# <a name="manage-azure-digital-twins-models"></a>Azure Digital Apparaatdubbels-modellen beheren

U kunt de [modellen](concepts-models.md) beheren die uw Azure Digital apparaatdubbels-exemplaar kent over het gebruik van de [**DigitalTwinModels-api's**](/rest/api/digital-twins/dataplane/models), de [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md). 

Beheer bewerkingen zijn onder andere uploaden, valideren, ophalen en verwijderen van modellen. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>Manieren om modellen te beheren

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>Modellen maken

Modellen voor Azure Digital Apparaatdubbels zijn geschreven in DTDL en opgeslagen als *JSON* -bestanden. Er is ook een [DTDL-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) beschikbaar voor [Visual Studio code](https://code.visualstudio.com/), die syntaxis validatie en andere functies biedt om het schrijven van DTDL-documenten te vergemakkelijken.

Bekijk een voor beeld waarin een zieken huis hun kamers digitaal wil vertegenwoordigen. Elke ruimte bevat een slimme Zeep-dispenser voor het bewaken van de hand wassen en Sens oren om het verkeer via de ruimte te bewaken.

De eerste stap bij de oplossing is het maken van modellen om aspecten van het zieken huis weer te geven. In dit scenario kan een patiënt kamer als volgt worden beschreven:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Dit is een voorbeeld hoofdtekst voor een JSON-bestand waarin een model is gedefinieerd en opgeslagen om te worden geüpload als onderdeel van een client project. De REST API roept daarentegen een matrix met model definities aan zoals de hierboven beschreven (die is toegewezen aan een `IEnumerable<string>` in de .NET SDK). Als u dit model direct wilt gebruiken in de REST API, plaatst u het tussen vier Kante haken.

Dit model definieert een naam en een unieke ID voor de patiënt kamer en eigenschappen om het aantal bezoekers en de status van de hand te geven (deze tellers worden bijgewerkt op basis van bewegings sensoren en slimme zeep-dispensers en worden gebruikt om een eigenschap van het *handwash percentage* te berekenen). Het model definieert ook een relatie *hasDevices*, die wordt gebruikt voor het verbinden van een [digitale apparaatdubbels](concepts-twins-graph.md) op basis van dit *room* -model op de werkelijke apparaten.

U kunt aan de slag met deze methode om modellen te definiëren voor de weers huizen, zones of het zieken huis zelf.

### <a name="validate-syntax"></a>Syntaxis valideren

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Modellen uploaden

Zodra de modellen zijn gemaakt, kunt u deze uploaden naar het Azure Digital Apparaatdubbels-exemplaar.

Wanneer u klaar bent voor het uploaden van een model, kunt u het volgende code fragment gebruiken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Houd er rekening mee dat de `CreateModels` methode meerdere bestanden in één trans actie accepteert. Hier volgt een voor beeld om te illustreren:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Model bestanden kunnen meer dan één model bevatten. In dit geval moeten de modellen in een JSON-matrix worden geplaatst. Bijvoorbeeld:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

Bij het uploaden worden model bestanden gevalideerd door de service.

## <a name="retrieve-models"></a>Modellen ophalen

U kunt modellen weer geven en ophalen die zijn opgeslagen in uw Azure Digital Apparaatdubbels-exemplaar. 

Hier zijn uw opties voor het volgende:
* Eén model ophalen
* Alle modellen ophalen
* Meta gegevens en afhankelijkheden voor modellen ophalen

Hier volgen enkele voor beelden van aanroepen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

De API roept alle retour objecten op om modellen op te halen `DigitalTwinsModelData` . `DigitalTwinsModelData` bevat meta gegevens over het model dat is opgeslagen in het Azure Digital Apparaatdubbels-exemplaar, zoals de naam, de DTMI en de aanmaak datum van het model. Het `DigitalTwinsModelData` object bevat ook optioneel het model zelf. Afhankelijk van de para meters kunt u de opgehaalde aanroepen gebruiken om alleen meta gegevens op te halen (wat handig is in scenario's waarin u een lijst met beschik bare hulpprogram ma's voor de gebruikers interface wilt weer geven) of het hele model.

De `RetrieveModelWithDependencies` aanroep retourneert niet alleen het aangevraagde model, maar ook alle modellen waarvan het aangevraagde model afhankelijk is.

Modellen zijn niet per se geretourneerd in het document formulier waarin ze zijn geüpload. Azure Digital Apparaatdubbels garandeert alleen dat het retour formulier semantisch gelijkwaardig is. 

## <a name="update-models"></a>Modellen bijwerken

Zodra een model is geüpload naar uw Azure Digital Apparaatdubbels-exemplaar, is de gehele model interface onveranderbaar. Dit betekent dat er geen traditionele ' editing ' van modellen is. Azure Digital Apparaatdubbels staat ook niet toe om hetzelfde model opnieuw te uploaden.

Als u in plaats daarvan wijzigingen wilt aanbrengen in een model, zoals bijwerken `displayName` of `description` , kunt u dit doen door een **nieuwere versie** van het model te uploaden. 

### <a name="model-versioning"></a>Versiebeheer model

Begin met het DTDL van het oorspronkelijke model om een nieuwe versie van een bestaand model te maken. Werk de velden die u wilt wijzigen bij, voeg ze toe of verwijder deze.

Markeer deze vervolgens als een nieuwere versie van het model door het `id` veld van het model bij te werken. De laatste sectie van de model-ID, na de `;` , vertegenwoordigt het model nummer. Om aan te geven dat dit nu een meer bijgewerkte versie van dit model is, verhoogt u het aantal aan het einde van de `id` waarde naar een getal dat groter is dan het huidige versie nummer.

Als uw vorige model-ID bijvoorbeeld er als volgt uitziet:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

versie 2 van dit model kan er als volgt uitzien:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Upload vervolgens de nieuwe versie van het model naar uw exemplaar. 

Deze versie van het model is vervolgens beschikbaar in uw-exemplaar om te gebruiken voor Digital apparaatdubbels. Eerdere versies van het model worden **niet** overschreven, waardoor er meerdere versies van het model in uw exemplaar bestaan, totdat u [deze verwijdert](#remove-models).

### <a name="impact-on-twins"></a>Impact op apparaatdubbels

Wanneer u een nieuwe twee maakt, omdat de nieuwe model versie en de oude model versie naast elkaar bestaan, kan de nieuwe twee de nieuwe versie van het model of de oudere versie gebruiken.

Dit betekent ook dat het uploaden van een nieuwe versie van een model niet automatisch invloed heeft op bestaande apparaatdubbels. De bestaande apparaatdubbels blijft gewoon instanties van de oude model versie.

U kunt deze bestaande apparaatdubbels bijwerken naar de nieuwe versie van het model door deze te patchen, zoals wordt beschreven in het gedeelte [*een Digital*](how-to-manage-twin.md#update-a-digital-twins-model) - *navolgende model bijwerken van How-to: Manage Digital apparaatdubbels*. Binnen dezelfde patch moet u de **model-id** (naar de nieuwe versie) bijwerken en **alle velden die moeten worden gewijzigd op de dubbele waarde zodat deze overeenkomt met het nieuwe model**.

## <a name="remove-models"></a>Modellen verwijderen

Modellen kunnen ook worden verwijderd uit de service, op een van de volgende twee manieren:
* **Buiten gebruik stellen** : zodra een model uit bedrijf is genomen, kunt u het niet meer gebruiken om nieuwe digitale apparaatdubbels te maken. Bestaande digitale apparaatdubbels die al gebruikmaken van dit model, worden niet beïnvloed, zodat u ze nog steeds kunt bijwerken met dingen als eigenschaps wijzigingen en het toevoegen of verwijderen van relaties.
* **Verwijdering** : Hiermee wordt het model volledig verwijderd uit de oplossing. Alle apparaatdubbels die gebruikmaken van dit model, zijn niet meer gekoppeld aan een geldig model, zodat ze worden behandeld alsof ze helemaal geen model hebben. U kunt deze apparaatdubbels nog steeds lezen, maar kunt er geen updates op uitvoeren voordat ze opnieuw worden toegewezen aan een ander model.

Dit zijn afzonderlijke functies en ze zijn niet van invloed op elkaar, maar ze kunnen samen worden gebruikt om een model geleidelijk te verwijderen. 

### <a name="decommissioning"></a>Uit bedrijf nemen

Dit is de code voor het buiten gebruik stellen van een model:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

De status van de buiten gebruik stellen van een model is opgenomen in de records die worden `ModelData` geretourneerd door de api's voor het ophalen van het model.

### <a name="deletion"></a>Verwijdering

U kunt alle modellen in uw exemplaar in één keer verwijderen, of u kunt dit op afzonderlijke manieren doen.

Voor een voor beeld van het verwijderen van alle modellen downloadt u de voor beeld-app die wordt gebruikt in de [*zelf studie: Verken de basis principes met een voor beeld-client-app*](tutorial-command-line-app.md). Het *CommandLoop.cs* -bestand doet dit in een `CommandDeleteAllModels` functie.

In de rest van deze sectie wordt de verwijdering van een model dichter bij de details weer gegeven en wordt getoond hoe u dit kunt doen voor een afzonderlijk model.

#### <a name="before-deletion-deletion-requirements"></a>Voor verwijdering: vereisten voor verwijderen

Over het algemeen kunnen modellen op elk gewenst moment worden verwijderd.

De uitzonde ring is modellen waarvan andere modellen afhankelijk zijn, hetzij met een `extends` relatie of als een onderdeel. Als een *ConferenceRoom* -model bijvoorbeeld een *room* -model uitbreidt en een *ACUnit* -model als onderdeel heeft, kunt u geen *ruimte* of *ACUnit* verwijderen totdat *ConferenceRoom* die respectieve verwijzingen verwijdert. 

U kunt dit doen door het afhankelijke model bij te werken om de afhankelijkheden te verwijderen, of door het afhankelijke model volledig te verwijderen.

#### <a name="during-deletion-deletion-process"></a>Tijdens verwijderen: verwijderings proces

Zelfs als een model voldoet aan de vereisten om het direct te verwijderen, kunt u eerst enkele stappen door lopen om onbedoelde gevolgen voor de apparaatdubbels te voor komen. Hier volgen enkele stappen die u kunnen helpen bij het beheren van het proces:
1. Buiten gebruik stellen van het model
2. Wacht een paar minuten om er zeker van te zijn dat de service de laatste aanvragen voor twee keer maken heeft verwerkt die zijn verzonden vóór de buiten gebruik stellen
3. Query apparaatdubbels op model om alle apparaatdubbels te zien die het nu-buiten gebruik gestelde model gebruiken
4. Verwijder de apparaatdubbels als u deze niet meer nodig hebt of patch ze naar een nieuw model, indien nodig. U kunt er ook voor kiezen om ze alleen te laten staan, in welk geval ze worden apparaatdubbels zonder modellen wanneer het model is verwijderd. Zie de volgende sectie voor de implicaties van deze status.
5. Wacht een paar minuten om er zeker van te zijn dat de wijzigingen percolated zijn.
6. Het model verwijderen 

Als u een model wilt verwijderen, gebruikt u deze aanroep:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>Na verwijdering: Apparaatdubbels zonder modellen

Zodra een model is verwijderd, worden alle digitale apparaatdubbels die gebruikmaken van het model, nu beschouwd als zonder model. Houd er rekening mee dat er geen query is waarmee u een lijst met alle apparaatdubbels in deze status kunt geven. u *kunt* echter wel een query uitvoeren op de apparaatdubbels door het verwijderde model om te weten welke apparaatdubbels van toepassing is.

Hier volgt een overzicht van wat u wel en niet kunt doen met apparaatdubbels die geen model hebben.

Dingen die u **kunt** doen:
* Query uitvoeren op de dubbele
* Leeseigenschappen
* Uitgaande relaties lezen
* Inkomende relaties toevoegen en verwijderen (zoals in kan andere apparaatdubbels nog steeds relaties *met* dit dubbele formulier vormen)
  - De `target` in de definitie van de relatie kan nog steeds de DTMI van het verwijderde model weer spie gelen. Een relatie zonder gedefinieerd doel kan ook hier worden gebruikt.
* Relaties verwijderen
* Verwijder de dubbele

Wat u **niet kunt** doen:
* Uitgaande relaties (zoals in, relaties *van* deze twee tot andere apparaatdubbels) bewerken
* Eigenschappen bewerken

#### <a name="after-deletion-re-uploading-a-model"></a>Na verwijdering: een model opnieuw uploaden

Nadat een model is verwijderd, kunt u later besluiten een nieuw model te uploaden met dezelfde ID als die u hebt verwijderd. Dit is wat er gebeurt in dat geval.
* In het perspectief van het oplossings archief is dit hetzelfde als het uploaden van een volledig nieuw model. De service is niet meer weet dat de oude is geüpload.   
* Als er resterende apparaatdubbels zijn in de grafiek die verwijst naar het verwijderde model, worden deze niet meer zwevend. Deze model-ID is opnieuw geldig met de nieuwe definitie. Als de nieuwe definitie voor het model echter anders is dan de model definitie die is verwijderd, kunnen deze apparaatdubbels eigenschappen en relaties hebben die overeenkomen met de verwijderde definitie en die niet geldig zijn voor de nieuwe.

Deze status wordt niet door Azure Digital Apparaatdubbels voor komen. Zorg er daarom voor dat u de apparaatdubbels op juiste wijze patcht om ervoor te zorgen dat ze geldig blijven via de model definitie-switch.

## <a name="next-steps"></a>Volgende stappen

Zie digitale apparaatdubbels maken en beheren op basis van uw modellen:
* [*Instructies: digitale apparaatdubbels beheren*](how-to-manage-twin.md)