---
title: Key Vault bewaken met Azure Event Grid
description: Azure Event Grid gebruiken om u te abonneren op Key Vault gebeurtenissen
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90087433"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Key Vault bewaken met Azure Event Grid

Key Vault integratie met Event Grid kunnen gebruikers een melding ontvangen wanneer de status van een geheim dat is opgeslagen in de sleutel kluis is gewijzigd. Een status wijziging wordt gedefinieerd als een geheim dat bijna is verlopen (30 dagen vóór de verval datum), een geheim dat is verlopen of een geheim met een nieuwe versie die beschikbaar is. Meldingen voor alle drie de geheime typen (sleutel, certificaat en geheim) worden ondersteund.

Toepassingen kunnen reageren op deze gebeurtenissen met moderne serverloze architecturen, zonder complexe code of dure en inefficiënte polling Services. Gebeurtenissen worden gepusht via [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar gebeurtenis afhandelingen zoals [Azure functions](https://azure.microsoft.com/services/functions/), [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen webhook, en u betaalt alleen voor wat u gebruikt. Zie [Event grid prijzen](https://azure.microsoft.com/pricing/details/event-grid/)voor meer informatie over prijzen.

## <a name="key-vault-events-and-schemas"></a>Key Vault gebeurtenissen en schema's

Gebeurtenisraster maakt gebruik van [gebeurtenisabonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten te routen naar abonnees. Key Vault gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U kunt een Key Vault gebeurtenis identificeren omdat de eigenschap Event type met ' micro soft.-sleutel kluis ' begint.

Zie het [Key Vault-gebeurtenis schema](../../event-grid/event-schema-key-vault.md)voor meer informatie.

> [!WARNING]
> Meldings gebeurtenissen worden alleen geactiveerd op nieuwe versies van geheimen, sleutels en certificaten. u moet zich eerst abonneren op de gebeurtenis in uw sleutel kluis om deze meldingen te ontvangen.

## <a name="practices-for-consuming-events"></a>Procedures voor het gebruiken van gebeurtenissen

Toepassingen die Key Vault gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:

* Meerdere abonnementen kunnen worden geconfigureerd voor het door sturen van gebeurtenissen naar dezelfde gebeurtenis-handler. Het is belang rijk om te voor komen dat gebeurtenissen afkomstig zijn van een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de sleutel kluis die u verwacht.
* Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
* Velden negeren die u niet begrijpt.  Met deze procedure kunt u de nieuwe functies die in de toekomst kunnen worden toegevoegd, flexibeler maken.
* Gebruik het voor voegsel ' subject ' en achtervoegsel overeenkomsten om gebeurtenissen te beperken tot een bepaalde gebeurtenis.

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-overzicht](overview.md)
- [Overzicht van Azure Event Grid](../../event-grid/overview.md)
- Procedure: [Key Vault-gebeurtenissen omleiden naar Automation-Runbook](event-grid-tutorial.md).
- Procedure: [E-mail ontvangen wanneer een sleutelkluisgeheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Overzicht van Azure Automation](../../automation/index.yml)
