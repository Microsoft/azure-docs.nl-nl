---
ms.openlocfilehash: 82e475c97aa2c68a33a48b04fe3e45fb13728b88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629356"
---

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Een actieve Communication Services-resource.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Een telefoonnummer aanvragen

Als u wilt beginnen met het inrichten van nummers, gaat u naar uw Communication Services-resource in de [Azure-portal](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

### <a name="search-for-available-phone-numbers"></a>Zoeken naar beschik bare telefoon nummers

Ga naar de blade **Telefoonnummers** in het resourcemenu.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Schermopname van de telefoonnummerpagina van een Communicatie Services-resource.":::

Druk op de knop **Ophalen** om de wizard te starten. U wordt door de wizard op de blade **Telefoonnummers** door een reeks vragen begeleidt waarmee u het telefoonnummer kunt kiezen dat het beste bij uw scenario past. 

U moet eerst bij het **land of de regio** kiezen waar u het telefoonnummer wilt inrichten. Nadat u een land/regio hebt geselecteerd, moet u de **Use-case** selecteren die het beste bij uw behoeften past. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Schermopname van de weergave voor het aanvragen van telefoonnummers.":::

### <a name="select-your-phone-number-features"></a>Telefoonnummerfuncties selecteren

Het configureren van uw telefoonnummer is onderverdeeld in twee stappen: 

1. De selectie van het [nummertype](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. De selectie van de [aantal mogelijkheden](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

U kunt kiezen uit twee typen telefoonnummers: **Geografisch** en **Gratis**. Wanneer u een type nummer hebt geselecteerd, kunt u de functie kiezen.

In dit voorbeeld is een nummer van het type **Gratis** geselecteerd met de functies **Uitgaande gesprekken** en **Uitgaande en inkomende sms-berichten**.

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Schermopname van de weergave voor selectie van functies.":::

Klik hier op de knop **Volgende: nummers** onder aan de pagina om de telefoonnummers aan te passen die u wilt inrichten.

### <a name="customizing-phone-numbers"></a>Telefoonnummers aanpassen

Op de pagina **Nummers** past u de telefoonnummers aan die u wilt inrichten.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Schermopname van de pagina voor het selecteren van nummers.":::

> [!NOTE]
> In deze quickstart wordt de stroom voor het aanpassen van het nummertype **Gratis** weergegeven. De werkwijze kan enigszins afwijken als u het nummertype **Geografisch** hebt gekozen, maar het eindresultaat is hetzelfde.

Kies het **Netnummer** in de lijst met beschikbare netnummers, voer het aantal nummers in dat u wilt inrichten en klik vervolgens op **Zoeken** om nummers te vinden die aan uw selectiecriteria voldoen. De telefoonnummers die aan uw criteria voldoen, worden samen met de kosten per maand weergegeven.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Schermopname van de pagina voor het selecteren van nummers, met gereserveerde nummers.":::

> [!NOTE]
> De beschikbaarheid is afhankelijk van het nummertype, de locatie en de functies die u hebt geselecteerd.
> Nummers worden korte tijd gereserveerd voordat de transactie verloopt. Als de transactie verloopt, moet u de nummers opnieuw selecteren.

Als u het aankoopoverzicht wilt bekijken en uw bestelling wilt plaatsen, klikt u op de knop **Volgende: overzicht** onder aan de pagina.

### <a name="purchase-phone-numbers"></a>Telefoon nummers kopen

Op de overzichtspagina worden het nummertype, de kenmerken, de telefoonnummers en de totale maandelijkse kosten voor het inrichten van de telefoonnummers weergegeven.

> [!NOTE]
> De weergegeven prijzen zijn de **maandelijks terugkerende kosten** die aan u worden berekend voor het leasen van het geselecteerde telefoonnummer. Niet inbegrepen in deze weergave zijn de **kosten voor betalen per gebruik**, die u maakt voor binnenkomende of uitgaande oproepen. De prijslijsten zijn [hier beschikbaar](../../../concepts/pricing.md). Deze kosten zijn afhankelijk van het nummertype en van welke nummers u belt. Zo kan de prijs per minuut voor een gesprek van een regionaal nummer in Seattle naar een regionaal nummer in New York erg verschillen van de gesprekskosten voor een gesprek van datzelfde nummer in Seattle naar een mobiel nummer in het Verenigd Koninkrijk.

Klik ten slotte ter bevestiging op **Bestelling plaatsen** onder aan de pagina.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Schermopname van de overzichtspagina met het nummertype, de kenmerken, de telefoonnummers en de totale maandelijkse kosten.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Uw telefoonnummers zoeken in de Azure-portal

Ga naar de Azure Communication Services-resource in de [Azure-portal](https://portal.azure.com):

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Schermopname van de hoofdpagina van een Communication Services-resource.":::

Selecteer de blade Telefoonnummers in het menu om uw telefoonnummers te beheren.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Schermopname van de telefoonnummerpagina van een Communication Services-resource.":::

> [!NOTE]
> Het kan enkele minuten duren voordat de ingerichte nummers op deze pagina worden weergegeven.


### <a name="update-phone-number-capabilities"></a>Mogelijkheden voor telefoon nummer bijwerken

Op de pagina **Nummers** kunt u een telefoonnummer selecteren dat u wilt configureren.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Schermopname van de pagina met updatefuncties.":::

Selecteer de functies uit de beschikbare opties en klik op **Bevestigen** om de selectie toe te passen.

### <a name="release-phone-number"></a>Telefoon nummer van release

Op de pagina **getallen** kunt u telefoon nummers vrijgeven.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Scherm afbeelding van de pagina met telefoon nummers voor de release.":::

Selecteer het telefoon nummer dat u wilt vrijgeven en klik vervolgens op de knop **release** .