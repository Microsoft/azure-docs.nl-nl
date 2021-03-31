---
title: Zelf-hostende Gateway implementeren naar docker | Microsoft Docs
description: Meer informatie over het implementeren van een zelf-hostend gateway onderdeel van Azure API Management naar docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82205090"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Een Azure API Management zelf-hostende Gateway implementeren naar docker

Dit artikel bevat de stappen voor het implementeren van een zelf-hostend gateway onderdeel van Azure API Management naar een docker-omgeving.

> [!NOTE]
> Hosting van zelf-hostende gateway in docker is het meest geschikt voor het gebruik van evaluatie-en ontwikkelings scenario's. Kubernetes wordt aanbevolen voor productie gebruik. Zie [Dit](how-to-deploy-self-hosted-gateway-kubernetes.md) document voor meer informatie over het implementeren van een zelf-hostende gateway naar Kubernetes.

## <a name="prerequisites"></a>Vereisten

- Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- Maak een docker-omgeving. [Docker voor desktop](https://www.docker.com/products/docker-desktop) is een goede optie voor ontwikkelings-en evaluatie doeleinden. Raadpleeg [docker-documentatie](https://docs.docker.com) voor informatie over alle docker-edities, hun functies en uitgebreide documentatie over docker zelf.
- [Een gateway bron inrichten in uw API Management-exemplaar](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Een zelf-hostende gateway is verpakt als een x86-64 Linux-docker-container.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>De zelf-hostende Gateway implementeren naar docker

1. Selecteer **gateways** onder **implementatie en infra structuur**.
2. Selecteer de gateway resource die u wilt implementeren.
3. Selecteer **implementatie**.
4. Houd er rekening mee dat een toegangs token in het tekstvak **token** automatisch is gegenereerd voor u met de standaard waarden voor de **verlopende** en **geheime sleutel** . Indien nodig kunt u de gewenste waarden in een of beide besturings elementen kiezen om een nieuw token te genereren.
4. Zorg ervoor dat **docker** is geselecteerd onder **implementatie scripts**.
5. Selecteer de bestands koppeling **env. conf** naast de **omgeving** om het bestand te downloaden.
6. Selecteer het pictogram **kopiëren** aan de rechter kant van het tekstvak **uitvoeren** om de docker-opdracht naar het klem bord te kopiëren.
7. Plak de opdracht in het venster Terminal (of Command). Pas de poort toewijzingen en de container naam waar nodig aan. Houd er rekening mee dat met de opdracht wordt aangenomen dat het gedownloade omgevings bestand aanwezig is in de huidige map.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Voer de opdracht uit. De opdracht geeft uw docker-omgeving de instructie om de container uit te voeren met behulp van de [container installatie kopie](https://aka.ms/apim/sputnik/dhub) die is gedownload van de micro soft-container Registry en om de http-(8080) en HTTPS-poorten (8081) van de container toe te wijzen aan de poorten 80 en 443 op de host.
9. Voer de onderstaande opdracht uit om te controleren of de gateway container wordt uitgevoerd:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Ga terug naar Azure Portal, klik op **overzicht** en bevestig dat zelf-hostende gateway container die u zojuist hebt geïmplementeerd, een status in orde meldt.

![Gateway status](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Gebruik <code>console docker container logs <gateway-name></code> de opdracht om een moment opname van het zelf-hostende gateway logboek weer te geven.
>
> Gebruik <code>docker container logs --help</code> de opdracht om alle opties voor het weer geven van logboeken weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)voor meer informatie over de zelf-hostende gateway.
* [Configureer de aangepaste domein naam voor de zelf-hostende gateway](api-management-howto-configure-custom-domain-gateway.md).
