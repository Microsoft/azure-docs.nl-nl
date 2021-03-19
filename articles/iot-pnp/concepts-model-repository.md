---
title: Inzicht in de concepten van de opslag plaats voor apparaat modellen | Microsoft Docs
description: Als oplossings ontwikkelaar of IT-Professional leert u meer over de basis concepten van de opslag plaats voor apparaat modellen.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582643"
---
# <a name="device-models-repository"></a>Opslag plaats voor apparaat modellen

Met de Device models-opslag plaats (DMR) kunnen apparaats bouwers IoT Plug en Play-apparaten beheren en delen. De modellen van het apparaat zijn JSON-bestanden die zijn gedefinieerd met de [Digital Apparaatdubbels Modeling Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

De DMR definieert een patroon voor het opslaan van DTDL-interfaces in een mappen structuur op basis van de dubbele model-id van het apparaat (DTMI). U kunt een interface vinden in de DMR door de DTMI te converteren naar een relatief pad. Bijvoorbeeld, de `dtmi:com:example:Thermostat;1` DTMI vertaalt naar `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-models-repository"></a>Opslag plaats voor modellen voor open bare apparaten

Micro soft host een open bare DMR met de volgende kenmerken:

- Gecurede modellen. Micro soft beoordeelt en goed keurt alle beschik bare interfaces met behulp van een validatie werk stroom voor GitHub pull-aanvragen (PR).
- Onveranderbaarheid.  Nadat deze is gepubliceerd, kan een interface niet worden bijgewerkt.
- Hyper-scale. Micro soft biedt de vereiste infra structuur voor het maken van een veilig, schaalbaar eind punt waar u apparaten modellen kunt publiceren en gebruiken.

## <a name="custom-device-models-repository"></a>Opslag plaats voor aangepaste apparaat modellen

Gebruik hetzelfde DMR-patroon voor het maken van een aangepaste DMR in een opslag medium, zoals het lokale bestands systeem of aangepaste HTTP-webservers. U kunt de modellen van de aangepaste DMR op dezelfde manier ophalen als uit de open bare DMR door de basis-URL te wijzigen die wordt gebruikt voor toegang tot de DMR.

> [!NOTE]
> Micro soft biedt hulpprogram ma's voor het valideren van apparaten modellen in de open bare DMR. U kunt deze hulpprogram ma's opnieuw gebruiken in aangepaste opslag plaatsen.

## <a name="public-models"></a>Open bare modellen

De modellen voor open bare apparaten die zijn opgeslagen in de bibliotheek modellen, zijn beschikbaar voor iedereen die ze in hun toepassingen kan gebruiken en integreren. Modellen voor open bare apparaten bieden een open eco-systeem voor apparaten en oplossings ontwikkelaars om hun IoT Plug en Play-apparaatklassen te delen en opnieuw te gebruiken.

Raadpleeg de sectie [een model publiceren](#publish-a-model) voor instructies over het publiceren van een model in de opslag plaats modellen om het openbaar te maken.

Gebruikers kunnen open bare interfaces zoeken, doorzoeken en weer geven vanuit de officiële [github-opslag plaats](https://github.com/Azure/iot-plugandplay-models).

Alle interfaces in de `dtmi` mappen zijn ook beschikbaar vanuit het open bare eind punt [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Modellen oplossen

Als u via een programma toegang wilt krijgen tot deze interfaces, kunt u de `ModelsRepositoryClient` Beschik baarheid gebruiken in het NuGet-pakket [Azure. IOT. ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository). Deze client is standaard geconfigureerd voor het uitvoeren van een query op de open bare DMR die beschikbaar zijn op [devicemodels.Azure.com](https://devicemodels.azure.com/) en kan worden geconfigureerd voor elke aangepaste opslag plaats.

De client accepteert een `DTMI` als-invoer en retourneert een woorden lijst met alle vereiste interfaces:

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

In de verwachte uitvoer moeten de drie interfaces worden weer gegeven die `DTMI` in de afhankelijkheids keten zijn gevonden:

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

De `ModelsRepositoryClient` kan worden geconfigureerd voor het uitvoeren van een query op een aangepaste model opslagplaats die beschikbaar is via http (s), en geef de afhankelijkheids oplossing op met behulp van een van de volgende beschik bare `ModelDependencyResolution` :

- Uitgeschakeld. Retourneert alleen de opgegeven interface, zonder enige afhankelijkheid.
- Ingeschakeld. Retourneert alle interfaces in de afhankelijkheids keten
- TryFromExpanded. Het `.expanded.json` bestand gebruiken om de vooraf berekende afhankelijkheden op te halen 

> [!Tip] 
> Het bestand wordt mogelijk niet door aangepaste opslag plaatsen weer gegeven `.expanded.json` wanneer de client niet beschikbaar is om elke afhankelijkheid lokaal te verwerken.

De volgende voorbeeld code laat zien hoe u met `ModelsRepositoryClient` behulp van een aangepaste basis-URL voor de opslag plaats initialiseert, in dit geval met behulp `raw` van de url's van de GITHUB-API zonder het formulier te gebruiken `expanded` omdat deze niet beschikbaar is in het `raw` eind punt. De `AzureEventSourceListener` wordt geïnitialiseerd voor het controleren van de HTTP-aanvraag die door de client is uitgevoerd:

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Er zijn meer voor beelden beschikbaar in de bron code in de Azure SDK GitHub-opslag plaats: [Azure. IOT. ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>Een model publiceren

> [!Important]
> U moet een GitHub-account hebben om modellen te kunnen verzenden naar de open bare DMR.

1. De open bare GitHub-opslag plaats splitsen: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Kloon de gesplitste opslag plaats. Maak eventueel een nieuwe vertakking om uw wijzigingen geïsoleerd van de vertakking te laten blijven `main` .
1. Voeg de nieuwe interfaces toe aan de `dtmi` map met de Conventie map/bestands naam. Zie [een model importeren in de `dtmi/` map](#import-a-model-to-the-dtmi-folder)voor meer informatie.
1. Valideer de modellen lokaal met het `dmr-client` hulp programma. Zie [modellen valideren](#validate-models)voor meer informatie.
1. De wijzigingen lokaal door voeren en naar uw Fork pushen.
1. Maak vanuit uw Fork een pull-aanvraag die gericht is op de `main` vertakking. Zie [een uitgifte-of pull-aanvraag documenten maken](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Controleer de [vereisten voor de pull-aanvraag](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

Met de pull-aanvraag wordt een set GitHub-acties geactiveerd die de ingediende interfaces valideren en zorgt u ervoor dat uw pull-aanvraag voldoet aan alle vereisten.

Micro soft reageert op een pull-aanvraag met alle controles in drie werk dagen.

### <a name="dmr-client-tools"></a>`dmr-client` software

De hulpprogram ma's die worden gebruikt voor het valideren van de modellen tijdens de PR-controles, kunnen ook worden gebruikt om de DTDL-interfaces lokaal toe te voegen en te valideren.

> [!NOTE]
> Voor dit hulp programma is de [.NET SDK](https://dotnet.microsoft.com/download) -versie 3,1 of hoger vereist.

### <a name="install-dmr-client"></a>Vooraf `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Een model importeren in de `dtmi/` map

Als uw model al is opgeslagen in json-bestanden, kunt u de `dmr-client import` opdracht gebruiken om ze toe te voegen aan de `dtmi/` map met de juiste bestands namen:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> U kunt het `--local-repo` argument gebruiken om de hoofdmap van de lokale opslag plaats op te geven.

### <a name="validate-models"></a>Modellen valideren

U kunt uw modellen valideren met de `dmr-client validate` opdracht:

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> De validatie maakt gebruik van de nieuwste versie van de DTDL-parser om ervoor te zorgen dat alle interfaces compatibel zijn met de taal specificatie DTDL.

Als u externe afhankelijkheden wilt valideren, moeten deze aanwezig zijn in de lokale opslag plaats. Als u modellen wilt valideren, gebruikt u de `--repo` optie om een `local` of-map op te geven voor het oplossen van `remote` afhankelijkheden:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Strikte validatie

De DMR bevat aanvullende [vereisten](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md) `stict` voor het valideren van uw model met de markering:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Controleer de console-uitvoer voor eventuele fout berichten.

### <a name="export-models"></a>Exportmodellen

Modellen kunnen vanuit een bepaalde opslag plaats (lokaal of extern) naar één bestand worden geëxporteerd met behulp van een JSON-matrix:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is het controleren van de [IoT Plug en Play-architectuur](concepts-architecture.md).
