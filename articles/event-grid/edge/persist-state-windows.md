---
title: Status persistent in Windows-Azure Event Grid IoT Edge | Microsoft Docs
description: Status voor persistent in Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002814"
---
# <a name="persist-state-in-windows"></a>Status voor persistent in Windows

Onderwerpen en abonnementen die in de Event Grid module zijn gemaakt, worden standaard opgeslagen in het container bestandssysteem. Zonder persistentie, als de module opnieuw wordt geïmplementeerd, worden alle meta gegevens die zijn gemaakt, verloren gegaan. Als u de gegevens in de implementaties wilt behouden en opnieuw wilt opstarten, moet u de gegevens buiten het bestands systeem van de container bewaren. 

Standaard worden alleen meta gegevens persistent gemaakt en gebeurtenissen worden nog steeds in het geheugen opgeslagen voor betere prestaties. Volg de sectie gebeurtenissen persistent maken om ook gebeurtenis persistentie in te scha kelen.

In dit artikel worden de stappen beschreven die nodig zijn om Event Grid module te implementeren met persistentie in Windows-implementaties.

> [!NOTE]
>De Event Grid-module wordt uitgevoerd als een gebruiker met beperkte rechten **ContainerUser** in Windows.

## <a name="persistence-via-volume-mount"></a>Persistentie via volume koppeling

[Docker-volumes](https://docs.docker.com/storage/volumes/) worden gebruikt voor het bewaren van gegevens over implementaties. Als u een volume wilt koppelen, moet u dit maken met behulp van docker-opdrachten, machtigingen verlenen zodat de container er naar kan lezen, ernaar schrijven en de module vervolgens implementeren.

1. Maak een volume door de volgende opdracht uit te voeren:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Bijvoorbeeld:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Haal de host-map op waaraan het volume is toegewezen door de onderstaande opdracht uit te voeren

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Bijvoorbeeld:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Voorbeeld uitvoer:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Voeg als volgt de groep **gebruikers** toe aan waarde punt **koppel punt** :
    1. Start bestanden Verkenner.
    1. Ga naar de map die is gepointt door **koppel punt**.
    1. Klik met de rechter muisknop en selecteer **Eigenschappen**.
    1. Selecteer **Beveiliging**.
    1. Selecteer onder * groeps-of gebruikers namen de optie **bewerken**.
    1. Selecteer **toevoegen**, invoeren `Users` , selecteer **Namen controleren** en selecteer **OK**.
    1. Selecteer onder *machtigingen voor gebruikers* **wijzigen** en selecteer **OK**.
1. Gebruik **bindingen** om dit volume te koppelen en Event grid module opnieuw te implementeren vanuit Azure Portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie in de module. Voor Event Grid module op Windows moet **C: \\ app \\ metadataDb** zijn.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistentie via Host Directory-koppeling

In plaats van een volume te koppelen, kunt u een map op het hostsysteem maken en die directory koppelen.

1. Maak een map op het host-bestands systeem door de volgende opdracht uit te voeren.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Bijvoorbeeld:

   ```sh
   mkdir C:\myhostdir
   ```
1. Gebruik **bindingen** om uw map te koppelen en de module Event Grid van Azure Portal opnieuw te implementeren.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie in de module. Voor de module Event Grid in Windows moet **C: \\ app \\ metadataDb** zijn.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Gebeurtenissen persistent maken

Als u gebeurtenis persistentie wilt inschakelen, moet u eerst gebeurtenissen persistentie via volume koppeling of host Directory koppelen inschakelen met behulp van de bovenstaande secties.

Belang rijke aandachtspunten voor het aanhouden van persistente gebeurtenissen:

* Het persistent maken van gebeurtenissen wordt ingeschakeld per gebeurtenis abonnement en is een opt-in nadat een volume of map is gekoppeld.
* Gebeurtenis persistentie wordt tijdens het maken geconfigureerd op een gebeurtenis abonnement en kan niet worden gewijzigd nadat het gebeurtenis abonnement is gemaakt. Als u wilt overschakelen op gebeurtenis persistentie, moet u het gebeurtenis abonnement verwijderen en opnieuw maken.
* Het persistent maken van gebeurtenissen is bijna altijd langzamer dan in geheugen bewerkingen, maar het snelheids verschil is echter zeer afhankelijk van de kenmerken van het station. De verhouding tussen snelheid en betrouw baarheid is inherent aan alle Messa ging-systemen, maar wordt alleen merkbaar op grote schaal.

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