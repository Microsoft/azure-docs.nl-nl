---
title: Apparaten inrichten voor multitenancy in Azure IoT Hub Device Provisioning Service
description: Apparaten inrichten voor multitenancy met uw DPS-exemplaar (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0b88923ff6447785a4ef5a7c80e1ff44d1a2b9cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777315"
---
# <a name="how-to-provision-for-multitenancy"></a>Inrichten voor multitenancy 

In dit artikel wordt gedemonstreerd hoe u veilig meerdere symmetrische sleutelapparaten kunt inrichten voor een groep IoT Hubs met behulp van [een toewijzingsbeleid.](concepts-service.md#allocation-policy) Toewijzingsbeleid dat door de inrichtingsservice is gedefinieerd, ondersteunt diverse toewijzingsscenario's. Twee veelvoorkomende scenario's zijn:

* **Geolocatie/GeoLatency:** wanneer een apparaat tussen locaties wordt verplaatst, wordt de netwerklatentie verbeterd door het apparaat in te stellen voor de IoT-hub die zich het dichtst bij elke locatie bevindt. In dit scenario wordt een groep IoT-hubs, die meerdere regio's bespannen, geselecteerd voor inschrijvingen. Het **toewijzingsbeleid Laagste latentie** is geselecteerd voor deze inschrijvingen. Dit beleid zorgt ervoor dat Device Provisioning Service de latentie van het apparaat evalueert en de juiste IoT-hub van de groep IoT-hubs bepaalt. 

* **Meerdere tenancy:** apparaten die in een IoT-oplossing worden gebruikt, moeten mogelijk worden toegewezen aan een specifieke IoT-hub of groep IoT-hubs. Voor de oplossing moeten mogelijk alle apparaten voor een bepaalde tenant communiceren met een specifieke groep IoT-hubs. In sommige gevallen is een tenant mogelijk eigenaar van IoT-hubs en moeten apparaten worden toegewezen aan hun IoT-hubs.

Het is gebruikelijk om deze twee scenario's te combineren. Met een IoT-oplossing met meerdere tenants worden bijvoorbeeld vaak tenantapparaten toegewezen met behulp van een groep IoT-hubs die zijn verspreid over regio's. Deze tenantapparaten kunnen worden toegewezen aan de IoT-hub in die groep, die de laagste latentie heeft op basis van geografische locatie.

In dit artikel wordt een voorbeeld van een gesimuleerd apparaat uit de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt om te laten zien hoe u apparaten inrichten in een multitenant-scenario tussen regio's. In dit artikel voert u de volgende stappen uit:

> [!div class="checklist"]
> * De Azure CLI gebruiken om twee regionale IoT-hubs te maken **(VS - west** en **VS - oost)**
> * Een multitenant-inschrijving maken
> * Gebruik de Azure CLI om twee regionale virtuele Linux-VM's te maken om te fungeren als apparaten in dezelfde regio's **(VS -** west en VS **- oost)**
> * De ontwikkelomgeving instellen voor de Azure IoT C SDK op beide Linux-VM's
> * Simuleer de apparaten om te zien dat ze zijn ingericht voor dezelfde tenant in de dichtstbijzijnde regio.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

- Voltooiing van de [quickstart IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) quickstart.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Twee regionale IoT-hubs maken

In deze sectie gebruikt u de Azure Cloud Shell om twee nieuwe regionale IoT-hubs te maken in de regio's **VS** - west en VS - oost voor een tenant. 


1. Gebruik de Azure Cloud Shell om een resourcegroep te maken met de [opdracht az group create.](/cli/azure/group#az_group_create) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

    In het volgende voorbeeld wordt een resourcegroep met de *naam contoso-us-resource-group* gemaakt in de *regio eastus.* U wordt aangeraden deze groep te gebruiken voor alle resources die in dit artikel zijn gemaakt. Dit maakt het eenvoudiger om op te schonen nadat u klaar bent.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Gebruik de Azure Cloud Shell om een IoT-hub te maken in de regio **eastus** met de [opdracht az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) De IoT-hub wordt toegevoegd aan *de contoso-us-resource-group*.

    In het volgende voorbeeld wordt een IoT-hub met de *naam contoso-east-hub* gemaakt op de *locatie VS* Oost. U moet uw eigen unieke hubnaam gebruiken in plaats van **contoso-east-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Het volledig uitvoeren van de opdracht kan even duren.

3. Gebruik de Azure Cloud Shell om een IoT-hub te maken in de regio **westus** met de [opdracht az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) Deze IoT-hub wordt ook toegevoegd aan *de contoso-us-resource-group*.

    In het volgende voorbeeld wordt een IoT-hub met de *naam contoso-west-hub* gemaakt op de *locatie westus.* U moet uw eigen unieke hubnaam gebruiken in plaats van **contoso-west-hub.**

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Het volledig uitvoeren van de opdracht kan even duren.



## <a name="create-the-multitenant-enrollment"></a>De multitenant-inschrijving maken

In deze sectie maakt u een nieuwe registratiegroep voor de tenantapparaten.  

Ter vereenvoudiging wordt in dit artikel bij de registratie gebruikgemaakt van een [verklaring met symmetrische sleutels](concepts-symmetric-key-attestation.md). Voor een veiligere oplossing kunt u gebruikmaken van de [verklaring met het X.509-certificaat](concepts-x509-attestation.md) met een vertrouwensketen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com)en open uw Device Provisioning Service-exemplaar.

2. Selecteer **het tabblad Registraties beheren** en klik vervolgens op de knop **Inschrijvingsgroep toevoegen** boven aan de pagina. 

3. Voer **in Registratiegroep toevoegen** de volgende gegevens in en klik op de **knop** Opslaan.

    **Groepsnaam:** voer **contoso-us-devices in.**

    **Attestation-type:** selecteer **Symmetrische sleutel.**

    **Sleutels automatisch genereren:** dit selectievakje moet al zijn ingeschakeld.

    **Selecteer hoe u apparaten wilt toewijzen aan hubs:** selecteer **Laagste latentie.**

    ![Multitenant-registratiegroep toevoegen voor attestation met symmetrische sleutel](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Klik **in Registratiegroep toevoegen op** Een nieuwe **IoT-hub koppelen om** beide regionale hubs te koppelen.

    **Abonnement:** als u meerdere abonnementen hebt, kiest u het abonnement waarin u de regionale IoT-hubs hebt gemaakt.

    **IoT-hub:** selecteer een van de regionale hubs die u hebt gemaakt.

    **Toegangsbeleid:** kies **iothubowner**.

    ![De regionale IoT-hubs koppelen aan de inrichtingsservice](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Zodra beide regionale IoT-hubs zijn gekoppeld, moet u deze  selecteren voor de registratiegroep en op Opslaan klikken om de regionale IoT-hubgroep voor de inschrijving te maken.

    ![De regionale hubgroep maken voor de inschrijving](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Nadat u de registratie hebt opgeslagen, opent u deze opnieuw en noteert u de **primaire sleutel**. U moet de registratie eerst opslaan voordat de sleutels kunnen worden gegenereerd. Deze sleutel wordt later gebruikt voor het genereren van unieke apparaatsleutels voor beide gesimuleerde apparaten.


## <a name="create-regional-linux-vms"></a>Regionale Linux-VM's maken

In deze sectie maakt u twee regionale virtuele Linux-machines (VM's). Op deze VM's wordt een voorbeeld van apparaatsimulatie uit elke regio uitgevoerd om het inrichten van apparaten voor tenantapparaten uit beide regio's te demonstreren.

Om het ops schonen gemakkelijker te maken, worden deze VM's toegevoegd aan dezelfde resourcegroep die de IoT-hubs bevat die zijn gemaakt, *contoso-us-resource-group.* De VM's worden echter uitgevoerd in afzonderlijke regio's **(VS - west** en **VS - oost).**

1. Voer in Azure Cloud Shell volgende opdracht uit om  een VM in de regio VS - oost te maken nadat u de volgende parameterwijzigingen in de opdracht heeft aangebracht:

    **--name:** Voer een unieke naam in voor de regionale apparaat-VM **VS** - oost. 

    **--admin-username:** gebruik uw eigen gebruikersnaam voor de beheerder.

    **--admin-password:** gebruik uw eigen beheerderswachtwoord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Het uitvoeren van deze opdracht duurt enkele minuten. Zodra de opdracht is voltooid, noteert u de **publicIpAddress-waarde** voor de VM in de regio VS - oost.

1. Voer in Azure Cloud Shell opdracht uit om een  VM in de regio VS - west te maken nadat u de volgende parameterwijzigingen in de opdracht heeft aangebracht:

    **--name:** Voer een unieke naam in voor uw regionale apparaat-VM **VS** - west. 

    **--admin-username:** gebruik uw eigen gebruikersnaam voor de beheerder.

    **--admin-password:** gebruik uw eigen beheerderswachtwoord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Het uitvoeren van deze opdracht duurt enkele minuten. Zodra de opdracht is voltooid, noteert u de **publicIpAddress-waarde** voor de VM in de regio VS - west.

1. Open twee opdrachtregelshells. Maak verbinding met een van de regionale VM's in elke shell met behulp van SSH. 

    Geef de gebruikersnaam van de beheerder en het openbare IP-adres dat u voor de VM hebt genoteerd als parameters door aan SSH. Voer het beheerderswachtwoord in wanneer u hier om wordt gevraagd.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>De ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie kloont u de Azure IoT C SDK op elke VM. De SDK bevat een voorbeeld dat de apparaatinrichting van een tenant vanuit elke regio simuleert.

1. Installeer voor elke VM **CMake,** **g++**, **gcc** en [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) met behulp van de volgende opdrachten:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de SDK.

1. Kloon [de Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) op beide VM's.  Gebruik de tag die u in de vorige stap hebt gevonden als waarde voor de parameter `-b`:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Voor beide VM's maakt u een **nieuwe cmake-map** in de opslagplaats en gaat u naar die map.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Voer voor beide VM's de volgende opdracht uit, waarmee een versie van de SDK wordt gebouwd die specifiek is voor uw clientplatform voor ontwikkeling. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Unieke apparaatsleutels afleiden

Wanneer u attestation met symmetrische sleutels gebruikt met groepsinschrijvingen, gebruikt u de registratiegroepssleutels niet rechtstreeks. In plaats daarvan maakt u een unieke afgeleide sleutel voor elk apparaat en vermeld in [Groepsinschrijvingen met symmetrische sleutels.](concepts-symmetric-key-attestation.md#group-enrollments)

Als u de apparaatsleutel wilt genereren, gebruikt u de groepsmastersleutel om een [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-id voor het apparaat te berekenen en converteert u het resultaat naar base64-indeling.

Neem uw groepssleutel niet op in de apparaatcode.

Gebruik het Bash-shellvoorbeeld om voor elk apparaat een afgeleide apparaatsleutel te maken met **behulp van openssl.**

- Vervang de waarde voor **KEY** door de **primaire sleutel die** u eerder hebt genoteerd voor uw inschrijving.

- Vervang de waarde voor **REG_ID** door uw eigen unieke registratie-id voor elk apparaat. Gebruik alfanumerieke tekens in kleine letters en streepjes ('-') om beide ID's te definiëren.

Voorbeeld van het genereren van *apparaatsleutels voor contoso-simdevice-east:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Voorbeeld van het genereren van *apparaatsleutels voor contoso-simdevice-west:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


De tenantapparaten gebruiken elk hun afgeleide apparaatsleutel en unieke registratie-id om tijdens het inrichten naar de tenant-IoT-hubs een attestation met een symmetrische sleutel uit te voeren met de registratiegroep.




## <a name="simulate-the-devices-from-each-region"></a>De apparaten uit elke regio simuleren


In deze sectie gaat u een inrichtingsvoorbeeld bijwerken in de Azure IoT C SDK voor beide regionale VM's. 

De voorbeeldcode simuleert een opstartvolgorde van het apparaat die de inrichtingsaanvraag naar uw Device Provisioning Service-exemplaar verzendt. De opstartvolgorde zorgt ervoor dat het apparaat wordt herkend en toegewezen aan de IoT-hub die het dichtst bij ligt op basis van latentie.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Open **~/azure-iot-sdk-c/provisioning \_ client/samples/prov \_ dev \_ client \_ sample/prov \_ dev client \_ \_ sample.c** voor bewerking op beide VM's.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat `hsm_type` de variabele is ingesteld op , zoals hieronder wordt weergegeven, om overeen te komen met de `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` attestation-methode voor de inschrijvingsgroep. 

    Sla de wijzigingen in de bestanden op beide VM's op.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Zoek op beide VM's de aanroep naar `prov_dev_set_symmetric_key_info()` in **prov \_ dev \_ client \_ sample.c,** waarvoor een opmerking is opgenomen.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    De aanroepen van de functie verwijderen en de waarden van de tijdelijke aanduiding (inclusief de vierkante haken) vervangen door de unieke registratie-ID's en afgeleide apparaatsleutels voor elk apparaat. De onderstaande sleutels zijn alleen bedoeld als voorbeeld. Gebruik de sleutels die u eerder hebt gegenereerd.

    VS - oost:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    VS - west:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Sla de bestanden op.

1. Navigeer op beide VM's naar de voorbeeldmap die hieronder wordt weergegeven en bouw het voorbeeld.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Zodra de build is geslaagd, kunt u **de prov \_ dev-client \_ \_** uitvoerensample.exebeide VM's om een tenantapparaat uit elke regio te simuleren. U ziet dat elk apparaat wordt toegewezen aan de tenant-IoT-hub die het dichtst bij de regio's van het gesimuleerde apparaat ligt.

    Voer de simulatie uit:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Voorbeelduitvoer van de VM VS - oost:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Voorbeelduitvoer van de VM VS - west:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met resources die in dit artikel zijn gemaakt, kunt u ze laten. Als u niet van plan bent om de resource te blijven gebruiken, gebruikt u de volgende stappen om alle resources te verwijderen die in dit artikel zijn gemaakt om onnodige kosten te voorkomen.

Bij de stappen die hier worden beschreven, wordt ervan uitgegaan dat u alle resources in dit artikel volgens de instructies hebt gemaakt in dezelfde resourcegroep met de naam **contoso-us-resource-group**.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de IoT Hub in een bestaande resourcegroep hebt gemaakt met resources die u wilt behouden, moet u alleen de IoT Hub-resource zelf verwijderen in plaats van de resourcegroep te verwijderen.
>

U verwijdert als volgt de resourcegroep op naam:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Resourcegroepen**.

2. Typ in het tekstvak **Filteren op naam...** de naam van de resourcegroep die uw resources bevat, **contoso-us-resource-group**. 

3. Klik rechts van de resourcegroep in de lijst met resultaten op **...** en vervolgens op **Resourcegroep verwijderen**.

4. U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ de naam van de resourcegroep nogmaals om te bevestigen en klik op **Verwijderen**. Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over het opnieuw invisioneren

> [!div class="nextstepaction"]
> [IoT Hub voor het opnieuw in de inrichting van apparaten](concepts-device-reprovision.md)

* Zie voor meer informatie over het deprovisioning
> [!div class="nextstepaction"]
> [De inrichting van apparaten die eerder automatisch zijn ingericht, inrichten](how-to-unprovision-devices.md)