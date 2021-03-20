---
title: Recovery Manager om problemen met Shard-kaarten op te lossen
description: De klasse toewijzingen oplossen gebruiken om problemen met Shard-kaarten op te lossen
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91bcd998849c619a328a198c97bb8c977b9d8232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792222"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Problemen met shard-toewijzingen oplossen met de RecoveryManager-klasse
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

De [toewijzingen oplossen](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) -klasse biedt ADO.NET-toepassingen de mogelijkheid om op eenvoudige wijze eventuele inconsistenties tussen de wereld wijde Shard-kaart (GSM) en de lokale Shard-map (LSM) in een Shard-database omgeving te detecteren en te corrigeren.

De GSM en LSM volgen de toewijzing van elke data base in een Shard-omgeving. Af en toe treedt er een breuk op tussen de GSM en de LSM. In dat geval gebruikt u de toewijzingen oplossen-klasse om de breek routine te detecteren en te herstellen.

De klasse toewijzingen oplossen maakt deel uit van de [Elastic database-client bibliotheek](elastic-database-client-library.md).

![Shard-kaart][1]

Zie de [woordenlijst voor hulpprogramma's van de elastische database](elastic-scale-glossary.md) voor definities van termen. Zie [Shard map Management](elastic-scale-shard-map-management.md)voor informatie over hoe de **ShardMapManager** wordt gebruikt voor het beheren van gegevens in een Shard-oplossing.

## <a name="why-use-the-recovery-manager"></a>Waarom de Recovery Manager gebruiken?

In een Shard-database omgeving is er één Tenant per data base en veel data bases per server. Er kunnen ook veel servers in de omgeving zijn. Elke Data Base is toegewezen in de Shard-kaart, zodat aanroepen kunnen worden doorgestuurd naar de juiste server en data base. Data bases worden getraceerd op basis van een **sharding-sleutel** en elke Shard wordt toegewezen aan een **reeks sleutel waarden**. Een sharding-sleutel kan bijvoorbeeld de klant namen van D tot en met F vertegenwoordigen. De toewijzing van alle Shards (ook wel bekend als data bases genoemd) en de bijbehorende toewijzings bereik bevinden zich in de **Global Shard map (GSM)**. Elke Data Base bevat ook een kaart van de bereiken die zich bevinden in de Shard die wordt aangeduid als de **lokale Shard-toewijzing (LSM)**. Wanneer een app verbinding maakt met een Shard, wordt de toewijzing in de cache opgeslagen met de app voor snel ophalen. De LSM wordt gebruikt om gegevens in de cache te valideren.

De GSM-en LSM kunnen om de volgende redenen niet meer worden gesynchroniseerd:

1. Het verwijderen van een Shard waarvan het bereik is aangenomen, is niet meer in gebruik of de naam van een Shard wordt gewijzigd. Het verwijderen van een Shard resulteert in een **zwevende Shard-toewijzing**. Op dezelfde manier kan een hernoemde Data Base een zwevende Shard-toewijzing veroorzaken. Afhankelijk van de bedoeling van de wijziging moet de Shard mogelijk worden verwijderd, of moet de Shard-locatie worden bijgewerkt. Zie [een verwijderde data base herstellen](recovery-using-backups.md)als u een verwijderde data base wilt herstellen.
2. Er treedt een geo-failover-gebeurtenis op. Als u wilt door gaan, moet de server naam en de database naam van Shard-toewijzings beheer in de toepassing worden bijgewerkt en vervolgens de gegevens van de Shard voor alle Shards in een Shard-toewijzing bijwerken. Als er sprake is van een geo-failover, moet deze herstel logica worden geautomatiseerd binnen de failover-werk stroom. Het automatiseren van herstel acties maakt het mogelijk dat er een wrijvings beheer kan worden uitgevoerd voor geo-compatibele data bases en dat hand matige acties worden voor komen. Zie [bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) en [herstel na nood gevallen](disaster-recovery-guidance.md)voor meer informatie over de opties om een Data Base te herstellen als er sprake is van een storing in een Data Center.
3. Een Shard of de ShardMapManager-data base wordt hersteld naar een eerder tijdstip. Zie [herstel met behulp van back-ups](recovery-using-backups.md)voor meer informatie over herstel naar een bepaald tijdstip met behulp van back-ups.

Zie het volgende voor meer informatie over Azure SQL Database Elastic Database-hulpprogram ma's, geo-replicatie en herstel:

* [Overzicht: bedrijfs continuïteit in de Cloud en herstel na een Data Base met SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Aan de slag met hulpprogram ma's voor Elastic data base](elastic-scale-get-started.md)  
* [ShardMap-beheer](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Toewijzingen oplossen ophalen uit een ShardMapManager

De eerste stap is het maken van een toewijzingen oplossen-exemplaar. De [methode GetRecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) retourneert de Recovery Manager voor het huidige [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) -exemplaar. Als u inconsistenties in de Shard-kaart wilt oplossen, moet u eerst de toewijzingen oplossen ophalen voor de desbetreffende Shard-toewijzing.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

In dit voor beeld wordt de toewijzingen oplossen geïnitialiseerd vanuit de ShardMapManager. De ShardMapManager met een ShardMap is ook al geïnitialiseerd.

Omdat met deze toepassings code de Shard-kaart zelf wordt gemanipuleerd, moeten de referenties die worden gebruikt in de fabrieks methode (in het vorige voor beeld smmConnectionString) referenties zijn met de machtigingen lezen/schrijven voor de GSM-data base waarnaar wordt verwezen door de connection string. Deze referenties verschillen meestal van referenties die worden gebruikt om verbindingen te openen voor gegevens afhankelijke route ring. Zie [referenties gebruiken in de client voor Elastic data base](elastic-scale-manage-credentials.md)voor meer informatie.

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Een Shard verwijderen uit de ShardMap nadat een Shard is verwijderd

De [methode DetachShard](/previous-versions/azure/dn842083(v=azure.100)) koppelt de gegeven Shard van de Shard-toewijzing en verwijdert toewijzingen die zijn gekoppeld aan de Shard.  

* De locatie parameter is de Shard locatie, de naam van de server en de data base, van de Shard die worden losgekoppeld.
* De para meter shardMapName is de naam van de Shard-toewijzing. Dit is alleen vereist wanneer meerdere Shard-kaarten worden beheerd door hetzelfde Shard-toewijzings beheer. Optioneel.

> [!IMPORTANT]
> Gebruik deze techniek alleen als u zeker weet dat het bereik voor de bijgewerkte toewijzing leeg is. In de bovenstaande methoden worden geen gegevens gecontroleerd voor het bereik dat wordt verplaatst, zodat u het beste controles in uw code kunt toevoegen.

In dit voor beeld wordt Shards verwijderd uit de Shard-toewijzing.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

De Shard-kaart weerspiegelt de Shard-locatie in de GSM vóór het verwijderen van de Shard. Omdat de Shard is verwijderd, wordt ervan uitgegaan dat dit opzettelijk was en het sharding-sleutel bereik niet meer in gebruik is. Als dat niet het geval is, kunt u herstel op tijdstippen uitvoeren. de Shard vanaf een eerder tijdstip herstellen. (In dat geval raadpleegt u de volgende sectie voor het detecteren van inconsistenties in Shard.) Zie [herstel van punt in tijd](recovery-using-backups.md)om te herstellen.

Omdat ervan wordt uitgegaan dat het verwijderen van de data base opzettelijk was, is de definitieve opschoon actie van de beheerder om de vermelding te verwijderen in de Shard in Shard-toewijzings beheer. Hiermee wordt voor komen dat de toepassing per ongeluk informatie schrijft naar een bereik dat niet wordt verwacht.

## <a name="to-detect-mapping-differences"></a>Toewijzings verschillen detecteren

De [methode DetectMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) selecteert en retourneert een van de Shard-kaarten (lokaal of globaal) als de bron van waarheid en vergelijkt toewijzingen op zowel Shard Maps (GSM en LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* De *locatie* geeft de naam van de server en de data base.
* De para meter *shardMapName* is de naam van de Shard-toewijzing. Dit is alleen vereist als meerdere Shard-kaarten worden beheerd door hetzelfde Shard-toewijzings beheer. Optioneel.

## <a name="to-resolve-mapping-differences"></a>Toewijzings verschillen oplossen

De [methode ResolveMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) selecteert een van de Shard-kaarten (lokaal of globaal) als de bron van de waarheid en vergelijkt toewijzingen op beide Shard Maps (GSM en LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* De *RecoveryToken* para meter geeft de verschillen in de toewijzingen tussen de GSM en het LSM voor de specifieke Shard.
* De [opsomming MappingDifferenceResolution](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) wordt gebruikt om de methode aan te geven voor het oplossen van het verschil tussen de Shard-toewijzingen.
* **MappingDifferenceResolution. KeepShardMapping** wordt aanbevolen dat wanneer de LSM de nauw keurige toewijzing bevat en daarom de toewijzing in de Shard moet worden gebruikt. Dit is meestal het geval als er een failover is: de Shard bevindt zich nu op een nieuwe server. Omdat de Shard eerst moet worden verwijderd uit de GSM (met behulp van de methode toewijzingen oplossen. DetachShard), bestaat er geen toewijzing meer op de GSM. Daarom moet de LSM worden gebruikt om de Shard-toewijzing opnieuw tot stand te brengen.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Een Shard koppelen aan de ShardMap nadat een Shard is hersteld

De [methode AttachShard](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) koppelt de opgegeven Shard aan de Shard-toewijzing. Vervolgens worden eventuele inconsistenties van de Shard-toewijzing gedetecteerd en worden de toewijzingen bijgewerkt zodat deze overeenkomen met de Shard op het moment dat het herstel van de Shard. Er wordt van uitgegaan dat de naam van de data base ook wordt gewijzigd in overeenstemming met de oorspronkelijke database naam (voordat de Shard is hersteld), omdat de standaard instellingen voor het herstellen van het tijdstip in een nieuwe Data Base worden toegevoegd met de tijds tempel.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* De *locatie* parameter is de naam van de server en de database naam van de Shard die wordt gekoppeld.
* De para meter *shardMapName* is de naam van de Shard-toewijzing. Dit is alleen vereist wanneer meerdere Shard-kaarten worden beheerd door hetzelfde Shard-toewijzings beheer. Optioneel.

In dit voor beeld wordt een Shard toegevoegd aan de Shard-toewijzing die onlangs is hersteld vanaf een eerdere punt-in-tijd. Omdat de Shard (dat wil zeggen de toewijzing voor de Shard in de LSM) is hersteld, is het mogelijk inconsistent met de Shard vermelding in de GSM. Buiten deze voorbeeld code is de Shard teruggezet en is de naam gewijzigd in de oorspronkelijke naam van de data base. Omdat deze is hersteld, wordt ervan uitgegaan dat de toewijzing in de LSM de vertrouwde toewijzing is.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Shard-locaties bijwerken na een geo-failover (herstellen) van de Shards

Als er sprake is van een geo-failover, wordt de secundaire data base toegankelijk gemaakt en wordt de nieuwe primaire data base. De naam van de server en mogelijk de data base (afhankelijk van uw configuratie) kan afwijken van de oorspronkelijke primaire. Daarom moeten de toewijzings vermeldingen voor de Shard in de GSM-en LSM worden opgelost. En als de data base wordt hersteld naar een andere naam of locatie of naar een eerder tijdstip, kan dit leiden tot inconsistenties in de Shard Maps. Het Shard-toewijzings beheer verwerkt de distributie van open verbindingen met de juiste data base. Distributie is gebaseerd op de gegevens in de Shard-kaart en de waarde van de sharding-sleutel die het doel is van de aanvraag van de toepassing. Na een geo-failover moet deze informatie worden bijgewerkt met de juiste server naam, database naam en Shard toewijzing van de herstelde data base.

## <a name="best-practices"></a>Aanbevolen procedures

Geo-failover en herstel zijn bewerkingen die doorgaans worden beheerd door een Cloud beheerder van de toepassing die opzettelijk gebruikmaakt van Azure SQL Database-functies voor bedrijfs continuïteit. Voor de planning van bedrijfs continuïteit zijn processen, procedures en metingen vereist om ervoor te zorgen dat bedrijfs activiteiten zonder onderbreking kunnen worden voortgezet. De methoden die beschikbaar zijn als onderdeel van de klasse toewijzingen oplossen, moeten binnen deze werk stroom worden gebruikt om ervoor te zorgen dat de GSM-en LSM up-to-date blijven, op basis van de uitgevoerde herstel actie. Er zijn vijf basis stappen om ervoor te zorgen dat de GSM-en LSM de nauw keurige informatie na een failover-gebeurtenis weer spie gelen. De toepassings code voor het uitvoeren van deze stappen kan worden geïntegreerd in bestaande hulpprogram ma's en werk stroom.

1. Haal de toewijzingen oplossen op uit de ShardMapManager.
2. Ontkoppel de oude Shard van de Shard-kaart.
3. Koppel de nieuwe Shard aan de Shard-kaart, met inbegrip van de nieuwe Shard-locatie.
4. Detecteer inconsistenties in de toewijzing tussen de GSM-en LSM.
5. Los de verschillen tussen de GSM en de LSM op en vertrouwt de LSM.

In dit voor beeld worden de volgende stappen uitgevoerd:

1. Verwijdert Shards uit de Shard-toewijzing die de Shard locaties vóór de failover-gebeurtenis weer spie gelen.
2. Koppelt Shards aan de Shard-toewijzing die de nieuwe Shard locaties weerspiegelt (de para meter ' configuratie. SecondaryServer ' is de nieuwe server naam, maar dezelfde database naam).
3. Hiermee worden de herstel tokens opgehaald door toewijzings verschillen tussen de GSM en de LSM voor elke Shard te detecteren.
4. Hiermee worden de inconsistenties opgelost door de toewijzing van de LSM van elke Shard te vertrouwen.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png