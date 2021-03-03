---
title: SAP S/4HANA source and Setup scans (preview) registreren in azure controle sfeer liggen
description: In dit artikel vindt u een overzicht van het registreren van SAP S/4HANA-bron in azure controle sfeer liggen en het instellen van een scan.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 6d31bd0911b5cf765215e6a482a39b2458c4ba0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696147"
---
# <a name="register-and-scan-a-sap-s4hana-source-preview"></a>Een SAP S/4HANA-bron registreren en scannen (preview)

In dit artikel wordt beschreven hoe u een SAP S/4HANA-bron in controle sfeer liggen kunt registreren en een scan kunt instellen.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De SAP S/4HANA-bron ondersteunt **volledige scan** voor het extra heren van meta gegevens uit een SAP S/4HANA-exemplaar en het ophalen van **afkomst** tussen gegevensassets.

## <a name="prerequisites"></a>Vereisten

1.  Stel de nieuwste [zelf-hostende Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717)in.
    Zie [een zelf-hostende Integration runtime maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)voor meer informatie.

2.  Zorg ervoor dat de [jdk 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) is geïnstalleerd op uw virtuele machine waarop zelf-hostende Integration runtime is geïnstalleerd.

3.  Controleer of \" Visual C++ redistributable 2012 update 4 \" is geïnstalleerd op de zelf-hostende Integration runtime-computer. Als u de app \' nog niet hebt geïnstalleerd, kunt u deze [hier](https://www.microsoft.com/download/details.aspx?id=30679)downloaden.

4.  Down load de 64-bits [SAP-connector voor Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) van \' de SAP s-website en installeer deze op de zelf-hostende Integration runtime-computer. Zorg er tijdens de installatie voor dat u de optie **Assembly's installeren naar GAC** selecteert in het venster **optionele installatie stappen** .

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="vereiste" border="true":::

5.  De connector leest meta gegevens van SAP met behulp van de Java connector (JCo) 3,0-API. Zorg er daarom voor dat de Java-connector beschikbaar is op uw virtuele machine waarop zelf-hostende Integration runtime is geïnstalleerd.
    Zorg ervoor dat u de juiste JCo-distributie gebruikt voor uw omgeving. Controleer bijvoorbeeld op een micro soft Windows-computer of de bestanden sapjco3. jar en sapjco3.dll beschikbaar zijn.

    > [!Note] 
    >Het stuurprogramma moet toegankelijk zijn voor alle accounts in de VM. Installeer het niet in een gebruikers account.

6.  Implementeer de ABAP-functie module voor het ophalen van meta gegevens op de SAP-server door de stappen te volgen die worden beschreven in de [implementatie handleiding voor ABAP-functies](abap-functions-deployment-guide.md). U hebt een ABAP-ontwikkelaars account nodig om de functie module RFC te maken op de SAP-server. Het gebruikers account vereist voldoende machtigingen om verbinding te maken met de SAP-server en de volgende RFC-functie modules uit te voeren:
    -   STFC_CONNECTION (connectiviteit controleren)
    -   RFC_SYSTEM_INFO (systeem gegevens controleren)

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

De enige ondersteunde verificatie voor SAP S/4HANA-bron is **basis verificatie**

## <a name="register-sap-s4hana-source"></a>SAP S/4HANA-bron registreren

Ga als volgt te werk om een nieuwe SAP S/4HANA-bron in uw Data Catalog te registreren:

1.  Navigeer naar uw controle sfeer liggen-account.
2.  Selecteer **bronnen** in het linkernavigatievenster.
3.  Selecteer **Registreren**
4.  Selecteer voor bronnen registreren **SAP S/4HANA.** Selecteer **Doorgaan**

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="SAP'S/4Hana-opties registreren" border="true":::

Ga als volgt te werk op het scherm **bronnen registreren (SAP S/4HANA)** :

1.  Voer een **naam** in die voor de gegevens bron wordt weer gegeven in de catalogus.

2.  Voer de naam van de **toepassings server** in om verbinding te maken met de SAP S/4HANA-bron. Het kan ook een IP-adres zijn van de SAP Application Server-host.

3.  Voer het SAP- **systeem nummer** in. Dit is een geheel getal van twee cijfers tussen 00 en 99.

4.  Selecteer een verzameling of maak een nieuwe (optioneel)

5.  Voltooi om de gegevensbron te registreren.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="SAP S/4HANA registreren" border="true":::

## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

Doe het volgende om een nieuwe scan te maken en uit te voeren:

1.  Klik in het Beheercentrum op Integratieruntime. Zorg ervoor dat een zelf-hostende Integration Runtime is ingesteld. Als deze niet is ingesteld, volgt u de stappen die [hier](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) worden beschreven om een zelf-hostende Integration runtime te maken

2.  Navigeer naar **bronnen.**

3.  Selecteer de geregistreerde SAP S/4HANA-bron.

4.  Selecteer **+ Nieuwe scan**

5.  Geef de onderstaande details op:

    a.  **Naam**: de naam van de scan

    b.  **Verbinding maken via Integration runtime**: Selecteer de geconfigureerde zelf-hostende Integration runtime.

    c.  **Referentie**: Selecteer de referentie om verbinding te maken met uw gegevens bron. Zorg ervoor dat:

    -   Selecteer basis verificatie tijdens het maken van een referentie.
    -   Geef een gebruikers-ID op om verbinding te maken met de SAP-server in het invoer veld voor de gebruikers naam.
    -   Sla het gebruikers wachtwoord op dat wordt gebruikt om verbinding te maken met de SAP-server in de geheime sleutel.

    d.  **Client-id:** Geef hier de SAP-client-ID op. De client wordt aangeduid met een numeriek getal van Maxi maal 999.

    e.  **Pad naar JCo**: Geef het pad op naar de map waarin zich de JCo-bibliotheken bevinden.

    f.  **Maxi maal beschikbaar geheugen:** Maxi maal geheugen (in GB) dat beschikbaar is op de virtuele machine van de klant om te worden gebruikt door Scan processen. Dit is afhankelijk van de grootte van SAP S/4HANA-bron die moet worden gescand.

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="SAP S/4HANA scannen" border="true":::

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
