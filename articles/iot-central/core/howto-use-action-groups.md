---
title: Meerdere acties uitvoeren vanuit een Azure IoT Central-regel | Microsoft Docs
description: Meerdere acties uitvoeren vanuit één IoT Central regel en herbruikbare groepen met acties maken die u vanuit meerdere regels kunt uitvoeren.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 84a723023bf8614b837ba9783bae987403921c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585480"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Meerdere acties groeperen om uit te voeren vanuit een of meer regels

*Dit artikel is van toepassing op bouwers en beheerders.*

In azure IoT Central maakt u regels om acties uit te voeren wanneer aan een voor waarde wordt voldaan. Regels zijn gebaseerd op telemetrie van apparaten of gebeurtenissen. U kunt bijvoorbeeld een operator waarschuwen wanneer de Tempe ratuur van een apparaat een drempel waarde overschrijdt. In dit artikel wordt beschreven hoe u [Azure monitor](../../azure-monitor/overview.md) *actie groepen* kunt gebruiken om meerdere acties aan een IOT Central regel te koppelen. U kunt een actie groep koppelen aan meerdere regels. Een [actie groep](../../azure-monitor/alerts/action-groups.md) is een verzameling voor keuren voor meldingen die zijn gedefinieerd door de eigenaar van een Azure-abonnement.

## <a name="prerequisites"></a>Vereisten

- Een toepassing die is gemaakt met een Standard-prijs plan
- Een Azure-account en-abonnement om Azure Monitor actie groepen te maken en te beheren

## <a name="create-action-groups"></a>Actiegroepen maken

U kunt [actie groepen maken en beheren in de Azure Portal](../../azure-monitor/alerts/action-groups.md) of met een [Azure Resource Manager sjabloon](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md).

Een actie groep kan:

- Verzend meldingen zoals een e-mail, een SMS of een telefoon gesprek.
- Een actie uitvoeren zoals het aanroepen van een webhook.

De volgende scherm afbeelding toont een actie groep die e-mail-en SMS-meldingen verzendt en een webhook aanroept:

![Actiegroep](media/howto-use-action-groups/actiongroup.png)

Als u een actie groep wilt gebruiken in een IoT Central regel, moet de actie groep zich in hetzelfde Azure-abonnement bevallen als de IoT Central-toepassing.

## <a name="use-an-action-group"></a>Een actie groep gebruiken

Als u een actie groep wilt gebruiken in uw IoT Central-toepassing, maakt u eerst een regel. Wanneer u een actie aan de regel toevoegt, selecteert u **Azure monitor actie groepen**:

![Actie kiezen](media/howto-use-action-groups/chooseaction.png)

Kies een actie groep uit uw Azure-abonnement:

![Actie groep kiezen](media/howto-use-action-groups/chooseactiongroup.png)

Selecteer **Opslaan**. De actie groep wordt nu weer gegeven in de lijst met acties die moeten worden uitgevoerd wanneer de regel wordt geactiveerd:

![Opgeslagen actie groep](media/howto-use-action-groups/savedactiongroup.png)

De volgende tabel bevat een overzicht van de informatie die wordt verzonden naar de ondersteunde actie typen:

| Actietype | Uitvoerindeling |
| ----------- | -------------- |
| E-mail       | E-mail sjabloon standaard IoT Central |
| Sms         | Azure IoT Central-waarschuwing: $ {ApplicationName}-"$ {ruleNaam} ' geactiveerd op ' $ {DeviceName} ' op $ {triggerDate} $ {triggerTime} |
| Spraak       | Azure I. O. T Central alert: regel "$ {DeviceName}" geactiveerd op apparaat "$ {apparaatnaam}" op $ {triggerDate} $ {triggerTime}, in toepassing $ {ApplicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "gegevens": {[normale webhook Payload](howto-create-webhooks.md#payload)}} |

De volgende tekst is een voor beeld van een SMS-bericht van een actie groep:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u actie groepen met regels kunt gebruiken, is de voorgestelde volgende stap om te leren hoe u [uw apparaten beheert](howto-manage-devices.md).
