---
title: 'Zelfstudie: IoT Edge apparaat configureren - machine learning in Azure IoT Edge'
description: In deze zelfstudie configureert u een virtuele Azure-machine waarop Linux wordt uitgevoerd als een Azure IoT Edge-apparaat dat als een transparante gateway fungeert.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: 74d77d8c81455116cec861bf6704c6cb96526561
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121087"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Zelfstudie: een IoT Edge-apparaat configureren

In dit artikel gaan we een virtuele Azure-machine waarop Linux wordt uitgevoerd, configureren als een IoT Edge-apparaat dat als een transparante gateway fungeert. Met een dergelijke configuratie kunnen apparaten via de gateway verbinding maken met Azure IoT Hub zonder dat ze van het bestaan van de gateway af weten. Een gebruiker die interactie heeft met de apparaten in Azure IoT Hub is evenmin op de hoogte van het tussenliggende gatewayapparaat. Als laatste gaan we edge-analytics toevoegen aan ons systeem door IoT Edge-modules toe te voegen aan de transparante gateway.

De stappen in dit artikel worden doorgaans uitgevoerd door een cloud-ontwikkelaar.

In dit deel van de zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Certificaten maken om uw gateway-apparaat veilig verbinding te laten maken met uw downstream-apparaten.
> * Een IoT Edge-apparaat maken.
> * Een virtuele Azure-machine maken om uw IoT Edge-apparaat te simuleren.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel terecht bent gekomen, gaat u naar de [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="create-certificates"></a>Certificaten maken

Een apparaat kan alleen als gateway functioneren als het op veilige manier verbinding kan maken met downstream apparaten. Met Azure IoT Edge kunt u een PKI (Public Key Infrastructure) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval geven we een downstream IoT-apparaat toestemming om verbinding te maken met een IoT Edge-apparaat dat als transparante gateway fungeert. Om een redelijke beveiliging te garanderen, moet het downstream apparaat de identiteit van het IoT Edge apparaat bevestigen. Zie [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md) (Begrijpen hoe Azure IoT Edge certificaten gebruikt) voor meer informatie over hoe IoT Edge-apparaten certificaten gebruiken.

In dit gedeelte maken we de zelfondertekende certificaten met behulp van een Docker-installatiekopie die we vervolgens compileren en uitvoeren. We hebben ervoor gekozen om een Docker-installatiekopie te gebruiken voor deze stap omdat het aantal stappen dat nodig is voor het maken van de certificaten op de Windows-ontwikkelcomputer hierdoor aanzienlijk wordt gereduceerd. Zie [Create demo certificates to test IoT Edge device features](how-to-create-test-certificates.md) (Democertificaten maken om de functies van IoT Edge-apparaten te testen) om te begrijpen wat er met de Docker-installatiekopie wordt geautomatiseerd.

1. Meld u aan bij uw ontwikkel-VM.

2. Maak een nieuwe map met het pad en de naam `c:\edgeCertificates`.

3. Start **Docker voor Windows** vanuit het menu Start van Windows als het programma nog niet is gestart.

4. Open Visual Studio Code.

5. Selecteer **File** > **Open Folder...** en kies **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Klik in het deelvenster Explorer met de rechtermuisknop op **dockerfile** en kies **Build Image**.

7. Accepteer in het dialoogvenster de standaardwaarde voor de naam en het label van de installatiekopie: **createcertificates:latest**.

    ![Certificaten maken in Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Wacht tot de compilatie is voltooid.

    > [!NOTE]
    > Mogelijk ziet u een waarschuwing over een ontbrekende openbare sleutel. U kunt deze waarschuwing negeren. Er wordt ook een beveiligingswaarschuwing weergegeven met het advies om de machtigingen voor de installatiekopie te controleren/opnieuw in te stellen. Dit advies kunt u in dit geval eveneens negeren.

9. Voer in het terminalvenster van Visual Studio Code de container createcertificates uit.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker vraagt om toegang tot het **c:\\** -station. Selecteer **Share it**.

11. Voer uw referenties in wanneer hierom wordt gevraagd.

12. Als de container is uitgevoerd, controleert u of de volgende bestanden aanwezig in **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Certificaten uploaden naar Azure Key Vault

Om onze certificaten veilig op te slaan en ze toegankelijk te maken vanaf meerdere apparaten, gaan we de certificaten uploaden naar Azure Key Vault. Zoals u kunt zien in de bovenstaande lijst, hebben we twee typen certificaatbestanden: PFX en PEM. De PFX-bestanden behandelen we als Key Vault-certificaten die worden geüpload naar Key Vault. De PEM-bestanden zijn tekst zonder opmaak en deze behandelen we als Key Vault-geheimen. We gebruiken de sleutelkluis die is gekoppeld aan de Azure Machine Learning-werkruimte die we hebben gemaakt door de [Jupyter Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-jupyter-notebooks) uit te voeren.

1. Ga in [Azure Portal](https://portal.azure.com) naar uw Azure Machine Learning-werkruimte.

2. Zoek op de pagina Overzicht van de werkruimte de naam van de **sleutelkluis**.

    ![Naam van sleutelkluis kopiëren](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Upload de certificaten op de ontwikkelcomputer naar Key Vault. Vervang **\<subscriptionId\>** en **\<keyvaultname\>** door uw resourcegegevens.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Meld u aan bij Azure als u hierom wordt gevraagd.

5. Het script wordt gedurende enkele minuten uitgevoerd en laat dan een lijst zien met daarin de nieuwe Key Vault-vermeldingen.

    ![Uitvoer van Key Vault-script](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Een IoT Edge-apparaat maken

Om een Azure IoT Edge-apparaat te verbinden met een IoT-hub, maken we eerst een identiteit voor het apparaat in de hub. We nemen de verbindingsreeks van de apparaat-id in de cloud en gebruiken die om de runtime op ons IoT Edge-apparaat te configureren. Zodra een geconfigureerd apparaat verbinding maakt met de hub, kunnen we modules implementeren en berichten verzenden. We kunnen ook de configuratie van het fysieke IoT Edge-apparaat wijzigen door de bijbehorende apparaat-id in IoT hub aan te passen.

Voor deze zelfstudie maken we de nieuwe apparaat-id met behulp van Visual Studio Code. U kunt deze stappen ook uitvoeren met de Azure-portal of de Azure CLI.

1. Open Visual Studio Code op uw ontwikkelcomputer.

2. Vouw in de Explorer van Visual Studio Code het frame **Azure IoT Hub** uit.

3. Klik op het weglatingsteken en selecteer **Create IoT Edge Device**.

4. Geef het apparaat een naam. Voor het gemak gebruiken we de naam **aaTurbofanEdgeDevice** zodat het apparaat bovenaan de lijst met apparaten komt te staan.

5. Het nieuwe apparaat wordt weergegeven in de lijst met apparaten.

    ![Nieuw aaTurbofanEdgeDevice in Explorer van VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Virtuele Azure-machine implementeren

We gebruiken de installatiekopie [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) van de Azure Marketplace om het IoT Edge-apparaat voor deze zelfstudie te maken. Met deze installatiekopie worden de meest recente Azure IoT Edge-runtime en de bijbehorende afhankelijkheden geïnstalleerd bij het opstarten. We implementeren de VM met behulp van een PowerShell-script, `Create-EdgeVM.ps1`, een Resource Manager-sjabloon, `IoTEdgeVMTemplate.json`, en een shell-script, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Programmatische implementatie inschakelen

Als u de installatiekopie van de Marketplace in een scriptimplementatie wilt gebruiken, moet u programmatische implementatie inschakelen voor de installatiekopie.

1. Meld u aan bij de Azure-portal.

1. Selecteer **Alle services**.

1. Typ **Marketplace** in het zoekvak en druk op Enter.

1. Typ **Azure IoT Edge on Ubuntu** in het zoekvak van Marketplace en druk op Enter.

1. Selecteer de hyperlink **Aan de slag** om programmatisch te implementeren.

1. Selecteer de knop **Inschakelen** en daarna de knop **Opslaan**.

    ![Programmatische implementatie inschakelen voor VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. U ziet een melding dat de taak is voltooid.

### <a name="create-virtual-machine"></a>Virtuele machine maken

Voer vervolgens het script uit om de virtuele machine voor uw IoT Edge-apparaat te maken.

1. Open een PowerShell-venster en ga naar de map **EdgeVM**.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Voer het script uit om de virtuele machine te maken.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Geef waarden op voor elke parameter wanneer u hierom wordt gevraagd. Voor het abonnement, de resourcegroep en de locatie raden we aan om de waarden te gebruiken die u eerder hebt opgegeven voor de resources in deze zelfstudie.

    * **Azure-abonnements-id**: u vindt de abonnements-id in Azure Portal.
    * **Naam van resourcegroep**: een makkelijk te onthouden naam voor het groeperen van de resources voor deze zelfstudie.
    * **Locatie**: kies een Azure-locatie waarin de virtuele machine wordt gemaakt, bijvoorbeeld west 2. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/) voor meer informatie.
    * **AdminUsername**: de naam voor het beheerdersaccount dat u gebruikt om u aan te melden bij de virtuele machine.
    * **AdminPassword**: het wachtwoord dat moet worden ingesteld voor de AdminUsername op de virtuele machine.

4. Als u wilt dat het script de VM kan instellen, moet u zich aanmelden bij Azure met de referenties die zijn gekoppeld aan het Azure-abonnement dat u gebruikt.

5. U ziet een bericht dat met het script een VM wordt gemaakt. Druk op **y** of **Enter** om door te gaan.

6. Het script wordt enkele minuten uitgevoerd terwijl de volgende stappen worden uitgevoerd:

    * De resourcegroep maken als deze nog niet bestaat
    * De virtuele machine maken
    * NSG-uitzonderingen voor de VM toevoegen voor de poorten 22 (SSH), 5671 (AMQP), 5672 (AMPQ) en 443 (TLS)
    * Installeer de [Azure CLI](/cli/azure/install-azure-cli-apt)

7. Het script voert de SSH-verbindingsreeks uit die nodig is om verbinding te maken met de VM. Kopieer de verbindingsreeks voor de volgende stap.

    ![SSH-verbindingsreeks kopiëren voor VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Verbinding maken met uw IoT Edge-apparaat

In de volgende gedeelten gaan we de virtuele Azure-machine configureren die we hebben gemaakt. De eerste stap is met maken van verbinding met de virtuele machine.

1. Open een opdrachtprompt en plak hier de SSH-verbindingsreeks die u hebt gekopieerd uit de scriptuitvoer. Voer uw eigen gegevens in voor de gebruikersnaam, het achtervoegsel en de regio, overeenkomstig de waarden die u hebt opgegeven voor het PowerShell-script in het vorige gedeelte.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Wanneer u wordt gevraagd om de authenticiteit van de host te valideren, typt u **yes** en drukt u op **Enter**.

3. Geef uw wachtwoord op als dit wordt gevraagd.

4. In Ubuntu wordt een welkomstbericht weergegeven en u ziet daarna een prompt zoals `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Key Vault-certificaten downloaden

Eerder in dit artikel hebben we certificaten geüpload naar Key Vault om ze beschikbaar te maken voor het IoT Edge apparaat en het leaf-apparaat. Het leaf-apparaat is een downstream apparaat dat het IoT Edge-apparaat als een gateway gebruikt om te communiceren met IoT Hub.

Verderop in de zelfstudie vindt u meer informatie over het leaf-apparaat. In dit gedeelte downloadt u de certificaten naar het IoT Edge-apparaat.

1. Meld u vanuit de SSH-sessie op de virtuele Linux-machine aan bij Azure met de Azure CLI.

    ```azurecli
    az login
    ```

1. U wordt gevraagd om een browser te openen en naar <https://microsoft.com/devicelogin> te gaan om daar een unieke code op te geven. U kunt deze stappen uitvoeren op uw lokale computer. Sluit het browservenster wanneer u klaar bent met verifiëren.

1. Wanneer u bent geverifieerd, wordt de Linux-VM aangemeld en worden uw Azure-abonnementen weergegeven.

1. Stel het Azure-abonnement in dat u wilt gebruiken voor Azure CLI-opdrachten.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Maak een map op de VM voor de certificaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Download de certificaten die u hebt opgeslagen in de sleutelkluis: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem en azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Configuratie van IoT Edge-apparaat bijwerken

De IoT Edge-runtime gebruikt het bestand `/etc/iotedge/config.yaml` om de configuratie persistent te maken. Er moeten drie gegevens worden bijgewerkt in dit bestand:

* **Verbindingsreeks voor apparaat**: de verbindingsreeks van de identiteit van dit apparaat in IoT Hub
* **Certificaten:** de certificaten die moeten worden gebruikt voor verbindingen met downstream apparaten
* **Hostnaam:** de FQDN (Fully Qualified Domain Name) van het VM IoT Edge-apparaat.

De installatiekopie *Azure IoT Edge on Ubuntu* die we hebben gebruikt om de IoT Edge-VM te maken, bevat een shell-script waarmee het bestand config.yaml wordt bijgewerkt met de verbindingsreeks.

1. Klik in Visual Studio Code met de rechtermuisknop op het IoT Edge-apparaat en selecteer vervolgens **Copy Device Connection String**.

    ![Verbindingsreeks kopiëren uit Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Voer in de SSH-sessie de volgende opdracht uit om het bestand config.yaml bij te werken met de verbindingsreeks van uw apparaat.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Vervolgens gaan we de certificaten en de hostnaam bijwerken door het bestand config.yaml rechtstreeks te bewerken.

1. Open het bestand config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Werk de sectie certificates van config.yaml bij door de `#` aan het begin te verwijderen en het pad zodanig in te stellen dat het bestand eruitziet als in het volgende voorbeeld:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Zorg ervoor dat de regel **certificates:** geen voorloopspaties heeft en dat alle geneste certificaten met twee spaties worden ingesprongen.

    Als u met de rechtermuisknop klikt in nano, wordt de inhoud van het klembord op de huidige cursorpositie geplakt. Als u de tekenreeks wilt vervangen, gebruikt u de pijlen op het toetsenbord om naar de tekenreeks te gaan die u wilt vervangen. Verwijder de tekenreeks en klik met de rechtermuisknop om de inhoud van de buffer te plakken.

3. Navigeer in Azure Portal naar uw nieuwe virtuele machine. Kopieer de DNS-naam (FQDN van de computer) uit het gedeelte **Overzicht**.

4. Plak de FQDN in de sectie hostname van het bestand config.yml. Zorg ervoor dat de naam alleen uit kleine letters bestaat.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Sla het bestand op en sluit het vervolgens (`Ctrl + X`, `Y`, `Enter`).

6. Start de iotedge-daemon opnieuw.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Controleer de status van de IoT Edge-daemon (typ na de opdracht ':q' om af te sluiten).

    ```bash
    systemctl status iotedge
    ```

8. Als u fouten ziet (gekleurde tekst voorafgegaan door \[ERROR\]) in de status, raadpleegt u de daemon-logboeken voor gedetailleerde informatie over de fout.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht met het opschonen van resources totdat u de laatste zelfstudie hebt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

We hebben een Azure-VM geconfigureerd als een transparante gateway van Azure IoT Edge. We hebben eerst testcertificaten gegenereerd die we daarna hebben geüpload naar Azure Key Vault. Vervolgens hebben we een script en een Resource Manager-sjabloon gebruikt om de VM te implementeren met de installatiekopie Ubuntu Server 16.04 LTS + Azure IoT Edge runtime van Azure Marketplace. Met de VM actief hebben we via SSH verbinding gemaakt, ons aangemeld bij Azure en certificaten gedownload uit Key Vault. De configuratie van de IoT Edge-runtime hebben we hier en daar aangepast door het bestand config.yaml bij te werken.

Ga verder naar het volgende artikel om IoT Edge-modules te maken.

> [!div class="nextstepaction"]
> [Aangepaste IoT Edge-modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md)
