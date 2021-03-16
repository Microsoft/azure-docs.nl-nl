---
title: 'Quickstart: sms-berichten verzenden in Azure Logic Apps met behulp van Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: In deze quickstart leert u hoe i sms-berichten kunt verzenden in Azure Logic Apps-werkstromen met behulp van de Azure Communication Services-connector.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 11cce223a0802c20c5fc144eed681327f989a949
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488377"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Quickstart: Sms-berichten verzenden in Azure Logic Apps met Azure Communication Services

Met de [Azure Communication Services SMS](../../overview.md)-connector en [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) kunt u geautomatiseerde werkstromen of *logische apps* maken die sms-berichten kunnen verzenden. In deze quickstart ziet u hoe u automatisch sms-berichten kunt verzenden als reactie op een triggergebeurtenis, wat de eerste stap is in een logische-app-werkstroom. Een triggergebeurtenis kan een binnenkomend e-mail bericht zijn, een terugkeerpatroon, een [Azure Event Grid](../../../event-grid/overview.md)-resourcegebeurtenis of een andere [trigger die door Azure Logic Apps wordt ondersteund](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Schermopname van de Azure-portal waarin de Logic App Designer is geopend met een voorbeeld van een logische app die de actie Sms verzenden gebruikt voor de Azure Communication Services-connector.":::

Hoewel deze quickstart gericht is op het gebruiken van de connector om te reageren op een trigger, kunt u de connector ook gebruiken om te reageren op andere acties, wat de stappen zijn die in een werkstroom volgen op de trigger. Als u niet bekend bent met Logic Apps, lees dan [Wat is Azure Logic Apps?](../../../logic-apps/logic-apps-overview.md) voordat u aan de slag gaat.

> [!NOTE]
> Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement, of [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Een actieve Azure Communication Services-resource, of [een Communication Services-resource maken](../create-communication-resource.md).

- Een actieve Logic Apps-resource (logische app) of [een logische app maken die leeg is afgezien van de trigger die u wilt gebruiken](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Momenteel biedt de Azure Communication Services SMS-connector alleen acties, dus moet uw logische app minimaal een trigger bevatten.

  In deze quickstart wordt gebruikgemaakt van de trigger **Wanneer een nieuw e-mailbericht binnenkomt**, die beschikbaar is bij de [Office 365 Outlook-connector](/connectors/office365/).

- Een telefoonnummer waarop u sms-berichten kunt ontvangen, of [een telefoonnummer aanvragen](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>Een sms-actie toevoegen

Om de actie **Sms verzenden** toe te voegen als een nieuwe stap in uw werkstroom met behulp van de Azure Communication Services SMS-connector, volgt u deze stappen in de [Azure-portal](https://portal.azure.com) terwijl de werkstroom van uw logische app geopend is in Logic App Designer:

1. Selecteer in de ontwerper **Nieuwe stap** onder de stap waar u de nieuwe actie wilt toevoegen. Als u de nieuwe actie tussen stappen wilt toevoegen, plaatst u de muisaanwijzer op de pijl tussen die stappen, selecteert u het plusteken ( **+** ) en selecteert u **Een actie toevoegen**.

1. Voer in het zoekvak **Kies een bewerking** `Azure Communication Services` in. Selecteer **Sms verzenden** in de lijst met acties.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Schermopname van de Logic app Designer en de Azure Communication Services-connector met de actie Sms verzenden geselecteerd.":::

1. Maak nu een verbinding met uw Communication Services-resource.

   1. Geef een naam op voor de verbinding.

   1. Selecteer uw Azure Communication Services-resource.

   1. Selecteer **Maken**.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Schermopname van de configuratie van de actie Sms verzenden met voorbeeldgegevens.":::

1. Geef bij de actie **Sms verzenden** de volgende gegevens op: 

   * De bron- en doeltelefoonnummers. Voor testdoeleinden kunt u uw eigen telefoonnummer gebruiken als doeltelefoonnummer.

   * De inhoud van het bericht dat u wilt verzenden, bijvoorbeeld 'Groeten van Logic Apps!'.

   Hier is een **Sms verzenden**-actie met voorbeeldgegevens:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Schermopname van de actie Sms verzenden met voorbeeldgegevens.":::

1. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

Voer vervolgens de logische app uit om deze te testen.

## <a name="test-your-logic-app"></a>Uw logische app testen

Selecteer **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren. U kunt ook wachten tot de logische app wordt getriggerd. In beide gevallen zou de logische app een sms-bericht moeten verzenden naar het opgegeven doeltelefoonnummer. Lees [Uw logische app uitvoeren](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app) voor meer informatie over het uitvoeren van uw logische app

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder een Communication Services-abonnement door de resource of resourcegroep van Communication Services te verwijderen. Als u de resourcegroep verwijdert, worden ook alle andere resources in die groep verwijderd. Lees [ Communication Services-resources opschonen](../create-communication-resource.md#clean-up-resources) voor mee informatie.

Als u de werkstroom van uw logische app en gerelateerde resources wilt opschonen, raadpleegt u [Logic Apps-resources opschonen](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u sms-berichten kunt verzenden met behulp van Azure Logic Apps en Azure Communication Services. Ga verder met Abonneren op sms-gebeurtenissen voor meer informatie:

> [!div class="nextstepaction"]
> [Abonneren op sms-gebeurtenissen](./handle-sms-events.md)

Raadpleeg de volgende artikelen voor meer informatie over sms in Azure Communication Services:

- [Sms-concepten](../../concepts/telephony-sms/concepts.md)
- [Telefoon nummer typen](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
