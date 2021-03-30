---
title: ReliableDictionaryActorStateProvider-instellingen wijzigen
description: Meer informatie over het configureren van Azure Service Fabric stateful actors van het type ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609737"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Reliable Actors configureren--ReliableDictionaryActorStateProvider
U kunt de standaard configuratie van ReliableDictionaryActorStateProvider wijzigen door het settings.xml bestand te wijzigen dat is gegenereerd in de Visual Studio package root onder de map config voor de opgegeven actor.

De Azure Service Fabric runtime zoekt naar vooraf gedefinieerde sectie namen in het settings.xml-bestand en verbruikt de configuratie waarden tijdens het maken van de onderliggende runtime-onderdelen.

> [!NOTE]
> Verwijder of wijzig de sectie namen van de volgende configuraties **niet** in het settings.xml bestand dat wordt gegenereerd in de Visual Studio-oplossing.
> 
> 

Er zijn ook algemene instellingen die van invloed zijn op de configuratie van ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Algemene configuratie
De globale configuratie is opgegeven in het cluster manifest voor het cluster in de sectie KtlLogger. Hiermee kunt u de locatie en grootte van het gedeelde logboek configureren, plus de globale geheugen limieten die door de logboeken worden gebruikt. Houd er rekening mee dat wijzigingen in het cluster manifest van invloed zijn op alle services die gebruikmaken van ReliableDictionaryActorStateProvider en betrouw bare stateful Services.

Het cluster manifest is een enkel XML-bestand met instellingen en configuraties die van toepassing zijn op alle knoop punten en services in het cluster. Het bestand wordt doorgaans ClusterManifest.xml genoemd. U kunt het cluster manifest voor uw cluster zien met behulp van de Get-ServiceFabricClusterManifest Power shell-opdracht.

### <a name="configuration-names"></a>Configuratie namen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilo bytes |8388608 |Minimum aantal KB dat moet worden toegewezen in de kernelmodus voor de geheugen groep schrijf buffer voor logboek registratie. Deze geheugen groep wordt gebruikt voor het opslaan van de status informatie voordat naar de schijf wordt geschreven. |
| WriteBufferMemoryPoolMaximumInKB |Kilo bytes |Geen limiet |Maximale grootte van de geheugen groep voor schrijf buffer voor logboek registratie kan worden uitgebreid. |
| SharedLogId |GUID |"" |Hiermee geeft u een unieke GUID op die moet worden gebruikt voor het identificeren van het standaard gedeelde logboek bestand dat wordt gebruikt door alle betrouw bare Services op alle knoop punten in het cluster die de SharedLogId niet opgeven in hun servicespecifieke configuratie. Als SharedLogId is opgegeven, moet SharedLogPath ook worden opgegeven. |
| SharedLogPath |Fully Qualified Path name |"" |Hiermee geeft u het volledige pad op waar het gedeelde logboek bestand wordt gebruikt door alle betrouw bare Services op alle knoop punten in het cluster die de SharedLogPath niet opgeven in hun servicespecifieke configuratie. Als SharedLogPath echter is opgegeven, moet SharedLogId ook worden opgegeven. |
| SharedLogSizeInMB |Mega bytes |8192 |Hiermee geeft u het aantal MB schijf ruimte op dat statisch moet worden toegewezen aan het gedeelde logboek. De waarde moet 2048 of groter zijn. |

### <a name="sample-cluster-manifest-section"></a>Sectie voor beeld van cluster manifest
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Opmerkingen
De logger heeft een globale geheugen groep die is toegewezen op basis van niet-wisselbaar kernelgeheugen dat beschikbaar is voor alle betrouw bare Services op een knoop punt om status gegevens in de cache op te zetten voordat ze worden geschreven naar het toegewezen logboek dat is gekoppeld aan de betrouw bare service replica. De pool grootte wordt bepaald door de instellingen WriteBufferMemoryPoolMinimumInKB en WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Hiermee geeft u de oorspronkelijke grootte van deze geheugen groep en de laagste grootte op waarmee de geheugen groep kan worden verkleind. WriteBufferMemoryPoolMaximumInKB is de hoogste grootte waarmee de geheugen groep mag groeien. Elke betrouw bare service replica die wordt geopend, kan de grootte van de geheugen groep verhogen door een door het systeem vastgesteld bedrag tot WriteBufferMemoryPoolMaximumInKB. Als er meer geheugen nodig is dan beschikbaar is, worden de aanvragen voor geheugen uitgesteld totdat er geheugen beschikbaar is. Als de geheugen groep schrijf buffer te klein is voor een bepaalde configuratie, kan dit de prestaties nadelig beïnvloeden.

De instellingen SharedLogId en SharedLogPath worden altijd samen gebruikt voor het definiëren van de GUID en locatie voor het standaard gedeelde logboek voor alle knoop punten in het cluster. Het standaard gedeelde logboek wordt gebruikt voor alle betrouw bare services die de instellingen niet opgeven in de settings.xml voor de specifieke service. Voor de beste prestaties moeten gedeelde logboek bestanden worden geplaatst op schijven die alleen worden gebruikt voor het gedeelde logboek bestand om conflicten te verminderen.

SharedLogSizeInMB Hiermee geeft u de hoeveelheid schijf ruimte op die vooraf moet worden toegewezen voor het gedeelde standaard logboek op alle knoop punten.  SharedLogId en SharedLogPath hoeven niet te worden opgegeven om SharedLogSizeInMB te kunnen opgeven.

## <a name="replicator-security-configuration"></a>Replicatie-beveiligings configuratie
Replicatie-beveiligings configuraties worden gebruikt voor het beveiligen van het communicatie kanaal dat wordt gebruikt tijdens de replicatie. Dit betekent dat het replicatie verkeer van de andere services niet kan worden weer gegeven, zodat de gegevens die Maxi maal beschikbaar worden, ook worden beveiligd.
Een lege beveiligings configuratie sectie voor komt standaard replicatie beveiliging.

> [!IMPORTANT]
> Voor Linux-knoop punten moeten certificaten PEM zijn. Zie [certificaten configureren voor Linux](./service-fabric-configure-certificates-linux.md)voor meer informatie over het zoeken en configureren van certificaten voor Linux. 
> 

### <a name="section-name"></a>Sectie naam
&lt;Actornaam &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replicator-configuratie
Replicatie configuraties worden gebruikt voor het configureren van de Replicator die verantwoordelijk is voor het zeer betrouwbaar maken van de status van de actor State-provider door de status lokaal te repliceren en op te slaan.
De standaard configuratie wordt gegenereerd door de Visual Studio-sjabloon en moet voldoende zijn. In deze sectie vindt u meer informatie over aanvullende configuraties die beschikbaar zijn voor het afstemmen van de Replicator.

### <a name="section-name"></a>Sectie naam
&lt;Actornaam &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Configuratie namen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconden |0,015 |De periode gedurende welke de Replicator op het secundaire wacht na ontvangst van een bewerking voordat een bevestiging wordt verzonden naar de primaire. Alle andere bevestigingen die moeten worden verzonden voor bewerkingen die binnen dit interval worden verwerkt, worden als één antwoord verzonden. |
| ReplicatorEndpoint |N.v.t. |Geen standaard-vereiste para meter |Het IP-adres en de poort die door de primaire/secundaire Replicator worden gebruikt om te communiceren met andere replicatie Programma's in de replicaset. Dit moet verwijzen naar een TCP-bron eindpunt in het service manifest. Raadpleeg de [service manifest bronnen](service-fabric-service-manifest-resources.md) voor meer informatie over het definiëren van eindpunt resources in service manifest. |
| MaxReplicationMessageSize |Bytes |50 MB |De maximale grootte van de replicatie gegevens die in één bericht kunnen worden verzonden. |
| MaxPrimaryReplicationQueueSize |Aantal bewerkingen |8192 |Maximum aantal bewerkingen in de primaire wachtrij. Er wordt een bewerking vrijgemaakt nadat de primaire Replicator een bevestiging van alle secundaire replicaties heeft ontvangen. Deze waarde moet groter zijn dan 64 en een macht van 2 zijn. |
| MaxSecondaryReplicationQueueSize |Aantal bewerkingen |16384 |Maximum aantal bewerkingen in de secundaire wachtrij. Een bewerking wordt vrijgegeven nadat de status Maxi maal beschikbaar is via persistentie. Deze waarde moet groter zijn dan 64 en een macht van 2 zijn. |
| CheckpointThresholdInMB |MB |200 |De hoeveelheid logboek bestands ruimte waarna de status van een controle punt wordt gemaakt. |
| MaxRecordSizeInKB |KB |1024 |Grootste record grootte die door de Replicator kan worden geschreven in het logboek. Deze waarde moet een meervoud van 4 en groter dan 16 zijn. |
| OptimizeLogForLowerDiskUsage |Booleaans |true |Indien true, wordt het logboek geconfigureerd zodat het vastgelegde logboek bestand van de replica wordt gemaakt met behulp van een NTFS sparse-bestand. Hiermee wordt het werkelijke gebruik van schijf ruimte voor het bestand verminderd. Als deze eigenschap onwaar is, wordt het bestand gemaakt met vaste toewijzingen. Dit biedt de beste schrijf prestaties. |
| SharedLogId |guid |"" |Hiermee geeft u een unieke GUID op die moet worden gebruikt voor het identificeren van het gedeelde logboek bestand dat wordt gebruikt met deze replica. Normaal gesp roken moeten services deze instelling niet gebruiken. Als SharedLogId echter is opgegeven, moet SharedLogPath ook worden opgegeven. |
| SharedLogPath |Fully Qualified Path name |"" |Hiermee geeft u het volledige pad op waar het gedeelde logboek bestand voor deze replica wordt gemaakt. Normaal gesp roken moeten services deze instelling niet gebruiken. Als SharedLogPath echter is opgegeven, moet SharedLogId ook worden opgegeven. |

## <a name="sample-configuration-file"></a>Voorbeeldconfiguratiebestand
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Opmerkingen
De BatchAcknowledgementInterval-para meter beheert de replicatie latentie. De waarde ' 0 ' resulteert in de laagst mogelijke latentie, tegen de kosten van de door Voer (omdat er meer bevestigings berichten moeten worden verzonden en verwerkt, elk met minder bevestigingen).
Hoe groter de waarde voor BatchAcknowledgementInterval, hoe hoger de totale replicatie doorvoer, tegen de kosten van een hogere latentie van de bewerking. Dit kan rechtstreeks worden omgezet naar de latentie van trans acties door voeren.

De CheckpointThresholdInMB para meter bepaalt de hoeveelheid schijf ruimte die door de Replicator kan worden gebruikt om status gegevens op te slaan in het specifieke logboek bestand van de replica. Het verhogen van dit naar een hogere waarde dan de standaard instelling kan leiden tot snellere herconfiguratie tijden wanneer een nieuwe replica wordt toegevoegd aan de set. Dit wordt veroorzaakt door de gedeeltelijke status overdracht die plaatsvindt als gevolg van de beschik baarheid van meer geschiedenis van bewerkingen in het logboek. Dit kan leiden tot een grotere herstel tijd van een replica na een crash.

Als u OptimizeForLowerDiskUsage instelt op True, wordt de ruimte van het logboek bestand overbelast zodat actieve replica's meer status informatie in hun logboek bestanden kunnen opslaan, terwijl inactieve replica's minder schijf ruimte in beslag nemen. Dit maakt het mogelijk om meer replica's op een knoop punt te hosten. Als u OptimizeForLowerDiskUsage instelt op False, wordt de status informatie sneller naar de logboek bestanden geschreven.

De instelling MaxRecordSizeInKB definieert de maximale grootte van een record die door de Replicator kan worden geschreven naar het logboek bestand. In de meeste gevallen is de standaard record grootte van 1024 KB optimaal. Als de service echter zorgt voor grotere gegevens items die deel uitmaken van de status informatie, moet deze waarde mogelijk worden verhoogd. Er is weinig voor deel bij het maken van MaxRecordSizeInKB kleiner dan 1024, omdat kleinere records alleen de benodigde ruimte voor de kleinere record gebruiken. We verwachten dat deze waarde alleen in zeldzame gevallen moet worden gewijzigd.

De instellingen SharedLogId en SharedLogPath worden altijd samen gebruikt om een service een afzonderlijk gedeeld logboek te laten gebruiken van het gedeelde standaard logboek voor het knoop punt. Voor de beste efficiëntie moeten zoveel mogelijke services hetzelfde gedeelde logboek opgeven. Gedeelde logboek bestanden moeten worden geplaatst op schijven die uitsluitend voor het gedeelde logboek bestand worden gebruikt, om de inhoud van de hoofd verplaatsing te verminderen. We verwachten dat deze waarden alleen in zeldzame gevallen moeten worden gewijzigd.

