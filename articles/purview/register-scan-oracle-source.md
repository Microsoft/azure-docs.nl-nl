---
title: Registreren van Oracle-bron en installatie scans (preview) in azure controle sfeer liggen
description: In dit artikel vindt u een overzicht van het registreren van Oracle-bron in azure controle sfeer liggen en het instellen van een scan.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 37f6a779e7dd83a6aa61de9850ad3b49b57393f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010434"
---
# <a name="register-and-scan-oracle-source-preview"></a>Oracle-bron registreren en scannen (preview)

In dit artikel wordt beschreven hoe u een Oracle-Data Base kunt registreren in controle sfeer liggen en een scan kunt instellen.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Oracle-bron ondersteunt **volledige scan** om meta gegevens uit een Oracle-Data Base uit te pakken en **afkomst** tussen gegevensassets op te halen.

## <a name="prerequisites"></a>Vereisten

1.  Stel de nieuwste [zelf-hostende Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717)in.
    Zie [een zelf-hostende Integration runtime maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)voor meer informatie.

2.  Zorg ervoor dat [jdk 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) is geïnstalleerd op uw virtuele machine waarop zelf-hostende Integration runtime is geïnstalleerd.

3.  Controleer of \" Visual C++ redistributable 2012 update 4 \" is geïnstalleerd op de zelf-hostende Integration runtime-computer. Als u de app \' nog niet hebt geïnstalleerd, kunt u deze [hier](https://www.microsoft.com/download/details.aspx?id=30679)downloaden.

4.  U moet een Oracle JDBC-stuur programma hand matig downloaden [op uw virtuele machine waarop zelf](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) -hostende Integration runtime wordt uitgevoerd.

    > [!Note] 
    > Het stuurprogramma moet toegankelijk zijn voor alle accounts in de VM. Installeer het niet in een gebruikers account.

5.  Ondersteunde versies van Oracle Data Base zijn 6i naar 19c.

6.  Gebruikers machtiging: om ervoor te zorgen dat een geslaagde scan voor de eerste keer wordt uitgevoerd, is de machtiging Volledig beheer type Administrator vereist.

    Voor volgende scans is een alleen-lezen toegang tot systeem tabellen vereist. De gebruiker moet gemachtigd zijn om een sessie te maken, maar ook de rol \_ catalogus \_ functie is toegewezen. Het is ook mogelijk dat de gebruiker machtiging heeft voor elke afzonderlijke systeem tabel die deze connector doorzoekt naar de meta gegevens:
       > Maak een sessie aan de \[ gebruiker toekennen \] ; \
        Select op alle \_ gebruikers toestaan voor \[ gebruiker \] ; \
        Select op DBA- \_ objecten toewijzen aan \[ gebruiker \] ; \
        de selectie op \_ het tabblad DBA toestaan \_ opmerkingen bij \[ gebruiker \] ; \
        Selecteer op DBA \_ externe \_ locaties voor \[ gebruiker \] . \
        Selecteer de optie voor het toewijzen van DBA- \_ mappen aan de \[ gebruiker \] . \
        Select op DBA \_ mviews toekennen aan \[ gebruiker \] ; \
        Select op DBA \_ clu- \_ kolommen toestaan voor \[ gebruiker \] ; \
        kolommen selecteren op \_ het tabblad DBA toewijzen \_ aan \[ gebruiker \] ; \
        Select op DBA- \_ kol- \_ opmerkingen aan \[ gebruiker toestaan \] ; \
        Select op DBA- \_ beperkingen toekennen aan \[ gebruiker \] ; \
        Select op DBA- \_ nadelen \_ kolommen voor \[ gebruiker toestaan \] ; \
        Select op DBA- \_ indexen toestaan voor \[ gebruiker \] ; \
        Selecteer de optie voor het selecteren van een DBA- \_ \_ kolom aan de \[ gebruiker \] . \
        Select op DBA- \_ procedures voor \[ gebruiker toestaan \] ; \
        Select op DBA- \_ Synoniemen voor \[ gebruiker toestaan \] ; \
        Select op DBA- \_ weer gaven toewijzen aan \[ gebruiker \] ; \
        Select op DBA- \_ bron aan \[ gebruiker toestaan \] ; \
        Select op DBA- \_ Triggers aan \[ gebruiker toewijzen \] ; \
        Select op DBA- \_ argumenten toekennen aan \[ gebruiker \] ; \
        Select op DBA- \_ reeksen toekennen aan \[ gebruiker \] ; \
        Select op DBA- \_ afhankelijkheden voor \[ gebruiker toestaan \] ; \
        Grant Select op V \_ \$ instance to \[ gebruiker \] ; \
        Select op v \_ \$ Data Base aan \[ gebruiker toestaan \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

De enige ondersteunde verificatie voor een Oracle-bron is **basis verificatie**.

## <a name="register-an-oracle-source"></a>Een Oracle-bron registreren

Ga als volgt te werk om een nieuwe Oracle-bron in uw Data Catalog te registreren:

1.  Navigeer naar uw controle sfeer liggen-account.
2.  Selecteer **bronnen** in het linkernavigatievenster.
3.  Selecteer **Registreren**
4.  Selecteer **Oracle** bij bronnen registreren. Selecteer **Doorgaan**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="bronnen registreren" border="true":::

Ga als volgt te werk op het scherm **bronnen registreren (Oracle)** :

1.  Voer een **naam** in die voor de gegevens bron wordt weer gegeven in de catalogus.

2.  Voer de **hostnaam** in om verbinding te maken met een Oracle-bron. Dit kan een van de volgende zijn:
    - Een hostnaam die wordt gebruikt door JDBC om verbinding te maken met de database server. Bijvoorbeeld MyDatabaseServer.com of
    - IP-adres. Bijvoorbeeld 192.169.1.2 of
    - De volledig gekwalificeerde JDBC-connection string. Bijvoorbeeld: \
        JDBC: Oracle: dun: @ (beschrijving = (taak \_ verdeling = aan) (adres = (protocol = TCP) (host = oracleserver1) (poort = 1521)) (adres = (protocol = TCP) (host = oracleserver2) (poort = 1521)) (adres = (protocol = TCP) (host = oracleserver3) (poort = 1521)) (Connect \_ Data = (service \_ naam = ORCL)))

3.  Voer het **poort nummer** in dat door JDBC wordt gebruikt om verbinding te maken met de database server (standaard 1521 voor Oracle).

4.  Voer de **naam** van de Oracle-Service in die door JDBC wordt gebruikt om verbinding te maken met de database server.

5.  Selecteer een verzameling of maak een nieuwe (optioneel)

6.  Voltooi om de gegevensbron te registreren.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="opties voor bronnen registreren" border="true":::

## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

Doe het volgende om een nieuwe scan te maken en uit te voeren:

1.  Klik in het Beheercentrum op Integratieruntime. Zorg ervoor dat een zelf-hostende Integration Runtime is ingesteld. Als deze niet is ingesteld, volgt u de stappen die [hier](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) worden beschreven om een zelf-hostende Integration runtime te maken.

2.  Navigeer naar **bronnen**.

3.  Selecteer de geregistreerde Oracle-bron.

4.  Selecteer **+ nieuwe scan**.

5.  Geef de onderstaande details op:

    a.  **Naam**: de naam van de scan

    b.  **Verbinding maken via Integration runtime**: Selecteer de geconfigureerde zelf-hostende Integration runtime

    c.  **Referentie**: Selecteer de referentie om verbinding te maken met uw gegevens bron. Zorg ervoor dat:
    - Selecteer basis verificatie tijdens het maken van een referentie.        
    - Geef de gebruikers naam op die door JDBC wordt gebruikt om verbinding te maken met de database server in het invoer veld voor de gebruikers naam        
    - Sla het gebruikers wachtwoord op dat door JDBC wordt gebruikt om verbinding te maken met de database server in de geheime sleutel.

    d.  **Schema**: een subset van schema's weer geven die moeten worden geïmporteerd, uitgedrukt in een door punt komma's gescheiden lijst. Bijvoorbeeld, Schema1; schema2. Alle gebruikers schema's worden geïmporteerd als de lijst leeg is. Alle systeemschema's (bijvoorbeeld SysAdmin) en objecten worden standaard genegeerd. Wanneer de lijst leeg is, worden alle beschik bare schema's geïmporteerd.
        Acceptabele schema naam patronen die gebruikmaken van de syntaxis van SQL LIKE-expressies, zijn bijvoorbeeld het gebruik van%. Een%; B % C%; !
       -   begin met A of        
       -   eindig met B of        
       -   bevat C of        
       -   gelijk aan D

    Het gebruik van niet-en speciale tekens is niet toegestaan.

6.  **Locatie van het stuur programma**: Geef het pad op naar de locatie van het JDBC-stuur programma in uw VM waar de Self-Host Integration runtime wordt uitgevoerd. Dit moet het pad naar een geldige JAR-maplocatie zijn.

7.  **Maxi maal beschikbaar geheugen**: Maxi maal geheugen (in GB) dat beschikbaar is op de virtuele machine van de klant om te worden gebruikt door Scan processen. Dit is afhankelijk van de grootte van SAP S/4HANA-bron die moet worden gescand.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Oracle scannen" border="true":::

8.  Klik op **door gaan**.

9.  Kies de **scan trigger**. U kunt een schema instellen of de scan eenmalig uitvoeren.

10.  Controleer de scan en klik op **opslaan en uitvoeren**.

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