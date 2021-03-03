---
title: Een Teradata-bron en installatie scans (preview) registreren in azure controle sfeer liggen
description: In dit artikel wordt beschreven hoe u een Teradata-bron in Azure Purview kunt registreren en een scan kunt instellen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 0885870497ef3488d78fe899c96ee2a82a5b84fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696159"
---
# <a name="register-and-scan-teradata-source-preview"></a>Een Teradata-bron registreren en scannen (preview)

In dit artikel wordt beschreven hoe u een Teradata-bron in Purview kunt registreren en een scan kunt instellen.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Teradata-bron ondersteunt **Volledig scannen** om metagegevens uit een Teradata-database op te halen en **Gegevensherkomst** tussen gegevens-assets op te halen.

## <a name="prerequisites"></a>Vereisten

1.  Stel de nieuwste [zelf-hostende Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717)in.
    Zie [een zelf-hostende Integration runtime maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)voor meer informatie.

2.  Zorg ervoor dat de [jdk 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) is geïnstalleerd op uw virtuele machine waarop zelf-hostende Integration runtime is geïnstalleerd.

3.  Controleer of \" Visual C++ redistributable 2012 update 4 \" is geïnstalleerd op de zelf-hostende Integration runtime-computer. Als u de app \' nog niet hebt geïnstalleerd, kunt u deze [hier](https://www.microsoft.com/download/details.aspx?id=30679)downloaden.

4.  U moet het JDBC-stuur programma van Teradata hand matig downloaden op uw virtuele machine waarop zelf-hostende Integration runtime wordt uitgevoerd.
    Het uitvoer bare JAR-bestand kan worden gedownload van de Teradata- [website](https://downloads.teradata.com/).

    > [!Note]
    > Het stuurprogramma moet toegankelijk zijn voor alle accounts in de VM. Voer geen installatie uit in een gebruikersaccount.

5.  Ondersteunde versies van de Teradata-database zijn 12.x tot en met 16.x. Zorg ervoor dat u Leestoegang hebt tot de Teradata-bron die wordt gescand.

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

De enige ondersteunde verificatie voor een Teradata-bron is **basis verificatie**.

## <a name="register-a-teradata-source"></a>Een Teradata-bron registreren

Ga als volgt te werk om een nieuwe Teradata-bron in uw gegevenscatalogus te registreren:

1.  Navigeer naar uw controle sfeer liggen-account.
2.  Selecteer **bronnen** in het linkernavigatievenster.
3.  Selecteer **Registreren**
4.  Selecteer in bron registreren de optie **Teradata**. Selecteer **Doorgaan**

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Teradata-opties registreren" border="true":::

Ga als volgt te werk op het scherm **Bronnen registreren (Teradata)** :

1.  Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.

2.  Voer de naam van de **Host** in om verbinding te maken met een Teradata-bron. Het kan ook een IP-adres of een volledig gekwalificeerde verbindingsreeks op de server zijn.

3.  Selecteer een verzameling of maak een nieuwe (optioneel)

4.  Voltooi om de gegevensbron te registreren.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Teradata registreren" border="true":::

## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

Doe het volgende om een nieuwe scan te maken en uit te voeren:

1.  Klik in het Beheercentrum op **Integratieruntime**. Zorg ervoor dat een zelf-hostende Integration Runtime is ingesteld. Als deze niet is ingesteld, volgt u de stappen die [hier](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) worden beschreven voor het instellen van een zelf-hostende Integration runtime

2.  Navigeer naar de **Bronnen**

3.  Selecteer de geregistreerde Teradata-bron.

4.  Selecteer **+ Nieuwe scan**

5.  Geef de onderstaande details op:

    a.  **Naam**: de naam van de scan

    b.  **Verbinding maken via Integration runtime**: Selecteer de geconfigureerde zelf-hostende Integration runtime.

    c.  **Referentie**: Selecteer de referentie om verbinding te maken met uw gegevens bron. Zorg ervoor dat:

    -   Selecteer basis verificatie tijdens het maken van een referentie.
    -   Geef een gebruikers naam op om verbinding te maken met de database server in het invoer veld voor de gebruikers naam
    -   Sla het database server wachtwoord op in de geheime sleutel.

        Raadpleeg de koppeling [hier](https://docs.microsoft.com/azure/purview/manage-credentials) voor meer informatie over referenties

6.  **Schema**: een subset van schema's weer geven die moeten worden geïmporteerd, uitgedrukt in een door punt komma's gescheiden lijst. bijvoorbeeld, Schema1; schema2. Alle gebruikers schema's worden geïmporteerd als de lijst leeg is. Alle systeemschema's (bijvoorbeeld SysAdmin) en objecten worden standaard genegeerd. Wanneer de lijst leeg is, worden alle beschik bare schema's geïmporteerd.

        Acceptable schema name patterns using SQL LIKE expressions syntax include using %, e.g. A%; %B; %C%; D
        - start with A or    
        - end with B or    
        - contain C or    
        - equal D

        Usage of NOT and special characters are not acceptable

7.  **Locatie van het stuur programma**: Geef het pad op naar de locatie van het JDBC-stuur programma in uw VM waar de Self-Host Integration runtime wordt uitgevoerd. Dit moet het pad naar een geldige JAR-maplocatie zijn.

8.  **Maxi maal beschikbaar geheugen:** Maxi maal geheugen (in GB) dat beschikbaar is op de virtuele machine van de klant om te worden gebruikt door Scan processen. Dit is afhankelijk van de grootte van de Teradata-bron die moet worden gescand.

    > [!Note] 
    > Als vuistregel moet u 2 GB geheugen voor elke 1000 tabellen opgeven

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="scan instellen" border="true":::

6.  Klik op **door gaan**.

7.  Kies de **scan trigger**. U kunt een schema instellen of de scan eenmalig uitvoeren.

8.  Controleer de scan en klik op **opslaan en uitvoeren**.

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