---
title: DTDL modellen
titleSuffix: Azure Digital Twins
description: Krijg inzicht in de manier waarop Azure Digital Apparaatdubbels gebruikmaakt van aangepaste modellen om entiteiten in uw omgeving te beschrijven.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d3570a22fdd935237e673ea3e43ab5e463b66456
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590531"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Meer informatie over dubbel-modellen in Azure Digital Twins

Een belang rijk kenmerk van Azure Digital Apparaatdubbels is de mogelijkheid om uw eigen woorden lijst te definiëren en uw dubbele grafiek te bouwen in de zelfgedefinieerde voor waarden van uw bedrijf. Deze mogelijkheid wordt gegeven via door de gebruiker verschafte **modellen**. U kunt modellen beschouwen als de zelfstandige naam woorden in een beschrijving van uw wereld. 

Een model is vergelijkbaar met een **klasse** in een object georiënteerde programmeer taal en definieert een gegevensshape voor een bepaald concept in uw echte werk omgeving. Modellen hebben namen (zoals *room* of *temperatuur sensor*) en bevatten elementen zoals eigenschappen, telemetrie/gebeurtenissen en opdrachten die beschrijven wat dit type entiteit in uw omgeving kan doen. Later gaat u deze modellen gebruiken om [**digitale apparaatdubbels**](concepts-twins-graph.md) te maken die specifieke entiteiten vertegenwoordigen die aan deze type beschrijving voldoen.

Azure Digital Apparaatdubbels-modellen worden weer gegeven in de JSON-LD-based **Digital-definitie taal (DTDL)**.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital-dubbele-definitie taal (DTDL) voor modellen

Modellen voor Azure Digital Apparaatdubbels worden gedefinieerd met behulp van de Digital Apparaatdubbels Definition Language (DTDL). 

U kunt de volledige taal specificaties voor DTDL bekijken in GitHub: [**Digital Apparaatdubbels Definition Language (DTDL)-versie 2**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

DTDL is gebaseerd op JSON-LD en is computertaalonafhankelijk. DTDL is niet exclusief voor Azure Digital Apparaatdubbels, maar wordt ook gebruikt voor het weer geven van apparaatgegevens in andere IoT-Services, zoals [IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md). Azure Digital Apparaatdubbels maakt gebruik van DTDL **versie 2** (gebruik van DTDL versie 1 met Azure Digital apparaatdubbels is nu afgeschaft). 

De rest van dit artikel geeft een overzicht van de manier waarop de taal wordt gebruikt in azure Digital Apparaatdubbels.

> [!NOTE] 
> Niet alle services die gebruikmaken van DTDL, implementeren exact dezelfde functies van DTDL. IoT Plug en Play maakt bijvoorbeeld geen gebruik van de DTDL-functies die voor grafieken gelden, terwijl Azure Digital Apparaatdubbels momenteel geen DTDL-opdrachten implementeert.
>
> Voor meer informatie over de DTDL-functies die specifiek zijn voor Azure Digital Apparaatdubbels raadpleegt u de sectie verderop in dit artikel over de [Implementatie Details van Azure Digital APPARAATDUBBELS DTDL](#azure-digital-twins-dtdl-implementation-specifics).

## <a name="elements-of-a-model"></a>Elementen van een model

Binnen een model definitie is het code-item op het hoogste niveau een **Interface**. Hiermee wordt het volledige model ingekapseld en wordt de rest van het model gedefinieerd in de interface. 

Een DTDL-model interface kan nul, één of veel van de volgende velden bevatten:
* **Eigenschap** -eigenschappen zijn gegevens velden die de status van een entiteit vertegenwoordigen (zoals de eigenschappen in veel object-georiënteerde programmeer talen). Eigenschappen hebben een back-up van de opslag en kunnen op elk gewenst moment worden gelezen.
* **Telemetrie** -telemetrie-velden vertegenwoordigen metingen of gebeurtenissen en worden vaak gebruikt om de leesingen van de sensor te beschrijven. In tegens telling tot eigenschappen wordt telemetrie niet opgeslagen op een digitale dubbele; het is een reeks tijdgebonden gegevens gebeurtenissen die moeten worden verwerkt wanneer deze zich voordoen. Zie de sectie [*Eigenschappen versus telemetrie*](#properties-vs-telemetry) hieronder voor meer informatie over de verschillen tussen eigenschappen en telemetrie.
* **Onderdeel** -onderdelen bieden u de mogelijkheid om uw model interface als een assembly van andere interfaces te maken, als u dat wilt. Een voor beeld van een component is een *frontCamera* -interface (en een andere onderdeel interface *backCamera*) die worden gebruikt voor het definiëren van een model voor een *telefoon*. U moet eerst een interface voor *frontCamera* definiëren alsof het een eigen model is, en vervolgens kunt u hiernaar verwijzen bij het definiëren van de *telefoon*.

    Gebruik een onderdeel om iets te beschrijven dat een integraal onderdeel is van uw oplossing, maar waarvoor geen afzonderlijke identiteit nodig is, en dat u niet afzonderlijk hoeft te maken, verwijderen of opnieuw wilt rangschikken in het dubbele diagram. Als u wilt dat entiteiten onafhankelijk van elkaar aanwezig zijn in de dubbele grafiek, vertegenwoordigen ze als afzonderlijke digitale apparaatdubbels van verschillende modellen, verbonden door *relaties* (zie volgende opsommings teken).
    
    >[!TIP] 
    >Onderdelen kunnen ook worden gebruikt voor organisaties om sets van gerelateerde eigenschappen binnen een model interface te groeperen. In dit geval kunt u elk onderdeel beschouwen als een naam ruimte of ' folder ' in de-interface.
* **Relatie** -relaties bieden u de mogelijkheid om te zien hoe een digitale twee kunnen worden betrokken bij andere digitale apparaatdubbels. Relaties kunnen verschillende semantische betekenissen vertegenwoordigen, zoals *contains* ("vloer bevat room"), *koelen* ("HVAC Cool Room"), *isBilledTo* ("compressor" wordt gefactureerd voor gebruiker "), enzovoort. Met relaties kan de oplossing een grafiek van gerelateerde entiteiten bieden.

> [!NOTE]
> De [specificatie voor DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definieert ook **opdrachten**. Dit zijn methoden die kunnen worden uitgevoerd op een digitale twee (zoals een reset-opdracht of een opdracht om een ventilator in of uit te scha kelen). *Opdrachten worden momenteel echter niet ondersteund in azure Digital apparaatdubbels.*

### <a name="properties-vs-telemetry"></a>Eigenschappen versus telemetrie

Hier volgt een aantal aanvullende richt lijnen voor het onderscheiden van DTDL- **Eigenschappen** en **telemetrie** -velden in azure Digital apparaatdubbels.

Het verschil tussen eigenschappen en telemetrie voor Azure Digital Apparaatdubbels-modellen is als volgt:
* Er wordt naar verwachting van de **Eigenschappen** een back-up van de opslag. Dit betekent dat u op elk gewenst moment een eigenschap kunt lezen en de waarde ervan ophaalt. Als de eigenschap schrijfbaar is, kunt u ook een waarde opslaan in de-eigenschap.  
* **Telemetrie** lijkt meer op een stroom aan gebeurtenissen; het is een set gegevens berichten met korte levens duur. Als u Luis teren niet hebt ingesteld voor de gebeurtenis en de acties die moeten worden uitgevoerd wanneer deze zich voordoen, is het niet mogelijk om de gebeurtenis op een later tijdstip te traceren. U kunt er niet meer op terugkeren en deze later lezen. 
  - In C#-termen is telemetrie vergelijkbaar met een C#-gebeurtenis. 
  - In IoT-termen is telemetrie meestal één meting die door een apparaat wordt verzonden.

**Telemetrie** wordt vaak gebruikt met IOT-apparaten, omdat veel apparaten niet geschikt zijn voor of geïnteresseerd zijn in het opslaan van de meet waarden die ze genereren. Ze verzenden ze net als een stroom van "telemetrie"-gebeurtenissen. In dit geval kunt u op geen enkel moment op het apparaat zoeken naar de laatste waarde van het veld telemetrie. In plaats daarvan moet u Luis teren naar de berichten van het apparaat en acties uitvoeren wanneer de berichten binnenkomen. 

Als gevolg hiervan zult u bij het ontwerpen van een model in azure Digital Apparaatdubbels waarschijnlijk **Eigenschappen** in de meeste gevallen gebruiken om uw apparaatdubbels te model leren. Zo kunt u de back-upopslag en de mogelijkheid om gegevens velden te lezen en er query's op uitvoeren.

Telemetrie en eigenschappen werken vaak samen om het binnenbrengen van gegevens van apparaten te verwerken. Als alle inkomend verkeer naar Azure Digital Apparaatdubbels is via [api's](how-to-use-apis-sdks.md), gebruikt u meestal uw inkomende functie om de telemetrie-of eigenschaps gebeurtenissen van apparaten te lezen en een eigenschap in te stellen in azure Digital apparaatdubbels als antwoord. 

U kunt ook een telemetrie-gebeurtenis publiceren vanuit de Azure Digital Apparaatdubbels-API. Net als bij andere telemetrie is dit een gebeurtenis met een korte levens duur waarvoor een listener moet worden afgehandeld.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Specifieke Azure Digital Apparaatdubbels DTDL-implementaties

Een DTDL-model om compatibel te zijn met Azure Digital Apparaatdubbels, moet aan deze vereisten voldoen.

* Alle DTDL-elementen op het hoogste niveau in een model moeten van het type *Interface* zijn. Dit komt doordat Azure Digital Apparaatdubbels model-Api's JSON-objecten kunnen ontvangen die een interface of een matrix van interfaces vertegenwoordigen. Als gevolg hiervan zijn geen andere DTDL-element typen toegestaan op het hoogste niveau.
* DTDL voor Azure Digital Apparaatdubbels mogen geen *opdrachten* definiëren.
* Azure Digital Apparaatdubbels staat slechts één niveau van geneste onderdelen toe. Dit betekent dat een interface die als onderdeel wordt gebruikt, zelf geen onderdelen kan hebben. 
* Interfaces kunnen niet in line worden gedefinieerd binnen andere DTDL-interfaces; ze moeten worden gedefinieerd als afzonderlijke entiteiten op het hoogste niveau met hun eigen Id's. Wanneer een andere interface die interface wil toevoegen als een onderdeel of via overname, kan deze verwijzen naar de ID.

Azure Digital Apparaatdubbels houdt ook geen rekening `writable` met het kenmerk voor eigenschappen of relaties. Hoewel dit kan worden ingesteld met de specificaties per DTDL, wordt de waarde niet gebruikt door Azure Digital Apparaatdubbels. In plaats daarvan worden deze altijd behandeld als beschrijfbaar door externe clients die algemene schrijf machtigingen hebben voor de Azure Digital Apparaatdubbels-service.

## <a name="example-model-code"></a>Voorbeeld model code

Dubbele type modellen kunnen worden geschreven in elke tekst editor. De DTDL-taal volgt de JSON-syntaxis, dus u moet modellen met de extensie *. json* opslaan. Door gebruik te maken van de JSON-extensie biedt veel Program meren-tekst editors de mogelijkheid om basis syntaxis controles uit te voeren en te markeren voor uw DTDL-documenten. Er is ook een [DTDL-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) beschikbaar voor [Visual Studio code](https://code.visualstudio.com/).

Deze sectie bevat een voor beeld van een typisch model, geschreven als een DTDL-interface. Het model beschrijft **plan eten**, elk met een naam, massa en een Tempe ratuur.
 
Houd er rekening mee dat plan eten ook kunnen communiceren met **manen** die hun satellieten zijn, en kan **Craters** bevatten. In het onderstaande voor beeld `Planet` drukt het model verbindingen met deze andere entiteiten af door te verwijzen naar twee externe modellen, `Moon` en `Crater` . Deze modellen worden ook gedefinieerd in de voorbeeld code hieronder, maar worden heel eenvoudig bewaard, zodat ze niet vanuit het primaire voor beeld kunnen worden getraceerd `Planet` .

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

De velden van het model zijn:

| Veld | Description |
| --- | --- |
| `@id` | Een id voor het model. Moet de indeling hebben `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Hiermee wordt het type informatie aangegeven dat wordt beschreven. Voor een interface is het type *Interface*. |
| `@context` | Hiermee stelt u de [context](https://niem.github.io/json/reference/json-ld/context/) voor het JSON-document. Modellen moeten worden gebruikt `dtmi:dtdl:context;2` . |
| `displayName` | Beschrijving Hiermee kunt u het model een beschrijvende naam geven, indien gewenst. |
| `contents` | Alle overige Interface gegevens worden hier geplaatst, als een matrix met kenmerk definities. Elk kenmerk moet een `@type` (*eigenschap*, *telemetrie*, *opdracht*, *relatie* of *onderdeel*) bevatten om de sortering van interface gegevens te identificeren die hierin wordt beschreven, en vervolgens een set eigenschappen die het werkelijke kenmerk definiëren (bijvoorbeeld `name` en `schema` om een *eigenschap* te definiëren). |

> [!NOTE]
> Houd er rekening mee dat de component interface (*Crater* in dit voor beeld) is gedefinieerd in dezelfde matrix als de interface die gebruikmaakt van IT (*planeet*). Onderdelen moeten op deze manier worden gedefinieerd in API-aanroepen om de interface te vinden.

### <a name="possible-schemas"></a>Mogelijke schema's

Volgens DTDL kan het schema voor *eigenschaps* -en *telemetrie* -kenmerken van de standaard primitieve typen,,, `integer` en en `double` `string` `Boolean` andere typen, zoals `DateTime` en `Duration` , zijn. 

Naast primitieve typen kunnen *eigenschaps* -en *telemetrie* -velden de volgende complexe typen hebben:
* `Object`
* `Map`
* `Enum`

*Telemetrie* -velden ondersteunen ook `Array` .

### <a name="model-inheritance"></a>Model overname

Soms wilt u mogelijk een model specialiseren. Het kan bijvoorbeeld nuttig zijn om een algemene model *kamer* te hebben, en speciale varianten *ConferenceRoom* en *Gym*. Voor het expliciet aanduiden van specialisatie biedt DTDL ondersteuning voor overname: interfaces kunnen overnemen van een of meer andere interfaces. 

In het volgende voor beeld wordt het *planeet* model van het vorige DTDL-voor beeld opnieuw beschouwd als een subtype van een groter *CelestialBody* -model. Het bovenliggende model wordt eerst gedefinieerd en vervolgens wordt het onderliggende model op basis van het veld samengesteld `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

In dit voor beeld draagt *CelestialBody* bij aan een naam, massa en een temperatuur voor *planeet*. De `extends` sectie is een interface naam of een matrix met interface namen (waardoor de uitbreidings interface kan overnemen van meerdere bovenliggende modellen, indien gewenst).

Zodra overname is toegepast, worden alle eigenschappen van de volledige overname keten beschikbaar gemaakt door de uitbrei ding van de interface.

De uitbreidende interface kan geen van de definities van de bovenliggende interfaces wijzigen; Er kan alleen worden toegevoegd. Het is ook niet mogelijk een functie opnieuw te definiëren die al is gedefinieerd in een van de bovenliggende interfaces (zelfs als de mogelijkheden zijn gedefinieerd om hetzelfde te zijn). Als een bovenliggende interface bijvoorbeeld een `double` eigenschaps *massa* definieert, kan de uitbrei ding van de interface geen declaratie van *massa* bevatten, zelfs als deze ook a `double` .

## <a name="best-practices-for-designing-models"></a>Aanbevolen procedures voor het ontwerpen van modellen

Bij het ontwerpen van modellen om de entiteiten in uw omgeving weer te geven, kan het nuttig zijn om vooruit te kijken en de [query](concepts-query-language.md) implicaties van uw ontwerp te bekijken. Mogelijk wilt u eigenschappen ontwerpen op een manier waarmee grote resultaten sets worden voor komen op basis van grafiek navigatie. Het is ook mogelijk dat u relaties wilt model leren die in één query worden beantwoord als relaties met één niveau.

### <a name="validating-models"></a>Modellen valideren

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Hulp middelen voor modellen 

Er zijn verschillende voor beelden beschikbaar waarmee u eenvoudiger kunt omgaan met modellen en Ontologies. Ze bevinden zich in deze opslag plaats: [Hulpprogram ma's voor Digital Apparaatdubbels Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-tools).

In deze sectie wordt de huidige set voor beelden uitvoeriger beschreven.

### <a name="model-uploader"></a>Model Uploader 

_**Voor het uploaden van modellen naar Azure Digital Apparaatdubbels**_

Zodra u klaar bent met het maken, uitbreiden of selecteren van uw modellen, kunt u ze uploaden naar uw Azure Digital Apparaatdubbels-exemplaar om ze beschikbaar te maken voor gebruik in uw oplossing. Dit wordt gedaan met behulp van de [apparaatdubbels-api's van Azure](how-to-use-apis-sdks.md), zoals beschreven in [*How to: Manage DTDL models*](how-to-manage-model.md#upload-models).

Als u echter veel modellen wilt uploaden, of als ze veel onderlinge afhankelijkheden hebben waardoor afzonderlijke uploads gecompliceerd worden uitgevoerd, kunt u dit voor beeld gebruiken om een groot aantal modellen tegelijk te uploaden: [**Azure Digital Apparaatdubbels model Uploader**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Volg de instructies in het voor beeld voor het configureren en gebruiken van dit project voor het uploaden van modellen in uw eigen exemplaar.

### <a name="model-visualizer"></a>Model visualer 

_**Voor het visualiseren van modellen**_

Zodra u modellen hebt geüpload naar uw Azure Digital Apparaatdubbels-exemplaar, kunt u de modellen in uw Azure Digital Apparaatdubbels-instantie bekijken, inclusief eventuele overname-en model relaties, met behulp van de [**Visual Apparaatdubbels model-visualisatie van Azure**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Dit voor beeld bevindt zich momenteel in een concept status. We raden aan de Digital apparaatdubbels Development Community uit te breiden en bijdragen te leveren aan het voor beeld. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van modellen op basis van Ontologies: [ *concepten: wat is een Ontology?*](concepts-ontologies.md)

* Meer informatie over het beheren van modellen met API-bewerkingen: [ *instructies: DTDL-modellen beheren*](how-to-manage-model.md)

* Meer informatie over hoe modellen worden gebruikt voor het maken van Digital apparaatdubbels: [ *concepten: Digital apparaatdubbels en de dubbele grafiek*](concepts-twins-graph.md)

