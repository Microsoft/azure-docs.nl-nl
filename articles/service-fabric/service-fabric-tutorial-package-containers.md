---
title: Containers verpakken en implementeren
description: In deze zelfstudie leert u hoe u een Azure Service Fabric-toepassingsdefinitie kunt genereren met behulp van Yeoman en de toepassing verpakken.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 995291a783d14a6d2db8ed8319c720f55c009d91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92738864"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application-using-yeoman"></a>Zelfstudie: Containers verpakken en implementeren als een Service Fabric-toepassing met behulp van Yeoman

Deze zelfstudie is deel twee van een serie. In deze zelfstudie wordt een hulpprogramma voor het genereren van sjablonen (Yeoman) gebruikt om een Service Fabric-toepassingsdefinitie te genereren. Deze toepassing kan vervolgens worden gebruikt voor het implementeren van containers naar Service Fabric. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Yeoman installeren
> * Een toepassingspakket maken met behulp van Yeoman
> * Instellingen configureren in het toepassingspakket voor gebruik met containers
> * De toepassing bouwen
> * De toepassing implementeren en uitvoeren
> * De toepassing opschonen

## <a name="prerequisites"></a>Vereisten

* Er wordt gebruikgemaakt van de naar het Azure Container Registry gepushte containerinstallatiekopieën die in [Deel 1](service-fabric-tutorial-create-container-images.md) van deze zelfstudiereeks zijn gemaakt.
* De Linux-ontwikkelomgeving moet zijn [ingesteld](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Yeoman installeren

Service Fabric biedt hulpprogramma's waarmee u vanuit de terminal toepassingen kunt maken met behulp van de Yeoman-sjabloongenerator. Volg de stappen hieronder om ervoor te zorgen dat u de Yeoman-sjabloongenerator hebt.

1. Installeer nodejs en NPM op uw computer. Houd er rekening mee dat Mac OSX-gebruikers het pakketbeheerprogramma Homebrew moeten gebruiken

    ```bash
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
    nvm install node 
    ```
2. De Yeoman-sjabloongenerator op uw computer installeren vanuit NPM

    ```bash
    npm install -g yo
    ```
3. De Yeoman-containergenerator van Service Fabric installeren

    ```bash 
    npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Een Docker-containerinstallatiekopie verpakken met Yeoman

1. Voer de volgende opdracht uit in de directory 'container-tutorial' van de gekloonde opslagplaats om een Service Fabric-containertoepassing te maken.

    ```bash
    yo azuresfcontainer
    ```
2. Typ 'TestContainer' als naam voor uw toepassing
3. Typ 'azurevotefront' als naam voor uw toepassingsservice.
4. Geef het pad van de containerinstallatiekopie in ACR op voor de front-endopslagplaats, bijvoorbeeld \<acrName>.azurecr.io/azure-vote-front:v1. Het veld \<acrName> moet gelijk zijn aan de waarde die is gebruikt in de vorige zelfstudie.
5. Druk op Enter om de sectie Opdrachten leeg te laten.
6. Geef 1 exemplaar op.

Hieronder ziet u de invoer en uitvoer van de yo-opdracht:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Voer de volgende stappen uit als u nog een containerservice wilt toevoegen aan een toepassing die al is gemaakt met Yeoman:

1. Wijzig de directory naar een niveau hoger dan **TestContainer**, bijvoorbeeld *./TestContainer*
2. Voer `yo azuresfcontainer:AddService` uit.
3. Noem de service 'azurevoteback'
4. Geef het pad van de containerinstallatiekopie voor Redis op: 'alpine:redis'
5. Druk op Enter om de sectie Opdrachten leeg te laten
6. Geef '1' exemplaar op.

Alle items voor het toevoegen van de gebruikte service worden getoond:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

In de rest van deze zelfstudie werken we in de directory **TestContainer**. Bijvoorbeeld: *./TestContainer/TestContainer*. De inhoud van deze directory moet de volgende zijn.

```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Het toepassingsmanifest configureren met referenties voor Azure Container Registry

Opdat Service Fabric de containerinstallatiekopieën kan ophalen uit de Azure Container Registry, moeten we de referenties opgeven in **ApplicationManifest.xml**.

Meld u aan bij uw ACR-exemplaar. Gebruik de opdracht **az acr login** om de bewerking te voltooien. Geef de unieke naam op die u het containerregister hebt gegeven toen u het maakte.

```azurecli
az acr login --name <acrName>
```

De opdracht retourneert het bericht **Aanmelden geslaagd** wanneer deze is uitgevoerd.

Voer vervolgens de volgende opdracht uit om het wachtwoord van uw containerregister op te halen. Dit wachtwoord wordt door Service Fabric gebruikt voor authenticatie bij ACR om de containerinstallatiekopieën op te halen.

```azurecli
az acr credential show -n <acrName> --query passwords[0].value
```

Voeg in **ApplicationManifest.xml** het codefragment toe onder het element **ServiceManifestImport** voor de front-endservice. Voeg uw **acrName** in voor het veld **AccountName** en het wachtwoord dat met de vorige opdracht is geretourneerd wordt gebruikt voor het veld **Wachtwoord**. Aan het einde van dit document vindt u een volledig **ApplicationManifest.xml**-bestand.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Communicatie en poort-naar-host poorttoewijzing van de container configureren

### <a name="configure-communication-port"></a>Communicatiepoort configureren

Een HTTP-eindpunt configureren zodat clients met uw service kunnen communiceren. Open het bestand *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* en declareer een eindpuntresource in het element **ServiceManifest**.  Voeg het protocol, de poort en de naam toe. In deze zelfstudie luistert de service op poort 80. Het volgende fragment wordt onder de *ServiceManifest*-tag in de resource geplaatst.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```

Wijzig het Service Manifest voor de back-endservice op dezelfde manier. Open het bestand *./TestContainer/azurevotebackPkg/ServiceManifest.xml* en declareer een eindpuntresource in het element **ServiceManifest**. Voor deze zelfstudie wordt de Redis-standaardwaarde van 6379 aangehouden. Het volgende fragment wordt onder de *ServiceManifest*-tag in de resource geplaatst.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Door het **UriScheme** op te geven wordt het eindpunt van de container automatisch geregistreerd bij de Service Fabric Naming-service voor de zichtbaarheid. Aan het einde van dit artikel wordt een volledig ServiceManifest.xml-bestand voor de back-endservice gegeven als voorbeeld.

### <a name="map-container-ports-to-a-service"></a>Containerpoorten toewijzen aan een service

Om de containers in het cluster beschikbaar te maken, moeten we ook een poortbinding maken in ApplicationManifest.xml. Het **PortBinding**-beleid verwijst naar de **Endpoints** die we hebben gedefinieerd in de **ServiceManifest.xml**-bestanden. Binnenkomende aanvragen voor deze eindpunten worden toegewezen aan de containerpoorten die hier worden geopend en gebonden. Voeg in het bestand **ApplicationManifest.xml** de volgende code toe om poort 80 en 6379 aan de eindpunten te binden. Aan het einde van dit document is een volledig **ApplicationManifest.xml**-bestand beschikbaar.

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Een DNS-naam toevoegen aan de back-endservice

Opdat Service Fabric deze DNS-naam kan toewijzen aan de back-endservice, moet de naam worden opgegeven in **ApplicationManifest.xml**. Voeg het kenmerk **ServiceDnsName** als volgt toe aan het **Service**-element:

```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

De frontend-service leest een omgevingsvariabele om achter de DNS-naam van het Redis-exemplaar te komen. Deze omgevingsvariabele is al gedefinieerd in de Dockerfile die is gebruikt om de Docker-installatiekopie te genereren, en er hoeft hier geen actie te worden ondernomen.

```dockerfile
ENV REDIS redisbackend.testapp
```

Het volgende codefragment laat zien hoe de front-end-Python-code de in de Dockerfile beschreven omgevingsvariabele opneemt. Er hoeft hier geen actie te worden ondernomen.

```python
# Get DNS Name
redis_server = os.environ['REDIS']

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Op dit punt in de zelfstudie is de sjabloon voor een Service Package-toepassing beschikbaar voor implementatie naar een cluster. In de volgende zelfstudie wordt deze toepassing geïmplementeerd en uitgevoerd in een Service Fabric-cluster.

## <a name="create-a-service-fabric-cluster"></a>Een Service Fabric-cluster maken

Voor het implementeren van de toepassing in Azure hebt u een Service Fabric-cluster nodig om de toepassing uit te voeren. Met de volgende opdrachten wordt een cluster met vijf knooppunten in Azure gemaakt.  Met de opdrachten wordt ook een zelfondertekend certificaat gemaakt, dat wordt toegevoegd aan een sleutelkluis en waarmee het certificaat lokaal wordt gedownload als PEM-bestand. Het nieuwe certificaat wordt gebruikt om het cluster te beveiligen wanneer het wordt geïmplementeerd en wordt tevens gebruikt om clients te verifiëren.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  
# The certificate is downloaded locally as a PEM file.
az sf cluster create --resource-group $ResourceGroupName --location $Location \ 
--certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \ 
--cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName \ 
--vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> De web-front-endservice is geconfigureerd om naar binnenkomend verkeer te luisteren op poort 80. Standaard is poort 80 geopend in uw cluster-VM's en de Azure Load Balancer.
>

Zie [Een Service Fabric-cluster maken op Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) voor meer informatie over het maken van uw eigen cluster.

## <a name="build-and-deploy-the-application-to-the-cluster"></a>De toepassing bouwen en implementeren in het cluster

U kunt de toepassing implementeren in het Azure-cluster met behulp van de Service Fabric CLI. Als de Service Fabric CLI niet op uw computer is geïnstalleerd, volgt u de instructies [hier](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) om deze te installeren.

Maak verbinding met het Service Fabric-cluster in Azure. Vervang het voorbeeld van een eindpunt door uw eigen eindpunt. Het eindpunt moet een volledige URL zijn die lijkt op de onderstaande.  Het PEM-bestand is het zelfondertekende certificaat dat eerder is gemaakt.

```bash
sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
```

Gebruik het installatiescript uit de **TestContainer**-directory om het toepassingspakket te kopiëren naar de installatiekopieopslag van het cluster, het toepassingstype te registreren en een exemplaar van de toepassing te maken.

```bash
./install.sh
```

Open een browser en ga naar Service Fabric Explorer op http:\//containertestcluster.eastus.cloudapp.azure.com:19080/Explorer. Vouw het knooppunt Toepassingen uit. U ziet dat er een vermelding is voor uw toepassingstype en nog een voor het exemplaar.

![Service Fabric Explorer][sfx]

U maakt verbinding met de actieve toepassing door een webbrowser te openen en naar de cluster-URL te gaan: bijvoorbeeld http:\//containertestcluster.eastus.cloudapp.azure.com:80. U ziet nu de stemtoepassing in de webgebruikersinterface.

![De schermopname toont de Azure stem-app met knoppen voor katten, honden en resetten en totalen.][votingapp]

## <a name="clean-up"></a>Opschonen

Gebruik het uninstall-script dat is opgegeven in de sjabloon om het toepassingsexemplaar te verwijderen uit het cluster en de registratie van het toepassingstype op te heffen. Het duurt enige tijd om het exemplaar te wissen met deze opdracht, en de opdracht ‘install.sh’ mag niet onmiddellijk na dit script worden uitgevoerd.

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Voorbeelden van voltooide manifesten

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Front-end ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn meerdere containers verpakt in een Service Fabric-toepassing met behulp van Yeoman. Deze toepassing is vervolgens geïmplementeerd en uitgevoerd op een Service Fabric-cluster. De volgende stappen zijn voltooid:

> [!div class="checklist"]
> * Yeoman installeren
> * Een toepassingspakket maken met behulp van Yeoman
> * Instellingen configureren in het toepassingspakket voor gebruik met containers
> * De toepassing bouwen
> * De toepassing implementeren en uitvoeren
> * De toepassing opschonen

Ga naar de volgende zelfstudie voor meer informatie over failover en schalen van de toepassing in Service Fabric.

> [!div class="nextstepaction"]
> [Meer informatie over failover en schalen van toepassingen](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png
