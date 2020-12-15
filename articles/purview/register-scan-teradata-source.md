---
title: Een Teradata-bron en installatiescans registreren (preview)
description: In dit artikel wordt beschreven hoe u een Teradata-bron in Azure Purview kunt registreren en een scan kunt instellen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841652"
---
# <a name="register-and-scan-teradata-source-preview"></a>Een Teradata-bron registreren en scannen (preview)

In dit artikel wordt beschreven hoe u een Teradata-bron in Purview kunt registreren en een scan kunt instellen.

> [!IMPORTANT]
> Deze gegevensbron is momenteel beschikbaar als preview. U kunt het uitproberen en ons feedback geven.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Teradata-bron ondersteunt **Volledig scannen** om metagegevens uit een Teradata-database op te halen en **Gegevensherkomst** tussen gegevens-assets op te halen.

## <a name="prerequisites"></a>Vereisten

- De connector biedt ondersteuning voor gegevensopslag die zich alleen in een on-premises netwerk, een virtueel Azure-netwerk of Amazon Virtual Private Cloud bevindt. Daarom moet u een [zelf-hostende Integration Runtime](manage-integration-runtimes.md) instellen om er verbinding mee te maken.

- Zorg ervoor dat de Java Runtime Environment (JRE) is geïnstalleerd op de virtuele machine waarop de zelf-hostende Integration Runtime is geïnstalleerd.
 
- Controleer of 'Visual C++ Redistributable 2012 Update 4' is geïnstalleerd op de zelf-hostende Integration Runtime-computer. Als u deze nog niet hebt geïnstalleerd, downloadt u deze [hier](https://www.microsoft.com/download/details.aspx?id=30679).

- U moet een stuurprogramma met de naam Teradata JDBC Driver handmatig installeren op uw on-premises virtuele machine. Het uitvoerbare JAR-bestand kan worden gedownload van de [Teradata-website](https://downloads.teradata.com/).

    > [!Note] 
    > Het stuurprogramma moet toegankelijk zijn voor alle accounts in de VM. Voer geen installatie uit in een gebruikersaccount.

- Ondersteunde versies van de Teradata-database zijn **12.x tot en met 16.x**. Zorg ervoor dat u Leestoegang hebt tot de Teradata-bron die wordt gescand.

### <a name="feature-flag"></a>Functievlag

Registratie en scannen van een Teradata-bron is beschikbaar achter een functievlag. Voeg het volgende toe aan uw URL: *?feature.ext.datasource=%7b"teradata":"true"%7d* 

Bijvoorbeeld, volledige URL [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen
De enige ondersteunde manier om verificatie in te stellen voor een Teradata-bron is **Basic-database**-verificatie

## <a name="register-a-teradata-source"></a>Een Teradata-bron registreren

Ga als volgt te werk om een nieuwe Teradata-bron in uw gegevenscatalogus te registreren:

1. Ga naar uw Purview-account
2. Selecteer **Bronnen** in het linkernavigatievenster
3. Selecteer **Registreren**
4. Selecteer **Teradata **op** Bronnen registreren**
5. Selecteer **Doorgaan**

Ga als volgt te werk op het scherm **Bronnen registreren (Teradata)** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
2. Voer de naam van de **Host** in om verbinding te maken met een Teradata-bron. Het kan ook een IP-adres of een volledig gekwalificeerde verbindingsreeks op de server zijn.
3. Selecteer een verzameling of maak een nieuwe (optioneel)
4. **Voltooi** om de gegevensbron te registreren.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="opties voor bronnen registreren" border="true":::

## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

Doe het volgende om een nieuwe scan te maken en uit te voeren:
1. Klik in het Beheercentrum op *Integratieruntime*. Zorg ervoor dat een zelf-hostende Integration Runtime is ingesteld. Als deze niet is ingesteld, volgt u de stappen die [hier](manage-integration-runtimes.md) worden beschreven om een zelf-hostende Integration Runtime te maken voor het scannen op een on-premises of Azure VM die toegang heeft tot uw on-premises netwerk.

2. Ga vervolgens naar **Bronnen**

3. Selecteer de geregistreerde Teradata-bron.

4. Selecteer + Nieuwe scan
 
5. Geef de onderstaande details op:

    - Naam: De naam van de scan

    - Verbinden via integratieruntime: Selecteer de geconfigureerde zelf-hostende Information Runtime

    - Verificatiemethode: Databaseverificatie is de enige optie die nu wordt ondersteund. Dit wordt standaard geselecteerd

    - Gebruikersnaam: Een gebruikersnaam om verbinding te maken met de databaseserver. Deze gebruikersnaam moet leestoegang hebben tot de server

    - Wachtwoord: Het gebruikerswachtwoord dat wordt gebruikt om verbinding te maken met de databaseserver

    - Schema: Een lijst met een subset van schema's die moeten worden geïmporteerd, uitgedrukt in een door puntkomma's gescheiden lijst. bijvoorbeeld schema1;schema2. Alle gebruikersschema's worden geïmporteerd als de lijst leeg is. Alle systeemschema's (bijvoorbeeld SysAdmin) en objecten worden standaard genegeerd.

        Acceptabele schemanaampatronen met de syntaxis van SQL LIKE-expressies zijn onder andere het gebruik van %, bijvoorbeeld A%; %B; %C%; D
        - begin met A of    
        - eindig met B of    
        - bevat C of    
        - gelijk aan D

        Gebruik van NOT en speciale tekens is niet toegestaan

    - Locatie van stuurprogramma: Volledige pad naar de locatie van het Teradata-stuurprogramma op de VM van de klant. De naam van het Teradata JDBC-stuurprogramma moet zijn: com.teradata.jdbc.TeraDriver

    - Maximaal beschikbaar geheugen: Maximaal geheugen (in GB) dat beschikbaar is op de VM van de klant om te worden gebruikt door scanprocessen. Dit is afhankelijk van de grootte van de Teradata-bron die moet worden gescand.

    > [!Note] 
    > Als vuistregel moet u 2 GB geheugen voor elke 1000 tabellen opgeven

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="scan instellen" border="true":::

6. Klik op *Doorgaan.*

7. Kies de scantrigger. U kunt een schema instellen of de scan eenmalig uitvoeren.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="trigger voor scannen" border="true":::

8. Controleer uw scan en klik op *Opslaan en uitvoeren.*

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
