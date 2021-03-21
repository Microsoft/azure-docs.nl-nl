---
title: Azure SQL Edge beveiligen
description: Meer informatie over beveiliging in Azure SQL Edge
keywords: SQL Edge, beveiliging
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021011"
---
# <a name="securing-azure-sql-edge"></a>Azure SQL Edge beveiligen

Met de toename van de invoering van IoT en Edge computing in verschillende branches is er een toename van het aantal apparaten en de gegevens die op basis van deze apparaten worden gegenereerd. Het verhoogde volume aan gegevens en het aantal apparaat-eind punten vormt een aanzienlijke uitdaging op het gebied van beveiliging van gegevens en de apparaten. 

Azure SQL Edge biedt meerdere functies en mogelijkheden waarmee u de IoT-gegevens in de SQL Server-data bases relatief gemakkelijker kunt beveiligen. Azure SQL Edge is gebouwd met behulp van dezelfde SQL-engine die Microsoft SQL Server en Azure SQL bezorgt, waarbij dezelfde beveiligings mogelijkheden worden gedeeld, waardoor het eenvoudiger wordt om hetzelfde beveiligings beleid en dezelfde procedures van de Cloud naar de rand uit te breiden.

Net als bij Microsoft SQL Server en Azure SQL kan het beveiligen van Azure SQL Edge-implementaties worden beschouwd als een reeks stappen die betrekking hebben op vier gebieden: het platform, de verificatie, de objecten (inclusief gegevens) en toepassingen die toegang hebben tot het systeem. 

## <a name="platform-and-system-security"></a>Platform-en systeem beveiliging

Het platform voor Azure SQL Edge bevat de fysieke docker-host, het besturings systeem op de host en de netwerk systemen die het fysieke apparaat verbinden met toepassingen en clients. 

Het implementeren van platform beveiliging begint met het blijven van onbevoegde gebruikers buiten het netwerk. Enkele van de best practices zijn, maar zijn niet beperkt tot:
- Implementatie van firewall regels om te zorgen voor het beveiligings beleid van de organisatie. 
- Zorg ervoor dat voor het besturings systeem op het fysieke apparaat alle meest recente beveiligings updates zijn toegepast. 
- Host-poorten opgeven en beperken die worden gebruikt voor Azure SQL Edge
- Zorg ervoor dat het juiste toegangs beheer wordt toegepast op alle gegevens volumes die als host optreden voor Azure SQL Edge-gegevens. 

Zie [netwerk protocollen en TDS-eind punten](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105))voor meer informatie over Azure SQL Edge-netwerk protocollen en TDS-eind punten.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie 

### <a name="authentication"></a>Verificatie  
Verificatie is het proces waarbij de gebruiker wordt geclaimd. Azure SQL Edge ondersteunt momenteel alleen het `SQL Authentication` mechanisme.

- *SQL-verificatie*:

    SQL-verificatie verwijst naar de verificatie van een gebruiker bij het verbinden met Azure SQL Edge met behulp van de gebruikers naam en het wacht woord. Het wacht woord voor de SQL **sa** -aanmelding moet worden opgegeven tijdens de implementatie van SQL Edge. Daarna kunnen extra SQL-aanmeldingen en-gebruikers worden gemaakt door de server beheerder, waarmee gebruikers verbinding maken met behulp van de gebruikers naam en het wacht woord.

    Raadpleeg voor meer informatie over het maken en beheren van aanmeldingen en gebruikers in SQL Edge [een aanmelding maken](/sql/relational-databases/security/authentication-access/create-a-login) en [database gebruiker maken](/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Autorisatie   

Autorisatie verwijst naar de machtigingen die zijn toegewezen aan een gebruiker in een data base in Azure SQL Edge en bepaalt wat de gebruiker mag doen. Machtigingen worden beheerd door gebruikers accounts toe te voegen aan [database rollen](/sql/relational-databases/security/authentication-access/database-level-roles) en machtigingen op database niveau toe te wijzen aan deze rollen of door de gebruiker bepaalde [machtigingen op object niveau](/sql/relational-databases/security/permissions-database-engine)toe te kennen. Zie [aanmeldingen en gebruikers](../azure-sql/database/logins-create-manage.md)voor meer informatie.

Maak als best practice aangepaste rollen wanneer dat nodig is. Voeg gebruikers toe aan de rol met de mini maal benodigde bevoegdheden voor het uitvoeren van hun functie. Wijs machtigingen niet rechtstreeks toe aan gebruikers. Het account van de server beheerder is lid van de ingebouwde db_owner rol, met uitgebreide machtigingen en mag alleen worden toegekend aan een beperkt aantal gebruikers met beheerders rechten. Gebruik voor toepassingen de [uitvoeren als](/sql/t-sql/statements/execute-as-clause-transact-sql) om de uitvoerings context van de aangeroepen module op te geven of gebruik [toepassings rollen](/sql/relational-databases/security/authentication-access/application-roles) met beperkte machtigingen. Op deze manier zorgt u ervoor dat de toepassing die verbinding maakt met de data base, over de minste bevoegdheden beschikt die nodig zijn voor de toepassing. Het volgen van deze aanbevolen procedures bevordert ook de schei ding van taken.

## <a name="database-object-security"></a>Beveiliging data base-object

Principals zijn de personen, groepen en processen die toegang tot SQL Edge hebben gekregen. ' Beveilig bare items ' zijn de server, data base en objecten die de Data Base bevat. Elk beschikt over een set machtigingen die kan worden geconfigureerd om de surface area te verminderen. De volgende tabel bevat informatie over principals en beveilig bare items.

|Voor informatie over|Raadpleeg|  
|---------------------------|---------|  
|Server-en database gebruikers, rollen en processen|[Data base-engine voor principals](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Beveiliging van server-en database objecten|[Beveilig bare items](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Versleuteling en certificaten  
 
Met versleuteling worden problemen met toegangs beheer niet opgelost. Het verbetert echter de beveiliging door gegevens verlies te beperken, zelfs in het zeldzame geval dat toegangs beheer wordt overgeslagen. Als de computer van de data base bijvoorbeeld onjuist is geconfigureerd en een kwaadwillende gebruiker gevoelige gegevens ophaalt, zoals creditcard nummers, is het mogelijk dat gestolen gegevens overbodig zijn als deze zijn versleuteld. De volgende tabel bevat meer informatie over versleuteling in Azure SQL Edge.  
  
|Voor informatie over|Raadpleeg|  
|---------------------------|---------|  
|Beveiligde verbindingen implementeren|[Verbindingen versleutelen](/sql/linux/sql-server-linux-encrypted-connections)|  
|Versleutelings functies|[Cryptografische functies &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Gegevens versleuteling in rust|[Transparante gegevensversleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Altijd versleuteld|[Altijd versleuteld](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> De beveiligings beperkingen die worden beschreven voor [SQL Server on Linux](/sql/linux/sql-server-linux-security-overview) , zijn ook van toepassing op Azure SQL Edge. 


> [!NOTE]
> Het hulp programma MSSQL-conf is niet inbegrepen in de Edge van Azure SQL. Alle configuraties inclusief configuratie van versleutelings problemen moeten worden uitgevoerd via de bestand-of [omgevings variabelen](configure.md#configure-by-using-environment-variables) [MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file) . 


Net als bij Azure SQL en Microsoft SQL Server biedt Azure SQL Edge hetzelfde mechanisme voor het maken en gebruiken van certificaten om de beveiliging van objecten en verbindingen te verbeteren. Zie voor meer informatie [certificaat maken (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Toepassingsbeveiliging

### <a name="client-programs"></a>Client Programma's

De aanbevolen procedures voor de beveiliging van Azure SQL Edge zijn het schrijven van beveiligde client toepassingen. Zie [client-netwerk configuratie](/sql/database-engine/configure-windows/client-network-configuration)voor meer informatie over het beveiligen van client toepassingen op de netwerklaag.

### <a name="security-catalog-views-and-functions"></a>Beveiligings catalogus weergaven en-functies  
Beveiligings informatie wordt weer gegeven in verschillende weer gaven en functies die zijn geoptimaliseerd voor prestaties en hulp programma. De volgende tabel bevat informatie over beveiligings weergaven en-functies in Azure SQL Edge.  
  
|Functies en weer gaven|Koppelingen|  
|---------------------------|---------|  
|Beveiligings catalogus weergaven, die informatie retour neren over machtigingen op database niveau en op server niveau, principals, rollen, enzovoort. Daarnaast zijn er catalogus weergaven die informatie geven over de versleutelings sleutels, certificaten en referenties.|[Beveiligings catalogus weergaven &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Beveiligings functies, die informatie over de huidige gebruiker, machtigingen en schema's retour neren.|[Beveiligings functies &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Dynamische beheer weergaven voor beveiliging.|[Met beveiliging gerelateerde dynamische beheer weergaven en-functies &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Controleren 

Azure SQL Edge biedt dezelfde controle mechanismen als SQL Server. Zie [SQL Server audit (data base-engine)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met beveiligings functies](/sql/linux/sql-server-linux-security-get-started)
- [Azure SQL Edge uitvoeren als niet-hoofd gebruiker](configure.md#run-azure-sql-edge-as-non-root-user)
- [Azure Security Center voor IoT](../defender-for-iot/overview.md)