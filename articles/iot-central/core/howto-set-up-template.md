---
title: Geef een nieuw IoT-apparaattype op in azure IoT Central | Microsoft Docs
description: In dit artikel wordt beschreven hoe u, als opbouw functie voor oplossingen, een nieuwe Azure IoT-sjabloon maakt in uw Azure IoT Central-toepassing. U definieert de telemetrie, de status, de eigenschappen en de opdrachten voor uw type.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 22e948a0100f23dbddef8fc138576bb4b9372c77
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363199"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Een nieuw IoT-apparaattype definiëren in uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

Een sjabloon voor een apparaat is een blauw druk die de kenmerken en het gedrag definieert van een type apparaat dat verbinding maakt met een [Azure IOT Central-toepassing](concepts-app-templates.md).

Een opbouw functie kan bijvoorbeeld een apparaatprofiel maken voor een verbonden ventilator die de volgende kenmerken heeft:

- Verzendt een telemetrie van de Tempe ratuur
- Eigenschap voor verzenden van locatie
- Hiermee worden fout gebeurtenissen van een ventilator motor verzonden
- Hiermee wordt de status van de ventilator verzonden
- Biedt een Beschrijf bare ventilator snelheids eigenschap
- Biedt een opdracht voor het opnieuw opstarten van het apparaat
- Geeft een algemene weer gave van het apparaat met behulp van een weer gave

Met deze apparaatprofiel kan een operator echte ventilator apparaten maken en verbinden. Al deze ventilatoren hebben metingen, eigenschappen en opdrachten die Opera tors gebruiken om ze te controleren en te beheren. Opera tors gebruiken de [weer gaven](#add-views) en formulieren van het apparaat om te communiceren met de ventilator apparaten. Een ontwikkelaar van het apparaat gebruikt de sjabloon om te begrijpen hoe het apparaat samenwerkt met de toepassing. Zie [telemetrie, Property en Command payloads](concepts-telemetry-properties-commands.md)voor meer informatie.

> [!NOTE]
> Alleen bouwers en beheerders kunnen sjablonen voor apparaten maken, bewerken en verwijderen. Elke gebruiker kan apparaten op de pagina **apparaten** maken op basis van bestaande Apparaatinstellingen.

In een IoT Central-toepassing gebruikt een apparaatprofiel een model voor het beschrijven van de mogelijkheden van een apparaat. Als opbouw functie hebt u verschillende opties voor het maken van Apparaatinstellingen:

- Ontwerp de sjabloon voor het apparaat in IoT Central en [Implementeer vervolgens het model van het apparaat in de code van uw apparaat](concepts-telemetry-properties-commands.md).
- Importeer een apparaatprofiel vanuit de [Azure Certified voor IOT-apparaat Catalog](https://aka.ms/iotdevcat). Pas de sjabloon voor het apparaat aan aan uw vereisten in IoT Central.
> [!NOTE]
> Voor IoT Central is het volledige model vereist met alle interfaces waarnaar wordt verwezen in hetzelfde bestand; wanneer u een model uit de model opslagplaats importeert, gebruikt u het tref woord ' uitgebreid ' om de volledige versie op te halen.
Bijvoorbeeld. https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json

- Maak een model voor een apparaat met de [Digital Apparaatdubbels Definition Language (DTDL)-versie 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Visual Studio code heeft een uitbrei ding die ondersteuning biedt voor het ontwerpen van DTDL modellen. Zie voor meer informatie [De DTDL-ontwerptools installeren en gebruiken](../../iot-pnp/howto-use-dtdl-authoring-tools.md). Publiceer het model vervolgens naar de open bare model opslagplaats. Zie [device model-opslag plaats](../../iot-pnp/concepts-model-repository.md)voor meer informatie. Implementeer uw apparaatcode vanuit het model en verbind uw echte apparaat met uw IoT Central-toepassing. IoT Central zoekt en importeert het model van het apparaat uit de open bare opslag plaats en genereert een sjabloon voor het apparaat. U kunt vervolgens alle Cloud eigenschappen, aanpassingen en weer gaven toevoegen aan de IoT Central toepassing die nodig is voor de sjabloon voor het apparaat.
- Maak een model voor een apparaat met behulp van de DTDL. Implementeer uw apparaatcode vanuit het model. Importeer het model van het apparaat hand matig in uw IoT Central-toepassing en voeg vervolgens alle Cloud eigenschappen, aanpassingen en weer gaven toe die nodig zijn voor uw IoT Central toepassing.

> [!TIP]
> IoT Central vereist het volledige model met alle interfaces waarnaar wordt verwezen in hetzelfde bestand. Wanneer u een model uit de model opslagplaats importeert, gebruikt u het tref woord *uitgevouwen* om de volledige versie op te halen.
> Bijvoorbeeld [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json) .

U kunt ook apparaatprofielen toevoegen aan een IoT Central-toepassing met behulp van de [rest API](/learn/modules/manage-iot-central-apps-with-rest-api/) of de [cli](howto-manage-iot-central-from-cli.md).

Sommige [toepassings sjablonen](concepts-app-templates.md) bevatten al sjablonen voor apparaten die handig zijn in het scenario dat door de toepassings sjabloon wordt ondersteund. Zie bijvoorbeeld [in-Store Analytics-architectuur](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Een sjabloon voor een apparaat maken vanuit de catalogus met apparaten

Als bouwer kunt u snel beginnen met het bouwen van uw oplossing met behulp van een gecertificeerd apparaat. Zie de lijst in de [Azure IOT-Apparaatbeheer](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreert met de catalogus van het apparaat, zodat u een apparaatprofiel kunt importeren vanuit een van de gecertificeerde apparaten. Een sjabloon voor een apparaat maken op basis van een van deze apparaten in IoT Central:

1. Ga naar de pagina met **Apparaatinstellingen** in uw IOT Central-toepassing.
1. Selecteer **+ Nieuw** en selecteer vervolgens een van de gecertificeerde apparaten uit de catalogus. IoT Central maakt een sjabloon op basis van dit model.
1. Voeg Cloud eigenschappen, aanpassingen of weer gaven toe aan de sjabloon voor uw apparaat.
1. Selecteer **publiceren** om de sjabloon beschikbaar te maken voor Opera tors om apparaten weer te geven en te verbinden.

## <a name="create-a-device-template-from-scratch"></a>Een volledig nieuwe sjabloon voor een apparaat maken

Een sjabloon voor het apparaat bevat:

- Een _model_ dat de telemetrie, eigenschappen en opdrachten specificeert die het apparaat implementeert. Deze mogelijkheden zijn ingedeeld in een of meer onderdelen.
- _Cloud eigenschappen_ waarmee informatie wordt gedefinieerd die uw IOT Central-app over uw apparaten opslaat. Een Cloud eigenschap kan bijvoorbeeld de datum vastleggen waarop een apparaat voor het laatst is verwerkt. Deze informatie wordt nooit gedeeld met het apparaat.
- Met _aanpassingen_ kunnen de opbouw functie sommige definities in het model van het apparaat overschrijven. De opbouw functie kan bijvoorbeeld de naam van een eigenschap apparaat onderdrukken. Eigenschaps namen worden weer gegeven in IoT Central weer gaven en formulieren.
- Met _weer gaven en formulieren_ kan de opbouw functie een gebruikers interface maken waarmee Opera tors de apparaten kunnen controleren en beheren die zijn verbonden met uw toepassing.

Een sjabloon voor een apparaat maken in IoT Central:

1. Ga naar de pagina met **Apparaatinstellingen** in uw IOT Central-toepassing.
1. Selecteer **+ Nieuw**  >  **IOT-apparaat**. Selecteer vervolgens **Volgende: Aanpassen**.
1. Voer een naam in voor de sjabloon, zoals **Thermo** staat. Selecteer vervolgens **volgende: controleren** en selecteer vervolgens **maken**.
1. Met IoT Central maakt u een sjabloon voor een leeg apparaat en kunt u ervoor kiezen om een nieuw aangepast model te maken of een DTDL-model te importeren.

## <a name="manage-a-device-template"></a>Een sjabloon voor een apparaat beheren

U kunt de naam van een sjabloon wijzigen of verwijderen via de start pagina van de sjabloon.

Nadat u een apparaatprofiel aan uw sjabloon hebt toegevoegd, kunt u het model publiceren. Totdat u de sjabloon hebt gepubliceerd, kunt u op de pagina **apparaten** geen verbinding maken met een apparaat dat is gebaseerd op deze sjabloon voor uw Opera tors.

## <a name="create-a-capability-model"></a>Een mogelijkheidsprofiel maken

Als u een model wilt maken, kunt u het volgende doen:

- Gebruik IoT Central om een volledig aangepast model te maken.
- Importeer een DTDL-model uit een JSON-bestand. Een opbouw functie voor apparaten kan Visual Studio code gebruiken om een model voor uw toepassing te ontwerpen.
- Selecteer een van de apparaten in de catalogus met apparaten. Met deze optie wordt het model van het apparaat geïmporteerd dat de fabrikant heeft gepubliceerd voor dit apparaat. Een apparaatprofiel dat wordt geïmporteerd zoals deze wordt automatisch gepubliceerd.

## <a name="manage-a-capability-model"></a>Een mogelijkheidsprofiel beheren

Nadat u een apparaatprofiel hebt gemaakt, kunt u het volgende doen:

- Onderdelen toevoegen aan het model. Een model moet ten minste één onderdeel hebben.
- Meta gegevens van het model bewerken, zoals de ID, naam ruimte en naam.
- Verwijder het model.

## <a name="create-a-component"></a>Een onderdeel maken

Een apparaatprofiel moet ten minste één standaard onderdeel hebben. Een onderdeel is een herbruikbare verzameling mogelijkheden.

Een onderdeel maken:

1. Ga naar het model van uw apparaat en kies **+ onderdeel toevoegen**.

1. Op de pagina **een onderdeel interface toevoegen** kunt u het volgende doen:

    - Maak een geheel nieuw aangepast onderdeel.
    - Importeer een bestaand onderdeel uit een DTDL-bestand. Een opbouw functie voor apparaten kan Visual Studio code gebruiken om een onderdeel interface voor uw apparaat te schrijven.

1. Nadat u een onderdeel hebt gemaakt, kiest u **identiteit bewerken** om de weergave naam van het onderdeel te wijzigen.

1. Als u een nieuw aangepast onderdeel wilt maken, kunt u de mogelijkheden van uw apparaat toevoegen. De mogelijkheden van apparaten zijn telemetrie, eigenschappen en opdrachten.

### <a name="telemetry"></a>Telemetrie

Telemetrie is een stroom van waarden die van het apparaat worden verzonden, meestal van een sensor. Een sensor kan bijvoorbeeld de omgevings temperatuur rapporteren.

De volgende tabel bevat de configuratie-instellingen voor een telemetrie-mogelijkheid:

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de telemetrie-waarde die wordt gebruikt voor weer gaven en formulieren. |
| Name | De naam van het veld in het telemetrie-bericht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. Dit veld moet alfanumeriek zijn. |
| Type mogelijkheid | Telemetrie. |
| Semantisch type | Het semantische type van de telemetrie, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type telemetrie, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Severity | Alleen beschikbaar voor het semantische gebeurtenis type. De ernst is **fout**, **informatie** of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de telemetrische waarde, zoals **mph**, **%** of **&deg; C**. |
| Eenheid weer geven | Een weergave-eenheid die wordt gebruikt voor weer gaven en formulieren. |
| Opmerking | Eventuele opmerkingen over de telemetrie-mogelijkheid. |
| Description | Een beschrijving van de telemetrie-mogelijkheid. |

### <a name="properties"></a>Eigenschappen

Eigenschappen vertegenwoordigen waarden van het tijdstip. Een apparaat kan bijvoorbeeld een eigenschap gebruiken om de doel temperatuur te rapporteren die het probeert te bereiken. U kunt Beschrijf bare eigenschappen van IoT Central instellen.

De volgende tabel bevat de configuratie-instellingen voor een eigenschaps mogelijkheid:

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de waarde van de eigenschap die wordt gebruikt voor weer gaven en formulieren. |
| Name | De naam van de eigenschap. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. Dit veld moet alfanumeriek zijn. |
| Type mogelijkheid | Eigenschap. |
| Semantisch type | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type van de eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status. |
| Beschrijfbaar | Als de eigenschap niet schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central. Als de eigenschap schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan IoT Central en IoT Central eigenschaps updates naar het apparaat verzenden.
| Severity | Alleen beschikbaar voor het semantische gebeurtenis type. De ernst is **fout**, **informatie** of **waarschuwing**. |
| Status waarden | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben. |
| Eenheid | Een eenheid voor de waarde van de eigenschap, zoals **mph**, **%** of **&deg; C**. |
| Eenheid weer geven | Een weergave-eenheid die wordt gebruikt voor weer gaven en formulieren. |
| Opmerking | Eventuele opmerkingen over de eigenschaps mogelijkheid. |
| Description | Een beschrijving van de eigenschaps mogelijkheid. |

### <a name="commands"></a>Opdracht

U kunt de opdrachten van een apparaat aanroepen vanuit IoT Central. Opdrachten geven optioneel para meters door aan het apparaat en ontvangen een reactie van het apparaat. U kunt bijvoorbeeld een opdracht aanroepen om een apparaat binnen tien seconden opnieuw op te starten.

De volgende tabel bevat de configuratie-instellingen voor een opdracht mogelijkheid:

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de opdracht die wordt gebruikt voor weer gaven en formulieren. |
| Name | De naam van de opdracht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. Dit veld moet alfanumeriek zijn. |
| Type mogelijkheid | Cmd. |
| Opmerking | Eventuele opmerkingen over de opdracht mogelijkheid. |
| Description | Een beschrijving van de opdracht mogelijkheid. |
| Aanvraag | Indien ingeschakeld, een definitie van de aanvraag parameter, met inbegrip van: naam, weergave naam, schema, eenheid en weer gave-eenheid. |
| Antwoord | Als deze optie is ingeschakeld, wordt een definitie van het opdracht antwoord gegeven, waaronder: naam, weergave naam, schema, eenheid en weer gave-eenheid. |

Zie [telemetrie, Property en Command payloads > opdrachten en langlopende opdrachten](concepts-telemetry-properties-commands.md#commands)voor meer informatie over hoe apparaten opdrachten implementeren.

#### <a name="offline-commands"></a>Offline opdrachten

U kunt wachtrij opdrachten kiezen als een apparaat momenteel offline is door de optie **wachtrij als offline** in te scha kelen voor een opdracht in de sjabloon voor het apparaat.

Met deze optie worden IoT Hub Cloud-naar-apparaat-berichten gebruikt om meldingen naar apparaten te verzenden. Zie het artikel IoT Hub [Cloud-naar-apparaat-berichten verzenden](../../iot-hub/iot-hub-devguide-messages-c2d.md)voor meer informatie.

Cloud-naar-apparaat-berichten:

- U kunt in één richting meldingen verzenden naar het apparaat vanuit uw oplossing.
- Bezorgt ten minste één keer per bericht levering. IoT Hub blijven Cloud-naar-apparaat-berichten in wacht rijen per apparaat behouden, zodat er toleranties worden gegarandeerd tegen connectiviteit en storingen in het apparaat.
- Stel in dat het apparaat een bericht-handler moet implementeren om het Cloud-naar-apparaat-bericht te verwerken.

> [!NOTE]
> Deze optie is alleen beschikbaar in de gebruikers interface van IoT Central web. Deze instelling is niet opgenomen als u een model of onderdeel uit de sjabloon voor het apparaat exporteert.

## <a name="manage-a-component"></a>Een onderdeel beheren

Als u het onderdeel nog niet hebt gepubliceerd, kunt u de mogelijkheden bewerken die door het onderdeel zijn gedefinieerd. Als u het onderdeel hebt gepubliceerd en u wijzigingen wilt aanbrengen, moet u een nieuwe versie van de sjabloon apparaat maken en [het onderdeel versie](howto-version-device-template.md). In het gedeelte **aanpassen** kunt u wijzigingen aanbrengen waarvoor geen versie beheer nodig is, zoals het weer geven van namen of eenheden.

U kunt het onderdeel ook exporteren als een JSON-bestand als u het opnieuw wilt gebruiken in een ander model voor functionaliteit.

## <a name="add-cloud-properties"></a>Cloudeigenschappen toevoegen

Gebruik Cloud eigenschappen om informatie over apparaten op te slaan in IoT Central. Cloud eigenschappen worden nooit verzonden naar een apparaat. U kunt bijvoorbeeld Cloud eigenschappen gebruiken om de naam op te slaan van de klant die het apparaat heeft geïnstalleerd of de laatste service datum van het apparaat.

De volgende tabel bevat de configuratie-instellingen voor een Cloud eigenschap:

| Veld | Description |
| ----- | ----------- |
| Weergavenaam | De weergave naam voor de waarde van de Cloud eigenschap die wordt gebruikt voor weer gaven en formulieren. |
| Name | De naam van de Cloud eigenschap. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. |
| Semantisch type | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn. |
| Schema | Het gegevens type van de Cloud eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. |

## <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen wanneer u een geïmporteerd onderdeel moet wijzigen of IoT Central-specifieke functies aan een mogelijkheid wilt toevoegen. U kunt alleen velden aanpassen die geen onderdeel compatibiliteit verstoren. U kunt bijvoorbeeld:

- Pas de weergave naam en de eenheden van een mogelijkheid aan.
- Een standaard kleur toevoegen die moet worden gebruikt wanneer de waarde in een grafiek wordt weer gegeven.
- Geef de initiële, minimale en maximale waarde voor een eigenschap op.

U kunt de naam of het type van de mogelijkheid niet aanpassen. Als er wijzigingen zijn die u niet in de sectie **aanpassen** kunt aanbrengen, moet u de sjabloon en het onderdeel van uw apparaat versie voor het wijzigen van de functionaliteit.

### <a name="generate-default-views"></a>Standaard weergaven genereren

Het genereren van standaard weergaven is een snelle manier om uw belang rijke apparaatgegevens te visualiseren. U hebt Maxi maal drie standaard weergaven gegenereerd voor uw apparaatprofiel:

- **Opdrachten**: een weer gave met opdrachten op het apparaat, zodat uw operator ze naar uw apparaat kan verzenden.
- **Overzicht**: een weer gave met telemetrie van apparaten, weer geven van grafieken en metrische gegevens.
- **Over**: een weer gave met apparaatgegevens, waarbij de apparaateigenschappen worden weer gegeven.

Nadat u **Standaard weergaven genereren** hebt geselecteerd, ziet u dat ze automatisch zijn toegevoegd onder het gedeelte **weer gaven** van de sjabloon voor uw apparaat.

## <a name="add-views"></a>Weergaven toevoegen

Voeg weer gaven toe aan een apparaatprofiel om Opera tors in staat te stellen een apparaat te visualiseren met behulp van grafieken en metrische gegevens. U kunt meerdere weer gaven voor een apparaatprofiel hebben.

Een weer gave toevoegen aan een sjabloon voor een apparaat:

1. Ga naar de sjabloon voor het apparaat en selecteer **weer gaven**.
1. Kies **het apparaat visualiseren**.
1. Voer een naam in voor de weer gave in de **weergave naam**.
1. Tegels toevoegen aan uw weer gave vanuit de lijst met statische, eigenschap, Cloud eigenschap, telemetrie en opdracht tegels. Sleep de tegels die u wilt toevoegen aan uw weer gave en zet deze neer.
1. Als u meerdere telemetrie-waarden wilt tekenen op één grafiek tegel, selecteert u de telemetriegegevens en selecteert u vervolgens **combi neren**.
1. Configureer elke tegel die u toevoegt om de manier aan te passen waarop gegevens worden weer gegeven. Toegang tot deze optie door het tandwiel pictogram te selecteren of door **configuratie wijzigen** te selecteren in de grafiek tegel.
1. De tegels in uw weer gave rangschikken en het formaat ervan wijzigen.
1. Sla de wijzigingen op.

### <a name="configure-preview-device-to-view"></a>Preview-apparaat configureren voor weer gave

Selecteer **Preview-apparaat configureren** om uw weer gave te bekijken en te testen. Met deze functie kunt u de weer gave zien als uw operator ziet nadat deze is gepubliceerd. Gebruik deze functie om te controleren of de juiste gegevens in uw weer gaven worden weer gegeven. U kunt een keuze maken uit de volgende opties:

- Geen preview-apparaat.
- Het echte test apparaat dat u hebt geconfigureerd voor de sjabloon voor het apparaat.
- Een bestaand apparaat in uw toepassing met behulp van de apparaat-ID.

## <a name="add-forms"></a>Formulieren toevoegen

Voeg formulieren toe aan een apparaatprofiel om Opera tors in staat te stellen om een apparaat te beheren door eigenschappen te bekijken en in te stellen. Opera tors kunnen alleen Cloud eigenschappen en eigenschappen van Beschrijf bare apparaten bewerken. U kunt meerdere formulieren voor een sjabloon voor het apparaat hebben.

Een formulier toevoegen aan een sjabloon voor een apparaat:

1. Ga naar de sjabloon voor het apparaat en selecteer **weer gaven**.
1. Kies **apparaat-en Cloud gegevens bewerken**.
1. Voer een naam in voor het formulier in de vorm van een **formulier naam**.
1. Selecteer het aantal kolommen dat moet worden gebruikt om het formulier in te delen.
1. Voeg eigenschappen toe aan een bestaande sectie in het formulier of selecteer Eigenschappen en kies **sectie toevoegen**. Gebruik secties om eigenschappen in uw formulier te groeperen. U kunt een titel toevoegen aan een sectie.
1. Configureer elke eigenschap op het formulier om het gedrag aan te passen.
1. De eigenschappen van het formulier rangschikken.
1. Sla de wijzigingen op.

## <a name="publish-a-device-template"></a>Een apparaatsjabloon publiceren

Voordat u verbinding kunt maken met een apparaat dat uw model implementeert, moet u de sjabloon voor het apparaat publiceren.

Nadat u een sjabloon voor een apparaat hebt gepubliceerd, kunt u alleen beperkte wijzigingen in het model van het apparaat aanbrengen. Als u een onderdeel wilt wijzigen, moet u [een nieuwe versie maken en publiceren](./howto-version-device-template.md).

Als u een sjabloon voor een apparaat wilt publiceren, gaat u naar uw apparaatprofiel en selecteert u **publiceren**.

Nadat u een sjabloon voor een apparaat hebt gepubliceerd, kan een operator naar de pagina **apparaten** gaan en een echte of gesimuleerde apparaten toevoegen die gebruikmaken van de sjabloon van het apparaat. U kunt door gaan met het wijzigen en opslaan van de sjabloon voor het apparaat wanneer u wijzigingen aanbrengt. Wanneer u deze wijzigingen wilt pushen naar de operator om weer te geven op de pagina **apparaten** , moet u elke keer **publiceren** selecteren.

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u het beste de volgende stap lezen over het [versie beheer](./howto-version-device-template.md)van de Apparaatbeheer.
