---
title: De configuratie van een zelfstandig cluster bijwerken
description: Meer informatie over het bijwerken van de configuratie waarmee een zelfstandig Service Fabric cluster wordt uitgevoerd.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 3cb1d40f5b32415588d3fd5a108967cfb4c0e534
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842611"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>De configuratie van een zelfstandig cluster bijwerken 

Voor elk modern systeem is de mogelijkheid om een upgrade uit te kunnen zetten van de lange termijn voor het succes van uw product. Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent. In dit artikel wordt beschreven hoe u de configuratie-instellingen van uw zelfstandige Service Fabric cluster bijwerkt.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Cluster instellingen aanpassen in de ClusterConfig.jsvoor het bestand
Zelfstandige clusters worden geconfigureerd via de *ClusterConfig.jsvoor* het bestand. Zie [configuratie-instellingen voor een zelfstandig Windows-cluster](service-fabric-cluster-manifest.md)voor meer informatie over de verschillende instellingen.

U kunt instellingen toevoegen, bijwerken of verwijderen in de `fabricSettings` sectie onder het gedeelte [cluster eigenschappen](./service-fabric-cluster-manifest.md#cluster-properties) in *ClusterConfig.jsop*. 

De volgende JSON voegt bijvoorbeeld een nieuwe instelling *MaxDiskQuotaInMB* toe aan de sectie *Diagnostische gegevens* onder `fabricSettings` :

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Nadat u de instellingen in uw ClusterConfig.jsin het bestand hebt gewijzigd, [test u de cluster configuratie](#test-the-cluster-configuration) en voert u vervolgens [een upgrade uit voor de cluster configuratie](#upgrade-the-cluster-configuration) om de instellingen toe te passen op uw cluster. 

## <a name="test-the-cluster-configuration"></a>De cluster configuratie testen
Voordat u de configuratie-upgrade initieert, kunt u de nieuwe JSON van de cluster configuratie testen door het volgende Power shell-script uit te voeren in het zelfstandige pakket:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Of gebruik dit script:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Sommige configuraties kunnen niet worden bijgewerkt, zoals eind punten, cluster naam, knooppunt-IP, enzovoort. De nieuwe JSON van de cluster configuratie wordt getest op basis van de oude en genereert fouten in het Power shell-venster als er een probleem is.

## <a name="upgrade-the-cluster-configuration"></a>De cluster configuratie upgraden
Voer [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)uit om de upgrade van de cluster configuratie bij te werken. De upgrade van de configuratie wordt verwerkt op basis van het upgrade domein.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Cluster certificaat configuratie bijwerken
Er wordt een cluster certificaat gebruikt voor verificatie tussen cluster knooppunten. De overschakeling van het certificaat moet extra voorzichtig zijn omdat fout de communicatie blokkeert tussen cluster knooppunten.

Er worden vier opties ondersteund:  

* Upgrade van één certificaat: het upgradepad is certificaat A (primair)-> certificaat B (primair)-> certificaat C (primair)->....

* Dubbele certificaat upgrade: het upgradepad is certificaat A (primair)-> certificaat A (primair) en B (secundair)-> certificaat B (primair): > certificaat B (primair) en C (secundair)-> certificaat C (primair)->....

* Upgrade van certificaat type: certificaat configuratie op basis van een vinger afdruk <-> certificaat configuratie op basis van algemene certificaten. Bijvoorbeeld: certificaat vingerafdruk A (primair) en vinger afdruk B (secundair)-> certificaat Commonnaam C.

* Vinger afdruk upgrade van certificaat verlener: het upgradepad is certificaat CN = A, IssuerThumbprint = IT1 (Primary)-> Certificate CN = A, IssuerThumbprint = IT1, IT2 (Primary)-> certificaat CN = A, IssuerThumbprint = IT2 (Primary).


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het aanpassen van sommige [service Fabric cluster instellingen](service-fabric-cluster-fabric-settings.md).
* Meer informatie over hoe u [uw cluster in-en uitschaalt](service-fabric-cluster-scale-in-out.md).
* Meer informatie over [toepassings upgrades](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
