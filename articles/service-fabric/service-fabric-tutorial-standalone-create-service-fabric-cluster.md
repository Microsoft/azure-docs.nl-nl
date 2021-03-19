---
title: Zelfstandige Service Fabric-client installeren
description: In deze zelfstudie leert u hoe u de standalone Service Fabric-client installeert op het cluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae0b343be986f4d8d5176c1f39eef6b23ca81278
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91840639"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Zelfstudie: Service Fabric-cluster installeren en maken

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze reeks zelfstudies maakt u een zelfstandig cluster dat in AWS of Azure wordt gehost. Vervolgens installeert u een toepassing in het cluster.

Deze zelfstudie is deel twee van een serie. In deze zelfstudie wordt u stapsgewijs begeleid door het maken van een zelfstandig Service Fabric-cluster.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Het zelfstandige Service Fabric-pakket downloaden en installeren
> * Het Service Fabric-cluster maken
> * Verbinding maken met het Service Fabric-cluster

## <a name="download-the-service-fabric-for-windows-server-package"></a>Het pakket Service Fabric voor Windows Server downloaden

Service Fabric biedt een installatiepakket voor het maken van zelfstandige Service Fabric-clusters.  [Download het installatiepakket](https://go.microsoft.com/fwlink/?LinkId=730690) op uw lokale computer.  Zodra het pakket is gedownload, kopieert u het via de RDP-verbinding naar uw VM en plakt u het op het bureaublad.

Selecteer het ZIP-bestand, open het contextmenu en selecteer **Alles uitpakken** > **Uitpakken**.  Als u de bestanden uitpakt, wordt er op het bureaublad een map gegenereerd met de naam van het ZIP-bestand.

U kunt desgewenst meer informatie weergeven over [de inhoud van het installatiepakket](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Configuratiebestand instellen

U maakt een Windows-cluster met drie knooppunten, wat betekent dat u het bestand `ClusterConfig.Unsecure.MultiMachine.json` moet aanpassen.

Werk vervolgens de drie ipAddress-regels bij in het bestand, regels 8, 15 en 22, zodat deze de IP-adressen voor elk van de exemplaren bevatten.

Als u hiermee klaar bent, zien de knooppunten er als volgt uit:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Vervolgens moet u enkele eigenschappen bijwerken.  Op regel 34 moet u de verbindingsreeks voor de DiagnosticsStore aanpassen. De reeks moet er als volgt uitzien na de wijziging: `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`.

Voeg ten slotte in de sectie `nodeTypes` van de configuratie een nieuwe sectie toe om de tijdelijke poorten toe te wijzen die door Windows worden gebruikt.  Het configuratiebestand moet er nu als volgt uitzien:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>De omgeving valideren

Het script *TestConfiguration.ps1* in het zelfstandige pakket wordt gebruikt als Best Practices Analyzer om te valideren of een cluster in een bepaalde omgeving kan worden geïmplementeerd. In de [Implementatievoorbereiding](service-fabric-cluster-standalone-deployment-preparation.md) vindt u de implementatie- en omgevingsvereisten. Voer het script uit om te controleren of u het ontwikkelingscluster kunt maken:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

De uitvoer moet eruit zien als in het volgende voorbeeld. Als voor het veld 'Passed' de waarde `True` wordt geretourneerd, zijn de controles uitgevoerd en kan het cluster worden geïmplementeerd op basis van de ingevoerde configuratie.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Het cluster maken

Zodra de configuratie van het cluster is gevalideerd, voert u het script *CreateServiceFabricCluster.ps1* uit om het Service Fabric-cluster te implementeren op de virtuele machines in het configuratiebestand.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Als alles werkt, krijgt u deze uitvoer te zien:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Er worden implementatietraceringen weggeschreven naar de VM/computer waarop u het PowerShell-script CreateServiceFabricCluster.ps1 hebt uitgevoerd. U vindt deze traceringen in de submap DeploymentTraces, in de map van waaruit het script is uitgevoerd. U weet dat Service Fabric goed is geïmplementeerd op een computer als de geïnstalleerde bestanden in de map FabricDataRoot staan, zoals beschreven in de sectie FabricSettings van het configuratiebestand voor het cluster (standaard c:\ProgramData\SF). Daarnaast moeten de processen FabricHost.exe en Fabric.exe actief zijn in Taakbeheer.
>
>

### <a name="open-service-fabric-explorer"></a>Service Fabric Explorer openen

U kunt nu met behulp van Service Fabric Explorer verbinding maken met het cluster. Dat kan rechtstreeks vanaf een van de computers via http:\//localhost:19080/Explorer/index.html of op afstand via http:\//<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Knooppunten toevoegen en verwijderen

U kunt knooppunten toevoegen aan of verwijderen uit uw zelfstandige Service Fabric-cluster wanneer de bedrijfsbehoeften veranderen. Zie [Knooppunten toevoegen aan of verwijderen uit een zelfstandig Service Fabric-cluster](service-fabric-cluster-windows-server-add-remove-nodes.md) voor gedetailleerde stappen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over het gelijktijdig uploaden van grote hoeveelheden willekeurige gegevens naar een opslagaccount, om het volgende te kunnen doen:

> [!div class="checklist"]
> * De verbindingsreeks configureren
> * De toepassing bouwen
> * De toepassing uitvoeren
> * Het aantal verbindingen valideren

Ga naar deel drie van de reeks om een toepassing te installeren in het cluster dat u hebt gemaakt.

> [!div class="nextstepaction"]
> [De toepassing installeren in het Service Fabric-cluster](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
