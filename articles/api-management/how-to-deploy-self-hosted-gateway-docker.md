---
title: Zelf-hostende gateway implementeren in Docker | Microsoft Docs
description: Meer informatie over het implementeren van een zelf-hostend gatewayonderdeel van Azure API Management naar Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: apimpm
ms.openlocfilehash: 531421726bc1e081d85eca9d535267520d3fea5f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725599"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Een zelf API Management-hostende Azure-gateway implementeren in Docker

Dit artikel bevat de stappen voor het implementeren van een zelf-hostend gatewayonderdeel van Azure API Management in een Docker-omgeving.

> [!NOTE]
> Het hosten van een zelf-hostende gateway in Docker is het meest geschikt voor evaluatie- en ontwikkelingsgebruiksgevallen. Kubernetes wordt aanbevolen voor productiegebruik. Zie [dit](how-to-deploy-self-hosted-gateway-kubernetes.md) document voor meer informatie over het implementeren van een zelf-hostende gateway naar Kubernetes.

## <a name="prerequisites"></a>Vereisten

- Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- Een Docker-omgeving maken. [Docker voor Desktop](https://www.docker.com/products/docker-desktop) is een goede optie voor ontwikkelings- en evaluatiedoeleinden. Zie [Docker-documentatie](https://docs.docker.com) voor informatie over alle Docker-edities, hun functies en uitgebreide documentatie over Docker zelf.
- [Een gatewayresource inrichten in uw API Management-exemplaar](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Zelf-hostende gateway wordt verpakt als een Op Linux gebaseerde Docker-container x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>De zelf-hostende gateway implementeren in Docker

1. Selecteer **Gateways onder** **Implementatie en infrastructuur.**
2. Selecteer de gatewayresource die u wilt implementeren.
3. Selecteer **Implementatie.**
4. Houd er rekening mee dat een toegangsteken in het **tekstvak Token** automatisch is gegenereerd met behulp van de standaardwaarden Vervaldatum en **Geheim.**  Kies indien nodig gewenste waarden in een van beide besturingselementen of beide besturingselementen om een nieuw token te genereren.
5. Zorg ervoor **dat Docker** is geselecteerd onder **Implementatiescripts.**
6. Selecteer **de koppeling env.conf-bestand** naast de **omgeving om** het bestand te downloaden.
7. Selecteer **het kopieerpictogram** aan de rechterkant van het **tekstvak** Uitvoeren om de Docker-opdracht naar het klembord te kopiëren.
8. Plak de opdracht in het terminalvenster (of het opdrachtvenster). Pas waar nodig de poorttoewijzingen en containernaam aan. Houd er rekening mee dat de opdracht ervan uit gaat dat het gedownloade omgevingsbestand aanwezig is in de huidige map.
   ```
       docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```
9. Voer de opdracht uit. De opdracht instrueert uw Docker-omgeving om de container uit te voeren met behulp van een [containerafbeelding](https://aka.ms/apim/sputnik/dhub) die is gedownload van de Microsoft Container Registry, en om de HTTP- (8080) en HTTPS-poorten (8081) van de container toe te wijs aan de poorten 80 en 443 op de host.
10. Voer de onderstaande opdracht uit om te controleren of de gatewaycontainer wordt uitgevoerd:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Terug u Azure Portal, klikt u  op Overzicht en controleert u of de zelf-hostende gatewaycontainer die u zojuist hebt geïmplementeerd een goede status rapporteert.

    ![gatewaystatus](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Gebruik <code>console docker container logs <gateway-name></code> de opdracht om een momentopname van het zelf-hostende gatewaylogboek weer te geven.
>
> Gebruik <code>docker container logs --help</code> de opdracht om alle weergaveopties voor logboeken weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Zie Overzicht van azure API Management zelf-hostende gateway voor meer informatie over de [zelf-hostende gateway.](self-hosted-gateway-overview.md)
* [Configureer de aangepaste domeinnaam voor de zelf-hostende gateway](api-management-howto-configure-custom-domain-gateway.md).
