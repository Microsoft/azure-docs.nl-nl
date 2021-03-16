---
title: Quickstart - SMS-gebeurtenissen verwerken
titleSuffix: An Azure Communication Services quickstart
description: Meer informatie over hoe u SMS-gebeurtenissen verwerkt met Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f47a8f1edb4afed41587eac3d539b5ad7cb8180f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488462"
---
# <a name="quickstart-handle-sms-events"></a>Quickstart: SMS-gebeurtenissen verwerken

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Aan de slag met Azure Communication Services met Azure Event Grid om SMS-gebeurtenissen van Communication Services te verwerken.

## <a name="about-azure-event-grid"></a>Over Azure Event Grid

[Azure Event Grid](../../../event-grid/overview.md) is een gebeurtenisservice in de cloud. In dit artikel leert u hoe u zich abonneert op gebeurtenissen voor [Communication Service-gebeurtenissen](../../concepts/event-handling.md) en hoe u een gebeurtenis activeert om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. In dit artikel sturen we de gebeurtenissen naar een web-app die de berichten verzamelt en weergeeft.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een Azure Communication Service-resource. Meer informatie vindt u in de quickstart [Een Azure Communication-resource maken](../create-communication-resource.md).
- Een telefoonnummer met sms-functionaliteit. [Een telefoonnummer aanvragen](./get-phone-number.md).

## <a name="setting-up"></a>Instellen

### <a name="enable-event-grid-resource-provider"></a>Event Grid-resourceprovicer inschakelen

Als u Event Grid in uw Azure-abonnement nog niet eerder hebt gebruikt, moet u mogelijk de Event Grid-resourceprovider registreren in de onderstaande stappen:

In Azure Portal:

1. Selecteer **Abonnementen** in het menu links.
2. Selecteer het abonnement dat u voor Event Grid gebruikt.
3. Selecteer onder **Instellingen** in het menu links de optie **Resourceproviders**.
4. Zoek **Microsoft.EventGrid**.
5. Als deze niet is geregistreerd, selecteert u **Registreren**.

Het kan even duren voordat de registratie is voltooid. Selecteer **Vernieuwen** om de status bij te werken. Wanneer de **Status****Geregistreerde** is, bent u klaar om verder te gaan.

### <a name="event-grid-viewer-deployment"></a>Event Grid Viewer-implementatie

Voor deze quickstart gebruiken we het [Azure Event Grid Viewer-voorbeeld](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) om bijna in realtime gebeurtenissen te bekijken. Zo krijgt de gebruiker ervaring met een realtime feed. Bovendien moet de payload voor elke gebeurtenis ook beschikbaar zijn voor inspectie.

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Abonneren op SMS-gebeurtenissen via webhooks

Navigeer in de portal naar de Azure Communication Services-resource die u hebt gemaakt. Selecteer in de Communication Service-resource **Gebeurtenissen** in het menu aan de linkerzijde van de pagina **Communication Services**.

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Schermopname met de selectie van het gebeurtenisabonnement binnen de gebeurtenispagina van een resource.":::

Druk op **Gebeurtenisabonnement toevoegen** om de wizard te openen.

Voer op de pagina **Gebeurtenisabonnement maken** een **naam** in voor het gebeurtenisabonnement.

U kunt zich op specifieke gebeurtenissen abonneren om Event Grid te laten weten welke SMS-gebeurtenissen u wilt volgen en waar die gebeurtenissen naartoe moeten worden gestuurd. Selecteer de gebeurtenissen waarvoor u zich wilt abonneren in het vervolgkeuzemenu. Voor SMS hebt u de optie `SMS Received` en `SMS Delivery Report Received`.

Als u wordt gevraagd om de naam van een **Systeemonderwerp** op te geven, kunt u een unieke tekenreeks opgeven. Dit veld heeft geen invloed op uw ervaring en wordt gebruikt voor interne telemetrie.

Bekijk de volledige lijst [door Azure Communication Services ondersteunde gebeurtenissen](../../concepts/event-handling.md).

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Schermafbeelding met het ontvangen SMS-bericht en SMS-rapport met ontvangen gebeurtenistypen.":::

Selecteer **Webhook** voor het **Eindpunttype**.

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Schermopname met het veld Eindpunttype dat wordt ingesteld op Webhook.":::

Klik voor **Eindpunt** op **Een eindpunt selecteren** en voer de URL van uw web-app in.

In dit geval gebruiken we de URL van het [Azure Event Grid Viewer-voorbeeld](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) dat we eerder in deze quickstart hebben ingesteld. De URL voor het voorbeeld heeft de volgende indeling: `https://{{site-name}}.azurewebsites.net/api/updates`

Selecteer vervolgens **Selectie bevestigen**.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Schermopname van de bevestiging van een Webhook-eindpunt.":::

## <a name="viewing-sms-events"></a>SMS-gebeurtenissen weergeven

### <a name="triggering-sms-events"></a>SMS-gebeurtenissen activeren

Om gebeurtenistriggers te bekijken, moeten we allereerst gebeurtenissen genereren.

- `SMS Received`-gebeurtenissen worden gegenereerd wanneer het Communication Services-telefoonnummer een sms-bericht ontvangt. Als u een gebeurtenis wilt activeren, stuurt u een bericht van uw telefoon naar het telefoonnummer dat is gekoppeld aan uw Communication Services-resource.
- `SMS Delivery Report Received`-gebeurtenissen worden gegenereerd wanneer u een sms naar een gebruikers stuurt via een Communication Services-telefoonnummer. Als u een gebeurtenis wilt activeren, moet u `Delivery Report` inschakelen in de opties van het [verstuurde sms-bericht](../telephony-sms/send.md). Probeer een bericht naar uw telefoon te sturen met `Delivery Report`. Voor het voltooien van deze actie worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Bekijk de volledige lijst [door Azure Communication Services ondersteunde gebeurtenissen](../../concepts/event-handling.md).

### <a name="receiving-sms-events"></a>SMS-gebeurtenissen ontvangen

Nadat u een van de bovenstaande acties hebt voltooid, ziet u dat de gebeurtenissen `SMS Received` en `SMS Delivery Report Received` naar uw eindpunt worden gestuurd. Deze gebeurtenissen worden weergegeven in het [Azure Event Grid Viewer-voorbeeld](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) dat we in het begin hebben ingesteld. U kunt op het oogpictogram naast de gebeurtenis klikken om de hele payload te bekijken. Gebeurtenissen zien er als volgt uit:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Schermopname van het Event Grid Schema voor een ontvangen SMS-gebeurtenis.":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Schermopname van het Event Grid Schema voor een gerapporteerde SMS-gebeurtenis.":::

Meer informatie over de [gebeurtenisschema's en andere gebeurtenisconcepten](../../concepts/event-handling.md).

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart leert u hoe u SMS-gebeurtenis gebruikt. U kunt SMS-berichten ontvangen door een Event Grid-abonnement te maken.

> [!div class="nextstepaction"]
> [SMS verzenden](../telephony-sms/send.md)

U wilt mogelijk ook:

 - [Meer informatie over de concepten van gebeurtenisverwerking](../../concepts/event-handling.md)
 - [Meer informatie over Event Grid](../../../event-grid/overview.md)
