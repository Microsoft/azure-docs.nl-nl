---
title: Apparaatupdate inrichten voor Azure IoT Hub agent| Microsoft Docs
description: Apparaatupdate inrichten voor Azure IoT Hub agent
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 812de4850c6c3577346915a0072ea11c60f7ba73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365447"
---
# <a name="device-update-agent-provisioning"></a>Agent voor apparaatupdates inrichten

De agent voor de apparaatupdatemodule kan worden uitgevoerd naast andere systeemprocessen en [IoT Edge modules](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) die verbinding maken met uw IoT Hub als onderdeel van hetzelfde logische apparaat. In deze sectie wordt beschreven hoe u de agent voor apparaatupdates inrichten als module-id. 


## <a name="module-identity-vs-device-identity"></a>Module-id versus apparaat-id

In IoT Hub kunt u onder elke apparaat-id maximaal 50 module-id's maken. Elke module-identiteit genereert impliciet een module twin. Aan de apparaatzijde kunt u met IoT Hub apparaat-SDK's modules maken waarbij elk apparaat een onafhankelijke verbinding met de IoT Hub. Module-id's en module-tweelingen bieden dezelfde mogelijkheden als apparaat-id's en apparaat dubbels, maar met een fijner granulariteit. [Meer informatie over module-identiteiten in IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Ondersteuning voor apparaatupdate

De volgende IoT-apparaattypen worden momenteel ondersteund met Apparaatupdate:

* Linux-apparaten (IoT Edge en niet-IoT Edge apparaten):
    * A/B-update voor afbeelding:
        - Yocto - ARM64 (referentieafbeelding), kan worden open source [om](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) uw eigen afbeeldingen te bouwen voor andere architectuur, naar behoefte.
        - Ubuntu 18.04-simulator
       
    * Package Agent ondersteunde builds voor de volgende platformen/architecturen:
        - Ubuntu Server 18.04 x64 Package Agent 
        - Debian 9 
        
* Beperkte apparaten:
    * Voorbeelden van AzureRTOS Device [Update-agent: Apparaatupdate voor Azure IoT Hub zelfstudie voor Azure-real-time-besturingssysteem](device-update-azure-real-time-operating-system.md)

* Niet-verbonden apparaten: 
    * [Ondersteuning voor niet-verbonden apparaatupdates begrijpen](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Vereisten  

Als u het IoT-apparaat/IoT-IoT Edge-apparaat instelt voor updates op basis van [pakketten,](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)voegt u packages.microsoft.com toe aan de opslagplaatsen van uw computer door de volgende stappen uit te voeren:

1. Meld u aan bij de computer of het IoT-apparaat waarop u de Device Update-agent wilt installeren.

1. Open een Terminal-venster.

1. Installeer de configuratie van de opslagplaats die overeenkomt met het besturingssysteem van uw apparaat.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Kopieer de gegenereerde lijst naar de map sources.list.d.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Installeer de openbare Microsoft GPG-sleutel.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>De agent voor apparaatupdates inrichten als module-id

In deze sectie wordt beschreven hoe u de agent voor apparaatupdates inrichten als module-id op IoT Edge-apparaten, niet-Edge IoT-apparaten en andere IoT-apparaten.


### <a name="on-iot-edge-enabled-devices"></a>Op IoT Edge ingeschakelde apparaten

Volg deze instructies voor het inrichten van de agent voor apparaatupdates [IoT Edge ingeschakelde apparaten.](https://docs.microsoft.com/azure/iot-edge)

1. Volg de instructies voor het [installeren en inrichten van Azure IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true).

1. De update-agent voor de installatie van de apparaatupdate installeren
    - We bieden voorbeeldafbeeldingen in [Artefacten](https://github.com/Azure/iot-hub-device-update/releases) om update-implementaties van afbeeldingen te proberen naar verschillende versies met behulp van een basisafbeelding (adu-base-image) en één update-afbeelding (adu-update-image). Zie een voorbeeld van [het flashen van de afbeelding naar uw IoT Hub apparaat.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)  

1. De update-agent voor het apparaatupdatepakket installeren  
    - Voor de nieuwste agentversies van packages.miscrosoft.com: pakketlijsten op uw apparaat bijwerken en het pakket van de Device Update-agent en de afhankelijkheden ervan installeren met behulp van:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - Voor toekomstige releasekandidaatversies van [Artefacten:](https://github.com/Azure/iot-hub-device-update/releases) download het .dep-bestand naar de computer op wie u de Device Update-agent wilt installeren en ga als volgende te werk:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. U bent nu klaar om de agent voor apparaatupdates te starten op IoT Edge apparaat. 

### <a name="on-non-edge-iot-linux-devices"></a>Op niet-Edge IoT Linux-apparaten

Volg deze instructies voor het inrichten van de agent voor apparaatupdates op uw IoT Linux-apparaten.

1. Installeer de IoT Identity Service en voeg de nieuwste versie toe aan uw IoT-apparaat. 
    1. Meld u aan bij de computer of het IoT-apparaat.
    1. Een terminalvenster openen.
    1.  Installeer de nieuwste [IoT Identity Service op](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) uw IoT-apparaat met behulp van deze opdracht:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. IoT Identity-service inrichten om de IoT-apparaatgegevens op te halen.
    * Maak een aangepaste kopie van de configuratiesjabloon zodat we de inrichtingsgegevens kunnen toevoegen. Voer in een terminal de onderstaande opdracht in.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Bewerk vervolgens het configuratiebestand om de connection string van het apparaat dat u wilt gebruiken als de inrichting voor dit apparaat of apparaat op te nemen. Voer in een terminal de onderstaande opdracht in.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Als het goed is, ziet u een bericht zoals in het volgende voorbeeld:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagram van het configuratiebestand van de IoT Identity Service." lightbox="media/understand-device-update/config.png":::

    1. Zoek in hetzelfde nanovenster het blok met 'Handmatige inrichting met connection string'.
    1. Verwijder in het venster het symbool '#' voor 'inrichten'
    1. Verwijder in het venster het symbool '#' voor 'bron' 
    1. Verwijder in het venster het symbool '#' voor 'connection_string'
    1. Verwijder in het venster de tekenreeks tussen de aanhalingstekens rechts van 'connection_string' en voeg uw connection string toe 
    1. Sla uw wijzigingen in het bestand op met 'Ctrl+X' en vervolgens 'Y' en druk op de enter-toets om uw wijzigingen op te slaan. 
    
1.  Pas nu de IoT Identity-service toe en start deze opnieuw met de onderstaande opdracht. Als het goed is, ziet u nu 'Klaar!' afdrukken, wat betekent dat u de IoT Identity Service hebt geconfigureerd.

    > [!Note]
    > De IoT Identity-service registreert module-identiteiten met IoT Hub met behulp van symmetrische sleutels.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Installeer ten slotte de Agent voor apparaatupdates. We bieden voorbeeldafbeeldingen in [Artefacten](https://github.com/Azure/iot-hub-device-update/releases) om update-implementaties van afbeeldingen te proberen naar verschillende versies met behulp van een basisafbeelding (adu-base-image) en één update-afbeelding (adu-update-image). Zie een voorbeeld van [het flashen van de afbeelding naar uw IoT Hub apparaat.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  U bent nu klaar om de agent voor apparaatupdates op uw IoT-apparaat te starten. 

### <a name="other-iot-devices"></a>Andere IoT-apparaten

De Agent voor apparaatupdates kan ook worden geconfigureerd zonder de IoT Identity-service voor testen of op beperkte apparaten. Volg de onderstaande stappen om de Agent voor apparaatupdates in te connection string (van de module of het apparaat).

1.  We bieden voorbeeldafbeeldingen in [Artefacten](https://github.com/Azure/iot-hub-device-update/releases) om update-implementaties van afbeeldingen te proberen naar verschillende versies met behulp van een basisafbeelding (adu-base-image) en één update-afbeelding (adu-update-image). Zie een voorbeeld van [hoe u de afbeelding naar uw apparaat IoT Hub flasht.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  Meld u aan bij de computer IoT Edge apparaat/IoT-apparaat.
    
1.  Een terminalvenster openen.

1.  Voeg de connection string toe aan het [configuratiebestand voor apparaatupdates:](device-update-configuration-file.md)
    1. Voer het onderstaande in het terminalvenster in:
        - [Gebruik voor](device-update-ubuntu-agent.md) pakketupdates: sudo nano /etc/adu/adu-conf.txt
        - [Updates voor afbeeldingen](device-update-raspberry-pi.md) gebruiken: sudo nano /adu/adu-conf.txt
       
    1. Als het goed is, wordt er een venster geopend met tekst. Verwijder de volledige tekenreeks na 'connection_String=' de eerste keer dat u de Agent voor apparaatupdates inrichten op het IoT-apparaat. Het is alleen tekst van de plaatshouder.
    
    1. Vervang in de terminal '<your-connection-string>' door de connection string van het apparaat voor uw exemplaar van de Device Update-agent.
    
        > [!Important]
        > Voeg geen aanhalingstekens toe rond connection string.
        ```shell
        connection_string=<ADD CONNECTION STRING HERE>
        ```
       
    1. Voer in en sla het op.
    
1.  U bent nu klaar om de agent voor apparaatupdates op uw IoT-apparaat te starten. 


## <a name="how-to-start-the-device-update-agent"></a>De agent voor apparaatupdates starten

In deze sectie wordt beschreven hoe u de agent voor apparaatupdates start en verifieert als een module-id die wordt uitgevoerd op uw IoT-apparaat.

1.  Meld u aan bij de computer of het apparaat waar de Apparaatupdate-agent is geïnstalleerd.

1.  Open een Terminal-venster en voer de onderstaande opdracht in.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  U kunt de status van de agent controleren met behulp van de onderstaande opdracht. Als u problemen ziet, raadpleegt u deze gids [voor probleemoplossing.](troubleshoot-device-update.md)
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Als het goed is, ziet u de status OK.

1.  Ga in IoT Hub portal naar IoT-apparaat of IoT Edge om het apparaat te vinden dat u hebt geconfigureerd met de Device Update-agent. Daar ziet u dat de Agent voor apparaatupdates wordt uitgevoerd als een module. Bijvoorbeeld:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagram van de modulenaam van de apparaatupdate." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>De agent voor apparaatupdates bouwen en uitvoeren

U kunt ook uw eigen agent voor apparaatupdates van klanten bouwen en wijzigen.

Volg de instructies voor het [bouwen van](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) de Agent voor apparaatupdates vanuit de bron.

Zodra de agent is gebouwd, is het tijd om de agent [uit](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) te voeren.

Maak nu de wijzigingen die nodig zijn om de agent in uw afbeelding op te nemen.  Bekijk hoe u de [agent voor apparaatupdates](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) kunt wijzigen voor hulp.


## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Als u problemen hebt, bekijkt u de [](troubleshoot-device-update.md) Gids voor het oplossen van problemen IoT Hub Apparaatupdates om mogelijke problemen op te lossen en de benodigde informatie te verzamelen om Aan Microsoft te verstrekken.


## <a name="next-steps"></a>Volgende stappen

U kunt de volgende vooraf gebouwde afbeeldingen en binaire bestanden gebruiken voor een eenvoudige demonstratie van apparaatupdates voor IoT Hub:

- Update van de afbeelding: Aan de slag met [Raspberry Pi 3 B+ Reference Yocto Image extensible](device-update-raspberry-pi.md) via open source om uw eigen afbeeldingen te bouwen voor een andere architectuur als dat nodig is.

- [Aan de slag Ubuntu (18.04 x64) Simulator Reference Agent gebruiken](device-update-simulator.md)

- [Package Update:Aan de slag using Ubuntu Server 18.04 x64 Package agent](device-update-ubuntu-agent.md)

- [Apparaatupdate voor Azure IoT Hub zelfstudie voor Azure-real-time-besturingssysteem](device-update-azure-real-time-operating-system.md)

