---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6f80d41001d11c52a00454ea2a593f3f1fce32db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027539"
---
## <a name="download-and-install"></a>Downloaden en installeren

#### <a name="windows-install"></a>[Windows installeren](#tab/windowsinstall)

Volg deze stappen om de Speech CLI te installeren voor Windows:

1. In Windows hebt u het [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) voor uw platform nodig. Bij een eerste installatie is mogelijk een herstart vereist.
2. Download het Speech CLI-[zip-bestand](https://aka.ms/speech/spx-zips.zip) en pak het uit.
3. Ga naar de map waar u `spx-zips` hebt geëxtraheerd. Deze map bevat programmabestanden voor de Speech CLI op verschillende platforms. 
4. Extraheer de bestanden voor uw platform (`spx-net471` voor .NET Framework 4.7 of `spx-netcore-win-x64` voor .NET Core 3.0 op een x64-CPU). Houd er rekening mee dat u `spx` vanuit deze map uitvoert.

### <a name="run-the-speech-cli"></a>De Speech CLI uitvoeren

1. Open de opdrachtprompt of PowerShell en ga naar de map waarin u de Speech CLI hebt uitgepakt.  
2. Typ `spx` om Help-opdrachten voor de Speech CLI weer te geven.

> [!NOTE]
> De lokale map wordt niet door Powershell gecontroleerd tijdens het zoeken naar een opdracht. Wijzig in Powershell de map naar de locatie van `spx` en roep het hulpprogramma aan door `.\spx` in te voeren.
> Als u deze map aan het pad, Powershell en de opdrachtprompt van Windows toevoegt, vindt u `spx` vanuit een willekeurige map, zonder het `.\`-voorvoegsel.

### <a name="font-limitations"></a>Lettertypebeperkingen

In Windows kan de Speech CLI alleen de lettertypen weergeven die beschikbaar zijn voor de opdrachtprompt op de lokale computer.
[Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) ondersteunt alle lettertypen die interactief worden gemaakt door de Speech CLI.

Als u naar een bestand uitvoert, kunnen in een teksteditor zoals Kladblok of een webbrowser zoals Microsoft Edge ook alle lettertypen worden weergegeven.

#### <a name="linux-install"></a>[Linux-installatie](#tab/linuxinstall)

Volg deze stappen om de Speech CLI te installeren voor Linux op een x64 CPU:

1. Installeer [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Download het Speech CLI-[zip-bestand](https://aka.ms/speech/spx-zips.zip) en pak het uit.
3. Ga naar de hoofdmap `spx-zips` van het gedownloade bestand en pak `spx-netcore-30-linux-x64` uit naar een nieuwe `~/spx` map.
4. Typ in een terminal de volgende opdrachten:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Typ `spx` om Help weer te geven voor de Speech CLI.

#### <a name="docker-install"></a>[Docker-installatie](#tab/dockerinstall)

> [!NOTE]
> <a href="https://www.docker.com/get-started" target="_blank">De desktopversie van Docker voor uw platform<span class="docon docon-navigate-external x-hidden-focus"></span></a> moet zijn geïnstalleerd.

Volg deze stappen om de Speech CLI te installeren in een Docker-container:

1. Typ in een nieuwe opdrachtprompt of terminal de volgende opdracht: `docker pull msftspeech/spx`
2. Typ deze opdracht. Raadpleeg de Help-informatie voor Speech CLI: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Een map koppelen in de container

Met het hulpprogramma Speech CLI worden configuratie-instellingen opgeslagen als bestanden, en worden deze bestanden geladen wanneer u een willekeurige opdracht uitvoert (behalve Help-opdrachten).
Wanneer u Speech CLI in een Docker-container gebruikt, moet u een lokale map vanuit de container koppelen. Hierdoor kunnen met het hulpprogramma de configuratie-instellingen worden opgeslagen of gevonden, en kunnen eventuele bestanden die zijn vereist via de opdracht (zoals audiobestanden of spraak), worden gelezen of geschreven.

Typ in Windows deze opdracht om een lokale map te maken die in de container kan worden gebruikt voor Speech CLI:

`mkdir c:\spx-data`

Voor Linux of Mac typt u deze opdracht in een terminal om een map te maken en het bijbehorende absolute pad te zien:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

U gebruikt het absolute pad wanneer u de Speech CLI aanroept.

### <a name="run-speech-cli-in-the-container"></a>Speech CLI uitvoeren in de container

In deze documentatie ziet u de Speech CLI-opdracht `spx` die wordt gebruikt in niet-Docker-installaties.
Wanneer u de opdracht `spx` aanroept in een Docker-container, moet u een map in de container koppelen aan uw bestandssysteem, waarin de Speech CLI configuratiewaarden kan opslaan en vinden, en bestanden kan lezen en schrijven.
In Windows beginnen de opdrachten als volgt:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

In Linux of Mac beginnen de opdrachten ongeveer als volgt:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Vervang `/ABSOLUTE_PATH` door het absolute pad dat wordt weergegeven met de opdracht `pwd` in de bovenstaande sectie.

Als u de opdracht `spx` wilt gebruiken in een container, moet u altijd de volledige opdracht invoeren, zoals hierboven wordt weergegeven, gevolgd door de parameters van uw aanvraag.
In Windows wordt bijvoorbeeld met deze opdracht de sleutel ingesteld:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> U kunt niet de microfoon of speaker van de computer gebruiken wanneer u de Speech CLI uitvoert in een Docker-container.
> Als u deze apparaten wilt gebruiken, laat u audiobestanden van en naar de Speech CLI gaan voor opnemen/afspelen buiten de Docker-container.
> Het hulpprogramma Speech CLI heeft toegang tot de lokale map die u hebt ingesteld in de bovenstaande stappen.

***

## <a name="create-subscription-config"></a>Abonnementsconfiguratie maken

Als u de Speech CLI wilt gaan gebruiken, moet u de sleutel voor het spraakabonnement en de regio-id invoeren. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../overview.md#try-the-speech-service-for-free).
Zodra u uw abonnementssleutel en regio-id hebt (bijvoorbeeld `eastus`, `westus`), voert u de volgende opdrachten uit.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Uw abonnementsverificatie wordt nu opgeslagen voor toekomstige SPX-aanvragen. Als u een van deze opgeslagen waarden wilt verwijderen, voert u `spx config @region --clear` of `spx config @key --clear` uit.
