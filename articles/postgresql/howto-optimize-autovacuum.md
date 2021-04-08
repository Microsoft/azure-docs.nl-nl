---
title: Autoonderdruk optimaliseren-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u autovacuüm kunt optimaliseren op een Azure Database for PostgreSQL-één server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012558"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Autoonderdruk op een Azure Database for PostgreSQL-één server optimaliseren

In dit artikel wordt beschreven hoe u autovacuüm op een Azure Database for PostgreSQL Server effectief optimaliseert.

## <a name="overview-of-autovacuum"></a>Overzicht van autovacuüm

PostgreSQL maakt gebruik van gelijktijdigheids beheer met meerdere versies (MVCC) om een grotere database gelijktijdigheid toe te staan. Elke update resulteert in een invoegen en verwijderen en elke verwijderings bewerking resulteert in rijen die zacht worden gemarkeerd voor verwijdering. Zacht markeren identificeert Dead-Tuples die later worden verwijderd. PostgreSQL voert een vacuüm taak uit om deze taken uit te voeren.

Een vacuüm taak kan hand matig of automatisch worden geactiveerd. Er zijn meer dode Tuples wanneer de data base zware werk-of verwijderings bewerkingen ondervindt. Er zijn minder Dead-Tuples wanneer de data base niet actief is. U moet regel matig een vacuüm van de belasting van de data base maken, waardoor vacuüm taken *hand matig* onhandig worden uitgevoerd.

Autovacuüm kan worden geconfigureerd en voor delen van het afstemmen. De standaard waarden die PostgreSQL worden geleverd, proberen te controleren of het product op allerlei apparaten werkt. Deze apparaten zijn Raspberry Pis. De ideale configuratie waarden zijn afhankelijk van het volgende:

- Totaal aantal beschik bare resources, zoals SKU en opslag grootte.
- Resource gebruik.
- Afzonderlijke object kenmerken.

## <a name="autovacuum-benefits"></a>Voor delen van autovacuüm

Als u niet van tijd tot tijd vacuüm, kunnen de niet-actieve Tuples die worden samengevoegd resulteren in:

- Gegevens grootten, zoals grotere data bases en tabellen.
- Grotere suboptimale indexen.
- Meer I/O-bewerkingen.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Meer bewaken met autovacuüm query's

De volgende voorbeeld query is ontworpen om het aantal dode en actieve Tuples in een tabel met de naam XYZ te identificeren:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Autovacuüm-configuraties

De configuratie parameters die de autoonderdruk bepalen, zijn gebaseerd op de antwoorden op twee belang rijke vragen:

- Wanneer moet het worden gestart?
- Hoeveel moet er worden opgeschoond nadat het is gestart?

Hier volgt een aantal para meters voor automatische vacuüm configuratie die u op basis van de vorige vragen kunt bijwerken, samen met een aantal richt lijnen.

Parameter|Beschrijving|Standaardwaarde
---|---|---
autovacuum_vacuum_threshold|Hiermee geeft u het minimale aantal bijgewerkte of verwijderde Tuples op dat nodig is om een vacuüm bewerking in een tabel te activeren. De standaard waarde is 50 Tuples. Stel deze para meter alleen in het bestand postgresql. conf of op de server opdracht regel in. Als u de instelling voor afzonderlijke tabellen wilt onderdrukken, wijzigt u de para meters voor de tabel opslag.|50
autovacuum_vacuum_scale_factor|Hiermee geeft u een fractie van de tabel grootte op die moet worden toegevoegd aan autovacuum_vacuum_threshold bij het bepalen of een vacuüm bewerking moet worden geactiveerd. De standaard waarde is 0,2. Dit is 20 procent van de tabel grootte. Stel deze para meter alleen in het bestand postgresql. conf of op de server opdracht regel in. Als u de instelling voor afzonderlijke tabellen wilt onderdrukken, wijzigt u de para meters voor de tabel opslag.|0,2
autovacuum_vacuum_cost_limit|Hiermee geeft u de kosten limiet waarde op die in automatische vacuüm bewerkingen wordt gebruikt. Als-1 is opgegeven, dat wil zeggen de standaard waarde, wordt de regel reguliere vacuum_cost_limit gebruikt. Als er meer dan één werk nemer is, wordt de waarde proportioneel verdeeld over de actieve autoonderdruk-werk rollen. De som van de limieten voor elke werk nemer overschrijdt niet de waarde van deze variabele. Stel deze para meter alleen in het bestand postgresql. conf of op de server opdracht regel in. Als u de instelling voor afzonderlijke tabellen wilt onderdrukken, wijzigt u de para meters voor de tabel opslag.|-1
autovacuum_vacuum_cost_delay|Hiermee geeft u de waarde voor de kosten vertraging op die in automatische vacuüm bewerkingen wordt gebruikt. Als-1 is opgegeven, wordt de reguliere vacuum_cost_delay-waarde gebruikt. De standaard waarde is 20 milliseconden. Stel deze para meter alleen in het bestand postgresql. conf of op de server opdracht regel in. Als u de instelling voor afzonderlijke tabellen wilt onderdrukken, wijzigt u de para meters voor de tabel opslag.|20 MS
autovacuum_naptime | Hiermee geeft u de minimale vertraging tussen autovacuüm-uitvoeringen op een bepaalde data base. Bij elke afronding onderzoekt de daemon de data base en worden vacuüm-en analyse opdrachten voor tabellen in die data base onderzocht. De vertraging wordt gemeten in seconden. Stel deze para meter alleen in het bestand postgresql. conf of op de server opdracht regel in.| 15 s
autovacuum_max_workers | Hiermee geeft u het maximum aantal autovacuüm-processen op, behalve het autovacuüm-start programma, dat op elk gewenst moment kan worden uitgevoerd. De standaard waarde is drie. Stel deze para meter alleen in op het starten van de server.|3

Als u de instellingen voor afzonderlijke tabellen wilt overschrijven, wijzigt u de para meters voor de tabel opslag.

## <a name="autovacuum-cost"></a>Autovacuüm kosten

Dit zijn de kosten van het uitvoeren van een vacuüm bewerking:

- De gegevens pagina's waarop de vacuüm wordt uitgevoerd, worden vergrendeld.
- Compute en Memory worden gebruikt wanneer een vacuüm taak wordt uitgevoerd.

Als gevolg hiervan moet u geen vacuüm taken te vaak of te vaak uitvoeren. Een vacuüm taak moet worden aangepast aan de workload. Test alle wijzigingen in de autovacuüm-para meter door de verschillende afwegingen.

## <a name="autovacuum-start-trigger"></a>Trigger voor automatische vacuüm starten

Autoonderdruk wordt geactiveerd wanneer het aantal dode Tuples groter is dan autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Hier is reltuples een constante.

Opschonen van de autoonderdruk moet de data base laden. Anders is het mogelijk dat u geen opslag ruimte meer hebt en een grote vertraging in query's ondervindt. De snelheid waarmee een vacuüm bewerking opschoont, moet gelijk zijn aan de snelheid waarmee Dead-Tuples worden gemaakt.

Data bases met veel updates en verwijderingen hebben meer dode Tuples en hebben meer ruimte nodig. Over het algemeen profiteren data bases met veel updates en verwijderingen van lage waarden autovacuum_vacuum_scale_factor en autovacuum_vacuum_threshold. De lage waarden voor komen een langdurige accumulatie van Dead-Tuples. U kunt hogere waarden gebruiken voor beide para meters met kleinere data bases, omdat de behoefte aan vacuüm minder urgent is. Veelvuldige vacuüming is gebaseerd op de kosten van reken kracht en het geheugen.

De standaard schaal factor van 20 procent werkt goed voor tabellen met een laag percentage dode Tuples. Het werkt niet goed op tabellen met een hoog percentage dode Tuples. In een tabel van 20 GB wordt deze schaal factor bijvoorbeeld omgezet in 4 GB aan Dead-Tuples. In een tabel van 1 TB zijn er 200 GB aan Dead-Tuples.

Met PostgreSQL kunt u deze para meters instellen op tabel niveau of op instantie niveau. U kunt deze para meters nu alleen op tabel niveau instellen in Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>De kosten van de autovacuüm schatten

Het uitvoeren van autovacuüm is ' kostbaar ' en er zijn para meters voor het beheren van de runtime van vacuüm bewerkingen. De volgende para meters helpen u bij het schatten van de kosten van het uitvoeren van vacuüm:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Met het vacuüm proces worden fysieke pagina's en controles voor onbestelbare Tuples gelezen. Voor elke pagina in shared_buffers wordt aangenomen dat de kosten 1 (vacuum_cost_page_hit) zijn. Voor alle andere pagina's geldt een prijs van 20 (vacuum_cost_page_dirty), als er sprake is van Dead-Tuples of 10 (vacuum_cost_page_miss) als er geen Dead-Tuples bestaan. De vacuüm bewerking stopt wanneer het proces de autovacuum_vacuum_cost_limit overschrijdt.

Nadat de limiet is bereikt, wordt het proces in slaap stand gezet voor de duur die is opgegeven door de para meter autovacuum_vacuum_cost_delay voordat deze opnieuw wordt gestart. Als de limiet niet is bereikt, begint autovacuüm na de waarde die is opgegeven door de para meter autovacuum_nap_time.

In samen vatting wordt met de para meters autovacuum_vacuum_cost_delay en autovacuum_vacuum_cost_limit bepaald hoeveel gegevens opschonen per tijds eenheid is toegestaan. Houd er rekening mee dat de standaard waarden voor de meeste prijs categorieën te laag zijn. De optimale waarden voor deze para meters zijn prijs categorie afhankelijk en moeten dienovereenkomstig worden geconfigureerd.

De para meter autovacuum_max_workers bepaalt het maximum aantal autovacuüm-processen dat tegelijkertijd kan worden uitgevoerd.

Met PostgreSQL kunt u deze para meters instellen op tabel niveau of op instantie niveau. U kunt deze para meters nu alleen op tabel niveau instellen in Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Autoonderdruk per tabel optimaliseren

U kunt alle voor gaande configuratie parameters per tabel configureren. Hier volgt een voorbeeld:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuüm is een synchroon proces per tabel. Het grotere percentage dode Tuples dat een tabel heeft, des te hoger de "kosten" voor autovacuüm. U kunt tabellen splitsen met een hoog aantal updates en verwijderen in meerdere tabellen. Het splitsen van tabellen helpt bij het parallelliserenen van autovacuüm en het verminderen van de ' kosten ' voor het volt ooien van autoonderdruk in één tabel. U kunt ook het aantal parallelle autovacuüm werkers verhogen om ervoor te zorgen dat werk nemers op vrije tijd worden gepland.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende PostgreSQL-documentatie voor meer informatie over het gebruik van en het afstemmen van de autoonderdruk:

- [Hoofd stuk 18, Server configuratie](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Hoofd stuk 24, onderhouds taken voor routine-data bases](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
