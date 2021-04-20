---
title: Bewaking Key Vault met Azure Event Grid
description: Gebruik Azure Event Grid om u te abonneren op Key Vault gebeurtenissen
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 4fb6d57bb84f4a3b4c5c138be9306489191bfce8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753358"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Bewaking Key Vault met Azure Event Grid

Key Vault integratie met Event Grid kunnen gebruikers een melding ontvangen wanneer de status van een geheim dat is opgeslagen in de sleutelkluis is gewijzigd. Een statuswijziging wordt gedefinieerd als een geheim dat op het punt staat te verlopen (30 dagen vóór de vervaldatum), een geheim dat is verlopen of een geheim met een nieuwe versie. Meldingen voor alle drie de geheime typen (sleutel, certificaat en geheim) worden ondersteund.

Toepassingen kunnen reageren op deze gebeurtenissen met behulp van moderne serverloze architecturen, zonder complexe code of dure en inefficiënte pollingservices. Gebeurtenissen worden via een [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) naar gebeurtenis-handlers zoals [Azure Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps of](https://azure.microsoft.com/services/logic-apps/)zelfs naar uw eigen webhook, en u betaalt alleen voor wat u gebruikt. Zie prijzen voor Event Grid [informatie over prijzen.](https://azure.microsoft.com/pricing/details/event-grid/)

## <a name="key-vault-events-and-schemas"></a>Key Vault gebeurtenissen en schema's

Gebeurtenisraster maakt gebruik van [gebeurtenisabonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten te routen naar abonnees. Key Vault gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U kunt een Key Vault omdat de eigenschap eventType begint met 'Microsoft.KeyVault'.

Zie voor meer informatie het Key Vault [gebeurtenisschema](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Meldingsgebeurtenissen worden alleen geactiveerd voor nieuwe versies van geheimen, sleutels en certificaten. U moet zich eerst abonneren op de gebeurtenis in uw sleutelkluis om deze meldingen te ontvangen.

## <a name="practices-for-consuming-events"></a>Procedures voor het gebruiken van gebeurtenissen

Toepassingen die Key Vault verwerken, moeten een aantal aanbevolen procedures volgen:

* Er kunnen meerdere abonnementen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenis-handler te doorverlinken. Het is belangrijk om er niet van uit te gaan dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om te controleren of het afkomstig is van de sleutelkluis die u verwacht.
* Controleer op dezelfde manier of het eventType het type is dat u wilt verwerken en ga er niet van uit dat alle gebeurtenissen die u ontvangt de typen zijn die u verwacht.
* Negeer velden die u niet begrijpt.  Deze praktijk helpt u bestand te houden tegen nieuwe functies die in de toekomst kunnen worden toegevoegd.
* Gebruik het voorvoegsel 'subject' en het achtervoegsel matches om gebeurtenissen te beperken tot een bepaalde gebeurtenis.

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-overzicht](overview.md)
- [Overzicht van Azure Event Grid](../../event-grid/overview.md)
- How to: [Route Key Vault Events to Automation Runbook](event-grid-tutorial.md).
- Procedure: [E-mail ontvangen wanneer een sleutelkluisgeheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Overzicht van Azure Automation](../../automation/index.yml)
