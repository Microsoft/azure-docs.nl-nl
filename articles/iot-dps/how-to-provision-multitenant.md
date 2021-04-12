---
title: Apparaten inrichten voor multitenancy in azure IoT Hub Device Provisioning Service
description: Apparaten voor multitenancy inrichten met het DPS-exemplaar (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 04ab4ae4d3de6f33f800011b9b0802665d5fb16e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228313"
---
# <a name="how-to-provision-for-multitenancy"></a>Inrichten voor multitenancy 

In dit artikel wordt beschreven hoe u met behulp van een [toewijzings beleid](concepts-service.md#allocation-policy)veilig meerdere symmetrische-sleutel apparaten kunt inrichten voor een groep van IOT-hubs. Toewijzings beleid dat door de inrichtings service is gedefinieerd, ondersteunt diverse toewijzings scenario's. Twee veelvoorkomende scenario's zijn:

* **Geolocatie/geolatentie**: wanneer een apparaat tussen locaties wordt verplaatst, wordt de netwerk latentie verbeterd doordat het apparaat wordt ingericht voor de IOT-hub die het dichtst bij elke locatie is. In dit scenario worden een groep IoT-hubs, die over verschillende regio's vallen, geselecteerd voor inschrijvingen. Het **laagste** toewijzings beleid voor de latentie is geselecteerd voor deze inschrijvingen. Dit beleid zorgt ervoor dat de Device Provisioning Service de latentie van het apparaat evalueert en de kast IoT-hub uit de groep van IoT-hubs bepaalt. 

* **Multitenancy: apparaten** die worden gebruikt binnen een IOT-oplossing moeten mogelijk worden toegewezen aan een specifieke IOT-hub of groep van IOT-hubs. De oplossing vereist mogelijk dat alle apparaten voor een bepaalde Tenant communiceren met een specifieke groep IoT hubs. In sommige gevallen kan een Tenant eigenaar zijn van IoT-hubs en vereisen dat apparaten worden toegewezen aan hun IoT-hubs.

Het is gebruikelijk om deze twee scenario's te combi neren. Zo zal een multi tenant IoT-oplossing meestal Tenant apparaten toewijzen met behulp van een groep IoT-hubs die verspreid over verschillende regio's zijn. Deze Tenant apparaten kunnen worden toegewezen aan de IoT-hub in die groep, met de laagste latentie op basis van de geografische locatie.

In dit artikel wordt een voor beeld van een gesimuleerd apparaat uit de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt om te demonstreren hoe u apparaten inricht in een multi tenant-scenario in verschillende regio's. In dit artikel voert u de volgende stappen uit:

> [!div class="checklist"]
> * De Azure CLI gebruiken om twee regionale IoT-hubs te maken (VS-**West** en **VS-Oost**)
> * Een multi tenant-inschrijving maken
> * De Azure CLI gebruiken om twee regionale virtuele Linux-machines te maken die als apparaten in dezelfde regio's kunnen fungeren (**VS-West** en **VS-Oost**)
> * De ontwikkel omgeving voor de Azure IoT C-SDK instellen op virtuele Linux-machines
> * Simuleer de apparaten om te zien dat ze zijn ingericht voor dezelfde Tenant in de dichtstbijzijnde regio.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

- Volt ooien van het [instellen van IOT hub Device Provisioning Service met de Azure Portal](./quick-setup-auto-provision.md) Snelstartgids.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Twee regionale IoT-hubs maken

In deze sectie gebruikt u de Azure Cloud Shell om twee nieuwe regionale IoT-hubs te maken in de regio's **VS-West** en VS- **Oost** voor een Tenant.


1. Gebruik de Azure Cloud Shell om een resource groep te maken met de opdracht [AZ Group Create](/cli/azure/group#az-group-create) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

    In het volgende voor beeld wordt een resource groep met de naam *Contoso-US-Resource-Group* in de regio *eastus* gemaakt. Het wordt aanbevolen deze groep te gebruiken voor alle resources die in dit artikel zijn gemaakt. Dit maakt de opschoon bewerking gemakkelijker nadat u klaar bent.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Gebruik de Azure Cloud Shell om een IoT-hub in de regio **Oost** te maken met de opdracht [AZ IOT hub Create](/cli/azure/iot/hub#az-iot-hub-create) . De IoT-hub wordt toegevoegd aan de *groep contoso-US-Resource-Group*.

    In het volgende voor beeld wordt een IoT-hub gemaakt met de naam *Contoso-Oost-hub* op de locatie *eastus* . U moet uw eigen unieke naaf naam gebruiken in plaats van **Contoso-Oost-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Het volledig uitvoeren van de opdracht kan even duren.

3. Gebruik de Azure Cloud Shell om een IoT-hub in de regio **westus** te maken met de opdracht [AZ IOT hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Deze IoT-hub wordt ook toegevoegd aan de *groep contoso-US-Resource-Group*.

    In het volgende voor beeld wordt een IoT-hub met de naam *Contoso-West-hub* in de locatie *westus* gemaakt. U moet uw eigen unieke naaf naam gebruiken in plaats van **Contoso-West-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Het volledig uitvoeren van de opdracht kan even duren.



## <a name="create-the-multitenant-enrollment"></a>Multi tenant-inschrijving maken

In deze sectie maakt u een nieuwe registratie groep voor de Tenant apparaten.  

Ter vereenvoudiging wordt in dit artikel bij de registratie gebruikgemaakt van een [verklaring met symmetrische sleutels](concepts-symmetric-key-attestation.md). Voor een veiligere oplossing kunt u gebruikmaken van de [verklaring met het X.509-certificaat](concepts-x509-attestation.md) met een vertrouwensketen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open uw Device Provisioning service-exemplaar.

2. Selecteer het tabblad **inschrijvingen beheren** en klik vervolgens op de knop **registratie groep toevoegen** boven aan de pagina. 

3. Voer in **registratie groep toevoegen** de volgende gegevens in en klik op de knop **Opslaan** .

    **Groeps naam**: Voer **Contoso-VS-devices** in.

    **Attestation-type**: Selecteer **symmetrische sleutel**.

    **Sleutels automatisch genereren**: dit selectie vakje moet al zijn ingeschakeld.

    **Selecteer de manier waarop u apparaten aan hubs wilt toewijzen**: Selecteer de **laagste latentie**.

    ![Multi tenant-registratie groep voor symmetrische sleutel attest toevoegen](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Klik in de **groep inschrijving toevoegen** op **een nieuwe IOT-hub koppelen** om beide regionale hubs te koppelen.

    **Abonnement**: als u meerdere abonnementen hebt, kiest u het abonnement waar u de regionale IOT-hubs hebt gemaakt.

    **IOT hub**: Selecteer een van de regionale hubs die u hebt gemaakt.

    **Toegangs beleid**: Kies **iothubowner**.

    ![De regionale IoT-hubs koppelen aan de inrichtings service](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Als beide regionale IoT-hubs zijn gekoppeld, moet u ze selecteren voor de registratie groep en op **Opslaan** klikken om de regionale IOT hub-groep voor de registratie te maken.

    ![De regionale hub-groep voor de inschrijving maken](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Nadat u de registratie hebt opgeslagen, opent u deze opnieuw en noteert u de **primaire sleutel**. U moet de registratie eerst opslaan voordat de sleutels kunnen worden gegenereerd. Deze sleutel wordt gebruikt voor het later genereren van unieke apparaatinstellingen voor beide gesimuleerde apparaten.


## <a name="create-regional-linux-vms"></a>Regionale Linux-Vm's maken

In deze sectie maakt u twee regionale virtuele Linux-machines (Vm's). Deze Vm's voeren een voor beeld van een apparaat simulatie uit vanuit elke regio om het inrichten van apparaten voor Tenant apparaten uit beide regio's te demonstreren.

Als u het opschonen eenvoudiger wilt maken, worden deze Vm's toegevoegd aan dezelfde resource groep die de IoT-hubs bevat die zijn gemaakt, *Contoso-US-Resource-Group*. De virtuele machines worden echter uitgevoerd in afzonderlijke regio's (VS-**West** en **VS-Oost**).

1. Voer in de Azure Cloud Shell de volgende opdracht uit voor het maken van een VM in de regio **VS-Oost** , nadat u de volgende para meters in de opdracht hebt gewijzigd:

    **--naam**: Voer een unieke naam in voor de VM van het lokale **Amerikaanse** land. 

    **--Administrator-gebruikers** naam: gebruik uw eigen beheerders gebruikersnaam.

    **--Administrator-wacht woord**: gebruik uw eigen beheerders wachtwoord.

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

    Het uitvoeren van deze opdracht duurt enkele minuten. Nadat de opdracht is voltooid, noteert u de waarde voor **publicIpAddress** voor de VM in de regio VS-Oost.

1. Voer in het Azure Cloud Shell de opdracht uit voor het maken van een VM met de regio **VS-West** nadat u de volgende para meters in de opdracht hebt gewijzigd:

    **--naam**: Voer een unieke naam in voor de VM van uw **VS-West** . 

    **--Administrator-gebruikers** naam: gebruik uw eigen beheerders gebruikersnaam.

    **--Administrator-wacht woord**: gebruik uw eigen beheerders wachtwoord.

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

    Het uitvoeren van deze opdracht duurt enkele minuten. Nadat de opdracht is voltooid, noteert u de waarde voor **publicIpAddress** voor de VM van de regio vs-West.

1. Open twee opdracht regel shells. Maak via SSH verbinding met een van de regionale Vm's in elke shell. 

    Geef de gebruikers naam van uw beheerder en het open bare IP-adres dat u voor de virtuele machine hebt genoteerd als para meters voor SSH. Geef het beheerders wachtwoord op wanneer u hierom wordt gevraagd.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>De ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie kloont u de Azure IoT C SDK op elke VM. De SDK bevat een voor beeld waarmee de inrichting van een Tenant apparaat van elke regio wordt gesimuleerd.

1. Installeer **cmake**, **g + +**, **gcc** en [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) voor elke VM met behulp van de volgende opdrachten:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de SDK.

1. Kloon de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c) op beide vm's.  Gebruik de tag die u in de vorige stap hebt gevonden als waarde voor de parameter `-b`:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Voor beide Vm's maakt u een nieuwe **cmake** -map in de opslag plaats en gaat u naar die map.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Voor beide Vm's voert u de volgende opdracht uit, waarmee u een versie van de SDK bouwt die specifiek is voor uw ontwikkelings-client platform. 

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

Wanneer u gebruikmaakt van symmetrische sleutel attest met groeps registraties, gebruikt u de sleutels van de registratie groep niet rechtstreeks. In plaats daarvan maakt u een unieke afgeleide sleutel voor elk apparaat dat wordt vermeld in [groeps registraties met symmetrische sleutels](concepts-symmetric-key-attestation.md#group-enrollments).

Als u de apparaatcode wilt genereren, gebruikt u de groeps hoofd sleutel om een [HMAC-sha256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-id voor het apparaat te berekenen en converteert u het resultaat naar Base64-indeling.

Neem uw groeps hoofd sleutel niet op in de code van uw apparaat.

Gebruik het voor beeld van de bash-shell om een afgeleide apparaatwachtwoord voor elk apparaat te maken met behulp van **openssl**.

- Vervang de waarde voor **Key** door de **primaire sleutel** die u eerder hebt genoteerd voor uw registratie.

- Vervang de waarde voor **REG_ID** door uw eigen unieke registratie-id voor elk apparaat. Gebruik kleine letters, alfanumerieke tekens en streepjes ('-') om beide Id's te definiëren.

Voor beeld van genereren van een apparaatcode voor *Contoso-simdevice-Oost*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Voor beeld van genereren van een apparaatcode voor *Contoso-simdevice-West*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


De Tenant apparaten gebruiken elk een afgeleide-apparaatwachtwoord en de unieke registratie-ID voor het uitvoeren van de symmetrische sleutel attest met de registratie groep tijdens het inrichten van de IoT-hubs van de Tenant.




## <a name="simulate-the-devices-from-each-region"></a>De apparaten van elke regio simuleren


In deze sectie gaat u een voor beeld van een inrichting bijwerken in de Azure IoT C-SDK voor beide regionale Vm's. 

Met de voorbeeld code wordt een opstart volgorde voor apparaten gesimuleerd die de inrichtings aanvraag naar uw Device Provisioning service-exemplaar verzendt. De opstart procedure zorgt ervoor dat het apparaat wordt herkend en toegewezen aan de IoT-hub die het dichtst op basis van de latentie ligt.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Open **~/Azure-IOT-SDK-c/Provisioning \_ client/samples/Prov \_ dev \_ client sample \_ /Prov \_ dev \_ client \_ sample. c** voor bewerking op beide vm's.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat de `hsm_type` variabele is ingesteld op `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` zoals hieronder wordt weer gegeven, zodat deze overeenkomt met de Attestation-methode van de registratie groep. 

    Sla de wijzigingen in de bestanden op beide Vm's op.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Zoek op beide Vm's de aanroep naar `prov_dev_set_symmetric_key_info()` in **Prov \_ dev \_ client \_ sample. c** . Dit is een opmerking.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Verwijder de opmerking over de functie aanroepen en vervang de tijdelijke aanduidingen (inclusief de punt haken) door de unieke registratie-Id's en de afgeleide apparaatinstellingen voor elk apparaat. De sleutels die hieronder worden weer gegeven, zijn alleen bedoeld als voor beeld. Gebruik de sleutels die u eerder hebt gegenereerd.

    VS-Oost:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    VS-West:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Sla de bestanden op.

1. Navigeer op beide Vm's naar de voor beeld-map die hieronder wordt weer gegeven en bouw het voor beeld.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Zodra de build is gelukt, voert u **Prov \_ dev \_ client \_sample.exe** op beide vm's uit om een Tenant apparaat uit elke regio te simuleren. U ziet dat elk apparaat wordt toegewezen aan de Tenant IoT-hub die het dichtst bij de regio's van het gesimuleerde apparaat ligt.

    De simulatie uitvoeren:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Voor beeld van uitvoer van de VS-Oost-VM:

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

    Voorbeeld uitvoer van de VS-West-VM:
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

Als u van plan bent om verder te gaan met de resources die in dit artikel zijn gemaakt, kunt u ze blijven gebruiken. Als u niet van plan bent om door te gaan met het gebruik van de resource, gebruikt u de volgende stappen om alle resources te verwijderen die zijn gemaakt in dit artikel om onnodige kosten te voor komen.

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

* Zie voor meer informatie over het opnieuw inrichten

> [!div class="nextstepaction"]
> [Concepten IoT Hub apparaten opnieuw inrichten](concepts-device-reprovision.md)

* Zie voor meer informatie over het ongedaan maken van de inrichting.
> [!div class="nextstepaction"]
> [De inrichting van apparaten die eerder automatisch zijn ingericht, ongedaan maken](how-to-unprovision-devices.md)