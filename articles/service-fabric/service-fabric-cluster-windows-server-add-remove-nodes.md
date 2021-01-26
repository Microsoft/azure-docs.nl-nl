---
title: Knoop punten toevoegen aan of verwijderen uit een zelfstandig Service Fabric cluster
description: Meer informatie over het toevoegen of verwijderen van knoop punten aan een Azure Service Fabric-cluster op een fysieke of virtuele machine met Windows Server, die on-premises of in elke Cloud kan zijn.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 26945b4785a0591d997139f2427b0ae6b59fa742
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790593"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Knooppunten toevoegen aan of verwijderen uit een zelfstandig Service Fabric-cluster dat wordt uitgevoerd op Windows Server
Nadat u [uw zelfstandige service Fabric cluster op Windows Server-computers hebt gemaakt](service-fabric-cluster-creation-for-windows-server.md), kan het nodig zijn om uw (zakelijke)-clusters te wijzigen en moet u knoop punten aan uw cluster toevoegen of eruit verwijderen, zoals wordt beschreven in dit artikel.

> [!NOTE]
> De functionaliteit voor het toevoegen en verwijderen van knoop punten wordt niet ondersteund in lokale ontwikkel clusters.

## <a name="add-nodes-to-your-cluster"></a>Knoop punten toevoegen aan uw cluster

1. Bereid de virtuele machine die u wilt toevoegen aan uw cluster voor door de stappen te volgen die worden beschreven in het [plannen en voorbereiden van de implementatie van het service Fabric-cluster](service-fabric-cluster-standalone-deployment-preparation.md).

2. Bepaal welk fout domein en welk upgrade domein u deze VM/machine gaat toevoegen aan.

   Als u certificaten gebruikt voor het beveiligen van het cluster, worden de certificaten naar verwachting geïnstalleerd in de lokale certificaat archieven in de voor bereiding van het knoop punt om lid te worden van het cluster. De analoge is van toepassing wanneer u andere vormen van beveiliging gebruikt.

3. Extern bureau blad (RDP) naar de virtuele machine/machine die u aan het cluster wilt toevoegen.

4. Kopieer of [down load het zelfstandige pakket voor service Fabric voor Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) naar de VM/machine en pak het pakket uit.

5. Voer Power shell uit met verhoogde bevoegdheden en ga naar de locatie van het uitgepakte pakket.

6. Voer het *AddNode.ps1* script uit met de para meters die het nieuwe knoop punt beschrijven dat moet worden toegevoegd. In het volgende voor beeld wordt een nieuw knoop punt met de naam VM5, met het type NodeType0 en IP-adres 182.17.34.52, toegevoegd aan UD1 en FD:/DC1/R0. `ExistingClusterConnectionEndPoint` is een verbindings eindpunt voor een knoop punt dat al aanwezig is in het bestaande cluster. Dit kan het IP-adres zijn van *een wille keurig* knoop punt in het cluster. 

   Niet beveiligd (prototypen):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Beveiligd (op basis van een certificaat):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Wanneer het uitvoeren van het script is voltooid, kunt u controleren of het nieuwe knoop punt is toegevoegd door de cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) uit te voeren.

7. Om consistentie tussen de verschillende knoop punten in het cluster te garanderen, moet u een configuratie-upgrade starten. Voer [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) uit om het meest recente configuratie bestand op te halen en voeg het zojuist toegevoegde knoop punt toe aan de sectie knoop punten. Het is ook raadzaam om altijd de meest recente cluster configuratie beschikbaar te hebben voor het geval u een cluster met dezelfde configuratie opnieuw moet implementeren.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Voer [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) uit om de upgrade te starten.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   U kunt de voortgang van de upgrade op Service Fabric Explorer bewaken. U kunt ook [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)uitvoeren.

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Knoop punten toevoegen aan clusters die zijn geconfigureerd met Windows-beveiliging met behulp van gMSA
Voor clusters die zijn geconfigureerd met een door een groep beheerd service account (gMSA) ( https://technet.microsoft.com/library/hh831782.aspx) kan een nieuw knoop punt worden toegevoegd met behulp van een configuratie-upgrade:
1. Voer [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) uit op een van de bestaande knoop punten om het meest recente configuratie bestand op te halen en Voeg details toe over het nieuwe knoop punt dat u wilt toevoegen in de sectie ' knoop punten '. Zorg ervoor dat het nieuwe knoop punt deel uitmaakt van hetzelfde beheerde account van de groep. Dit account moet een beheerder op alle computers zijn.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Voer [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) uit om de upgrade te starten.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    U kunt de voortgang van de upgrade op Service Fabric Explorer bewaken. U kunt ook [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) uitvoeren

### <a name="add-node-types-to-your-cluster"></a>Knooppunt typen toevoegen aan uw cluster
Als u een nieuw knooppunt type wilt toevoegen, wijzigt u de configuratie zodat het nieuwe knooppunt type wordt vermeld in de sectie ' NodeTypes ' onder ' Eigenschappen ' en begint u met het uitvoeren van een configuratie-upgrade met [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Zodra de upgrade is voltooid, kunt u nieuwe knoop punten aan uw cluster toevoegen met dit knooppunt type.

## <a name="remove-nodes-from-your-cluster"></a>Knoop punten uit uw cluster verwijderen
Een knoop punt kan worden verwijderd uit een cluster met behulp van een configuratie-upgrade, op de volgende manier:

1. Voer [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) uit om het meest recente configuratie bestand op te halen en *Verwijder* het knoop punt uit de sectie knoop punten.
Voeg de para meter ' NodesToBeRemoved ' toe aan de sectie ' Setup ' in de sectie ' FabricSettings '. De ' waarde ' moet een door komma's gescheiden lijst zijn met knooppunt namen van knoop punten die moeten worden verwijderd.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Voer [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) uit om de upgrade te starten.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    U kunt de voortgang van de upgrade op Service Fabric Explorer bewaken. U kunt ook [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)uitvoeren.

> [!NOTE]
> Het verwijderen van knoop punten kan meerdere upgrades initiëren. Sommige knoop punten zijn gemarkeerd met `IsSeedNode=”true”` tag en kunnen worden geïdentificeerd door het cluster manifest te doorzoeken met `Get-ServiceFabricClusterManifest` . Het verwijderen van deze knoop punten kan langer duren dan andere omdat de Seed-knoop punten in dergelijke scenario's moeten worden verplaatst. Het cluster moet mini maal drie knoop punten van het primaire knooppunt type onderhouden.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Knooppunt typen uit uw cluster verwijderen
Controleer voordat u een knooppunt type verwijdert of er knoop punten zijn die verwijzen naar het knooppunt type. Verwijder deze knoop punten voordat u het bijbehorende knooppunt type verwijdert. Als alle bijbehorende knoop punten zijn verwijderd, kunt u het NodeType verwijderen uit de cluster configuratie en een configuratie-upgrade starten met [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).


### <a name="replace-primary-nodes-of-your-cluster"></a>Primaire knoop punten van het cluster vervangen
De vervanging van primaire knoop punten moet na een andere knoop punt worden uitgevoerd, in plaats van verwijderen en vervolgens in batches toe te voegen.


## <a name="next-steps"></a>Volgende stappen
* [Configuratie-instellingen voor zelfstandig Windows-cluster](service-fabric-cluster-manifest.md)
* [Een zelfstandige cluster in Windows beveiligen met x509-certificaten](service-fabric-windows-cluster-x509-security.md)
* [Een zelfstandig Service Fabric cluster maken met virtuele Azure-machines waarop Windows wordt uitgevoerd](./service-fabric-cluster-creation-via-arm.md)
