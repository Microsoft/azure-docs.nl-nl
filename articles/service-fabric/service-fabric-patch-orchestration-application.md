---
title: Het Windows-besturings systeem in uw Service Fabric cluster bijwerken
description: In dit artikel wordt beschreven hoe u patches van besturings systemen kunt automatiseren op een Service Fabric cluster met behulp van patch Orchestration-toepassing.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: e51b247f8c1a5a9ed8f6ec8e24363015afb2f7de
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614408"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Het Windows-besturings systeem in uw Service Fabric cluster bijwerken

## <a name="automatic-os-image-upgrades"></a>Automatische upgrades van de besturingssysteem installatie kopie

[U krijgt automatische installatie kopieën van besturings systemen op uw Virtual Machine Scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) de best practice voor het bewaren van uw besturings systeem in Azure. Voor virtuele-machine schaal sets op basis van automatische installatie kopieën van besturings systemen is een grotere duurzaamheid vereist voor een schaalset.

Vereisten voor automatische upgrades van installatie kopieën van besturings systemen door Virtual Machine Scale Sets
-   Service Fabric [duurzaamheids niveau](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) is zilver of goud en niet bronzen.
-   De Service Fabric-extensie voor de model definitie van de schaalset moet TypeHandlerVersion 1,1 of hoger hebben.
-   Duurzaamheids niveau moet hetzelfde zijn op het Service Fabric cluster en Service Fabric extensie in de model definitie van de schaalset.
- Een extra status test of het gebruik van de toepassings status extensie voor Virtual Machine Scale Sets is niet vereist.

Zorg ervoor dat de duurzaamheids instellingen niet overeenkomen met het Service Fabric cluster en de uitbrei ding Service Fabric, omdat een niet-overeenkomend resultaat is opgetreden tijdens de upgrade. Duurzaamheids niveaus kunnen worden gewijzigd volgens de richt lijnen die op [Deze pagina](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)worden beschreven.

Met Bronze duurzaamheid is de automatische upgrade van de installatie kopie van het besturings systeem niet beschikbaar. Hoewel [patch Orchestration-toepassing](#patch-orchestration-application ) (alleen bedoeld voor niet door Azure gehoste clusters) *niet wordt aanbevolen* voor Silver-of hogere duurzaamheids niveaus, is dit de enige optie om Windows-updates te automatiseren met betrekking tot service Fabric upgrade domeinen.

> [!IMPORTANT]
> De in-VM-Upgrades waarbij ' Windows Update ' patches van besturings systemen toepast zonder de besturingssysteem schijf te vervangen, worden niet ondersteund in azure Service Fabric.

Er zijn twee stappen nodig om de functie in te scha kelen met uitgeschakelde Windows Update op het besturings systeem.

1. Automatisch bijwerken van installatie kopie van besturings systeem inschakelen, ARM van Windows-updates uitschakelen 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    Azure PowerShell
    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ``` 
    
1. Model voor het bijwerken van schaal sets nadat deze configuratie is gewijzigd, is het wijzigen van de installatie kopie van alle computers nodig om het model van de schaalset bij te werken, zodat de wijziging van kracht wordt.
    
    Azure PowerShell
    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ``` 
    
Bekijk de [automatische upgrades van de besturingssysteem installatie kopie door Virtual Machine Scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) voor verdere instructies.

## <a name="patch-orchestration-application"></a>Patch Orchestration-toepassing

> [!IMPORTANT]
> Vanaf 30 april 2019 wordt patch Orchestration Application versie 1,2. * niet meer ondersteund. Zorg ervoor dat u een upgrade uitvoert naar de nieuwste versie.

Patch Orchestration Application (POA) is een wrapper rond de Azure Service Fabric Repair Manager-service, waarmee de patch planning op basis van het besturings systeem kan worden geconfigureerd voor niet door Azure gehoste clusters. POA is niet vereist voor niet door Azure gehoste clusters, maar het plannen van een patch-installatie per update domein is vereist voor het patchen van Service Fabric cluster-hosts zonder uitval tijd.

POA is een Service Fabric toepassing waarmee het besturings systeem wordt geautomatiseerd op een Service Fabric cluster zonder uitval tijd.

POA biedt de volgende functies:

- **Automatische installatie** van de update van het besturings systeem. Updates van het besturings systeem worden automatisch gedownload en geïnstalleerd. Cluster knooppunten worden indien nodig opnieuw opgestart zonder dat er uitval tijd in het cluster ontstaat.

- **Integratie van cluster bewuste patches en status**. Tijdens POA worden updates toegepast, wordt de status van de cluster knooppunten gecontroleerd. Cluster knooppunten worden één knoop punt tegelijk of per update domein per keer bijgewerkt. Als de status van het cluster uitvalt vanwege het patch proces, wordt patches gestopt om te voor komen dat het probleem wordt verergerd.

## <a name="internal-details-of-poa"></a>Interne Details van POA

POA bestaat uit de volgende subonderdelen:

- **Coördinator service**: dit stateful service is verantwoordelijk voor:
    - De Windows Update-taak coördineren op het hele cluster.
    - Het resultaat van voltooide Windows Update bewerkingen opslaan.

- **Knoop punt-Agent service**: dit stateless service uitgevoerd op alle service Fabric cluster knooppunten. De service is verantwoordelijk voor:
    - De NTService van de knooppunt agent Boots trappen.
    - De NTService van de knooppunt agent bewaken.

- **NTService van de knoop punt-agent**: deze Windows NT-service wordt uitgevoerd op een privilege op een hoger niveau (systeem). De node Agent-service en de coördinator service worden daarentegen uitgevoerd met een bevoegdheid op lager niveau (netwerk SERVICE). De service is verantwoordelijk voor het uitvoeren van de volgende Windows Update taken op alle cluster knooppunten:
    - Automatische Windows-updates op het knoop punt uit te scha kelen.
    - Het downloaden en installeren van Windows-updates volgens het beleid dat door de gebruiker is gegeven.
    - De installatie van de computer na Windows-updates opnieuw starten.
    - Het uploaden van de resultaten van Windows-updates naar de coördinator-service.
    - Rapportage van status rapporten als een bewerking is mislukt nadat alle pogingen zijn uitgeput.

> [!NOTE]
> POA maakt gebruik van de service Service Fabric Repair Manager om het knoop punt in of uit te scha kelen en status controles uit te voeren. De herstel taak die door POA is gemaakt, houdt de Windows Update voortgang van elk knoop punt bij.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> De vereiste minimale .NET Framework versie is 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>De Repair Manager-service inschakelen (als deze nog niet wordt uitgevoerd)

POA vereist dat de Repair Manager-service is ingeschakeld op het cluster.

#### <a name="azure-clusters"></a>Azure-clusters

De Repair Manager-service is standaard ingeschakeld voor Azure-clusters op de duurzaamheids categorie zilver. Afhankelijk van de manier waarop deze clusters zijn gemaakt, is het mogelijk dat de Repair Manager-service niet is ingeschakeld voor Azure-clusters in de laag voor duurzaamheid van het goud. Azure-clusters in de laag Bronze duurzaamheid hebben standaard niet de Repair Manager-service ingeschakeld. Als de service al is ingeschakeld, kunt u deze bekijken in de sectie systeem services in Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Azure Portal
U kunt Repair Manager van de Azure Portal inschakelen tijdens het instellen van het cluster. Wanneer u het cluster configureert, selecteert u de optie **Repair Manager** toevoegen onder **invoeg** toepassingen.

![Afbeelding van het inschakelen van Repair Manager van de Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Het Azure Resource Manager-implementatie model
U kunt ook het [Azure Resource Manager-implementatie model](./service-fabric-cluster-creation-via-arm.md) gebruiken om de Repair Manager-service in te scha kelen op nieuwe en bestaande service Fabric clusters. Haal de sjabloon op voor het cluster dat u wilt implementeren. U kunt de voorbeeld sjablonen gebruiken of een aangepaste sjabloon voor Azure Resource Manager implementatie model maken. 

Ga als volgt te werk om de Repair Manager-service in te scha kelen met behulp van de [Azure Resource Manager-implementatie model sjabloon](./service-fabric-cluster-creation-via-arm.md):

1. Controleer of `apiVersion` het is ingesteld op *2017-07-01-preview* voor de resource *micro soft. ServiceFabric/clusters* . Als dat niet het geval is, moet u bijwerken `apiVersion` naar *2017-07-01-preview* of hoger:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Schakel de Repair Manager-service in door de volgende sectie toe te voegen `addonFeatures` na de `fabricSettings` sectie:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Nadat u de cluster sjabloon met deze wijzigingen hebt bijgewerkt, past u deze toe en laat u de update volt ooien. U kunt nu de Repair Manager-service zien die in uw cluster wordt uitgevoerd. Het heet *Fabric:/System/RepairManagerService* in de sectie systeem services van service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Zelfstandige on-premises clusters

Als u de Repair Manager-service wilt inschakelen op een nieuw of bestaand Service Fabric cluster, kunt u de [configuratie-instellingen voor een zelfstandig Windows-cluster](./service-fabric-cluster-manifest.md)gebruiken.

De Repair Manager-service inschakelen:

1. Controleer `apiVersion` of [algemene cluster configuraties](./service-fabric-cluster-manifest.md#general-cluster-configurations) is ingesteld op *04-2017* of hoger, zoals hier wordt weer gegeven:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Schakel de Repair Manager-service in door de volgende sectie toe te voegen `addonFeatures` na de `fabricSettings` sectie, zoals hier wordt weer gegeven:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Het cluster manifest bijwerken met deze wijzigingen met behulp van het bijgewerkte cluster manifest [een nieuw cluster maken of een](./service-fabric-cluster-creation-for-windows-server.md) [upgrade van de cluster configuratie uitvoeren](./service-fabric-cluster-upgrade-windows-server.md). 

   Wanneer het cluster wordt uitgevoerd met een bijgewerkt cluster manifest, ziet u de Repair Manager-service die in het cluster wordt uitgevoerd. Het heet *Fabric:/System/RepairManagerService* en is de sectie systeem services in service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Windows-updates configureren voor alle knoop punten

Automatische Windows-updates kunnen leiden tot een Beschikbaarheids verlies, omdat meerdere cluster knooppunten tegelijk kunnen worden gestart. POA probeert standaard de automatische Windows-updates op elk cluster knooppunt uit te scha kelen. Als de instellingen echter worden beheerd door een beheerder of een groepsbeleid, raden we u aan het beleid voor Windows Update expliciet in te stellen op ' waarschuwen voordat downloaden '.

## <a name="download-the-application-package"></a>Het toepassings pakket downloaden

Als u het toepassings pakket wilt downloaden, gaat u naar de [pagina patch Orchestration Application release](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) op github.

## <a name="configure-poa-behavior"></a>POA-gedrag configureren

U kunt POA-gedrag configureren om te voldoen aan uw behoeften. De standaard waarden onderdrukken door door gegeven in de toepassings parameter tijdens het maken of bijwerken van de toepassing. U kunt toepassings parameters opgeven door `ApplicationParameter` in te stellen op de `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` cmdlets.

| Parameter        | Type                          | Details |
|:-|-|-|
|MaxResultsToCache    |Omvang                              | Het maximum aantal Windows Update resultaten dat in de cache moet worden opgeslagen. <br><br>De standaard waarde is 3000, ervan uitgaande dat: <br> &nbsp;&nbsp;-Het aantal knoop punten is 20. <br> &nbsp;&nbsp;-Het aantal updates voor een knoop punt per maand is 5. <br> &nbsp;&nbsp;-Het aantal resultaten per bewerking mag 10 zijn. <br> &nbsp;&nbsp;-De resultaten voor de afgelopen drie maanden moeten worden opgeslagen. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy geeft het beleid aan dat door de coördinator service moet worden gebruikt om Windows-updates op de Service Fabric cluster knooppunten te installeren.<br><br>De toegestane waarden zijn: <br>*NodeWise*: Windows-updates worden één knoop punt tegelijk geïnstalleerd. <br> *UpgradeDomainWise*: Windows-updates worden per keer één update domein geïnstalleerd. (De meeste knoop punten die deel uitmaken van een update domein kunnen naar een Windows-Update gaan.)<br><br> Zie de sectie [Veelgestelde vragen](#frequently-asked-questions) om te bepalen welk beleid het meest geschikt is voor uw cluster.
|LogsDiskQuotaInMB   |Omvang  <br> (Standaard: *1024*)               | De maximale grootte van Logboeken voor patch indelings toepassingen in MB, die lokaal kan worden bewaard op knoop punten.
| WUQuery               | tekenreeks<br>(Standaard: *IsInstalled = 0*)                | Query uitvoeren om Windows-updates op te halen. Zie WuQuery voor meer informatie [.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
| InstallWindowsOSOnlyUpdates | *Booleaans* <br> (standaard: onwaar)                 | Gebruik deze markering om te bepalen welke updates moeten worden gedownload en geïnstalleerd. De volgende waarden zijn toegestaan <br>True: installeert alleen updates van het Windows-besturings systeem.<br>onwaar: alle beschik bare updates op de computer worden geïnstalleerd.          |
| WUOperationTimeOutInMinutes | Int <br>(Standaard: *90*)                   | Hiermee geeft u de time-out voor een Windows Update bewerking (zoeken of downloaden of installeren). Als de bewerking niet binnen de opgegeven time-out wordt voltooid, wordt deze afgebroken.       |
| WURescheduleCount     | Int <br> (Standaard: *5*)                  | Het maximum aantal keren dat de service de Windows-Update opnieuw plant als een bewerking permanent mislukt.          |
| WURescheduleTimeInMinutes | Int <br>(Standaard: *30*) | Het interval waarmee de Windows-updates opnieuw worden gepland als de fout zich blijft voordoen. |
| WUFrequency           | Door komma's gescheiden teken reeks (standaard: *wekelijks, woensdag, 7:00:00*)     | De frequentie voor het installeren van Windows-updates. De notatie en mogelijke waarden zijn: <br>-Maandelijks, DD, uu: MM: SS (voor beeld: *Monthly, 5, 12:22:32*). Toegestane waarden voor het veld _dd_ (dag) zijn getallen van 1 tot en met 28 en _laatste_. <br>-Wekelijks, dag, uu: MM: SS (voor beeld: *wekelijks, dinsdag, 12:22:32*)  <br>-Dagelijks, uu: MM: SS (voor beeld: *dagelijks, 12:22:32*)  <br>-Week, dag, uu: MM: SS (voor beeld: *2, vrijdag, 21:00:00* geeft 9:00 uur UTC op vrijdag van de 2e week van elke maand) <br>- *Geen* geeft aan dat Windows-updates niet mogen worden uitgevoerd.  <br><br> Tijden zijn UTC.|
| AcceptWindowsUpdateEula | Booleaans <br>(Standaard: *True*) | Door deze vlag in te stellen, accepteert de toepassing de End-User gebruiksrecht overeenkomst voor Windows Update namens de eigenaar van de computer.              |

> [!TIP]
> Als u wilt dat Windows-updates onmiddellijk worden uitgevoerd, stelt u `WUFrequency` in ten opzichte van de implementatie tijd van de toepassing. Stel dat u een test cluster met vijf knoop punten hebt en u de app op ongeveer 5:00 uur UTC wilt implementeren. Als u ervan uitgaat dat de upgrade of implementatie van de toepassing 30 minuten Maxi maal duurt, stelt u de WUFrequency als dagelijks in op *17:30:00*.

## <a name="deploy-poa"></a>POA implementeren

1. Alle vereiste stappen volt ooien om het cluster voor te bereiden.
1. Implementeer POA zoals andere Service Fabric-apps. Zie [toepassingen implementeren en verwijderen met](./service-fabric-deploy-remove-applications.md)behulp van Power shell om deze te implementeren met behulp van Power shell.
1. Als u de toepassing op het tijdstip van de implementatie wilt configureren, geeft u de door `ApplicationParameter` aan de `New-ServiceFabricApplication` cmdlet. Voor uw gemak hebben we het script Deploy.ps1 samen met de toepassing. Het script gebruiken:

    - Verbinding maken met een Service Fabric cluster met behulp van `Connect-ServiceFabricCluster` .
    - Voer het Power shell-script Deploy.ps1 uit met de juiste `ApplicationParameter` waarde.

> [!NOTE]
> Bewaar het script en de toepassingsmap *PatchOrchestrationApplication* in dezelfde map.

## <a name="upgrade-poa"></a>POA bijwerken

Volg de instructies in [service Fabric toepassing bijwerken met behulp van Power](./service-fabric-application-upgrade-tutorial-powershell.md)shell om een upgrade uit te voeren van uw POA-versie met behulp van Power shell.

## <a name="remove-poa"></a>POA verwijderen

Als u de toepassing wilt verwijderen, volgt u de instructies in [toepassingen implementeren en verwijderen met behulp van Power shell](./service-fabric-deploy-remove-applications.md).

Voor uw gemak hebben we het Undeploy.ps1-script in combi natie met de toepassing voorzien. Het script gebruiken:

  - Verbinding maken met een Service Fabric cluster met behulp van ```Connect-ServiceFabricCluster``` .
  - Voer de Power shell-script Undeploy.ps1 uit.

> [!NOTE]
> Bewaar het script en de toepassingsmap *PatchOrchestrationApplication* in dezelfde map.

## <a name="view-the-windows-update-results"></a>De Windows Update resultaten weer geven

POA maakt REST-Api's beschikbaar om de historische resultaten voor gebruikers weer te geven. Hier volgt een voor beeld van de JSON van het resultaat:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

De JSON-velden worden in de volgende tabel beschreven:

Veld | Waarden | Details
-- | -- | --
Kan operationresult niet | 0: voltooid<br> 1-geslaagd met fouten<br> 2-mislukt<br> 3-afgebroken<br> 4-afgebroken met time-out | Hiermee wordt het resultaat van de algehele bewerking aangegeven, waarbij doorgaans een of meer updates worden geïnstalleerd.
ResultCode | Hetzelfde als kan operationresult niet | In dit veld wordt het resultaat van de installatie bewerking voor een afzonderlijke update aangegeven.
OperationType | 1-installatie<br> 0: zoeken en downloaden| Standaard is installatie de enige OperationType die wordt weer gegeven in de resultaten.
WindowsUpdateQuery | De standaard waarde is ' IsInstalled = 0 ' | De Windows Update query die is gebruikt om naar updates te zoeken. Zie [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)voor meer informatie.
RebootRequired | waar-opnieuw opstarten is vereist<br> ONWAAR-opnieuw opstarten is niet vereist | Geeft aan of opnieuw opstarten is vereist om de installatie van updates te volt ooien.
OperationStartTime | DateTime | Geeft de tijd aan waarop de bewerking (downloaden/installeren) is gestart.
OperationTime | DateTime | Geeft de tijd aan waarop de bewerking (downloaden/installeren) is voltooid.
HResult | 0-geslaagd<br> overige-fout| Hiermee wordt de reden aangegeven voor het mislukken van de Windows-Update met updateID ' 7392acaf-6a85-427c-8a8d-058c25beb0d6 '.

Als er nog geen update is gepland, is de JSON van het resultaat leeg.

Meld u aan bij het cluster om Windows Update resultaten op te vragen. Zoek het IP-adres van de replica voor het primaire adres van de coördinator-service en open de volgende URL vanuit de browser: http:// &lt; replica-IP &gt; : &lt; ApplicationPort &gt; /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Het REST-eind punt voor de coördinator service heeft een dynamische poort. Als u de exacte URL wilt controleren, raadpleegt u Service Fabric Explorer. De resultaten zijn bijvoorbeeld beschikbaar op *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Afbeelding van het REST-eind punt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Als de omgekeerde proxy is ingeschakeld op het cluster, kunt u ook toegang krijgen tot de URL van buiten het cluster.

Het eind punt waarop u moet drukken, is *http:// &lt; SERVERURL &gt; : &lt; REVERSEPROXYPORT &gt; /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Als u de omgekeerde proxy op het cluster wilt inschakelen, volgt u de instructies in [reverse proxy in Azure service Fabric](./service-fabric-reverseproxy.md). 

> 
> [!WARNING]
> Nadat de omgekeerde proxy is geconfigureerd, zijn alle micro Services in het cluster die een HTTP-eind punt beschikbaar stellen van buiten het cluster adresseerbaar.

## <a name="diagnostics-and-health-events"></a>Diagnostische en status gebeurtenissen

In deze sectie wordt beschreven hoe u problemen met patch-updates oplost of diagnosticeert via POA op Service Fabric clusters.

> [!NOTE]
> Als u een groot aantal van de volgende, zelf diagnostische verbeteringen wilt ontvangen, moet u POA-versie 1.4.0 of hoger hebben geïnstalleerd.

De knooppunt agent NTService maakt [reparatie taken](/dotnet/api/system.fabric.repair.repairtask) voor het installeren van updates op de knoop punten. Elke taak wordt vervolgens voor bereid door de coördinator service volgens het goedkeurings beleid voor de taak. Ten slotte worden de voor bereide taken goedgekeurd door Repair Manager, waarbij geen enkele taak wordt goedgekeurd als het cluster een slechte status heeft. 

Ga als volgt te werk om inzicht te krijgen in hoe updates worden uitgevoerd op een knoop punt:

1. NodeAgentNTService wordt uitgevoerd op elk knoop punt en zoekt naar beschik bare Windows-updates op het geplande tijdstip. Als er updates beschikbaar zijn, worden deze gedownload op het knoop punt.

1. Nadat de updates zijn gedownload, maakt de NTService van de knooppunt agent een bijbehorende herstel taak voor het knoop punt met de naam *POS___ \<unique_id>*. U kunt deze herstel taken weer geven met behulp van de cmdlet [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) of met behulp van sfx in de sectie knooppunt Details. Nadat de herstel taak is gemaakt, wordt deze snel verplaatst naar de [status *geclaimd*](/dotnet/api/system.fabric.repair.repairtaskstate).

1. De coördinator service zoekt regel matig naar herstel taken *in de* aangegeven status en werkt deze vervolgens bij om de status op basis van TaskApprovalPolicy te *voorbereiden* . Als TaskApprovalPolicy is geconfigureerd om te worden NodeWise, wordt een herstel taak die overeenkomt met een knoop punt alleen voor bereid als er momenteel geen andere herstel taak is voor het *voorbereiden*, *goed keuren*, *uitvoeren* of *herstellen* van de status. 

   Op dezelfde manier zijn er in het geval van UpgradeWise TaskApprovalPolicy alleen taken in de voor gaande status voor knoop punten die deel uitmaken van hetzelfde update domein. Nadat een herstel taak is verplaatst om de status *voor te bereiden* , wordt het bijbehorende service Fabric knoop punt [uitgeschakeld](/powershell/module/servicefabric/disable-servicefabricnode) , waarbij de bedoeling is ingesteld op *opnieuw starten*.

   POA-versies 1.4.0 en hoger plaatsen gebeurtenissen met de eigenschap ClusterPatchingStatus op CoordinatorService om de knoop punten weer te geven waarop een patch wordt uitgevoerd. De updates worden geïnstalleerd op _poanode_0, zoals wordt weer gegeven in de volgende afbeelding:

    [![Afbeelding van de status van de cluster patching](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Nadat het knoop punt is uitgeschakeld, wordt de herstel taak verplaatst naar de status *bezig met uitvoeren* . 
   
   > [!NOTE]
   > Een knoop punt dat zich in de *Uitgeschakelde* status bevindt, kan een nieuwe herstel taak blok keren, waardoor de patch bewerking op het cluster wordt gestopt.

1. Wanneer de herstel taak de status *bezig met uitvoeren* heeft, begint de installatie van de patch op dat knoop punt. Nadat de patch is geïnstalleerd, is het mogelijk dat het knoop punt niet opnieuw wordt opgestart, afhankelijk van de patch. Vervolgens wordt de herstel taak verplaatst naar *herstel* status, waarmee het knoop punt opnieuw wordt ingeschakeld. De herstel taak wordt vervolgens als voltooid gemarkeerd.

   In POA-versies 1.4.0 en hoger kunt u de status van de update vinden door de status gebeurtenissen op NodeAgentService te bekijken met de WUOperationStatus- \<NodeName> eigenschap. De gemarkeerde secties in de volgende installatie kopieën tonen de status van Windows-updates op knoop punten *poanode_0* en *poanode_2*:

   [![Scherm opname toont console venster van Windows Update bewerkings status met poanode_0 gemarkeerd.](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Scherm opname toont console venster van Windows Update bewerkings status met poanode_1 gemarkeerd.](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   U kunt de details ook ophalen met behulp van Power shell. Hiervoor maakt u verbinding met het cluster en haalt u de status van de reparatie taak op met behulp van [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask). 
   
   In het volgende voor beeld bevindt de taak ' POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15 ' zich in de *DownloadComplete* -status. Dit betekent dat er updates zijn gedownload op het knoop punt *poanode_2* en dat de installatie wordt uitgevoerd wanneer de taak wordt verplaatst naar de status *bezig met uitvoeren* .

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Als er meer problemen moeten worden gevonden, meldt u zich aan bij uw virtuele machine (VM) of Vm's en leert u deze met behulp van Windows-gebeurtenis Logboeken. De eerder genoemde reparatie taak kan alleen voor komen in de volgende Substatussen van de module:

      ExecutorSubState | Beschrijving
    -- | -- 
      Geen = 1 |  Betekent dat er geen actieve bewerking op het knoop punt is. De status kan in overgang zijn.
      DownloadCompleted = 2 | Houdt in dat de Download bewerking is voltooid met succes, gedeeltelijk mislukt of mislukt.
      InstallationApproved = 3 | Houdt in dat de Download bewerking eerder is voltooid en dat de installatie is goedgekeurd door Repair Manager.
      InstallationInProgress = 4 | Komt overeen met de status van het uitvoeren van de herstel taak.
      InstallationCompleted = 5 | Houdt in dat de installatie is voltooid met succes, gedeeltelijk geslaagd of mislukt.
      RestartRequested = 6 | Houdt in dat de installatie van de patch is voltooid en dat er een bewerking voor opnieuw starten in behandeling is op het knoop punt.
      RestartNotNeeded = 7 |  Impliceert dat het opnieuw opstarten niet nodig is nadat de installatie van de patch is voltooid.
      RestartCompleted = 8 | Betekent dat de herstart is voltooid.
      OperationCompleted = 9 | De Windows Update bewerking is voltooid.
      OperationAborted = 10 | Betekent dat de Windows Update bewerking is afgebroken.

1. In POA versies 1.4.0 en hoger, wanneer een update poging van het knoop punt is voltooid, wordt een gebeurtenis met de eigenschap ' WUOperationStatus-[nodenaam] ' op NodeAgentService geplaatst om u te waarschuwen wanneer de volgende poging om de Windows-updates te downloaden en te installeren wordt gestart. Dit wordt weer gegeven in de volgende afbeelding:

     [![Scherm opname toont het console venster van Windows Update bewerkings status met de NodeAgentService.](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Logboeken met diagnostische gegevens

Patch Orchestration-toepassings logboeken worden verzameld als onderdeel van de runtime-logboeken van Service Fabric.

U kunt Logboeken vastleggen met behulp van het diagnostische hulp programma of de pijp lijn van uw keuze. POA maakt gebruik van de volgende vaste provider-Id's voor het registreren van gebeurtenissen via de [gebeurtenis bron](/dotnet/api/system.diagnostics.tracing.eventsource):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Status rapporten

POA publiceert ook status rapporten voor de knoop punt-Agent service of de coördinator-service in de volgende scenario's:

* De NTService van de knooppunt agent is niet beschikbaar

   Als de NTService van de knoop punt op een knoop punt niet beschikbaar is, wordt er een status rapport op waarschuwings niveau gegenereerd voor de knoop punt Agent service.

* De Repair Manager-service is niet ingeschakeld

   Als de Repair Manager-service niet op het cluster wordt gevonden, wordt er een status rapport op waarschuwings niveau gegenereerd voor de coördinator service.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Waarom wordt mijn cluster in een fout status weer geven wanneer POA wordt uitgevoerd?**

A: tijdens het installatie proces worden knoop punten door POA uitgeschakeld of opnieuw gestart. Dit kan tijdelijk leiden tot een slecht cluster.

Afhankelijk van het beleid van de toepassing kan één knoop punt worden uitgevoerd tijdens een patch bewerking *of* kan een volledig update domein in één keer omlaag gaan.

Aan het einde van de installatie van Windows-updates, worden de knoop punten opnieuw ingeschakeld na het opnieuw opstarten.

In het volgende voor beeld heeft het cluster tijdelijk een fout status gekregen omdat twee knoop punten niet beschikbaar waren en het MaxPercentageUnhealthyNodes-beleid werd geschonden. De fout is tijdelijk totdat de patch bewerking kan worden gestart.

![Afbeelding van een beschadigd cluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Als het probleem zich blijft voordoen, raadpleegt u de sectie probleem oplossing.

**V: wat kan ik doen als POA een waarschuwings status heeft?**

A: Controleer of een status rapport dat is gepost op de toepassing, de hoofd oorzaak aangeeft. Normaal gesp roken bevat de waarschuwing Details van het probleem. Als het probleem zich tijdelijk voordoet, wordt verwacht dat de toepassing automatisch wordt hersteld.

**V: wat kan ik doen als mijn cluster beschadigd is en ik een urgente update van het besturings systeem moet uitvoeren?**

A: POA installeert geen updates terwijl het cluster beschadigd is. Probeer het cluster naar een goede staat te brengen om de POA-werk stroom te blok keren.

**V: moet ik TaskApprovalPolicy instellen als ' NodeWise ' of ' UpgradeDomainWise ' voor mijn cluster?**

A: de instelling ' UpgradeDomainWise ' versnelt de volledige reparatie van het cluster door patching uit te zetten op alle knoop punten die deel uitmaken van een update domein. Tijdens het proces zijn knoop punten die deel uitmaken van een geheel update domein niet beschikbaar ( [ *uitgeschakeld*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)).

De instelling ' NodeWise ' houdt daarentegen slechts één knoop punt in beslag, wat inhoudt dat de algehele cluster patches mogelijk langer duren. Tijdens het patch proces is echter slechts één knoop punt niet beschikbaar (in de status *uitgeschakeld* ).

Als uw cluster gedurende een patch-cyclus kan worden uitgevoerd op een N-1 aantal update domeinen (waarbij N het totale aantal update domeinen in uw cluster is), kunt u het beleid instellen als ' UpgradeDomainWise '. Zo niet, dan stelt u deze in op ' NodeWise '.

**V: hoe lang duurt het om een knoop punt te patchen?**

A: het bijwerken van een knoop punt kan enkele minuten duren (zoals [definitie-updates voor Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) in uren (bijvoorbeeld [cumulatieve Windows-updates](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). De benodigde tijd voor het patchen van een knoop punt is vooral afhankelijk van: 
 - De grootte van de updates.
 - Het aantal updates dat moet worden toegepast in een patch venster.
 - De tijd die nodig is voor het installeren van de updates, het opnieuw opstarten van het knoop punt (indien nodig) en het volt ooien van de installatie stappen na opnieuw opstarten.
 - De prestaties van de virtuele machine of computer en netwerk omstandigheden.

**V: hoe lang duurt het om een volledig cluster te patchen?**

A: de benodigde tijd voor het patchen van een volledig cluster is afhankelijk van:

- De tijd die nodig is om een knoop punt te patchen.

- Het beleid van de coördinator service. Het standaard beleid ' NodeWise ' resulteert in het patchen van slechts één knoop punt tegelijk, een benadering die langzamer is dan het gebruik van ' UpgradeDomainWise '. 

   Bijvoorbeeld: als een knoop punt ~ 1 uur duurt om een patch uit te voeren, moet u een cluster van 20 knoop punten (hetzelfde type knoop punt) bijwerken met 5 update domeinen, elk met 4 knoop punten, om het volgende te doen:
    - Voor "NodeWise": ~ 20 uur.
    - Voor "UpgradeDomainWise": ~ 5 uur.

- De cluster belasting. Voor elke patch bewerking moet de werk belasting van de klant worden verplaatst naar andere beschik bare knoop punten in het cluster. Een van de knoop punten die worden bijgewerkt, zou tijdens deze tijd de [status *uitschakelen*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling) kunnen hebben. Als het cluster wordt uitgevoerd bij een piek belasting, neemt het proces meer tijd in beslag. Daarom zou het algemene proces van patches langzaam kunnen lijken in dergelijke extreme omstandigheden.

- Cluster status fouten tijdens het uitvoeren van patches. Bij een [afname](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error) in de [status van het cluster](./service-fabric-health-introduction.md) wordt het patch proces onderbroken. Dit probleem zou de totale tijd die nodig is om het hele cluster op te lossen, toe te voegen.

**V: Waarom worden sommige updates in de Windows Update resultaten weer geven die zijn verkregen via REST API, maar niet onder de Windows Update geschiedenis op de computer?**

A: sommige product updates worden alleen in hun eigen update-of patch geschiedenis weer gegeven. Updates van Windows Defender worden bijvoorbeeld mogelijk wel of niet weer gegeven in Windows Update geschiedenis van Windows Server 2016.

**V: kan POA worden gebruikt om mijn ontwikkel cluster (een cluster met één knoop punt) te patchen?**

A: Nee, POA kan niet worden gebruikt voor het patchen van een cluster met één knoop punt. Deze beperking is zo ontworpen, omdat [service Fabric systeem services](./service-fabric-technical-overview.md#system-services) of andere klant-apps downtime zouden kunnen opleveren. Daarom worden herstel taken voor patches nooit goedgekeurd door Repair Manager.

**V: Hoe kan ik patch cluster knooppunten op Linux?**

A: voor meer informatie over het indelen van updates in Linux, raadpleegt u de [Azure virtual machine-schaal sets automatische upgrades van besturings systemen](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

**V: Waarom wordt de update cyclus zo lang duren?**

A: Voer een query uit voor de JSON van het resultaat, geef de update cyclus voor alle knoop punten op en probeer vervolgens de tijd te achterhalen die wordt gebruikt door de installatie van de update op elk knoop punt met behulp van OperationStartTime en OperationTime (OperationCompletionTime). 

Als er sprake is van een groot tijd venster waarin geen update plaatsvindt, kan het cluster een fout status hebben en daarom kan Repair Manager geen POA-herstel taken goed keuren. Als de installatie van de update lange tijd in beslag neemt op een knoop punt, is het mogelijk dat het knoop punt in een tijdje niet is bijgewerkt. Een groot aantal updates kan wachten op installatie, wat kan leiden tot vertragingen. 

Het kan ook zijn dat het bijwerken van het knoop punt wordt geblokkeerd omdat het vastloopt aan het *uitschakelen* van de status. Dit gebeurt meestal doordat het uitschakelen van het knoop punt kan leiden tot problemen met het quorum of gegevens verlies.

**V: Waarom moet het knoop punt worden uitgeschakeld wanneer POA wordt bijgewerkt?**

A: POA schakelt het knoop punt uit met een *herstart* intentie, waarmee alle service Fabric services die op het knoop punt worden uitgevoerd, worden gestopt of opnieuw worden toegewezen. POA doet er alles aan om ervoor te zorgen dat toepassingen niet eindigen met een combi natie van nieuwe en oude Dll's. Daarom raden we u aan om een knoop punt niet te repareren zonder het uit te scha kelen.

**V: wat is het maximum aantal knoop punten dat kan worden bijgewerkt met behulp van POA?**

A: POA gebruikt Service Fabric Repair Manager om herstel taken voor knoop punten voor updates te maken. Er kunnen echter niet meer dan 250 reparatie taken tegelijkertijd aanwezig zijn. Op dit moment maakt POA herstel taken voor elk knoop punt op hetzelfde moment, zodat POA niet meer dan 250 knoop punten in een cluster kan bijwerken. 

## <a name="disclaimers"></a>Disclaimers

- POA accepteert de End-User gebruiksrecht overeenkomst voor Windows Update namens de gebruiker. De instelling kan eventueel worden uitgeschakeld in de configuratie van de toepassing.

- POA verzamelt telemetrie om het gebruik en de prestaties bij te houden. De telemetrie van de toepassing volgt de instelling van de telemetrie-instelling van de Service Fabric runtime (deze is standaard ingeschakeld).

## <a name="troubleshooting"></a>Problemen oplossen

In deze sectie vindt u mogelijke oplossingen voor het oplossen van problemen met patch knooppunten.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Een knoop punt is niet teruggestuurd naar de status up

* Het knoop punt is mogelijk vastgelopen in de status *uitschakelen* omdat:

  - Er is een veiligheids controle in behandeling. Om deze situatie te verhelpen, moet u ervoor zorgen dat er voldoende knoop punten in een goede staat zijn.

* Het knoop punt is *uitgeschakeld* vanwege de volgende status:

  - Deze is hand matig uitgeschakeld.
  - Deze is uitgeschakeld vanwege een voortdurende Azure-infrastructuur taak.
  - Het is tijdelijk uitgeschakeld door POA om het knoop punt te patchen.

* Het knoop punt kan zich in de status omlaag bezitten omdat:

  - Deze is hand matig in een lagere status geplaatst.
  - Er wordt een herstart uitgevoerd (dit wordt mogelijk veroorzaakt door POA).
  - Er is een defecte VM of machine of er zijn problemen met de netwerk verbinding.

### <a name="updates-were-skipped-on-some-nodes"></a>Updates zijn overgeslagen op enkele knoop punten

POA probeert een Windows-Update op basis van het beleid voor opnieuw plannen te installeren. De service probeert het knoop punt te herstellen en de update op basis van het toepassings beleid over te slaan.

In een dergelijk geval wordt een status rapport op waarschuwings niveau gegenereerd voor de knoop punt Agent service. Het Windows Update resultaat bevat ook de mogelijke reden voor de fout.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>De status van het cluster gaat naar een fout terwijl de update wordt geïnstalleerd

Een defecte Windows-Update kan de status van een toepassing of cluster op een bepaald knoop punt of update domein innemen. POA wordt elke volgende Windows Update bewerking beëindigd totdat het cluster weer in orde is.

Een beheerder moet ingrijpen en bepalen waarom de toepassing of het cluster slecht is geworden vanwege Windows Update.

## <a name="poa-release-notes"></a>Release opmerkingen bij POA

>[!NOTE]
> Voor POA-versies 1.4.0 en hoger kunt u release opmerkingen en releases vinden op de [release pagina patch Orchestration Application](https://github.com/microsoft/Service-Fabric-POA/releases/) op github.

### <a name="version-110"></a>Versie 1.1.0
- Open bare versie

### <a name="version-111"></a>Versie 1.1.1
- Er is een fout opgelost in SetupEntryPoint van NodeAgentService waardoor NodeAgentNTService niet is geïnstalleerd.

### <a name="version-120"></a>Versie 1.2.0

- Fout oplossingen rond de werk stroom voor het opnieuw opstarten van het systeem.
- Fout oplossing bij het maken van RM-taken als gevolg waarvan de status controle tijdens het voorbereiden van herstel taken niet gebeurt zoals verwacht.
- De opstart modus voor Windows-service POANodeSvc gewijzigd van automatisch in delayed-auto.

### <a name="version-121"></a>Versie 1.2.1

- Fout oplossing in de werk stroom Scale-down van het cluster. Geïntroduceerde garbage-verzamelings logica voor POA-herstel taken die horen bij niet-bestaande knoop punten.

### <a name="version-122"></a>Versie 1.2.2

- Diverse oplossingen voor fouten.
- Binaire bestanden zijn nu ondertekend.
- De sfpkg-koppeling voor de toepassing is toegevoegd.

### <a name="version-130"></a>Versie 1.3.0

- Als InstallWindowsOSOnlyUpdates is ingesteld op False, worden alle beschik bare updates geïnstalleerd.
- De logica voor het uitschakelen van automatische updates is gewijzigd. Hiermee wordt een bug opgelost waarbij automatische updates niet zijn uitgeschakeld op de server 2016 en hoger.
- De plaatsings beperking met para meters voor de micro services van POA voor geavanceerde use cases.

### <a name="version-131"></a>Versie 1.3.1
- Herstellen van regressie waarbij POA 1.3.0 niet werkt op Windows Server 2012 R2 of eerder vanwege een fout bij het uitschakelen van automatische updates. 
- Bug corrigeren waarbij de InstallWindowsOSOnlyUpdates-configuratie altijd waar is.
- De standaard waarde van InstallWindowsOSOnlyUpdates wijzigen in false.

### <a name="version-132"></a>Versie 1.3.2
- Het oplossen van een probleem dat van invloed is op de levens cyclus van patches op een knoop punt, als er knoop punten zijn met een naam die een subset is van de naam van het huidige knoop punt. Voor dergelijke knoop punten is het mogelijk dat patches zijn gemist of opnieuw opstarten in behandeling is.
