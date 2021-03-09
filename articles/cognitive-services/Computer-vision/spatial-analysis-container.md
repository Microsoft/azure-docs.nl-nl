---
title: De container voor ruimtelijke analyse installeren en uitvoeren-Computer Vision
titleSuffix: Azure Cognitive Services
description: Met de container voor ruimtelijke analyse kunt u personen en afstanden detecteren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 9989c6ea6b75203d43c37854caef7fdcbc321779
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519024"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>De container voor ruimtelijke analyse installeren en uitvoeren (preview-versie)

De container voor ruimtelijke analyse biedt u de mogelijkheid om in realtime streaming video te analyseren om ruimtelijke relaties tussen personen, hun beweging en interacties met objecten in fysieke omgevingen te begrijpen. Containers zijn ideaal voor specifieke vereisten voor beveiliging en gegevensbeheer.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" maakt u een computer vision resource "  target="_blank"> een computer vision resource maken </a> voor de Standard S1-laag in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de container voor ruimtelijke analyse uit te voeren. U gaat later uw sleutel en eind punt gebruiken.


### <a name="spatial-analysis-container-requirements"></a>Container vereisten voor ruimtelijke analyse

Als u de container voor ruimtelijke analyse wilt uitvoeren, hebt u een compute-apparaat nodig met een [Nvidia Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). U wordt aangeraden [Azure stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) met GPU-versnelling te gebruiken, maar de container wordt uitgevoerd op een andere computer die voldoet aan de minimale vereisten. We verwijzen naar dit apparaat als de hostcomputer.

#### <a name="azure-stack-edge-device"></a>[Azure Stack edge-apparaat](#tab/azure-stack-edge)

Azure Stack Edge is een hardware-as-a-service-oplossing en een met AI ingeschakeld Edge computing-apparaat met mogelijkheden voor netwerk gegevens overdracht. Zie de [documentatie van Azure stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md)voor gedetailleerde instructies voor voor bereiding en installatie.

#### <a name="desktop-machine"></a>[Desktop computer](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Minimale hardwarevereisten

* 4 GB RAM-geheugen
* 4 GB aan GPU-RAM
* 8-core CPU
* 1 NVIDIA Tesla T4 GPU
* 20 GB aan HDD-ruimte

#### <a name="recommended-hardware"></a>Aanbevolen hardware

* 32 GB systeem-RAM
* 16 GB aan GPU-RAM
* 8-core CPU
* 2 NVIDIA Tesla T4-Gpu's
* 50 GB SSD-ruimte

In dit artikel downloadt en installeert u de volgende software pakketten. De hostcomputer moet het volgende kunnen uitvoeren (zie hieronder voor instructies):

* [NVIDIA grafische Stuur Programma's](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) en [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Configuraties voor [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (multi-process-service).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) en [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IOT Edge](../../iot-edge/how-to-install-iot-edge.md) runtime.

#### <a name="azure-vm-with-gpu"></a>[Azure VM met GPU](#tab/virtual-machine)
In ons voor beeld gebruiken we een VM- [serie](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) met één K80-GPU.

---

| Vereiste | Beschrijving |
|--|--|
| Camera | De container voor ruimtelijke analyse is niet gebonden aan een specifiek camera merk. Het camera apparaat moet: ondersteuning bieden voor Real-Time streaming protocol (RTSP) en H. 264-code ring, toegankelijk zijn voor de hostcomputer en kunnen worden gestreamd op 15FPS en 1080p-resolutie. |
| Linux-besturings systeem | [Ubuntu Desktop 18,04 LTS](http://releases.ubuntu.com/18.04/) moet zijn geïnstalleerd op de hostcomputer.  |


## <a name="request-approval-to-run-the-container"></a>Goed keuring aanvragen om de container uit te voeren

Vul het [aanvraag formulier](https://aka.ms/csgate) in en verzend het om goed keuring te vragen om de container uit te voeren.

Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de container gaat gebruiken. Nadat u het formulier hebt verzonden, wordt het door het Azure Cognitive Services-team gecontroleerd en een e-mail bericht met een beslissing verzonden.

> [!IMPORTANT]
> * Op het formulier moet u een e-mail adres gebruiken dat is gekoppeld aan een Azure-abonnements-ID.
> * De Computer Vision resource die u gebruikt om de container uit te voeren, moet zijn gemaakt met de goedgekeurde Azure-abonnements-ID.

Nadat u bent goedgekeurd, kunt u de container uitvoeren nadat u deze hebt gedownload via de micro soft-Container Registry (MCR), die verderop in dit artikel wordt beschreven.

U kunt de container niet uitvoeren als uw Azure-abonnement niet is goedgekeurd.

## <a name="set-up-the-host-computer"></a>De hostcomputer instellen

Het is raadzaam om een Azure Stack edge-apparaat te gebruiken voor uw hostcomputer. Klik op **Bureau computer** als u een ander apparaat (of **virtuele machine** ) wilt configureren als u een VM gebruikt.

#### <a name="azure-stack-edge-device"></a>[Azure Stack edge-apparaat](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Reken kracht configureren op de Azure Stack Edge-Portal 
 
Ruimtelijke analyse maakt gebruik van de reken functies van de Azure Stack Edge om een AI-oplossing uit te voeren. Als u de reken functies wilt inschakelen, moet u het volgende doen: 

* U hebt [verbinding gemaakt en](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) uw Azure stack edge-apparaat geactiveerd. 
* U hebt een Windows-client systeem waarop Power shell 5,0 of hoger wordt uitgevoerd om toegang te krijgen tot het apparaat.  
* Als u een Kubernetes-cluster wilt implementeren, moet u uw Azure Stack edge-apparaat configureren via de **lokale gebruikers interface** van de [Azure Portal](https://portal.azure.com/): 
  1. De compute-functie op uw Azure Stack edge-apparaat inschakelen. Als u Compute wilt inschakelen, gaat u naar de **Compute** -pagina in de webinterface voor uw apparaat. 
  2. Selecteer een netwerk interface die u wilt inschakelen voor Compute en klik vervolgens op **inschakelen**. Hiermee maakt u een virtuele switch op het apparaat op die netwerk interface.
  3. Laat de IP-adressen van het test knooppunt van de Kubernetes en de IP-adressen van de Kubernetes externe services leeg.
  4. Klik op **Toepassen**. Deze bewerking kan ongeveer twee minuten duren. 

![Rekenproces configureren](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Een Edge Compute Role instellen en een IoT Hub resource maken

Navigeer in het [Azure Portal](https://portal.azure.com/)naar uw Azure stack Edge-resource. Klik op de pagina **overzicht** of navigatie lijst op de knop rand berekening **aan de slag** . Klik in de tegel  **Edge Compute configureren**   op **configureren**. 

![Koppeling](media/spatial-analysis/configure-edge-compute-tile.png)

Kies op de pagina **rand berekening configureren**   een bestaand IOT hub of kies ervoor om een nieuw item te maken. Standaard wordt een standaard prijs categorie (S1) gebruikt voor het maken van een IoT Hub bron. Als u een gratis laag IoT Hub resource wilt gebruiken, maakt u er een en selecteert u deze. De IoT Hub resource gebruikt hetzelfde abonnement en dezelfde resource groep die wordt gebruikt door de resource Azure Stack Edge 

Klik op **Create**. Het maken van IoT Hub bronnen kan een paar minuten duren. Nadat de IoT Hub resource is gemaakt, wordt de tegel **Edge Compute** bijgewerkt om de nieuwe configuratie weer te geven. Als u wilt bevestigen dat de rol Edge Compute is geconfigureerd, selecteert u **configuratie weer geven** op de tegel **Compute configureren**   .

Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. De Azure IoT Edge runtime wordt al uitgevoerd op het IoT Edge apparaat.

> [!NOTE]
> * Momenteel wordt alleen het Linux-platform ondersteund voor IoT Edge apparaten. Zie het artikel [logboek registratie en probleem oplossing](spatial-analysis-logging.md) voor hulp bij het oplossen van problemen met het Azure stack edge-apparaat.
> * Zie [een IOT edge apparaat configureren om te communiceren via een proxy](../../iot-edge/how-to-configure-proxy-support.md#azure-portal) server voor meer informatie over het configureren van een IOT edge apparaat om te communiceren via een proxy server

###  <a name="enable-mps-on-azure-stack-edge"></a>MPS inschakelen op Azure Stack rand 

1. Een Windows Power shell-sessie uitvoeren als beheerder. 

2. Zorg ervoor dat de Windows Remote Management-service wordt uitgevoerd op de client. Gebruik in de Power shell-terminal de volgende opdracht: 
    
    ```powershell
    winrm quickconfig
    ```
    
    Als er waarschuwingen over een firewall uitzondering worden weer gegeven, controleert u het type netwerk verbinding en raadpleegt u de documentatie van [Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) .

3. Wijs een variabele toe aan het IP-adres van het apparaat. 
    
    ```powershell
    $ip = "<device-IP-address>" 
    ```
    
4. Gebruik de volgende opdracht om het IP-adres van uw apparaat toe te voegen aan de lijst met vertrouwde hosts van de client: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Start een Windows Power shell-sessie op het apparaat. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Geef het wacht woord op wanneer u hierom wordt gevraagd. Gebruik hetzelfde wacht woord dat wordt gebruikt om u aan te melden bij de lokale webgebruikersinterface. Het standaard wacht woord voor de lokale webgebruikersinterface is `Password1` .

Typ `Start-HcsGpuMPS` om de MPS-service op het apparaat te starten. 

Zie [problemen oplossen met het Azure stack Edge](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) -apparaat voor hulp bij het oplossen van problemen met het Azure stack edge-apparaat 

#### <a name="desktop-machine"></a>[Desktop computer](#tab/desktop-machine)

Volg deze instructies als uw hostcomputer geen Azure Stack edge-apparaat is.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>NVIDIA CUDA Toolkit en NVIDIA grafische Stuur Programma's op de hostcomputer installeren

Gebruik het volgende bash-script om de vereiste NVIDIA grafische Stuur Programma's en CUDA Toolkit te installeren.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Start de machine opnieuw op en voer de volgende opdracht uit.

```bash
nvidia-smi
```

De volgende uitvoer wordt weergegeven.

![Uitvoer van NVIDIA-stuur programma](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Docker CE en NVIDIA-docker2 installeren op de hostcomputer

Installeer docker CE op de hostcomputer.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Installeer het *NVIDIA-docker-2-* software pakket.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>NVIDIA MPS inschakelen op de hostcomputer

> [!TIP]
> * Installeer MPS niet als uw GPU-reken capaciteit lager is dan 7. x (pre-Volta). Zie [CUDA-compatibiliteit](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) voor naslag informatie. 
> * Voer de MPS-instructies uit vanuit een Terminal venster op de hostcomputer. Niet binnen uw docker-container exemplaar.

Voor de beste prestaties en het gebruik van kunt u de GPU ('s) van de hostcomputer configureren voor de [NVIDIA multi processor-service (MPS)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Voer de MPS-instructies uit vanuit een Terminal venster op de hostcomputer.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Azure IoT Edge op de hostcomputer configureren

Als u de container voor ruimtelijke analyse op de hostcomputer wilt implementeren, moet u een exemplaar van een [Azure IOT hub](../../iot-hub/iot-hub-create-through-portal.md) -service maken met behulp van de prijs categorie Standard (S1) of gratis (F0). 

Gebruik de Azure CLI om een exemplaar van Azure IoT Hub te maken. Vervang de para meters, indien van toepassing. U kunt de Azure-IoT Hub ook maken op de [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Zie [regio ondersteuning](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) voor beschik bare regio's.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

U moet [Azure IOT Edge](../../iot-edge/how-to-install-iot-edge.md) versie 1.0.9 installeren. Volg deze stappen om de juiste versie te downloaden:

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Kopieer de gegenereerde lijst.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installeer de openbare Microsoft GPG-sleutel.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Werk de pakket lijsten op het apparaat bij.

```bash
sudo apt-get update
```

Installeer de 1.0.9-release:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Registreer vervolgens de hostcomputer als IoT Edge apparaat in uw IoT Hub-exemplaar met behulp van een [Connection String](../../iot-edge/how-to-register-device.md?view=iotedge-2018-06).

U moet het IoT Edge apparaat verbinden met uw Azure-IoT Hub. U moet de connection string kopiëren van het IoT Edge apparaat dat u eerder hebt gemaakt. U kunt ook de onderstaande opdracht uitvoeren in de Azure CLI.

```bash
sudo az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

Open op de hostcomputer  `/etc/iotedge/config.yaml` om te bewerken. Vervang door `ADD DEVICE CONNECTION STRING HERE` de Connection String. Sla het bestand op en sluit het. Voer deze opdracht uit om de IoT Edge-service op de hostcomputer opnieuw op te starten.

```bash
sudo systemctl restart iotedge
```

Implementeer de container voor ruimtelijke analyse als een IoT-module op de hostcomputer, hetzij van de [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) of [Azure cli](../cognitive-services-apis-create-account-cli.md?tabs=windows). Als u de portal gebruikt, stelt u de afbeeldings-URI in op de locatie van uw Azure Container Registry. 

Gebruik de onderstaande stappen om de container te implementeren met behulp van de Azure CLI.

#### <a name="azure-vm-with-gpu"></a>[Azure VM met GPU](#tab/virtual-machine)

Een virtuele machine van Azure met een GPU kan ook worden gebruikt om ruimtelijke analyse uit te voeren. In het volgende voor beeld wordt een VM van de [NC-serie](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) gebruikt die één K80-GPU heeft.

#### <a name="create-the-vm"></a>De VM maken

Open de wizard [een virtuele machine maken](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) in de Azure Portal.

Geef uw virtuele machine een naam en selecteer de regio die moet worden (VS) West 2. Zorg ervoor dat u instelt `Availability Options` op geen infra structuur-redundantie vereist. Raadpleeg de onderstaande afbeelding voor de volledige configuratie en de volgende stap om de juiste VM-grootte te vinden. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.png" alt-text="Details van de virtuele-machine configuratie." lightbox="media/spatial-analysis/virtual-machine-instance-details.png":::

Als u de VM-grootte wilt vinden, selecteert u ' alle grootten weer geven ' en bekijkt u de lijst voor ' niet-Premium-opslag-VM-grootten ', zoals hieronder wordt weer gegeven.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Grootte van virtuele machines." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Selecteer vervolgens **nc6** of **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="promotie selectie" lightbox="media/spatial-analysis/promotional-selection.png":::

Maak vervolgens de virtuele machine. Nadat u hebt gemaakt, gaat u naar de VM-resource in de Azure Portal en selecteert u in `Extensions` het linkerdeel venster. Het venster extensies wordt weer gegeven met alle beschik bare uitbrei dingen. Selecteer `NVIDIA GPU Driver Extension` , klik op maken en voltooi de wizard.

Zodra de uitbrei ding met succes is toegepast, gaat u naar de hoofd pagina van de VM in de Azure Portal en klikt u op `Connect` . U kunt toegang krijgen tot de virtuele machine via SSH of RDP. RDP is handig als het weer geven van het venster voor het visualiseren van Vensters wordt ingeschakeld (later uitgelegd). Configureer de RDP-toegang door de volgende [stappen uit te voeren](../../virtual-machines/linux/use-remote-desktop.md) en een verbinding met een extern bureau blad te openen met de virtuele machine.

### <a name="verify-graphics-drivers-are-installed"></a>Controleren of grafische Stuur Programma's zijn geïnstalleerd

Voer de volgende opdracht uit om te controleren of de grafische Stuur Programma's zijn geïnstalleerd. 

```bash
nvidia-smi
```

De volgende uitvoer wordt weergegeven.

![Uitvoer van NVIDIA-stuur programma](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Docker CE en NVIDIA-docker2 installeren op de virtuele machine

Voer de volgende opdrachten een voor een uit om docker CE en NVIDIA-docker2 te installeren op de VM.

Installeer docker CE op de hostcomputer.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Installeer het *NVIDIA-docker-2-* software pakket.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Nu u de virtuele machine hebt ingesteld en geconfigureerd, volgt u de onderstaande stappen om Azure IoT Edge te configureren. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Azure IoT Edge op de virtuele machine configureren

Als u de container voor ruimtelijke analyse op de VM wilt implementeren, moet u een exemplaar van een [Azure IOT hub](../../iot-hub/iot-hub-create-through-portal.md) -service maken met behulp van de prijs categorie Standard (S1) of gratis (F0).

Gebruik de Azure CLI om een exemplaar van Azure IoT Hub te maken. Vervang de para meters, indien van toepassing. U kunt de Azure-IoT Hub ook maken op de [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Zie [regio ondersteuning](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) voor beschik bare regio's.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

U moet [Azure IOT Edge](../../iot-edge/how-to-install-iot-edge.md) versie 1.0.9 installeren. Volg deze stappen om de juiste versie te downloaden:

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Kopieer de gegenereerde lijst.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installeer de openbare Microsoft GPG-sleutel.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Werk de pakket lijsten op het apparaat bij.

```bash
sudo apt-get update
```

Installeer de 1.0.9-release:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Registreer vervolgens de virtuele machine als IoT Edge apparaat in uw IoT Hub-exemplaar met behulp van een [Connection String](../../iot-edge/how-to-register-device.md?view=iotedge-2018-06).

U moet het IoT Edge apparaat verbinden met uw Azure-IoT Hub. U moet de connection string kopiëren van het IoT Edge apparaat dat u eerder hebt gemaakt. U kunt ook de onderstaande opdracht uitvoeren in de Azure CLI.

```bash
sudo az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

Open voor bewerking op de VM  `/etc/iotedge/config.yaml` . Vervang door `ADD DEVICE CONNECTION STRING HERE` de Connection String. Sla het bestand op en sluit het. Voer deze opdracht uit om de IoT Edge-service op de VM opnieuw op te starten.

```bash
sudo systemctl restart iotedge
```

Implementeer de container voor ruimtelijke analyse als een IoT-module op de VM, hetzij vanuit de [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) of [Azure cli](../cognitive-services-apis-create-account-cli.md?tabs=windows). Als u de portal gebruikt, stelt u de afbeeldings-URI in op de locatie van uw Azure Container Registry. 

Gebruik de onderstaande stappen om de container te implementeren met behulp van de Azure CLI.

---

### <a name="iot-deployment-manifest"></a>IoT-implementatie manifest

Als u de implementatie van containers op meerdere hostcomputers wilt stroom lijnen, kunt u een manifest bestand voor implementatie maken om de opties voor het maken van de container en omgevings variabelen op te geven. U vindt een voor beeld van een implementatie manifest [voor Azure stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [andere desktop computers](https://go.microsoft.com/fwlink/?linkid=2152270)en [Azure VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189) op github.

In de volgende tabel ziet u de verschillende omgevings variabelen die worden gebruikt door de module IoT Edge. U kunt deze ook instellen in het eerder gekoppelde implementatie manifest, met behulp van het- `env` kenmerk in `spatialanalysis` :

| Instellingsnaam | Waarde | Beschrijving|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Valuta Uitgebreide | Logboek registratie niveau, selecteer een van de twee waarden|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Niet wijzigen|
| ARCHON_PERF_MARKER| onjuist| Stel deze waarde in op True voor prestatie logboek registratie, anders moet dit onwaar zijn| 
| ARCHON_NODES_LOG_LEVEL | Valuta Uitgebreide | Logboek registratie niveau, selecteer een van de twee waarden|
| OMP_WAIT_POLICY | Pass | Niet wijzigen|
| QT_X11_NO_MITSHM | 1 | Niet wijzigen|
| APIKEY | uw API-sleutel| Verzamel deze waarde van Azure Portal van uw Computer Vision-resource. U kunt deze vinden in de sectie **Key en endpoint** van uw resource. |
| FACTURERING | de URI van uw eind punt| Verzamel deze waarde van Azure Portal van uw Computer Vision-resource. U kunt deze vinden in de sectie **Key en endpoint** van uw resource.|
| HOUDT | zodat | Deze waarde moet worden ingesteld om te *accepteren* dat de container moet worden uitgevoerd |
| DISPLAY | : 1 | Deze waarde moet gelijk zijn aan de uitvoer van `echo $DISPLAY` op de hostcomputer. Azure Stack edge-apparaten hebben geen weer gave. Deze instelling is niet van toepassing|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Deze omgevings variabele toevoegen als uw GPU **niet** T4 of NVIDIA 2080 Ti is|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Deze omgevings variabele toevoegen als uw GPU **niet** T4 of NVIDIA 2080 Ti is|
| KEY_ENV | ASE-versleutelings sleutel | Deze omgevings variabele toevoegen als Video_URL een verborgen teken reeks is |
| IV_ENV | Initialisatie vector | Deze omgevings variabele toevoegen als Video_URL een verborgen teken reeks is|

> [!IMPORTANT]
> De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](#billing)voor meer informatie.

Zodra u het implementatie manifest voor [Azure stack edge-apparaten](https://go.microsoft.com/fwlink/?linkid=2142179), [een desktop machine](https://go.microsoft.com/fwlink/?linkid=2152270) of [Azure VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189) met uw eigen instellingen en een selectie van bewerkingen hebt bijgewerkt, kunt u de onderstaande [Azure cli](../cognitive-services-apis-create-account-cli.md?tabs=windows) -opdracht gebruiken om de container op de hostcomputer te implementeren, als een IOT Edge-module.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Parameter  |Beschrijving  |
|---------|---------|
| `--hub-name` | De naam van uw Azure-IoT Hub. |
| `--content` | De naam van het implementatie bestand. |
| `--target-condition` | De naam van uw IoT Edge apparaat voor de hostcomputer. |
| `-–subscription` | Abonnements-ID of-naam. |

Met deze opdracht wordt de implementatie gestart. Ga naar de pagina van uw Azure IoT Hub-exemplaar in het Azure Portal om de implementatie status te bekijken. De status kan worden weer gegeven als *417: de implementatie configuratie van het apparaat is niet ingesteld* totdat het apparaat klaar is met het downloaden van de container installatie kopieën en wordt gestart.

## <a name="validate-that-the-deployment-is-successful"></a>Controleren of de implementatie is voltooid

Er zijn verschillende manieren om te controleren of de container wordt uitgevoerd. Zoek de *runtime status* in de **instellingen** van de IOT Edge-module voor de module ruimtelijke analyse in uw Azure IOT hub-exemplaar op de Azure Portal. Controleer of de **gewenste waarde** en de **gemelde waarde** voor de *runtime status* worden *uitgevoerd*.

![Voor beeld van implementatie verificatie](./media/spatial-analysis/deployment-verification.png)

Zodra de implementatie is voltooid en de container actief is, begint de **hostcomputer** met het verzenden van gebeurtenissen naar de Azure-IOT hub. Als u de `.debug` versie van de bewerkingen hebt gebruikt, ziet u een visualer-venster voor elke camera die u in het implementatie manifest hebt geconfigureerd. U kunt nu de regels en zones definiëren die u wilt bewaken in het implementatie manifest en de instructies volgen om opnieuw te implementeren. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>De bewerkingen configureren die worden uitgevoerd door ruimtelijke analyse

U moet [ruimtelijke analyse bewerkingen](spatial-analysis-operations.md) gebruiken om de container te configureren voor het gebruik van verbonden camera's, het configureren van de bewerkingen en nog veel meer. Voor elk camera apparaat dat u configureert, wordt met de bewerkingen voor ruimtelijke analyse een uitvoer stroom van JSON-berichten gegenereerd, verzonden naar uw exemplaar van Azure IoT Hub.

## <a name="redeploy-or-delete-the-deployment"></a>Implementatie opnieuw implementeren of verwijderen

Als u de implementatie wilt bijwerken, moet u ervoor zorgen dat uw vorige implementaties zijn geïmplementeerd, of moet u IoT Edge-apparaten verwijderen die niet zijn voltooid. Anders wordt deze implementaties voortgezet, waardoor het systeem een slechte status heeft. U kunt de Azure Portal of de [Azure cli](../cognitive-services-apis-create-account-cli.md?tabs=windows)gebruiken.

## <a name="use-the-output-generated-by-the-container"></a>De uitvoer gebruiken die door de container is gegenereerd

Als u de uitvoer wilt gaan gebruiken die door de container is gegenereerd, raadpleegt u de volgende artikelen:

*   Gebruik de Azure Event hub SDK voor de gekozen programmeer taal om verbinding te maken met het Azure IoT Hub-eind punt en de gebeurtenissen te ontvangen. Zie [apparaat-naar-Cloud-berichten lezen van het ingebouwde eind punt](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) voor meer informatie. 
*   Stel bericht routering in op uw Azure-IoT Hub om de gebeurtenissen naar andere eind punten te verzenden of de gebeurtenissen op te slaan in Azure Blob Storage, enzovoort. Zie [IOT hub Message Routing](../../iot-hub/iot-hub-devguide-messages-d2c.md) voor meer informatie. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Ruimtelijke analyse uitvoeren met een opgenomen video bestand

U kunt ruimtelijke analyse gebruiken met zowel opgenomen als live video. Als u ruimtelijke analyse voor opgenomen video wilt gebruiken, probeert u een video bestand op te nemen en op te slaan als een MP4-bestand. Maak een Blob Storage-account in azure of gebruik een bestaande. Werk vervolgens de volgende Blob Storage-instellingen bij in de Azure Portal:
    1. **Beveiligde overdracht moet** worden gewijzigd in **uitgeschakeld**
    2. **Open bare BLOB-toegang** tot **ingeschakeld** wijzigen toestaan

Navigeer naar het gedeelte **container** en maak een nieuwe container of gebruik een bestaande. Upload het video bestand vervolgens naar de container. Vouw de bestands instellingen voor het geüploade bestand uit en selecteer **Sa's genereren**. Zorg ervoor dat u de **verval datum** lang genoeg instelt om de test periode te bedekken. **Toegestane protocollen** instellen op *http* (*https* wordt niet ondersteund).

Klik op **SAS-token en URL genereren** en kopieer de URL van de BLOB-SAS. Vervang het begin `https` door `http` en test de URL in een browser die het afspelen van video ondersteunt.

Vervang `VIDEO_URL` in het implementatie manifest voor uw [Azure stack edge-apparaat](https://go.microsoft.com/fwlink/?linkid=2142179), [desktop machine](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure-VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189) met de URL die u hebt gemaakt, voor alle grafieken. Stel `VIDEO_IS_LIVE` in op `false` en implementeer de container voor ruimtelijke analyse opnieuw met het bijgewerkte manifest. Zie het voorbeeld hieronder.

De module ruimtelijke analyse begint met het gebruiken van het video bestand en wordt ook automatisch opnieuw afgespeeld.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Problemen oplossen

Zie [telemetrie en troubleshooting](spatial-analysis-logging.md) voor stappen voor veelvoorkomende problemen als u problemen ondervindt bij het starten of uitvoeren van de container. Dit artikel bevat ook informatie over het genereren en verzamelen van Logboeken en het verzamelen van de systeem status.

## <a name="billing"></a>Billing

De container voor ruimtelijke analyse verzendt factuur gegevens naar Azure met behulp van een Computer Vision resource in uw Azure-account. Het gebruik van ruimtelijke analyse in open bare preview is momenteel gratis. 

Azure Cognitive Services-containers mogen niet worden uitgevoerd zonder dat ze zijn verbonden met het eind punt voor meting/facturering. U moet de containers in staat stellen om de facturerings gegevens te allen tijde te communiceren met het eind punt. Cognitive Services containers verzenden geen klant gegevens, zoals de video of afbeelding die wordt geanalyseerd, naar micro soft.


## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het downloaden, installeren en uitvoeren van de container voor ruimtelijke analyse. Samenvatting:

* Ruimtelijke analyse is een Linux-container voor docker.
* Container installatie kopieën worden gedownload uit de micro soft-Container Registry.
* Container installatie kopieën worden als IoT-modules in Azure IoT Edge uitgevoerd.
* De container configureren en implementeren op een host-computer.

## <a name="next-steps"></a>Volgende stappen

* [Een web-app voor het tellen van personen implementeren](spatial-analysis-web-app.md)
* [Ruimtelijke-analyse bewerkingen configureren](spatial-analysis-operations.md)
* [Logboekregistratie en problemen oplossen](spatial-analysis-logging.md)
* [Gids voor camera plaatsing](spatial-analysis-camera-placement.md)
* [Hand leiding voor zone-en lijn plaatsing](spatial-analysis-zone-line-placement.md)