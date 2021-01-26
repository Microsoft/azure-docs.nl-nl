---
title: Een zelfstandige Azure Service Fabric-cluster maken
description: Maak een Azure Service Fabric-cluster op elke machine (fysiek of virtueel) waarop Windows Server wordt uitgevoerd, ongeacht of deze zich on-premises of in een wille keurige Cloud bevindt.
ms.topic: conceptual
ms.date: 2/21/2019
ms.openlocfilehash: 41af655be07ccae2b66e75f5bfe87629cdb54924
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785681"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Een zelfstandig cluster maken dat wordt uitgevoerd in Windows Server
U kunt Azure Service Fabric gebruiken om Service Fabric-clusters te maken op alle virtuele machines of computers waarop Windows Server wordt uitgevoerd. Dit betekent dat u Service Fabric-toepassingen kunt implementeren en uitvoeren in elke omgeving met een set onderling verbonden Windows Server-computers, deze on-premises of met een Cloud provider. Service Fabric biedt een installatie pakket om Service Fabric clusters te maken die het zelfstandige Windows Server-pakket worden genoemd. Traditionele Service Fabric clusters op Azure zijn beschikbaar als beheerde service, terwijl zelfstandige Service Fabric clusters self-service zijn. Zie [Azure en zelfstandige service Fabric clusters vergelijken](./service-fabric-deploy-anywhere.md)voor meer informatie over de verschillen.

Dit artikel begeleidt u stapsgewijs door de stappen voor het maken van een Service Fabric zelfstandig cluster.

> [!NOTE]
> Dit zelfstandige Windows Server-pakket is gratis verkrijgbaar en kan worden gebruikt voor productie-implementaties. Dit pakket bevat mogelijk nieuwe Service Fabric-functies die in ' preview ' zijn opgenomen. Schuif omlaag naar '[Preview-functies die deel uitmaken van dit pakket](#previewfeatures_anchor)'. van de sectie voor de lijst met preview-functies. U kunt nu [een exemplaar van de gebruiksrecht overeenkomst downloaden](https://go.microsoft.com/fwlink/?LinkID=733084) .
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Ontvang ondersteuning voor de Service Fabric voor Windows Server-pakket
* Vraag de community naar het zelfstandige Service Fabric-pakket voor Windows Server op de [pagina micro soft Q&een vraag voor Azure service Fabric](/answers/topics/azure-service-fabric.html).
* Open een ticket voor [professionele ondersteuning voor service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Meer informatie over Professional Support van micro soft [vindt u hier](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* U kunt ook ondersteuning voor dit pakket krijgen als onderdeel van [micro soft Premier Support](https://support.microsoft.com/en-us/premier).
* Raadpleeg de [ondersteunings opties voor Azure service Fabric](./service-fabric-support.md)voor meer informatie.
* Als u Logboeken voor ondersteunings doeleinden wilt verzamelen, voert u de [service Fabric zelfstandige logboek verzamelaar](service-fabric-cluster-standalone-package-contents.md)uit.

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Het pakket Service Fabric voor Windows Server downloaden
Als u het cluster wilt maken, gebruikt u de Service Fabric voor Windows Server-pakket (Windows Server 2012 R2 en nieuwer) die hier worden gevonden: <br>
[Koppeling downloaden-Service Fabric zelfstandig pakket-Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Zoek [hier](service-fabric-cluster-standalone-package-contents.md)meer informatie over de inhoud van het pakket.

Het runtime pakket voor Service Fabric wordt automatisch gedownload op het moment dat het cluster wordt gemaakt. Als u implementeert vanaf een computer die niet is verbonden met internet, moet u het runtime-pakket buiten band downloaden: <br>
[Koppeling downloaden-Service Fabric runtime-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Zoek voor beelden van zelfstandige cluster configuraties op: <br>
[Voor beelden van zelfstandige cluster configuraties](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Het cluster maken
Met het installatiepakket worden diverse voorbeelden van clusterconfiguraties geïnstalleerd. *ClusterConfig.Unsecure.DevCluster.json* is de eenvoudigste clusterconfiguratie: een onbeveiligd cluster met drie knooppunten die worden uitgevoerd op één computer.  Andere configuratiebestanden beschrijven clusters voor één of meerdere machines die zijn beveiligd met een x.509-certificaat of met Windows-beveiliging.  U hoeft de standaardconfiguratie-instellingen voor deze zelfstudie niet te wijzigen, maar neem wel het configuratiebestand door om vertrouwd te raken met de instellingen.  De sectie **nodes** beschrijft de drie knooppunten in het cluster: naam, IP-adres, [knooppunttype, foutdomein en upgradedomein](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  De sectie **properties** definieert [de beveiliging, het betrouwbaarheidsniveau, de diagnostische verzameling en de typen knooppunten](service-fabric-cluster-manifest.md#cluster-properties) voor het cluster.

Het cluster dat in dit artikel is gemaakt, is niet beveiligd.  Iedereen kan anoniem verbinding maken en beheerbewerkingen uitvoeren. Productieclusters moeten dus altijd worden beveiligd met X.509-certificaten of Windows-beveiliging.  Beveiliging kan alleen worden geconfigureerd tijdens het maken van het cluster. Het is niet mogelijk beveiliging in te schakelen nadat het cluster is gemaakt. Update het configuratie bestand [certificaat beveiliging](service-fabric-windows-cluster-x509-security.md) of [Windows-beveiliging](service-fabric-windows-cluster-windows-security.md)inschakelen. Lees [Een cluster beveiligen](service-fabric-cluster-security.md) voor meer informatie over de beveiliging van Service Fabric-clusters.

### <a name="step-1-create-the-cluster"></a>Stap 1: het cluster maken

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenario A: een niet-beveiligd lokaal ontwikkel cluster maken
Service Fabric kan worden geïmplementeerd in een ontwikkel cluster met één machine met behulp van de *ClusterConfig.Unsecure.DevCluster.jsvoor* het bestand dat is opgenomen in voor [beelden](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Pak het zelfstandige pakket op uw computer uit, kopieer het voorbeeld configuratie bestand naar de lokale computer en voer vervolgens het *CreateServiceFabricCluster.ps1* script uit via een Power shell-sessie van een Administrator uit de map van het zelfstandige pakket.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Zie de sectie omgeving instellen op het [plannen en voorbereiden van de implementatie van het cluster](service-fabric-cluster-standalone-deployment-preparation.md) voor informatie over probleem oplossing.

Als u klaar bent met het uitvoeren van ontwikkel scenario's, kunt u het Service Fabric-cluster verwijderen van de machine door te verwijzen naar de stappen in de sectie '[een cluster verwijderen](#removecluster_anchor)'. 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenario B: een cluster met meerdere machines maken
Nadat u de plannings-en voorbereidings stappen hebt door lopen in het [plannen en voorbereiden van de implementatie van uw cluster](service-fabric-cluster-standalone-deployment-preparation.md), kunt u uw productie cluster maken met behulp van het cluster configuratie bestand.

De clusterbeheerder die het cluster implementeert en configureert, moet administratorbevoegdheden hebben op de computer. U kunt Service Fabric niet installeren op een domeincontroller.

1. Het script *TestConfiguration.ps1* in het zelfstandige pakket wordt gebruikt als Best Practices Analyzer om te valideren of een cluster in een bepaalde omgeving kan worden geïmplementeerd. In de [Implementatievoorbereiding](service-fabric-cluster-standalone-deployment-preparation.md) vindt u de implementatie- en omgevingsvereisten. Voer het script uit om te controleren of u het ontwikkelingscluster kunt maken:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    De uitvoer ziet er als volgt uit. Als het onderste veld ' door gegeven ' wordt geretourneerd als ' True ', worden Sanity-controles door gegeven en kan het cluster op basis van de invoer configuratie worden geïmplementeerd.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Het cluster maken: Voer het *CreateServiceFabricCluster.ps1* script uit om het service Fabric cluster op elke computer te implementeren in de configuratie. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Er worden implementatietraceringen weggeschreven naar de VM/computer waarop u het PowerShell-script CreateServiceFabricCluster.ps1 hebt uitgevoerd. U vindt deze traceringen in de submap DeploymentTraces, in de map van waaruit het script is uitgevoerd. U weet dat Service Fabric goed is geïmplementeerd op een computer als de geïnstalleerde bestanden in de map FabricDataRoot staan, zoals beschreven in de sectie FabricSettings van het configuratiebestand voor het cluster (standaard c:\ProgramData\SF). Daarnaast moeten de processen FabricHost.exe en Fabric.exe actief zijn in Taakbeheer.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenario C: een offline cluster (verbinding met Internet) maken
Het runtime pakket voor Service Fabric wordt automatisch gedownload tijdens het maken van het cluster. Wanneer u een cluster implementeert op computers die niet zijn verbonden met internet, moet u het Service Fabric runtime pakket afzonderlijk downloaden en het pad naar het cluster maken.
Het runtime pakket kan afzonderlijk worden gedownload, van een andere computer die is verbonden met internet, via de [Download koppeling-service Fabric runtime-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Kopieer het runtime pakket naar de locatie waarvandaan u het offline cluster implementeert en maak het cluster door uit te voeren `CreateServiceFabricCluster.ps1` met de `-FabricRuntimePackagePath` para meter opgenomen, zoals wordt weer gegeven in dit voor beeld: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.jsop* en *.\MicrosoftAzureServiceFabric.cab* zijn de paden naar respectievelijk de cluster configuratie en het bestand runtime. cab.

### <a name="step-2-connect-to-the-cluster"></a>Stap 2: verbinding maken met het cluster
Maak verbinding met het cluster om te controleren of het cluster actief is en beschikbaar is. De Service Fabric PowerShell-module wordt met de runtime geïnstalleerd.  U kunt verbinding maken met het cluster vanaf een van de cluster knooppunten of vanaf een externe computer met de Service Fabric runtime.  De cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) brengt een verbinding met het cluster tot stand.

Als u verbinding wilt maken met een niet-beveiligd cluster, voert u de volgende Power shell-opdracht uit:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Bijvoorbeeld:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
```

Zie [Connect to a secure cluster](service-fabric-connect-to-secure-cluster.md) (Verbinding maken met een beveiligd cluster) voor meer voorbeelden van clusterverbindingen. Nadat u verbinding hebt gemaakt met het cluster, gebruikt u de cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) om een lijst weer te geven van de knooppunten in het cluster, evenals statusinformatie voor elk knooppunt. **HealthState** moet *OK* zijn voor elk knooppunt.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Stap 3: het cluster visualiseren met Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) is een goed hulpmiddel om een cluster te visualiseren en toepassingen te beheren.  Service Fabric Explorer is een service die in het cluster wordt uitgevoerd en die u gebruikt om toegang te krijgen tot een browser door naar te navigeren `http://localhost:19080/Explorer` .

Het clusterdashboard bevat een overzicht van het cluster, inclusief een overzicht van de toepassings- en knooppuntstatus. In de knooppuntweergave ziet u de fysieke indeling van het cluster. Voor elk knooppunt kunt u controleren voor welke toepassingen er op het knooppunt code is geïmplementeerd.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Knooppunten toevoegen en verwijderen
U kunt knooppunten toevoegen aan of verwijderen uit uw zelfstandige Service Fabric-cluster wanneer de bedrijfsbehoeften veranderen. Zie [Knooppunten toevoegen aan of verwijderen uit een zelfstandig Service Fabric-cluster](service-fabric-cluster-windows-server-add-remove-nodes.md) voor gedetailleerde stappen.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Een cluster verwijderen
Als u een cluster wilt verwijderen, voert u het PowerShell-script *RemoveServiceFabricCluster.ps1* uit vanuit de pakketmap en geeft u daarbij het pad op naar het JSON-configuratiebestand. Optioneel kunt u een locatie voor het verwijderingslogboek opgeven.

Dit script kan worden uitgevoerd op elke computer met beheerders toegang tot alle computers die worden vermeld als knoop punten in het cluster configuratie bestand. De computer waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>De telemetriegegevens die zijn verzameld en hoe u deze kunt afmelden
Standaard verzamelt het product telemetrie op het Service Fabric gebruik om het product te verbeteren. De best practice Analyzer die als onderdeel van de installatie wordt uitgevoerd, controleert de verbinding met [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) . Als deze niet bereikbaar is, mislukt de installatie tenzij u de telemetrie afmeldt.

1. De telemetrie-pijp lijn probeert de volgende gegevens [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) één keer per dag te uploaden. Het is een upload van het hoogste aantal en heeft geen invloed op de functionaliteit van het cluster. De telemetrie wordt alleen verzonden vanuit het knoop punt dat de primaire failover Manager uitvoert. Geen andere knoop punten verzenden telemetrie.
2. De telemetrie bestaat uit het volgende:

* Aantal services
* Aantal ServiceTypes
* Aantal toepassingen
* Aantal ApplicationUpgrades
* Aantal FailoverUnits
* Aantal InBuildFailoverUnits
* Aantal UnhealthyFailoverUnits
* Aantal Replica's
* Aantal InBuildReplicas
* Aantal StandByReplicas
* Aantal OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Aantal knoop punten
* IsContextComplete: True/False
* ClusterId: dit is een GUID die wille keurig is gegenereerd voor elk cluster
* ServiceFabricVersion
* Het IP-adres van de virtuele machine of computer van waaruit de telemetrie is geüpload

Als u telemetrie wilt uitschakelen, voegt u het volgende toe aan de *Eigenschappen* in uw cluster configuratie: *enableTelemetry: False*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Preview-functies die zijn opgenomen in dit pakket
Geen.


> [!NOTE]
> Vanaf de nieuwe [Ga-versie van het zelfstandige cluster voor Windows Server (versie 5.3.204. x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)kunt u uw cluster upgraden naar toekomstige releases, hand matig of automatisch. Raadpleeg [een zelfstandig service Fabric cluster versie document bijwerken](service-fabric-cluster-upgrade-windows-server.md) voor meer informatie.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Toepassingen implementeren en verwijderen met behulp van Power shell](service-fabric-deploy-remove-applications.md)
* [Configuratie-instellingen voor zelfstandig Windows-cluster](service-fabric-cluster-manifest.md)
* [Knoop punten toevoegen aan of verwijderen uit een zelfstandig Service Fabric cluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Een zelfstandige Service Fabric cluster versie bijwerken](service-fabric-cluster-upgrade-windows-server.md)
* [Een zelfstandig Service Fabric cluster maken met virtuele Azure-machines waarop Windows wordt uitgevoerd](./service-fabric-cluster-creation-via-arm.md)
* [Een zelfstandige cluster in Windows beveiligen met behulp van Windows-beveiliging](service-fabric-windows-cluster-windows-security.md)
* [Een zelfstandige cluster in Windows beveiligen met x509-certificaten](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
