---
title: API-proxy module configureren-Azure IoT Edge | Microsoft Docs
description: Meer informatie over het aanpassen van de API-proxy module voor IoT Edge gateway-hiërarchieën.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1070a4c8daecfedae513f2fd8738c27abfb33078
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200586"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>De API-proxy module configureren voor uw gateway hiërarchie scenario (preview-versie)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Met de API-proxy module kunnen IoT Edge-apparaten HTTP-aanvragen via gateways verzenden in plaats van directe verbindingen met Cloud Services te maken. In dit artikel worden de configuratie opties beschreven, zodat u de module kunt aanpassen ter ondersteuning van de vereisten voor de gateway-hiërarchie.

>[!NOTE]
>Voor deze functie is IoT Edge versie 1.2, in openbare preview, met Linux-containers vereist.

In sommige netwerk architecturen hebben IoT Edge apparaten achter gateways geen rechtstreekse toegang tot de Cloud. Modules die proberen verbinding te maken met Cloud Services, zullen mislukken. De API-proxy module biedt ondersteuning voor downstream IoT Edge-apparaten in deze configuratie door het opnieuw routeren van module verbindingen om de lagen van een gateway hiërarchie door te lopen. Het biedt clients een veilige manier om via HTTPS te communiceren met meerdere services zonder tunneling, maar door de verbindingen op elke laag te beëindigen. De API-proxy module fungeert als een proxy module tussen de IoT Edge-apparaten in een gateway-hiërarchie totdat deze het IoT Edge apparaat in de bovenste laag bereikt. Op dat moment worden door de services die worden uitgevoerd op de bovenste laag IoT Edge apparaat deze aanvragen afhandelt, en in de API-proxy module alle HTTP-verkeer van lokale services naar de Cloud via één poort.

De API-proxy module kan veel scenario's bieden voor gateway hiërarchieën, zoals het toestaan van lagere laag apparaten om container installatie kopieën te halen of om blobs te pushen naar opslag. De configuratie van de proxy regels bepaalt hoe gegevens worden gerouteerd. In dit artikel worden verschillende algemene configuratie opties besproken.

## <a name="deploy-the-proxy-module"></a>De proxy module implementeren

De API-proxy module is beschikbaar via de micro soft-Container Registry (MCR): `mcr.microsoft.com/azureiotedge-api-proxy:latest` .

U kunt de API-proxy module ook rechtstreeks vanuit Azure Marketplace implementeren: [IOT Edge API-proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Meer informatie over de proxy module

De API-proxy module maakt gebruik van een reverse proxy voor nginx om gegevens via netwerk lagen te routeren. Een proxy is inge sloten in de module. Dit betekent dat de module installatie kopie de proxy configuratie moet ondersteunen. Als de proxy bijvoorbeeld op een bepaalde poort luistert, moet deze poort door de module worden geopend.

De proxy begint met een standaard configuratie bestand dat is inge sloten in de module. U kunt een nieuwe configuratie door geven van de module vanuit de Cloud met behulp van de [module dubbele](../iot-hub/iot-hub-devguide-module-twins.md). Daarnaast kunt u omgevings variabelen gebruiken om configuratie-instellingen in of uit te scha kelen tijdens de implementatie.

Dit artikel richt zich eerst op het standaard configuratie bestand en hoe u omgevings variabelen kunt gebruiken om de instellingen in te scha kelen. Vervolgens bespreken we het aanpassen van het configuratie bestand aan het einde.

### <a name="default-configuration"></a>Standaardconfiguratie

De API-proxy module wordt geleverd met een standaard configuratie die algemene scenario's ondersteunt en die aanpassingen mogelijk maakt. U kunt de standaard configuratie beheren via omgevings variabelen van de module.

Momenteel zijn de standaard omgevings variabelen:

| Omgevingsvariabele | Description |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Een lijst met alle variabelen die u wilt bijwerken in een lijst met door komma's gescheiden waarden. Met deze stap wordt voor komen dat de verkeerde configuratie-instellingen per ongeluk worden gewijzigd.
| `NGINX_DEFAULT_PORT` | Hiermee wijzigt u de poort waarnaar de nginx-proxy luistert. Als u deze omgevings variabele bijwerkt, moet u ervoor zorgen dat de poort die u selecteert ook wordt weer gegeven in de module dockerfile en is gedeclareerd als een poort binding in het implementatie manifest.<br><br>De standaard waarde is 443.<br><br>Wanneer de implementatie wordt geïmplementeerd vanuit Azure Marketplace, wordt de standaard poort bijgewerkt naar 8000, om conflicten met de edgeHub-module te voor komen. Zie [open poorten minimaliseren](#minimize-open-ports)voor meer informatie. |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Adres voor het routeren van docker-aanvragen. Wijzig deze variabele op het bovenste laag apparaat om naar de register module te verwijzen.<br><br>De standaard waarde is de bovenliggende hostnaam. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Adres om BLOB-register aanvragen te routeren. Wijzig deze variabele op het bovenste laag apparaat om naar de Blob Storage-module te verwijzen.<br><br>De standaard waarde is de bovenliggende hostnaam. |

## <a name="minimize-open-ports"></a>Open poorten minimaliseren

Om het aantal open poorten te minimaliseren, moet de API-proxy module alle HTTPS-verkeer door sturen (poort 443), inclusief het verkeer dat is gericht op de edgeHub-module. De API-proxy module is standaard geconfigureerd voor het opnieuw routeren van alle edgeHub-verkeer op poort 443.

Gebruik de volgende stappen om uw implementatie te configureren om open poorten te minimaliseren:

1. Werk de instellingen van de edgeHub-module bij om niet te binden op poort 443, anders zijn er conflicten met poort bindingen. De edgeHub-module is standaard gebonden aan de poorten 443, 5671 en 8883. Verwijder de poort 443-binding en sluit de andere twee in plaats:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. De API-proxy module configureren voor binding op poort 443.

   1. Stel de waarde van de variabele **NGINX_DEFAULT_PORT** omgeving in op `443` .
   1. Werk de container Create-opties bij om te binden op poort 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Als u open poorten niet hoeft te minimaliseren, kunt u de edgeHub-module poort 443 gebruiken en de API-proxy module configureren om op een andere poort te Luis teren. De API-proxy module kan bijvoorbeeld op poort 8000 Luis teren door de waarde van de variabele **NGINX_DEFAULT_PORT** omgeving in te stellen `8000` en een poort binding voor poort 8000 te maken.

## <a name="enable-container-image-download"></a>Downloaden van container installatie kopie inschakelen

Een veelgebruikte use-case voor de API-proxy module is om IoT Edge-apparaten in lagere lagen in te scha kelen om container installatie kopieën te halen. In dit scenario wordt de [docker-REGI ster-module](https://hub.docker.com/_/registry) gebruikt om container installatie kopieën op te halen uit de Cloud en deze op te slaan in de bovenste laag. De API-proxy stuurt alle HTTPS-aanvragen om een container installatie kopie te downloaden uit de lagere lagen die moeten worden geleverd door de register module in de bovenste laag.

Dit scenario vereist dat downstream IoT Edge-apparaten verwijzen naar de domein naam `$upstream` gevolgd door het poort nummer van de API-proxy module in plaats van het container register van een installatie kopie. Bijvoorbeeld: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Deze use-case wordt geïllustreerd in de zelf studie [een hiërarchie van IOT edge apparaten maken met behulp van gateways](tutorial-nested-iot-edge.md).

Configureer de volgende modules in de **bovenste laag**:

* Een docker-register module
  * Configureer de module met een naam die kan worden onthouden, zoals het *REGI ster* en maak een poort in de module beschikbaar voor het ontvangen van aanvragen.
  * Configureer de module zodanig dat deze wordt toegewezen aan uw container register.
* Een API-proxy module
  * Configureer de volgende omgevings variabelen:

    | Name | Waarde |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | De naam van de register module en open poort. Bijvoorbeeld `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | De poort waarop de nginx-proxy luistert naar aanvragen van downstream-apparaten. Bijvoorbeeld `8000`. |

  * Configureer de volgende createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configureer de volgende module op een **lagere laag** voor dit scenario:

* Een API-proxy module
  * Configureer de volgende omgevings variabelen:

    | Name | Waarde |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | De poort waarop de nginx-proxy luistert naar aanvragen van downstream-apparaten. Bijvoorbeeld `8000`. |

  * Configureer de volgende createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>BLOB-upload inschakelen

Een ander gebruiks voorbeeld voor de API-proxy module is om IoT Edge-apparaten in lagere lagen in te scha kelen voor het uploaden van blobs. Met deze use-case kunt u probleemoplossings functies inschakelen op apparaten met een lagere laag, zoals het uploaden van module Logboeken of het uploaden van de ondersteunings

In dit scenario wordt de [Azure Blob Storage op IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) module in de bovenste laag gebruikt om het maken en uploaden van blobs af te handelen.

Configureer de volgende modules in de **bovenste laag**:

* Een Azure-Blob Storage op IoT Edge-module.
* Een API-proxy module
  * Configureer de volgende omgevings variabelen:

    | Name | Waarde |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | De naam van de Blob Storage-module en de open poort. Bijvoorbeeld `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | De poort waarop de nginx-proxy luistert naar aanvragen van downstream-apparaten. Bijvoorbeeld `8000`. |

  * Configureer de volgende createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configureer de volgende module op een **lagere laag** voor dit scenario:

* Een API-proxy module
  * Configureer de volgende omgevings variabelen:

    | Name | Waarde |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | De poort waarop de nginx-proxy luistert naar aanvragen van downstream-apparaten. Bijvoorbeeld `8000`. |

  * Configureer de volgende createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Gebruik de volgende stappen om het ondersteunings pakket of logboek bestand te uploaden naar de Blob Storage-module die zich op de bovenste laag bevindt:

1. Maak een BLOB-container met behulp van Azure Storage Explorer of de REST-Api's. Zie voor meer informatie [gegevens opslaan aan de rand met Azure Blob Storage op IOT Edge](how-to-store-data-blob.md).
1. Vraag een upload van een logboek of ondersteuning aan op basis van de stappen in [Logboeken ophalen uit IOT Edge-implementaties](how-to-retrieve-iot-edge-logs.md), maar gebruik de domein naam `$upstream` en de open proxy poort in plaats van het adres van de Blob Storage-module. Bijvoorbeeld:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>De proxy configuratie bewerken

Een standaard configuratie bestand is inge sloten in de API-proxy module, maar u kunt via de Cloud een nieuwe configuratie door geven naar de module met behulp van de module dubbele.

Wanneer u uw eigen configuratie schrijft, kunt u nog steeds omgeving gebruiken om de instellingen per implementatie aan te passen. Gebruik de volgende syntaxis:

* Gebruiken `${MY_ENVIRONMENT_VARIABLE}` om de waarde van een omgevings variabele op te halen.
* Gebruik voorwaardelijke instructies om instellingen in of uit te scha kelen op basis van de waarde van een omgevings variabele:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Wanneer de API proxy-module een proxy configuratie parseert, vervangt deze eerst alle omgevings variabelen in de `PROXY_CONFIG_ENV_VAR_LIST` met hun opgegeven waarden met behulp van substitutie. Vervolgens wordt alles tussen een `#if_tag` en `#endif_tag` paar vervangen. De module biedt vervolgens de geparseerde configuratie van de nginx-reverse-proxy.

Als u de proxy configuratie dynamisch wilt bijwerken, gebruikt u de volgende stappen:

1. Schrijf het configuratie bestand. U kunt deze standaard sjabloon als referentie gebruiken: [nginx_default_config. conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Kopieer de tekst van het configuratie bestand en converteer het naar base64.
1. Plak het gecodeerde configuratie bestand als de waarde van de `proxy_config` gewenste eigenschap in de module dubbele.

   ![Een gecodeerd configuratie bestand plakken als waarde van de eigenschap proxy_config](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Volgende stappen

Gebruik de API-proxy module om [een downstream-IOT edge apparaat te verbinden met een Azure IOT Edge gateway](how-to-connect-downstream-iot-edge-device.md).