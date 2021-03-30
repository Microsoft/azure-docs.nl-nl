---
title: Azure Functions bindings uitbreidingen registreren
description: Meer informatie over het registreren van een Azure Functions bindings uitbreiding op basis van uw omgeving.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689548"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions bindings uitbreidingen registreren

Vanaf Azure Functions versie 2. x bevat de functions-runtime standaard alleen HTTP-en timer-triggers. Andere [Triggers en bindingen](./functions-triggers-bindings.md) zijn beschikbaar als afzonderlijke pakketten.

.NET Class Library functions apps gebruiken bindingen die in het project zijn geïnstalleerd als NuGet-pakketten. Met uitbreidings bundels kunnen apps met non-.NET-functies gebruikmaken van dezelfde bindingen zonder dat ze de .NET-infra structuur hoeven te verwerken.

De volgende tabel geeft aan wanneer en hoe u bindingen registreert.

| Ontwikkelomgeving |Registratie<br/> in functions 1. x  |Registratie<br/> in functies 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisch|Automatisch<sup>*</sup>|
|Non-.NET talen|Automatisch|Gebruik [uitbreidings bundels](#extension-bundles) (aanbevolen) of [Installeer expliciet extensies](#explicitly-install-extensions)|
|C# Class-bibliotheek met Visual Studio|[NuGet-hulpprogram ma's gebruiken](#vs)|[NuGet-hulpprogram ma's gebruiken](#vs)|
|C#-klassen bibliotheek met Visual Studio code|N.v.t.|[.NET Core SLI gebruiken](#vs-code)|

<sup>*</sup> Portal maakt gebruik van uitbreidings bundels.

## <a name="access-extensions-in-non-net-languages"></a>Toegangs uitbreidingen in non-.NET talen

Voor Java-, java script-, Power shell-, python-en aangepaste handler-functie-apps raden we u aan uitbreidings bundels te gebruiken om toegang te krijgen tot bindingen. In gevallen waarin uitbreidings bundels niet kunnen worden gebruikt, kunt u expliciet bindings uitbreidingen installeren.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Uitbreidings bundels

Uitbreidings bundels zijn een manier om een compatibele set bindings uitbreidingen toe te voegen aan uw functie-app. U schakelt uitbreidings bundels in de *host.jsvan* de app in.

U kunt uitbreidings bundels gebruiken met versie 2. x en latere versies van de functions-runtime.

Uitbreidings bundels zijn versie nummer. Elke versie bevat een specifieke set bindings uitbreidingen die worden gecontroleerd om samen te werken. Selecteer een bundel versie op basis van de uitbrei dingen die u nodig hebt in uw app.

Als u een uitbreidings bundel wilt toevoegen aan uw functie-app, voegt `extensionBundle` u de sectie toe aan *host.jsop*. In veel gevallen voegt Visual Studio code en Azure Functions Core Tools deze automatisch toe voor u.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

De volgende tabel geeft een lijst van de momenteel beschik bare versies van de standaard *micro soft. Azure. functions. ExtensionBundle* bundel en koppelingen naar de uitbrei dingen die ze bevatten.

| Bundel versie | Versie in host.jsop | Inbegrepen uitbrei dingen |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Zie [extensions.jsvoor](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) het genereren van de bundel |
| 2.x | `[2.*, 3.0.0)` | Zie [extensions.jsvoor](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) het genereren van de bundel |

> [!NOTE]
> Hoewel u een aangepast versie bereik kunt opgeven in host.js, raden we u aan om een versie waarde uit deze tabel te gebruiken.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Expliciete extensies installeren

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Extensies van NuGet in .NET-talen installeren

Voor een project op basis van C#-klassen bibliotheek moet u de extensies rechtstreeks installeren. Uitbreidings bundels zijn speciaal ontworpen voor projecten die geen C#-klassen bibliotheek zijn.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> C \# Class-bibliotheek met Visual Studio

In **Visual Studio** kunt u pakketten installeren via de Package Manager-console met behulp van de opdracht [installeren-pakket](/nuget/tools/ps-ref-install-package) , zoals wordt weer gegeven in het volgende voor beeld:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

De naam van het pakket dat voor een bepaalde binding wordt gebruikt, is opgenomen in het referentie artikel voor die binding. Zie de [sectie pakketten in het artikel service bus binding referentie](functions-bindings-service-bus.md#functions-1x)voor een voor beeld.

Vervang `<TARGET_VERSION>` in het voor beeld door een specifieke versie van het pakket, zoals `3.0.0-beta5` . Geldige versies worden weer gegeven op de afzonderlijke pakket pagina's op [NuGet.org](https://nuget.org). De primaire versies die overeenkomen met functions runtime 1. x of 2. x worden opgegeven in het referentie artikel voor de binding.

Als u gebruikt `Install-Package` om te verwijzen naar een binding, hoeft u geen [uitbreidings bundels](#extension-bundles)te gebruiken. Deze benadering is specifiek voor klassen bibliotheken die zijn gemaakt in Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> C#-klassen bibliotheek met Visual Studio code

In **Visual Studio code** installeert u pakketten voor een C# Class Library-project vanaf de opdracht prompt met behulp van de [DotNet add package](/dotnet/core/tools/dotnet-add-package) opdracht in de .net core SLI. In het volgende voor beeld ziet u hoe u een binding toevoegt:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

De .NET Core SLI kan alleen worden gebruikt voor de ontwikkeling van Azure Functions 2. x.

Vervang door `<BINDING_TYPE_NAME>` de naam van het pakket dat de binding bevat die u nodig hebt. U kunt het artikel over de gewenste bindings verwijzing vinden in de [lijst met ondersteunde bindingen](./functions-triggers-bindings.md#supported-bindings).

Vervang `<TARGET_VERSION>` in het voor beeld door een specifieke versie van het pakket, zoals `3.0.0-beta5` . Geldige versies worden weer gegeven op de afzonderlijke pakket pagina's op [NuGet.org](https://nuget.org). De primaire versies die overeenkomen met functions runtime 1. x of 2. x worden opgegeven in het referentie artikel voor de binding.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Azure function-trigger en binding-voor beeld](./functions-bindings-example.md)
