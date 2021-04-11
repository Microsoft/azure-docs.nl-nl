---
title: Update van het apparaat voor Azure IoT Hub agent inrichten | Microsoft Docs
description: Update van het apparaat voor Azure IoT Hub agent inrichten
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e778c7ee14d2115bf6d8cf7f12ceaa61e364a4a2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120169"
---
# <a name="device-update-agent-provisioning"></a>Apparaat-Update Agent inrichten

De module agent voor het bijwerken van apparaten kan naast andere systeem processen en [IOT Edge modules](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) worden uitgevoerd die als onderdeel van hetzelfde logische apparaat verbinding maken met uw IOT hub. In deze sectie wordt beschreven hoe u de Update Agent van het apparaat inricht als een module-identiteit. 


## <a name="module-identity-vs-device-identity"></a>Module-identiteit tegenover apparaat-id

In IoT Hub kunt u onder elke apparaat-id Maxi maal 50 module-identiteiten maken. Elke module-id genereert impliciet een module dubbele. Aan de kant van het apparaat kunt u met de Sdk's van het IoT Hub apparaat modules maken waarbij elke ene een onafhankelijke verbinding met IoT Hub opent. Module-identiteit en module dubbele bieden de vergelijk bare mogelijkheden als de apparaat-id en het apparaat, met een nauw keurigere granulariteit. [Meer informatie over module-identiteiten in IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Ondersteuning voor het bijwerken van apparaten

De volgende IoT-apparaattypen worden momenteel ondersteund met updates voor apparaten:

* Linux-apparaten (IoT Edge en niet-IoT Edge apparaten):
    * Update A/B installatie kopie:
        - Yocto-ARM64 (referentie afbeelding), uitbreidbaar via open source om [uw eigen installatie kopieën](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) voor andere architectuur te bouwen.
        - Ubuntu 18,04 Simulator
       
    * Pakket agent-ondersteunde builds voor de volgende platforms/architecturen:
        - Ubuntu Server 18,04 x64-pakket agent 
        - Debian 9 
        
* Beperkte apparaten:
    * Voor beelden van AzureRTOS-apparaat bijwerken: [Update voor azure IOT hub-zelf studie voor Azure-real-time-besturings systeem](device-update-azure-real-time-operating-system.md)

* Niet-verbonden apparaten: 
    * [Ondersteuning voor niet-verbonden apparaten bijwerken](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Vereisten  

Als u het IoT-apparaat/IoT Edge-apparaat instelt voor [op pakketten gebaseerde updates](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts), voegt u packages.Microsoft.com toe aan de opslag plaatsen van uw machine door de volgende stappen uit te voeren:

1. Meld u aan bij de machine of IoT-apparaat waarop u de Update Agent voor het apparaat wilt installeren.

1. Open een Terminal venster.

1. Installeer de configuratie van de opslag plaats die overeenkomt met het besturings systeem van uw apparaat.
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

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>De Update Agent van het apparaat inrichten als een module-id

In deze sectie wordt beschreven hoe u de Update Agent van het apparaat inricht als een module-id op IoT Edge ingeschakelde apparaten, IoT-apparaten die geen deel uitmaken en andere IoT-apparaten.


### <a name="on-iot-edge-enabled-devices"></a>Op IoT Edge ingeschakelde apparaten

Volg deze instructies om de apparaat-Update agent in te richten op [IOT Edge ingeschakelde apparaten](https://docs.microsoft.com/azure/iot-edge).

1. Volg de instructies om [de Azure IOT Edge runtime te installeren en](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true)in te richten.

1. Installeer vervolgens de Update Agent van het apparaat vanuit [artefacten](https://github.com/Azure/iot-hub-device-update/releases) en u bent nu klaar om de apparaat-Update Agent op uw IOT edge-apparaat te starten.


### <a name="on-non-edge-iot-linux-devices"></a>Van niet-Edge IoT Linux-apparaten

Volg deze instructies om de apparaat-Update Agent op uw IoT Linux-apparaten in te richten.

1. Installeer de IoT Identity-service en voeg de nieuwste versie toe aan uw IoT-apparaat. 
    1. Meld u aan bij de machine of IoT-apparaat.
    1. Een terminalvenster openen.
    1.  Installeer de meest recente [IOT-identiteits service](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) op uw IOT-apparaat met behulp van deze opdracht:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. IoT Identity service inrichten voor het verkrijgen van IoT-apparaatgegevens.
    * Maak een aangepaste kopie van de configuratie sjabloon zodat we de inrichtings gegevens kunnen toevoegen. Voer de onderstaande opdracht in een terminal in.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Bewerk vervolgens het configuratie bestand om de connection string van het apparaat op te nemen dat u wilt gebruiken als de inrichtings controle voor dit apparaat of deze computer. Voer de onderstaande opdracht in een terminal in.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Er wordt een bericht weer gegeven zoals in het volgende voor beeld:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagram van het configuratie bestand van de IoT-identiteits service." lightbox="media/understand-device-update/config.png":::

    1. Zoek in hetzelfde nano venster het blok met hand matig inrichten met connection string.
    1. Verwijder in het venster het symbool ' # ' vóór ' inrichting '
    1. Verwijder in het venster het symbool ' # ' vóór ' Bron ' 
    1. Verwijder in het venster het symbool ' # ' vóór ' connection_string '
    1. Verwijder in het venster de teken reeks binnen de aanhalings tekens rechts van ' connection_string ' en voeg vervolgens uw connection string toe 
    1. Sla de wijzigingen in het bestand op met CTRL + X en vervolgens op Y en druk op de Enter-toets om uw wijzigingen op te slaan. 
    
1.  Pas nu de IoT-identiteits service toe en start deze opnieuw met behulp van de onderstaande opdracht. U ziet nu een ' gereed! ' afdrukken dat betekent dat de IoT-identiteits service is geconfigureerd.

    > [!Note]
    > De IoT Identity-service registreert module-identiteiten met IoT Hub door op dit moment symmetrische sleutels te gebruiken.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Installeer de Update Agent van het apparaat ten slotte vanuit [artefacten](https://github.com/Azure/iot-hub-device-update/releases) en u bent nu klaar om de apparaat-Update Agent op uw IOT edge-apparaat te starten.


### <a name="other-iot-devices"></a>Andere IoT-apparaten

De Update Agent van het apparaat kan ook worden geconfigureerd zonder de IoT-identiteits service voor het testen of op beperkte apparaten. Volg de onderstaande stappen om de Update Agent van het apparaat in te richten met behulp van een connection string (vanuit de module of het apparaat).

1.  Update-Agent voor apparaat installeren vanuit [artefacten](https://github.com/Azure/iot-hub-device-update/releases).

1.  Meld u aan bij de machine of IoT Edge apparaat/IoT-apparaat.
    
1.  Een terminalvenster openen.

1.  Voeg de connection string toe aan het [configuratie bestand](device-update-configuration-file.md)voor het bijwerken van het apparaat:
    1. Voer in het Terminal venster het volgende in:
        - [Pakket updates](device-update-ubuntu-agent.md) gebruiken: sudo nano/etc/Adu/adu-conf.txt
        - [Installatie kopie-updates](device-update-raspberry-pi.md) gebruiken: sudo nano/Adu/adu-conf.txt
       
    1. Er wordt een venster geopend met een tekst. Verwijder de volledige teken reeks na ' connection_String = ' de eerste keer dat u de Update Agent voor het apparaat op het IoT-apparaat inricht. Het is alleen plaats tekst.
    
    1. Vervang in de Terminal <uw-verbindings reeks> met de connection string van het apparaat voor uw instantie van apparaat Update Agent.
    
        > [!Important]
        > Voeg geen aanhalings tekens toe rondom de connection string.
        
        - connection_string =<uw-Connection String>
       
    1. Voer in en sla het op.
    
1.  Nu bent u klaar om de apparaat-Update Agent op uw IoT Edge-apparaat te starten. 


## <a name="how-to-start-the-device-update-agent"></a>De Update-Agent voor het apparaat starten

In deze sectie wordt beschreven hoe u de Update Agent van het apparaat start en controleert als een module-identiteit die op uw IoT-apparaat wordt uitgevoerd.

1.  Meld u aan bij de computer of het apparaat waarop de Update Agent voor het apparaat is geïnstalleerd.

1.  Open een Terminal venster en voer de onderstaande opdracht in.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  U kunt de status van de agent controleren met behulp van de onderstaande opdracht. Raadpleeg deze [hand leiding voor probleem oplossing](troubleshoot-device-update.md)als u problemen ziet.
    ```shell
    sudo systemctl status adu-agent
    ```
    
    U ziet de status OK.

1.  Ga op de IoT Hub-Portal naar IoT-apparaat of IoT Edge apparaten om het apparaat te vinden dat u hebt geconfigureerd met Device Update Agent. Hier ziet u de Update Agent van het apparaat die wordt uitgevoerd als een module. Bijvoorbeeld:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagram van de naam van de module voor het bijwerken van apparaten." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Update Agent voor apparaten bouwen en uitvoeren

U kunt ook uw eigen Update Agent voor klant apparaten maken en wijzigen.

Volg de instructies om de Update Agent van het apparaat te [bouwen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) vanuit de bron.

Zodra de agent is gebouwd, wordt de agent [uitgevoerd](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) .

Breng nu de wijzigingen aan die nodig zijn om de agent in uw installatie kopie op te nemen.  Lees hoe u de Update Agent voor het apparaat kunt [wijzigen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) voor hulp.


## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Als u problemen ondervindt, raadpleegt u de update voor het oplossen van problemen met IoT Hub voor [probleem oplossing](troubleshoot-device-update.md) om mogelijke problemen op te lossen en benodigde informatie te verzamelen die aan micro soft kan worden verstrekt.


## <a name="next-steps"></a>Volgende stappen

U kunt de volgende vooraf gemaakte installatie kopieën en binaire bestanden gebruiken voor een eenvoudige demonstratie van het bijwerken van het apparaat voor IoT Hub:

- [Update van de installatie kopie: aan de slag met Raspberry Pi 3 B + Reference yocto-installatie kopie](device-update-raspberry-pi.md) uitbreidbaar via open source om uw eigen installatie kopieën te maken voor andere architectuur waar nodig.

- [Aan de slag met Ubuntu (18,04 x64) Simulator referentie agent](device-update-simulator.md)

- [Pakket update: aan de slag met de Ubuntu Server 18,04 x64-pakket agent](device-update-ubuntu-agent.md)

- [Zelf studie over het bijwerken van apparaten voor Azure IoT Hub voor Azure-real-time-besturings systeem](device-update-azure-real-time-operating-system.md)

