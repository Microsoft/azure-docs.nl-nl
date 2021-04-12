---
title: Azure controle sfeer liggen Studio gebruiken
description: In dit artikel wordt beschreven hoe u Azure controle sfeer liggen Studio gebruikt.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: ba22c322d47d8738b1d607597d6f93b8b8456616
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283866"
---
# <a name="use-purview-studio"></a>Purview Studio gebruiken

Dit artikel geeft een overzicht van enkele van de belangrijkste functies van Azure controle sfeer liggen.

## <a name="prerequisites"></a>Vereisten

* Er is al een actief controle sfeer liggen-account gemaakt in Azure Portal en de gebruiker heeft machtigingen voor toegang tot controle sfeer liggen Studio.

## <a name="launch-purview-account"></a>Controle sfeer liggen-account starten

* Als u uw controle sfeer liggen-account wilt starten, gaat u naar controle sfeer liggen accounts in Azure Portal, selecteert u het account dat u wilt starten en start u het account.

  :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Schermopname van de selectie om het Azure Purview-account te starten.":::

* U kunt ook een controle sfeer liggen-account starten door naar te gaan `https://web.purview.azure.com` , **Azure Active Directory** en een account naam te selecteren om het account te starten.

## <a name="home-page"></a>Startpagina

**Home** is de start pagina voor de Azure controle sfeer liggen-client.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Scherm opname van de start pagina.":::

De volgende lijst bevat een overzicht van de belangrijkste functies van de **Start pagina**. Elk getal in de lijst komt overeen met een gemarkeerd getal in de vorige scherm afbeelding.

1. Beschrijvende naam van de catalogus. U kunt catalogus naam instellen in de  >  **account gegevens** van het Management Center.

2. Catalogus analyse toont het aantal:

   * Gebruikers, groepen en toepassingen
   * Gegevensbronnen
   * Assets
   * Woordenlijsttermen

3. Met het zoekvak kunt u zoeken naar gegevensassets in de gegevens catalogus.

4. Met de knoppen voor snelle toegang krijgt u toegang tot veelgebruikte functies van de toepassing. Welke knoppen worden weer gegeven, is afhankelijk van de rol die is toegewezen aan uw gebruikers account.

   * Voor *Data curator* zijn de knoppen **kennis centrum**, **Bladeren door assets**, de **woorden lijst beheren** en **inzichten weer geven**.
   * Voor de *gegevens lezer* zijn de aanbevolen knoppen **kennis centrum**, **Bladeren door assets**, **woorden lijst weer geven** en **inzichten weer geven**.
   * Voor *gegevens bron beheer*  +  *gegevens curator* zijn de aanbevolen knoppen **kennis centrum**, **gegevens bronnen registreren**, **Bladeren door assets** en **woorden lijst beheren**.
   * Voor de gegevens lezer van de *gegevensbron beheerder*  +  zijn de aanbevolen knoppen **kennis centrum**, **gegevens bronnen registreren**, **Bladeren door assets** en **woorden lijst weer geven**.

5. De linkernavigatiebalk helpt u bij het zoeken naar de hoofd pagina's van de toepassing. Welke knoppen worden weer gegeven, is afhankelijk van de rol die is toegewezen aan uw gebruikers account.

   * Voor *Data curator* zijn de knoppen **Home**, **Glossary**, **Insights** en **Management Center**.
   * Voor de *gegevens lezer* zijn de knoppen **Home**, **Glossary**, **Insights** en **Management Center**.
   * Voor de *gegevens bron beheerder* of *gegevens curator/lezer* zijn de knoppen **Home**, **bronnen**, **woorden lijst**, **inzichten** en **Management Center**.
  
6. Op het tabblad **recent geopend** ziet u een lijst met recent geopende gegevensassets. Zie voor meer informatie over het openen van assets [de Data Catalog zoeken](how-to-search-catalog.md) en [Bladeren per activum type](how-to-browse-catalog.md#browse-experience).  Tabblad **mijn items** is een lijst met gegevens assets die eigendom zijn van de aangemelde gebruiker.
7. **Handige koppelingen** bevatten koppelingen naar status van regio's, Documentatie, prijzen, overzicht en controle sfeer liggen status
8. De bovenste navigatie balk bevat informatie over release opmerkingen/updates, het wijzigen van controle sfeer liggen-account, meldingen, Help en feedback secties.

## <a name="knowledge-center"></a>Kennis centrum

In het kennis centrum vindt u alle Video's en zelf studies die betrekking hebben op controle sfeer liggen.

## <a name="guided-tours"></a>Rond leidingen

Elke UX in azure controle sfeer liggen Studio bevat rond leidingen om een overzicht van de pagina te geven. Als u de rond leiding wilt starten, selecteert u **Help** op de bovenste balk en selecteert u **begeleide rond leidingen**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Scherm afbeelding van de rond leiding.":::

> [!Important]
> De *beheerder van de gegevens bron* heeft geen toegang tot controle sfeer liggen Studio.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beveiligingsprincipal toevoegen](tutorial-scan-data.md)
