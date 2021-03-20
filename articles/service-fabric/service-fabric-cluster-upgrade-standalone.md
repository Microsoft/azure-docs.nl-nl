---
title: Een zelfstandige Azure Service Fabric-cluster upgraden
description: Meer informatie over het bijwerken van de versie of configuratie van een zelfstandige Azure Service Fabric-cluster.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 519a9163d16fda2dd9fcf49cf22fe4ad4a272e09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260988"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Een Service Fabric zelfstandige cluster bijwerken en bijwerken

Voor elk modern systeem is het ontwerpen van een hoge mate van omvang voor het succes van uw product op lange termijn. Een zelfstandige Azure Service Fabric cluster is een bron waarvan u de eigenaar bent. In dit artikel wordt beschreven wat er kan worden bijgewerkt of bijgewerkt.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>De infrastructuur versie beheren die op uw cluster wordt uitgevoerd
Zorg ervoor dat uw cluster altijd een [ondersteunde service Fabric versie](service-fabric-versions.md)uitvoert. Wanneer micro soft de release van een nieuwe versie van Service Fabric aankondigt, wordt de vorige versie gemarkeerd voor het einde van de ondersteuning na mini maal 60 dagen na de datum van de aankondiging. Nieuwe releases worden aangekondigd [op het service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). De nieuwe versie kan op dat moment worden gekozen.

U kunt uw cluster zo instellen dat automatische infrastructuur upgrades worden ontvangen wanneer deze door micro soft worden uitgebracht of u kunt hand matig een ondersteunde infrastructuur versie selecteren waarop u het cluster wilt maken. Lees voor meer informatie [de service Fabric-versie die op uw cluster wordt uitgevoerd, bijwerken](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Configuratie-instellingen aanpassen

Er kunnen veel verschillende [configuratie-instellingen](service-fabric-cluster-manifest.md) worden ingesteld in de *ClusterConfig.jsvoor* het bestand, zoals het betrouwbaarheids niveau van het cluster en de knooppunt eigenschappen.  Lees voor meer informatie [de configuratie van een zelfstandig cluster bijwerken](service-fabric-cluster-config-upgrade-windows-server.md).  Veel andere, geavanceerdere instellingen kunnen ook worden aangepast.  Lees [service Fabric cluster Fabric-instellingen](service-fabric-cluster-fabric-settings.md)voor meer informatie.

## <a name="define-node-properties"></a>Knooppunt eigenschappen definiëren
Soms wilt u ervoor zorgen dat bepaalde werk belastingen alleen worden uitgevoerd op bepaalde typen knoop punten in het cluster. Een voor beeld: een bepaalde werk belasting kan Gpu's of Ssd's vereisen, terwijl anderen dat niet mogelijk is. Voor elk van de knooppunt typen in een cluster kunt u aangepaste knooppunt eigenschappen toevoegen aan cluster knooppunten. Plaatsings beperkingen zijn de instructies die zijn gekoppeld aan afzonderlijke services die worden geselecteerd voor een of meer knooppunt eigenschappen. Plaatsings beperkingen bepalen waar services moeten worden uitgevoerd.

Lees de [knooppunt eigenschappen en plaatsings beperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)voor meer informatie over het gebruik van plaatsings beperkingen, eigenschappen van knoop punten en hoe u deze definieert.
 

## <a name="add-capacity-metrics"></a>Metrische gegevens over capaciteit toevoegen
Voor elk van de typen knoop punten kunt u aangepaste metrische gegevens voor capaciteit toevoegen die u in uw toepassingen wilt gebruiken om de belasting te rapporteren. Raadpleeg de Service Fabric cluster resource manager-documenten voor het [beschrijven van uw cluster](service-fabric-cluster-resource-manager-cluster-description.md) en [metrische gegevens en belasting](service-fabric-cluster-resource-manager-metrics.md)voor meer informatie over het gebruik van metrische gegevens over capaciteit voor het rapporteren van de belasting.

## <a name="patch-the-os-in-the-cluster-nodes"></a>Het besturings systeem in de cluster knooppunten bijwerken
De patch Orchestration Application (POA) is een Service Fabric-toepassing waarmee het besturings systeem patches op een Service Fabric cluster zonder downtime wordt geautomatiseerd. De [patch Orchestration-toepassing voor Windows](service-fabric-patch-orchestration-application.md) kan worden geïmplementeerd in uw cluster om patches te installeren op een gegroepeerde manier terwijl de services steeds beschikbaar blijven. 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het aanpassen van een aantal van de [service Fabric-cluster infrastructuur instellingen](service-fabric-cluster-fabric-settings.md)
* Meer informatie over hoe u [uw cluster in-en uitschaalt](service-fabric-cluster-scale-in-out.md)
* Meer informatie over [toepassings upgrades](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
