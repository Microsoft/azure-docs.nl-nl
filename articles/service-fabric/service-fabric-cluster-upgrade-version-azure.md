---
title: Service Fabric cluster upgrades beheren
description: Beheren wanneer en hoe uw Service Fabric cluster runtime wordt bijgewerkt
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731156"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Service Fabric cluster upgrades beheren

Een Azure Service Fabric-cluster is een bron die u bezit, maar is gedeeltelijk beheerd door micro soft. Hier leest u hoe u kunt beheren wanneer en hoe micro soft uw Azure Service Fabric-cluster bijwerkt.

Zie [Azure service Fabric-clusters upgraden en bijwerken](service-fabric-cluster-upgrade.md) voor meer achtergrond informatie over concepten en processen voor cluster upgrades.

## <a name="set-upgrade-mode"></a>Upgrade modus instellen

U kunt uw cluster zo instellen dat automatische Service Fabric upgrades worden ontvangen wanneer deze door micro soft worden uitgebracht, of u kunt hand matig kiezen uit een lijst met momenteel ondersteunde versies door de upgrade modus voor uw cluster in te stellen. U kunt dit doen via het besturings element *infrastructuur upgrade modus* in azure portal of de `upgradeMode` instelling in uw cluster implementatie sjabloon.

### <a name="azure-portal"></a>Azure Portal

Met Azure Portal kunt u kiezen tussen automatische of hand matige upgrades wanneer u een nieuw Service Fabric cluster maakt.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Kiezen tussen automatische of hand matige upgrades bij het maken van een nieuw cluster in Azure Portal vanuit de opties voor Geavanceerd":::

U kunt ook scha kelen tussen automatische of hand matige upgrades van het onderdeel **infrastructuur upgrades** van een bestaande cluster bron.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Selecteer Automatische of hand matige upgrades in de sectie ' infrastructuur upgrades ' van uw cluster bron in Azure Portal":::

### <a name="manual-upgrades-with-azure-portal"></a>Hand matige upgrades met Azure Portal

Wanneer u de optie hand matige upgrade selecteert, hoeft u alleen maar een upgrade te starten in de vervolg keuzelijst beschik bare versies en vervolgens *Opslaan* te selecteren. Vanaf daar wordt de upgrade van het cluster onmiddellijk uitgevoerd.

Het [cluster status beleid](#custom-policies-for-manual-upgrades) (een combi natie van de status van het knoop punt en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt tijdens de upgrade gerespecteerd. Als niet aan het cluster status beleid wordt voldaan, wordt de upgrade teruggedraaid.

Zodra u de problemen hebt opgelost die tot het terugdraaien hebben geleid, moet u de upgrade opnieuw starten door dezelfde stappen te volgen als voorheen.

### <a name="resource-manager-template"></a>Resource Manager-sjabloon

Als u de upgrade modus voor het cluster wilt wijzigen met behulp van een resource manager-sjabloon, geeft u *automatisch* of *hand matig* op voor de  `upgradeMode` eigenschap van de resource definitie *micro soft. ServiceFabric/clusters* . Als u hand matig bijwerken kiest, stelt u ook de `clusterCodeVersion` in op een momenteel [ondersteunde infrastructuur versie](#query-for-supported-cluster-versions).

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="In de scherm afbeelding wordt een sjabloon weer gegeven. Dit is een Inge sprongen tekst waarbij de structuur wordt weer gegeven. De eigenschappen ' clusterCodeVersion ' en ' upgrade mode ingesteld ' zijn gemarkeerd.":::

Wanneer de implementatie van de sjabloon is geslaagd, worden wijzigingen in de cluster upgrade modus toegepast. Als uw cluster zich in de hand matige modus bevindt, wordt de upgrade van het cluster automatisch gestart.

Het [cluster status beleid](#custom-policies-for-manual-upgrades) (een combi natie van de status van het knoop punt en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt tijdens de upgrade gerespecteerd. Als niet aan het cluster status beleid wordt voldaan, wordt de upgrade teruggedraaid.

Zodra u de problemen hebt opgelost die tot het terugdraaien hebben geleid, moet u de upgrade opnieuw starten door dezelfde stappen te volgen als voorheen.

## <a name="wave-deployment-for-automatic-upgrades"></a>Wave-implementatie voor automatische upgrades

Met de automatische upgrade modus hebt u de mogelijkheid om uw cluster in te scha kelen voor de implementatie van Wave. Met een Wave-implementatie kunt u een pijp lijn maken voor het upgraden van uw test-, fase-en productie clusters, gescheiden door ingebouwde ' maken-tijd ' om aanstaande Service Fabric versies te valideren voordat uw productie clusters worden bijgewerkt.

### <a name="enable-wave-deployment"></a>Wave-implementatie inschakelen

> [!NOTE]
> Voor de implementatie van Wave is de `2020-12-01-preview` API-versie (of hoger) vereist voor uw *micro soft. ServiceFabric/clusters-* resource.

Als u de implementatie van Wave wilt inschakelen voor automatische upgrade, moet u eerst bepalen welke Golf moet worden toegewezen aan uw cluster:

* **Wave 0** ( `Wave0` ): clusters worden bijgewerkt zodra een nieuwe service Fabric build wordt uitgebracht. Bedoeld voor test-en ontwikkelings clusters.
* **Wave 1** ( `Wave1` ): clusters worden een week bijgewerkt (zeven dagen) nadat een nieuwe build is uitgebracht. Bedoeld voor pre-productie/faserings clusters.
* **Golf 2** ( `Wave2` ): clusters worden twee weken (14 dagen) bijgewerkt nadat een nieuwe build is uitgebracht. Bedoeld voor productie clusters.

Voeg vervolgens een eigenschap toe `upgradeWave` aan uw cluster bron sjabloon met een van de hierboven genoemde Wave-waarden. Zorg ervoor dat de cluster bron-API-versie is `2020-12-01-preview` of hoger is.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Zodra u de bijgewerkte sjabloon hebt geïmplementeerd, wordt uw cluster in de opgegeven Wave geregistreerd voor de volgende upgrade periode en daarna.

U kunt [zich registreren voor e-mail meldingen](#register-for-notifications) met koppelingen naar meer informatie als een cluster upgrade mislukt.

### <a name="register-for-notifications"></a>Registreren voor meldingen

U kunt zich registreren voor meldingen wanneer een cluster upgrade mislukt. Er wordt een e-mail bericht verzonden naar uw toegewezen e-mail adres (sen) met meer informatie over de upgrade fout en koppelingen naar verdere hulp.

> [!NOTE]
> Inschrijving in Wave-implementatie is niet vereist voor het ontvangen van meldingen voor upgrade fouten.

Als u zich wilt inschrijven voor meldingen, voegt `notifications` u een sectie toe aan uw cluster resource sjabloon en wijst u een of meer e-mail adressen (*ontvangers*) toe om meldingen te ontvangen:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Zodra u de bijgewerkte sjabloon hebt geïmplementeerd, wordt u Inge schreven voor meldingen over mislukte upgrades.

## <a name="custom-policies-for-manual-upgrades"></a>Aangepaste beleids regels voor hand matige upgrades

U kunt aangepaste status beleidsregels opgeven voor hand matige cluster upgrades. Deze beleids regels worden telkens toegepast wanneer u een nieuwe runtime versie selecteert, waarmee het systeem wordt geactiveerd om de upgrade van het cluster te starten. Als u het beleid niet overschrijft, worden de standaard waarden gebruikt.

U kunt het aangepaste status beleid opgeven of de huidige instellingen controleren onder het gedeelte **infrastructuur upgrades** van uw cluster bron in azure portal door *aangepaste* optie voor **upgrade beleid** te selecteren.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Selecteer de optie aangepast upgrade beleid in de sectie ' infrastructuur upgrades ' van uw cluster bron in Azure Portal om aangepast status beleid in te stellen tijdens de upgrade":::

## <a name="query-for-supported-cluster-versions"></a>Query voor ondersteunde cluster versies

U kunt [Azure rest API](/rest/api/azure/) gebruiken om alle beschik bare service Fabric runtime-versies ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) weer te geven die beschikbaar zijn voor de opgegeven locatie en uw abonnement.

U kunt ook verwijzen naar [service Fabric-versies](service-fabric-versions.md) voor meer informatie over ondersteunde versies en besturings systemen.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
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

De `supportExpiryUtc` in de uitvoer rapporten wanneer een bepaalde release verloopt of is verlopen. De nieuwste releases hebben geen geldige datum, maar in plaats van de waarde *9999-12-31T23:59:59.9999999*, wat betekent dat de verval datum nog niet is ingesteld.


## <a name="next-steps"></a>Volgende stappen

* [Service Fabric upgrades beheren](service-fabric-cluster-upgrade-version-azure.md)
* Uw [service Fabric cluster instellingen](service-fabric-cluster-fabric-settings.md) aanpassen
* [Uw cluster in-en uitschalen](service-fabric-cluster-scale-in-out.md)
* Meer informatie over [toepassings upgrades](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
