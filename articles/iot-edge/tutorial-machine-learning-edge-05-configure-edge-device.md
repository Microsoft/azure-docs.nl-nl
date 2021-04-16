---
title: 'Zelfstudie: Een Azure IoT Edge configureren - Machine learning op IoT Edge'
description: In deze zelfstudie configureert u een virtuele Azure-machine met Linux als een Azure IoT Edge apparaat dat fungeert als een transparante gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 65fd6e5b4d494f8e8486d72079b9fa97a175894b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376882"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Zelfstudie: Een Azure IoT Edge configureren

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In dit artikel configureren we een virtuele Azure-machine met Linux als een Azure IoT Edge apparaat dat fungeert als een transparante gateway. Met een dergelijke configuratie kunnen apparaten via de gateway verbinding maken met Azure IoT Hub zonder dat ze van het bestaan van de gateway af weten. Tegelijkertijd is een gebruiker die interactie heeft met de apparaten in IoT Hub niet op de hoogte van het tussenliggende gatewayapparaat. Uiteindelijk voegen we edge-analyses toe aan ons systeem door IoT Edge modules toe te voegen aan de transparante gateway.

>[!NOTE]
>De concepten in deze zelfstudie zijn van toepassing op alle versies van IoT Edge, maar het voorbeeldapparaat dat u maakt om het scenario uit te proberen, wordt uitgevoerd IoT Edge versie 1.1.

De stappen in dit artikel worden doorgaans uitgevoerd door een cloud-ontwikkelaar.

In dit deel van de zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Certificaten maken om uw gateway-apparaat veilig verbinding te laten maken met uw downstream-apparaten.
> * Een IoT Edge-apparaat maken.
> * Een virtuele Azure-machine maken om uw IoT Edge-apparaat te simuleren.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel bent aangekomen, bekijkt u het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="create-certificates"></a>Certificaten maken

Een apparaat kan alleen als gateway functioneren als het veilig verbinding moet maken met downstreamapparaten. Met IoT Edge kunt u een openbare-sleutelinfrastructuur (PKI) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval geven we een downstream IoT-apparaat toestemming om verbinding te maken met een IoT Edge-apparaat dat als transparante gateway fungeert. Om een redelijke beveiliging te garanderen, moet het downstream apparaat de identiteit van het IoT Edge apparaat bevestigen. Zie [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md) (Begrijpen hoe Azure IoT Edge certificaten gebruikt) voor meer informatie over hoe IoT Edge-apparaten certificaten gebruiken.

In deze sectie maken we de zelf-ondertekende certificaten met behulp van een Docker-afbeelding die we vervolgens bouwen en uitvoeren. We hebben ervoor gekozen om een Docker-afbeelding te gebruiken om deze stap uit te voeren, omdat hiermee het aantal stappen wordt beperkt dat nodig is om de certificaten op de Windows-ontwikkelmachine te maken. Zie Democertificaten maken om de functies van IoT Edge te testen voor meer inzicht in wat we [met de Docker-IoT Edge geautomatiseerd.](how-to-create-test-certificates.md)

1. Meld u aan bij uw ontwikkel-VM.
1. Maak een nieuwe map met het pad en de naam **c:\edgeCertificates.**

1. Als docker voor Windows nog niet wordt uitgevoerd, start u **docker** vanuit het Windows-Startmenu.

1. Open Visual Studio Code.

1. Selecteer   >  **Bestand Map openen** en selecteer vervolgens **C: source \\ \\ IoTEdgeAndMlSample \\ CreateCertificates.**

1. Klik in **het deelvenster** Explorer met de rechtermuisknop **op dockerfile** en selecteer **Build Image.**

1. Accepteer in het dialoogvenster de standaardwaarde voor de naam en tag van de afbeelding: **createcertificates: latest.**

    ![Schermopname van het maken van certificaten in Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Wacht tot de compilatie is voltooid.

    > [!NOTE]
    > Mogelijk ziet u een waarschuwing over een ontbrekende openbare sleutel. Het is veilig om deze waarschuwing te negeren. Op dezelfde manier ziet u een beveiligingswaarschuwing waarin u wordt aangeraden de machtigingen voor uw afbeelding te controleren of opnieuw in te stellen, wat veilig is om te negeren voor deze afbeelding.

1. Voer in het terminalvenster van Visual Studio Code de container createcertificates uit.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker vraagt om toegang tot het **c:\\** -station. Selecteer **Share it**.

1. Voer uw referenties in wanneer hierom wordt gevraagd.

1. Nadat de container is uitgevoerd, controleert u op de volgende bestanden in **c: \\ edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Certificaten uploaden naar Azure Key Vault

Om onze certificaten veilig op te slaan en ze toegankelijk te maken vanaf meerdere apparaten, uploaden we de certificaten naar Azure Key Vault. Zoals u in de vorige lijst kunt zien, hebben we twee typen certificaatbestanden: PFX en PEM. We behandelen het PFX-bestand als Key Vault certificaten die moeten worden geüpload naar Key Vault. De PEM-bestanden zijn tekst zonder tekst en we behandelen ze als Key Vault geheimen. We gebruiken het Key Vault dat is gekoppeld aan de Azure Machine Learning werkruimte die we hebben gemaakt door de [Jupyter-notebooks uit te uitvoeren.](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks)

1. Ga vanuit [Azure Portal](https://portal.azure.com)naar uw Azure Machine Learning werkruimte.

1. Zoek op de overzichtspagina van Machine Learning werkruimte de naam van **Key Vault**.

    ![Schermopname van het kopiëren van de naam van de sleutelkluis.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Upload de certificaten op de ontwikkelcomputer naar Key Vault. Vervang **\<subscriptionId\>** en **\<keyvaultname\>** door uw resourcegegevens.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Meld u aan bij Azure als u hier om wordt gevraagd.

1. Het script wordt een paar minuten uitgevoerd met uitvoer die de nieuwe Key Vault vermeldt.

    ![Schermopname met Key Vault scriptuitvoer.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Een IoT Edge-apparaat maken

Om een Azure IoT Edge-apparaat te verbinden met een IoT-hub, maken we eerst een identiteit voor het apparaat in de hub. We nemen de verbindingsreeks van de apparaat-id in de cloud en gebruiken die om de runtime op ons IoT Edge-apparaat te configureren. Nadat een geconfigureerd apparaat verbinding heeft met de hub, kunnen we modules implementeren en berichten verzenden. We kunnen ook de configuratie van het fysieke apparaat IoT Edge door de bijbehorende apparaat-id te wijzigen in IoT Hub.

Voor deze zelfstudie maken we de nieuwe apparaat-id met behulp van Visual Studio Code. U kunt deze stappen ook uitvoeren met behulp van de Azure Portal of de Azure CLI.

1. Open Visual Studio Code op uw ontwikkelcomputer.

1. Vouw het **Azure IoT Hub** uit vanuit de Visual Studio **codeverkennerweergave.**

1. Selecteer het beletselteken en selecteer **IoT Edge apparaat.**

1. Geef het apparaat een naam. Voor het gemak gebruiken we de naam **aaTurbofanEdgeDevice,** zodat deze bovenaan de vermelde apparaten wordt gesorteerd.

1. Het nieuwe apparaat wordt weergegeven in de lijst met apparaten.

    ![Schermopname van een weergave van het apparaat in Visual Studio Code Explorer.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Een virtuele Azure-machine implementeren

We gebruiken de [Azure IoT Edge op Ubuntu-installatie](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) Azure Marketplace om ons IoT Edge te maken voor deze zelfstudie. De Azure IoT Edge op Ubuntu installeert de meest recente IoT Edge runtime en de afhankelijkheden ervan bij het opstarten. We implementeren de VM met behulp van:

- Een PowerShell-script, `Create-EdgeVM.ps1` .
- Een Azure Resource Manager sjabloon, `IoTEdgeVMTemplate.json` .
- Een shellscript, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Programmatische implementatie inschakelen

Als u de installatie van de Azure Marketplace in een scriptimplementatie wilt gebruiken, moet u programmatische implementatie voor de installatieprogramma's inschakelen.

1. Meld u aan bij de Azure-portal.

1. Selecteer **Alle services**.

1. Typ **Marketplace** in het zoekvak en druk op Enter.

1. Typ **Azure IoT Edge on Ubuntu** in het zoekvak van Marketplace en druk op Enter.

1. Selecteer de hyperlink **Aan de slag** om programmatisch te implementeren.

1. Selecteer de **knop** Inschakelen en selecteer vervolgens **Opslaan.**

    ![Schermopname van het inschakelen van programmatische implementatie voor een virtuele machine.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. U ziet een melding dat het is gelukt.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Voer vervolgens het script uit om de virtuele machine voor uw IoT Edge-apparaat te maken.

1. Open een PowerShell-venster en ga naar de **map EdgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Voer het script uit om de virtuele machine te maken.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Wanneer u hier om wordt gevraagd, geeft u waarden op voor elke parameter. Voor abonnement, resourcegroep en locatie raden we u aan dezelfde waarden te gebruiken als voor alle resources in deze zelfstudie.

    * **Azure-abonnements-id:** deze vindt u in Azure Portal.
    * **Naam van resourcegroep:** een onthoudende naam voor het groeperen van de resources voor deze zelfstudie.
    * **Locatie**: kies een Azure-locatie waarin de virtuele machine wordt gemaakt, bijvoorbeeld west 2. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/) voor meer informatie.
    * **AdminUsername:** de naam voor het beheerdersaccount dat u gebruikt om u aan te melden bij de virtuele machine.
    * **AdminPassword:** het wachtwoord dat moet worden ingesteld voor de gebruikersnaam van de beheerder op de virtuele machine.

1. Voor het instellen van de VM met het script moet u zich aanmelden bij Azure met de referenties die zijn gekoppeld aan het Azure-abonnement dat u gebruikt.

1. U ziet een bericht dat met het script een VM wordt gemaakt. Druk op **y** of **Enter** om door te gaan.

1. Het script wordt enkele minuten uitgevoerd terwijl de volgende stappen worden uitgevoerd:

    * Hiermee maakt u de resourcegroep als deze nog niet bestaat
    * Hiermee maakt u de virtuele machine
    * Voegt NSG-uitzonderingen toe voor de VM voor poorten 22 (SSH), 5671 (AMQP), 5672 (AMPQ) en 443 (TLS)
    * De [Azure CLI installeren](/cli/azure/install-azure-cli-apt)

1. Het script voert de SSH-verbindingsreeks uit die nodig is om verbinding te maken met de VM. Kopieer de verbindingsreeks voor de volgende stap.

    ![Schermopname van het kopiëren van de SSH-connection string voor een virtuele machine.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Verbinding maken met uw IoT Edge-apparaat

In de volgende gedeelten gaan we de virtuele Azure-machine configureren die we hebben gemaakt. De eerste stap is met maken van verbinding met de virtuele machine.

1. Open een opdrachtprompt en plak de SSH-connection string u hebt gekopieerd uit de scriptuitvoer. Voer uw eigen gegevens in voor de gebruikersnaam, het achtervoegsel en de regio, overeenkomstig de waarden die u hebt opgegeven voor het PowerShell-script in het vorige gedeelte.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Wanneer u wordt gevraagd om de echtheid van de host te valideren, voert u **ja** in en selecteert u **Enter.**

1. Geef uw wachtwoord op wanneer u hier om wordt gevraagd.

1. Ubuntu geeft een welkomstbericht weer en vervolgens ziet u een prompt zoals `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Key Vault-certificaten downloaden

Eerder in dit artikel hebben we certificaten geüpload naar Key Vault om ze beschikbaar te maken voor het IoT Edge apparaat en het leaf-apparaat. Het leaf-apparaat is een downstream apparaat dat het IoT Edge-apparaat als een gateway gebruikt om te communiceren met IoT Hub.

Verder in de zelfstudie gaan we verder met het leaf-apparaat. In dit gedeelte downloadt u de certificaten naar het IoT Edge-apparaat.

1. Meld u vanuit de SSH-sessie op de virtuele Linux-machine aan bij Azure met de Azure CLI.

    ```azurecli
    az login
    ```

1. U wordt gevraagd om een browser te openen op een aanmeldingspagina van [Microsoft-apparaten](https://microsoft.com/devicelogin) en een unieke code op te geven. U kunt deze stappen uitvoeren op uw lokale computer. Sluit het browservenster wanneer u klaar bent met verifiëren.

1. Wanneer u bent geverifieerd, wordt de Linux-VM aangemeld en worden uw Azure-abonnementen weergegeven.

1. Stel het Azure-abonnement in dat u wilt gebruiken voor Azure CLI-opdrachten.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Maak een map op de VM voor de certificaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Download de certificaten die u hebt opgeslagen in de sleutelkluis: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem en azure-iot-test-only.root.ca.cert.pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Configuratie van IoT Edge-apparaat bijwerken

De IoT Edge runtime gebruikt het bestand /etc/iotedge/config.yaml om de configuratie te blijven gebruiken. Er moeten drie gegevens worden bijgewerkt in dit bestand:

* **Apparaat connection string:** de connection string van de identiteit van dit apparaat in IoT Hub
* **Certificaten:** de certificaten die moeten worden gebruikt voor verbindingen met downstreamapparaten
* **Hostnaam:** de FQDN (Fully Qualified Domain Name) van het VM-IoT Edge apparaat

De Azure IoT Edge op Ubuntu-installatie afbeelding die we hebben gebruikt om de IoT Edge-VM te maken, wordt geleverd met een shellscript waarmee het bestand config.yaml wordt bijgewerkt met de connection string.

1. Klik Visual Studio Code met de rechtermuisknop op IoT Edge apparaat en selecteer **vervolgens Apparaatverbindingsreeks kopiëren.**

    ![Schermopname van het kopiëren van de connection string uit Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Voer in de SSH-sessie de volgende opdracht uit om het bestand config.yaml bij te werken met de verbindingsreeks van uw apparaat.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Vervolgens werken we de certificaten en hostnaam bij door het bestand config.yaml rechtstreeks te bewerken.

1. Open het bestand config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Werk de sectie certificaten van het bestand config.yaml bij door het vooraanstaand bestand te verwijderen en het pad in te stellen, zodat het bestand eruitziet **#** als in het volgende voorbeeld:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Zorg ervoor dat **de regel certificaten:** geen voorafgaande spaties heeft en dat elk van de geneste certificaten door twee spaties is ingesprongen.

    Als u met de rechtermuisknop klikt in nano, wordt de inhoud van het klembord op de huidige cursorpositie geplakt. Als u de tekenreeks wilt vervangen, gebruikt u de toetsenbordpijlen om naar de tekenreeks te gaan die u wilt vervangen, verwijdert u de tekenreeks en klikt u vervolgens met de rechtermuisknop om uit de buffer te plakken.

1. Ga in Azure Portal naar uw virtuele machine. Kopieer de DNS-naam (FQDN van de computer) uit het gedeelte **Overzicht**.

1. Plak de FQDN in de sectie hostnaam van het bestand config.yml. Zorg ervoor dat de naam alleen kleine letters heeft.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Sla het bestand op en sluit het door **Ctrl+X,** **Y** en **Enter te selecteren.**

1. Start de IoT Edge daemon opnieuw.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Controleer de status van de IoT Edge daemon. Voer na de opdracht **:q in om** af te sluiten.

    ```bash
    systemctl status iotedge
    ```

1. Als er fouten (gekleurde tekst met het voorvoegsel ERROR ") in de status worden weergegeven, bekijkt u \[ \] de daemonlogboeken voor gedetailleerde foutinformatie.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht totdat u de laatste zelfstudie hebt voltooid om alle resources op te schonen.

## <a name="next-steps"></a>Volgende stappen

We hebben zojuist de configuratie van een Virtuele Azure-IoT Edge een transparante gateway voltooid. We begonnen met het genereren van testcertificaten die we hebben geüpload naar Key Vault. Vervolgens hebben we een script en Resource Manager-sjabloon gebruikt om de VM te implementeren met de Ubuntu Server 16.04 LTS + Azure IoT Edge runtime-installatie Azure Marketplace. Nu de VM actief is, hebben we verbinding via SSH. Vervolgens hebben we ons aangemeld bij Azure en certificaten gedownload van Key Vault. We hebben verschillende updates aangebracht in de configuratie van de IoT Edge runtime door het bestand config.yaml bij te werken.

Ga verder naar het volgende artikel om IoT Edge-modules te maken.

> [!div class="nextstepaction"]
> [Aangepaste IoT Edge-modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md)
