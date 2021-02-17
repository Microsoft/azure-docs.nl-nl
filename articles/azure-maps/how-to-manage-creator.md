---
title: Microsoft Azure Maps-toewijzings aars beheren (preview-versie)
description: In dit artikel leert u hoe u Microsoft Azure Maps Creator (preview) kunt beheren.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/16/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d26df4287032bc59cc58dd1d832d9d5a9c40afcd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559198"
---
# <a name="manage-azure-maps-creator-preview"></a>Azure Maps Maker beheren (preview-versie) 

> [!IMPORTANT]
> Azure Maps Creator-services zijn momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Maps Maker kunt u persoonlijke gegevens voor een binnenste kaart maken. Met de Azure Maps-API en de module over kaarten kunt u interactieve en dynamische kaart webtoepassingen voor het binnenste Web ontwikkelen. Op dit moment is de Maker alleen beschikbaar in de Verenigde Staten met de prijs categorie S1.

In dit artikel worden de stappen beschreven voor het maken en verwijderen van een Creator-resource in een Azure Maps-account.

## <a name="create-creator-preview-resource"></a>Resource Maker maken (preview)

1. Meld u aan bij [Azure Portal](https://portal.azure.com)

2. Selecteer uw Azure Maps-account. Als u uw Azure Maps-account niet kunt zien onder de **recente resources**, gaat u naar het menu Azure Portal. Selecteer **Alle resources**. Zoek en selecteer uw Azure Maps-account.

    ![Start pagina van Azure Maps Portal](./media/how-to-manage-creator/select-maps-account.png)

3. Als u zich op de pagina Azure Maps-account bevindt, gaat u naar de optie **overzicht** onder **Maker**. Selecteer  **maken**  om een Azure Maps Creator-resource te maken.

    ![Azure Maps Creator-pagina maken](./media/how-to-manage-creator/creator-blade-settings.png)

4. Voer de naam en locatie in voor de bron van de maker. Momenteel wordt de Maker alleen ondersteund in de Verenigde Staten. Selecteer **Controleren + maken**.

   ![Informatie pagina voor de account van de Creator invoeren](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Controleer uw instellingen en selecteer **maken**.

    ![Pagina instellingen van de Creator-account bevestigen](./media/how-to-manage-creator/creator-create-dialog.png)

6. Wanneer de implementatie is voltooid, ziet u een pagina met een geslaagde of fout melding.

   ![Pagina status van implementatie van resource](./media/how-to-manage-creator/creator-resource-created.png)

7. Selecteer **Ga naar resource**. Op de pagina Maker resource weergave ziet u de status van uw Creator-resource en de gekozen demografische regio.

    ![Pagina status van de maker](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Op de pagina Creator-resource kunt u teruggaan naar het Azure Maps account waarmee het deel uitmaakt door Azure Maps account te selecteren.

## <a name="delete-creator-preview-resource"></a>Bron van Creator (preview) verwijderen

Als u de Creator-resource wilt verwijderen, gaat u naar uw Azure Maps-account. Selecteer **overzicht** onder **Maker**. Selecteer de knop **Verwijderen**.

>[!WARNING]
>Wanneer u de maker-resource van uw Azure Maps-account verwijdert, verwijdert u ook de gegevens sets, tilesets en functie statesets die zijn gemaakt met behulp van Creator Services.

![De pagina Maker met de knop verwijderen](./media/how-to-manage-creator/creator-delete.png)

Selecteer de knop **verwijderen** en typ de naam van de maker om de verwijdering te bevestigen. Zodra de resource is verwijderd, ziet u een bevestigings pagina, zoals in de onderstaande afbeelding:

![Pagina Maker met bevestiging van verwijderen](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>Verificatie

Creator (preview) neemt de instellingen van Azure Maps Access Control (IAM) over. Alle API-aanroepen voor gegevens toegang moeten met verificatie-en autorisatie regels worden verzonden.

De gebruiks gegevens van de maker zijn opgenomen in uw Azure Maps-gebruiks diagrammen en het activiteiten logboek.  Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie.

## <a name="access-to-creator-services"></a>Toegang tot Creator-services

Creator-Services (preview) en services die gebruikmaken van gegevens die worden gehost in Creator (bijvoorbeeld service renderen), zijn toegankelijk via een geografische URL. De geografische URL wordt bepaald door de locatie die is geselecteerd tijdens het maken. Als er bijvoorbeeld een maker is gemaakt op de geografische locatie Verenigde Staten, moeten alle aanroepen naar de conversie service worden ingediend bij `us.atlas.microsoft.com/conversion/convert` .

Daarnaast moeten alle gegevens die in de Maker worden geïmporteerd, worden geüpload naar dezelfde geografische locatie als de maker-resource. Als de maker bijvoorbeeld is ingericht in de Verenigde Staten, moeten alle onbewerkte gegevens worden geüpload via `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Volgende stappen

Inleiding tot Creator-Services (preview) voor een binnenste toewijzing:

> [!div class="nextstepaction"]
> [Gegevens uploaden](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Gegevens conversie](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Gegevensset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tegelset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Functie status ingesteld](creator-indoor-maps.md#feature-statesets)

Meer informatie over het gebruik van de Creator-Services (preview) voor het weer geven van binnenste kaarten in uw toepassing:

> [!div class="nextstepaction"]
> [Zelf studie Azure Maps Maker](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamische opmaak van kaarten in een binnenshuis](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [De module Indoor Maps gebruiken](how-to-use-indoor-module.md)