---
title: Beperkingen-Azure Database for MySQL flexibele server
description: In dit artikel worden de beperkingen in Azure Database for MySQL flexibele server beschreven, zoals het aantal opties voor verbinding en opslag engine.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 48aef337326d58b2a503dc48862571efde0d37ab
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034517"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Beperkingen in Azure Database for MySQL flexibele server (preview-versie)

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel worden de beperkingen in de Azure Database for MySQL flexibele Server-service beschreven. [Algemene beperkingen](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) in de MySQL-data base-engine zijn ook van toepassing. Als u meer wilt weten over resource-lagen (compute, geheugen, opslag), raadpleegt u het artikel [Compute and Storage](concepts-compute-storage.md) .

## <a name="server-parameters"></a>Serverparameters

> [!NOTE]
> Als u op zoek bent naar de minimale/maximale waarden voor server `max_connections` parameters `innodb_buffer_pool_size` , zoals en, is deze informatie verplaatst naar het artikel [server parameters](./concepts-server-parameters.md) concepten-hoofd artikelen.

Azure Database for MySQL biedt ondersteuning voor het afstemmen van de waarden van server parameters. De minimum-en maximum waarde van sommige para meters (bijvoorbeeld `max_connections`, `join_buffer_size` , `query_cache_size` ) wordt bepaald door de compute-laag en de reken grootte van de server. Raadpleeg [server parameters](./concepts-server-parameters.md) voor meer informatie over deze limieten.

De invoeg toepassingen voor wacht woorden, zoals ' validate_password ' en ' caching_sha2_password ', worden niet ondersteund door de service.

## <a name="storage-engines"></a>Opslag engines

MySQL ondersteunt veel opslag engines. Op Azure Database for MySQL flexibele server worden de volgende opslag engines ondersteund en niet ondersteund:

### <a name="supported"></a>Ondersteund
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [GEHEUGENMETABASE](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Niet ondersteund
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [FAXBERICHTEN](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Federatie](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Bevoegdheden & ondersteuning voor gegevens manipulatie

Veel server parameters en-instellingen kunnen per ongeluk de prestaties van de server afnemen of de ACID-eigenschappen van de MySQL-server negatien. Voor het onderhouden van de service-integriteit en SLA op het niveau van een product, worden met deze service niet meerdere rollen beschikbaar. 

De MySQL-service staat geen directe toegang tot het onderliggende bestands systeem toe. Sommige opdrachten voor het bewerken van gegevens worden niet ondersteund. 

### <a name="unsupported"></a>Niet ondersteund

Het volgende wordt niet ondersteund:
- Rol van DBA: beperkt. U kunt ook de gebruiker beheerder (gemaakt tijdens het maken van een nieuwe server) gebruiken om de meeste DDL-en DML-instructies uit te voeren. 
- SUPER bevoegdheid: op dezelfde manier is [Super privilege](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) beperkt.
- DEFINE: vereist Super privileges om te maken en beperkt. Als u gegevens importeert met behulp van een back-up, verwijdert u de `CREATE DEFINER` opdrachten hand matig of gebruikt u de `--skip-definer` opdracht bij het uitvoeren van een mysqldump.
- Systeem databases: de [MySQL-systeem database](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) is alleen-lezen en wordt gebruikt ter ondersteuning van verschillende PaaS-functionaliteit. U kunt geen wijzigingen aanbrengen in de `mysql` systeem database.
- `SELECT ... INTO OUTFILE`: Wordt niet ondersteund in de service.

### <a name="supported"></a>Ondersteund
- `LOAD DATA INFILE` wordt ondersteund, maar de `[LOCAL]` para meter moet worden opgegeven en worden omgeleid naar een UNC-pad (Azure-opslag gekoppeld aan SMB).

## <a name="functional-limitations"></a>Functionele beperkingen

### <a name="zone-redundant-ha"></a>Zone redundante HA
- Deze configuratie kan alleen worden ingesteld tijdens het maken van de server.
- Niet ondersteund in een Burstive Compute-laag.

### <a name="networking"></a>Netwerken
- De verbindings methode kan niet worden gewijzigd na het maken van de server. Als de server is gemaakt met *persoonlijke toegang (VNet-integratie)*, kan deze niet meer worden gewijzigd in *open bare toegang (toegestane IP-adressen)* na het maken, en andersom
- TLS/SSL is standaard ingeschakeld en kan niet worden uitgeschakeld.
- De minimale TLS-versie die op de server wordt ondersteund, is TLS 1.2. Raadpleeg [verbinding maken met behulp van TLS/SSL](./how-to-connect-tls-ssl.md) voor meer informatie.

### <a name="stopstart-operation"></a>Stoppen/starten-bewerking
- Niet ondersteund met zone-redundante HA-configuraties (primair en stand-by).
- Niet ondersteund bij het lezen van replica configuraties (zowel bron-als replica).

### <a name="scale-operations"></a>Schaal bewerkingen
- Het verminderen van de ingerichte Server opslag wordt niet ondersteund.

### <a name="read-replicas"></a>Leesreplica's
- Niet ondersteund met zone-redundante HA-configuraties (primair en stand-by).

### <a name="server-version-upgrades"></a>Server versie-upgrades
- Automatische migratie tussen de primaire data base-engine versies wordt niet ondersteund. Als u een upgrade wilt uitvoeren voor de primaire versie, neemt u een [dump op en herstelt](../concepts-migrate-dump-restore.md) u deze op een server die is gemaakt met de nieuwe engine versie.

### <a name="restoring-a-server"></a>Een server herstellen
- Bij herstel naar een bepaald tijdstip worden nieuwe servers gemaakt met dezelfde Compute-en opslag configuraties als de bron server waarop deze is gebaseerd. De herstelde server Compute kan worden geschaald nadat de server is gemaakt.
- Het herstellen van een verwijderde server wordt niet ondersteund.

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>Beschik bare functies op één server, maar nog niet ondersteund op de flexibele server 
Niet alle functies die beschikbaar zijn in Azure Database for MySQL-één server, zijn nog beschikbaar op de flexibele server. Voor een volledige lijst met functie vergelijking tussen één server en een flexibele server raadpleegt u [de optie juiste mysql-server kiezen in azure-documentatie.](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het kiezen van de juiste mysql-server optie in azure-documentatie](../select-right-deployment-type.md)
- Inzicht [in wat er beschikbaar is voor de berekenings-en opslag opties van een flexibele server](concepts-compute-storage.md)
- Meer informatie over [ondersteunde mysql-versies](concepts-supported-versions.md)
- Snelstartgids: [Gebruik de Azure portal voor het maken van een Azure database for MySQL flexibele server](quickstart-create-server-portal.md)
