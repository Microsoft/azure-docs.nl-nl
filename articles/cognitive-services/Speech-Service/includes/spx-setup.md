---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c939c1d061f4e01a00bc2b886056e42f0b059ad5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511121"
---
## <a name="download-and-install"></a>Downloaden en installeren

#### <a name="windows-install"></a>[Windows installeren](#tab/windowsinstall)

Volg deze stappen om de Speech CLI te installeren voor Windows:

1. In Windows hebt u het [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) voor uw platform nodig. Bij een eerste installatie is mogelijk een herstart vereist.
1. Installeer de [.net Core 3,1-SDK](/dotnet/core/install/windows).
2. Installeer de speech CLI met behulp van NuGet door de volgende opdracht in te voeren:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0
   ```
Typ `spx` om Help weer te geven voor de Speech CLI.

> [!NOTE]
> Als alternatief voor NuGet kunt u het speech CLI [zip-archief](https://aka.ms/speech/spx-zips.zip)downloaden en uitpakken, uw platform in de Directory zoeken en uitpakken `spx-zips` en het `spx` pad toevoegen aan de variabele  van het systeempad.


### <a name="font-limitations"></a>Lettertypebeperkingen

In Windows kan de Speech CLI alleen de lettertypen weergeven die beschikbaar zijn voor de opdrachtprompt op de lokale computer.
[Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) ondersteunt alle lettertypen die interactief worden gemaakt door de Speech CLI.

Als u naar een bestand uitvoert, kunnen in een teksteditor zoals Kladblok of een webbrowser zoals Microsoft Edge ook alle lettertypen worden weergegeven.

#### <a name="linux-install"></a>[Linux-installatie](#tab/linuxinstall)

De volgende Linux-distributies worden ondersteund voor x64-architecturen met behulp van de speech CLI:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04/18.04/20.04

> [!NOTE]
> Aanvullende architecturen worden ondersteund door de Speech SDK (niet de speech CLI). Zie [about the Speech SDK](../speech-sdk.md)(Engelstalig) voor meer informatie.

Volg deze stappen om de Speech CLI te installeren voor Linux op een x64 CPU:

1. Installeer de [.net Core 3,1-SDK](/dotnet/core/install/linux).
2. Installeer de speech CLI met behulp van NuGet door de volgende opdracht in te voeren:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

Typ `spx` om Help weer te geven voor de Speech CLI.

> [!NOTE]
> Als alternatief voor NuGet kunt u de binaire bestanden in het [zip-archief](https://aka.ms/speech/spx-zips.zip)downloaden, uitpakken `spx-netcore-30-linux-x64.zip` naar een nieuwe `~/spx` map, `sudo chmod +r+x spx` het binaire bestand typen en het `~/spx` pad toevoegen aan de systeem variabele PATH.


#### <a name="docker-install-windows-linux-macos"></a>[Docker-installatie (Windows, Linux, macOS)](#tab/dockerinstall)

Volg deze stappen om de Speech CLI te installeren in een Docker-container:

1. <a href="https://www.docker.com/get-started" target="_blank">Installeer Docker Desktop</a> voor uw platform als dat nog niet is geïnstalleerd.
2. Typ in een nieuwe opdrachtprompt of terminal de volgende opdracht: 
   ```console   
   docker pull msftspeech/spx
   ```
3. Typ deze opdracht. Raadpleeg de Help-informatie voor Speech CLI: 
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Een map koppelen in de container

Met het hulpprogramma Speech CLI worden configuratie-instellingen opgeslagen als bestanden, en worden deze bestanden geladen wanneer u een willekeurige opdracht uitvoert (behalve Help-opdrachten).
Wanneer u Speech CLI in een Docker-container gebruikt, moet u een lokale map vanuit de container koppelen. Hierdoor kunnen met het hulpprogramma de configuratie-instellingen worden opgeslagen of gevonden, en kunnen eventuele bestanden die zijn vereist via de opdracht (zoals audiobestanden of spraak), worden gelezen of geschreven.

Typ in Windows deze opdracht om een lokale map te maken die in de container kan worden gebruikt voor Speech CLI:

`mkdir c:\spx-data`

Voor Linux of macOS typt u deze opdracht in een terminal om een map te maken en het bijbehorende absolute pad te zien:

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

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

In Linux of macOS zien uw opdrachten eruit als in het onderstaande voorbeeld. Vervang `ABSOLUTE_PATH` door het absoluut pad van uw gekoppelde map. Dit pad is geretourneerd door de opdracht `pwd` in het vorige gedeelte. 

Als u deze opdracht uitvoert voordat u uw sleutel en regio instelt, krijgt u een fout waarin staat dat u uw sleutel en regio moet instellen:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Als u de opdracht `spx` wilt gebruiken in een container, moet u altijd de volledige opdracht invoeren, zoals hierboven wordt weergegeven, gevolgd door de parameters van uw aanvraag.
In Windows wordt bijvoorbeeld met deze opdracht de sleutel ingesteld:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config --set @key SUBSCRIPTION-KEY
```

Voor uitgebreidere interactie met het opdrachtregelprogramma kunt u een container starten met een interactieve bash shell door een beginpuntparameter toe te voegen.
Voer in Windows deze opdracht in om een container te starten met een interactieve opdrachtregelinterface waarin u meerdere `spx`-opdrachten kunt invoeren:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> U kunt niet de microfoon van de computer gebruiken wanneer u de Speech CLI uitvoert in een Docker-container. U kunt audiobestanden echter lezen uit en opslaan in uw lokaal gekoppelde map. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Abonnementsconfiguratie maken

Als u de Speech CLI wilt gaan gebruiken, moet u de sleutel voor het spraakabonnement en de regio-id invoeren. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../overview.md#try-the-speech-service-for-free).
Zodra u uw abonnementssleutel en regio-id hebt (bijvoorbeeld `eastus`, `westus`), voert u de volgende opdrachten uit.

```console
spx config --set @key SUBSCRIPTION-KEY
spx config --set @region REGION
```

Uw abonnementsverificatie wordt nu opgeslagen voor toekomstige SPX-aanvragen. Als u een van deze opgeslagen waarden wilt verwijderen, voert u `spx config @region --clear` of `spx config @key --clear` uit.
