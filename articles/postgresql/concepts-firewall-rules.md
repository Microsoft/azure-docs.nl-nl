---
title: Firewall regels-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u firewall regels gebruikt om verbinding te maken met Azure Database for PostgreSQL-één-server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 08c0d05ac10d9e61497d36793740c8e827fbeca1
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903680"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Firewall regels in Azure Database for PostgreSQL-één server
Azure Database for PostgreSQL Server firewall voor komt dat de toegang tot uw database server wordt verhinderd totdat u opgeeft welke computers zijn gemachtigd. De firewall verleent toegang tot de server op basis van het oorspronkelijke IP-adres van elke aanvraag.
U configureert de firewall door firewallregels te maken die bereiken opgeven van acceptabele IP-adressen. U kunt Firewall regels maken op server niveau.

**Firewall regels:** Met deze regels kunnen clients toegang krijgen tot uw hele Azure Database for PostgreSQL server, dat wil zeggen, alle data bases binnen dezelfde logische server. Firewall regels op server niveau kunnen worden geconfigureerd met behulp van de Azure Portal of met Azure CLI-opdrachten. Als u firewall regels op server niveau wilt maken, moet u de eigenaar van het abonnement of een mede werker van het abonnement zijn.

## <a name="firewall-overview"></a>Firewalloverzicht
Alle database toegang tot uw Azure Database for PostgreSQL-server wordt standaard geblokkeerd door de firewall. Als u uw server vanaf een andere computer wilt gebruiken, moet u een of meer firewall regels op server niveau opgeven om toegang tot uw server mogelijk te maken. Gebruik de firewall regels om op te geven welke IP-adresbereiken van Internet moeten worden toegestaan. De firewall regels hebben geen invloed op de Azure Portal website zelf.
Verbindings pogingen via internet en Azure moeten eerst door de firewall worden door gegeven voordat ze uw PostgreSQL-data base kunnen bereiken, zoals wordt weer gegeven in het volgende diagram:

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Voor beeld van de werking van de firewall":::

## <a name="connecting-from-the-internet"></a>Verbinding maken vanuit internet
Firewall regels op server niveau zijn van toepassing op alle data bases op dezelfde Azure Database for PostgreSQL server. Als het IP-adres van de aanvraag binnen een van de bereiken ligt die zijn opgegeven in de firewallregels op serverniveau, wordt de verbinding toegestaan.
Als het IP-adres van de aanvraag niet binnen de bereiken valt die zijn opgegeven in een van de firewall regels op server niveau, mislukt de verbindings aanvraag.
Als uw toepassing bijvoorbeeld verbinding maakt met het JDBC-stuur programma voor PostgreSQL, kan deze fout optreden wanneer de firewall de verbinding blokkeert.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: fataal: geen PG \_ HBA. conf entry for host "123.45.67.890", User "adminuser", data base "postgresql", SSL

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Het is raadzaam om het uitgaande IP-adres van een toepassing of service te vinden en expliciet toegang tot die afzonderlijke IP-adressen of bereiken toe te staan. U kunt bijvoorbeeld het uitgaande IP-adres van een Azure App Service vinden of een open bare IP gebruiken dat is gekoppeld aan een virtuele machine of een andere bron (zie hieronder voor informatie over het maken van een verbinding met de privé-IP van een virtuele machine via service-eind punten). 

Als er geen vast uitgaand IP-adres beschikbaar is voor uw Azure-service, kunt u overwegen verbindingen van alle IP-adressen van Azure-Data Center in te scha kelen. Deze instelling kan worden ingeschakeld via de Azure Portal door de optie **toegang tot Azure-Services toestaan** in te stellen **op in** het deel venster **verbindings beveiliging** en op **Opslaan** te drukken. Vanuit de Azure CLI wordt een instelling van een firewall regel waarbij het begin-en eind adres gelijk is aan 0.0.0.0 het equivalent. Als de verbindings poging niet is toegestaan, wordt de Azure Database for PostgreSQL server niet bereikt door de aanvraag.

> [!IMPORTANT]
> Met de optie **toegang tot Azure-Services toestaan wordt** de firewall zodanig geconfigureerd dat alle verbindingen van Azure, inclusief verbindingen van de abonnementen van andere klanten, worden toegestaan. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="Toegang tot Azure-Services in de portal toestaan configureren":::

### <a name="connecting-from-a-vnet"></a>Verbinding maken vanaf een VNet
Als u een beveiligde verbinding wilt maken met uw Azure Database for PostgreSQL-server vanuit een VNet, kunt u gebruikmaken van [vnet-service-eind punten](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Firewallregels programmatisch beheren
Naast de Azure Portal kunnen Firewall regels programmatisch worden beheerd met behulp van Azure CLI.
Zie ook [Azure database for PostgreSQL firewall regels maken en beheren met behulp van Azure cli](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Problemen met de firewall oplossen
Houd rekening met de volgende punten wanneer de toegang tot de Microsoft Azure-Data Base voor PostgreSQL Server-service niet werkt zoals verwacht:

* **Wijzigingen in de acceptatie lijst zijn nog niet doorgevoerd:** Het kan een vertraging van vijf minuten duren voordat wijzigingen in de configuratie van de Azure Database for PostgreSQL Server-firewall van kracht worden.

* **De aanmelding is niet geautoriseerd of er is een onjuist wacht woord gebruikt:** Als een aanmelding geen machtigingen heeft op de Azure Database for PostgreSQL server of het gebruikte wacht woord onjuist is, wordt de verbinding met de Azure Database for PostgreSQL-server geweigerd. Het maken van een firewall instelling biedt clients alleen de mogelijkheid om verbinding te maken met uw server. elke client moet nog steeds de benodigde beveiligings referenties opgeven.

   Als u bijvoorbeeld een JDBC-client gebruikt, kan de volgende fout worden weer gegeven.
   > java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: fataal: wachtwoord verificatie is mislukt voor gebruiker ' yourusername '

* **Dynamisch IP-adres:** als u een internetverbinding hebt met dynamische IP-adressering en problemen ondervindt bij het passeren van de firewall, kunt u een van de volgende oplossingen proberen:

   * Vraag uw Internet provider (ISP) naar het IP-adres bereik dat is toegewezen aan uw client computers die toegang hebben tot de Azure Database for PostgreSQL server en voeg het IP-adres bereik vervolgens toe als een firewall regel.

   * Neem in plaats daarvan statische IP-adressen op voor uw client computers en voeg vervolgens het statische IP-adres toe als een firewall regel.

* **Het IP-adres van de server lijkt openbaar te zijn:** Verbindingen met de Azure Database for PostgreSQL-server worden doorgestuurd via een openbaar toegankelijke Azure-gateway. Het daadwerkelijke IP-adres van de server wordt echter beschermd door de firewall. Raadpleeg het artikel [over de connectiviteitsarchitectuur](concepts-connectivity-architecture.md) voor meer informatie.

* **Kan geen verbinding maken met een Azure-resource met een toegestaan IP-adres:** Controleer of het **micro soft. SQL** service-eind punt is ingeschakeld voor het subnet waarmee u verbinding maakt. Als **micro soft. SQL** is ingeschakeld, betekent dit dat u alleen VNet- [service-eindpunt regels](concepts-data-access-and-security-vnet.md) wilt gebruiken op dat subnet.

   Het is bijvoorbeeld mogelijk dat u de volgende fout ziet als u verbinding maakt vanaf een Azure-VM in een subnet waarvoor **micro soft. SQL** is ingeschakeld, maar geen corresponderende VNet-regel heeft:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* De **firewall regel is niet beschikbaar voor de IPv6-indeling:** De firewall-regels moeten de IPv4-indeling hebben. Als u firewall regels in de IPv6-indeling opgeeft, wordt de validatie fout weer gegeven.


## <a name="next-steps"></a>Volgende stappen
* [Azure Database for PostgreSQL firewall regels maken en beheren met behulp van de Azure Portal](howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL firewall regels maken en beheren met Azure CLI](howto-manage-firewall-using-cli.md)
* [VNet-service-eind punten in Azure Database for PostgreSQL](./concepts-data-access-and-security-vnet.md)
