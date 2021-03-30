---
title: Problemen oplossen-Azure Event Grid IoT Edge | Microsoft Docs
description: Problemen oplossen in Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171343"
---
# <a name="common-issues"></a>Veelvoorkomende problemen

Als u problemen ondervindt met het gebruik van Azure Event Grid op IoT Edge in uw omgeving, gebruikt u dit artikel als richt lijn voor het oplossen van problemen en oplossingen.

## <a name="view-event-grid-module-logs"></a>Event Grid-module Logboeken weer geven

Als u problemen wilt oplossen, moet u mogelijk toegang krijgen tot Event Grid-module Logboeken. Voer hiervoor de volgende opdracht uit op de VM waarop de module is geïmplementeerd:

In Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Op Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Kan geen HTTPS-aanvragen doen

* Zorg er eerst voor dat Event Grid module **Inkomend: serverAuth: tlsPolicy** is ingesteld op **strikt** of **ingeschakeld**.

* Als de communicatie van module naar module is, zorgt u ervoor dat u de aanroep op poort **4438** en de naam van de module overeenkomt met wat er wordt geïmplementeerd. 

  Als Event Grid module bijvoorbeeld is geïmplementeerd met de naam **eventgridmodule** , moet uw URL zijn **https://eventgridmodule:4438** . Controleer of het hoofdletter gebruik en het poort nummer juist zijn.
    
* Als het vanuit de niet-IoT-module is, moet u ervoor zorgen dat Event Grid poort wordt toegewezen aan de host-machine tijdens de implementatie, bijvoorbeeld

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Kan geen HTTP-aanvragen maken

* Zorg er eerst voor dat Event Grid module **Inkomend: serverAuth: tlsPolicy** is ingesteld op **ingeschakeld** of **uitgeschakeld**.

* Als de communicatie van module naar module is, zorgt u ervoor dat u de aanroep op poort **5888** en de naam van de module overeenkomt met wat er wordt geïmplementeerd. 

  Als Event Grid module bijvoorbeeld is geïmplementeerd met de naam **eventgridmodule** , moet uw URL zijn **http://eventgridmodule:5888** . Controleer of het hoofdletter gebruik en het poort nummer juist zijn.
    
* Als het vanuit de niet-IoT-module is, moet u ervoor zorgen dat Event Grid poort wordt toegewezen aan de host-machine tijdens de implementatie, bijvoorbeeld

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>De certificaat keten is uitgegeven door een niet-vertrouwde instantie

Event Grid module is standaard geconfigureerd voor het verifiëren van clients met een certificaat dat is uitgegeven door de IoT Edge Security daemon. Zorg ervoor dat de client een certificaat presenteert dat is geroot naar deze keten.

De klasse **IoTSecurity** in [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) laat zien hoe u certificaten kunt ophalen uit IOT Edge-beveiligings-daemon en hoe u uitgaande oproepen kunt configureren.

Als het een niet-productie-omgeving is, hebt u de mogelijkheid om client verificatie uit te scha kelen. Raadpleeg [beveiliging en verificatie](security-authentication.md) voor meer informatie over hoe u dit doet.

## <a name="debug-events-not-received-by-subscriber"></a>Fout opsporing gebeurtenissen die niet zijn ontvangen door de abonnee

Veelvoorkomende oorzaken hiervoor zijn:

* De gebeurtenis is nooit gepubliceerd. Er moet een HTTP-status code van 200 (OK) zijn ontvangen bij het posten van een gebeurtenis in de module Event Grid.

* Controleer het gebeurtenis abonnement om te verifiëren:
    * Eind punt-URL is geldig
    * Filters in het abonnement zorgen er niet toe dat de gebeurtenis wordt verwijderd.

* Controleren of de abonnee module wordt uitgevoerd

* Meld u aan bij de VM waar Event Grid module is geïmplementeerd en Bekijk de logboeken ervan.

* Schakel logboek registratie per levering in door **Broker: logDeliverySuccess = True** in te stellen en de module Event grid opnieuw te implementeren en de aanvraag opnieuw uit te voeren. Het inschakelen van logboek registratie per levering kan invloed hebben op de door Voer en latentie wanneer de fout opsporing is voltooid. Als u deze optie uitschakelt, wordt de aanbeveling ingesteld op **Broker: logDeliverySuccess = False**  en wordt Event grid module opnieuw geïmplementeerd.

* Schakel metrische gegevens in door metrische gegevens in te stellen **: reportertype = console** en implementeer Event grid module opnieuw. Als er bewerkingen worden uitgevoerd, worden er metrische gegevens geregistreerd op de console van Event Grid module, die kan worden gebruikt om fouten op te sporen. We raden u aan om metrische gegevens alleen in te scha kelen voor fout opsporing en eenmaal op te zetten om deze functie uit te voeren door metrische gegevens in te stellen **: reportertype = none** en de module Event grid opnieuw te implementeren.

## <a name="next-steps"></a>Volgende stappen

Meld alle problemen en suggesties voor het gebruik van Event Grid op IoT Edge op [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .