---
title: Een container maken voor Apache Tomcat op Linux
description: Maak een Linux-container om een toepassing beschikbaar te maken die wordt uitgevoerd op Apache Tomcat server op Azure Service Fabric. Bouw een docker-installatie kopie met uw toepassing en Apache Tomcat-server, push de installatie kopie naar een container register, bouw en implementeer een Service Fabric-container toepassing.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 3de97bc277195dff2daf5868c0eb9aec5d6e27c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96534026"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Service Fabric-container maken met Apache Tomcat server op Linux
Apache Tomcat is een populaire, open-source implementatie van de Java-servlet en Java-Server technologieën. In dit artikel wordt beschreven hoe u een container bouwt met Apache Tomcat en een eenvoudige webtoepassing, de container implementeert in een Service Fabric cluster met Linux en verbinding maakt met de webtoepassing.  

Zie de [Introductie pagina van Apache Tomcat](https://tomcat.apache.org/)voor meer informatie over Apache Tomcat. 

## <a name="prerequisites"></a>Vereisten
* Een ontwikkelcomputer waarop wordt uitgevoerd:
  * [Service Fabric SDK en hulpprogramma's](service-fabric-get-started-linux.md).
  * [Docker CE voor Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric-CLI](service-fabric-cli.md)

* Een container register in Azure Container Registry. U kunt een container register maken in uw Azure-abonnement met behulp van [de Azure Portal](../container-registry/container-registry-get-started-portal.md) of [de Azure cli](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Een tomcat-installatie kopie bouwen en lokaal uitvoeren
Volg de stappen in deze sectie om een docker-installatie kopie te maken op basis van een Apache Tomcat-installatie kopie en een eenvoudige web-app en voer deze uit in een container op het lokale systeem. 
 
1. Kloon de [service Fabric aan de slag met](https://github.com/Azure-Samples/service-fabric-java-getting-started) de opslag plaats voor Java-voor beelden op uw ontwikkel computer.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Wijzig mappen in de voorbeeld Directory Apache Tomcat server (*service-Fabric-Java-Getting-Started/container-Apache-Tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Maak een docker-bestand op basis van de officiële [tomcat-installatie kopie](https://hub.docker.com/_/tomcat/) die zich bevindt in docker hub en het Tomcat server-voor beeld. Maak in de map *service-Fabric-Java-Getting-Started/container-Apache-Tomcat-web-server-voor beeld* een bestand met de naam *Dockerfile* (zonder bestands extensie). Voeg het volgende toe aan het bestand *Dockerfile* en sla de wijzigingen op:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Zie de [Dockerfile-referentie](https://docs.docker.com/engine/reference/builder/) voor meer informatie.


4. Meld u aan bij docker en voer de `docker build` opdracht uit om de installatie kopie te maken waarmee uw webtoepassing wordt uitgevoerd:

   ```bash
   docker login
   docker build . -t tomcattest
   ```

   Met deze opdracht wordt de nieuwe installatie kopie gemaakt met behulp van de instructies in de Dockerfile, naamgeving (-t tagging) van de installatie kopie `tomcattest` . Als u een container installatie kopie wilt maken, wordt eerst de basis installatie kopie gedownload uit docker hub en wordt de toepassing hieraan toegevoegd. 

   Nadat de buildopdracht is voltooid, voert u de opdracht `docker images` uit om de gegevens van de nieuwe installatiekopie te bekijken:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Controleer of de container toepassing lokaal wordt uitgevoerd voordat u deze naar het container register pusht:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` benoemt de container, zodat u deze kunt gebruiken met een beschrijvende naam in plaats van de ID.
   * `-p` Hiermee geeft u de poort toewijzing tussen de container en het besturings systeem van de host op. 

   > [!Note]
   > De poort die u met de `-p` para meter opent, moet de poort zijn waarop uw Tomcat-toepassing luistert naar aanvragen. In het huidige voor beeld is er een connector geconfigureerd in het bestand *ApacheTomcat/conf/server.xml* om te Luis teren op poort 8080 voor HTTP-aanvragen. Deze poort is toegewezen aan poort 8080 op de host. 

   Zie de [documentatie van docker run](https://docs.docker.com/engine/reference/commandline/run/)voor meer informatie over andere para meters.

1. Als u de container wilt testen, opent u een browser en voert u een van de volgende Url's in. Er wordt een variant van de Hallo wereld! weer geven welkomst scherm voor elke URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hallo wereld/sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. De container stoppen en verwijderen van uw ontwikkel computer:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>De tomcat-installatie kopie naar het container register pushen
Nu u hebt gecontroleerd of de tomcat-installatie kopie wordt uitgevoerd in een container op uw ontwikkel computer, pusht u deze naar een opslag plaats in een container register om de onderbreking van de ontwikkelings-en implementatie werk stromen van uw installatie kopie te [verminderen](../container-registry/buffer-gate-public-content.md) . In dit artikel wordt gebruikgemaakt van Azure Container Registry voor het opslaan van de installatie kopie, maar u kunt elk gewenst container register gebruiken als u een aantal stappen hebt gewijzigd. In dit artikel wordt ervan uitgegaan dat de register naam *myregistry* is en dat de volledige register naam myregistry.azurecr.io is. Wijzig deze op de juiste manier voor uw scenario. 

1. Voer uit `docker login` om u aan te melden bij uw container register met uw [register referenties](../container-registry/container-registry-authentication.md).

   In het volgende voorbeeld worden de id en het wachtwoord van een [service-principal](../active-directory/develop/app-objects-and-service-principals.md) van Azure Active Directory doorgegeven. U hebt bijvoorbeeld een service-principal aan uw register toegewezen voor een automatiseringsscenario. U kunt zich ook aanmelden met uw gebruikers naam en wacht woord voor het REGI ster.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Met de volgende opdracht maakt u een tag (of alias) van de installatiekopie met een volledig gekwalificeerd pad naar uw register. In dit voorbeeld wordt de installatiekopie in de naamruimte `samples` geplaatst om overbodige items in de hoofdmap van het register te voorkomen.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. De installatiekopie naar het containerregister pushen:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>De Service Fabric-container toepassing bouwen en implementeren
Nu u de tomcat-installatie kopie naar een container register hebt gepusht, kunt u een Service Fabric container toepassing bouwen en implementeren die de tomcat-installatie kopie uit uw REGI ster haalt en uitvoeren als een container service in uw cluster. 

1. Maak een nieuwe map buiten uw lokale kloon (buiten de *service-Fabric-Java-Getting-Started* Directory-structuur). Ga naar het en gebruik Yeoman om een steiger voor een container toepassing te maken: 

   ```bash
   yo azuresfcontainer 
   ```
   Voer de volgende waarden in wanneer u hierom wordt gevraagd:

   * Uw toepassing een naam: ServiceFabricTomcat
   * Naam van de toepassings service: TomcatService
   * Voer de naam van de installatie kopie in: Geef de URL voor de container installatie kopie op in het container register. bijvoorbeeld myregistry.azurecr.io/samples/tomcattest.
   * Opdrachten: laat dit veld leeg. Omdat voor deze installatiekopie een workloadinvoerpunt is gedefinieerd, hoeft u niet expliciet invoeropdrachten op te geven (opdrachten worden uitgevoerd in de container, zodat de container na het opstarten actief blijft).
   * Aantal exemplaren van de gast container toepassing: 1

   ![Service Fabric Yeoman-generator voor containers](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. Voeg in het service manifest (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) het volgende XML-bestand onder de root **ServiceManfest** -code toe om de poort te openen waarnaar uw toepassing luistert naar aanvragen. Het **eindpunt** label declareert het protocol en de poort voor het eind punt. Voor dit artikel luistert de container service naar poort 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. Voeg in het manifest van de toepassing (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) onder de **ServiceManifestImport** -tag de volgende XML toe. Vervang de **AccountName** en het **wacht woord** in de **RepositoryCredentials** -tag door de naam van het container register en het wacht woord dat vereist is om u aan te melden.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Het **ContainerHostPolicies** -label geeft beleids regels op voor het activeren van container-hosts.
    
   * Het **PortBinding** -label configureert het poort toewijzings beleid voor de container poort-naar-host. Het kenmerk **ContainerPort** is ingesteld op 8080 omdat de container poort 8080 beschikbaar maakt, zoals is opgegeven in de Dockerfile. Het kenmerk **EndpointRef** is ingesteld op ' endpointTest ', het eind punt dat is gedefinieerd in het service manifest in de vorige stap. Inkomende aanvragen voor de service op poort 8080 worden dus toegewezen aan poort 8080 op de container. 
   * De **RepositoryCredentials** -tag geeft de referenties op die de container moet verifiëren met de opslag plaats (privé) waar de installatie kopie van wordt opgehaald. U hebt dit beleid niet nodig als de installatie kopie wordt opgehaald uit een open bare opslag plaats.
    

12. Maak in de map *ServiceFabricTomcat* verbinding met uw service Fabric-cluster. 

   * Als u verbinding wilt maken met het lokale Service Fabric cluster, voert u de volgende handelingen uit:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Als u verbinding wilt maken met een beveiligd Azure-cluster, controleert u of het client certificaat aanwezig is als een. pem-bestand in de map *ServiceFabricTomcat* en voert u de volgende handelingen uit: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Vervang in de voor gaande opdracht door `your-certificate.pem` de naam van het client certificaat bestand. In ontwikkel-en test omgevingen wordt het cluster certificaat vaak gebruikt als het client certificaat. Als uw certificaat niet zelf is ondertekend, laat u de `-no-verify` para meter weg. 
       
     Cluster certificaten worden doorgaans lokaal gedownload als. pfx-bestand. Als u uw certificaat nog niet in de PEM-indeling hebt, kunt u de volgende opdracht uitvoeren om een. pem-bestand te maken vanuit een pfx-bestand:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Als uw pfx-bestand niet is beveiligd met een wacht woord, gebruikt u `-passin pass:` voor de laatste para meter.


13. Voer het installatie script uit dat is opgenomen in de sjabloon om de toepassing te implementeren in uw cluster. Het script kopieert het toepassings pakket naar de installatie kopie opslag van het cluster, registreert het toepassings type en maakt een exemplaar van de toepassing.

     ```bash
     ./install.sh
     ```

   Nadat u het installatie script hebt uitgevoerd, opent u een browser en navigeert u naar Service Fabric Explorer:
    
   * Gebruik `http://localhost:19080/Explorer` (Vervang *localhost* door het privé-IP-adres van de virtuele machine als u Vagrant op Mac OS X gebruikt) op een lokaal cluster.
   * Gebruik op een veilig Azure-cluster `https://PublicIPorFQDN:19080/Explorer` . 
    
   Vouw het knoop punt **toepassingen** uit en houd er rekening mee dat er nu een vermelding is voor uw toepassings type, **ServiceFabricTomcatType** en een andere voor het eerste exemplaar van dat type. Het kan enkele minuten duren voordat de toepassing volledig is geïmplementeerd, dus een ogen blik geduld.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Als u toegang wilt krijgen tot de toepassing op de Tomcat-server, opent u een browser venster en voert u een van de volgende Url's in. Als u hebt geïmplementeerd op het lokale cluster, gebruikt u *localhost* voor *PublicIPorFQDN*. Er wordt een variant van de Hallo wereld! weer geven welkomst scherm voor elke URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Opschonen
Gebruik het uninstall-script dat is opgenomen in de sjabloon om het toepassings exemplaar te verwijderen uit het cluster en de registratie van het toepassings type op te heffen.

```bash
./uninstall.sh
```

Nadat u de installatiekopie naar het containerregister hebt gepusht, kunt u de lokale installatiekopie op de ontwikkelcomputer verwijderen:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Volgende stappen
* Lees [uw eerste service Fabric-container toepassing maken in Linux](service-fabric-get-started-containers-linux.md)voor snelle stappen voor aanvullende functies van de Linux-container.
* Lees de zelf studie [een Linux-container-app maken](service-fabric-tutorial-create-container-images.md) voor meer gedetailleerde stappen voor Linux-containers.
* Meer informatie over het uitvoeren van [containers in Service Fabric](service-fabric-containers-overview.md).


