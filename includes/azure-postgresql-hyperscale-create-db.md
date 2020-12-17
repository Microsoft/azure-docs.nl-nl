---
title: bestand opnemen
description: bestand opnemen
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 12/16/2020
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 54cc31cdb0ddc7694568d60b1446f1cf99663e95
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616623"
---
## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Een Azure Database for PostgreSQL maken - Hyperscale (Citus)

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).


Volg deze stappen voor het maken van een Azure Database voor PostgreSQL-server:
1. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.
2. Selecteer **Databases** op de pagina **Nieuw** en selecteer **Azure Database voor PostgreSQL** op de pagina **Databases**.
3. Klik voor de implementatieoptie op de knop **Maken** onder **Servergroep voor Hyperscale (Citus)** .
4. Vul het formulier voor de gegevens van de nieuwe server als volgt in:
   - Resourcegroep: klik op de koppeling **Nieuwe maken** onder het tekstvak voor dit veld. Voer een naam in, bijvoorbeeld **mijnresourcegroep**.
   - Naam van servergroep: voer een unieke naam in voor de nieuwe servergroep, die ook wordt gebruikt voor een subdomein van de server.
   - Gebruikersnaam van beheerder: momenteel is de waarde **citus** vereist en kan dit niet worden gewijzigd.
   - Wachtwoord: Uw wachtwoord moet minimaal 8 tekens lang zijn en tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
   - Locatie: gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.

   > [!IMPORTANT]
   > Het wachtwoord van de serverbeheerder die u hier opgeeft, is vereist voor aanmelding bij de server en de bijbehorende databases. Onthoud of noteer deze informatie voor later gebruik.

5. Klik op **Servergroep configureren**. Laat de instellingen in die sectie ongewijzigd en klik op **Opslaan**.
6. Klik op **Volgende: Netwerken >** onderaan het scherm.

7. Klik op het keuzerondje **Openbaar eindpunt** op het tabblad **Netwerken**.
   ![Openbaar eindpunt geselecteerd](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klik op de koppeling **+ Huidig IP-adres van client toevoegen**.
   ![IP-adres van client toegevoegd](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > De Azure PostgreSQL-server communiceert via poort 5432. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u alleen verbinding maken met uw Hyperscale (Citus)-cluster als uw IT-afdeling poort 5432 openstelt.
   >

9. Klik op **Beoordelen en maken** en vervolgens op **Maken** om de server in te richten. De inrichting duurt een paar minuten.
10. De pagina wordt omgeleid om de implementatie te bewaken. Wanneer de Live-status wordt gewijzigd van **Uw implementatie wordt uitgevoerd** in **Uw implementatie is voltooid**, klikt u op de menuopdracht **Uitvoer** aan de linkerkant van de pagina.
11. De pagina Uitvoer bevat een coördinatorhostnaam met een knop ernaast om de waarde naar het klembord te kopiëren. Noteer deze informatie voor later gebruik.

### <a name="connect-to-the-database-using-psql"></a>Verbinding maken met de database met behulp van psql

Wanneer u de Azure Database for PostgreSQL-server maakt, wordt een standaarddatabase met de naam **citus** gemaakt. Als u verbinding wilt maken met uw databaseserver, hebt u een verbindingsreeks en het beheerderswachtwoord nodig.

1. Haal de verbindingsreeks op. Klik op de pagina Servergroep op de menuopdracht **Verbindingsreeksen**. (Deze bevindt zich onder **Instellingen**.) Zoek de tekenreeks met de markering **psql**. Deze heeft de volgende vorm:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Kopieer de tekenreeks. Vervang "{your\_password}" door het beheerderswachtwoord dat u eerder hebt gekozen. Uw leesbare wachtwoord wordt niet opgeslagen in het systeem en kan dus niet worden weergegeven in de verbindingsreeks.

2. Open een terminalvenster op de lokale computer.

3. In de prompt maakt u verbinding met uw Azure Database for PostgreSQL-server via het [psql](https://www.postgresql.org/docs/current/app-psql.html)-hulpprogramma. Geef uw verbindingsreeks tussen haakjes door, en zorg ervoor dat het uw wachtwoord bevat:
   ```bash
   psql "host=..."
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met het coördinatorknooppunt van de servergroep **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
