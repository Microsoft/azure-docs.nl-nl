---
title: Firewall regels-grootschalige (Citus)-Azure Database for PostgreSQL
description: In dit artikel worden de firewall regels voor Azure Database for PostgreSQL-grootschalige (Citus) beschreven.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90902090"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Firewall regels in Azure Database for PostgreSQL-grootschalige (Citus)
Azure Database for PostgreSQL Server firewall voor komt dat alle toegang tot uw grootschalige-coördinator knooppunt (Citus) wordt voor komen, totdat u opgeeft welke computers zijn gemachtigd. De firewall verleent toegang tot de server op basis van het oorspronkelijke IP-adres van elke aanvraag.
U configureert de firewall door firewallregels te maken die bereiken opgeven van acceptabele IP-adressen. U kunt Firewall regels maken op server niveau.

**Firewall regels:** Met deze regels kunnen clients toegang krijgen tot uw grootschalige (Citus)-coördinator knooppunt, dat wil zeggen, alle data bases binnen dezelfde logische server. Firewall regels op server niveau kunnen worden geconfigureerd met behulp van de Azure Portal. Als u firewall regels op server niveau wilt maken, moet u de eigenaar van het abonnement of een mede werker van het abonnement zijn.

## <a name="firewall-overview"></a>Firewalloverzicht
Alle database toegang tot uw coördinator knooppunt wordt standaard geblokkeerd door de firewall. Als u uw server vanaf een andere computer wilt gebruiken, moet u een of meer firewall regels op server niveau opgeven om toegang tot uw server mogelijk te maken. Gebruik de firewall regels om op te geven welke IP-adresbereiken van Internet moeten worden toegestaan. De firewall regels hebben geen invloed op de Azure Portal website zelf.
Verbindings pogingen via internet en Azure moeten eerst door de firewall worden door gegeven voordat ze uw PostgreSQL-data base kunnen bereiken, zoals wordt weer gegeven in het volgende diagram:

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Voor beeld van de werking van de firewall":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Verbinding maken via internet en Azure

Een grootschalige (Citus) Server groep firewall bepaalt wie verbinding kan maken met het coördinator knooppunt van de groep. De firewall bepaalt de toegang door een Configureer bare lijst met regels te raadplegen. Elke regel is een IP-adres of een bereik met adressen dat is toegestaan in.

Wanneer de firewall verbindingen blokkeert, kan dit leiden tot toepassings fouten. Als u het JDBC-stuur programma van PostgreSQL gebruikt, treedt er bijvoorbeeld een fout op als:

> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: fataal: geen PG \_ HBA. conf entry for host "123.45.67.890", User "Citus", data base "Citus", SSL

Zie [firewall regels maken en beheren](howto-hyperscale-manage-firewall-using-portal.md) voor meer informatie over het definiëren van de regels.

## <a name="troubleshooting-the-database-server-firewall"></a>Problemen met de database server firewall oplossen
Wanneer de toegang tot de Microsoft Azure-Data Base voor PostgreSQL-grootschalige (Citus) niet werkt zoals verwacht, moet u rekening houden met de volgende punten:

* **Wijzigingen in de acceptatie lijst zijn nog niet doorgevoerd:** Er kan vijf minuten duren voordat wijzigingen in de configuratie van de grootschalige-firewall (Citus) van kracht worden.

* **De gebruiker is niet gemachtigd of er is een onjuist wacht woord gebruikt:** Als een gebruiker geen machtigingen heeft op de server of het gebruikte wacht woord onjuist is, wordt de verbinding met de server geweigerd. Het maken van een firewall instelling biedt clients alleen de mogelijkheid om verbinding te maken met uw server. elke client moet nog steeds de benodigde beveiligings referenties opgeven.

Als u bijvoorbeeld een JDBC-client gebruikt, kan de volgende fout worden weer gegeven.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: fataal: wachtwoord verificatie is mislukt voor gebruiker ' yourusername '

* **Dynamisch IP-adres:** als u een internetverbinding hebt met dynamische IP-adressering en problemen ondervindt bij het passeren van de firewall, kunt u een van de volgende oplossingen proberen:

* Neem contact op met uw Internet provider (ISP) voor het IP-adres bereik dat is toegewezen aan uw client computers die toegang hebben tot het grootschalige (Citus)-coördinator knooppunt en voeg het IP-adres bereik vervolgens toe als een firewall regel.

* Neem in plaats daarvan statische IP-adressen op voor uw client computers en voeg vervolgens het statische IP-adres toe als een firewall regel.

## <a name="next-steps"></a>Volgende stappen
Zie voor artikelen over het maken van firewall regels op server niveau en op database niveau:
* [Azure Database for PostgreSQL firewall regels maken en beheren met behulp van de Azure Portal](howto-hyperscale-manage-firewall-using-portal.md)
