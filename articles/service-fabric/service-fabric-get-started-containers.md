---
title: Een Azure Service Fabric-container toepassing maken
description: Maak uw eerste Windows-containertoepassing in Azure Service Fabric. Bouw een docker-installatie kopie met een python-toepassing, push de installatie kopie naar een container register en bouw en implementeer de container vervolgens naar Azure Service Fabric.
ms.topic: conceptual
ms.date: 01/25/2019
ms.custom: devx-track-python
ms.openlocfilehash: 197423670ffe05f15fdc5bfd351efdfba33b53cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96533771"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Uw eerste Service Fabric-containertoepassing maken in Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Er zijn geen wijzigingen in uw toepassing vereist om een bestaande toepassing in een Windows-container uit te voeren in een Service Fabric-cluster. In dit artikel wordt stapsgewijs beschreven hoe u een docker-installatie kopie met een python- [kolf](http://flask.pocoo.org/) -webtoepassing maakt en deze implementeert in een Azure service Fabric-cluster. U gaat uw containertoepassing ook delen via [Azure Container Registry](../container-registry/index.yml). In dit artikel wordt ervan uitgegaan dat u de basisbeginselen kent van Docker. Meer informatie over Docker kunt u lezen in het [Docker-overzicht](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Dit artikel is van toepassing op een Windows-ontwikkel omgeving.  De runtime van Service Fabric cluster en de docker-runtime moeten op hetzelfde besturings systeem worden uitgevoerd.  U kunt geen Windows-containers uitvoeren op een Linux-cluster.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

* Een ontwikkelcomputer waarop wordt uitgevoerd:
  * Visual Studio 2015 of Visual Studio 2019.
  * [Service Fabric SDK en hulpprogramma's](service-fabric-get-started.md).
  *  Docker voor Windows. [Download Docker CE voor Windows (stabiel)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Nadat u Docker hebt geïnstalleerd en gestart, klikt u met de rechtermuisknop op het systeemvakpictogram en selecteert u **Overschakelen naar Windows-containers**. Deze stap is vereist voor het uitvoeren van Docker-installatiekopieën onder Windows.

* Een Windows-cluster met drie of meer knoop punten die worden uitgevoerd op Windows Server met containers. 

  Voor dit artikel moet de versie (build) van Windows Server met containers die op uw cluster knooppunten worden uitgevoerd, overeenkomen met die op uw ontwikkel computer. Dit komt doordat u de docker-installatie kopie op uw ontwikkel machine bouwt en er compatibiliteits beperkingen zijn tussen versies van het container besturingssysteem en het hostbesturingssysteem waarop het is geïmplementeerd. Zie [Windows Server container OS en host OS Compatibility](#windows-server-container-os-and-host-os-compatibility)(Engelstalig) voor meer informatie. 
  
    Als u wilt bepalen welke versie van Windows Server u nodig hebt voor het cluster, voert u de `ver` opdracht uit vanaf een Windows-opdracht prompt op de ontwikkel computer. Raadpleeg de [compatibiliteit met Windows Server container OS en host OS](#windows-server-container-os-and-host-os-compatibility) voordat u [een cluster maakt](service-fabric-cluster-creation-via-portal.md).

* Een register in Azure Container Registry - [Een containerregister maken](../container-registry/container-registry-get-started-portal.md) in uw Azure-abonnement.

> [!NOTE]
> Het implementeren van containers in een Service Fabric cluster dat wordt uitgevoerd op Windows 10 wordt ondersteund.  Raadpleeg [dit artikel](service-fabric-how-to-debug-windows-containers.md) voor informatie over het configureren van Windows 10 om Windows-containers uit te voeren.

> [!NOTE]
> Service Fabric versies 6,2 en hoger ondersteunen het implementeren van containers in clusters die worden uitgevoerd op Windows Server versie 1709.

## <a name="define-the-docker-container"></a>De Docker-container definiëren

Bouw een installatiekopie op basis van de [Python-installatiekopie](https://hub.docker.com/_/python/) die zich in de Docker-hub bevindt.

Geef uw Docker-container in een Dockerfile op. Het bestand Dockerfile bestaat uit instructies voor het instellen van de omgeving in de container, het laden van de toepassing die u wilt uitvoeren en het toewijzen van poorten. Het Dockerfile is de invoer van de opdracht `docker build` waarmee de installatiekopie wordt gemaakt.

Maak een lege map en maak het bestand *Dockerfile* (zonder extensie). Voeg het volgende toe aan het bestand *Dockerfile* en sla de wijzigingen op:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Lees het [Dockerfile-referentiemateriaal](https://docs.docker.com/engine/reference/builder/) voor meer informatie.

## <a name="create-a-basic-web-application"></a>Algemene webtoepassing maken
Maak een Flask-webtoepassing die luistert op poort 80 en `Hello World!` retourneert. Maak in dezelfde map het bestand *requirements.txt*. Voeg het volgende toe en sla de wijzigingen op:
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

<a id="Build-Containers"></a>

## <a name="login-to-docker-and-build-the-image"></a>Meld u aan bij docker en bouw de installatie kopie

Daarna maken we de installatie kopie die uw webtoepassing uitvoert. Bij het ophalen van open bare installatie kopieën van docker (zoals `python:2.7-windowsservercore` in onze Dockerfile), is het een best practice om te verifiëren met uw docker hub-account in plaats van een anonieme pull-aanvraag te doen.

> [!NOTE]
> Wanneer u regel matig anonieme pull-aanvragen maakt, ziet u mogelijk docker-fouten die vergelijkbaar zijn met `ERROR: toomanyrequests: Too Many Requests.` of `You have reached your pull rate limit.` worden geverifieerd bij docker hub om deze fouten te voor komen. Zie [open bare inhoud beheren met Azure container Registry](../container-registry/buffer-gate-public-content.md) voor meer informatie.

Open een PowerShell-venster en navigeer naar de map met het bestand Dockerfile. Voer vervolgens de volgende opdrachten uit:

```
docker login
docker build -t helloworldapp .
```

Met deze opdracht wordt de nieuwe installatiekopie gebouwd met behulp van de instructies in het Dockerfile, en krijgt de installatiekopie de naam (-t tagging) `helloworldapp`. Voor het bouwen van een containerinstallatiekopie, wordt eerst de basisinstallatiekopie gedownload vanaf Docker Hub, waaraan de toepassing wordt toegevoegd. 

Nadat de buildopdracht is voltooid, voert u de opdracht `docker images` uit om de gegevens van de nieuwe installatiekopie te bekijken:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren
Controleer de installatiekopie eerst lokaal voordat u deze naar het containerregister pusht. 

Voer de toepassing uit:

```
docker run -d --name my-web-site helloworldapp
```

Bij *naam* kunt de actieve container een naam geven (in plaats van de container-id).

Zodra de container is gestart, zoekt u het bijbehorende IP-adres zodat u vanuit een browser verbinding kunt maken met de actieve container:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Als deze opdracht niets retourneert, voert u de volgende opdracht uit en inspecteert u het element **NetworkSettings** -> **Networks** voor het IP-adres:
```
docker inspect my-web-site
```

Maak verbinding met de actieve container. Open een webbrowser die verwijst naar het IP-adres dat wordt geretourneerd, bijvoorbeeld ' http: \/ /172.31.194.61 '. Als het goed is, ziet u de koptekst Hallo wereld! weergegeven in de browser.

Als u de container wilt stoppen, voert u dit uit:

```
docker stop my-web-site
```

De container verwijderen van de ontwikkelcomputer:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>De installatiekopie naar het containerregister pushen

Nadat u hebt gecontroleerd of de container actief is op de ontwikkelcomputer, pusht u de installatiekopie naar het register in Azure Container Registry.

Voer uit ``docker login`` om u aan te melden bij uw container register met uw [register referenties](../container-registry/container-registry-authentication.md).

In het volgende voorbeeld worden de id en het wachtwoord van een [service-principal](../active-directory/develop/app-objects-and-service-principals.md) van Azure Active Directory doorgegeven. U hebt bijvoorbeeld een service-principal aan uw register toegewezen voor een automatiseringsscenario. U kunt zich ook aanmelden met uw gebruikers naam en wacht woord voor het REGI ster.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Met de volgende opdracht maakt u een tag (of alias) van de installatiekopie met een volledig gekwalificeerd pad naar uw register. In dit voorbeeld wordt de installatiekopie in de naamruimte ```samples``` geplaatst om overbodige items in de hoofdmap van het register te voorkomen.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

De installatiekopie naar het containerregister pushen:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>De beperkte service maken in Visual Studio
De Service Fabric SDK en hulpprogramma's bieden een servicesjabloon waarmee u een containertoepassing kunt maken.

1. Start Visual Studio. Selecteer **Bestand** > **Nieuw** > **Project**.
2. Selecteer **Service Fabric-toepassing**, geef deze de naam MyFirstContainer en klik op **OK**.
3. Selecteer **Container** in de lijst met **servicesjablonen**.
4. Voer bij **Naam van installatiekopie** het volgende in: myregistry.azurecr.io/samples/helloworldapp. Dit is de installatiekopie die u naar uw containeropslagplaats hebt gepusht.
5. Geef de service een naam en klik op **OK**.

## <a name="configure-communication"></a>Communicatie configureren
De containerservice heeft een eindpunt voor communicatie nodig. Voeg een `Endpoint`-element met het protocol, de poort en het type toe aan het bestand ServiceManifest.xml. In dit voorbeeld wordt een ingestelde poort 8081 gebruikt. Als er geen poort is opgegeven, wordt een willekeurige poort uit het poortbereik van de toepassing gekozen. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Aanvullende eind punten voor een service kunnen worden toegevoegd door extra eindpunt elementen te declareren met toepasselijke eigenschaps waarden. Elke poort kan slechts één protocol waarde declareren.

Als u een eindpunt opgeeft, publiceert Service Fabric het eindpunt naar de Naming-service. Andere services die in dit cluster worden uitgevoerd, kunnen deze container dan omzetten. U kunt ook communicatie van container naar container laten plaatsvinden met behulp van een [omgekeerde proxy](service-fabric-reverseproxy.md). Communicatie wordt uitgevoerd door de omgekeerde proxy de HTTP-poort voor luisteren en de naam van de services waarmee u wilt communiceren door te geven als omgevingsvariabelen.

De service luistert op een specifieke poort (8081 in dit voorbeeld). Wanneer de toepassing in een cluster in Azure wordt geïmplementeerd, worden zowel het cluster als de toepassing achter een load balancer van Azure uitgevoerd. De poort van de toepassing moet open zijn in de load balancer van Azure zodat binnenkomend verkeer de service kan bereiken.  U kunt deze poort openen in de load balancer van Azure met een [PowerShell-script](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) of in [Azure Portal](https://portal.azure.com).

## <a name="configure-and-set-environment-variables"></a>Omgevingsvariabelen configureren en instellen
Er kunnen omgevingsvariabelen worden opgegeven voor ieder codepakket in het servicemanifest. Deze functie is beschikbaar voor alle services, ongeacht of ze zijn geïmplementeerd als containers, processen of uitvoerbare gastbestanden. U kunt waarden van omgevingsvariabelen overschrijven in het toepassingsmanifest of ze opgeven als toepassingsparameters tijdens de implementatie.

Het volgende XML-fragment voor het servicemanifest toont een voorbeeld van het opgeven van omgevingsvariabelen voor een codepakket:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Deze omgevingsvariabelen kunnen worden overschreven in het toepassingsmanifest:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Poort-naar-host-toewijzing voor containers en container-naar-container-detectie configureren
Configureer een hostpoort voor communicatie met de container. De poortbinding wijst de poort toe waarop de service binnen de container luistert naar een poort op de host. Voeg een element `PortBinding` toe aan het element `ContainerHostPolicies` van het bestand ApplicationManifest.xml. Voor dit artikel geldt: `ContainerPort` is 80 (de container gebruikt poort 80, zoals opgegeven in het bestand Dockerfile) en `EndpointRef` is 'Guest1TypeEndpoint' (het eindpunt dat eerder is gedefinieerd in het servicemanifest). Binnenkomende aanvragen naar de service op poort 8081 worden toegewezen aan poort 80 in de container.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> Aanvullende PortBindings voor een service kunnen worden toegevoegd door extra PortBinding-elementen te declareren met toepasselijke eigenschaps waarden.

## <a name="configure-container-repository-authentication"></a>Verificatie van container opslagplaats configureren

Bekijk [container opslagplaats verificatie](configure-container-repository-credentials.md)voor meer informatie over het configureren van verschillende verificatie typen voor het downloaden van container installatie kopieën.

## <a name="configure-isolation-mode"></a>Isolatiemodus configureren
Windows ondersteunt twee isolatiemodi voor containers: proces en Hyper-V. Met de procesisolatiemodus delen alle containers die worden uitgevoerd op dezelfde hostcomputer de kernel met de host. Met de Hyper-V-isolatiemodus hebben de kernels een scheiding tussen elke Hyper-V-container en de containerhost. De isolatiemodus is in het manifestbestand van de toepassing opgegeven in het element `ContainerHostPolicies`. De isolatiemodi die kunnen worden opgegeven zijn `process`, `hyperv` en `default`. De standaard instelling is proces isolatie modus op Windows Server-hosts. Op Windows 10-hosts wordt alleen de isolatie modus voor Hyper-V ondersteund, zodat de container wordt uitgevoerd in de isolatie modus voor Hyper-V, ongeacht de instelling van de isolatie modus. Het volgende codefragment toont hoe de isolatiemodus wordt opgegeven in het manifestbestand van de toepassing.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > De hyperv-isolatiemodus is beschikbaar in de Azure-SKU's Ev3 en Dv3 aangezien die ondersteuning voor geneste virtualisatie bieden. 
   >
   >

## <a name="configure-resource-governance"></a>Resourcebeheer configureren
[Resourcebeheer](service-fabric-resource-governance.md) beperkt de resources die de container op de host kan gebruiken. Het element `ResourceGovernancePolicy`, dat is opgegeven in het toepassingsmanifest, wordt gebruikt om resourcebeperkingen te declareren voor een servicecodepakket. Er kunnen resourcebeperkingen worden ingesteld voor de volgende resources: geheugen, MemorySwap, CpuShares (relatief CPU-gewicht), MemoryReservationInMB, BlkioWeight (relatief BlockIO-gewicht). In dit voorbeeld krijgt het servicepakket Guest1Pkg één kern op de clusterknooppunten waar het wordt geplaatst. Geheugenlimieten zijn absoluut, dus het codepakket wordt beperkt tot 1024 MB aan geheugen (en een gegarandeerde flexibele reservering hierop). Codepakketten (containers of processen) kunnen niet meer geheugen toewijzen dan deze limiet. Een poging dit toch te doen, leidt tot een Onvoldoende geheugen-uitzondering. Voor een effectieve handhaving van resourcebeperkingen moeten voor alle pakketten binnen een servicepakket geheugenlimieten zijn opgegeven.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
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

![Scherm afbeelding toont details van het geïmplementeerde service pakket NodeServicePackage.][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

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

## <a name="deploy-the-container-application"></a>De containertoepassing implementeren
Sla al uw wijzigingen op en bouw de toepassing. Klik in Solution Explorer met de rechtermuisknop op **MyFirstContainer** en selecteer **Publish** om uw toepassing te publiceren.

Voer bij **Verbindingseindpunt** het beheereindpunt voor het cluster in. Bijvoorbeeld `containercluster.westus2.cloudapp.azure.com:19000`. U vindt het eindpunt voor de clientverbinding op het tabblad Overzicht voor het cluster in [Azure Portal](https://portal.azure.com).

Klik op **Publish**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) is een webhulpprogramma voor het inspecteren en beheren van toepassingen en knooppunten in een Service Fabric-cluster. Open een browser, ga naar `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/` en volg de implementatie van de toepassing. De toepassing wordt geïmplementeerd, maar heeft een foutstatus totdat de installatiekopie is gedownload op de clusterknooppunten (wat enige tijd kan duren, afhankelijk van de grootte van de installatiekopie): ![Fout][1]

De toepassing is gereed bij een ```Ready```-status: ![Gereed][2]

Open een browser en ga naar `http://containercluster.westus2.cloudapp.azure.com:8081`. Als het goed is, ziet u de koptekst Hallo wereld! weergegeven in de browser.

## <a name="clean-up"></a>Opschonen

Zolang het cluster actief is, worden er kosten in rekening gebracht. Overweeg daarom [het cluster te verwijderen](./service-fabric-tutorial-delete-cluster.md).

Nadat u de installatiekopie naar het containerregister hebt gepusht, kunt u de lokale installatiekopie op de ontwikkelcomputer verwijderen:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Compatibiliteit met Windows Server container OS en host OS

Windows Server-containers zijn niet compatibel in alle versies van een host-besturings systeem. Bijvoorbeeld:
 
- Windows Server-containers die zijn gemaakt met Windows Server versie 1709 werken niet op een host met Windows Server versie 2016. 
- Windows Server-containers die zijn gemaakt met Windows Server 2016 werken alleen in de isolatie modus van Hyper-V op een host met Windows Server versie 1709. 
- Met Windows Server-containers die zijn gebouwd met behulp van Windows Server 2016, kan het nodig zijn om ervoor te zorgen dat de revisie van het container besturingssysteem en het hostbesturingssysteem hetzelfde zijn als in de isolatie modus voor processen wordt uitgevoerd op een host met Windows Server 2016.
 
Zie compatibiliteit met Windows- [container versie](/virtualization/windowscontainers/deploy-containers/version-compatibility)voor meer informatie.

Denk na over de compatibiliteit van het hostbesturingssysteem en het container besturingssysteem wanneer u containers bouwt en implementeert op uw Service Fabric-cluster. Bijvoorbeeld:

- Zorg ervoor dat u containers implementeert met een besturings systeem dat compatibel is met het besturings systeem op uw cluster knooppunten.
- Zorg ervoor dat de isolatie modus die is opgegeven voor de container-app consistent is met de ondersteuning voor het container besturingssysteem op het knoop punt waar het wordt geïmplementeerd.
- Bedenk hoe upgrades van besturings systemen naar uw cluster knooppunten of containers van invloed kunnen zijn op de compatibiliteit. 

We raden u aan de volgende procedures uit te voeren om ervoor te zorgen dat containers correct worden geïmplementeerd op uw Service Fabric cluster:

- Gebruik expliciete afbeeldings labels met uw docker-installatie kopieën om de versie van Windows Server-besturings systeem op te geven waaruit een container is opgebouwd. 
- Gebruik [labels voor het besturings systeem](#specify-os-build-specific-container-images) in het manifest bestand van de toepassing om er zeker van te zijn dat uw toepassing compatibel is met verschillende Windows Server-versies en-upgrades.

> [!NOTE]
> Met Service Fabric versie 6,2 en hoger kunt u containers op basis van Windows Server 2016 lokaal op een Windows 10-host implementeren. In Windows 10 worden containers uitgevoerd in de isolatie modus van Hyper-V, ongeacht de isolatie modus die in het toepassings manifest is ingesteld. Zie [isolatie modus configureren](#configure-isolation-mode)voor meer informatie.   
>
 
## <a name="specify-os-build-specific-container-images"></a>Containerinstallatiekopieën opgeven die specifiek zijn voor de build van het besturingssysteem 

Windows Server-containers zijn mogelijk niet compatibel in verschillende versies van het besturings systeem. Windows Server-containers die zijn gemaakt met Windows Server 2016, werken bijvoorbeeld niet in Windows Server versie 1709 in de isolatie modus voor processen. Als cluster knooppunten worden bijgewerkt naar de nieuwste versie, kunnen container Services die zijn gemaakt met de eerdere versies van het besturings systeem, mislukken. Om dit te omzeilen met versie 6,1 van de runtime en nieuwer, biedt Service Fabric ondersteuning voor het opgeven van meerdere installatie kopieën van besturings systemen per container en het coderen van deze met de build-versies van het besturings systeem in het toepassings manifest. U kunt de build-versie van het besturings systeem verkrijgen door uit te voeren `winver` vanaf een Windows-opdracht prompt. Werk eerst per besturingssysteemversie de toepassingsmanifesten bij en geef het gebruik van specifieke installatiekopieën op, en werk daarna het besturingssysteem op de knooppunten bij. Het volgende fragment toont hoe u meerdere containerinstallatiekopieën opgeeft in het toepassingsmanifest **ApplicationManifest.xml**:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
De build-versie voor Windows Server 2016 is 14393, voor Windows Server versie 1709 is dat 16299. Het servicemanifest blijft slechts één installatiekopie per containerservice opgeven, zoals u hier ziet:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > De tagfunctionaliteit voor de build-versie van het besturingssysteem is alleen beschikbaar voor Service Fabric in Windows
   >

Als het onderliggende besturingssysteem op de VM build 16299 (versie 1709) is, kiest Service Fabric de containerinstallatiekopie die overeenkomt met die versie van Windows Server. Als in het toepassingsmanifest een containerinstallatiekopie zonder tag wordt opgegeven samen met een containerinstallatiekopie met tag, behandelt Service Fabric de installatiekopie zonder tag als installatiekopie die in meerdere versies werkt. Voorzie de containerinstallatiekopieën expliciet van tags om problemen tijdens de upgraden te voorkomen.

De containerinstallatiekopie zonder tag werkt als overschrijving van de installatiekopie in het bestand ServiceManifest.xml. Met de installatiekopie "myregistry.azurecr.io/samples/helloworldappDefault" wordt dus ImageName "myregistry.azurecr.io/samples/helloworldapp" in het bestand ServiceManifest.xml overschreven.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Volledig voorbeeld van de manifesten voor de Fabric Service-toepassing en -service
Dit zijn de volledige manifesten voor de service en toepassing die in dit artikel worden gebruikt.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Tijdsinterval configureren voor geforceerd beëindigen van container

U kunt een tijdsinterval configureren, zodat de runtime die tijd wacht voordat de container wordt verwijderd nadat het verwijderen van een service (of het verplaatsen naar een ander knooppunt) is gestart. Als u een tijdsinterval configureert, wordt de `docker stop <time in seconds>` opdracht verzonden naar de container.  Zie [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) voor meer informatie. Het tijdsinterval dat moet worden gewacht, kunt u opgeven in de sectie `Hosting`. De `Hosting` sectie kan worden toegevoegd tijdens het maken van een cluster of later in een configuratie-upgrade. Het volgende fragment van een clustermanifest laat zien hoe u het wachtinterval instelt:

```json
"fabricSettings": [
    ...,
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
]
```
Het standaardtijdsinterval is 10 seconden. Aangezien deze configuratie dynamisch is, wordt de time-out bijgewerkt bij een configuratie-upgrade van het cluster. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>De runtime configureren voor het verwijderen van ongebruikte containerinstallatiekopieën

U kunt het Service Fabric-cluster configureren voor het verwijderen van ongebruikte containerinstallatiekopieën van het knooppunt. Met deze configuratie kunt u schijfruimte vrijmaken als er te veel containerinstallatiekopieën aanwezig zijn op het knooppunt. Als u deze functie wilt inschakelen, werkt u het gedeelte [Hosting](service-fabric-cluster-fabric-settings.md#hosting) in het cluster manifest bij, zoals wordt weer gegeven in het volgende code fragment: 


```json
"fabricSettings": [
    ...,
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
]
```

De installatiekopieën die niet moeten worden verwijderd, kunt u opgeven met de parameter `ContainerImagesToSkip`.  


## <a name="configure-container-image-download-time"></a>Downloadtijd van containerinstallatiekopie configureren

De Service Fabric-runtime wijst 20 minuten toe om containerinstallatiekopieën te downloaden en uit te pakken. Voor de meeste containerinstallatiekopieën is dat voldoende. Voor grote kopieën, of als de netwerkverbinding langzaam is, kan het echter nodig zijn om de toegewezen tijd te verlengen, zodat het downloaden en uitpakken van de installatiekopie niet voortijdig wordt afgebroken. Deze time-out wordt ingesteld met het kenmerk **ContainerImageDownloadTimeout** in de sectie **Hosting** van het clustermanifest, zoals u in het volgende fragment ziet:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Bewaarbeleid voor containers instellen

Om gemakkelijker opstartfouten bij containers te analyseren, ondersteunt Service Fabric (versie 6.1 of hoger) het bewaren van containers die zijn gestopt of niet kunnen opstartten. Dit beleid kan worden ingesteld in het bestand **ApplicationManifest.xml**, zoals u in het volgende fragment ziet:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

De instelling **ContainersRetentionCount** geeft aan hoeveel containers er moeten worden bewaard wanneer ze fouten genereren. Als er een negatieve waarde wordt opgegeven, worden alle niet goed werkende containers bewaard. Wanneer er bij het kenmerk **ContainersRetentionCount** niets is opgegeven, worden er geen containers bewaard. Het kenmerk **ContainersRetentionCount** ondersteunt ook toepassingsparameters, zodat gebruikers verschillende waarden kunnen opgeven voor test- en productieclusters. Bij het gebruik van deze functies dient u plaatsingsbeperkingen in te stellen om de containerservice op een bepaald knooppunt te richten. Zo voorkomt u dat de containerservice naar een ander knooppunt wordt verplaatst. Containers die met behulp van deze functie zijn bewaard, moeten handmatig worden verwijderd.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>De Docker-daemon met aangepaste argumenten starten

Met versie 6.2 of hoger van de Service Fabric-runtime kunt u de Docker-daemon met aangepaste argumenten starten. Wanneer er aangepaste argumenten zijn opgegeven, geeft Service Fabric geen andere argumenten aan de docker-engine door, behalve het argument `--pidfile`. Daarom moet `--pidfile` niet als een argument worden doorgegeven. Bovendien moet het argument de docker-daemon nog steeds laten luisteren op de standaard benoemde pipe voor Windows (of unix-domeinsocket voor Linux) zodat Service Fabric met de daemon kan communiceren. De aangepaste argumenten worden doorgegeven in het clustermanifest onder de sectie **Hosting** onder **ContainerServiceArguments**, zoals weergegeven in het volgende fragment: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het uitvoeren van [containers in Service Fabric](service-fabric-containers-overview.md).
* Lees de zelfstudie [Een .NET-toepassing implementeren in een container](service-fabric-host-app-in-a-container.md).
* Meer informatie over de [levenscyclus](service-fabric-application-lifecycle.md) van de Service Fabric-toepassing.
* Bekijk [voorbeelden van Service Fabric-containercode op GitHub](https://github.com/Azure-Samples/service-fabric-containers).

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
