---
title: Een v2 Azure IoT Central-toepassing migreren naar v3 | Microsoft Docs
description: Als beheerder leert u hoe u uw v2 Azure IoT Central-toepassing kunt migreren naar v3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702722"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Uw v2 IoT Central-toepassing migreren naar v3

*Dit artikel is van toepassing op beheerders.*

Wanneer u op dit moment een nieuwe IoT Central toepassing maakt, is dit een v3-toepassing. Als u eerder een toepassing hebt gemaakt, kan deze afhankelijk van het moment waarop u deze hebt gemaakt, v2 zijn. In dit artikel wordt beschreven hoe u een v2 naar een v3-toepassing migreert om ervoor te zorgen dat u de nieuwste IoT Central-functies gebruikt.

Zie [over uw toepassing](howto-get-app-info.md)voor meer informatie over het identificeren van de versie van een IOT Central toepassing.

De stappen voor het migreren van een toepassing van v2 naar v3 zijn:

1. Maak een nieuwe v3-toepassing vanuit de v2-toepassing.
1. Configureer de V3-toepassing.
1. Verwijderen naar v2-toepassing.

## <a name="create-a-new-v3-application"></a>Een nieuwe v3-toepassing maken

Gebruik de migratie wizard om een nieuwe v3-toepassing te maken.

IoT Central biedt geen ondersteuning voor het migreren naar een bestaande v3-toepassing. Als u bestaande apparaten automatisch wilt verplaatsen, gebruikt u de migratie wizard om uw v3-toepassing te maken.

De migratie wizard:

- Hiermee maakt u een nieuwe v3-toepassing.
- Controleert uw Apparaatinstellingen op compatibiliteit met v3.
- Kopieert alle sjablonen van uw apparaten naar de nieuwe v3-toepassing.
- Kopieert alle gebruikers en roltoewijzingen naar de nieuwe v3-toepassing.

> [!NOTE]
> Om ervoor te zorgen dat het apparaat compatibel is met v3, worden primitieve typen in de apparaatprofiel object eigenschappen. U hoeft geen wijzigingen aan te brengen in uw apparaatcode.

U moet een beheerder zijn om een toepassing te migreren naar v3.

1. Selecteer in het menu **beheer** de optie **migreren naar een v3-toepassing**:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Scherm opname van de wizard toepassings migratie":::

1. Voer een nieuwe **toepassings naam** in en wijzig desgewenst de automatisch gegenereerde  **URL**. De URL mag niet hetzelfde zijn als de URL van uw huidige v2-toepassing. U kunt de URL echter later wijzigen nadat u uw v2-toepassing hebt verwijderd.

1. Selecteer **een nieuwe v3-app maken**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Scherm opname van de opties in de wizard toepassings migratie":::

    Afhankelijk van het aantal en de complexiteit van uw Apparaatinstellingen kan het enkele minuten duren voordat dit proces is voltooid.

    > [!Warning]
    > Het maken van uw v3-toepassing mislukt als een sjabloon voor een apparaat velden bevat die beginnen met een getal of een van de volgende tekens bevatten:,,,,,,,,,,, `+` `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` , `\` . In het DTDL-sjabloon schema dat door v3-toepassingen wordt gebruikt, zijn deze tekens niet toegestaan. Werk de sjabloon voor het apparaat bij om dit probleem op te lossen voordat u migreert naar v3.

1. Wanneer uw nieuwe v3-app gereed is, selecteert u **de nieuwe app openen** om deze te openen.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Scherm opname van de wizard toepassings migratie na de migratie van de toepassing":::

## <a name="configure-the-v3-application"></a>De V3-toepassing configureren

Nadat de nieuwe v3-toepassing is gemaakt, brengt u eventuele configuratie wijzigingen aan voordat u uw apparaten naar de V3-toepassing verplaatst vanuit de v2-toepassing.

> [!TIP]
> Neem even de tijd om [vertrouwd te raken met v3](overview-iot-central-tour.md#navigate-your-application) , aangezien er een aantal verschillen is met de vorige versie.

Hier volgen enkele aanbevolen configuratie stappen die u moet overwegen:

- [Dash boards configureren](howto-add-tiles-to-your-dashboard.md)
- [Gegevensexport configureren](howto-export-data.md)
- [Regels en acties configureren](quick-configure-rules.md)
- [De gebruikers interface van de toepassing aanpassen](howto-customize-ui.md)

Wanneer uw v3-toepassing is geconfigureerd om te voldoen aan uw behoeften, bent u klaar om uw apparaten te verplaatsen vanuit uw v2-toepassing naar uw v3-toepassing.

## <a name="move-your-devices-to-the-v3-application"></a>Uw apparaten naar de V3-toepassing verplaatsen

Wanneer deze stap is voltooid, communiceren al uw apparaten alleen met uw nieuwe v3-toepassing.

> [!IMPORTANT]
> Voordat u uw apparaten naar uw v3-toepassing verplaatst, moet u alle apparaten verwijderen die u in de V3-toepassing hebt gemaakt.

Met deze stap worden al uw bestaande apparaten verplaatst naar de nieuwe v3-toepassing. De gegevens van uw apparaat worden niet gekopieerd.

Selecteer **alle apparaten verplaatsen** om uw apparaten te verplaatsen. Het kan enkele minuten duren voordat deze stap is voltooid.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Scherm opname van de wizard toepassings migratie met de optie apparaten verplaatsen":::

Nadat de verplaatsing is voltooid, start u alle apparaten opnieuw op om ervoor te zorgen dat ze verbinding maken met de nieuwe v3-toepassing.

> [!WARNING]
> Verwijder uw v3-toepassing niet omdat uw v2-toepassing nu niet meer werkt.

## <a name="delete-your-old-v2-application"></a>Uw oude v2-toepassing verwijderen

Nadat u hebt gecontroleerd of alles werkt zoals verwacht in uw nieuwe v3-toepassing, verwijdert u uw oude v2-toepassing. Met deze stap zorgt u ervoor dat er geen kosten in rekening worden gebracht voor een toepassing die u niet meer gebruikt.

> [!Note]
> Als u een toepassing wilt verwijderen, moet u gemachtigd zijn om resources te verwijderen in het Azure-abonnement dat u hebt gekozen tijdens het maken van de toepassing. Zie op [rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot uw Azure-abonnements resources](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

1. Selecteer in uw v2-toepassing het tabblad **beheer** in het menu
2. Selecteer **verwijderen** om uw IOT Central-toepassing permanent te verwijderen. Met deze optie worden alle gegevens die aan de toepassing zijn gekoppeld, permanent verwijderd.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw toepassing kunt migreren, kunt u de [Azure IOT Central-gebruikers interface](overview-iot-central-tour.md) bekijken om te zien wat er in v3 is gewijzigd.