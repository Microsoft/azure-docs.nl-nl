---
title: DTDL-modellen
titleSuffix: Azure Digital Twins
description: Begrijpen hoe Azure Digital Twins aangepaste modellen gebruikt om entiteiten in uw omgeving te beschrijven.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8942262c2e02670d57b1db324eb154dcc38f00f8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575391"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Meer informatie over dubbel-modellen in Azure Digital Twins

Een belangrijk kenmerk van Azure Digital Twins is de mogelijkheid om uw eigen vocabulaire te definiëren en uw tweelinggrafiek te bouwen in de zelf gedefinieerde termen van uw bedrijf. Deze mogelijkheid wordt geboden via door de gebruiker geleverde **modellen.** U kunt modellen zien als zelfstandige naamwoorden in een beschrijving van uw wereld. 

Een model is vergelijkbaar met een **klasse** in een objectgeoriënteerde programmeertaal, door een gegevensvorm te definiëren voor één bepaald concept in uw echte werkomgeving. Modellen hebben namen (zoals *Room* of *TemperatureSensor)* en bevatten elementen zoals eigenschappen, telemetrie/gebeurtenissen en opdrachten die beschrijven wat dit type entiteit in uw omgeving kan doen. Later gebruikt u deze modellen om digitale tweelingen [**te**](concepts-twins-graph.md) maken die specifieke entiteiten vertegenwoordigen die voldoen aan deze typebeschrijving.

Azure Digital Twins worden weergegeven in de op JSON LD gebaseerde **Digital Twin Definition Language (DTDL).**  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital Twin Definition Language (DTDL) voor modellen

Modellen voor Azure Digital Twins worden gedefinieerd met behulp van Digital Twins Definition Language (DTDL). 

U kunt de volledige taalspecificaties voor DTDL bekijken in GitHub: [**Digital Twins Definition Language (DTDL) - versie 2.**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)

DTDL is gebaseerd op JSON-LD en is computertaalonafhankelijk. DTDL is niet exclusief voor Azure Digital Twins, maar wordt ook gebruikt om apparaatgegevens weer te geven in andere IoT-services, [zoals IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md). Azure Digital Twins DTDL versie **2** gebruikt (het gebruik van DTDL versie 1 met Azure Digital Twins is nu afgeschaft). 

In de rest van dit artikel wordt samengevat hoe de taal wordt gebruikt in Azure Digital Twins.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>details Azure Digital Twins DTDL-implementatie

Niet alle services die gebruikmaken van DTDL implementeren exact dezelfde functies van DTDL. Zo gebruikt IoT Plug en Play niet de DTDL-functies die voor grafieken zijn, terwijl Azure Digital Twins momenteel geen DTDL-opdrachten implementeert. 

Een DTDL-model kan alleen compatibel zijn met Azure Digital Twins als het voldoet aan de volgende vereisten:

* Alle DTDL-elementen op het hoogste niveau in een model moeten van het type *interface zijn.* Dit komt door Azure Digital Twins model-API's JSON-objecten kunnen ontvangen die een interface of een matrix van interfaces vertegenwoordigen. Als gevolg hiervan zijn er geen andere DTDL-elementtypen toegestaan op het hoogste niveau.
* DTDL voor Azure Digital Twins mag geen opdrachten *definiëren.*
* Azure Digital Twins staat slechts één niveau van nesten van onderdelen toe. Dit betekent dat een interface die wordt gebruikt als onderdeel, zelf geen onderdelen kan hebben. 
* Interfaces kunnen niet inline worden gedefinieerd binnen andere DTDL-interfaces; Ze moeten worden gedefinieerd als afzonderlijke entiteiten op het hoogste niveau met hun eigen ID's. Wanneer een andere interface die interface vervolgens wil opnemen als onderdeel of overname, kan deze verwijzen naar de id.

Azure Digital Twins ziet ook niet het kenmerk `writable` op eigenschappen of relaties. Hoewel dit kan worden ingesteld volgens DTDL-specificaties, wordt de waarde niet gebruikt door Azure Digital Twins. In plaats daarvan worden deze altijd behandeld als beschrijfbaar door externe clients met algemene schrijfmachtigingen voor de Azure Digital Twins service.

## <a name="elements-of-a-model"></a>Elementen van een model

Binnen een modeldefinitie is het code-item op het hoogste niveau een **interface**. Hiermee wordt het volledige model ingekapseld en wordt de rest van het model gedefinieerd in de interface. 

Een DTDL-modelinterface kan nul, één of veel van de volgende velden bevatten:
* **Eigenschap:** eigenschappen zijn gegevensvelden die de status van een entiteit vertegenwoordigen (zoals de eigenschappen in veel objectgeoriënteerde programmeertalen). Eigenschappen hebben een back-opslag en kunnen op elk moment worden gelezen.
* **Telemetrie:** telemetrievelden vertegenwoordigen metingen of gebeurtenissen en worden vaak gebruikt om de metingen van apparaatsensoren te beschrijven. In tegenstelling tot eigenschappen wordt telemetrie niet opgeslagen op een digitale tweeling; Het is een reeks tijdsgebonden gegevensgebeurtenissen die moeten worden verwerkt wanneer ze plaatsvinden. Zie de sectie Eigenschappen versus [*telemetrie*](#properties-vs-telemetry) hieronder voor meer informatie over de verschillen tussen eigenschap en telemetrie.
* **Onderdeel:** met onderdelen kunt u uw modelinterface bouwen als een assembly van andere interfaces, als u wilt. Een voorbeeld van een onderdeel is een *frontCamera-interface* (en een andere *componentinterface backCamera*) die worden gebruikt bij het definiëren van een model voor een *telefoon.* U moet eerst een interface voor *frontCamera* definiëren alsof het een eigen model is en vervolgens kunt u hier naar verwijzen bij het definiëren van *Telefoon.*

    Gebruik een onderdeel om iets te beschrijven dat een integraal onderdeel van uw oplossing is, maar geen afzonderlijke identiteit nodig heeft en niet afzonderlijk in de tweelinggrafiek hoeft te worden gemaakt, verwijderd of opnieuw hoeft te worden gesrangeerd. Als u wilt dat entiteiten onafhankelijke bestaan in de tweelinggrafiek hebben, geeft u deze weer als afzonderlijke digitale tweelingen van verschillende modellen, verbonden door relaties *(zie* volgende opsommingsteken).
    
    >[!TIP] 
    >Onderdelen kunnen ook worden gebruikt voor de organisatie, om sets gerelateerde eigenschappen binnen een modelinterface te groepen. In dit geval kunt u elk onderdeel zien als een naamruimte of map in de interface.
* **Relatie:** met relaties kunt u laten zien hoe een digitale tweeling kan worden betrokken bij andere digitale tweelingen. Relaties kunnen verschillende semantische betekeniss vertegenwoordigen, zoals *contains* ('floor contains room'), *cools* ('hvac cools room'), *isBilledTo* ('floor contains room'), enzovoort. Met relaties kan de oplossing een grafiek van onderling gerelateerde entiteiten bieden.

> [!NOTE]
> De [specificatie voor DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definieert ook Opdrachten **.** Dit zijn methoden die kunnen worden uitgevoerd op een digitale tweeling (zoals een opdracht voor opnieuw instellen of een opdracht om een ventilator in of uit te schakelen). Opdrachten *worden momenteel echter niet ondersteund in Azure Digital Twins.*

### <a name="properties-vs-telemetry"></a>Eigenschappen versus telemetrie

Hier zijn enkele aanvullende richtlijnen voor  het onderscheiden van DTDL-eigenschapsvelden en **telemetrievelden** in Azure Digital Twins.

Het verschil tussen eigenschappen en telemetrie voor Azure Digital Twins modellen is als volgt:
* **Eigenschappen** hebben naar verwachting back-opslag. Dit betekent dat u een eigenschap op elk moment kunt lezen en de waarde ervan kunt ophalen. Als de eigenschap schrijfbaar is, kunt u ook een waarde opslaan in de eigenschap .  
* **Telemetrie** lijkt meer op een stroom gebeurtenissen; Het is een set gegevensberichten met een korte levensduur. Als u luisteren naar de gebeurtenis en acties die moeten worden ondernomen wanneer deze zich voor doen niet in stelt, is er op een later tijdstip geen trace van de gebeurtenis. U kunt er niet meer naar teruggaan en het later lezen. 
  - In C#-termen lijkt telemetrie op een C#-gebeurtenis. 
  - In IoT-termen is telemetrie doorgaans één meting die door een apparaat wordt verzonden.

**Telemetrie wordt** vaak gebruikt met IoT-apparaten, omdat veel apparaten niet geschikt zijn voor of geïnteresseerd zijn in het opslaan van de meetwaarden die ze genereren. Ze verzenden ze alleen als een stroom telemetriegebeurtenissen. In dit geval kunt u op het apparaat op geen enkel moment de meest recente waarde van het telemetrieveld opvragen. In plaats daarvan moet u luisteren naar de berichten van het apparaat en actie ondernemen wanneer de berichten binnenkomen. 

Als gevolg hiervan gebruikt u bij het ontwerpen van een model in Azure Digital Twins waarschijnlijk eigenschappen **in** de meeste gevallen om uw tweelingen te modelleren. Hierdoor hebt u de back-opslag en de mogelijkheid om de gegevensvelden te lezen en er query's op uit te voeren.

Telemetrie en eigenschappen werken vaak samen om gegevens van apparaten te verwerken. Omdat alle ingressen naar Azure Digital Twins via API's [zijn,](how-to-use-apis-sdks.md)gebruikt u doorgaans uw ingress-functie om telemetrie- of eigenschapsgebeurtenissen van apparaten te lezen en een eigenschap in te stellen in Azure Digital Twins reactie. 

U kunt ook een telemetriegebeurtenis publiceren vanuit de Azure Digital Twins API. Net als bij andere telemetrie is dat een korte gebeurtenis die een listener moet verwerken.

## <a name="model-inheritance"></a>Model overname

Soms wilt u misschien een model verder specialiseren. Het kan bijvoorbeeld handig zijn om een algemeen model *Room* en gespecialiseerde varianten *ConferenceRoom* enLeer te *hebben.* Om specialisatie uit te drukken, ondersteunt DTDL overname: interfaces kunnen overnemen van een of meer andere interfaces. 

In het volgende voorbeeld wordt het planetaire *model* uit het eerdere DTDL-voorbeeld opnieuw voorgesteld als een subtype van een groter *Model Voorbody.* Het bovenliggende model wordt eerst gedefinieerd en vervolgens bouwt het onderliggende model daarop voort met behulp van het veld `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

In dit voorbeeld draagt *MoetBody* een naam, een massa en een temperatuur bij aan *Planet*. De sectie is een interfacenaam of een matrix van interfacenamen (zodat de uitbreidbare interface kan worden overgenomen van meerdere `extends` bovenliggende modellen, indien gewenst).

Zodra overname is toegepast, worden alle eigenschappen van de gehele overnameketen door de uitbreidingsinterface gebruikt.

De uitbreidingsinterface kan geen van de definities van de bovenliggende interfaces wijzigen; het kan er alleen aan toevoegen. Het kan ook een mogelijkheid die al is gedefinieerd in een van de bovenliggende interfaces niet opnieuw definiëren (zelfs niet als de mogelijkheden zijn gedefinieerd als dezelfde). Als een bovenliggende interface bijvoorbeeld een eigenschaps massa definieert, kan de uitbreidbare interface geen declaratie van massa bevatten, zelfs niet als het ook `double` een   `double` is.

## <a name="model-code"></a>Modelcode

Dubbeltypemodellen kunnen in elke teksteditor worden geschreven. De DTDL-taal volgt de JSON-syntaxis, dus u moet modellen opslaan met de extensie *.json.* Als u de JSON-extensie gebruikt, kunnen veel teksteditors voor programmeren basissyntaxis controleren en markeren voor uw DTDL-documenten. Er is ook een [DTDL-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) beschikbaar [voor Visual Studio Code](https://code.visualstudio.com/).

### <a name="possible-schemas"></a>Mogelijke schema's

Volgens DTDL kan het  schema voor eigenschaps- en *telemetriekenmerken* bestaan uit standaard primitieve typen, , , en , en andere `integer` `double` `string` `Boolean` typen, zoals `DateTime` en `Duration` . 

Naast primitieve typen kunnen de *velden Eigenschap* en *Telemetrie* de volgende complexe typen hebben:
* `Object`
* `Map`
* `Enum`

*Telemetrievelden* bieden ook ondersteuning `Array` voor .

### <a name="example-model"></a>Voorbeeldmodel

Deze sectie bevat een voorbeeld van een typisch model, geschreven als een DTDL-interface. Het model beschrijft **planeten**, elk met een naam, een massa en een temperatuur.
 
Houd er rekening mee dat planeten ook kunnen communiceren **met manen** die hun satellieten zijn en kunnen **kunnen bestaan** uit . In het onderstaande voorbeeld geeft het model verbindingen met deze andere entiteiten weer door te verwijzen naar twee `Planet` externe modellen: `Moon` en `Crater` . Deze modellen worden ook gedefinieerd in de onderstaande voorbeeldcode, maar worden zeer eenvoudig gehouden, zodat dit niet ten koste gaat van het primaire `Planet` voorbeeld.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

De velden van het model zijn:

| Veld | Description |
| --- | --- |
| `@id` | Een id voor het model. Moet de notatie `dtmi:<domain>:<unique model identifier>;<model version number>` hebben. |
| `@type` | Identificeert het soort informatie dat wordt beschreven. Voor een interface is het type *Interface*. |
| `@context` | Hiermee stelt u [de context](https://niem.github.io/json/reference/json-ld/context/) voor het JSON-document in. Modellen moeten gebruikmaken van `dtmi:dtdl:context;2` . |
| `displayName` | [optioneel] Hiermee kunt u het model desgewenst een gebruiksvriendelijke naam geven. |
| `contents` | Alle resterende interfacegegevens worden hier geplaatst als een matrix met kenmerkdefinities. Elk kenmerk moet een ( Eigenschap `@type` , *Telemetrie,* *Opdracht*, *Relatie* of *Onderdeel*) bevatten om het soort interface-informatie te identificeren dat wordt beschreven, en vervolgens een set eigenschappen die het werkelijke kenmerk definiëren (bijvoorbeeld en om een eigenschap te `name` `schema` definiëren).  |

> [!NOTE]
> Houd er rekening mee dat de interface van het onderdeel (In dit *voorbeeld)* is gedefinieerd in dezelfde matrix als de interface die deze gebruikt (*Planet*). Onderdelen moeten op deze manier worden gedefinieerd in API-aanroepen om de interface te kunnen vinden.

## <a name="best-practices-for-designing-models"></a>Best practices voor het ontwerpen van modellen

Bij het ontwerpen van modellen die de entiteiten in uw omgeving weerspiegelen, kan het handig zijn om vooruit te kijken en na te denken over de gevolgen van [uw](concepts-query-language.md) ontwerp voor query's. Mogelijk wilt u eigenschappen zo ontwerpen dat grote resultatensets worden voorkomen door het doorkruisen van grafen. U kunt ook relaties modelleren die in één query moeten worden beantwoord als relaties met één niveau.

### <a name="validating-models"></a>Modellen valideren

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Hulpprogramma's voor modellen 

Er zijn verschillende voorbeelden beschikbaar om het nog gemakkelijker te maken om te gaan met modellen en ontologieën. Ze bevinden zich in deze opslagplaats: [Tools for Digital Twins Definition Language (DTDL).](https://github.com/Azure/opendigitaltwins-tools)

In deze sectie wordt de huidige set voorbeelden gedetailleerder beschreven.

### <a name="model-uploader"></a>Modeluploader 

_**Voor het uploaden van modellen naar Azure Digital Twins**_

Wanneer u klaar bent met het maken, uitbreiden of selecteren van uw modellen, kunt u ze uploaden naar uw Azure Digital Twins-exemplaar om ze beschikbaar te maken voor gebruik in uw oplossing. Dit wordt gedaan met behulp [van Azure Digital Twins API's](how-to-use-apis-sdks.md), zoals beschreven in [*How-to: Manage DTDL models (DTDL-modellen beheren).*](how-to-manage-model.md#upload-models)

Als u echter veel modellen moet uploaden, of als ze veel onderlinge afhankelijkheden hebben die het ordenen van afzonderlijke uploads gecompliceerd maken, kunt u dit voorbeeld gebruiken om veel modellen tegelijk te uploaden: [**Azure Digital Twins Model Uploader**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Volg de instructies in het voorbeeld om dit project te configureren en te gebruiken om modellen te uploaden naar uw eigen exemplaar.

### <a name="model-visualizer"></a>Modelvisualiseerder 

_**Voor het visualiseren van modellen**_

Zodra u modellen hebt geüpload naar uw Azure Digital Twins-exemplaar, kunt u de modellen in uw Azure Digital Twins-exemplaar weergeven, inclusief overname- en modelrelaties, met behulp van [**Azure Digital Twins Model Visualizer**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Dit voorbeeld heeft momenteel de status Concept. We raden de community voor digital twins-ontwikkeling aan om het voorbeeld uit te breiden en hieraan bij te dragen. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van modellen op basis van ontologieën die voldoen aan de industriestandaard: [ *Concepten: Wat is een ontologie?*](concepts-ontologies.md)

* Meer informatie over het beheren van modellen met [ *API-bewerkingen: How-to: Manage DTDL models (DTDL-modellen beheren)*](how-to-manage-model.md)

* Meer informatie over hoe modellen worden gebruikt om digitale tweelingen te maken: [ *Concepten: Digitale tweelingen en de tweelinggrafiek*](concepts-twins-graph.md)

