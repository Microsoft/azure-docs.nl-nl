---
title: Cluster resource Manager Service Fabric-plaatsings beleid
description: Overzicht van extra plaatsings beleid en regels voor Service Fabric Services
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 263e45928642aa74d682fc490e424a24deeb8076
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790678"
---
# <a name="placement-policies-for-service-fabric-services"></a>Plaatsings beleid voor service Fabric-Services
Plaatsings beleid is extra regels die kunnen worden gebruikt voor het bepalen van de service plaatsing in enkele specifieke, minder algemene scenario's. Enkele voor beelden van deze scenario's zijn:

- Uw Service Fabric-cluster bevat geografische afstanden, zoals meerdere on-premises data centers of over Azure-regio's
- Uw omgeving bevat meerdere gebieden van geopolitieke of juridische controle, of een andere situatie waarbij u beleids grenzen hebt die u moet afdwingen
- Er zijn communicatie prestaties of latentie overwegingen door grote afstanden of het gebruik van tragere of minder betrouw bare netwerk koppelingen
- U moet bepaalde werk belastingen co als Best effort, hetzij met andere workloads of nabijheid van klanten
- U hebt meerdere stateless instanties van een partitie op één knoop punt nodig

De meeste van deze vereisten worden uitgelijnd met de fysieke indeling van het cluster, weer gegeven als de fout domeinen van het cluster. 

De geavanceerde beleids regels voor plaatsing die helpen bij het oplossen van deze scenario's zijn:

1. Ongeldige domeinen
2. Vereiste domeinen
3. Voorkeurs domeinen
4. Replica pakketing niet toestaan
5. Meerdere stateless instanties op het knoop punt toestaan

De meeste van de volgende besturings elementen kunnen worden geconfigureerd via knooppunt eigenschappen en plaatsings beperkingen, maar sommige gecompliceerdere. Om het eenvoudiger te maken, biedt de Service Fabric cluster resource manager de volgende extra plaatsings beleidsregels. Plaatsings beleid wordt geconfigureerd op basis van een service-exemplaar per naam. Ze kunnen ook dynamisch worden bijgewerkt.

## <a name="specifying-invalid-domains"></a>Ongeldige domeinen opgeven
Met het **InvalidDomain** -plaatsings beleid kunt u opgeven dat een bepaald fout domein ongeldig is voor een specifieke service. Dit beleid zorgt ervoor dat een bepaalde service nooit wordt uitgevoerd in een bepaald gebied, bijvoorbeeld om redenen van geopolitieke of bedrijfs beleid. Meerdere ongeldige domeinen kunnen worden opgegeven via afzonderlijke beleids regels.

<center>

![Ongeldig domein voorbeeld][Image1]
</center>

Code:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Vereiste domeinen opgeven
Voor het vereiste domein plaatsings beleid moet de service alleen aanwezig zijn in het opgegeven domein. Meerdere vereiste domeinen kunnen worden opgegeven via afzonderlijke beleids regels.

<center>

![Voor beeld van vereist domein][Image2]
</center>

Code:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Een voorkeurs domein opgeven voor de primaire replica's van een stateful service
Het primaire domein dat als voorkeurs is, geeft het fout domein op waarin de primaire in moet worden geplaatst. De primaire fout eindigt in dit domein wanneer alles in orde is. Als het domein of de primaire replica mislukt of wordt afgesloten, wordt het primaire naar een andere locatie verplaatst, in het ideale geval in hetzelfde domein. Als deze nieuwe locatie zich niet in het voorkeurs domein bevindt, wordt deze zo snel mogelijk teruggezet naar het voorkeurs domein. Natuurlijk is deze instelling alleen zinvol voor stateful Services. Dit beleid is vooral nuttig in clusters die zijn verdeeld over Azure-regio's of meerdere data centers, maar die services hebben die de voor keur geven aan plaatsing op een bepaalde locatie. Als u Primaries dicht bij hun gebruikers of andere services houdt, kunt u een lagere latentie opgeven, met name voor lees bewerkingen, die standaard worden verwerkt door Primaries.

<center>

![Voorkeurs primaire domeinen en failover][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Het is niet toegestaan om de replica te distribueren en het pakket niet toe te staan
Replica's worden _normaal gesp roken_ gedistribueerd over fout-en upgrade domeinen wanneer het cluster in orde is. Er zijn echter gevallen waarin meer dan één replica voor een bepaalde partitie tijdelijk kan worden ingepakt in één domein. Stel bijvoorbeeld dat het cluster negen knoop punten heeft in drie fout domeinen, FD:/0, FD:/1 en FD:/2. U kunt er ook voor zorgen dat uw service drie replica's heeft. Stel dat de knoop punten die werden gebruikt voor deze replica's in FD:/1 en FD:/2 omlaag zijn gegaan. Normaal gesp roken zou cluster resource manager de voor keur geven aan andere knoop punten in hetzelfde fout domein. In dit geval is de oorzaak van capaciteits problemen geen van de andere knoop punten in die domeinen geldig. Als in cluster resource manager vervangingen voor die replica's zijn gebouwd, moet u knoop punten in FD:/0 kiezen. _Dit maakt echter_ een situatie waarin de beperking van het fout domein wordt geschonden. Het verpakken van replica's verg root de kans dat de hele replicaset kan worden uitgeschakeld of verloren gaat. 

> [!NOTE]
> Zie [dit onderwerp](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)voor meer informatie over de prioriteiten en beperkingen van de beperkingen in het algemeen.
>

Als u ooit een status bericht hebt gezien als ' `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` ', hebt u deze voor waarde bereikt of iets dergelijks. Meestal worden slechts één of twee replica's samen verpakt. Zolang er minder dan een quorum van replica's in een bepaald domein is, bent u veilig. Het verpakken is zeldzaam, maar het kan gebeuren en meestal zijn deze situaties tijdelijk omdat de knoop punten weer worden teruggestuurd. Als de knoop punten blijven bestaan en de cluster bron beheerder vervangingen moet maken, meestal zijn er andere knoop punten beschikbaar in het ideale fout domein.

Sommige werk belastingen hebben als voor keur altijd het doel aantal replica's, zelfs als ze in minder domeinen zijn verpakt. Deze werk belastingen zijn verwacht tegen het totale aantal gelijktijdige permanente domein storingen en kunnen de lokale status meestal herstellen. Andere werk belastingen zouden in plaats daarvan de uitval tijd in beslag nemen dan de juistheid van het risico of gegevens verlies. De meeste productie werkbelastingen worden uitgevoerd met meer dan drie replica's, meer dan drie fout domeinen en veel geldige knoop punten per fout domein. Als gevolg hiervan is het standaard gedrag van domein pakket standaard toegestaan. Met het standaard gedrag kunnen normale verdeling en failover deze extreme gevallen afhandelen, zelfs als dit betekent dat het tijdelijk domein wordt verpakt.

Als u dit pakket wilt uitschakelen voor een bepaalde workload, kunt u het `RequireDomainDistribution` beleid opgeven voor de service. Wanneer dit beleid is ingesteld, zorgt het cluster resource beheer ervoor dat er geen twee replica's van dezelfde partitie worden uitgevoerd in hetzelfde fout-of upgrade domein.

Code:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Het is nu mogelijk om deze configuraties te gebruiken voor services in een cluster dat niet geografisch is verspreid? Dat kan, maar er is ook geen goede reden. De vereiste, ongeldige en voorkeurs domein configuraties moeten worden vermeden tenzij de scenario's deze vereisen. Het is niet verstandig om een bepaalde workload af te dwingen om in één rek te worden uitgevoerd, of om de voor keur te geven aan een segment van uw lokale cluster ten opzichte van een andere. Verschillende hardwareconfiguraties moeten worden verdeeld over fout domeinen en worden afgehandeld via normale plaatsings beperkingen en eigenschappen van knoop punten.

## <a name="placement-of-multiple-stateless-instances-of-a-partition-on-single-node"></a>Plaatsing van meerdere stateless instanties van een partitie op één knoop punt
Het **AllowMultipleStatelessInstancesOnNode** -plaatsings beleid staat het plaatsen van meerdere stateless instanties van een partitie op één knoop punt toe. Standaard kunnen meerdere exemplaren van één partitie niet op een knoop punt worden geplaatst. Zelfs met een-1-service is het niet mogelijk om het aantal exemplaren te schalen dat groter is dan het aantal knoop punten in het cluster, voor een bepaalde benoemde service. Met dit plaatsings beleid wordt deze beperking verwijderd en kan InstanceCount hoger worden opgegeven dan het aantal knoop punten.

Als u ooit een status bericht hebt gezien als ' `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: ReplicaExclusion` ', hebt u deze voor waarde bereikt of iets dergelijks. 

Door het `AllowMultipleStatelessInstancesOnNode` beleid op te geven voor de service, kan InstanceCount worden ingesteld buiten het aantal knoop punten in het cluster.

Code:

```csharp
ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription allowMultipleInstances = new ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription();
serviceDescription.PlacementPolicies.Add(allowMultipleInstances);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless –PartitionSchemeSingleton –PlacementPolicy @(“AllowMultipleStatelessInstancesOnNode”) -InstanceCount 10 -ServicePackageActivationMode ExclusiveProcess 
```

> [!NOTE]
> Het plaatsings beleid bevindt zich momenteel in de preview-versie en achter de `EnableUnsupportedPreviewFeatures` cluster instelling. Aangezien dit nu een preview-functie is, kan het instellen van de preview-configuratie voor komen dat het cluster wordt bijgewerkt naar/van. Met andere woorden, u moet een nieuw cluster maken om de functie uit te proberen.
>

> [!NOTE]
> Momenteel wordt het beleid alleen ondersteund voor stateless Services met de [activerings modus](/dotnet/api/system.fabric.description.servicepackageactivationmode)voor het ExclusiveProcess-service pakket.
>

> [!WARNING]
> Het beleid wordt niet ondersteund wanneer het wordt gebruikt met statische poort eindpunten. Het gebruik van beide in combi natie kan leiden tot een slechte cluster als meerdere exemplaren op hetzelfde knoop punt verbinding met dezelfde poort proberen te maken en niet actief kunnen zijn. 
>

> [!NOTE]
> Het gebruik van een hoge waarde van [MinInstanceCount](/dotnet/api/system.fabric.description.statelessservicedescription.mininstancecount) met dit plaatsings beleid kan leiden tot vastgelopen toepassings upgrades. Als u bijvoorbeeld een cluster met vijf knoop punten hebt en InstanceCount = 10 hebt ingesteld, hebt u twee exemplaren per knoop punt. Als u MinInstanceCount = 9 instelt, kan een upgrade van de app worden vastgelopen. met MinInstanceCount = 8 kan dit worden vermeden.
>

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van Services vindt u op het [configureren van services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
