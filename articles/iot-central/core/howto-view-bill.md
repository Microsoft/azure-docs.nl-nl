---
title: Uw factuur beheren en converteren van het gratis prijs plan in azure IoT Central toepassing | Microsoft Docs
description: Als beheerder leert u hoe u uw factuur kunt beheren en hoe u het gratis prijs plan kunt verplaatsen naar een Standard-prijs plan in uw Azure IoT Central-toepassing
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 50d0119b08d2c76a5f6111e485408ebcdace83c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549018"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Uw factuur beheren in een IoT Central-toepassing

In dit artikel wordt beschreven hoe u, als beheerder, uw Azure IoT Central-facturering kunt beheren. U kunt uw toepassing verplaatsen van het prijs plan gratis naar een Standard-prijs plan en u hebt ook een upgrade of downgrade voor uw prijs plan.

Als u toegang wilt krijgen tot de sectie **beheer** , moet u de rol *beheerder* hebben of een *aangepaste* gebruikersrol hebben waarmee u de facturering kunt weer geven. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan de rol **beheerder** .

## <a name="move-from-free-to-standard-pricing-plan"></a>Verplaatsen van gratis naar Standard-prijs plan

- Toepassingen die gebruikmaken van het gratis prijs plan zijn zeven dagen beschikbaar voordat ze verlopen. Om te voor komen dat gegevens verloren gaan, kunt u ze op elk gewenst moment naar een Standard-prijs plan verplaatsen voordat ze verlopen.
- Toepassingen die gebruikmaken van een Standard-prijs plan worden per apparaat in rekening gebracht, met de eerste twee gratis apparaten per toepassing.

Lees meer over prijzen op de [prijzenpagina van IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

In het gedeelte prijzen kunt u uw toepassing verplaatsen van het gratis naar een standaard prijs plan.

Voer de volgende stappen uit om dit self-service proces te volt ooien:

1. Ga naar de pagina met **prijzen** in de sectie **beheer** .

    :::image type="content" source="media/howto-view-bill/freetrialbilling.png" alt-text="Status van de proef versie":::

1. Selecteer **converteren naar een betaald abonnement**.

    :::image type="content" source="media/howto-view-bill/convert.png" alt-text="Proef versie converteren":::

1. Selecteer de juiste Azure Active Directory en vervolgens het Azure-abonnement dat u wilt gebruiken voor uw toepassing die gebruikmaakt van een betaald abonnement.

1. Nadat u **converteren** hebt geselecteerd, maakt uw toepassing nu gebruik van een betaald abonnement en wordt u gefactureerd.

> [!Note]
> Standaard wordt u geconverteerd naar een prijs plan van *Standard 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Het prijs plan van uw toepassing wijzigen

Toepassingen die gebruikmaken van een Standard-prijs plan worden per apparaat in rekening gebracht, met de eerste twee gratis apparaten per toepassing.

In het gedeelte prijzen kunt u uw Azure IoT-prijs plan op elk gewenst moment bijwerken of verlagen.

1. Ga naar de pagina met **prijzen** in de sectie **beheer** .

    :::image type="content" source="media/howto-view-bill/pricing.png" alt-text="Upgrade prijs plan":::

1. Selecteer het **plan** en selecteer vervolgens **Opslaan** om te upgraden of Down graden.

## <a name="view-your-bill"></a>Uw factuur bekijken

1. Selecteer de juiste Azure Active Directory en vervolgens het Azure-abonnement dat u wilt gebruiken voor uw toepassing die gebruikmaakt van een betaald abonnement.

1. Nadat u **converteren** hebt geselecteerd, maakt uw toepassing nu gebruik van een betaald abonnement en wordt u gefactureerd.

> [!Note]
> Standaard wordt u geconverteerd naar een prijs plan van *Standard 2* .

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw factuur kunt beheren in azure IoT Central-toepassing, is de voorgestelde volgende stap meer informatie over het aanpassen van de [gebruikers interface](howto-customize-ui.md) van de toepassing in azure IOT Central.