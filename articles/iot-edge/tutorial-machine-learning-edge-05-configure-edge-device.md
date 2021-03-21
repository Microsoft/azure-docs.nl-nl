---
title: 'Zelf studie: een Azure IoT Edge apparaat configureren-machine learning op IoT Edge'
description: In deze zelf studie configureert u een virtuele Azure-machine waarop Linux wordt uitgevoerd als een Azure IoT Edge apparaat dat als transparante gateway fungeert.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463082"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Zelf studie: een Azure IoT Edge apparaat configureren

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In dit artikel wordt een virtuele Azure-machine met Linux zodanig geconfigureerd dat deze een Azure IoT Edge apparaat is dat als transparante gateway fungeert. Met een dergelijke configuratie kunnen apparaten via de gateway verbinding maken met Azure IoT Hub zonder dat ze van het bestaan van de gateway af weten. Op hetzelfde moment is een gebruiker met de apparaten in IoT Hub niet op de hoogte van het tussenliggende gateway apparaat. Uiteindelijk voegen we Edge Analytics toe aan ons systeem door IoT Edge modules toe te voegen aan de transparante gateway.

>[!NOTE]
>De concepten in deze zelf studie zijn van toepassing op alle versies van IoT Edge, maar het voorbeeld apparaat dat u maakt om het scenario uit te proberen, wordt uitgevoerd IoT Edge versie 1,1.

De stappen in dit artikel worden doorgaans uitgevoerd door een cloud-ontwikkelaar.

In dit deel van de zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Certificaten maken om uw gateway-apparaat veilig verbinding te laten maken met uw downstream-apparaten.
> * Een IoT Edge-apparaat maken.
> * Een virtuele Azure-machine maken om uw IoT Edge-apparaat te simuleren.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u in dit artikel rechtstreeks hebt gearriveerd, raadpleegt u het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="create-certificates"></a>Certificaten maken

Een apparaat kan alleen als gateway worden gebruikt als het een veilige verbinding met downstream-apparaten heeft. Met IoT Edge kunt u een open bare-sleutel infrastructuur (PKI) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval geven we een downstream IoT-apparaat toestemming om verbinding te maken met een IoT Edge-apparaat dat als transparante gateway fungeert. Om een redelijke beveiliging te garanderen, moet het downstream apparaat de identiteit van het IoT Edge apparaat bevestigen. Zie [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md) (Begrijpen hoe Azure IoT Edge certificaten gebruikt) voor meer informatie over hoe IoT Edge-apparaten certificaten gebruiken.

In deze sectie maken we de zelfondertekende certificaten met behulp van een docker-installatie kopie die we vervolgens bouwen en uitvoeren. U hebt ervoor gekozen om een docker-installatie kopie te gebruiken om deze stap te volt ooien, omdat hiermee het aantal benodigde stappen voor het maken van de certificaten op de Windows-ontwikkel computer wordt verminderd. Zie [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md)om te begrijpen wat er met de docker-installatie kopie wordt geautomatiseerd.

1. Meld u aan bij uw ontwikkel-VM.
1. Maak een nieuwe map met het pad en de naam **c:\edgeCertificates**.

1. Als deze nog niet wordt uitgevoerd, start u **docker voor Windows** vanuit het menu Start van Windows.

1. Open Visual Studio Code.

1. Selecteer **bestand**  >  **openen map** en selecteer vervolgens **C: \\ Source \\ IoTEdgeAndMlSample \\ CreateCertificates**.

1. Klik in het deel venster **Verkenner** met de rechter muisknop op **Dockerfile** en selecteer **installatie kopie maken**.

1. Accepteer in het dialoog venster de standaard waarde voor de naam van de installatie kopie en de tag: **createcertificates: meest recent**.

    ![Scherm opname van het maken van certificaten in Visual Studio code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Wacht tot de compilatie is voltooid.

    > [!NOTE]
    > Er wordt mogelijk een waarschuwing weer gegeven over een ontbrekende open bare sleutel. Het is veilig om deze waarschuwing te negeren. Op dezelfde manier wordt een beveiligings waarschuwing weer gegeven waarin u wordt geadviseerd om machtigingen voor uw installatie kopie te controleren of opnieuw in te stellen. deze installatie kopie kan veilig worden genegeerd.

1. Voer in het terminalvenster van Visual Studio Code de container createcertificates uit.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker vraagt om toegang tot het **c:\\** -station. Selecteer **Share it**.

1. Voer uw referenties in wanneer hierom wordt gevraagd.

1. Nadat de container is uitgevoerd, controleert u de volgende bestanden in **c: \\ edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Certificaten uploaden naar Azure Key Vault

Om onze certificaten veilig op te slaan en ze toegankelijk te maken vanaf meerdere apparaten, uploaden we de certificaten in Azure Key Vault. Zoals u kunt zien in de voor gaande lijst, hebben we twee typen certificaat bestanden: PFX en PEM. We behandelen het PFX-bestand als Key Vault certificaten die moeten worden geüpload naar Key Vault. De PEM-bestanden zijn tekst zonder opmaak en we behandelen ze als Key Vault geheimen. We gebruiken het Key Vault exemplaar dat is gekoppeld aan de Azure Machine Learning werk ruimte die we hebben gemaakt door de [Jupyter-notebooks](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks)uit te voeren.

1. Ga vanuit het [Azure Portal](https://portal.azure.com)naar uw Azure machine learning-werk ruimte.

1. Zoek op de pagina overzicht van de werk ruimte Machine Learning de naam voor **Key Vault**.

    ![Scherm afbeelding waarin de naam van de sleutel kluis wordt gekopieerd.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Upload de certificaten op de ontwikkelcomputer naar Key Vault. Vervang **\<subscriptionId\>** en **\<keyvaultname\>** door uw resourcegegevens.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Als u hierom wordt gevraagd, meldt u zich aan bij Azure.

1. Het script wordt een paar minuten uitgevoerd met uitvoer met een lijst met de nieuwe Key Vault vermeldingen.

    ![Scherm opname van Key Vault script uitvoer.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Een IoT Edge-apparaat maken

Om een Azure IoT Edge-apparaat te verbinden met een IoT-hub, maken we eerst een identiteit voor het apparaat in de hub. We nemen de verbindingsreeks van de apparaat-id in de cloud en gebruiken die om de runtime op ons IoT Edge-apparaat te configureren. Nadat een geconfigureerd apparaat verbinding maakt met de hub, kunnen we modules implementeren en berichten verzenden. We kunnen ook de configuratie van het fysieke IoT Edge apparaat wijzigen door de bijbehorende apparaat-id in IoT Hub te wijzigen.

Voor deze zelf studie maken we de nieuwe apparaat-id met behulp van Visual Studio code. U kunt deze stappen ook uitvoeren met behulp van de Azure Portal of de Azure CLI.

1. Open Visual Studio Code op uw ontwikkelcomputer.

1. Vouw het **Azure-IOT hub** frame uit vanuit de weer gave Visual Studio code **Explorer** .

1. Selecteer het beletsel teken en selecteer **IOT edge apparaat maken**.

1. Geef het apparaat een naam. Voor het gemak gebruiken we de naam **aaTurbofanEdgeDevice** zodat deze naar de bovenkant van de vermelde apparaten wordt gesorteerd.

1. Het nieuwe apparaat wordt weer gegeven in de lijst met apparaten.

    ![Scherm afbeelding met een weer gave van het apparaat in Visual Studio code Explorer.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Een virtuele machine van Azure implementeren

We gebruiken de [Azure IOT Edge op Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) -installatie kopie van Azure Marketplace om ons IOT edge-apparaat voor deze zelf studie te maken. Met de Azure IoT Edge op de Ubuntu-installatie kopie worden de meest recente IoT Edge runtime en de bijbehorende afhankelijkheden van het opstarten geïnstalleerd. De virtuele machine wordt geïmplementeerd met:

- Een Power shell-script, `Create-EdgeVM.ps1` .
- Een Azure Resource Manager sjabloon, `IoTEdgeVMTemplate.json` .
- Een shell script, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Programmatische implementatie inschakelen

Als u de installatie kopie van Azure Marketplace wilt gebruiken in een implementatie met een script, moeten we de installatie kopie op een programmatische manier implementeren.

1. Meld u aan bij de Azure-portal.

1. Selecteer **Alle services**.

1. Typ **Marketplace** in het zoekvak en druk op Enter.

1. Typ **Azure IoT Edge on Ubuntu** in het zoekvak van Marketplace en druk op Enter.

1. Selecteer de hyperlink **Aan de slag** om programmatisch te implementeren.

1. Selecteer de knop **inschakelen** en selecteer vervolgens **Opslaan**.

    ![Scherm opname die laat zien hoe u een programmatische implementatie voor een virtuele machine inschakelt.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. U ziet een geslaagde melding.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Voer vervolgens het script uit om de virtuele machine voor uw IoT Edge-apparaat te maken.

1. Open een Power shell-venster en ga naar de **EdgeVM** -map.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Voer het script uit om de virtuele machine te maken.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Wanneer u hierom wordt gevraagd, geeft u waarden op voor elke para meter. Voor het abonnement, de resource groep en de locatie, raden we u aan dezelfde waarden te gebruiken als voor alle resources in deze zelf studie.

    * **Azure-abonnements-id**: gevonden in de Azure Portal.
    * **Naam van resource groep**: naam voor het onthouden van het groeperen van de resources voor deze zelf studie.
    * **Locatie**: kies een Azure-locatie waarin de virtuele machine wordt gemaakt, bijvoorbeeld west 2. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/) voor meer informatie.
    * **AdminUsername**: de naam voor het beheerders account dat u gebruikt om u aan te melden bij de virtuele machine.
    * **AdminPassword**: het wacht woord dat moet worden ingesteld voor de gebruikers naam van de beheerder op de virtuele machine.

1. Meld u aan bij Azure met de referenties die zijn gekoppeld aan het Azure-abonnement dat u gebruikt voor het script om de virtuele machine in te stellen.

1. U ziet een bericht dat met het script een VM wordt gemaakt. Druk op **y** of **Enter** om door te gaan.

1. Het script wordt enkele minuten uitgevoerd terwijl de volgende stappen worden uitgevoerd:

    * Hiermee wordt de resource groep gemaakt als deze nog niet bestaat
    * Hiermee wordt de virtuele machine gemaakt
    * Voegt NSG-uitzonde ringen voor de VM toe voor poort 22 (SSH), 5671 (AMQP), 5672 (AMPQ) en 443 (TLS)
    * Hiermee wordt de [Azure cli](/cli/azure/install-azure-cli-apt) geïnstalleerd

1. Het script voert de SSH-verbindingsreeks uit die nodig is om verbinding te maken met de VM. Kopieer de verbindingsreeks voor de volgende stap.

    ![Scherm opname van het kopiëren van de SSH-connection string voor een virtuele machine.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Verbinding maken met uw IoT Edge-apparaat

In de volgende gedeelten gaan we de virtuele Azure-machine configureren die we hebben gemaakt. De eerste stap is met maken van verbinding met de virtuele machine.

1. Open een opdracht prompt en plak de SSH-connection string die u hebt gekopieerd uit de script uitvoer. Voer uw eigen gegevens in voor de gebruikersnaam, het achtervoegsel en de regio, overeenkomstig de waarden die u hebt opgegeven voor het PowerShell-script in het vorige gedeelte.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Wanneer u wordt gevraagd om de authenticiteit van de host te valideren, voert u **Ja** in en selecteert u **Enter**.

1. Geef uw wacht woord op wanneer u hierom wordt gevraagd.

1. In Ubuntu wordt een welkomst bericht weer gegeven. vervolgens ziet u een prompt `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Key Vault-certificaten downloaden

Eerder in dit artikel hebben we certificaten geüpload naar Key Vault om ze beschikbaar te maken voor het IoT Edge apparaat en het leaf-apparaat. Het leaf-apparaat is een downstream apparaat dat het IoT Edge-apparaat als een gateway gebruikt om te communiceren met IoT Hub.

Verderop in de zelf studie wordt het blad apparaat behandeld. In dit gedeelte downloadt u de certificaten naar het IoT Edge-apparaat.

1. Meld u vanuit de SSH-sessie op de virtuele Linux-machine aan bij Azure met de Azure CLI.

    ```azurecli
    az login
    ```

1. U wordt gevraagd een browser te openen op een aanmeldings pagina voor [micro soft-apparaten](https://microsoft.com/devicelogin) en een unieke code op te geven. U kunt deze stappen uitvoeren op uw lokale computer. Sluit het browservenster wanneer u klaar bent met verifiëren.

1. Wanneer u bent geverifieerd, wordt de Linux-VM aangemeld en worden uw Azure-abonnementen weergegeven.

1. Stel het Azure-abonnement in dat u wilt gebruiken voor Azure CLI-opdrachten.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Maak een map op de VM voor de certificaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Down load de certificaten die u hebt opgeslagen in de sleutel kluis: New-Edge-Device-Full-chain. cert. pem, New-Edge-device. key. pem en Azure-IOT-test-only. root. ca. cert. pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Configuratie van IoT Edge-apparaat bijwerken

De IoT Edge runtime gebruikt het bestand/etc/iotedge/config.yaml om de configuratie te behouden. Er moeten drie gegevens worden bijgewerkt in dit bestand:

* **Apparaat Connection String**: de Connection String van de identiteit van dit apparaat in IOT hub
* **Certificaten**: de certificaten die moeten worden gebruikt voor verbindingen die zijn gemaakt met downstream-apparaten
* **Hostnaam**: de Fully QUALIFIED domain name (FQDN) van de virtuele machine IOT edge apparaat

De Azure IoT Edge op de Ubuntu-installatie kopie die is gebruikt om de IoT Edge VM te maken, wordt geleverd met een shell script dat het bestand config. yaml bijwerkt met de connection string.

1. Klik in Visual Studio code met de rechter muisknop op het apparaat IoT Edge en selecteer **apparaat verbindings reeks kopiëren**.

    ![Scherm opname van het kopiëren van de connection string van Visual Studio code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Voer in de SSH-sessie de volgende opdracht uit om het bestand config.yaml bij te werken met de verbindingsreeks van uw apparaat.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Vervolgens worden de certificaten en de hostnaam bijgewerkt door het bestand config. yaml rechtstreeks te bewerken.

1. Open het bestand config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Werk de sectie certificaten van het bestand config. yaml bij door de eerste regel **#** te verwijderen en het pad zodanig in te stellen dat het bestand eruitziet als in het volgende voor beeld:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Zorg ervoor dat de regel **certificaten:** geen witruimte heeft en dat elk van de geneste certificaten met twee spaties wordt Inge sprongen.

    Als u met de rechtermuisknop klikt in nano, wordt de inhoud van het klembord op de huidige cursorpositie geplakt. Als u de teken reeks wilt vervangen, gebruikt u de pijlen op het toetsen bord om naar de teken reeks te gaan die u wilt vervangen, verwijdert u de teken reeks en klikt u met de rechter muisknop om uit de buffer te plakken.

1. Ga in het Azure Portal naar de virtuele machine. Kopieer de DNS-naam (FQDN van de computer) uit het gedeelte **Overzicht**.

1. Plak de FQDN in de sectie hostname van het bestand config. yml. Zorg ervoor dat de naam alleen uit kleine letters bestaat.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Sla het bestand op en sluit het door **CTRL + X**, **Y** en **Enter** te selecteren.

1. Start de IoT Edge-daemon opnieuw op.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Controleer de status van de IoT Edge-daemon. Typ na de opdracht **: q** om af te sluiten.

    ```bash
    systemctl status iotedge
    ```

1. Raadpleeg \[ \] daemon-logboeken voor gedetailleerde informatie over de fout als u in de status fouten (gekleurde tekst met voor voegsel ' fout ') ziet.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht tot de resources zijn opgeruimd totdat u de laatste zelf studie hebt voltooid.

## <a name="next-steps"></a>Volgende stappen

We hebben zojuist een Azure-VM geconfigureerd als IoT Edge transparante gateway. We zijn begonnen met het genereren van test certificaten die zijn geüpload naar Key Vault. We hebben vervolgens een script en Resource Manager-sjabloon gebruikt om de virtuele machine te implementeren met de Ubuntu Server 16,04 LTS + Azure IoT Edge runtime-installatie kopie van Azure Marketplace. Als de virtuele machine actief is, is er verbinding gemaakt via SSH. Vervolgens hebben we aangemeld bij Azure en gedownloade certificaten van Key Vault. Er zijn verschillende updates voor de configuratie van de IoT Edge runtime gemaakt door het bestand config. yaml bij te werken.

Ga verder naar het volgende artikel om IoT Edge-modules te maken.

> [!div class="nextstepaction"]
> [Aangepaste IoT Edge-modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md)
