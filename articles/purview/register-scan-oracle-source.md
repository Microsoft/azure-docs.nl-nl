---
title: Oracle-bron- en installatiescans (preview) registreren in Azure Purview
description: In dit artikel wordt beschreven hoe u de Oracle-bron registreert in Azure Purview en hoe u een scan in kunt stellen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 40c5e0ff2c2301607f5a548ff05c742c5c5a948d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517059"
---
# <a name="register-and-scan-oracle-source-preview"></a>Oracle-bron registreren en scannen (preview)

In dit artikel wordt beschreven hoe u een Oracle-gegevensbasis registreert in Purview en een scan instelt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Oracle-bron ondersteunt **volledige scan om metagegevens** te extraheren uit een Oracle-database en haalt **herkomst op** tussen gegevensactiva.

## <a name="prerequisites"></a>Vereisten

1.  Stel de meest recente [zelf-hostende Integration Runtime in.](https://www.microsoft.com/download/details.aspx?id=39717)
    Zie Create [and configure a self-hosted integration runtime (Een zelf-hostende Integration Runtime maken en configureren) voor meer informatie.](../data-factory/create-self-hosted-integration-runtime.md)

2.  Zorg ervoor [dat JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) is geïnstalleerd op de virtuele machine waarop de zelf-hostende Integration Runtime is geïnstalleerd.

3.  Zorg ervoor \" dat Visual C++ Redistributable 2012 Update 4 is geïnstalleerd op de \" zelf-hostende Integration Runtime-machine. Als u deze \' nog niet hebt geïnstalleerd, downloadt u deze [hier](https://www.microsoft.com/download/details.aspx?id=30679).

4.  U moet hier handmatig een Oracle JDBC-stuurprogramma downloaden op uw virtuele machine waarop de zelf-hostende Integration Runtime wordt uitgevoerd. [](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)

    > [!Note] 
    > Het stuurprogramma moet toegankelijk zijn voor alle accounts in de VM. Installeer deze niet in een gebruikersaccount.

5.  Ondersteunde Oracle-databaseversies zijn 6i tot 19c.

6.  Gebruikersmachtiging: Er is alleen-lezentoegang tot systeemtabellen vereist. De gebruiker moet machtigingen hebben om een sessie te maken en de rol SELECT \_ CATALOG ROLE moet zijn \_ toegewezen. De gebruiker kan ook de machtiging SELECT hebben verleend voor elke afzonderlijke systeemtabel waar deze connector metagegevens van opvraagt:
       > sessie voor maken aan gebruiker \[ \] verlenen;\
        grant select on all \_ users to \[ user \] ;\
        grant select on dba \_ objects to \[ user \] ;\
        grant select on dba \_ tab comments to user \_ \[ \] ;\
        grant select on dba \_ external locations to user \_ \[ \] ;\
        grant select on dba \_ directories to \[ user \] ;\
        grant select on dba \_ mviews to \[ user \] ;\
        grant select on dba \_ clu \_ columns to user \[ \] ;\
        grant select on dba \_ tab columns to user \_ \[ \] ;\
        grant select on dba \_ col comments to user \_ \[ \] ;\
        grant select on dba \_ constraints to \[ user \] ;\
        grant select on dba \_ cons columns to user \_ \[ \] ;\
        grant select on dba \_ indexes to \[ user \] ;\
        grant select on dba \_ ind \_ columns to user \[ \] ;\
        grant select on dba \_ procedures to \[ user \] ;\
        grant select on dba \_ synonyms to \[ user \] ;\
        grant select on dba \_ views to \[ user \] ;\
        grant select on dba \_ source to \[ user \] ;\
        grant select on dba \_ triggers to \[ user \] ;\
        grant select on dba \_ arguments to \[ user \] ;\
        grant select on dba \_ sequences to \[ user \] ;\
        grant select on dba \_ dependencies to \[ user \] ;\
        grant select on V \_ \$ INSTANCE to user \[ \] ;\
        select op \_ \$ v-database verlenen aan \[ gebruiker \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

De enige ondersteunde verificatie voor een Oracle-bron is **Basisverificatie.**

## <a name="register-an-oracle-source"></a>Een Oracle-bron registreren

Ga als volgt te werk om een nieuwe Oracle-bron te registreren in uw gegevenscatalogus:

1.  Navigeer naar uw Purview-account.
2.  Selecteer **Bronnen** in het linkernavigatievenster.
3.  Selecteer **Registreren**
4.  Selecteer oracle bij Bronnen **registreren.** Selecteer **Doorgaan**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="bronnen registreren" border="true":::

Ga op **het scherm Bronnen registreren (Oracle)** als volgt te werk:

1.  Voer een **Naam** in dat de gegevensbron wordt vermeld in de catalogus.

2.  Voer de **hostnaam** in om verbinding te maken met een Oracle-bron. Dit kan het volgende zijn:
    - Een hostnaam die door JDBC wordt gebruikt om verbinding te maken met de databaseserver. Bijvoorbeeld: MyDatabaseServer.com of
    - IP-adres. Bijvoorbeeld 192.169.1.2 of
    - De volledig gekwalificeerde JDBC-connection string. Bijvoorbeeld\
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD \_ BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521)(ADDRESS=(PROTOCOL=TCP)(HOST =oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521)))(CONNECT \_ DATA=(SERVICE \_ NAME=orcl)))

3.  Voer het **poortnummer in dat** door JDBC wordt gebruikt om verbinding te maken met de databaseserver (standaard 1521 voor Oracle).

4.  Voer de **Oracle-servicenaam in die** door JDBC wordt gebruikt om verbinding te maken met de databaseserver.

5.  Selecteer een verzameling of maak een nieuwe (optioneel)

6.  Voltooi om de gegevensbron te registreren.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="opties voor bronnen registreren" border="true":::

## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

Doe het volgende om een nieuwe scan te maken en uit te voeren:

1.  Klik in het Beheercentrum op Integratieruntime. Zorg ervoor dat een zelf-hostende Integration Runtime is ingesteld. Als deze niet is ingesteld, gebruikt u de stappen die hier worden [vermeld](./manage-integration-runtimes.md) om een zelf-hostende Integration Runtime te maken.

2.  Navigeer naar **Bronnen.**

3.  Selecteer de geregistreerde Oracle-bron.

4.  Selecteer **+ Nieuwe scan.**

5.  Geef de onderstaande details op:

    a.  **Naam:** de naam van de scan

    b.  **Verbinding maken via integratieruntime:** selecteer de geconfigureerde zelf-hostende Integration Runtime

    c.  **Referentie:** selecteer de referentie om verbinding te maken met uw gegevensbron. Zorg ervoor dat:
    - Selecteer Basisverificatie tijdens het maken van een referentie.        
    - Geef de gebruikersnaam op die door JDBC wordt gebruikt om verbinding te maken met de databaseserver in het invoerveld Gebruikersnaam        
    - Sla het gebruikerswachtwoord op dat door JDBC wordt gebruikt om verbinding te maken met de databaseserver in de geheime sleutel.

    d.  **Schema:** lijstsubset van schema's die moeten worden geïmporteerd, uitgedrukt als een lijst met door puntkomma's gescheiden schema's. Bijvoorbeeld schema1; schema2. Alle gebruikersschema's worden geïmporteerd als deze lijst leeg is. Alle systeemschema's (bijvoorbeeld SysAdmin) en objecten worden standaard genegeerd. Wanneer de lijst leeg is, worden alle beschikbare schema's geïmporteerd.
        Acceptabele schemanaampatronen met de syntaxis van SQL LIKE-expressies omvatten bijvoorbeeld het gebruik van %. A%; %B; %C%; D
       -   begin met A of        
       -   eindig met B of        
       -   bevat C of        
       -   gelijk aan D

    Het gebruik van NOT en speciale tekens is niet toegestaan.

6.  **Locatie van stuurprogramma:** geef het pad op naar de locatie van het JDBC-stuurprogramma op de VM waarop de selfhost Integration Runtime wordt uitgevoerd. Dit moet het pad naar de geldige locatie van de JAR-map zijn.

7.  **Maximaal beschikbaar geheugen:** Maximaal geheugen (in GB) dat beschikbaar is op de VM van de klant dat moet worden gebruikt door het scannen van processen. Dit is afhankelijk van de grootte van de SAP S/4HANA-bron die moet worden gescand.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="scan oracle" border="true":::

8.  Klik op **Doorgaan.**

9.  Kies de **scantrigger**. U kunt een schema instellen of de scan eenmalig uitvoeren.

10.  Controleer de scan en klik op **Opslaan en uitvoeren.**

## <a name="viewing-your-scans-and-scan-runs"></a>Uw scans en scanuitvoeringen bekijken

1. Ga naar het beheercentrum. Selecteer **Gegevensbronnen** onder het gedeelte **Bronnen en scannen**.

2. Selecteer de gewenste gegevensbron. U ziet een lijst bestaande scans van die gegevensbron.

3. Selecteer de scan waarvan u de resultaten wilt bekijken.

4. Op deze pagina ziet u alle vorige scanuitvoeringen, samen met metrische gegevens en statussen voor elke scanuitvoering. Ook wordt weergegeven of uw scan gepland of handmatig is uitgevoerd, op hoeveel assets classificaties waren toegepast, hoeveel assets zijn ontdekt, de begin- en eindtijd van de scan en de totale duur van de scan.

## <a name="manage-your-scans"></a>Uw scans beheren

Doe het volgende om een scan te beheren of te verwijderen:

1. Ga naar het beheercentrum. Selecteer **Gegevensbronnen** in het gedeelte **Bronnen en scannen** en selecteer vervolgens de gewenste gegevensbron.

2. Selecteer de share die u wilt beheren. U kunt de scan bewerken door **Bewerken** te selecteren.

3. U kunt uw scan verwijderen door **Verwijderen** te selecteren.

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)