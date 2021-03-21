---
title: Gegevensbronnen registreren in Azure Data Catalog
description: In dit artikel wordt uitgelegd hoe u gegevens bronnen registreert in Azure Data Catalog, met inbegrip van de velden voor meta gegevens die zijn geëxtraheerd tijdens de registratie.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fe132c5f80e138ef47db758ad42c04b8e854ca00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674798"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Gegevensbronnen registreren in Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Inleiding
Azure Data Catalog is een volledig beheerde Cloud service die fungeert als een systeem van registratie en detectie voor zakelijke gegevens bronnen. Met andere woorden, Data Catalog helpt mensen bij het ontdekken, begrijpen en gebruiken van gegevens bronnen, en het helpt organisaties meer waarde te krijgen van hun bestaande gegevens. De eerste stap voor het toegankelijk maken van een gegevens bron via Data Catalog is het registreren van die gegevens bron.

## <a name="register-data-sources"></a>Gegevensbronnen registreren
Registratie is het proces van het extra heren van meta gegevens uit de gegevens bron en het kopiëren van die gegevens naar de Data Catalog-service. De gegevens blijven waar ze zich momenteel bevinden, onder het beheer van de groep beheerders en het beleid van het huidige systeem.

Als u een gegevens bron wilt registreren, gaat u als volgt te werk:
1. Start in de Azure Data Catalog Portal het hulp programma registratie van Data Catalog gegevens bron. 
2. Aanmelden met uw werk-of schoolaccount met dezelfde Azure Active Directory referenties die u gebruikt om u aan te melden bij de portal.
3. Selecteer de gegevens bron die u wilt registreren.

Zie voor meer stapsgewijze Details de zelf studie [aan de slag met Azure Data Catalog](data-catalog-get-started.md) .

Nadat u de gegevens bron hebt geregistreerd, houdt de catalogus de locatie bij en indexeert de meta gegevens ervan. Gebruikers kunnen de gegevens bron zoeken, doorzoeken en detecteren, en de locatie ervan gebruiken om er verbinding mee te maken met behulp van de toepassing of het hulp programma van hun keuze.

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen
Zie [Data CATALOG DSR](data-catalog-dsr.md)voor een lijst met gegevens bronnen die momenteel worden ondersteund.

## <a name="structural-metadata"></a>Structurele meta gegevens
Wanneer u een gegevens bron registreert, haalt het registratie hulpprogramma informatie over de structuur van de objecten die u selecteert. Deze informatie wordt aangeduid als structurele meta gegevens.

Voor alle objecten bevat deze structurele meta gegevens de locatie van het object, zodat gebruikers die de gegevens detecteren deze informatie kunnen gebruiken om verbinding te maken met het object in de client hulpprogramma's van hun keuze. Andere structurele meta gegevens omvatten object naam en-type, en kenmerk/kolom naam en gegevens type.

## <a name="descriptive-metadata"></a>Beschrijvende meta gegevens
Naast de kern structuur meta gegevens die zijn geëxtraheerd uit de gegevens bron, haalt het hulp programma voor registratie van gegevens bronnen beschrijvende meta gegevens op. Voor SQL Server Analysis Services en SQL Server Reporting Services worden deze meta gegevens opgehaald uit de beschrijvings eigenschappen die door deze services worden weer gegeven. Voor SQL Server worden waarden die zijn gegeven met de \_ uitgebreide eigenschap MS Description geëxtraheerd. Voor Oracle Database haalt het hulp programma voor registratie van gegevens bronnen de kolom opmerkingen uit de \_ \_ weer gave alle tabblad opmerkingen.

Naast de beschrijvende meta gegevens die uit de gegevens bron zijn geëxtraheerd, kunnen gebruikers beschrijvende meta gegevens invoeren met het hulp programma voor registratie van gegevens bronnen. Gebruikers kunnen Tags toevoegen en ze kunnen experts identificeren voor de geregistreerde objecten. Al deze beschrijvende meta gegevens worden samen met de structurele meta gegevens gekopieerd naar de Data Catalog-service.

## <a name="include-previews"></a>Voor beelden toevoegen
Standaard worden alleen meta gegevens geëxtraheerd uit gegevens bronnen en gekopieerd naar de Data Catalog-service, maar inzicht in een gegevens bron wordt vaak eenvoudiger gemaakt wanneer u een voor beeld van de gegevens in het bestand kunt bekijken.

Met behulp van het Data Catalog hulp programma voor gegevens bron registratie kunt u een voor beeld van een moment opname van de gegevens in elke tabel en weer gave die is geregistreerd, toevoegen. Als u voor beelden tijdens de registratie wilt opnemen, omvat het registratie hulpprogramma Maxi maal twintig records uit elke tabel en weer gave. Deze moment opname wordt vervolgens gekopieerd naar de catalogus, samen met de structurele en beschrijvende meta gegevens.

> [!NOTE]
> Grote tabellen met een groot aantal kolommen kunnen minder dan twintig records bevatten die zijn opgenomen in de preview-versie.
>
>

## <a name="include-data-profiles"></a>Gegevens profielen toevoegen
Net als bij het opnemen van previews kan de context van gebruikers die zoeken naar gegevens bronnen in Data Catalog worden geboden, met inbegrip van een gegevens profiel, waardoor het eenvoudiger is om gedetecteerde gegevens bronnen te begrijpen.

Met behulp van het Data Catalog hulp programma voor gegevens bron registratie kunt u een gegevens profiel voor elke tabel en weer gave die is geregistreerd, toevoegen. Als u ervoor kiest om een gegevens profiel op te nemen tijdens de registratie, bevat het registratie hulpprogramma statistische statistieken over de gegevens in elke tabel en weer gave, waaronder:

* Het aantal rijen en de grootte van de gegevens in het object.
* De datum voor de meest recente update van de gegevens en het object schema.
* Het aantal null-records en DISTINCT-waarden voor kolommen.
* De waarden voor minimum, maximum, gemiddelde en standaard deviatie voor kolommen.

Deze statistieken worden vervolgens gekopieerd naar de catalogus, samen met de structurele en beschrijvende meta gegevens.

> [!NOTE]
> Tekst-en datum kolommen bevatten geen statistieken over gemiddelde of standaard deviatie in hun gegevens profiel.
>
>

## <a name="update-registrations"></a>Registraties bijwerken
Als u een gegevens bron registreert, kan deze worden gedetecteerd in Data Catalog wanneer u de meta gegevens en de optionele preview-versie die tijdens de registratie is geëxtraheerd, gebruikt. Als de gegevens bron moet worden bijgewerkt in de catalogus (bijvoorbeeld als het schema van een object is gewijzigd, de tabellen die oorspronkelijk zijn uitgesloten, moeten worden opgenomen of als u de gegevens wilt bijwerken die in de voor beelden zijn opgenomen), kan het hulp programma voor het registreren van gegevens bronnen opnieuw worden uitgevoerd.

Als u een al geregistreerde gegevens bron opnieuw registreert, wordt de bewerking ' upsert ' samen voegen uitgevoerd: bestaande objecten worden bijgewerkt en nieuwe objecten worden gemaakt. Meta gegevens die door gebruikers via de Data Catalog Portal worden verschaft, blijven behouden.

## <a name="summary"></a>Samenvatting
Omdat hiermee structurele en beschrijvende meta gegevens van een gegevens bron naar de catalogus service worden gekopieerd en de gegevens bron in Data Catalog wordt geregistreerd, zijn de gegevens eenvoudiger te detecteren en te begrijpen. Nadat u de gegevens bron hebt geregistreerd, kunt u aantekeningen maken, beheren en detecteren met behulp van de Data Catalog Portal.

## <a name="next-steps"></a>Volgende stappen
Zie de zelf studie [aan de slag met Azure Data Catalog](data-catalog-get-started.md) voor meer informatie over het registreren van gegevens bronnen.
