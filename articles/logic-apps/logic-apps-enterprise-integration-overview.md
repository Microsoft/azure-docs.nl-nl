---
title: B2B-bedrijfsintegratie
description: Informatie over het bouwen van geautomatiseerde B2B-werkstromen voor bedrijfsintegratie met behulp van Azure Logic Apps en Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: adf15904d4490647074d6e2b46ac7e9e871dd388
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86536399"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-bedrijfsintegratieoplossingen met Azure Logic Apps en Enterprise Integration Pack

Voor Business-to-Business-oplossingen (B2B) en naadloze communicatie tussen organisaties kunt u geautomatiseerde, schaalbare bedrijfsintegratiewerkstromen maken met behulp van het Enterprise Integration Pack (EIP) met [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Hoewel organisaties verschillende protocollen en indelingen gebruiken, kunnen ze elektronisch berichten uitwisselen. Het EIP transformeert verschillende indelingen naar één indeling die de systemen van uw organisatie kunnen verwerken en ondersteunt standaardprotocollen binnen de industrie, waaronder [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) en [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). U kunt ook de beveiliging van berichten verbeteren door versleuteling en digitale handtekeningen te gebruiken. Het EIP ondersteunt de volgende [connectors voor bedrijfsintegratie](../connectors/apis-list.md#integration-account-connectors) en industriestandaarden:

* Electronic Data Interchange (EDI)
* Enterprise Application Integration (EAI)

Misschien bent u bekend met Microsoft BizTalk Server of Azure BizTalk Services; het EIP volgt vergelijkbare concepten, waardoor de functies gebruiksvriendelijk zijn. Maar een belangrijk verschil is dat het EIP qua architectuur is gebaseerd op integratieaccounts om de opslag en het beheer van artefacten die worden gebruikt in B2B-communicatie te vereenvoudigen. Deze accounts zijn cloudcontainers waarin al uw artefacten, zoals partners, overeenkomsten, schema's, kaarten en certificaten, worden opgeslagen. 

## <a name="why-use-the-enterprise-integration-pack"></a>Voordelen van het Enterprise Integration Pack

* Met het EIP kunt u al uw artefacten opslaan op één plek: uw integratieaccount.

* U kunt B2B-werkstromen bouwen en integreren met SaaS-apps (software as a service) van derden, on-premises apps en aangepaste apps met behulp van Azure Logic Apps en connectors.

* U kunt aangepaste code voor uw logische apps maken met Azure-functies.

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

Voordat u werkstromen voor logische B2B-apps kunt bouwen met het EIP, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) met de artefacten die u wilt gebruiken

* Als u kaarten en schema's wilt maken, kunt u de [Microsoft Azure Logic Apps Enterprise Integration Tools voor Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) en Visual Studio 2015 gebruiken.

Nadat u een integratieaccount hebt gemaakt en uw artefacten hebt toegevoegd, kunt u B2B-werkstromen bouwen met deze artefacten door een logische app te maken in de Azure-portal. Als u geen ervaring hebt met logische apps, kunt u [een basic logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u met deze artefacten wilt werken, moet u eerst uw integratieaccount aan uw logische app koppelen. Daarna heeft uw logische toegang tot uw integratieaccount. U kunt ook logische apps maken, beheren en implementeren met behulp van Visual Studio of [PowerShell](/powershell/module/az.logicapp).

Dit zijn algemene stappen om te beginnen met het bouwen van logische B2B-apps:

![Vereisten voor het maken van logische B2B-apps](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Nu uitproberen

[Een volledig operationele logische voorbeeld-app implementeren die AS2-berichten verzendt en ontvangt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Volgende stappen

* [Handelspartners maken](logic-apps-enterprise-integration-partners.md)
* [Overeenkomsten maken](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Schema's toevoegen](logic-apps-enterprise-integration-schemas.md)
* [Kaarten toevoegen](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migreren vanuit BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
