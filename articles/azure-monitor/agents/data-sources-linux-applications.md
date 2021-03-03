---
title: Prestaties van Linux-toepassingen verzamelen in Azure Monitor | Microsoft Docs
description: In dit artikel vindt u informatie over het configureren van de Log Analytics-agent voor Linux voor het verzamelen van prestatie meter items voor MySQL-en Apache HTTP-server.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: f924bcdce0937c74c3deeab96f3d4b2a85e3944d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704443"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Verzamelen van prestatie meter items voor Linux-toepassingen in Azure Monitor 

In dit artikel vindt u informatie over het configureren van de [log Analytics-agent voor Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) voor het verzamelen van prestatie meter items voor specifieke toepassingen in azure monitor.  De toepassingen die in dit artikel zijn opgenomen, zijn:  

- [MySQL](#mysql)
- [Apache HTTP-server](#apache-http-server)

## <a name="mysql"></a>MySQL
Als de MySQL-server of de MariaDB-server wordt gedetecteerd op de computer wanneer de Log Analytics-agent is geïnstalleerd, wordt automatisch een provider voor prestatie bewaking voor MySQL-server geïnstalleerd. Deze provider maakt verbinding met de lokale MySQL/MariaDB-server om prestatie statistieken beschikbaar te maken. MySQL-gebruikers referenties moeten zo worden geconfigureerd dat de provider toegang kan krijgen tot de MySQL-server.

### <a name="configure-mysql-credentials"></a>MySQL-referenties configureren
De MySQL OMI-provider vereist een vooraf geconfigureerde MySQL-gebruiker en geïnstalleerde MySQL-client bibliotheken om de prestatie-en status informatie van het MySQL-exemplaar te kunnen opvragen.  Deze referenties worden opgeslagen in een verificatie bestand dat is opgeslagen in de Linux-agent.  Het verificatie bestand geeft aan op welk adres en welke poort het MySQL-exemplaar luistert en welke referenties moeten worden gebruikt voor het verzamelen van metrische gegevens.  

Tijdens de installatie van de Log Analytics-agent voor Linux scant de MySQL OMI-provider MySQL mijn. cnf-configuratie bestanden (standaard locaties) voor een bind adres en poort, en het MySQL OMI-verificatie bestand gedeeltelijk instellen.

Het MySQL-verificatie bestand wordt opgeslagen op `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth` .


### <a name="authentication-file-format"></a>Bestands indeling voor verificatie
Dit is de indeling voor het MySQL OMI-verificatie bestand

> [Poort] = [binding-adres], [gebruikers naam], [base64-gecodeerd wacht woord]  
> (Poort) = (binding-adres), (gebruikers naam), (base64-gecodeerd wacht woord)  
> (Poort) = (binding-adres), (gebruikers naam), (base64-gecodeerd wacht woord)  
> Auto update = [True | False]  

De vermeldingen in het verificatie bestand worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--|:--|
| Poort | Hiermee wordt de huidige poort van het MySQL-exemplaar geluisterd. Poort 0 geeft aan dat de volgende eigenschappen worden gebruikt voor het standaard exemplaar. |
| Bind-Address| Huidig MySQL-bindings adres. |
| gebruikersnaam| MySQL-gebruiker die wordt gebruikt om het MySQL-Server exemplaar te bewaken. |
| Base64-gecodeerd wacht woord| Wacht woord van de MySQL-bewakings gebruiker die is gecodeerd in base64. |
| Auto update| Hiermee geeft u op of wijzigingen in het bestand My. cnf opnieuw moeten worden gescand en het MySQL OMI-verificatie bestand moet worden overschreven wanneer de MySQL OMI-provider wordt bijgewerkt. |

### <a name="default-instance"></a>Standaardexemplaar
Het MySQL OMI-verificatie bestand kan een standaard exemplaar en poort nummer definiëren om het beheer van meerdere MySQL-instanties op één Linux-host eenvoudiger te maken.  Het standaard exemplaar wordt aangeduid met een instantie met poort 0. Alle extra instanties nemen eigenschappen over van het standaard exemplaar, tenzij ze verschillende waarden opgeven. Als bijvoorbeeld het MySQL-exemplaar dat luistert op poort 3308 is toegevoegd, worden het standaard exemplaar van het binding-adres, de gebruikers naam en het met base64 gecodeerde wacht woord gebruikt om het exemplaar te controleren op 3308. Als het exemplaar op 3308 is gebonden aan een ander adres en dezelfde MySQL-gebruikers naam en-wacht woord gebruikt, zijn alleen het binding-adres nodig en worden de andere eigenschappen overgenomen.

De volgende tabel bevat voor beelden van exemplaar-instellingen 

| Beschrijving | File |
|:--|:--|
| Standaard exemplaar en-exemplaar met poort 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Standaard exemplaar en-exemplaar met poort 3308 en andere gebruikers naam en wacht woord. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programma voor MySQL OMI-verificatie bestand
Inbegrepen bij de installatie van de MySQL OMI-provider is een MySQL OMI-verificatie bestand dat kan worden gebruikt voor het bewerken van het MySQL OMI-verificatie bestand. Het verificatie bestand programma bevindt zich op de volgende locatie.

`/opt/microsoft/mysql-cimprov/bin/mycimprovauth`

> [!NOTE]
> Het referentie bestand moet leesbaar zijn voor het omsagent-account. U wordt aangeraden de opdracht mycimprovauth uit te voeren als omsgent.

De volgende tabel bevat informatie over de syntaxis voor het gebruik van mycimprovauth.

| Bewerking | Voorbeeld | Beschrijving
|:--|:--|:--|
| Auto update *False of True* | mycimprovauth auto update False | Hiermee stelt u in of het verificatie bestand automatisch wordt bijgewerkt bij opnieuw opstarten of bijwerken. |
| standaard *wachtwoord voor bind adres gebruikers naam* | mycimprovauth standaard 127.0.0.1 hoofd-pwd | Hiermee stelt u het standaard exemplaar in het MySQL OMI-verificatie bestand.<br>Het veld wacht woord moet worden ingevoerd in tekst zonder opmaak: het wacht woord in het MySQL OMI-verificatie bestand is base 64 gecodeerd. |
| *standaard of port_num* verwijderen | mycimprovauth 3308 | Hiermee verwijdert u het opgegeven exemplaar standaard of op poort nummer. |
| help | Help bij mycimprov | Een lijst met te gebruiken opdrachten afdrukken. |
| afdrukken | mycimprov afdrukken | Hiermee drukt u een eenvoudig te lezen MySQL OMI-verificatie bestand. |
| port_num *wacht woord van gebruikers naam voor bind-adres* bijwerken | mycimprov update 3307 127.0.0.1 root pwd | Hiermee wordt het opgegeven exemplaar bijgewerkt of wordt het exemplaar toegevoegd als het niet bestaat. |

Met de volgende voorbeeld opdrachten wordt een standaard gebruikers account gedefinieerd voor de MySQL-server op localhost.  Het veld wacht woord moet worden ingevoerd in tekst zonder opmaak: het wacht woord in het MySQL OMI-verificatie bestand is base 64 gecodeerd

```console
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
sudo /opt/omi/bin/service_control restart
```

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Database machtigingen die zijn vereist voor MySQL-prestatie meter items
De MySQL-gebruiker heeft toegang nodig tot de volgende query's voor het verzamelen van prestatie gegevens van de MySQL-server. 

```sql
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

De MySQL-gebruiker moet ook toegang tot de volgende standaard tabellen selecteren.

- information_schema
- mysql. 

Deze bevoegdheden kunnen worden verleend door de volgende Grant-opdrachten uit te voeren.

```sql
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

> [!NOTE]
> Voor het verlenen van machtigingen aan een MySQL-bewakings gebruiker moet de gebruiker die de Grant heeft verleend de bevoegdheid ' optie verlenen ' hebben, evenals de bevoegdheid die wordt toegekend.

### <a name="define-performance-counters"></a>Prestatie meter items definiëren

Zodra u de Log Analytics-agent voor Linux hebt geconfigureerd om gegevens te verzenden naar Azure Monitor, moet u de prestatie meter items configureren die moeten worden verzameld.  Gebruik de procedure in [Windows-en Linux-prestatie gegevens bronnen in azure monitor](data-sources-performance-counters.md) met de tellers in de volgende tabel.

| Object naam | Tellernaam |
|:--|:--|
| MySQL-database | Schijf ruimte in bytes |
| MySQL-database | Tabellen |
| MySQL-server | Gebroken verbindings percentage |
| MySQL-server | Verbinding gebruiken pct |
| MySQL-server | Schijfruimte gebruik in bytes |
| MySQL-server | Volledige tabel scan pct |
| MySQL-server | Percentage treffers in InnoDB buffer pool |
| MySQL-server | InnoDB buffer pool gebruiken pct |
| MySQL-server | InnoDB buffer pool gebruiken pct |
| MySQL-server | Percentage treffers in sleutel cache |
| MySQL-server | Pct gebruiken in sleutel cache |
| MySQL-server | Key cache-schrijf-pct |
| MySQL-server | Percentage treffers in query cache |
| MySQL-server | De query cache haalt pct op |
| MySQL-server | Pct gebruiken in query cache |
| MySQL-server | Percentage treffers in tabel cache |
| MySQL-server | Percentage van tabel cache gebruiken |
| MySQL-server | Pct-inhouds vergrendeling tabel |

## <a name="apache-http-server"></a>Apache HTTP-server 
Als de Apache HTTP-server op de computer wordt gedetecteerd wanneer de omsagent-bundel is geïnstalleerd, wordt automatisch een provider voor prestatie bewaking voor de Apache HTTP-server geïnstalleerd. Deze provider is afhankelijk van een Apache-module die moet worden geladen in de Apache HTTP-server om toegang te krijgen tot prestatie gegevens. De module kan worden geladen met de volgende opdracht:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Als u de Apache-bewakings module wilt verwijderen, voert u de volgende opdracht uit:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Prestatie meter items definiëren

Zodra u de Log Analytics-agent voor Linux hebt geconfigureerd om gegevens te verzenden naar Azure Monitor, moet u de prestatie meter items configureren die moeten worden verzameld.  Gebruik de procedure in [Windows-en Linux-prestatie gegevens bronnen in azure monitor](data-sources-performance-counters.md) met de tellers in de volgende tabel.

| Object naam | Tellernaam |
|:--|:--|
| Apache HTTP-server | Drukke werk nemers |
| Apache HTTP-server | Niet-actieve werk nemers |
| Apache HTTP-server | Werk nemers met pct-druk |
| Apache HTTP-server | Totale pct-CPU |
| Apache virtuele host | Fouten per minuut-client |
| Apache virtuele host | Fouten per minuut-server |
| Apache virtuele host | KB per aanvraag |
| Apache virtuele host | Aantal aanvragen KB per seconde |
| Apache virtuele host | Aanvragen per seconde |



## <a name="next-steps"></a>Volgende stappen
* [Verzamelen van prestatie meter items](data-sources-performance-counters.md) van Linux-agents.
* Meer informatie over [logboek query's](../logs/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.