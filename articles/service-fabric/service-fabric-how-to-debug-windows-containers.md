---
title: Fouten opsporen in Windows-containers met Service Fabric en VS
description: Meer informatie over het opsporen van fouten in Windows-containers in azure Service Fabric met behulp van Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 3e6e7785278b182cebb21115a70f35ade52303c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247248"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Procedure: fouten opsporen in Windows-containers in azure Service Fabric met behulp van Visual Studio 2019

Met Visual Studio 2019 kunt u fouten opsporen in .NET-toepassingen in containers als Service Fabric Services. Dit artikel laat u zien hoe u uw omgeving kunt configureren en vervolgens fouten kunt opsporen in een .NET-toepassing in een container die wordt uitgevoerd in een lokaal Service Fabric cluster.

## <a name="prerequisites"></a>Vereisten

* In Windows 10 voert u deze Snelstartgids uit om [Windows 10 te configureren voor het uitvoeren van Windows-containers](/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* In Windows Server 2016, volgt u deze Quick [Start om Windows-containers uit te voeren](/virtualization/windowscontainers/quick-start/quick-start-windows-server) op Windows 2016
* Stel uw lokale Service Fabric-omgeving in door [de ontwikkel omgeving voor te bereiden in Windows](./service-fabric-get-started.md)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Uw ontwikkel omgeving configureren voor het opsporen van fouten in containers

1. Zorg ervoor dat de docker voor Windows-service wordt uitgevoerd voordat u verdergaat met de volgende stap.

1. Ter ondersteuning van de DNS-omzetting tussen containers moet u uw lokale ontwikkel cluster instellen met behulp van de computer naam. Deze stappen zijn ook nodig als u services wilt adresseren via de omgekeerde proxy.
   1. Power shell openen als Administrator
   2. Ga doorgaans naar de map voor het instellen van het SDK-cluster `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup` .
   3. Het script uitvoeren `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > U kunt de gebruiken `-CreateOneNodeCluster` om een cluster met één knoop punt te installeren. Met de standaard instelling wordt een lokaal cluster met vijf knoop punten gemaakt.
      >

      Zie [DNS-service in Azure service Fabric](./service-fabric-dnsservice.md)voor meer informatie over de DNS-service in service Fabric. Zie voor meer informatie over het gebruik van Service Fabric reverse proxy van services die in een container worden uitgevoerd, [reverse proxy rehandling for services die in containers worden uitgevoerd](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Bekende beperkingen bij het opsporen van fouten in containers in Service Fabric

Hieronder vindt u een lijst met bekende beperkingen met containers voor fout opsporing in Service Fabric en mogelijke oplossingen:

* Het gebruik van localhost voor ClusterFQDNorIP biedt geen ondersteuning voor DNS-omzetting in containers.
    * Oplossing: het lokale cluster instellen met behulp van machine naam (zie hierboven)
* Als Windows10 wordt uitgevoerd op een virtuele machine, wordt DNS-antwoord niet teruggestuurd naar de container.
    * Oplossing: Schakel de UDP-checksum-offload voor IPv4 uit op de Virtual Machines NIC
    * Als u Windows10 uitvoert, worden de netwerk prestaties op de computer verslechterd.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Het omzetten van services in dezelfde toepassing met behulp van de DNS-service naam werkt niet in Windows10, als de toepassing is geïmplementeerd met behulp van docker opstellen
    * Oplossing: gebruik ServiceName. ApplicationName om service-eind punten op te lossen
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Als u IP-adres gebruikt voor ClusterFQDNorIP, verbreekt het wijzigen van de primaire IP op de host de DNS-functionaliteit.
    * Oplossing: Maak het cluster opnieuw met het nieuwe primaire IP-adres op de host of gebruik de computer naam. Deze uitsplitsing is per ontwerp.
* Als de FQDN waarmee het cluster is gemaakt, niet kan worden omgezet in het netwerk, mislukt de DNS.
    * Oplossing: Maak het lokale cluster opnieuw met behulp van het primaire IP-adres van de host. Deze fout is standaard in het ontwerp.
* Bij het opsporen van fouten in een container zijn docker-Logboeken alleen beschikbaar in het Visual Studio-uitvoer venster, niet via Service Fabric Api's, waaronder Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Fouten opsporen in een .NET-toepassing die wordt uitgevoerd in docker-containers op Service Fabric

1. Start Visual Studio als beheerder.

1. Open een bestaande .NET-toepassing of maak een nieuwe.

1. Klik met de rechter muisknop op het project en selecteer **add-> container Orchestrator support-> service Fabric**

1. Druk op **F5** om de fout opsporing van de toepassing te starten.

    Visual Studio biedt ondersteuning voor console-en ASP.NET-project typen voor .NET en .NET core.

## <a name="next-steps"></a>Volgende stappen
Zie [service Fabric containers Overview](service-fabric-containers-overview.md)voor meer informatie over de mogelijkheden van service Fabric en containers.
