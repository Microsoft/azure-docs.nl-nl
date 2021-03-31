---
title: Replica's en instanties in azure Service Fabric
description: Meer informatie over replica's en instanties in Service Fabric, inclusief een overzicht van de levens cycli en functies.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84710742"
---
# <a name="replicas-and-instances"></a>Replica's en exemplaren 
Dit artikel geeft een overzicht van de levens cyclus van replica's van stateful Services en instanties van stateless Services.

## <a name="instances-of-stateless-services"></a>Exemplaren van stateless Services
Een exemplaar van een stateless service is een kopie van de service logica die wordt uitgevoerd op een van de knoop punten van het cluster. Een exemplaar binnen een partitie wordt uniek geïdentificeerd door de **InstanceId**. De levens cyclus van een exemplaar wordt gemodelleerd in het volgende diagram:

![Levens cyclus van exemplaar](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Inbouwen (IB)
Nadat de cluster resource manager een plaatsing voor het exemplaar heeft bepaald, wordt deze levens cyclus status geactiveerd. Het exemplaar wordt gestart op het knoop punt. De toepassingshost is gestart, het exemplaar wordt gemaakt en vervolgens geopend. Nadat het opstarten is voltooid, wordt het exemplaar overgezet naar de status gereed. 

Als de toepassingshost of het knoop punt voor deze instantie vastloopt, wordt deze overgezet naar de status verwijderd.

### <a name="ready-rd"></a>Gereed (RD)
In de status gereed is de instantie actief en wordt deze uitgevoerd op het knoop punt. Als dit een betrouw bare service is, is **RunAsync** aangeroepen. 

Als de toepassingshost of het knoop punt voor deze instantie vastloopt, wordt deze overgezet naar de status verwijderd.

### <a name="closing-cl"></a>Sluiten (CL)
In de sluitings status is Azure Service Fabric bezig met het afsluiten van het exemplaar op dit knoop punt. Dit kan te wijten zijn aan een groot aantal redenen: bijvoorbeeld een toepassings upgrade, taak verdeling of de service die wordt verwijderd. Nadat het afsluiten is voltooid, wordt de status verwijderd.

### <a name="dropped-dd"></a>Verwijderd (DD)
In de status verwijderd wordt het exemplaar niet meer uitgevoerd op het knoop punt. Op dit punt bewaart Service Fabric de meta gegevens over deze instantie, die uiteindelijk ook worden verwijderd.

> [!NOTE]
> U kunt overstappen van elke status naar de status verwijderd met behulp van de **ForceRemove** -optie op `Remove-ServiceFabricReplica` .
>

## <a name="replicas-of-stateful-services"></a>Replica's van stateful Services
Een replica van een stateful service is een kopie van de service logica die wordt uitgevoerd op een van de knoop punten van het cluster. Daarnaast houdt de replica een kopie bij van de status van die service. In twee gerelateerde concepten worden de levens cyclus en het gedrag van stateful replica's beschreven:
- Levens cyclus van replica's
- Replica-rol

In de volgende bespreking worden persistente stateful Services beschreven. Voor een volatiel (of in het geheugen) stateful-Services zijn de status omlaag en neergezet gelijkwaardig.

![Levens cyclus van replica's](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Inbouwen (IB)
Een inbouwen replica is een replica die wordt gemaakt of voor bereid voor het toevoegen van de replicaset. De IB heeft een andere semantiek, afhankelijk van de rol van de replica. 

Als de toepassingshost of het knoop punt voor een inbuild-replica vastloopt, wordt deze overgezet naar de status omlaag.

   - **Primaire inbuild-replica's**: primaire inbouwen zijn de eerste replica's voor een partitie. Deze replica treedt meestal op wanneer de partitie wordt gemaakt. Primaire inbuild-replica's ontstaan ook wanneer alle replica's van een partitie opnieuw worden opgestart of worden verwijderd.

   - **IdleSecondary inbuild-replica's**: Dit zijn de nieuwe replica's die worden gemaakt door de cluster resource manager of bestaande replica's die zijn opgetreden en die opnieuw moeten worden toegevoegd aan de set. Deze replica's worden geseed of gebouwd op basis van de primaire versie voordat ze kunnen deel nemen aan de replicaset als ActiveSecondary en deel nemen aan de quorum bevestiging van bewerkingen.

   - **ActiveSecondary inbuild-replica's**: deze status wordt in sommige query's in acht genomen. Het is een optimalisatie waarbij de replicaset niet verandert, maar een replica moet worden gebouwd. De replica zelf volgt de normale status computer overgangen (zoals beschreven in de sectie over replica rollen).

### <a name="ready-rd"></a>Gereed (RD)
Een kant-en-klare replica is een replica die deelneemt aan replicatie en quorum bevestiging van bewerkingen. De status gereed is van toepassing op primaire en actieve secundaire replica's.

Als de toepassingshost of het knoop punt voor een kant-en-klare replica vastloopt, wordt deze overgezet naar de status omlaag.

### <a name="closing-cl"></a>Sluiten (CL)
Een replica krijgt de sluitings status in de volgende scenario's:

- **De code voor de replica wordt afgesloten**: Service Fabric moet mogelijk de actieve code voor een replica afsluiten. Dit kan verschillende oorzaken hebben. Dit kan bijvoorbeeld gebeuren vanwege een toepassings-, infrastructuur-of infrastructuur upgrade, of vanwege een fout die door de replica is gerapporteerd. Wanneer het sluiten van de replica is voltooid, wordt de replica overgezet naar de status omlaag. De persistente status die is gekoppeld aan deze replica die is opgeslagen op schijf, wordt niet opgeschoond.

- **De replica verwijderen uit het cluster**: Service Fabric moet mogelijk de persistente status verwijderen en de actieve code voor een replica afsluiten. Deze afsluiting kan veel oorzaken hebben, bijvoorbeeld taak verdeling.

### <a name="dropped-dd"></a>Verwijderd (DD)
In de status verwijderd wordt het exemplaar niet meer uitgevoerd op het knoop punt. Er is ook geen status meer op het knoop punt. Op dit punt bewaart Service Fabric de meta gegevens over deze instantie, die uiteindelijk ook worden verwijderd.

### <a name="down-d"></a>Omlaag (D)
In de status down wordt de replica code niet uitgevoerd, maar de persistente status voor die replica bestaat op het knoop punt. Een replica kan een groot aantal redenen hebben: bijvoorbeeld het knoop punt is niet actief, een crash in de replica code, een toepassings upgrade of replica fouten.

Als de upgrade op het knoop punt is voltooid, wordt de replica door Service Fabric als vereist geopend.

De replica-rol is niet relevant in de status down.

### <a name="opening-op"></a>Openen (OP)
Met een down keer replica wordt de openings status geactiveerd wanneer Service Fabric de replica opnieuw moet instellen. Deze status kan bijvoorbeeld zijn nadat een code-upgrade voor de toepassing op een knoop punt is voltooid. 

Als de toepassingshost of het knoop punt voor een inconsistentie van de replica vastloopt, wordt de status naar beneden overgezet.

De replica-rol is niet relevant voor de openings status.

### <a name="standby-sb"></a>Stand-by (SB)
Een stand-by replica is een replica van een persistente service die is afgesloten en vervolgens werd geopend. Deze replica kan worden gebruikt door Service Fabric als een andere replica moet worden toegevoegd aan de replicaset (omdat de replica al een deel van de status heeft en het bouw proces sneller is). Nadat de StandByReplicaKeepDuration is verlopen, wordt de stand-by replica verwijderd.

Als de toepassingshost of het knoop punt van een stand-by-replica vastloopt, wordt deze overgezet naar de status omlaag.

De replica-rol is niet relevant voor de status stand-by.

> [!NOTE]
> Een replica die niet wordt ingeactief of verwijderd, wordt beschouwd als *actief*.
>

> [!NOTE]
> Het is mogelijk om van een wille keurige status over te stappen naar de status verwijderd met behulp van de **ForceRemove** -optie op `Remove-ServiceFabricReplica` .
>

## <a name="replica-role"></a>Replica-rol 
De rol van de replica bepaalt de functie in de replicaset:

- **Primair (P)**: er is één primaire set in de replicaset die verantwoordelijk is voor het uitvoeren van lees-en schrijf bewerkingen. 
- **ActiveSecondary (S)**: Dit zijn replica's die status updates ontvangen van de primaire, toe te passen en vervolgens teruggestuurde bevestigingen te sturen. Er zijn meerdere actieve secundaire zones in de replicaset. Het aantal van deze actieve secundaire zones bepaalt het aantal fouten dat de service kan verwerken.
- **IdleSecondary (I)**: deze replica's worden gebouwd door de primaire. Ze ontvangen de status van de primaire voordat ze kunnen worden gepromoveerd tot actief secundair. 
- **Geen (N)**: deze replica's hebben geen verantwoordelijkheid in de replicaset.
- **Onbekend (U)**: dit is de initiële rol van een replica voordat een **ChangeRole** -API-aanroep van service Fabric wordt ontvangen.

In het volgende diagram ziet u de overgangen van replica rollen en enkele voorbeeld scenario's waarin ze kunnen optreden:

![Replica-rol](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-> P: het maken van een nieuwe primaire replica.
- U-> I: het maken van een nieuwe niet-actieve replica.
- U-> N: verwijdering van een stand-by replica.
- I-> S: promotie van de niet-actieve secundaire naar actief secundair zodat de bevestigingen bijdragen aan het quorum.
- I-> P: de promotie van de niet-actieve secundaire naar de primaire. Dit kan gebeuren onder speciale herconfiguraties wanneer de niet-actieve secundaire de juiste kandidaat primair is.
- I-> N: de niet-actieve secundaire replica wordt verwijderd.
- S-> P: de promotie van de actieve secundaire naar de primaire. Dit kan worden veroorzaakt door failover van de primaire of primaire verplaatsing die door de cluster resource manager is gestart. Het kan bijvoorbeeld zijn als reactie op een toepassings upgrade of taak verdeling.
- S-> N: de actieve secundaire replica wordt verwijderd.
- P-> S: degradatie van de primaire replica. Dit kan worden veroorzaakt door een primaire verplaatsing die door de cluster resource manager is geïnitieerd. Het kan bijvoorbeeld zijn als reactie op een toepassings upgrade of taak verdeling.
- P-> N: verwijdering van de primaire replica.

> [!NOTE]
> Ontwikkel modellen op een hoger niveau, zoals [reliable actors](service-fabric-reliable-actors-introduction.md) en [reliable Services](service-fabric-reliable-services-introduction.md), verbergen het concept van replica rollen van de ontwikkelaar. In Actors is het begrip van een rol overbodig. In-Services is het grotendeels vereenvoudigd voor de meeste scenario's.
>

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over Service Fabric concepten:

[Levenscyclus van Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

