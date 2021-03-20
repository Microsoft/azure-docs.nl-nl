---
title: KVSActorStateProvider-instellingen wijzigen
description: Meer informatie over het configureren van Azure Service Fabric stateful actors van het type KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609771"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Reliable Actors configureren--KVSActorStateProvider
U kunt de standaard configuratie van KVSActorStateProvider wijzigen door het settings.xml bestand te wijzigen dat wordt gegenereerd in de hoofdmap van het micro soft Visual Studio-pakket onder de map config voor de opgegeven actor.

De Azure Service Fabric runtime zoekt naar vooraf gedefinieerde sectie namen in het settings.xml-bestand en verbruikt de configuratie waarden tijdens het maken van de onderliggende runtime-onderdelen.

> [!NOTE]
> Verwijder of wijzig de sectie namen van de volgende configuraties **niet** in het settings.xml bestand dat wordt gegenereerd in de Visual Studio-oplossing.
> 
> 

## <a name="replicator-security-configuration"></a>Replicatie-beveiligings configuratie
Replicatie-beveiligings configuraties worden gebruikt voor het beveiligen van het communicatie kanaal dat wordt gebruikt tijdens de replicatie. Dit betekent dat het replicatie verkeer van de andere services niet kan worden weer gegeven, zodat de gegevens die Maxi maal beschikbaar worden, ook worden beveiligd.
Een lege beveiligings configuratie sectie voor komt standaard replicatie beveiliging.

> [!IMPORTANT]
> Voor Linux-knoop punten moeten certificaten PEM zijn. Zie [certificaten configureren voor Linux](./service-fabric-configure-certificates-linux.md)voor meer informatie over het zoeken en configureren van certificaten voor Linux. 
> 

### <a name="section-name"></a>Sectie naam
&lt;Actornaam &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replicator-configuratie
Met replicatie configuraties wordt de Replicator geconfigureerd die verantwoordelijk is voor het zeer betrouwbaar maken van de status van de actor State-provider.
De standaard configuratie wordt gegenereerd door de Visual Studio-sjabloon en moet voldoende zijn. In deze sectie vindt u meer informatie over aanvullende configuraties die beschikbaar zijn voor het afstemmen van de Replicator.

### <a name="section-name"></a>Sectie naam
&lt;Actornaam &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Configuratie namen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconden |0,015 |De periode gedurende welke de Replicator op het secundaire wacht na ontvangst van een bewerking voordat een bevestiging wordt verzonden naar de primaire. Alle andere bevestigingen die moeten worden verzonden voor bewerkingen die binnen dit interval worden verwerkt, worden als één antwoord verzonden. |
| ReplicatorEndpoint |N.v.t. |Geen standaard-vereiste para meter |Het IP-adres en de poort die door de primaire/secundaire Replicator worden gebruikt om te communiceren met andere replicatie Programma's in de replicaset. Dit moet verwijzen naar een TCP-bron eindpunt in het service manifest. Raadpleeg de [service manifest bronnen](service-fabric-service-manifest-resources.md) voor meer informatie over het definiëren van eindpunt resources in het service manifest. |
| RetryInterval |Seconden |5 |De tijds periode waarna de Replicator een bericht opnieuw verzendt als er geen bevestiging wordt ontvangen voor een bewerking. |
| MaxReplicationMessageSize |Bytes |50 MB |De maximale grootte van de replicatie gegevens die in één bericht kunnen worden verzonden. |
| MaxPrimaryReplicationQueueSize |Aantal bewerkingen |1024 |Maximum aantal bewerkingen in de primaire wachtrij. Er wordt een bewerking vrijgemaakt nadat de primaire Replicator een bevestiging van alle secundaire replicaties heeft ontvangen. Deze waarde moet groter zijn dan 64 en een macht van 2 zijn. |
| MaxSecondaryReplicationQueueSize |Aantal bewerkingen |2048 |Maximum aantal bewerkingen in de secundaire wachtrij. Een bewerking wordt vrijgegeven nadat de status Maxi maal beschikbaar is via persistentie. Deze waarde moet groter zijn dan 64 en een macht van 2 zijn. |

## <a name="store-configuration"></a>Archief configuratie
Archief configuraties worden gebruikt voor het configureren van het lokale archief dat wordt gebruikt om de status te behouden die wordt gerepliceerd.
De standaard configuratie wordt gegenereerd door de Visual Studio-sjabloon en moet voldoende zijn. In deze sectie vindt u meer informatie over aanvullende configuraties die beschikbaar zijn voor het afstemmen van het lokale archief.

### <a name="section-name"></a>Sectie naam
&lt;Actornaam &gt; ServiceLocalStoreConfig

### <a name="configuration-names"></a>Configuratie namen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milliseconden |200 |Hiermee stelt u het maximale batch-interval in voor de opslag van duurzame lokale opslag. |
| MaxVerPages |Aantal pagina's |16384 |Het maximum aantal versie pagina's in de lokale store-data base. Hiermee wordt het maximum aantal openstaande trans acties bepaald. |

## <a name="sample-configuration-file"></a>Voorbeeldconfiguratiebestand
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

