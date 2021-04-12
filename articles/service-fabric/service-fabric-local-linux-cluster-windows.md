---
title: Azure Service Fabric Linux-cluster instellen in Windows
description: In dit artikel wordt beschreven hoe u Service Fabric Linux-clusters kunt instellen die worden uitgevoerd op Windows-ontwikkel machines. Deze aanpak is nuttig voor het ontwikkelen van meerdere platforms.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087074"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Een Linux Service Fabric-cluster instellen op uw Windows-ontwikkelaars computer

In dit document wordt beschreven hoe u een lokaal Linux Service Fabric-cluster instelt op een Windows-ontwikkel machine. Het instellen van een lokaal Linux-cluster is handig om snel toepassingen te testen die gericht zijn op Linux-clusters, maar die zijn ontwikkeld op een Windows-computer.

## <a name="prerequisites"></a>Vereisten
Service Fabric-clusters op basis van Linux worden niet uitgevoerd op Windows, maar voor het inschakelen van platformen voor meerdere platforms hebben we een Linux-Service Fabric één box cluster docker-container die kan worden geïmplementeerd via docker voor Windows.

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Ten minste 4 GB RAM-geheugen
* Nieuwste versie van [docker voor Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker moet worden uitgevoerd in de modus Linux-containers

>[!TIP]
> Volg de stappen in de [docker-documentatie](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)om docker te installeren op uw Windows-computer. Na de installatie dient u [uw installatie te controleren](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Een lokale container maken en Service Fabric configureren
Voer de volgende stappen uit als u een lokale docker-container wilt instellen en een Service Fabric cluster wilt uitvoeren:


1. Werk de configuratie van de Docker-daemon op uw host bij met het volgende en start de Docker-daemon opnieuw op: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    De aanbevolen manier om bij te werken is door naar te gaan: 

    * Docker-pictogram > instellingen > docker-engine
    * De hierboven vermelde nieuwe velden toevoegen
    * Toep assen & opnieuw opstarten: Start de docker-daemon opnieuw op om de wijzigingen van kracht te laten worden.

2. Start het cluster via Power shell.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Standaard wordt hierdoor de installatiekopie met de nieuwste versie van Service Fabric opgehaald. Zie de [service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) -pagina op docker hub voor een bepaalde revisies.



3. Optioneel: bouw uw uitgebreide Service Fabric-installatie kopie.

    Maak in een nieuwe map een bestand `Dockerfile` met de naam om uw aangepaste installatie kopie te bouwen:

    >[!NOTE]
    >U kunt de bovenstaande afbeelding aanpassen met een Dockerfile om extra Program ma's of afhankelijkheden aan uw container toe te voegen.
    >Bijvoorbeeld: het toevoegen van `RUN apt-get install nodejs -y` biedt ondersteuning voor `nodejs`-toepassingen als uitvoerbare gastbestanden.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Standaard wordt hierdoor de installatiekopie met de nieuwste versie van Service Fabric opgehaald. Ga naar de [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/)-pagina als u een bepaalde revisie wilt ophalen.

    Als u een herbruikbare afbeelding wilt bouwen vanuit de `Dockerfile` , opent u een Terminal en `cd` gaat u rechtstreeks naar het werk dat u `Dockerfile` vervolgens uitvoert:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Deze bewerking kan enige tijd duren, maar hoeft slechts eenmaal te worden uitgevoerd.

    U kunt nu snel een lokale kopie van Service Fabric wanneer u deze nodig hebt door uit te voeren:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Door een naam op te geven voor uw instantie van de container, kunt u deze op een beter leesbare manier verwerken. 
    >
    >Als uw toepassing op bepaalde poorten luistert, moeten de poorten worden opgegeven met behulp van aanvullende `-p` labels. Bijvoorbeeld, als uw toepassing op poort 8080 luistert, voeg dan de volgende `-p` tag toe:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. Het duurt even voor het cluster is gestart. U kunt logboeken bekijken via de volgende opdracht of naar het dashboard gaan om de status van het cluster, `http://localhost:19080`, te bekijken:

    ```powershell 
    docker logs sftestcluster
    ```

5. Nadat het cluster is geïmplementeerd, zoals in stap 4 is geobserveerd, kunt u ``http://localhost:19080`` vanaf de Windows-computer naar het dash board van service Fabric Explorer zoeken. Op dit moment kunt u verbinding maken met dit cluster met behulp van hulpprogram ma's van uw Windows-ontwikkelaars computer en toepassingen implementeren die gericht zijn op Linux-Service Fabric clusters. 

    > [!NOTE]
    > De Eclipse-invoegtoepassing wordt momenteel niet ondersteund in Windows. 

6. Wanneer u klaar bent, moet u de container stoppen en opschonen met de volgende opdracht:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Bekende beperkingen 
 
 Hier volgen bekende beperkingen van het uitvoeren van het lokale cluster in een container voor Mac-computers: 
 
 * De DNS-service wordt niet uitgevoerd en wordt momenteel niet ondersteund in de container. [Probleem #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Voor het uitvoeren van op containers gebaseerde apps moet SF worden uitgevoerd op een Linux-host. Geneste container-apps worden momenteel niet ondersteund.

## <a name="next-steps"></a>Volgende stappen
* [Uw eerste Service Fabric Java-toepassing in Linux maken en implementeren met behulp van Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* Aan de slag met [eclips](./service-fabric-get-started-eclipse.md)
* Andere Java-voor [beelden](https://github.com/Azure-Samples/service-fabric-java-getting-started) bekijken
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
