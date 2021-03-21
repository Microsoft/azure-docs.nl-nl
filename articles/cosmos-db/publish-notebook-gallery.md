---
title: Notitie blokken publiceren naar de galerie met Azure Cosmos DB notebooks
description: Meer informatie over het downloaden van notitie blokken uit de open bare galerie, het bewerken ervan en het publiceren van uw eigen notitie blokken in de galerie.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039250"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Notitie blokken publiceren naar de galerie met Azure Cosmos DB notebooks
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ingebouwde Jupyter-notebooks worden rechtstreeks geïntegreerd in uw Azure Cosmos DB-accounts in de Azure Portal. Met deze notitie blokken kunt u uw gegevens uit het Azure Portal analyseren en visualiseren. Ingebouwde notitie blokken voor Azure Cosmos DB zijn momenteel beschikbaar in [veel regio's](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Als u notitie blokken wilt gebruiken, [maakt u een nieuw Cosmos-account](create-cosmosdb-resources-portal.md) of [schakelt u notitie blokken in voor een bestaand account](enable-notebooks.md) in een van deze regio's.

De notitieblok omgeving in het Azure Portal bevat enkele voor beelden die zijn gepubliceerd door het Azure Cosmos DB team. Het bevat ook een open bare galerie waarin u uw eigen notitie blokken kunt publiceren en delen. Nadat een notitie blok is gepubliceerd in de galerie, is het beschikbaar voor alle Azure Cosmos DB gebruikers om het te bekijken en te gebruiken. In dit artikel leert u hoe u notitie blokken kunt gebruiken uit de open bare galerie en hoe u uw notitie blok kunt publiceren naar de galerie.

## <a name="download-a-notebook-from-the-gallery"></a>Een notitie blok downloaden uit de galerie

Gebruik de volgende stappen om notitie blokken in de galerie te bekijken en te downloaden naar uw eigen notitie blok-werk ruimte:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar uw Azure Cosmos DB-account dat is ingeschakeld met de notebook-omgeving.

1. Ga naar het   >    >    >  tabblad **open bare galerie** Data Explorer notitie blokken galerie.

1. Accepteer de [gedrags code](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  voordat u notitie blokken in de galerie bekijkt of publiceert. De gedrags code wordt per gebruiker vastgelegd, per het abonnements niveau. Wanneer u overschakelt naar een ander abonnement, moet u dit opnieuw accepteren voordat u toegang krijgt tot de galerie. Als u de gedrags code wilt accepteren, schakelt u het selectie vakje in en **gaat u door met** het volgende:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Ga naar de open bare galerie met notitie blokken en accepteer de gedrags code.":::

1. Vervolgens kunt u een specifieke notebook toevoegen aan het tabblad Favorieten of het notitie blok downloaden. Wanneer u een notitie blok downloadt, wordt het gekopieerd naar uw werk ruimte notitie blokken waar u het kunt uitvoeren of bewerken.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Down load een notitie blok van de galerie naar uw werk ruimte.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Een notitie blok naar de galerie publiceren

Wanneer u uw eigen notitie blokken bouwt of de bestaande notitie blokken bewerkt zodat deze overeenkomen met een ander scenario, kunt u deze publiceren naar de galerie. Nadat een notitie blok naar de galerie is gepubliceerd, hebben andere gebruikers er toegang toe. U kunt de galerie met voor [beelden van notebook verkennen](https://cosmos.azure.com/gallery.html) om de momenteel beschik bare notitie blokken weer te geven.

Gebruik de volgende stappen om een notitie blok te publiceren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar uw Azure Cosmos DB-account dat is ingeschakeld met de notebook-omgeving.

1. Ga naar het tabblad **Data Explorer**  >  **notitie blokken**  >  **mijn notitie** blokken.

1. Ga naar het notitie blok dat u wilt publiceren. Selecteer de knop **Opslaan** in de opdracht balk en selecteer vervolgens **publiceren naar galerie**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Kies een notitie blok om naar de galerie te publiceren.":::

   U kunt ook de optie **publiceren naar galerie** vinden door de te selecteren **...** Naast de naam van het notitie blok:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Een andere manier om een notitie blok te kiezen om te publiceren naar de galerie.":::

1. Vul het formulier **publiceren op Galerie** in met de volgende details:

   * **Naam:** Een beschrijvende naam om uw notitie blok te identificeren.
   * **Beschrijving:**  Een korte beschrijving van de werking van uw notitie blok.
   * **Tags:** Tags zijn optioneel en worden gebruikt voor het filteren van resultaten wanneer ze op een tref woord worden doorzocht.
   * **Afbeelding van voor beeld:** Een afbeelding die wordt gebruikt op het voor blad wanneer het notitie blok wordt gepubliceerd. U kunt een van de volgende opties kiezen:
   * **Aangepaste installatie kopie** : u kunt een installatie kopie van uw computer uploaden. Kies een afbeeldings bestand met een hoogte-breedte verhouding 256x144.
   * **URL** : Geef een openbaar toegankelijke URL op waar de installatie kopie zich bevindt.
   * **Scherm opname maken** : er wordt automatisch een scherm opname van uw geopende notitie blok gemaakt en geüpload naar de preview-versie.
   * De **eerste weer gave** -uitvoer uitvoer van de eerste cel met een weer gave-uitvoer. Cellen die alleen de prijs verlaging/tekst weer geven, tellen niet als een weergave uitvoer.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Vul het formulier publiceren op Galerie in.":::

   > [!NOTE]
   > Als u de optie **publiceren op Galerie** gebruikt vanuit de **..** . Naast de naam van het notitie blok, worden niet alle opties **voor de installatie kopie** weer geven. De reden hiervoor is dat het notitie blok mogelijk niet open is en dat Azure Cosmos DB geen scherm opname krijgt of toegang krijgt tot de eerste weer gave-uitvoer.

1. Controleer of de preview goed lijkt en selecteer **publiceren**. Wanneer u dit notitie blok publiceert, wordt dit in de open bare galerie Azure Cosmos DB-notitie blokken weer gegeven. Zorg ervoor dat u alle gevoelige gegevens of uitvoer hebt verwijderd voordat u deze publiceert. De inhoud van het notitie blok wordt gecontroleerd op eventuele schending van het micro soft-beleid voordat het wordt gepubliceerd. Dit proces duurt meestal een paar seconden. Als er schendingen worden gevonden, wordt er een bericht weer gegeven op het tabblad melding. Uw notitie blok wordt niet gepubliceerd als er een schending wordt gevonden. u verwijdert de geschonden tekst en publiceert deze opnieuw.

   > [!NOTE]
   > Nadat de notitie blokken zijn gepubliceerd naar de open bare galerie, kunnen alle Azure Cosmos DB gebruikers deze weer geven. De toegang is niet beperkt tot per abonnement of per organisatie niveau.

1. Nadat het notitie blok naar de galerie is gepubliceerd, kunt u het zien op het tabblad **mijn gepubliceerde werk** . U kunt ook de notitie blokken die u hebt gepubliceerd uit de open bare Galerie verwijderen of verwijderen.

1. U kunt ook een notitie blok melden dat de gedrags code schendt. Als er een schending wordt gevonden, wordt het notitie blok automatisch door Cosmos DB verwijderd uit de galerie. Als een notitie blok wordt verwijderd, kunnen gebruikers het weer geven op het tabblad **mijn gepubliceerde werk** met de verwijderde notitie. Als u een notitie blok wilt melden, gaat u naar het tabblad **Data Explorer**  >  **notitie blokken**  >  **Galerie**  >  **open bare galerie** . Open het notitie blok dat u wilt rapporteren, klik op de knop **Help** in de rechter bovenhoek en selecteer **rapport misbruik**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Meld een notebook dat de gedrags code schendt.":::

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de voor delen van [Azure Cosmos DB Jupyter-notebooks](cosmosdb-jupyter-notebooks.md)
* [Python-notebookfuncties en-opdrachten gebruiken](use-python-notebook-features-and-commands.md)
* [C#-notebookfuncties en -opdrachten gebruiken](use-csharp-notebook-features-and-commands.md)
* [Notitie blokken importeren uit een GitHub-opslag plaats](import-github-notebooks.md)
