---
title: Een Azure Service Fabric-container toepassing maken in Linux
description: Maak uw eerste Linux-containertoepassing in Azure Service Fabric. Bouw een Docker-installatiekopie met uw toepassing, push de installatiekopie naar een containerregister, en bouw en implementeer een Service Fabric-containertoepassing.
ms.topic: conceptual
ms.date: 1/4/2019
ms.custom: devx-track-python
ms.openlocfilehash: 0481cc2d36f7882bbd8eea9b984c3dc388de5dee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96534077"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Uw eerste Service Fabric-containertoepassing maken in Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Er zijn geen wijzigingen in uw toepassing vereist om een bestaande toepassing in een Linux-container uit te voeren in een Service Fabric-cluster. Dit artikel helpt u bij het maken van een Docker-installatiekopie met een Python [Flask](http://flask.pocoo.org/)-webtoepassing en het implementeren ervan in een Service Fabric-cluster. U gaat uw containertoepassing ook delen via [Azure Container Registry](../container-registry/index.yml). In dit artikel wordt ervan uitgegaan dat u de basisbeginselen kent van Docker. Meer informatie over Docker kunt u lezen in het [Docker-overzicht](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Dit artikel is van toepassing op een Linux-ontwikkel omgeving.  De runtime van Service Fabric cluster en de docker-runtime moeten op hetzelfde besturings systeem worden uitgevoerd.  U kunt geen Linux-containers uitvoeren op een Windows-cluster.

## <a name="prerequisites"></a>Vereisten
* Een ontwikkelcomputer waarop wordt uitgevoerd:
  * [Service Fabric SDK en hulpprogramma's](service-fabric-get-started-linux.md).
  * [Docker CE voor Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric-CLI](service-fabric-cli.md)

* Een Linux-cluster met drie of meer knoop punten.

* Een register in Azure Container Registry - [Een containerregister maken](../container-registry/container-registry-get-started-portal.md) in uw Azure-abonnement. 

## <a name="define-the-docker-container"></a>De Docker-container definiëren
Bouw een installatiekopie op basis van de [Python-installatiekopie](https://hub.docker.com/_/python/) die zich in de Docker-hub bevindt. 

Geef uw Docker-container in een Dockerfile op. Het bestand Dockerfile bestaat uit instructies voor het instellen van de omgeving in de container, het laden van de toepassing die u wilt uitvoeren en het toewijzen van poorten. Het Dockerfile is de invoer van de opdracht `docker build` waarmee de installatiekopie wordt gemaakt. 

Maak een lege map en maak het bestand *Dockerfile* (zonder extensie). Voeg het volgende toe aan het bestand *Dockerfile* en sla de wijzigingen op:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Lees het [Dockerfile-referentiemateriaal](https://docs.docker.com/engine/reference/builder/) voor meer informatie.

## <a name="create-a-basic-web-application"></a>Algemene webtoepassing maken
Maak een Flask-toepassing die luistert op poort 80 en 'Hallo Wereld!' retourneert. Maak in dezelfde map het bestand *requirements.txt*. Voeg het volgende toe en sla de wijzigingen op:
```
Flask
```

Maak ook het bestand *app.py* en voeg het volgende codefragment toe:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="login-to-docker-and-build-the-image"></a>Meld u aan bij docker en bouw de installatie kopie

Daarna maken we de installatie kopie die uw webtoepassing uitvoert. Bij het ophalen van open bare installatie kopieën van docker (zoals `python:2.7-slim` in onze Dockerfile), is het een best practice om te verifiëren met uw docker hub-account in plaats van een anonieme pull-aanvraag te doen.

> [!NOTE]
> Wanneer u regel matig anonieme pull-aanvragen maakt, ziet u mogelijk docker-fouten die vergelijkbaar zijn met `ERROR: toomanyrequests: Too Many Requests.` of `You have reached your pull rate limit.` worden geverifieerd bij docker hub om deze fouten te voor komen. Zie [open bare inhoud beheren met Azure container Registry](../container-registry/buffer-gate-public-content.md) voor meer informatie.

Open een PowerShell-venster en navigeer naar de map met het bestand Dockerfile. Voer vervolgens de volgende opdrachten uit:

```
docker login
docker build -t helloworldapp .
```

Met deze opdracht wordt de nieuwe installatiekopie gebouwd met behulp van de instructies in het Dockerfile, en krijgt de installatiekopie de naam (-t tagging) `helloworldapp`. Voor het bouwen van een containerinstallatiekopie, wordt eerst de basisinstallatiekopie gedownload vanaf Docker Hub, waaraan de toepassing wordt toegevoegd. 

Nadat de buildopdracht is voltooid, voert u de opdracht `docker images` uit om de gegevens van de nieuwe installatiekopie te bekijken:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren
Controleer of de containertoepassing lokaal wordt uitgevoerd voordat u deze naar het containerregister pusht. 

Voer de toepassing uit en wijs poort 4000 van uw computer toe aan de door de container beschikbaar gestelde poort 80:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

Bij *naam* kunt de actieve container een naam geven (in plaats van de container-id).

Maak verbinding met de actieve container. Open een webbrowser die verwijst naar het IP-adres dat is geretourneerd op poort 4000, bijvoorbeeld ' http: \/ /localhost: 4000 '. Als het goed is, ziet u de koptekst Hallo wereld! weergegeven in de browser.

![Hallo wereld!][hello-world]

Als u de container wilt stoppen, voert u dit uit:

```bash
docker stop my-web-site
```

De container verwijderen van de ontwikkelcomputer:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>De installatiekopie naar het containerregister pushen
Nadat u hebt gecontroleerd of de toepassing wordt uitgevoerd in Docker, pusht u de installatiekopie naar het register in Azure Container Registry.

Voer uit `docker login` om u aan te melden bij uw container register met uw [register referenties](../container-registry/container-registry-authentication.md).

In het volgende voorbeeld worden de id en het wachtwoord van een [service-principal](../active-directory/develop/app-objects-and-service-principals.md) van Azure Active Directory doorgegeven. U hebt bijvoorbeeld een service-principal aan uw register toegewezen voor een automatiseringsscenario. U kunt zich ook aanmelden met uw gebruikers naam en wacht woord voor het REGI ster.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Met de volgende opdracht maakt u een tag (of alias) van de installatiekopie met een volledig gekwalificeerd pad naar uw register. In dit voorbeeld wordt de installatiekopie in de naamruimte `samples` geplaatst om overbodige items in de hoofdmap van het register te voorkomen.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

De installatiekopie naar het containerregister pushen:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>De Docker-installatiekopie inpakken met Yeoman
De Service Fabric-SDK voor Linux bevat een [Yeoman](https://yeoman.io/)-generator waarmee u gemakkelijk uw eerste toepassing kunt maken en een containerinstallatiekopie kunt toevoegen. We gebruiken Yeoman om een toepassing te maken met een enkele Docker-container genaamd *SimpleContainerApp*.

Om een Service Fabric-containertoepassing te maken, opent u een terminalvenster en voert u `yo azuresfcontainer` uit. 

Geef uw toepassing een naam (bijvoorbeeld `mycontainer`) evenals de toepassingsservice (bijvoorbeeld `myservice`).

Geef voor de naam van de installatie kopie de URL voor de container installatie kopie in een container register op (bijvoorbeeld ' myregistry.azurecr.io/samples/helloworldapp '). 

Omdat voor deze installatiekopie een workloadinvoerpunt is gedefinieerd, hoeft u niet expliciet invoeropdrachten op te geven (opdrachten worden uitgevoerd in de container, zodat de container na het opstarten actief blijft). 

Geef '1' exemplaar op.

Geef de poort toewijzing in de juiste indeling op. Voor dit artikel moet u ```80:4000``` de poort toewijzing opgeven. Als u dit doet, worden alle binnenkomende aanvragen die afkomstig zijn van poort 4000 op de hostmachine omgeleid naar poort 80 van de container.

![Service Fabric Yeoman-generator voor containers][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>Verificatie van container opslagplaats configureren

Bekijk [container opslagplaats verificatie](configure-container-repository-credentials.md)voor meer informatie over het configureren van verschillende verificatie typen voor het downloaden van container installatie kopieën.

## <a name="configure-isolation-mode"></a>Isolatiemodus configureren
Met de 6,3-runtime versie wordt VM-isolatie ondersteund voor Linux-containers, waardoor er twee isolatie modi voor containers zijn: proces-en Hyper-V-ondersteuning. Met de isolatie modus voor Hyper-V worden de kernels geïsoleerd tussen elke container en de container-host. De Hyper-V-isolatie wordt geïmplementeerd met behulp van [Clear-containers](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker). De isolatie modus wordt opgegeven voor Linux-clusters in het- `ServicePackageContainerPolicy` element in het manifest bestand van de toepassing. De isolatiemodi die kunnen worden opgegeven zijn `process`, `hyperv` en `default`. De standaard instelling is proces isolatie modus. Het volgende codefragment toont hoe de isolatiemodus wordt opgegeven in het manifestbestand van de toepassing.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>Resourcebeheer configureren
[Resourcebeheer](service-fabric-resource-governance.md) beperkt de resources die de container op de host kan gebruiken. Het element `ResourceGovernancePolicy`, dat is opgegeven in het toepassingsmanifest, wordt gebruikt om resourcebeperkingen te declareren voor een servicecodepakket. Er kunnen resourcebeperkingen worden ingesteld voor de volgende resources: geheugen, MemorySwap, CpuShares (relatief CPU-gewicht), MemoryReservationInMB, BlkioWeight (relatief BlockIO-gewicht). In dit voorbeeld krijgt het servicepakket Guest1Pkg één kern op de clusterknooppunten waar het wordt geplaatst. Geheugenlimieten zijn absoluut, dus het codepakket wordt beperkt tot 1024 MB aan geheugen (en een gegarandeerde flexibele reservering hierop). Codepakketten (containers of processen) kunnen niet meer geheugen toewijzen dan deze limiet. Een poging dit toch te doen, leidt tot een Onvoldoende geheugen-uitzondering. Voor een effectieve handhaving van resourcebeperkingen moeten voor alle pakketten binnen een servicepakket geheugenlimieten zijn opgegeven.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Docker-STATUSCONTROLE configureren 

Vanaf versie 6.1 integreert Service Fabric automatisch [Docker-STATUSCONTROLE](https://docs.docker.com/engine/reference/builder/#healthcheck)-gebeurtenissen in het systeemstatusrapport. Dit betekent dat als voor uw container **STATUSCONTROLE** is ingeschakeld, Service Fabric de status van de container rapporteert wanneer Docker aangeeft dat deze is gewijzigd. In [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) wordt de status **OK** weergegeven wanneer *health_status**healthy* is en **WAARSCHUWING** wanneer *health_status**unhealthy* is. 

Vanaf de laatste vernieuwing van v 6.4 hebt u de optie om op te geven dat de status controle-evaluaties van docker moeten worden gerapporteerd als een fout. Als deze optie is ingeschakeld, wordt het status rapport **OK** weer gegeven  wanneer health_status *in orde* is en de **fout** wordt weer gegeven wanneer *health_status* een *slechte status* heeft.

De **status controle** -instructie die verwijst naar de daad werkelijke controle die wordt uitgevoerd voor de controle van de container status, moet aanwezig zijn in de Dockerfile die wordt gebruikt tijdens het genereren van de container installatie kopie.

![Scherm afbeelding toont details van het geïmplementeerde service pakket NodeServicePackage.][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

U kunt het gedrag van de **STATUSCONTROLE** voor elke container configureren door **HealthConfig**-opties op te geven als onderdeel van **ContainerHostPolicies** in ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
*IncludeDockerHealthStatusInSystemHealthReport* is standaard ingesteld op **True**, *RestartContainerOnUnhealthyDockerHealthStatus* is ingesteld op **False** en *TreatContainerUnhealthyStatusAsError* is ingesteld op **False**. 

Als *RestartContainerOnUnhealthyDockerHealthStatus* is ingesteld op **true**, wordt een herhaaldelijk niet goed werkende container opnieuw opgestart (mogelijk op andere knooppunten).

Als *TreatContainerUnhealthyStatusAsError* is ingesteld op **True**, worden **fouten** rapporten weer gegeven wanneer de *health_status* van de container *beschadigd* is.

Als u de integratie van **STATUSCONTROLE** wilt uitschakelen voor de hele Service Fabric-cluster, moet u [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) instellen op **onwaar**.

## <a name="deploy-the-application"></a>De toepassing implementeren
Als de toepassing is gemaakt, kunt u deze met behulp van de Service Fabric-CLI implementeren in het lokale cluster.

Maak verbinding met het lokale cluster van Service Fabric.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Gebruik het installatie script dat is opgenomen in de sjablonen https://github.com/Azure-Samples/service-fabric-containers/ om het toepassings pakket te kopiëren naar de installatie kopie opslag van het cluster, het toepassings type te registreren en een exemplaar van de toepassing te maken.


```bash
./install.sh
```

Open een browser en ga naar Service Fabric Explorer op http: \/ /localhost: 19080/Explorer (Vervang localhost door het privé-IP-adres van de virtuele machine als u Vagrant op Mac OS X gebruikt). Vouw het knooppunt Toepassingen uit. U ziet dat er nu een vermelding is voor uw toepassingstype en nog een voor het eerste exemplaar van dat type.

Maak verbinding met de actieve container. Open een webbrowser die verwijst naar het IP-adres dat is geretourneerd op poort 4000, bijvoorbeeld ' http: \/ /localhost: 4000 '. Als het goed is, ziet u de koptekst Hallo wereld! weergegeven in de browser.

![Hallo wereld!][hello-world]


## <a name="clean-up"></a>Opschonen
Gebruik het uninstall-script dat is opgegeven in de sjabloon om het toepassingsexemplaar te verwijderen uit het lokale ontwikkelomgevingscluster en de registratie van het toepassingstype op te heffen.

```bash
./uninstall.sh
```

Nadat u de installatiekopie naar het containerregister hebt gepusht, kunt u de lokale installatiekopie op de ontwikkelcomputer verwijderen:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Volledig voorbeeld van de manifesten voor de Fabric Service-toepassing en -service
Dit zijn de volledige manifesten voor de service en toepassing die in dit artikel worden gebruikt.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Meer services toevoegen aan een bestaande toepassing

Voer de volgende stappen uit als u nog een containerservice wilt toevoegen aan een toepassing die al is gemaakt met yeoman:

1. Stel de directory in op de hoofdmap van de bestaande toepassing. Bijvoorbeeld `cd ~/YeomanSamples/MyApplication` als `MyApplication` de toepassing is die is gemaakt door Yeoman.
2. `yo azuresfcontainer:AddService` uitvoeren

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Tijdsinterval configureren voor geforceerd beëindigen van container

U kunt een tijdsinterval configureren, zodat de runtime die tijd wacht voordat de container wordt verwijderd nadat het verwijderen van een service (of het verplaatsen naar een ander knooppunt) is gestart. Als u een tijdsinterval configureert, wordt de `docker stop <time in seconds>` opdracht verzonden naar de container.  Zie [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) voor meer informatie. Het tijdsinterval dat moet worden gewacht, kunt u opgeven in de sectie `Hosting`. Het volgende fragment van een clustermanifest laat zien hoe u het wachtinterval instelt:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

Het standaardtijdsinterval is 10 seconden. Aangezien deze configuratie dynamisch is, wordt de time-out bijgewerkt bij een configuratie-upgrade van het cluster. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>De runtime configureren voor het verwijderen van ongebruikte containerinstallatiekopieën

U kunt het Service Fabric-cluster configureren voor het verwijderen van ongebruikte containerinstallatiekopieën van het knooppunt. Met deze configuratie kunt u schijfruimte vrijmaken als er te veel containerinstallatiekopieën aanwezig zijn op het knooppunt. Om deze functie in te schakelen, past u de sectie `Hosting` in het clustermanifest aan zoals wordt weergegeven in het volgende fragment: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
} 
```

De installatiekopieën die niet moeten worden verwijderd, kunt u opgeven met de parameter `ContainerImagesToSkip`. 

## <a name="configure-container-image-download-time"></a>Downloadtijd van containerinstallatiekopie configureren

De Service Fabric-runtime wijst 20 minuten toe om containerinstallatiekopieën te downloaden en uit te pakken. Voor de meeste containerinstallatiekopieën is dat voldoende. Voor grote kopieën, of als de netwerkverbinding langzaam is, kan het echter nodig zijn om de toegewezen tijd te verlengen, zodat het downloaden en uitpakken van de installatiekopie niet voortijdig wordt afgebroken. Deze time-out wordt ingesteld met het kenmerk **ContainerImageDownloadTimeout** in de sectie **Hosting** van het clustermanifest, zoals u in het volgende fragment ziet:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Bewaarbeleid voor containers instellen

Om gemakkelijker opstartfouten bij containers te analyseren, ondersteunt Service Fabric (versie 6.1 of hoger) het bewaren van containers die zijn gestopt of niet kunnen starten. Dit beleid kan worden ingesteld in het bestand **ApplicationManifest.xml**, zoals u in het volgende fragment ziet:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

De instelling **ContainersRetentionCount** geeft aan hoeveel containers er moeten worden bewaard wanneer ze fouten genereren. Als er een negatieve waarde wordt opgegeven, worden alle niet goed werkende containers bewaard. Wanneer er bij het kenmerk **ContainersRetentionCount** niets is opgegeven, worden er geen containers bewaard. Het kenmerk **ContainersRetentionCount** ondersteunt ook toepassingsparameters, zodat gebruikers verschillende waarden kunnen opgeven voor test- en productieclusters. Bij het gebruik van deze functies dient u plaatsingsbeperkingen in te stellen om de containerservice op een bepaald knooppunt te richten. Zo voorkomt u dat de containerservice naar een ander knooppunt wordt verplaatst. Containers die met behulp van deze functie zijn bewaard, moeten handmatig worden verwijderd.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>De Docker-daemon met aangepaste argumenten starten

Met versie 6.2 of hoger van de Service Fabric-runtime kunt u de Docker-daemon met aangepaste argumenten starten. Wanneer er aangepaste argumenten zijn opgegeven, geeft Service Fabric geen andere argumenten aan de docker-engine door, behalve het argument `--pidfile`. Daarom moet `--pidfile` niet als een argument worden doorgegeven. Bovendien moet het argument de docker-daemon nog steeds laten luisteren op de standaard benoemde pipe voor Windows (of unix-domeinsocket voor Linux) zodat Service Fabric met de daemon kan communiceren. De aangepaste argumenten worden opgegeven in het clustermanifest onder de sectie **Hosting** onder **ContainerServiceArguments**. In het volgende fragment ziet u een voorbeeld: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het uitvoeren van [containers in Service Fabric](service-fabric-containers-overview.md).
* Lees de zelfstudie [Een .NET-toepassing implementeren in een container](service-fabric-host-app-in-a-container.md).
* Meer informatie over de [levenscyclus](service-fabric-application-lifecycle.md) van de Service Fabric-toepassing.
* Bekijk [voorbeelden van Service Fabric-containercode op GitHub](https://github.com/Azure-Samples/service-fabric-containers).

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
