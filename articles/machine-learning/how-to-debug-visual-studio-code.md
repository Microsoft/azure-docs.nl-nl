---
title: Interactieve debugging met Visual Studio Code
titleSuffix: Azure Machine Learning
description: Interactief fouten opsporen Azure Machine Learning code, pijplijnen en implementaties met behulp van Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 69a69afedbd86871987a8e62b55dfc070121cc78
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813862"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interactieve debugging met Visual Studio Code



Leer hoe u interactief fouten opsport Azure Machine Learning experimenten, pijplijnen en implementaties met behulp van Visual Studio Code (VS Code) en [foutopsporing](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Experimenten lokaal uitvoeren en fouten opsporen

Gebruik de Azure Machine Learning extensie om uw experimenten te valideren, uit te voeren machine learning fouten op te sporen voordat u ze naar de cloud indient.

### <a name="prerequisites"></a>Vereisten

* Azure Machine Learning VS Code-extensie (preview). Zie Set [up Azure Machine Learning VS Code extension (VS Code-extensie instellen) voor meer informatie.](tutorial-setup-vscode-extension.md)
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop voor Mac en Windows
  * Docker-engine voor Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> In Windows moet u [Docker configureren voor het gebruik van Linux-containers.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

> [!TIP]
> Voor Windows is het, hoewel niet vereist, raadzaam om Docker te gebruiken [met Windows-subsysteem voor Linux (WSL) 2.](/windows/wsl/tutorials/wsl-containers#install-docker-desktop)

> [!IMPORTANT]
> Voordat u uw experiment lokaal gaat uitvoeren, moet u ervoor zorgen dat Docker wordt uitgevoerd.

### <a name="debug-experiment-locally"></a>Lokaal fouten opsporen in experiment

1. Open in VS Code de weergave Azure Machine Learning extensie.
1. Vouw het abonnements knooppunt met uw werkruimte uit. Als u nog geen werkruimte hebt, kunt u een Azure Machine Learning [maken](how-to-manage-resources-vscode.md#create-a-workspace) met behulp van de extensie .
1. Vouw uw werkruimte-knooppunt uit.
1. Klik met de rechtermuisknop op **het knooppunt Experimenten** en selecteer **Experiment maken.** Wanneer de prompt wordt weergegeven, geeft u een naam op voor uw experiment.
1. Vouw het **knooppunt Experimenten** uit, klik met de rechtermuisknop op het experiment dat u wilt uitvoeren en selecteer **Experiment uitvoeren.**
1. Selecteer Lokaal in de lijst met opties voor het uitvoeren van **uw** experiment.
1. **Gebruik voor de eerste keer alleen in Windows.** Wanneer u wordt gevraagd om de bestands share toe te staan, selecteert **u Ja.** Wanneer u bestands delen inschakelen, kan Docker de map met uw script aan de container toevoegen. Daarnaast kan Docker de logboeken en uitvoer van uw run opslaan in een tijdelijke map op uw systeem.
1. Selecteer **Ja om** fouten in uw experiment op te sporen. Anders selecteert u **Nee**. Als u Nee selecteert, wordt uw experiment lokaal uitgevoerd zonder dat u het aan het debugger koppelt.
1. Selecteer **Nieuwe configuratie voor uitvoeren maken om** de uitvoeringsconfiguratie te maken. De uitvoeringsconfiguratie definieert het script dat u wilt uitvoeren, afhankelijkheden en gebruikte gegevenssets. Als u er al een hebt, kunt u deze ook selecteren in de vervolgkeuzekeuze.
    1. Kies uw omgeving. U kunt kiezen uit een van de [Azure Machine Learning gecureerd](resource-curated-environments.md) of uw eigen maken.
    1. Geef de naam op van het script dat u wilt uitvoeren. Het pad is relatief ten opzichte van de map die is geopend in VS Code.
    1. Kies of u een Azure Machine Learning wilt gebruiken. U kunt een [Azure Machine Learning maken met behulp](how-to-manage-resources-vscode.md#create-dataset) van de extensie .
    1. Foutopsporing is vereist om het foutopsporingsygger te koppelen aan de container waarin uw experiment wordt uitgevoerd. Als u foutopsporing wilt toevoegen als afhankelijkheid, selecteert **u Foutopsporing toevoegen .** Selecteer anders **Overslaan.** Als u foutopsporing niet toevoegt als een afhankelijkheid, wordt uw experiment uitgevoerd zonder dat u dit aan het foutopsporingsopsporingsexegger koppelt.
    1. Er wordt een configuratiebestand met de configuratie-instellingen voor de uitvoering geopend in de editor. Als u tevreden bent met de instellingen, selecteert u **Experiment verzenden.** U kunt ook het opdrachtenpalet (**View > Command Palette**) openen in de menubalk en de opdracht invoeren in het `Azure ML: Submit experiment` tekstvak.
1. Zodra het experiment is verzonden, wordt er een Docker-installatiebestand met uw script en de configuraties gemaakt die zijn opgegeven in de uitvoeringsconfiguratie.

    Wanneer het bouwproces van de Docker-afbeelding begint, wordt de inhoud van de `60_control_log.txt` bestandsstroom naar de uitvoerconsole in VS Code gestreamd.

    > [!NOTE]
    > De eerste keer dat uw Docker-afbeelding wordt gemaakt, kan dit enkele minuten duren.

1. Zodra de afbeelding is gemaakt, verschijnt er een prompt om het debugger te starten. Stel de onderbrekingspunten in uw script in en selecteer **Start debugger** wanneer u klaar bent om debuggen te starten. Als u dit doet, wordt het VS Code-debugger gekoppeld aan de container waar uw experiment wordt uitgevoerd. U kunt ook in de extensie Azure Machine Learning het knooppunt voor uw huidige run aanwijzen en het pictogram Afspelen selecteren om het debugger te starten.

    > [!IMPORTANT]
    > U kunt niet meerdere foutopsporingssessies voor één experiment hebben. U kunt echter fouten opsporen in twee of meer experimenten met behulp van meerdere VS Code-exemplaren.

Op dit moment moet u uw code stapsgewijs kunnen doorsluizen en fouten opsporen met behulp van VS Code.

Als u de run op een bepaald moment wilt annuleren, klikt u met de rechtermuisknop op uw run-knooppunt en **selecteert u Run annuleren.**

Net als bij externe experimentuitvoer kunt u uw run-knooppunt uitbreiden om de logboeken en uitvoer te inspecteren.

> [!TIP]
> Docker-afbeeldingen die gebruikmaken van dezelfde afhankelijkheden die in uw omgeving zijn gedefinieerd, worden hergebruikt tussen runs. Als u echter een experiment met een nieuwe of een andere omgeving wilt uitvoeren, wordt er een nieuwe afbeelding gemaakt. Omdat deze afbeeldingen worden opgeslagen in uw lokale opslag, is het raadzaam om oude of ongebruikte Docker-afbeeldingen te verwijderen. Als u installatie afbeeldingen van uw systeem wilt verwijderen, gebruikt u [de Docker CLI](https://docs.docker.com/engine/reference/commandline/rmi/) of de [VS Code Docker-extensie](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Fouten in Machine Learning-pijplijnen opsporen en oplossen

In sommige gevallen moet u mogelijk interactief fouten opsporen in de Python-code die wordt gebruikt in uw ML-pijplijn. Door VS Code en debugpy te gebruiken, kunt u aan de code koppelen terwijl deze wordt uitgevoerd in de trainingsomgeving.

### <a name="prerequisites"></a>Vereisten

* Een __Azure Machine Learning werkruimte__ die is geconfigureerd voor het gebruik van een Azure __Virtual Network.__
* Een __Azure Machine Learning pijplijn die__ gebruikmaakt van Python-scripts als onderdeel van de pijplijnstappen. Bijvoorbeeld een PythonScriptStep.
* Een Azure Machine Learning Compute-cluster, dat __zich in het__ virtuele netwerk en wordt gebruikt door de __pijplijn voor het trainen van__.
* Een __ontwikkelomgeving__ in __het virtuele netwerk__. De ontwikkelomgeving kan een van de volgende zijn:

  * Een virtuele Azure-machine in het virtuele netwerk
  * Een reken-exemplaar van notebook-VM in het virtuele netwerk
  * Een clientmachine met een particuliere netwerkverbinding met het virtuele netwerk, hetzij via VPN of via ExpressRoute.

Zie Voor meer informatie over het gebruik van een Azure Virtual Network met Azure Machine Learning, zie [Virtueel netwerkisolatie en privacyoverzicht.](how-to-network-security-overview.md)

> [!TIP]
> Hoewel u kunt werken met Azure Machine Learning resources die zich niet achter een virtueel netwerk, wordt het gebruik van een virtueel netwerk aanbevolen.

### <a name="how-it-works"></a>Uitleg

Met de ml-pijplijnstappen worden Python-scripts uitgevoerd. Deze scripts worden gewijzigd om de volgende acties uit te voeren:

1. Registreer het IP-adres van de host waar ze op worden uitgevoerd. U gebruikt het IP-adres om het debugger te verbinden met het script.

2. Start het foutopsporingsonderdeel debugpy en wacht tot een foutopsporingsopsporingsonderdeel verbinding maakt.

3. Vanuit uw ontwikkelomgeving bewaakt u de logboeken die tijdens het trainingsproces zijn gemaakt om het IP-adres te vinden waarop het script wordt uitgevoerd.

4. U vertelt VS Code het IP-adres om het debugger te verbinden met behulp van een `launch.json` -bestand.

5. U koppelt het debugger en doorsuurt het script interactief.

### <a name="configure-python-scripts"></a>Python-scripts configureren

Als u debuggen wilt inschakelen, moet u de volgende wijzigingen aanbrengen in de Python-script(s) die worden gebruikt door stappen in uw ML-pijplijn:

1. Voeg de volgende importinspraken toe:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Voeg de volgende argumenten toe. Met deze argumenten kunt u het debugger naar behoefte inschakelen en de time-out instellen voor het koppelen van het debugger:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Voeg de volgende -instructies toe. Met deze instructies wordt de context van de huidige run geladen, zodat u het IP-adres kunt logboeken van het knooppunt waarin de code wordt uitgevoerd:

    ```python
    global run
    run = Run.get_context()
    ```

1. Voeg een `if` -instructie toe die begint met foutopsporing en wacht tot een foutopsporingsopsporingsopsporing wordt toegevoegd. Als er geen debugger wordt gekoppeld vóór de time-out, wordt het script gewoon voortgezet. Zorg ervoor dat u de `HOST` waarden en vervangt door uw eigen `PORT` `listen` functie.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

In het volgende Python-voorbeeld ziet u een `train.py` eenvoudig bestand waarmee u kunt debuggen:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML-pijplijn configureren

Als u de Python-pakketten wilt leveren die nodig zijn om foutopsporing te starten en de context van de run op te halen, maakt u een omgeving en stelt u `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` in. Wijzig de SDK-versie in de versie die u gebruikt. In het volgende codefragment wordt gedemonstreerd hoe u een omgeving maakt:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

In de [sectie Python-scripts configureren](#configure-python-scripts) zijn nieuwe argumenten toegevoegd aan de scripts die worden gebruikt door uw ML-pijplijnstappen. In het volgende codefragment wordt gedemonstreerd hoe u deze argumenten gebruikt om debugging voor het onderdeel in te stellen en een time-out in te stellen. Ook wordt gedemonstreerd hoe u de omgeving gebruikt die u eerder hebt gemaakt door in te `runconfig=run_config` stellen:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Wanneer de pijplijn wordt uitgevoerd, maakt elke stap een onderliggende run. Als debuggen is ingeschakeld, registreert het gewijzigde script informatie die vergelijkbaar is met de volgende tekst in de `70_driver_log.txt` voor de onderliggende run:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Sla de waarde `ip_address` op. Deze wordt gebruikt in de volgende sectie.

> [!TIP]
> U kunt ook het IP-adres vinden in de runlogboeken voor de onderliggende run voor deze pijplijnstap. Zie Azure [ML-experiment](how-to-log-view-metrics.md)uitvoeren en metrische gegevens bewaken voor meer informatie over het weergeven van deze informatie.

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Gebruik de volgende opdracht om Debugpy te installeren in uw VS Code-ontwikkelomgeving:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Zie Externe foutopsporing voor meer informatie over het gebruik van foutopsporing [met](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)VS Code.

1. Maak een nieuwe foutopsporingsconfiguratie om VS Code te configureren om te communiceren met de Azure Machine Learning rekenkracht die het foutopsporingsbestand wordt uitgevoerd:

    1. Selecteer in VS Code het menu __Fouten opsporen__ en selecteer __vervolgens Configuraties openen.__ Een bestand met __delaunch.jsop__ wordt geopend.

    1. Zoek in __launch.jsbestand__ de regel die `"configurations": [` bevat en voeg de volgende tekst er achter in. Wijzig de `"host": "<IP-ADDRESS>"` vermelding in het IP-adres dat in uw logboeken uit de vorige sectie is geretourneerd. Wijzig de vermelding in een lokale map die een kopie bevat van het script dat `"localRoot": "${workspaceFolder}/code/step"` wordt ontspord:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Als er al andere vermeldingen in de sectie configuraties staan, voegt u een komma (,) toe na de code die u hebt ingevoegd.

        > [!TIP]
        > De best practice, met name voor pijplijnen, is het bewaren van de resources voor scripts in afzonderlijke directories, zodat code alleen relevant is voor elk van de stappen. In dit voorbeeld `localRoot` verwijst de voorbeeldwaarde naar `/code/step1` .
        >
        > Als u meerdere scripts wilt debuggen, maakt u in verschillende directories een afzonderlijke configuratiesectie voor elk script.

    1. Sla de __launch.jsop in het__ bestand.

### <a name="connect-the-debugger"></a>Verbinding maken met het debugger

1. Open VS Code en open een lokale kopie van het script.
2. Stel onderbrekingspunten in waar u wilt dat het script stopt nadat u het hebt gekoppeld.
3. Terwijl het onderliggende proces het script wordt uitgevoerd en de wordt weergegeven in de logboeken, gebruikt u de toets F5 of `Timeout for debug connection` __selecteert u Fouten opsporen.__ Wanneer u hier om wordt gevraagd, __selecteert Azure Machine Learning compute: configuratie voor externe foutopsporing.__ U kunt ook het pictogram voor foutopsporing selecteren in de zijbalk, de __vermelding Azure Machine Learning:__ remote debug in de vervolgkeuzelijst Fouten opsporen en vervolgens de groene pijl gebruiken om het foutopsporingspictogram te koppelen.

    Op dit moment maakt VS Code verbinding met debugpy op het reken knooppunt en stopt het bij het onderbrekingspunt dat u eerder hebt ingesteld. U kunt nu de code stapsgewijs bekijken terwijl deze wordt uitgevoerd, variabelen weergeven, enzovoort.

    > [!NOTE]
    > Als in het logboek een vermelding wordt weergegeven met de tekst , is de time-out verlopen en wordt `Debugger attached = False` het script voortgezet zonder het debugger. Verzend de pijplijn opnieuw en verbind het debugger na het bericht `Timeout for debug connection` en voordat de time-out verloopt.

## <a name="debug-and-troubleshoot-deployments"></a>Fouten opsporen en problemen met implementaties oplossen

In sommige gevallen moet u mogelijk interactief fouten opsporen in de Python-code in uw modelimplementatie. Bijvoorbeeld als het invoerscript mislukt en de reden niet kan worden bepaald door aanvullende logboekregistratie. Met behulp van VS Code en de foutopsporingsopsporing kunt u koppelen aan de code die wordt uitgevoerd in de Docker-container.

> [!IMPORTANT]
> Deze methode voor het debuggen werkt niet wanneer u `Model.deploy()` en gebruikt om een model lokaal te `LocalWebservice.deploy_configuration` implementeren. In plaats daarvan moet u een afbeelding maken met behulp van de [methode Model.package().](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Voor lokale webservice-implementaties is een werkende Docker-installatie op uw lokale systeem vereist. Zie de Docker-documentatie voor meer informatie over het gebruik [van Docker.](https://docs.docker.com/) Houd er rekening mee dat Docker al is geïnstalleerd wanneer u met reken-exemplaren werkt.

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Gebruik de volgende opdracht om debugpy te installeren in uw lokale VS Code-ontwikkelomgeving:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Zie Externe foutopsporing voor meer informatie over het gebruik van [debugpy met](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)VS Code.

1. Maak een nieuwe foutopsporingsconfiguratie om VS Code te configureren voor communicatie met de Docker-installatier:

    1. Selecteer in VS Code het menu __Foutopsporing__ in __de run-extention__ en selecteer vervolgens __Configuraties openen.__ Een bestand met __delaunch.jsop__ wordt geopend.

    1. Zoek in __launch.jsbestand__ het item __'configuraties'__ (de regel die bevat) en `"configurations": [` voeg de volgende tekst er achter in. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        Na het invoegen moet de __launch.jsop__ het bestand er ongeveer als volgt uit zien:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Als er al andere vermeldingen in de sectie configuraties staan, voegt u een komma ( __,__ ) toe na de code die u hebt ingevoegd.

        In deze sectie wordt gekoppeld aan de Docker-container met behulp van __poort 5678.__

    1. Sla de __launch.jsop in het__ bestand.

### <a name="create-an-image-that-includes-debugpy"></a>Een afbeelding maken die foutopsporing bevat

1. Wijzig de conda-omgeving voor uw implementatie, zodat deze foutopsporing bevat. In het volgende voorbeeld wordt gedemonstreerd hoe u deze toevoegt met behulp van de `pip_packages` parameter :

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Als u foutopsporing wilt starten en wilt wachten op een verbinding wanneer de service wordt gestart, voegt u het volgende toe aan het begin van het `score.py` bestand:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Maak een -afbeelding op basis van de omgevingsdefinitie en haal de -afbeelding naar het lokale register. 

    > [!NOTE]
    > In dit voorbeeld wordt ervan uitgenomen dat naar `ws` Azure Machine Learning werkruimte wijst en dat dit het model is dat wordt `model` geïmplementeerd. Het `myenv.yml` bestand bevat de Conda-afhankelijkheden die in stap 1 zijn gemaakt.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Zodra de afbeelding is gemaakt en gedownload (dit proces kan meer dan 10 minuten duren, dus wacht even met geduld), wordt het pad naar de afbeelding (inclusief opslagplaats, naam en tag, in dit geval ook de samenvatting) ten slotte weergegeven in een bericht dat er ongeveer als volgt uit zien:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Om het eenvoudiger te maken om lokaal met de afbeelding te werken, kunt u de volgende opdracht gebruiken om een tag toe te voegen voor deze afbeelding. Vervang `myimagepath` in de volgende opdracht door de locatiewaarde uit de vorige stap.

    ```bash
    docker tag myimagepath debug:1
    ```

    Voor de rest van de stappen kunt u naar de lokale afbeelding verwijzen als in `debug:1` plaats van de waarde voor het volledige pad naar de afbeelding.

### <a name="debug-the-service"></a>Fouten opsporen in de service

> [!TIP]
> Als u een time-out voor de foutopsporingsverbinding in het bestand in stelt, moet u VS Code verbinden met de foutopsporingssessie voordat `score.py` de time-out verloopt. Start VS Code, open de lokale kopie van , stel een onderbrekingspunt in en gebruik de stappen in deze sectie voordat u verder `score.py` gaat.
>
> Zie Debugging voor meer informatie over het debuggen en instellen [van onderbrekingspunten.](https://code.visualstudio.com/Docs/editor/debugging)

1. Gebruik de volgende opdracht om een Docker-container te starten met behulp van de -afbeelding:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Hiermee koppelt u `score.py` uw lokaal aan het bestand in de container. Wijzigingen die in de editor zijn aangebracht, worden daarom automatisch doorgevoerd in de container

2. Voor een betere ervaring kunt u naar de container gaan met een nieuwe VS-code-interface. Selecteer de omvang in de zijbalk van VS Code en zoek de lokale container die u hebt gemaakt. In deze `Docker` documentatie is dat `debug:1` . Klik met de rechtermuisknop op deze container en selecteer . Vervolgens wordt er automatisch een nieuwe VS Code-interface geopend. Deze interface toont de binnenzijde `"Attach Visual Studio Code"` van de gemaakte container.

    ![De VS Code-interface van de container](./media/how-to-troubleshoot-deployment/container-interface.png)

3. Voer in de container de volgende opdracht uit in de shell

    ```bash
    runsvdir /var/runit
    ```
    Vervolgens ziet u de volgende uitvoer in de shell in uw container:

    ![De uitvoer van de containeruitvoerconsole](./media/how-to-troubleshoot-deployment/container-run.png)

4. Als u VS Code wilt koppelen om fouten op te sporen in de container, opent u VS Code en gebruikt u de toets F5 of __selecteert u Fouten opsporen.__ Wanneer u hier om wordt gevraagd, __selecteert u Azure Machine Learning Implementatie: Docker-foutopsporingsconfiguratie.__ U kunt ook het pictogram Run __extention__ selecteren in de zijbalk, de __vermelding Azure Machine Learning Deployment: Docker Debug__ in de vervolgkeuzelijst Foutopsporing en vervolgens de groene pijl gebruiken om het foutopsporingspictogram te koppelen.

    ![Het foutopsporingspictogram, de foutopsporingsknop en de configuratie-selector](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Nadat u op de groene pijl hebt geklikt en het debugger hebt gekoppeld, ziet u in de VS Code-interface van de container enkele nieuwe informatie:
    
    ![De gekoppelde gegevens van het containerdebugger](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    In uw belangrijkste VS Code-interface ziet u ook het volgende:

    ![Het VS Code-onderbrekingspunt in score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

En nu is de lokale die is gekoppeld aan de container, al gestopt bij de `score.py` onderbrekingspunten waar u hebt ingesteld. Op dit moment maakt VS Code verbinding met debugpy in de Docker-container en wordt de Docker-container gestopt op het onderbrekingspunt dat u eerder hebt ingesteld. U kunt nu de code stapsgewijs bekijken terwijl deze wordt uitgevoerd, variabelen bekijken, enzovoort.

Zie Fouten opsporen in uw [Python-code](https://code.visualstudio.com/docs/python/debugging)voor meer informatie over het gebruik van VS Code voor het opsporen van fouten in Python.

### <a name="stop-the-container"></a>De container stoppen

Gebruik de volgende opdracht om de container te stoppen:

```bash
docker stop debug
```

## <a name="next-steps"></a>Volgende stappen

Nu u VS Code Remote hebt ingesteld, kunt u een reken-exemplaar gebruiken als externe rekenkracht van VS Code om interactief fouten in uw code op te sporen. 

Meer informatie over probleemoplossing:

* [Implementatie van lokaal model](how-to-troubleshoot-deployment-local.md)
* [Implementatie van extern model](how-to-troubleshoot-deployment.md)
* [Machine Learning-pijplijnen](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

