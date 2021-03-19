---
title: XML-berichten en platte bestanden
description: XML-berichten in Azure Logic Apps verwerken, valideren en transformeren met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792146"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-berichten en platte bestanden in Azure Logic Apps met Enterprise Integration Pack

In [Azure Logic apps](logic-apps-overview.md)kunt u XML-berichten verwerken die u verzendt en ontvangt met behulp van de Enterprise Integration Pack. Als u BizTalk Server hebt gebruikt, biedt de Enterprise Integration Pack vergelijk bare mogelijkheden voor het transformeren en valideren van berichten, het werken met platte bestanden en zelfs XPath gebruiken om specifieke eigenschappen van een bericht te verrijken of uit te pakken. Als u geen ervaring hebt met deze ruimte, kunt u met deze functies de berichten in de werk stroom van de logische app verwerken. Als u bijvoorbeeld een Business-to-business-scenario (B2B) hebt en met specifieke XML-schema's werkt, kunt u de Enterprise Integration Pack gebruiken om de manier waarop uw bedrijf deze berichten verwerkt, uit te breiden.

Het Enterprise Integration Pack bevat bijvoorbeeld de volgende mogelijkheden:

* [XML-validatie](logic-apps-enterprise-integration-xml-validation.md): een binnenkomend of uitgaand XML-bericht valideren op basis van een specifiek schema.

* [XML-trans formatie](logic-apps-enterprise-integration-transform.md): een XML-bericht converteren of aanpassen op basis van uw vereisten of de vereisten van een partner met behulp van Maps.

* [Platte bestands codering en platte bestands codering](logic-apps-enterprise-integration-flatfile.md): een plat bestand coderen of decoderen.

  SAP accepteert en verzendt bijvoorbeeld IDOC-bestanden in platte bestands indeling. Veel integratie platforms maken XML-berichten, met inbegrip van Logic Apps. U kunt dus een logische app maken die gebruikmaakt van de plat file Encoder voor het converteren van XML-bestanden naar platte bestanden.

* [XPath](workflow-definition-language-functions-reference.md#xpath): een bericht verrijken en specifieke eigenschappen uit het bericht ophalen. U kunt vervolgens de geëxtraheerde eigenschappen gebruiken om het bericht door te sturen naar een bestemming of een tussenliggend eind punt.

## <a name="sample"></a>Voorbeeld

[Implementeer een volledig operationele logische app](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (github-voor beeld) met behulp van de XML-functies in azure Logic apps.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
