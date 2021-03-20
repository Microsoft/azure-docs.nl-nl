---
title: De Azure Service Fabric-versie van een cluster upgraden
description: Voer een upgrade uit voor de Service Fabric code en/of configuratie waarmee een Service Fabric cluster wordt uitgevoerd, met inbegrip van het instellen van de cluster update modus, het bijwerken van certificaten, het toevoegen van toepassings poorten, het uitvoeren van besturingssysteem patches, enzovoort. Wat kunt u verwachten wanneer de upgrades worden uitgevoerd?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 01fe916f0ee78c8481ac6b17b8f7409b47c852ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564284"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Upgrade uitvoeren voor de Service Fabric-versie van een cluster

Voor elk modern systeem is het ontwerpen van een hoge mate van omvang voor het succes van uw product op lange termijn. Een Azure Service Fabric-cluster is een resource waarvan u de eigenaar bent, maar wordt gedeeltelijk beheerd door micro soft. In dit artikel wordt beschreven hoe u een upgrade uitvoert van de versie van Service Fabric die in uw Azure-cluster wordt uitgevoerd.

U kunt uw cluster zo instellen dat automatische infrastructuur upgrades worden ontvangen wanneer deze door micro soft worden uitgebracht of u kunt een ondersteunde infrastructuur versie selecteren waarop u het cluster wilt uitvoeren.

U doet dit door de cluster configuratie ' upgrade mode ingesteld ' in te stellen op de portal of door Resource Manager te gebruiken op het moment van maken of later op een live cluster 

> [!NOTE]
> Zorg ervoor dat uw cluster altijd een ondersteunde infrastructuur versie gebruikt. Wanneer we de release van een nieuwe versie van service Fabric aankondigen, wordt de vorige versie gemarkeerd voor het einde van de ondersteuning na mini maal 60 dagen vanaf die datum. De nieuwe releases worden aangekondigd [op het service Fabric-team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). De nieuwe versie is beschikbaar om te kiezen. 
> 
> 

14 dagen vóór het verstrijken van de release van het cluster wordt uitgevoerd, wordt er een status gebeurtenis gegenereerd waardoor het cluster een status waarschuwing krijgt. Het cluster behoudt een waarschuwings status totdat u een upgrade naar een ondersteunde infrastructuur versie uitvoert.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>De upgrade modus instellen in de Azure Portal
U kunt het cluster instellen op automatisch of hand matig wanneer u het cluster maakt.

![Scherm afbeelding toont het deel venster Service Fabric cluster maken met optie 2 cluster configuratie geselecteerd en het deel venster cluster configuratie geopend.][Create_Manualmode]

U kunt het cluster instellen op automatisch of hand matig bij een live cluster, met behulp van de ervaring beheren. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Een upgrade uitvoeren naar een nieuwe versie in een cluster dat is ingesteld op hand matige modus via de portal.
Als u een upgrade wilt uitvoeren naar een nieuwe versie, hoeft u alleen maar de beschik bare versie in de vervolg keuzelijst te selecteren en op te slaan. De upgrade van de infra structuur wordt automatisch gestart. Het cluster status beleid (een combi natie van de status van het knoop punt en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt tijdens de upgrade gerespecteerd.

Als niet aan het status beleid van het cluster wordt voldaan, wordt de upgrade teruggedraaid. Schuif omlaag in dit document voor meer informatie over het instellen van die aangepaste status beleidsregels. 

Zodra u de problemen hebt opgelost die het terugdraaien hebben veroorzaakt, moet u de upgrade opnieuw starten door dezelfde stappen te volgen als voorheen.

![Scherm afbeelding toont het venster Service Fabric clusters met het deel venster infrastructuur upgrades geopend en de upgrade opties gemarkeerd, inclusief automatisch en hand matig.][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>De upgrade modus instellen met behulp van een resource manager-sjabloon
Voeg de configuratie ' upgrade mode ingesteld ' toe aan de resource definitie voor micro soft. ServiceFabric/clusters en stel de ' clusterCodeVersion ' in op een van de ondersteunde infrastructuur versies, zoals hieronder wordt weer gegeven en implementeer de sjabloon. De geldige waarden voor ' upgrade mode ingesteld ' zijn ' hand matig ' of ' automatisch '

![In de scherm afbeelding wordt een sjabloon weer gegeven. Dit is een Inge sprongen tekst zonder opmaak om de structuur weer te geven en de clusterCodeVersion en upgrade mode ingesteld zijn gemarkeerd.][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Een upgrade uitvoeren naar een nieuwe versie op een cluster dat is ingesteld op hand matige modus via een resource manager-sjabloon.
Wanneer het cluster zich in de hand matige modus bevindt, wijzigt u de "clusterCodeVersion" in een ondersteunde versie en implementeert u deze in een nieuwe versie. De implementatie van de sjabloon, het starten van de Fabric-upgrade wordt automatisch gestart. Het cluster status beleid (een combi natie van de status van het knoop punt en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt tijdens de upgrade gerespecteerd.

Als niet aan het status beleid van het cluster wordt voldaan, wordt de upgrade teruggedraaid.  

Zodra u de problemen hebt opgelost die het terugdraaien hebben veroorzaakt, moet u de upgrade opnieuw starten door dezelfde stappen te volgen als voorheen.

## <a name="set-custom-health-polices-for-upgrades"></a>Aangepaste status policies instellen voor upgrades
U kunt aangepaste status policies opgeven voor de upgrade van de infra structuur. Als u uw cluster hebt ingesteld op automatische infrastructuur upgrades, worden deze beleids regels toegepast op [fase 1 van de automatische infrastructuur upgrades](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Als u uw cluster hebt ingesteld voor hand matige infrastructuur upgrades, worden deze beleids regels toegepast telkens wanneer u een nieuwe versie selecteert die het systeem activeert om de infrastructuur upgrade in uw cluster te starten. Als u het beleid niet overschrijft, worden de standaard waarden gebruikt.

U kunt het aangepaste status beleid opgeven of de huidige instellingen controleren onder de Blade upgrade van infrastructuur resources door de geavanceerde upgrade-instellingen te selecteren. Bekijk de volgende afbeelding voor instructies. 

![Aangepast status beleid beheren][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Alle beschik bare versies weer geven voor alle omgevingen voor een bepaald abonnement
Voer de volgende opdracht uit en u ziet een uitvoer die er ongeveer als volgt uitziet.

"supportExpiryUtc" vertelt uw wanneer een bepaalde release verloopt of is verlopen. De meest recente release heeft geen geldige datum: deze heeft de waarde ' 9999-12-31T23:59:59.9999999 '. Dit betekent alleen dat de verval datum nog niet is ingesteld.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

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
