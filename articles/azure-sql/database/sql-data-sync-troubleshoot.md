---
title: Problemen met SQL Data Sync oplossen
description: Meer informatie over het identificeren, oplossen en oplossen van veelvoorkomende problemen met SQL Data Sync in Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 02eaec4c86c934e8d2638de1b60aa9267babf7a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790165"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Problemen met SQL Data Sync oplossen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel wordt beschreven hoe u bekende problemen met SQL Data Sync in azure oplost. Als er een oplossing voor een probleem is, wordt deze hier weer gegeven.

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync in Azure ](sql-data-sync-data-sql-server-sql-database.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> SQL Data Sync biedt op dit moment **geen** ondersteuning voor Azure SQL Managed Instance.

## <a name="sync-issues"></a>Synchronisatie problemen

- [Synchronisatie mislukt in de portal-gebruikers interface voor on-premises data bases die zijn gekoppeld aan de client agent](#sync-fails)

- [Mijn synchronisatie groep is vastgelopen in de verwerkings status](#sync-stuck)

- [Ik zie onjuiste gegevens in mijn tabellen](#sync-baddata)

- [Ik zie inconsistente primaire-sleutel gegevens na een geslaagde synchronisatie](#sync-pkdata)

- [Ik zie een aanzienlijke vermindering van de prestaties](#sync-perf)

- [Ik zie dit bericht: ' kan de waarde NULL niet invoegen in de kolom \<column> . Null-waarden zijn niet toegestaan voor de kolom. Wat betekent dit en hoe kan ik het probleem oplossen?](#sync-nulls)

- [Hoe worden kring verwijzingen verwerkt met gegevens synchronisatie? Dat wil zeggen, wanneer dezelfde gegevens worden gesynchroniseerd in meerdere synchronisatie groepen en blijven veranderen als resultaat?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> Synchronisatie mislukt in de portal-gebruikers interface voor on-premises data bases die zijn gekoppeld aan de client agent

Synchronisatie mislukt in de gebruikers interface van de SQL Data Sync portal voor on-premises data bases die zijn gekoppeld aan de client agent. Op de lokale computer waarop de-agent wordt uitgevoerd, ziet u System. IO. IOException-fouten in het gebeurtenis logboek. De fouten zeggen dat de schijf onvoldoende ruimte heeft.

- **Oorzaak**. Er is onvoldoende ruimte op het station.

- **Oplossing**. Maak meer ruimte vrij op het station waarop de map% TEMP% zich bevindt.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> Mijn synchronisatie groep is vastgelopen in de verwerkings status

Een synchronisatie groep in SQL Data Sync heeft lange tijd de verwerkings status. Er wordt niet gereageerd op de opdracht **stoppen** en er worden geen nieuwe vermeldingen weer gegeven in de logboeken.

Een van de volgende voor waarden kan ertoe leiden dat een synchronisatie groep vastloopt in de verwerkings status:

- **Oorzaak**. De clientagent is offline

- **Oplossing**. Zorg ervoor dat de clientagent online is en probeer het vervolgens opnieuw.

- **Oorzaak**. De clientagent is verwijderd of ontbreekt.

- **Oplossing**. Als de clientagent is verwijderd of om een andere reden ontbreekt:

    1. Verwijder het XML-bestand voor de agent uit de SQL Data Sync-installatiemap, als dit bestand bestaat.
    1. Installeer de agent op een on-premises computer. (Dit kan dezelfde computer zijn of een andere.) Verzend vervolgens de agentsleutel die in de portal is gegenereerd voor de agent die offline is.

- **Oorzaak**. De SQL Data Sync-service is gestopt.

- **Oplossing**. Start de SQL Data Sync-service opnieuw.

    1. Zoek in het menu **Start** naar **Services**.
    1. Selecteer in de zoek resultaten **Services**.
    1. Zoek de **SQL Data Sync** -service.
    1. Als de status van de service is **gestopt**, klikt u met de rechter muisknop op de naam van de service en selecteert u vervolgens **starten**.

> [!NOTE]
> Als de voor gaande informatie uw synchronisatie groep niet uit de verwerkings status verplaatst, kan Microsoft Ondersteuning de status van de synchronisatie groep opnieuw instellen. Als u de status van de synchronisatie groep opnieuw wilt instellen, maakt u een bericht op de [pagina micro soft Q&een vraag voor Azure SQL database](/answers/topics/azure-sql-database.html). Neem in het bericht de abonnements-ID en de synchronisatie groep-ID op voor de groep die opnieuw moet worden ingesteld. Een Microsoft Ondersteuning-Engineer reageert op uw bericht en laat u weten wanneer de status opnieuw is ingesteld.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> Ik zie onjuiste gegevens in mijn tabellen

Als er tabellen met dezelfde naam maar uit verschillende database schema's zijn opgenomen in een synchronisatie, worden er na de synchronisatie onjuiste gegevens in de tabellen weer gegeven.

- **Oorzaak**. Het SQL Data Sync-inrichtings proces maakt gebruik van dezelfde tracerings tabellen voor tabellen die dezelfde naam hebben, maar die zich in verschillende schema's bevinden. Als gevolg hiervan worden wijzigingen van beide tabellen in dezelfde tracerings tabel weer gegeven. Hierdoor worden foutieve gegevens wijzigingen tijdens de synchronisatie veroorzaakt.

- **Oplossing**. Zorg ervoor dat de namen van tabellen die betrokken zijn bij een synchronisatie verschillend zijn, zelfs als de tabellen deel uitmaken van verschillende schema's in een Data Base.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> Ik zie inconsistente primaire-sleutel gegevens na een geslaagde synchronisatie

Een synchronisatie wordt gerapporteerd als geslaagd. in het logboek worden geen mislukte of overgeslagen rijen weer gegeven, maar u ziet dat de gegevens van de primaire sleutel inconsistent zijn tussen de data bases in de synchronisatie groep.

- **Oorzaak**. Dit resultaat is standaard. Wijzigingen in een primaire-sleutel kolom leiden tot inconsistente gegevens in de rijen waar de primaire sleutel is gewijzigd.

- **Oplossing**. Als u dit probleem wilt voor komen, moet u ervoor zorgen dat er geen gegevens in een primaire-sleutel kolom worden gewijzigd. Als u dit probleem wilt oplossen nadat dit is opgetreden, verwijdert u de rij met inconsistente gegevens uit alle eind punten in de synchronisatie groep. Voeg vervolgens de rij opnieuw in.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> Ik zie een aanzienlijke vermindering van de prestaties

Uw prestaties worden aanzienlijk verminderd, mogelijk op het punt waar u de gebruikers interface voor gegevens synchronisatie niet zelfs kunt openen.

- **Oorzaak**. De meest waarschijnlijke oorzaak is een Sync-lus. Een Sync-lus treedt op wanneer een synchronisatie-op basis van groep A een synchronisatie activeert door de groep B, waarmee vervolgens een synchronisatie wordt geactiveerd door de groep A. De werkelijke situatie is mogelijk complexer en er zijn mogelijk meer dan twee synchronisatie groepen in de lus betrokken. Het probleem is dat er een kring verwijzing wordt gegenereerd door synchronisatie groepen die elkaar overlappen.

- **Oplossing**. De beste oplossing is voor komen. Zorg ervoor dat uw synchronisatie groepen geen kring verwijzingen bevatten. Een rij die is gesynchroniseerd door één synchronisatie groep kan niet worden gesynchroniseerd met een andere synchronisatie groep.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> Ik zie dit bericht: ' kan de waarde NULL niet invoegen in de kolom \<column> . Null-waarden zijn niet toegestaan voor de kolom. Wat betekent dit en hoe kan ik het probleem oplossen? 
Dit fout bericht geeft aan dat een van de volgende twee problemen heeft plaatsgevonden:
-  Een tabel heeft geen primaire sleutel. U kunt dit probleem oplossen door een primaire sleutel toe te voegen aan alle tabellen die u synchroniseert.
-  Er is een WHERE-component in uw CREATE INDEX-instructie. Met gegevens synchronisatie wordt deze voor waarde niet verwerkt. U kunt dit probleem oplossen door de component WHERE te verwijderen of de wijzigingen hand matig door te voeren voor alle data bases. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> Hoe worden kring verwijzingen verwerkt met gegevens synchronisatie? Dat wil zeggen, wanneer dezelfde gegevens worden gesynchroniseerd in meerdere synchronisatie groepen en blijven veranderen als resultaat?
Met gegevens synchronisatie worden geen kring verwijzingen afgehandeld. Zorg ervoor dat u deze voor komt. 

## <a name="client-agent-issues"></a>Problemen met client agent

Zie problemen met de [Data Sync-agent oplossen voor informatie](sql-data-sync-agent-overview.md#agent-tshoot)over het oplossen van problemen met de client agent.

## <a name="setup-and-maintenance-issues"></a>Problemen met de installatie en het onderhoud

- [Er wordt een bericht weer gegeven dat er onvoldoende schijf ruimte is](#setup-space)

- [Ik kan mijn synchronisatie groep niet verwijderen](#setup-delete)

- [Ik kan de registratie van een SQL Server Data Base niet opheffen](#setup-unreg)

- [Ik heb niet voldoende rechten om systeem services te starten](#setup-perms)

- [De status van een Data Base is verouderd](#setup-date)

- [Een synchronisatie groep heeft een verouderde status](#setup-date2)

- [Een synchronisatie groep kan niet binnen drie minuten na het verwijderen of stoppen van de agent worden verwijderd](#setup-delete2)

- [Wat gebeurt er wanneer ik een verloren of beschadigde data base herstel?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> Er wordt een bericht weer gegeven dat er onvoldoende schijf ruimte is

- **Oorzaak**. Het bericht ' schijf is onvoldoende ruimte ' kan worden weer gegeven als overgebleven bestanden moeten worden verwijderd. Dit kan worden veroorzaakt door antivirus software, of bestanden zijn geopend wanneer er delete-bewerkingen worden uitgevoerd.

- **Oplossing**. Verwijder de synchronisatie bestanden in de map% Temp% () hand matig `del \*sync\* /s` . Verwijder vervolgens de submappen in de map% Temp%.

> [!IMPORTANT]
> Geen bestanden verwijderen terwijl de synchronisatie wordt uitgevoerd.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> Ik kan mijn synchronisatie groep niet verwijderen

De poging om een synchronisatie groep te verwijderen, is mislukt. Een van de volgende scenario's kan ertoe leiden dat een synchronisatie groep niet kan worden verwijderd:

- **Oorzaak**. De clientagent is offline.

- **Oplossing**. Zorg ervoor dat de client agent online is en probeer het opnieuw.

- **Oorzaak**. De clientagent is verwijderd of ontbreekt.

- **Oplossing**. Als de clientagent is verwijderd of om een andere reden ontbreekt:  
    a. Verwijder het XML-bestand voor de agent uit de SQL Data Sync-installatiemap, als dit bestand bestaat.  
    b. Installeer de agent op een on-premises computer. (Dit kan dezelfde computer zijn of een andere.) Verzend vervolgens de agentsleutel die in de portal is gegenereerd voor de agent die offline is.

- **Oorzaak**. Een Data Base is offline.

- **Oplossing**. Zorg ervoor dat uw data bases allemaal online zijn.

- **Oorzaak**. De synchronisatie groep wordt ingericht of gesynchroniseerd.

- **Oplossing**. Wacht tot het inrichtings-of synchronisatie proces is voltooid en probeer de synchronisatie groep vervolgens opnieuw te verwijderen.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a> Ik kan de registratie van een SQL Server Data Base niet opheffen

- **Oorzaak**. Waarschijnlijk probeert u de registratie van een Data Base die al is verwijderd, te verwijderen.

- **Oplossing**. Als u de registratie van een SQL Server Data Base ongedaan wilt maken, selecteert u de data base en selecteert u vervolgens **verwijderen forceren**.

  Als deze bewerking de data base niet kan verwijderen uit de synchronisatie groep:

  1. Stop de client agent host-service en start deze opnieuw:  
    a. Selecteer het menu **Start**.  
    b. Typ **Services. msc** in het zoekvak.  
    c. Dubbel klik in de sectie **Program ma's** van het deel venster Zoek resultaten op **Services**.  
    d. Klik met de rechter muisknop op de **SQL Data Sync** -service.  
    e. Als de service wordt uitgevoerd, stopt u deze.  
    f. Klik met de rechter muisknop op de service en selecteer **starten**.  
    g. Controleer of de data base nog steeds is geregistreerd. Als deze niet meer is geregistreerd, bent u klaar. Als dat niet het geval is, gaat u verder met de volgende stap.
  1. Open de client agent-app (SqlAzureDataSyncAgent).
  1. Selecteer **referenties bewerken** en voer vervolgens de referenties voor de data base in.
  1. Ga door met het opheffen van de registratie.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> Ik heb niet voldoende rechten om systeem services te starten

- **Oorzaak**. Deze fout treedt op in twee situaties:
  -   De gebruikers naam en/of het wacht woord zijn onjuist.
  -   Het opgegeven gebruikers account heeft onvoldoende rechten om u aan te melden als een service.

- **Oplossing**. Meld u aan bij het gebruikers account voor aanmelding bij een service aan de gebruiker:

  1. Ga naar **Start**  >  **configuratie scherm**  >  **systeem beheer** van lokale  >    >  **beleids**  >  **gebruikers Rights Management** van het lokale beveiligings beleid.
  1. Selecteer **Aanmelden als een service**.
  1. Voeg in het dialoog venster **Eigenschappen** het gebruikers account toe.
  1. Selecteer **Apply** en vervolgens **OK**.
  1. Sluit alle vensters.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> De status van een Data Base is verouderd

- **Oorzaak**. SQL Data Sync verwijdert data bases die gedurende 45 dagen of langer offline zijn van de service (geteld vanaf het moment dat de data base offline werd gezet). Als een Data Base gedurende 45 dagen offline is en weer online komt, is de status **verouderd**.

- **Oplossing**. U kunt een **verouderde** status voor komen door ervoor te zorgen dat er geen data bases meer dan 45 dagen offline gaan.

  Als de status van een Data Base **verouderd** is:

  1. Verwijder de data base met een **verouderde** status uit de synchronisatie groep.
  1. Voeg de Data Base weer toe aan de synchronisatie groep.

  > [!WARNING]
  > U verliest alle wijzigingen die in deze data base zijn aangebracht terwijl deze offline was.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> Een synchronisatie groep heeft een verouderde status

- **Oorzaak**. Als een of meer wijzigingen niet van toepassing zijn voor de gehele Bewaar periode van 45 dagen, kan een synchronisatie groep verouderd raken.

- **Oplossing**. Als u een **verouderde** status voor een synchronisatie groep wilt voor komen, controleert u de resultaten van uw synchronisatie taken in de geschiedenis Viewer regel matig. Onderzoek en los eventuele wijzigingen op die niet van toepassing zijn.

  Als de status van een synchronisatie groep **verouderd** is, verwijdert u de synchronisatie groep en maakt u deze opnieuw.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> Een synchronisatie groep kan niet binnen drie minuten na het verwijderen of stoppen van de agent worden verwijderd

Het is niet mogelijk om een synchronisatie groep te verwijderen binnen drie minuten van het ongedaan maken of stoppen van de gekoppelde SQL Data Sync-client agent.

- **Oplossing**.

  1. Een synchronisatie groep verwijderen terwijl de gekoppelde synchronisatie agenten online zijn (aanbevolen).
  1. Als de agent offline is, maar is geïnstalleerd, brengt u deze online naar de on-premises computer. Wacht tot de status van de agent **online** wordt weer gegeven in de SQL Data Sync Portal. Verwijder vervolgens de synchronisatie groep.
  1. Als de agent offline is omdat deze is verwijderd:  
    a.  Verwijder het XML-bestand voor de agent uit de SQL Data Sync-installatiemap, als dit bestand bestaat.  
    b.  Installeer de agent op een on-premises computer. (Dit kan dezelfde computer zijn of een andere.) Verzend vervolgens de agentsleutel die in de portal is gegenereerd voor de agent die offline is.  
    c. Probeer de synchronisatie groep te verwijderen.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> Wat gebeurt er wanneer ik een verloren of beschadigde data base herstel?

Als u een verloren of beschadigde data base herstelt vanuit een back-up, is er mogelijk een niet-convergentie van gegevens in de synchronisatie groepen waartoe de data base behoort.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync in Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Data Sync instellen
    - In de portal- [zelf studie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL database en SQL Server](sql-data-sync-sql-server-configure.md)
    - Met PowerShell
        -  [Power shell gebruiken om te synchroniseren tussen meerdere data bases in Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Power shell gebruiken om te synchroniseren tussen een data base in Azure SQL Database en een data base in een SQL Server-exemplaar](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent: [Data Sync Agent voor SQL Data Sync in Azure](sql-data-sync-agent-overview.md)
-   Best practices: [Best practices voor SQL Data Sync in Azure](sql-data-sync-best-practices.md)
-   Bewaken: [SQL Data Sync bewaken met Azure Monitor-logboeken](./monitor-tune-overview.md)
-   Het synchronisatieschema bijwerken
    -   Met Transact-SQL- [de replicatie van schema wijzigingen in SQL Data Sync in azure automatiseren](sql-data-sync-update-sync-schema.md)
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/update-sync-schema-in-sync-group.md)

Meer informatie over SQL Database vindt u in:

-   [Wat is de Azure SQL Database-service?](sql-database-paas-overview.md)
-   [Database Lifecycle Management (DLM)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))