---
title: Gegevens bronnen documenteren in Azure Data Catalog
description: Instructies voor het vastleggen van gegevensassets in Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 3674c316e34b1e54ed8282ac7d2c228a2b774c06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019018"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Gegevens bronnen documenteren in Azure Data Catalog

## <a name="introduction"></a>Introductie
**Microsoft Azure Data Catalog** is een volledig beheerde Cloud service die fungeert als registratie systeem en detectie systeem voor zakelijke gegevens bronnen. Met andere woorden, het **Azure Data Catalog** is alles wat helpt mensen bij het detecteren, *begrijpen* en gebruiken van gegevens bronnen, en helpt organisaties bij het verkrijgen van meer waarde dan hun bestaande gegevens.

Wanneer een gegevens bron wordt geregistreerd bij **Azure Data Catalog**, worden de meta gegevens gekopieerd en geïndexeerd door de service, maar het artikel eindigt daar niet. **Azure Data Catalog** kunnen gebruikers ook hun eigen volledige documentatie geven waarmee het gebruik en de algemene scenario's voor de gegevens bron kunnen worden beschreven.

[Als u aantekeningen wilt maken voor gegevens bronnen](data-catalog-how-to-annotate.md), leert u dat experts die de gegevens bron kennen, aantekeningen kunnen maken met tags en een beschrijving. De **Azure Data Catalog** -Portal bevat een RTF-editor, zodat gebruikers gegevens assets en containers volledig kunnen documenteren. De editor bevat opmaak van alinea's, zoals koppen, tekst opmaak, lijsten met opsommings tekens, genummerde lijsten en tabellen.

Tags en beschrijvingen zijn handig voor eenvoudige aantekeningen. Om gegevens gebruikers beter inzicht te geven in het gebruik van een gegevens bron en zakelijke scenario's voor een gegevens bron, kan een deskundige volledige, gedetailleerde documentatie bieden. Het is eenvoudig om een gegevens bron te documenteren. Selecteer een gegevens activum of container en kies **documentatie**.

![Het tabblad documentatie in een Data Catalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Gegevensassets documenteren
Dankzij het voor deel van **Azure Data Catalog** documentatie kunt u uw Data Catalog gebruiken als opslag plaats voor inhoud om een volledig afronding van uw gegevensassets te maken. U kunt gedetailleerde inhoud verkennen waarin containers en tabellen worden beschreven. Als u al inhoud in een andere inhouds opslagplaats hebt, zoals share point of een bestands share, kunt u toevoegen aan de Asset-documentatie koppelingen om te verwijzen naar deze bestaande inhoud. Met deze functie kunnen uw bestaande documenten beter worden gedetecteerd.

> [!NOTE]
> Documentatie is niet opgenomen in de zoek index.
>

![Het tabblad documentatie en Hyper Link naar web link](media/data-catalog-documentation/data-catalog-documentation2.png)

Het documentatie niveau kan variëren van het beschrijven van de kenmerken en waarde van een gegevens Asset container naar een gedetailleerde beschrijving van het tabel schema binnen een container. Het niveau van de documentatie moet worden verstrekt door uw bedrijfs behoeften. Maar in het algemeen zijn hier enkele voor-en nadelen van het documenteren van gegevensassets:

* Documenteer alleen een container: alle inhoud bevindt zich op één locatie, maar kan de gebruikers niet de benodigde informatie geven om een weloverwogen beslissing te nemen.
* Documenteer alleen de tabellen: inhoud is specifiek voor dat object, maar uw gebruikers hebben meerdere locaties voor documenten.
* Document containers en tabellen: de meest uitgebreide benadering, maar kan meer onderhoud van de documenten veroorzaken.

## <a name="summary"></a>Samenvatting
Het documenteren van gegevens bronnen met **Azure Data Catalog** kan een verduidelijking van uw gegevensassets in zoveel detail maken als u wilt.  Met koppelingen kunt u een koppeling maken naar inhoud die is opgeslagen in een bestaande inhouds opslagplaats, waardoor uw bestaande documenten en gegevens assets samen worden gebracht. Zodra de gebruikers de juiste gegevensassets hebben gedetecteerd, kunnen ze een volledige set documentatie hebben.
