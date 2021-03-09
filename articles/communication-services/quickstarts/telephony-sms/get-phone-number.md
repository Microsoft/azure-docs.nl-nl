---
title: 'Quickstart: een telefoonnummer aanvragen bij Azure Communication Services'
description: Meer informatie over het kopen van een telefoonnummer bij Communication Services met behulp van de Azure-portal.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 4acc1cb93fb04ba190173fea05805a1a4b833ffc
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488763"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Quickstart: Een telefoonnummer aanvragen met behulp van de Azure-portal

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Ga aan de slag met Azure Communication Services om een telefoonnummer te kopen met behulp van de Azure-portal.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Een actieve Communication Services-resource.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Een telefoonnummer aanvragen

Als u wilt beginnen met het inrichten van nummers, gaat u naar uw Communication Services-resource in de [Azure-portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

### <a name="getting-new-phone-numbers"></a>Nieuwe telefoonnummers aanvragen

Ga naar de blade **Telefoonnummers** in het resourcemenu.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Schermopname van de telefoonnummerpagina van een Communicatie Services-resource.":::

Druk op de knop **Ophalen** om de wizard te starten. U wordt door de wizard op de blade **Telefoonnummers** door een reeks vragen begeleidt waarmee u het telefoonnummer kunt kiezen dat het beste bij uw scenario past.

U moet eerst bij het **land of de regio** kiezen waar u het telefoonnummer wilt inrichten. Nadat u een land/regio hebt geselecteerd, moet u de **Use-case** selecteren die het beste bij uw behoeften past.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Schermopname van de weergave voor het aanvragen van telefoonnummers.":::

### <a name="select-your-phone-number-features"></a>Telefoonnummerfuncties selecteren

Het configureren van uw telefoonnummer is onderverdeeld in twee stappen:

1. De selectie van het [nummertype](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. De selectie van de [nummerfuncties](../../concepts/telephony-sms/plan-solution.md#phone-number-features-in-azure-communication-services)

U kunt kiezen uit twee typen telefoonnummers: **Geografisch** en **Gratis**. Wanneer u een type nummer hebt geselecteerd, kunt u de functie kiezen.

In dit voorbeeld is een nummer van het type **Gratis** geselecteerd met de functies **Uitgaande gesprekken** en **Uitgaande en inkomende sms-berichten**.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Schermopname van de weergave voor selectie van functies.":::

Klik hier op de knop **Volgende: nummers** onder aan de pagina om de telefoonnummers aan te passen die u wilt inrichten.

### <a name="customizing-phone-numbers"></a>Telefoonnummers aanpassen

Op de pagina **Nummers** past u de telefoonnummers aan die u wilt inrichten.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Schermopname van de pagina voor het selecteren van nummers.":::

> [!NOTE]
> In deze quickstart wordt de stroom voor het aanpassen van het nummertype **Gratis** weergegeven. De werkwijze kan enigszins afwijken als u het nummertype **Geografisch** hebt gekozen, maar het eindresultaat is hetzelfde.

Kies het **Netnummer** in de lijst met beschikbare netnummers, voer het aantal nummers in dat u wilt inrichten en klik vervolgens op **Zoeken** om nummers te vinden die aan uw selectiecriteria voldoen. De telefoonnummers die aan uw criteria voldoen, worden samen met de kosten per maand weergegeven.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Schermopname van de pagina voor het selecteren van nummers, met gereserveerde nummers.":::

> [!NOTE]
> De beschikbaarheid is afhankelijk van het nummertype, de locatie en de functies die u hebt geselecteerd.
> Nummers worden korte tijd gereserveerd voordat de transactie verloopt. Als de transactie verloopt, moet u de nummers opnieuw selecteren.

Als u het aankoopoverzicht wilt bekijken en uw bestelling wilt plaatsen, klikt u op de knop **Volgende: overzicht** onder aan de pagina.

### <a name="place-order"></a>Bestelling plaatsen

Op de overzichtspagina worden het nummertype, de kenmerken, de telefoonnummers en de totale maandelijkse kosten voor het inrichten van de telefoonnummers weergegeven.

> [!NOTE]
> De weergegeven prijzen zijn de **maandelijks terugkerende kosten** die aan u worden berekend voor het leasen van het geselecteerde telefoonnummer. Niet inbegrepen in deze weergave zijn de **kosten voor betalen per gebruik**, die u maakt voor binnenkomende of uitgaande oproepen. De prijslijsten zijn [hier beschikbaar](../../concepts/pricing.md). Deze kosten zijn afhankelijk van het nummertype en van welke nummers u belt. Zo kan de prijs per minuut voor een gesprek van een regionaal nummer in Seattle naar een regionaal nummer in New York erg verschillen van de gesprekskosten voor een gesprek van datzelfde nummer in Seattle naar een mobiel nummer in het Verenigd Koninkrijk.

Klik ten slotte ter bevestiging op **Bestelling plaatsen** onder aan de pagina.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Schermopname van de overzichtspagina met het nummertype, de kenmerken, de telefoonnummers en de totale maandelijkse kosten.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Uw telefoonnummers zoeken in de Azure-portal

Ga naar de Azure Communication Services-resource in de [Azure-portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Schermopname van de hoofdpagina van een Communication Services-resource.":::

Selecteer de blade Telefoonnummers in het menu om uw telefoonnummers te beheren.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Schermopname van de telefoonnummerpagina van een Communication Services-resource.":::

> [!NOTE]
> Het kan enkele minuten duren voordat de ingerichte nummers op deze pagina worden weergegeven.


### <a name="customizing-phone-numbers"></a>Telefoonnummers aanpassen

Op de pagina **Nummers** kunt u een telefoonnummer selecteren dat u wilt configureren.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Schermopname van de pagina met updatefuncties.":::

Selecteer de functies uit de beschikbare opties en klik op **Bevestigen** om de selectie toe te passen.

## <a name="troubleshooting"></a>Problemen oplossen

Veelvoorkomende vragen en problemen:

- Het kopen van telefoon wordt alleen ondersteund in de VS. Als u telefoon nummers wilt kopen, moet u het volgende doen:
  - Het factuur adres van het gekoppelde Azure-abonnement bevindt zich in de Verenigde Staten. U kunt op dit moment geen resource verplaatsen naar een ander abonnement.
  - Uw communicatie Services-bron is ingericht op de Verenigde Staten locatie van gegevens. U kunt op dit moment geen resource naar een andere gegevens locatie verplaatsen.

- Wanneer een telefoonnummer wordt vrijgegeven, wordt het telefoonnummer pas vrijgegeven en kan het pas worden teruggekocht aan het einde van de factureringsperiode.

- Wanneer een Communication Services-resource wordt verwijderd, worden de telefoonnummers die aan die resource zijn gekoppeld, automatisch op hetzelfde moment vrijgegeven.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een telefoonnummer kopen
> * Uw telefoonnummer beheren
> * Een telefoonnummer vrijgeven

> [!div class="nextstepaction"]
> [Een sms verzenden](../telephony-sms/send.md)
> [Aan de slag met gesprekken](../voice-video-calling/getting-started-with-calling.md)
