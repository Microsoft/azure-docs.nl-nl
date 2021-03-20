---
title: Azure Files volume stuur programma voor Service Fabric
description: Service Fabric ondersteunt het gebruik van Azure Files voor het maken van back-upvolumes in de container.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247690"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Azure Files volume stuur programma voor Service Fabric

Het Azure Files-volume stuur programma is een [docker volume-invoeg toepassing](https://docs.docker.com/engine/extend/plugins_volume/) die op [Azure files](../storage/files/storage-files-introduction.md) gebaseerde volumes biedt voor docker-containers. Het is verpakt als een Service Fabric toepassing die kan worden geïmplementeerd in een Service Fabric cluster om volumes te bieden voor andere Service Fabric container toepassingen in het cluster.

> [!NOTE]
> Versie 6.5.661.9590 van de invoeg toepassing voor het Azure Files-volume is uitgebracht voor algemene Beschik baarheid.
>

## <a name="prerequisites"></a>Vereisten
* De Windows-versie van de invoeg toepassing volume Azure Files werkt alleen op [Windows Server versie 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [windows 10 versie 1709](/windows/whats-new/whats-new-windows-10-version-1709) of hoger.

* De Linux-versie van de invoeg toepassing volume van Azure Files werkt op alle versies van het besturings systeem die door Service Fabric worden ondersteund.

* De invoeg toepassing Azure Files volume werkt alleen op Service Fabric versie 6,2 en hoger.

* Volg de instructies in de [Azure files-documentatie](../storage/files/storage-how-to-create-file-share.md) om een bestands share te maken voor de service Fabric container toepassing die als volume moet worden gebruikt.

* U hebt [Power shell nodig met de service Fabric-module](./service-fabric-get-started.md) of [SFCTL](./service-fabric-cli.md) geïnstalleerd.

* Als u Hyper-V-containers gebruikt, moeten de volgende fragmenten worden toegevoegd aan de ClusterManifest (lokale cluster) of fabricSettings in uw Azure Resource Manager-sjabloon (Azure cluster) of ClusterConfig.json (zelfstandige cluster).

In de ClusterManifest moet de volgende worden toegevoegd in de sectie hosting. In dit voor beeld is de volume naam **sfazurefile** en wordt de poort die wordt geluisterd naar op het cluster **19100**. Vervang deze door de juiste waarden voor uw cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

In de sectie fabricSettings in uw Azure Resource Manager-sjabloon (voor Azure-implementaties) of ClusterConfig.js(voor zelfstandige implementaties) moet het volgende code fragment worden toegevoegd. En vervang opnieuw de volume naam en poort waarden met uw eigen waarde.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Een voorbeeld toepassing implementeren met behulp van Service Fabric Azure Files volume stuur programma

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Azure Resource Manager via het meegeleverde Power shell-script gebruiken (aanbevolen)

Als uw cluster is gebaseerd op Azure, raden we u aan om toepassingen te implementeren met behulp van het resource model van de Azure Resource Manager-toepassing voor gebruiks gemak en om te helpen bij het model van het onderhoud van de infra structuur als code. Deze aanpak elimineert de nood zaak om de App-versie bij te houden voor het Azure Files-volume stuur programma. U kunt hiermee ook afzonderlijke Azure Resource Manager sjablonen onderhouden voor elk ondersteund besturings systeem. In het script wordt ervan uitgegaan dat u de nieuwste versie van de Azure Files-toepassing implementeert en para meters gebruikt voor het type besturings systeem, de cluster abonnement-ID en de resource groep. U kunt het script downloaden van de [service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Houd er rekening mee dat hiermee automatisch de ListenPort wordt ingesteld. Dit is de poort waarop de Azure Files-volume-invoeg toepassing luistert naar aanvragen van de docker-daemon tot 19100. U kunt dit wijzigen door een para meter met de naam ' listenPort ' toe te voegen. Zorg ervoor dat de poort niet in conflict is met een andere poort die door het cluster of uw toepassingen wordt gebruikt.
 

Azure Resource Manager implementatie opdracht voor Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure Resource Manager implementatie opdracht voor Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Zodra u het script hebt uitgevoerd, kunt u door gaan naar de [sectie uw toepassing configureren.](#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Hand matige implementatie voor zelfstandige clusters

De Service Fabric-toepassing die de volumes voor uw containers levert, kan worden gedownload van de [service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). De toepassing kan worden geïmplementeerd in het cluster via [Power shell](./service-fabric-deploy-remove-applications.md), [cli](./service-fabric-application-lifecycle-sfctl.md) of [FabricClient api's](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Gebruik de opdracht regel om de map te wijzigen in de hoofdmap van het gedownloade toepassings pakket.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Kopieer vervolgens het toepassings pakket naar het archief met installatie kopieën met de juiste waarden voor [ApplicationPackagePath] en [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Het toepassings type registreren

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Maak de toepassing en betaal de waarde voor de **ListenPort** -toepassings parameter. Deze waarde is de poort waarop de Azure Files-invoeg toepassing van het volume luistert naar aanvragen van de docker-daemon. Zorg ervoor dat de poort die aan de toepassing is gegeven, overeenkomt met de VolumePluginPorts in de ClusterManifest en niet in strijd is met een andere poort die door het cluster of uw toepassingen wordt gebruikt.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Data Center biedt geen ondersteuning voor het toewijzen van SMB-koppelingen aan containers ([dat wordt alleen ondersteund op Windows Server versie 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Deze beperking voor komt dat netwerk volume toewijzing en Azure Files volume Stuur Programma's op oudere versies dan 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>De toepassing implementeren op een lokaal ontwikkelings cluster
Volg de stappen 1-3 van de [bovenstaande.](#manual-deployment-for-standalone-clusters)

 Het standaard aantal service-exemplaren voor de Azure Files volume plugin-toepassing is-1, wat betekent dat er een exemplaar van de service op elk knoop punt in het cluster is geïmplementeerd. Bij het implementeren van de Azure Files volume plugin-toepassing op een lokaal ontwikkel cluster, moet het aantal service-exemplaren echter worden opgegeven als 1. Dit kan worden gedaan via de para meter **InstanceCount** -toepassing. Daarom is de opdracht voor het maken van de Azure Files volume plugin-toepassing op een lokaal ontwikkel cluster:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Uw toepassingen configureren voor het gebruik van het volume
In het volgende code fragment ziet u hoe een op Azure Files gebaseerd volume kan worden opgegeven in het manifest bestand van de toepassing van uw toepassing. Het specifieke element van belang is de **volume** code:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

De naam van het stuur programma voor de Azure Files-volume-invoeg toepassing is **sfazurefile**. Deze waarde wordt ingesteld voor het kenmerk **stuur programma** van het element **volume** tag in het manifest van de toepassing.

In de **volume** code in het bovenstaande fragment zijn de volgende kenmerken vereist voor de Azure files volume-invoeg toepassing:
- **Bron** : dit is de naam van het volume. De gebruiker kan een wille keurige naam voor het volume kiezen.
- **Doel** -dit kenmerk is de locatie waarnaar het volume wordt toegewezen binnen de container die wordt uitgevoerd. Uw bestemming mag dus geen locatie zijn die al in uw container bestaat

Zoals u kunt zien in de **DriverOption** -elementen in het bovenstaande fragment, ondersteunt de Azure files-volume-invoeg toepassing de volgende stuur programma-opties:
- **sharename** : de naam van de Azure files bestands share die het volume voor de container levert.
- **storageAccountName** : de naam van het Azure Storage-account dat de Azure files bestands share bevat.
- **storageAccountKey** : de toegangs sleutel voor het Azure Storage-account dat de Azure files bestands share bevat.
- **storageAccountFQDN** : de domein naam die aan het opslag account is gekoppeld. Als storageAccountFQDN niet is opgegeven, wordt de domein naam gevormd door het standaard achtervoegsel (. file. core. Windows. net) te gebruiken met de storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Uw eigen volume of stuur programma voor logboek registratie gebruiken
Met Service Fabric kunt u ook uw eigen aangepaste [volume](https://docs.docker.com/engine/extend/plugins_volume/) -of [logboek registratie](https://docs.docker.com/engine/admin/logging/overview/) Stuur Programma's gebruiken. Als het stuur programma voor volume/logboek registratie van docker niet is geïnstalleerd op het cluster, kunt u het hand matig installeren met behulp van de RDP/SSH-protocollen. U kunt de installatie met deze protocollen uitvoeren via een [opstart script voor de schaalset van een virtuele machine](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) of een [SetupEntryPoint-script](./service-fabric-application-model.md).

Een voor beeld van het script om het [docker-volume stuur programma voor Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) te installeren, is als volgt:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

In uw toepassingen moet u de juiste waarden opgeven in het **volume** -en **LogConfig** -element onder **ContainerHostPolicies** in het manifest van de toepassing om het volume-of logboek registratie stuur programma te gebruiken dat u hebt geïnstalleerd.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Wanneer u een volume-invoeg toepassing opgeeft, wordt Service Fabric automatisch het volume gemaakt met behulp van de opgegeven para meters. De **bron** code voor het element **volume** is de naam van het volume en het tag **stuur programma** specificeert de invoeg toepassing voor het volume stuur programma. De **doel** code is de locatie waarnaar de **bron** wordt toegewezen in de container die wordt uitgevoerd. Het doel kan dus geen locatie zijn die al in de container bestaat. U kunt opties als volgt opgeven met behulp van de **DriverOption** -code:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Toepassings parameters worden ondersteund voor volumes, zoals wordt weer gegeven in het voor gaande manifest fragment (zoek naar `MyStorageVar` een voor beeld).

Als er een docker-logboek stuur programma is opgegeven, moet u agents (of containers) implementeren voor het afhandelen van de logboeken in het cluster. De label **DriverOption** kan worden gebruikt om opties voor het logboek stuur programma op te geven.

## <a name="next-steps"></a>Volgende stappen
* Als u container voorbeelden, inclusief het volume stuur programma, wilt bekijken, gaat u naar de [service Fabric container](https://github.com/Azure-Samples/service-fabric-containers) -voor beelden
* Als u containers wilt implementeren in een Service Fabric cluster, raadpleegt u het artikel [een container implementeren op service Fabric](./service-fabric-get-started-containers.md)
