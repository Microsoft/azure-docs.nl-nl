---
title: Overzicht van zelfstandige Service Fabric clusters
description: Service Fabric-clusters worden uitgevoerd op Windows Server en Linux, wat betekent dat u Service Fabric toepassingen kunt implementeren en hosten overal waar u Windows Server of Linux moet uitvoeren.
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a3162a3d02510ce8efab6c5bcac0f1fdd2b2539b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94683995"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Overzicht van Service Fabric zelfstandige clusters

Een Service Fabric-cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een cluster knooppunt genoemd. Clusters kunnen worden geschaald naar duizenden knoop punten. Als u nieuwe knoop punten aan het cluster toevoegt, worden in Service Fabric de service partitie replica's en instanties over het verhoogde aantal knoop punten gebalanceerd. De algehele prestaties van toepassingen verbeteren en conflicten voor toegang tot het geheugen neemt af. Als de knoop punten in het cluster niet efficiënt worden gebruikt, kunt u het aantal knoop punten in het cluster verlagen. Service Fabric opnieuw, worden de partitie replica's en instanties over het aantal knoop punten verkleind om beter gebruik te maken van de hardware op elk knoop punt.

Een knooppunt type definieert de grootte, het aantal en de eigenschappen voor een set knoop punten in het cluster. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. Knooppunttypen worden gebruikt voor het definiëren van rollen voor een set clusterknooppunten, zoals 'front-end' of 'back-end'. Uw cluster kan meer dan één knooppunttype hebben, maar voor productieclusters moet het primaire knooppunttype ten minste vijf VM's bevatten (of ten minste drie VM's voor testclusters). [Service Fabric-systeemservices](service-fabric-technical-overview.md#system-services) worden op de knooppunten van het primaire knooppunttype geplaatst.

Het proces voor het maken van een Service Fabric cluster on-premises is vergelijkbaar met het proces van het maken van een cluster op een wille keurige cloud van uw keuze met een set Vm's. De eerste stappen voor het inrichten van de virtuele machines vallen onder de Cloud provider of de on-premises omgeving die u gebruikt. Zodra u een set Vm's met netwerk connectiviteit hebt ingeschakeld, worden de stappen voor het instellen van het Service Fabric-pakket, het bewerken van de cluster instellingen en het uitvoeren van het cluster en de beheer scripts identiek. Dit zorgt ervoor dat uw kennis en ervaring van het beheer van Service Fabric clusters kunnen worden overgedragen wanneer u ervoor kiest om nieuwe hosting omgevingen te richten.

## <a name="cluster-security"></a>Clusterbeveiliging

Een Service Fabric cluster is een bron waarvan u eigenaar bent.  Het is uw verantwoordelijkheid om uw clusters te beveiligen om te voor komen dat onbevoegde gebruikers verbinding kunnen maken. Een beveiligd cluster is vooral belang rijk wanneer u werk belastingen voor productie op het cluster uitvoert.

> [!NOTE]
> Windows-verificatie is gebaseerd op Kerberos. NTLM wordt niet ondersteund als verificatie type.
>
> Gebruik, indien mogelijk, X. 509-certificaat verificatie voor Service Fabric clusters.

### <a name="node-to-node-security"></a>Beveiliging van knoop punt naar knoop punt

Beveiliging van knoop punt naar knoop punt beveiligt de communicatie tussen de Vm's of computers in een cluster. Dit beveiligings scenario zorgt ervoor dat alleen computers die gemachtigd zijn om deel te nemen aan het cluster, kunnen deel nemen aan het hosten van toepassingen en services in het cluster. Service Fabric maakt gebruik van X. 509-certificaten voor het beveiligen van een cluster en het bieden van beveiligings functies van toepassingen.  Een cluster certificaat is vereist voor het beveiligen van het cluster verkeer en het bieden van cluster-en server authenticatie.  Zelfondertekende certificaten kunnen worden gebruikt voor test clusters, maar een certificaat van een vertrouwde certificerings instantie moet worden gebruikt voor het beveiligen van productie clusters.

Windows-beveiliging kan ook worden ingeschakeld voor een zelfstandig Windows-cluster. Als u Windows Server 2012 R2 en Windows Active Directory, raden we u aan Windows-beveiliging te gebruiken met door groepen beheerde service accounts. Gebruik anders Windows-beveiliging met Windows-accounts.

Lees voor meer informatie [knoop punt-naar-knooppunt beveiliging](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Beveiliging van client naar knoop punt

Bij client-naar-knoop punt-beveiliging worden clients geverifieerd en wordt de communicatie tussen een client en afzonderlijke knoop punten in het cluster beveiligd. Met dit type beveiliging kunt u ervoor zorgen dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de toepassingen die op het cluster worden geïmplementeerd. Clients zijn uniek geïdentificeerd via de beveiligings referenties van het X. 509-certificaat. Een wille keurig aantal optionele client certificaten kan worden gebruikt om beheerders of gebruikers clients te verifiëren met het cluster.

Naast client certificaten kan Azure Active Directory ook worden geconfigureerd voor het verifiëren van clients met het cluster.

Lees voor meer informatie [client-naar-knoop punt beveiliging](service-fabric-cluster-security.md#client-to-node-security)

### <a name="service-fabric-role-based-access-control"></a>Service Fabric op rollen gebaseerd toegangs beheer
Service Fabric biedt ook ondersteuning voor toegangs beheer om de toegang tot bepaalde cluster bewerkingen voor verschillende groepen gebruikers te beperken. Dit helpt het cluster beter te beveiligen. Twee typen toegangs beheer worden ondersteund voor clients die verbinding maken met een cluster: beheerdersrol en gebruikersrol.  

Lees [service Fabric op rollen gebaseerd toegangs beheer](service-fabric-cluster-security.md#service-fabric-role-based-access-control)voor meer informatie.

## <a name="scaling"></a>Schalen

Toepassings vereisten veranderen in de loop van de tijd. Mogelijk moet u cluster bronnen verg Roten om te voldoen aan de toegenomen werk belasting van de toepassing of het netwerk verkeer of cluster bronnen verlagen wanneer de vraag wordt neergezet. Nadat u een Service Fabric cluster hebt gemaakt, kunt u het cluster horizon taal schalen (Wijzig het aantal knoop punten) of verticaal (Wijzig de resources van de knoop punten). U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd. Tijdens het schalen van het cluster worden uw toepassingen ook automatisch geschaald.

Lees de [schaal van zelfstandige clusters](service-fabric-cluster-scaling-standalone.md)voor meer informatie.

## <a name="upgrading"></a>Upgrade uitvoeren

Een zelfstandig cluster is een bron die u volledig bezit. U bent zelf verantwoordelijk voor het patchen van het onderliggende besturings systeem en het initiëren van infrastructuur upgrades. U kunt uw cluster zo instellen dat automatische runtime-upgrades worden ontvangen, wanneer micro soft een nieuwe versie uitgeeft of een door u gewenste ondersteunde runtime versie selecteert. Naast infrastructuur upgrades kunt u ook het besturings systeem patchen en de cluster configuratie bijwerken, zoals certificaten of toepassings poorten. 

Lees [upgrades van zelfstandige clusters](service-fabric-cluster-upgrade-standalone.md)voor meer informatie.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

U kunt clusters maken op Vm's of computers waarop deze besturings systemen worden uitgevoerd (Linux wordt nog niet ondersteund):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beveiligen](service-fabric-cluster-security.md), [schalen](service-fabric-cluster-scaling-standalone.md)en [upgraden](service-fabric-cluster-upgrade-standalone.md) van zelfstandige clusters.

Meer informatie over [service Fabric ondersteunings opties](service-fabric-support.md).
