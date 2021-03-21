---
title: Interactieve fout opsporing met Visual Studio code
titleSuffix: Azure Machine Learning
description: Interactief fouten opsporen Azure Machine Learning code, pijp lijnen en implementaties met Visual Studio code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 783b5afdaef369582614cde3525f7968fdb5e567
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508636"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interactieve fout opsporing met Visual Studio code



Leer hoe u interactief fouten opspoort Azure Machine Learning experimenten, pijp lijnen en implementaties met Visual Studio code (VS code) en [debugpy](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Experimenten lokaal uitvoeren en fouten opsporen

Gebruik de Azure Machine Learning-extensie om uw machine learning experimenten te valideren, uit te voeren en fouten op te sporen voordat u deze naar de Cloud verzendt.

### <a name="prerequisites"></a>Vereisten

* Azure Machine Learning VS code-extensie (preview-versie). Zie [Azure machine learning VS code-extensie instellen](tutorial-setup-vscode-extension.md)voor meer informatie.
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop voor Mac en Windows
  * Docker-engine voor Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> Zorg ervoor dat u in Windows [docker configureert voor het gebruik van Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> Voor Windows, hoewel dit niet vereist is, is het raadzaam [docker te gebruiken met het Windows-subsysteem voor Linux (WSL) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> Voordat u uw experiment lokaal uitvoert, moet u ervoor zorgen dat docker actief is.

### <a name="debug-experiment-locally"></a>Debug experiment lokaal

1. Open in VS code de weer gave Azure Machine Learning extensie.
1. Vouw het knoop punt abonnement met uw werk ruimte uit. Als u er nog geen hebt, kunt u [een Azure machine learning-werk ruimte maken](how-to-manage-resources-vscode.md#create-a-workspace) met behulp van de extensie.
1. Vouw het knoop punt van de werk ruimte uit.
1. Klik met de rechter muisknop op het knoop punt **experimenten** en selecteer **experiment maken**. Wanneer de prompt wordt weer gegeven, geeft u een naam op voor uw experiment.
1. Vouw het knoop punt **experimenten** uit, klik met de rechter muisknop op het experiment dat u wilt uitvoeren en selecteer **Run experimenten**.
1. Selecteer **lokaal** in de lijst met opties om uw experiment uit te voeren.
1. De **eerste keer alleen in Windows gebruiken**. Selecteer **Ja** als u wordt gevraagd of u de bestands share wilt toestaan. Wanneer u bestands share inschakelt, kan docker de directory met uw script koppelen aan de container. Daarnaast kunt u met docker de logboeken en uitvoer van de uitvoering opslaan in een tijdelijke map op uw systeem.
1. Selecteer **Ja** om het experiment op te lossen. Anders selecteert u **Nee**. Als u Nee kiest, wordt uw experiment lokaal uitgevoerd zonder dat dit aan het fout opsporingsprogramma is gekoppeld.
1. Selecteer **nieuwe uitvoerings configuratie maken** om uw uitvoerings configuratie te maken. De uitvoerings configuratie definieert het script dat u wilt uitvoeren, afhankelijkheden en gegevens sets die worden gebruikt. Als u er al een hebt, selecteert u deze in de vervolg keuzelijst.
    1. Kies uw omgeving. U kunt kiezen uit een van de [Azure machine learning](resource-curated-environments.md) met de curator of uw eigen maken.
    1. Geef de naam op van het script dat u wilt uitvoeren. Het pad is relatief ten opzichte van de map die in VS code is geopend.
    1. Kies of u een Azure Machine Learning gegevensset wilt gebruiken of niet. U kunt [Azure machine learning gegevens sets](how-to-manage-resources-vscode.md#create-dataset) maken met behulp van de extensie.
    1. Debugpy is vereist om het fout opsporingsprogramma te koppelen aan de container die uw experiment uitvoert. Als u debugpy wilt toevoegen als een afhankelijkheid, selecteert u **Debugpy toevoegen**. Anders selecteert u **overs Laan**. Als u debugpy niet toevoegt als afhankelijkheid, wordt uw experiment uitgevoerd zonder dat het fout opsporingsprogramma is gekoppeld.
    1. Een configuratie bestand met de instellingen voor het uitvoeren van de configuratie wordt geopend in de editor. Als u tevreden bent met de instellingen, selecteert u **experiment verzenden**. U kunt ook het opdracht palet openen (**> opdracht palet weer geven**) in de menu balk en de `Azure ML: Submit experiment` opdracht invoeren in het tekstvak.
1. Zodra het experiment is verzonden, wordt een docker-installatie kopie met uw script en de configuraties die zijn opgegeven in de configuratie van de uitvoering, gemaakt.

    Wanneer het proces voor het bouwen van de docker-installatie kopie wordt gestart, wordt de inhoud van de `60_control_log.txt` Bestands stroom naar de uitvoer console in VS code.

    > [!NOTE]
    > De eerste keer dat uw docker-installatie kopie wordt gemaakt, kan het enkele minuten duren.

1. Zodra uw installatie kopie is gemaakt, wordt er een prompt weer gegeven om het fout opsporingsprogramma te starten. Stel de onderbrekings punten in het script in en selecteer **fout opsporing starten** wanneer u klaar bent om de fout opsporing te starten. Hiermee koppelt u het fout opsporingsprogramma VS code aan de container met uw experiment. U kunt ook in de uitbrei ding Azure Machine Learning de muis aanwijzer boven het knoop punt voor de huidige uitvoering en het pictogram afspelen selecteren om het fout opsporingsprogramma te starten.

    > [!IMPORTANT]
    > U kunt niet meerdere foutopsporingssessie voor één experiment hebben. U kunt in twee of meer experimenten echter fouten opsporen met behulp van meerdere exemplaren van VS code.

Op dit moment moet u uw code kunnen stapsgewijs door lopen en fouten opsporen met behulp van VS code.

Als u de uitvoering wilt annuleren, klikt u met de rechter muisknop op het knoop punt uitvoeren en selecteert u **uitvoeren annuleren**.

Net als bij extern experimenteren, kunt u het knoop punt uitvoeren uitvouwen om de logboeken en uitvoer te controleren.

> [!TIP]
> Docker-installatie kopieën die gebruikmaken van de afhankelijkheden die in uw omgeving zijn gedefinieerd, worden tussen uitvoeringen opnieuw gebruikt. Als u echter een experiment uitvoert met een nieuwe of andere omgeving, wordt er een nieuwe installatie kopie gemaakt. Omdat deze installatie kopieën worden opgeslagen in uw lokale opslag, kunt u het beste oude of ongebruikte docker-installatie kopieën verwijderen. Als u installatie kopieën van uw systeem wilt verwijderen, gebruikt u de docker- [cli](https://docs.docker.com/engine/reference/commandline/rmi/) of de [VS code docker-extensie](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Fouten in Machine Learning-pijplijnen opsporen en oplossen

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die wordt gebruikt in uw ML-pijp lijn. U kunt met behulp van VS code en debugpy koppelen aan de code zoals deze wordt uitgevoerd in de trainings omgeving.

### <a name="prerequisites"></a>Vereisten

* Een __Azure machine learning-werk ruimte__ die is geconfigureerd voor het gebruik van een __Azure Virtual Network__.
* Een __Azure machine learning pijp lijn__ die gebruikmaakt van python-scripts als onderdeel van de pijplijn stappen. Bijvoorbeeld een PythonScriptStep.
* Een Azure Machine Learning Compute-Cluster, dat zich __in het virtuele netwerk__ bevindt en wordt __gebruikt door de pijp lijn voor training__.
* Een __ontwikkel omgeving__ die zich __in het virtuele netwerk__ bevindt. De ontwikkel omgeving kan een van de volgende zijn:

  * Een virtuele machine van Azure in het virtuele netwerk
  * Een reken instantie van een notebook-VM in het virtuele netwerk
  * Een client computer met een particuliere netwerk verbinding met het virtuele netwerk, hetzij via VPN of via ExpressRoute.

Zie [Virtual Network-isolatie en privacy overview](how-to-network-security-overview.md)voor meer informatie over het gebruik van een Azure-Virtual Network met Azure machine learning.

> [!TIP]
> Hoewel u kunt werken met Azure Machine Learning-resources die zich niet achter een virtueel netwerk bevinden, wordt het gebruik van een virtueel netwerk aanbevolen.

### <a name="how-it-works"></a>Uitleg

Met uw ML pijplijn stappen voert u python-scripts uit. Deze scripts zijn gewijzigd om de volgende acties uit te voeren:

1. Registreer het IP-adres van de host waarop ze worden uitgevoerd. U gebruikt het IP-adres om de fout opsporing aan het script te koppelen.

2. Start het onderdeel debugpy debug en wacht tot er een fout opsporingsprogramma is om verbinding te maken.

3. Vanuit uw ontwikkel omgeving bewaakt u de logboeken die zijn gemaakt door het trainings proces om het IP-adres te vinden waarop het script wordt uitgevoerd.

4. U vertelt het IP-adres om het fout opsporingsprogramma te verbinden met behulp van een `launch.json` bestand.

5. U koppelt het fout opsporingsprogramma en interactieve stap door het script.

### <a name="configure-python-scripts"></a>Python-scripts configureren

Als u fout opsporing wilt inschakelen, moet u de volgende wijzigingen aanbrengen in de python-script (s) die worden gebruikt door de stappen in uw ML-pijp lijn:

1. Voeg de volgende import instructies toe:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Voeg de volgende argumenten toe. Met deze argumenten kunt u de debugger zo nodig inschakelen en de time-out voor het koppelen van het fout opsporingsprogramma instellen:

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

1. Voeg de volgende-instructies toe. Met deze instructies wordt de huidige uitvoerings context geladen, zodat u het IP-adres van het knoop punt waarop de code wordt uitgevoerd, kunt vastleggen:

    ```python
    global run
    run = Run.get_context()
    ```

1. Voeg een `if` instructie toe die debugpy start en wacht tot een fout opsporingsprogramma is gekoppeld. Als er voor de time-out geen fout opsporingsprogramma is gekoppeld, wordt het script gewoon voortgezet. Zorg ervoor dat u de `HOST` waarden en vervangt door `PORT` `listen` uw eigen waarde.

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

In het volgende python-voor beeld ziet u een eenvoudig `train.py` bestand dat fout opsporing mogelijk maakt:

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

### <a name="configure-ml-pipeline"></a>ML-pijp lijn configureren

Als u de Python-pakketten wilt opgeven die nodig zijn om debugpy te starten en de uitvoerings context op te halen, moet u een omgeving maken en instellen `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Wijzig de SDK-versie zodat deze overeenkomt met die die u gebruikt. Het volgende code fragment laat zien hoe u een omgeving maakt:

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

In de sectie [python-scripts configureren](#configure-python-scripts) zijn nieuwe argumenten toegevoegd aan de scripts die worden gebruikt door uw ml-pijplijn stappen. Het volgende code fragment laat zien hoe u deze argumenten kunt gebruiken om fout opsporing in te scha kelen voor het onderdeel en om een time-out in te stellen. Ook wordt gedemonstreerd hoe u de omgeving die u eerder hebt gemaakt, kunt gebruiken door het volgende in te stellen `runconfig=run_config` :

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

Wanneer de pijp lijn wordt uitgevoerd, maakt elke stap een onderliggende uitvoering. Als fout opsporing is ingeschakeld, wordt in het gewijzigde script informatie die lijkt op de volgende tekst in de `70_driver_log.txt` voor de onderliggende uitvoering:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Sla de `ip_address` waarde op. Deze wordt gebruikt in de volgende sectie.

> [!TIP]
> U kunt ook het IP-adres van de uitvoerings logboeken voor de onderliggende uitvoering van deze pijplijn stap vinden. Zie voor meer informatie over het weer geven van deze informatie [Azure ml experimenten en metrische gegevens bewaken](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Gebruik de volgende opdracht om debugpy te installeren op uw VS code Development Environment:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Zie [fout opsporing op afstand](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)voor meer informatie over het gebruik van DEBUGPY met VS-code.

1. Als u VS code wilt configureren om te communiceren met de Azure Machine Learning Compute waarop de fout opsporing wordt uitgevoerd, maakt u een nieuwe configuratie voor fout opsporing:

    1. Selecteer in VS code het menu __fout opsporing__ en selecteer vervolgens __Open configuraties__. Er wordt een bestand met de naam __launch.js__ geopend.

    1. Zoek in het bestand __launch.jsop__ de regel die bevat `"configurations": [` en voeg de volgende tekst toe. Wijzig de `"host": "<IP-ADDRESS>"` vermelding in het IP-adres dat in uw logboeken wordt weer gegeven in de vorige sectie. Wijzig de `"localRoot": "${workspaceFolder}/code/step"` vermelding in een lokale map met een kopie van het script waarin fouten worden opgespoord:

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
        > De best practice, met name voor pijp lijnen, is om de resources voor scripts in afzonderlijke directory's te blijven, zodat de code alleen relevant is voor elk van de stappen. In dit voor beeld wordt `localRoot` naar de voorbeeld waarde verwezen `/code/step1` .
        >
        > Als u fouten opspoort in meerdere scripts, maakt u in verschillende directory's een afzonderlijke configuratie sectie voor elk script.

    1. Sla de __launch.jsop in__ het bestand.

### <a name="connect-the-debugger"></a>De debugger verbinden

1. Open VS code en open een lokale kopie van het script.
2. Stel onderbrekings punten in waar het script moet worden gestopt zodra u het hebt gekoppeld.
3. Terwijl het onderliggende proces het script uitvoert en de `Timeout for debug connection` wordt weer gegeven in de logboeken, gebruikt u de F5-toets of selecteert u __fout opsporing__. Wanneer u hierom wordt gevraagd, selecteert u de __Azure machine learning berekenen: configuratie van externe fout opsporing__ . U kunt ook het pictogram voor fout opsporing selecteren in de zijbalk, het __Azure machine learning: externe fout opsporing__ in het vervolg keuzemenu voor fout opsporing en vervolgens de groene pijl gebruiken om het fout opsporingsprogramma te koppelen.

    Op dit punt verbindt de VS code met debugpy op het reken knooppunt en stopt dit met het onderbrekings punt dat u eerder hebt ingesteld. U kunt nu de code door lopen terwijl deze wordt uitgevoerd, variabelen weer geven, enzovoort.

    > [!NOTE]
    > Als in het logboek een vermelding wordt weer gegeven `Debugger attached = False` , is de time-out verlopen en wordt het script voortgezet zonder het fout opsporingsprogramma. Dien de pijp lijn opnieuw in en sluit de debugger na het `Timeout for debug connection` bericht en voordat de time-out is verlopen.

## <a name="debug-and-troubleshoot-deployments"></a>Problemen opsporen en oplossingen oplossen

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die in uw model implementatie is opgenomen. Als het script voor de vermelding bijvoorbeeld mislukt en de reden niet kan worden bepaald door aanvullende logboek registratie. U kunt met behulp van VS code en de debugpy koppelen aan de code die wordt uitgevoerd in de docker-container.

> [!IMPORTANT]
> Deze methode van fout opsporing werkt niet wanneer u `Model.deploy()` `LocalWebservice.deploy_configuration` een model gebruikt en lokaal implementeert. In plaats daarvan moet u een installatie kopie maken met behulp van de methode [model. package ()](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-) .

Voor lokale web service-implementaties is een werkende docker-installatie op uw lokale systeem vereist. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het gebruik van docker. Houd er rekening mee dat bij het werken met reken instanties al docker is geïnstalleerd.

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Gebruik de volgende opdracht om debugpy te installeren op uw lokale en ontwikkelings omgeving:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Zie [fout opsporing op afstand](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)voor meer informatie over het gebruik van DEBUGPY met VS-code.

1. Als u VS code wilt configureren om met de docker-installatie kopie te communiceren, maakt u een nieuwe configuratie voor fout opsporing:

    1. Selecteer in VS code het menu __fout opsporing__ in de uitbrei ding van de __uitvoering__ en selecteer vervolgens __Open configuraties__. Er wordt een bestand met de naam __launch.js__ geopend.

    1. Zoek in het bestand __launch.jsop__ het item __' configuraties '__ (de regel die bevat `"configurations": [` ) en voeg de volgende tekst toe. 

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
        Na het invoegen moet de __launch.jsop__ het bestand er ongeveer als volgt uitzien:
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

        Deze sectie wordt gekoppeld aan de docker-container via poort __5678__.

    1. Sla de __launch.jsop in__ het bestand.

### <a name="create-an-image-that-includes-debugpy"></a>Een installatie kopie maken die debugpy bevat

1. Wijzig de Conda-omgeving voor uw implementatie, zodat deze debugpy bevat. In het volgende voor beeld ziet u hoe u het toevoegt met behulp van de `pip_packages` para meter:

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

1. Als u debugpy wilt starten en wilt wachten op een verbinding wanneer de service wordt gestart, voegt u het volgende toe aan de bovenkant van het `score.py` bestand:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Maak een installatie kopie op basis van de omgevings definitie en haal de installatie kopie op in het lokale REGI ster. 

    > [!NOTE]
    > In dit voor beeld wordt ervan uitgegaan dat `ws` naar uw Azure machine learning-werk ruimte verwijst en dat `model` het model dat wordt geïmplementeerd. Het `myenv.yml` bestand bevat de Conda-afhankelijkheden die zijn gemaakt in stap 1.

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

    Nadat de installatie kopie is gemaakt en gedownload (dit proces kan meer dan tien minuten duren, dus wacht een ogen blik), het pad naar de installatie kopie (inclusief opslag plaats, naam en tag, in dit geval ook wel de samen vatting) wordt weer gegeven in een bericht dat lijkt op het volgende:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Om het gemakkelijker te maken met de installatie kopie, kunt u de volgende opdracht gebruiken om een tag voor deze installatie kopie toe te voegen. Vervang `myimagepath` in de volgende opdracht door de locatie waarde uit de vorige stap.

    ```bash
    docker tag myimagepath debug:1
    ```

    Voor de rest van de stappen kunt u de lokale installatie kopie `debug:1` gebruiken in plaats van de waarde van het volledige pad naar de afbeelding.

### <a name="debug-the-service"></a>Fouten opsporen in de service

> [!TIP]
> Als u een time-out instelt voor de debugpy-verbinding in het `score.py` bestand, moet u de VS-code verbinden met de foutopsporingssessie voordat de time-out verloopt. Start VS code, open het lokale exemplaar van `score.py` , stel een onderbrekings punt in en laat het gereed om door te gaan voordat u de stappen in deze sectie uitvoert.
>
> Zie [fout opsporing](https://code.visualstudio.com/Docs/editor/debugging)voor meer informatie over het opsporen van fouten en het instellen van onderbrekings punten.

1. Gebruik de volgende opdracht om een docker-container met behulp van de installatie kopie te starten:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Hiermee koppelt `score.py` u uw lokale map aan de container. Wijzigingen die in de editor zijn aangebracht, worden daarom automatisch weer gegeven in de container

2. Voor een betere ervaring kunt u met een nieuwe VS code-interface naar de container gaan. Selecteer de `Docker` uitbrei ding van de VS-code balk, zoek de lokale container die u hebt gemaakt, in deze documentatie `debug:1` . Klik met de rechter muisknop op deze container en selecteer en `"Attach Visual Studio Code"` vervolgens wordt er automatisch een nieuwe VS code-interface geopend. deze interface toont de binnenkant van de gemaakte container.

    ![De container VS code interface](./media/how-to-troubleshoot-deployment/container-interface.png)

3. Voer in de container de volgende opdracht uit in de shell

    ```bash
    runsvdir /var/runit
    ```
    Vervolgens ziet u de volgende uitvoer in de shell in uw container:

    ![De container uitvoer console uitvoeren](./media/how-to-troubleshoot-deployment/container-run.png)

4. Als u VS code aan debugpy in de container wilt koppelen, opent u VS code en gebruikt u de toets F5 of selecteert u __fout opsporing__. Wanneer u hierom wordt gevraagd, selecteert u de __Azure machine learning implementatie: docker debug__ -configuratie. U kunt ook het pictogram uitbrei ding van __uitvoer__ in de zijbalk selecteren, de __Azure machine learning implementatie: docker debug__ -vermelding in het vervolg keuzemenu voor fout opsporing en vervolgens de groene pijl gebruiken om het fout opsporingsprogramma te koppelen.

    ![Het pictogram fout opsporing, de knop fout opsporing starten en de configuratie kiezer](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Nadat u op de groene pijl hebt geklikt en de debugger hebt gekoppeld, kunt u in de container VS code-interface enkele nieuwe gegevens zien:
    
    ![Er is informatie toegevoegd aan het fout opsporingsprogramma voor containers](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    Wat u kunt zien, vindt u in uw belangrijkste VS-code Interface:

    ![Het VS-code-onderbrekings punt in score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

En nu is het lokale onderdeel `score.py` dat is gekoppeld aan de container, al gestopt op het onderbrekings punt waar u instelt. Op dit punt verbindt de VS code met debugpy in de docker-container en stopt de docker-container met het onderbrekings punt dat u eerder hebt ingesteld. U kunt nu de code door lopen terwijl deze wordt uitgevoerd, variabelen weer geven, enzovoort.

Zie [fouten opsporen in uw Python-code](https://code.visualstudio.com/docs/python/debugging)voor meer informatie over het gebruik van VS code voor het opsporen van problemen met python.

### <a name="stop-the-container"></a>De container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht:

```bash
docker stop debug
```

## <a name="next-steps"></a>Volgende stappen

Nu u versus externe code hebt ingesteld, kunt u een reken instantie gebruiken als externe Compute van VS code om uw code interactief te debuggen. 

Meer informatie over het oplossen van problemen:

* [Implementatie van lokaal model](how-to-troubleshoot-deployment-local.md)
* [Implementatie van extern model](how-to-troubleshoot-deployment.md)
* [Machine Learning-pijplijnen](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

