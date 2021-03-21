---
title: Het zelfstandige Azure Service Fabric-cluster configureren
description: Meer informatie over het configureren van uw zelfstandige of on-premises Azure Service Fabric-cluster.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91843155"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Configuratie-instellingen voor een zelfstandig Windows-cluster
In dit artikel worden de configuratie-instellingen van een zelfstandige Azure Service Fabric-cluster beschreven die kunnen worden ingesteld in de *ClusterConfig.jsvoor* het bestand. U gebruikt dit bestand om informatie op te geven over de knoop punten van het cluster, beveiligings configuraties en de netwerk topologie in termen van fout-en upgrade domeinen.  Nadat u configuratie-instellingen hebt gewijzigd of toegevoegd, kunt u [een zelfstandig cluster maken](service-fabric-cluster-creation-for-windows-server.md) of [de configuratie van een zelfstandig cluster bijwerken](service-fabric-cluster-config-upgrade-windows-server.md).

Wanneer u [het zelfstandige service Fabric-pakket downloadt](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), worden er ook ClusterConfig.jsop voor beelden opgenomen. De voor beelden die "DevCluster" in hun namen hebben, maken een cluster met alle drie knoop punten op dezelfde computer, met behulp van logische knoop punten. Uit deze knoop punten moet ten minste één als primair knoop punt zijn gemarkeerd. Dit type cluster is handig voor ontwikkel-en test omgevingen. Het wordt niet ondersteund als een productie cluster. De voor beelden die "meerdere machines" in hun namen hebben, kunnen productie cluster clusters maken, met elk knoop punt op een afzonderlijke computer. Het aantal primaire knoop punten voor deze clusters is gebaseerd op het [betrouwbaarheids niveau](#reliability)van het cluster. In Release 5,7, API-versie 05-2017, is de eigenschap betrouwbaarheids niveau verwijderd. In plaats daarvan berekent onze code het meest geoptimaliseerde betrouwbaarheids niveau voor uw cluster. Probeer geen waarde in te stellen voor deze eigenschap in versie 5,7 en hoger.

* ClusterConfig.Unsecure.DevCluster.jsop en ClusterConfig.Unsecure.MultiMachine.jsop weer geven hoe u een niet-beveiligd test-of productie cluster wilt maken.

* ClusterConfig.Windows.DevCluster.jsop en ClusterConfig.Windows.MultiMachine.jsop laten zien hoe u test-of productie clusters kunt maken die zijn beveiligd met [Windows-beveiliging](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.jsop en ClusterConfig.X509.MultiMachine.jsop weer geven hoe u test-of productie clusters kunt maken die zijn beveiligd met een [x509-certificaat beveiliging](service-fabric-windows-cluster-x509-security.md).

Laten we nu de verschillende secties van een ClusterConfig.jsin het bestand bekijken.

## <a name="general-cluster-configurations"></a>Algemene cluster configuraties
Algemene cluster configuraties dekken de brede cluster-specifieke configuraties, zoals wordt weer gegeven in het volgende JSON-fragment:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

U kunt uw Service Fabric-cluster een beschrijvende naam geven door het toe te wijzen aan de naam variabele. De clusterConfigurationVersion is het versie nummer van uw cluster. Verg root elke keer dat u uw Service Fabric cluster bijwerkt. Zorg ervoor dat apiVersion is ingesteld op de standaard waarde.

## <a name="nodes-on-the-cluster"></a>Knoop punten op het cluster
U kunt de knoop punten op uw Service Fabric cluster configureren met behulp van de knoop punten sectie, zoals in het volgende code fragment wordt weer gegeven:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Een Service Fabric-cluster moet ten minste drie knoop punten bevatten. U kunt op basis van uw instellingen meer knoop punten toevoegen aan deze sectie. In de volgende tabel worden de configuratie-instellingen voor elk knoop punt beschreven:

| **Knooppuntconfiguratie** | **Beschrijving** |
| --- | --- |
| nodeName |U kunt het knoop punt een beschrijvende naam geven. |
| IPAdres |Zoek het IP-adres van het knoop punt door een opdracht venster te openen en te typen `ipconfig` . Noteer het IPV4-adres en wijs dit toe aan de variabele iPAddress. |
| nodeTypeRef |Aan elk knoop punt kan een ander type knoop punt worden toegewezen. De [knooppunt typen](#node-types) worden gedefinieerd in de volgende sectie. |
| faultDomain |Met fout domeinen kunnen cluster beheerders de fysieke knoop punten definiëren die mogelijk op hetzelfde moment mislukken vanwege gedeelde fysieke afhankelijkheden. |
| Upgrade Domain |Met upgrade domeinen worden sets van knoop punten beschreven die op ongeveer hetzelfde moment worden afgesloten voor Service Fabric upgrades. U kunt kiezen welke knoop punten moeten worden toegewezen aan de upgrade domeinen, omdat deze niet zijn beperkt door fysieke vereisten. |

## <a name="cluster-properties"></a>Cluster eigenschappen
De sectie eigenschappen in de ClusterConfig.jsop wordt gebruikt om het cluster te configureren zoals wordt weer gegeven:

### <a name="reliability"></a>Betrouwbaarheid
Het concept van reliabilityLevel definieert het aantal replica's of exemplaren van de Service Fabric systeem services dat kan worden uitgevoerd op de primaire knoop punten van het cluster. Hiermee wordt de betrouw baarheid van deze services en daarom het cluster bepaald. De waarde wordt berekend door het systeem tijdens het maken van het cluster en de upgrade tijd.

### <a name="diagnostics"></a>Diagnostiek
In de sectie diagnosticsStore kunt u para meters configureren voor het inschakelen van diagnostische gegevens en het oplossen van knoop punten of cluster fouten, zoals wordt weer gegeven in het volgende code fragment: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

De meta gegevens zijn een beschrijving van uw cluster diagnostiek en kunnen worden ingesteld op basis van uw instellingen. Deze variabelen helpen bij het verzamelen van ETW-traceer logboeken en crash dumps en prestatie meter items. Zie [Tracelog](/windows-hardware/drivers/devtest/tracelog) en [etw-tracering](/dotnet/framework/wcf/samples/etw-tracing)voor meer informatie over etw-traceer Logboeken. Alle logboeken, met inbegrip van [crash dumps](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) en [prestatie meter items](/windows/win32/perfctrs/performance-counters-portal), kunnen worden omgeleid naar de map Connections Tring op uw computer. U kunt opslag ook gebruiken om diagnostische gegevens op te slaan. Raadpleeg het volgende voorbeeld fragment:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Beveiliging
De sectie Beveiliging is nodig voor een veilig zelfstandig Service Fabric cluster. Het volgende code fragment toont een deel van deze sectie:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

De meta gegevens zijn een beschrijving van uw beveiligde cluster en kunnen worden ingesteld op basis van uw instellingen. De ClusterCredentialType en ServerCredentialType bepalen het type beveiliging dat het cluster en de knoop punten implementeren. Ze kunnen worden ingesteld op *x.509* voor een beveiliging op basis van een certificaat of *Windows* voor beveiliging op basis van Active Directory. De rest van de sectie Beveiliging is gebaseerd op het type beveiliging. Zie [beveiliging op basis van certificaten in een zelfstandig cluster](service-fabric-windows-cluster-x509-security.md) of [Windows-beveiliging in een zelfstandig cluster](service-fabric-windows-cluster-windows-security.md)voor meer informatie over het invullen van de rest van de sectie beveiliging.

### <a name="node-types"></a>Knooppunt typen
In de sectie nodeTypes wordt het type knoop punten beschreven dat uw cluster heeft. Ten minste één knooppunt type moet worden opgegeven voor een cluster, zoals wordt weer gegeven in het volgende code fragment: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

De naam is de beschrijvende naam voor dit specifieke knooppunt type. Als u een knoop punt van dit knooppunt type wilt maken, wijst u de beschrijvende naam toe aan de variabele nodeTypeRef voor dat knoop punt, zoals [eerder is vermeld](#nodes-on-the-cluster). Definieer de verbindings eindpunten die worden gebruikt voor elk knooppunt type. U kunt elk poort nummer voor deze verbindings eindpunten kiezen, zolang ze niet conflicteren met andere eind punten in dit cluster. In een cluster met meerdere knoop punten bevinden zich een of meer primaire knoop punten (dat wil zeggen, isPrimary is ingesteld op *True*), afhankelijk van de [reliabilityLevel](#reliability). Zie [service Fabric overwegingen over het plannen van cluster capaciteit](service-fabric-cluster-capacity.md) voor informatie over NodeTypes en reliabilityLevel voor meer informatie over primaire en niet-primaire knooppunt typen. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Eind punten die worden gebruikt voor het configureren van de knooppunt typen
* clientConnectionEndpointPort is de poort die door de client wordt gebruikt om verbinding te maken met het cluster wanneer client-Api's worden gebruikt. 
* clusterConnectionEndpointPort is de poort waarin de knoop punten met elkaar communiceren.
* leaseDriverEndpointPort is de poort die wordt gebruikt door het cluster lease stuur programma om erachter te komen of de knoop punten nog actief zijn. 
* serviceConnectionEndpointPort is de poort die wordt gebruikt door de toepassingen en services die zijn geïmplementeerd op een knoop punt om te communiceren met de Service Fabric-client op het desbetreffende knoop punt.
* httpGatewayEndpointPort is de poort die wordt gebruikt door Service Fabric Explorer om verbinding te maken met het cluster.
* ephemeralPorts overschrijven de [dynamische poorten die door het besturings systeem worden gebruikt](https://support.microsoft.com/kb/929851). Service Fabric gebruikt een deel van deze poorten als toepassings poorten en het resterende onderdeel is beschikbaar voor het besturings systeem. Dit bereik wordt ook toegewezen aan het bestaande bereik in het besturings systeem, zodat u voor alle doel einden de bereiken kunt gebruiken die zijn opgegeven in de voor beeld-JSON-bestanden. Zorg ervoor dat het verschil tussen de begin-en eind poort ten minste 255 is. U kunt conflicten ondertreden als dit verschil te laag is, omdat dit bereik wordt gedeeld met het besturings systeem. Voer uit om het geconfigureerde dynamische poort bereik weer te geven `netsh int ipv4 show dynamicport tcp` .
* applicationPorts zijn de poorten die worden gebruikt door de Service Fabric-toepassingen. Het bereik van de toepassings poort moet groot genoeg zijn om de eindpunt vereiste van uw toepassingen te kunnen voorzien. Dit bereik moet exclusief zijn van het dynamische poort bereik op de computer, dat wil zeggen, het ephemeralPorts-bereik dat is ingesteld in de configuratie. Service Fabric gebruikt deze poorten wanneer er nieuwe poorten zijn vereist en de firewall voor deze poorten wordt geopend. 
* reverseProxyEndpointPort is een optioneel reverse-proxy-eind punt. Zie [service Fabric reverse proxy](service-fabric-reverseproxy.md)voor meer informatie. 

### <a name="log-settings"></a>Logboek instellingen
In de sectie fabricSettings kunt u de hoofd mappen voor de Service Fabric gegevens en logboeken instellen. U kunt deze directory's pas aanpassen tijdens het maken van het cluster. Raadpleeg het volgende voorbeeld fragment van deze sectie:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

U wordt aangeraden een niet-OS-station als FabricDataRoot en FabricLogRoot te gebruiken. Het biedt meer betrouw baarheid bij het vermijden van situaties wanneer het besturings systeem niet meer reageert. Als u alleen de hoofdmap van de gegevens aanpast, wordt de hoofdmap van het logboek één niveau onder de hoofdmap van de gegevens geplaatst.

### <a name="stateful-reliable-services-settings"></a>Stateful Reliable Services-instellingen
In de sectie KtlLogger kunt u de globale configuratie-instellingen voor Reliable Services instellen. Zie [stateful reliable Services configureren](service-fabric-reliable-services-configuration.md)voor meer informatie over deze instellingen. In het volgende voor beeld ziet u hoe u het gedeelde transactie logboek wijzigt dat wordt gemaakt om back-ups te maken van betrouw bare verzamelingen voor stateful Services:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Invoeg toepassingen
Als u invoeg toepassingen wilt configureren, configureert u de apiVersion als 04-2017 of hoger en configureert u de addonFeatures zoals hier wordt weer gegeven:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Alle beschik bare invoeg toepassingen kunnen worden weer gegeven in de [Naslag informatie over Service Fabric rest API](/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Ondersteuning voor containers
Als u container ondersteuning wilt inschakelen voor zowel Windows Server-containers als Hyper-V-containers voor zelfstandige clusters, moet u de functie voor DNS toevoegen inschakelen.

## <a name="next-steps"></a>Volgende stappen
Nadat u een volledigClusterConfig.jshebt voor het bestand dat is geconfigureerd *op basis van* uw zelfstandige cluster installatie, kunt u uw cluster implementeren. Volg de stappen in [een zelfstandige service Fabric cluster maken](service-fabric-cluster-creation-for-windows-server.md). 

Als u een zelfstandig cluster hebt geïmplementeerd, kunt u ook [de configuratie van een zelfstandig cluster bijwerken](service-fabric-cluster-config-upgrade-windows-server.md). 

Meer informatie over [het visualiseren van uw cluster met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
