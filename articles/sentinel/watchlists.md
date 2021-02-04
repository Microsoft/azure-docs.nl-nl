---
title: Azure Sentinel-volglijsten gebruiken
description: In dit artikel wordt beschreven hoe u Azure Sentinel Watchlists-onderzoek bedreigingen kunt gebruiken, zakelijke gegevens kunt importeren, lijsten voor toestaan en verrijkende gebeurtenis gegevens maakt.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555651"
---
# <a name="use-azure-sentinel-watchlists"></a>Azure Sentinel-volglijsten gebruiken

> [!IMPORTANT]
> De functie Watchlists is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Azure Sentinel Watchlists maken het verzamelen van gegevens uit externe gegevens bronnen mogelijk voor correlatie met de gebeurtenissen in uw Azure-Sentinel-omgeving. Nadat u deze hebt gemaakt, kunt u Watchlists gebruiken in uw Zoek-, detectie-, bedreigings-en reactie playbooks. Watchlists worden opgeslagen in uw Azure-Sentinel-werk ruimte als naam/waarde-paren en worden in de cache geplaatst voor optimale query prestaties en lage latentie.

Veelvoorkomende scenario's voor het gebruik van Watchlists zijn:

- Het **onderzoeken van bedreigingen** en het snel reageren op incidenten met de snelle invoer van IP-adressen, bestands-hashes en andere gegevens uit CSV-bestanden. Na het importeren kunt u watch list-naam/waarde-paren gebruiken voor samen voegingen en filters in waarschuwings regels, Threat-jacht, werkmappen, notitie blokken en algemene query's.

- **Bedrijfs gegevens importeren** als een watch list. Importeer bijvoorbeeld gebruikers lijsten met geprivilegieerde systeem toegang of beëindigde werk nemers, en gebruik vervolgens de watch list om lijsten toe te voegen en te weigeren die worden gebruikt om te voor komen dat gebruikers zich kunnen aanmelden bij het netwerk.

- **Waarschuwings-vermoeidheid verlagen**. Maak allow-lijsten om waarschuwingen van een groep gebruikers te onderdrukken, zoals gebruikers van geautoriseerde IP-adressen waarmee taken worden uitgevoerd waarmee de waarschuwing normaal gesp roken wordt geactiveerd, en voor komt dat ongeoorloofde gebeurtenissen waarschuwingen ontvangen.

- **Verrijkende gebeurtenis gegevens**. Gebruik Watchlists om uw gebeurtenis gegevens te verrijken met combi Naties van naam en waarde die zijn afgeleid van externe gegevens bronnen.

## <a name="create-a-new-watchlist"></a>Een nieuwe watch list maken

1. Navigeer vanuit het Azure Portal naar de **Azure Sentinel**  >  **Configuration**  >  **Watch list** en selecteer vervolgens **nieuwe toevoegen**.

    > [!div class="mx-imgBorder"]
    > ![nieuwe watch list](./media/watchlists/sentinel-watchlist-new.png)

1. Geef op de pagina **Algemeen** de naam, beschrijving en alias voor de watch list op en selecteer **volgende**.

    > [!div class="mx-imgBorder"]
    > ![pagina Algemeen watch list](./media/watchlists/sentinel-watchlist-general.png)

1. Selecteer op de pagina **bron** het type gegevensset, upload een bestand en selecteer vervolgens **volgende**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="Watch list-bron pagina" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Bestands uploads zijn momenteel beperkt tot bestanden van Maxi maal 3,8 MB.

1. Controleer de informatie, Controleer of deze juist is en selecteer vervolgens **maken**.

    > [!div class="mx-imgBorder"]
    > ![pagina Watch list-controle](./media/watchlists/sentinel-watchlist-review.png)

    Zodra de watch list is gemaakt, wordt er een melding weer gegeven.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="melding voor het maken van watch list geslaagd" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>Watchlists gebruiken in query's

1. Ga vanuit het Azure Portal naar de **Azure Sentinel**  >  **Configuration**  >  **Watch list**, selecteer de watch list die u wilt gebruiken en selecteer vervolgens **weer geven in log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="Watchlists gebruiken in query's" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. De items in uw watch list worden automatisch geëxtraheerd voor uw query en worden weer gegeven op het tabblad **resultaten** . In het onderstaande voor beeld ziet u de resultaten van de extractie van de velden **servername** en **IpAddress** .

    > [!NOTE]
    > De tijds tempel van uw query's wordt genegeerd in zowel de query-UI als in geplande waarschuwingen.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="query's met watch list-velden" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. U kunt gegevens in elke tabel opvragen op basis van gegevens uit een watch list door de watch list te behandelen als een tabel voor samen voegingen en zoek opdrachten.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="query's uitvoeren op watch list als zoeken":::

## <a name="use-watchlists-in-analytics-rules"></a>Watchlists gebruiken in Analytics-regels

Als u Watchlists in Analytics-regels wilt gebruiken, gaat u vanuit het Azure Portal naar **Azure Sentinel**  >  **Configuration**  >  **Analytics** en maakt u een regel met behulp van de `_GetWatchlist('<watchlist>')` functie in de query.

1. In dit voor beeld maakt u een watch list met de naam ' ipwatchlist ' met de volgende waarden:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="lijst met vier items voor watch list":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="Watch list maken met vier items":::

1. Maak vervolgens de Analytics-regel.  In dit voor beeld bevatten we alleen gebeurtenissen van IP-adressen in de watch list:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="Watchlists gebruiken in Analytics-regels":::

## <a name="view-list-of-watchlists-aliases"></a>Lijst met Watchlists-aliassen weer geven

Als u een lijst met watch list-aliassen wilt ophalen, gaat u vanuit het Azure Portal naar **Azure Sentinel**  >  **General**  >  **logs** en voert u de volgende query uit: `_GetWatchlistAlias` . U kunt de lijst met aliassen bekijken op het tabblad **resultaten** .

> [!div class="mx-imgBorder"]
> ![Watchlists weer geven](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Watchlists in azure Sentinel kunt gebruiken om gegevens te verrijken en onderzoeken te verbeteren. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
