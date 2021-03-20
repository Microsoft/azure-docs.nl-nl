---
title: De versie van een zelfstandig cluster bijwerken
description: Voer een upgrade uit voor de Azure Service Fabric-code die een zelfstandig Service Fabric cluster uitvoert.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842934"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Een upgrade uitvoeren van de Service Fabric versie die wordt uitgevoerd op uw cluster 

Voor elk modern systeem is de mogelijkheid om een upgrade uit te kunnen zetten van de lange termijn voor het succes van uw product. Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent. In dit artikel wordt beschreven hoe u een upgrade uitvoert van de versie van Service Fabric die op uw zelfstandige cluster wordt uitgevoerd.

> [!NOTE]
> Zorg ervoor dat uw cluster altijd een ondersteunde Service Fabric versie uitvoert. Wanneer micro soft de release van een nieuwe versie van Service Fabric aankondigt, wordt de vorige versie gemarkeerd voor het einde van de ondersteuning na mini maal 60 dagen na de datum van de aankondiging. Nieuwe releases worden aangekondigd [op het service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). De nieuwe versie kan op dat moment worden gekozen.
>
>

U kunt uw cluster alleen upgraden naar de nieuwe versie als u een configuratie van een knoop punt voor productie stijl gebruikt, waarbij elk Service Fabric knoop punt wordt toegewezen op een afzonderlijke fysieke of virtuele machine. Als u een ontwikkel cluster hebt, waarbij meer dan één Service Fabric knoop punt zich op één fysieke of virtuele machine bevindt, moet u het cluster opnieuw maken met de nieuwe versie.

Met twee afzonderlijke werk stromen kan uw cluster worden bijgewerkt naar de nieuwste versie of een ondersteunde Service Fabric versie. Een werk stroom is voor clusters die verbinding hebben om de nieuwste versie automatisch te downloaden. De andere werk stroom is voor clusters die geen verbinding hebben om de nieuwste Service Fabric-versie te downloaden.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Automatisch bijwerken inschakelen van de Service Fabric versie van uw cluster
Stel de `fabricClusterAutoupgradeEnabled` cluster configuratie in op *waar* om het cluster zo in te stellen dat updates van service Fabric worden gedownload wanneer micro soft een nieuwe versie loslaat. Als u hand matig een ondersteunde versie van Service Fabric wilt selecteren waarvan u het cluster wilt maken, stelt u de `fabricClusterAutoupgradeEnabled` cluster configuratie in op *Onwaar*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Clusters bijwerken die verbinding hebben om de nieuwste code en configuratie te downloaden
Gebruik deze stappen om uw cluster bij te werken naar een ondersteunde versie als uw cluster knooppunten een Internet verbinding met het [micro soft Download centrum](https://download.microsoft.com)hebben.

Voor clusters met een verbinding met het [micro soft Download centrum](https://download.microsoft.com)controleert micro soft regel matig of er nieuwe service Fabric versies beschikbaar zijn.

Wanneer een nieuwe Service Fabric-versie beschikbaar is, wordt het pakket lokaal gedownload naar het cluster en ingericht voor de upgrade. Om de klant van deze nieuwe versie hiervan op de hoogte te brengen, toont het systeem een expliciete cluster status waarschuwing die er ongeveer als volgt uitziet:

De ondersteuning voor de huidige cluster versie [Version #] eindigt [date]. "

Nadat het cluster de nieuwste versie heeft uitgevoerd, verdwijnt de waarschuwing.

Wanneer u de cluster status waarschuwing ziet, werkt u het cluster bij:

1. Maak verbinding met het cluster vanaf elke computer met beheerders toegang tot alle computers die worden vermeld als knoop punten in het cluster. De computer waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Haal de lijst met Service Fabric-versies op waarnaar u een upgrade kunt uitvoeren.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    U ziet een uitvoer die er ongeveer als volgt uitziet:

    ![Service Fabric versies ophalen][getfabversions]
3. Start een cluster upgrade naar een beschik bare versie met behulp van de [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows Power shell-opdracht.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Als u de voortgang van de upgrade wilt bewaken, kunt u Service Fabric Explorer gebruiken of de volgende Power shell-opdracht uitvoeren:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Als niet aan het status beleid van het cluster wordt voldaan, wordt de upgrade teruggedraaid. Zie de documentatie voor [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)als u een aangepast status beleid wilt opgeven voor de Start-ServiceFabricClusterUpgrade opdracht.

    Nadat u de problemen hebt opgelost die tot het terugdraaien hebben geleid, start u de upgrade opnieuw door dezelfde stappen te volgen als hierboven beschreven.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Clusters bijwerken die *geen verbinding* hebben om de nieuwste code en configuratie te downloaden
Gebruik deze stappen om uw cluster bij te werken naar een ondersteunde versie als uw cluster knooppunten geen Internet verbinding met het [micro soft Download centrum](https://download.microsoft.com)hebben.

> [!NOTE]
> Als u een cluster gebruikt dat niet is verbonden met internet, moet u de [service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) bewaken om meer te weten te komen over nieuwe releases. Er wordt in het systeem geen cluster status waarschuwing weer gegeven om u te waarschuwen over nieuwe releases.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatisch inrichten versus hand matige inrichting
Als u automatisch downloaden en registreren voor de nieuwste code versie wilt inschakelen, stelt u de Service Fabric Update service in. Zie *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* in het [zelfstandige pakket](service-fabric-cluster-standalone-package-contents.md)voor instructies.

Volg deze instructies voor het hand matige proces.

Wijzig de cluster configuratie om de volgende eigenschap in te stellen op *False* voordat u een configuratie-upgrade start:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Zie de Power shell-opdracht [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) voor meer informatie over het gebruik. Zorg ervoor dat u ' clusterConfigurationVersion ' in uw JSON bijwerkt voordat u de configuratie-upgrade start.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Upgrade werk stroom voor cluster

1. Voer [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) uit vanaf een van de knoop punten in het cluster en noteer de *TargetCodeVersion*.

2. Voer de volgende stappen uit vanaf een computer met een Internet verbinding om alle versie-compatibele versies met de huidige versie weer te geven en down load het bijbehorende pakket van de bijbehorende Download koppelingen:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Maak verbinding met het cluster vanaf elke computer met beheerders toegang tot alle computers die worden vermeld als knoop punten in het cluster. De computer waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Kopieer het gedownloade pakket naar het cluster installatie kopie archief.

5. Registreer het gekopieerde pakket.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Een upgrade van een cluster naar een beschik bare versie starten.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    U kunt de voortgang van de upgrade op Service Fabric Explorer controleren of u kunt de volgende Power shell-opdracht uitvoeren:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Als niet aan het status beleid van het cluster wordt voldaan, wordt de upgrade teruggedraaid. Zie de documentatie voor [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)als u een aangepast status beleid wilt opgeven voor de Start-ServiceFabricClusterUpgrade opdracht.

    Nadat u de problemen hebt opgelost die tot het terugdraaien hebben geleid, start u de upgrade opnieuw door dezelfde stappen te volgen als hierboven beschreven.

## <a name="next-steps"></a>Volgende stappen
* [De configuratie van een zelfstandig cluster bijwerken](service-fabric-cluster-config-upgrade-windows-server.md)
* Pas enkele [service Fabric cluster instellingen](service-fabric-cluster-fabric-settings.md)aan.
* [Schaal uw cluster in en uit](service-fabric-cluster-scale-in-out.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
