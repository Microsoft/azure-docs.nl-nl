---
title: Status persistent in Linux-Azure Event Grid IoT Edge | Microsoft Docs
description: Meta gegevens persistent maken in Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171479"
---
# <a name="persist-state-in-linux"></a>Status persistent in Linux

Onderwerpen en abonnementen die in de Event Grid module zijn gemaakt, worden standaard opgeslagen in het container bestandssysteem. Zonder persistentie, als de module opnieuw wordt geïmplementeerd, worden alle meta gegevens die zijn gemaakt, verloren gegaan. Als u de gegevens in de implementaties wilt behouden en opnieuw wilt opstarten, moet u de gegevens buiten het bestands systeem van de container bewaren.

Standaard worden alleen meta gegevens persistent gemaakt en gebeurtenissen worden nog steeds in het geheugen opgeslagen voor betere prestaties. Volg de sectie gebeurtenissen persistent maken om ook gebeurtenis persistentie in te scha kelen.

Dit artikel bevat de stappen voor het implementeren van de Event Grid module met persistentie in Linux-implementaties.

> [!NOTE]
>De module Event Grid wordt uitgevoerd als een gebruiker met beperkte bevoegdheden met een UID `2000` en naam `eventgriduser` .

## <a name="persistence-via-volume-mount"></a>Persistentie via volume koppeling

 [Docker-volumes](https://docs.docker.com/storage/volumes/) worden gebruikt om de gegevens in de implementaties te bewaren. U kunt docker automatisch een benoemd volume laten maken als onderdeel van de implementatie van de Event Grid-module. Deze optie is de eenvoudigste optie. U kunt de naam van het volume dat wordt gemaakt in de sectie **bindingen** als volgt opgeven:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie binnen de module. Voor de module Event Grid op Linux moet het **/app/metadataDb** zijn.

De volgende configuratie resulteert bijvoorbeeld in het maken van het volume **egmetadataDbVol** , waar de meta gegevens blijven bestaan.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

In plaats van een volume te koppelen, kunt u een map op het hostsysteem maken en die directory koppelen.

## <a name="persistence-via-host-directory-mount"></a>Persistentie via Host Directory-koppeling

In plaats van een docker-volume hebt u ook de mogelijkheid om een host-map te koppelen.

1. Maak eerst een gebruiker met de naam **eventgriduser** en id **2000** op de hostmachine door de volgende opdracht uit te voeren:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Maak een map op het bestands systeem van de host door de volgende opdracht uit te voeren.

   ```sh
   md <your-directory-name-here>
   ```

    Als u bijvoorbeeld de volgende opdracht uitvoert, wordt er een map gemaakt met de naam **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Maak vervolgens de **eventgriduser** -eigenaar van deze map door de volgende opdracht uit te voeren.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Bijvoorbeeld:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Gebruik **bindingen** om de map te koppelen en de module Event Grid van Azure Portal opnieuw te implementeren.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Bijvoorbeeld:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie binnen de module. Voor de module Event Grid op Linux moet **/app/metadataDb** en **/app/eventsDb**


## <a name="persist-events"></a>Gebeurtenissen persistent maken

Als u gebeurtenis persistentie wilt inschakelen, moet u eerst meta gegevens persistentie via volume koppeling of host Directory koppelen inschakelen met behulp van de bovenstaande secties.

Belang rijke aandachtspunten voor het aanhouden van persistente gebeurtenissen:

* Het persistent maken van gebeurtenissen wordt ingeschakeld per gebeurtenis abonnement en is een opt-in nadat een volume of map is gekoppeld.
* Gebeurtenis persistentie wordt tijdens het maken geconfigureerd op een gebeurtenis abonnement en kan niet worden gewijzigd nadat het gebeurtenis abonnement is gemaakt. Als u wilt overschakelen op gebeurtenis persistentie, moet u het gebeurtenis abonnement verwijderen en opnieuw maken.
* Het persistent maken van gebeurtenissen is bijna altijd langzamer dan in geheugen bewerkingen, maar het snelheids verschil is echter zeer afhankelijk van de kenmerken van het station. De verhouding tussen snelheid en betrouw baarheid is inherent aan alle berichten systemen, maar wordt in het algemeen alleen op grote schaal in het oog.

Als u gebeurtenis persistentie wilt inschakelen voor een gebeurtenis abonnement, stelt `persistencePolicy` u in op `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
