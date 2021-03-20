---
title: 'Service Fabric cluster beveiliging: client rollen'
description: In dit artikel worden de twee client rollen en de machtigingen beschreven die aan de rollen worden gegeven.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451903"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Op rollen gebaseerd toegangs beheer voor Service Fabric-clients
Azure Service Fabric ondersteunt twee verschillende typen toegangs beheer voor clients die zijn verbonden met een Service Fabric cluster: beheerder en gebruiker. Met toegangs beheer kan de Cluster beheerder de toegang tot bepaalde cluster bewerkingen voor verschillende groepen gebruikers beperken, waardoor het cluster beter is beveiligd.  

**Beheerders** hebben volledige toegang tot beheer mogelijkheden (inclusief Lees-en schrijf mogelijkheden). Standaard hebben **gebruikers** alleen lees toegang tot beheer mogelijkheden (bijvoorbeeld query mogelijkheden) en de mogelijkheid om toepassingen en services op te lossen.

U geeft de twee client rollen (beheerder en client) op het moment van maken van een cluster op door afzonderlijke certificaten te bieden. Zie [service Fabric cluster beveiliging](service-fabric-cluster-security.md) voor meer informatie over het instellen van een beveiligd service Fabric-cluster.

## <a name="default-access-control-settings"></a>Standaard instellingen voor toegangs beheer
Het beheer type voor beheerders toegang heeft volledige toegang tot alle FabricClient-Api's. Er kunnen Lees-en schrijf bewerkingen op het Service Fabric cluster worden uitgevoerd, met inbegrip van de volgende bewerkingen:

### <a name="application-and-service-operations"></a>Toepassings-en service bewerkingen
* **CreateService**: service maken                             
* **CreateServiceFromTemplate**: service maken op basis van een sjabloon                             
* **Verwerkt**: service-updates                             
* **Delete service**: verwijderen van service                             
* **ProvisionApplicationType**: inrichten van toepassings type                             
* **CreateApplication**: toepassing maken                               
* **DeleteApplication**: toepassing verwijderen                             
* **UpgradeApplication**: toepassings upgrades starten of onderbreken                             
* **UnprovisionApplicationType**: toepassings type ongedaan maken van inrichting                             
* **MoveNextUpgradeDomain**: toepassings upgrades hervatten met een expliciet update domein                             
* **ReportUpgradeHealth**: toepassings upgrades hervatten met de huidige voortgang van de upgrade                             
* **ReportHealth**: status rapportage                             
* **PredeployPackageToNode**: API voor predeployment                            
* **CodePackageControl**: code pakketten opnieuw starten                             
* **RecoverPartition**: een partitie herstellen                             
* **RecoverPartitions**: partities herstellen                             
* **RecoverServicePartitions**: Service partities herstellen                             
* **RecoverSystemPartitions**: systeem service partities herstellen                             

### <a name="cluster-operations"></a>Clusterbewerkingen
* **ProvisionFabric**: MSI en/of cluster manifest inrichting                             
* **UpgradeFabric**: cluster upgrades starten                             
* **UnprovisionFabric**: MSI en/of cluster manifest inrichting ongedaan maken                         
* **MoveNextFabricUpgradeDomain**: cluster upgrades hervatten met een expliciet update domein                             
* **ReportFabricUpgradeHealth**: cluster upgrades hervatten met de huidige voortgang van de upgrade                             
* **StartInfrastructureTask**: infrastructuur taken starten                             
* **FinishInfrastructureTask**: infrastructuur taken volt ooien                             
* **InvokeInfrastructureCommand**: beheer opdrachten voor taak infrastructuur                              
* **ActivateNode**: een knoop punt activeren                             
* **DeactivateNode**: een knoop punt deactiveren                             
* **DeactivateNodesBatch**: meerdere knoop punten deactiveren                             
* **RemoveNodeDeactivations**: deactivering op meerdere knoop punten herstellen                             
* **GetNodeDeactivationStatus**: status van deactiveren controleren                             
* **NodeStateRemoved**: status van het rapportage knooppunt is verwijderd                             
* **ReportFault**: fout rapportage                             
* **FileContent**: bestands overdracht van client installatie kopie (extern naar cluster)                             
* **FileDownload**: Download initialisatie van client bestand voor installatie kopie (extern naar cluster)                             
* **InternalList**: bewerking voor lijst met client bestanden voor installatie kopie (intern)                             
* **Verwijderen**: Verwijder bewerking voor Image Store-client                              
* **Uploaden**: Upload bewerking voor Image Store-client                             
* **NodeControl**: knoop punten starten, stoppen en opnieuw starten                             
* **MoveReplicaControl**: replica's van het ene naar het andere knoop punt verplaatsen                             

### <a name="miscellaneous-operations"></a>Diverse bewerkingen
* **Ping**: client pings                             
* **Query**: alle query's toegestaan
* **NameExists**: controleren van benoemtde URI-controles                             

Het besturings type voor de gebruikers toegang is standaard beperkt tot de volgende bewerkingen: 

* **EnumerateSubnames**: het inventariseren van de naamgevings-URI                             
* **EnumerateProperties**: opsomming van naamgevings eigenschappen                             
* **PropertyReadBatch**: Lees bewerkingen voor naamgevings eigenschappen                             
* **GetServiceDescription**: Service meldingen met lange polling en service beschrijvingen lezen                             
* **ResolveService**: service oplossing op basis van een klacht                             
* **ResolveNameOwner**: naam van eigenaar van de naamgevings-URI omzetten                             
* **ResolvePartition**: systeem services omzetten                             
* **ServiceNotifications**: Service meldingen op basis van gebeurtenissen                             
* **GetUpgradeStatus**: de status van de upgrade van de toepassing wordt gecontroleerd                             
* **GetFabricUpgradeStatus**: de upgrade status van het cluster wordt gecontroleerd                             
* **InvokeInfrastructureQuery**: controleren van infrastructuur taken                             
* **Lijst**: bewerking voor installatie kopie van client bestands lijst                             
* **ResetPartitionLoad**: de belasting voor een failover-eenheid opnieuw instellen                             
* **ToggleVerboseServicePlacementHealthReporting**: uitgebreide status rapportage voor service plaatsing in-of uitschakelen                             

Beheer toegangs beheer heeft ook toegang tot de voor gaande bewerkingen.

## <a name="changing-default-settings-for-client-roles"></a>De standaard instellingen voor client rollen wijzigen
In het manifest bestand van het cluster kunt u zo nodig beheer mogelijkheden bieden aan de client. U kunt de standaard waarden wijzigen door te gaan naar de optie **infrastructuur instellingen** tijdens het [maken](service-fabric-cluster-creation-via-portal.md)van het cluster en de voor gaande instellingen op te geven in de velden **naam**, **beheerder**, **gebruiker** en **waarde** .

## <a name="next-steps"></a>Volgende stappen
[Service Fabric cluster beveiliging](service-fabric-cluster-security.md)

[Service Fabric cluster maken](service-fabric-cluster-creation-via-portal.md)

