---
title: Verbinding maken met toegestane vertraging van Azure Logic Apps
description: Taken en werk stromen automatiseren waarmee bestanden worden bewaakt en kanalen, groepen en berichten in uw toegestane account kunnen worden beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 75f4cea0e5cd2b0bf42c28cbd81e199115834e11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87283959"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>De toegestane vertraging controleren en beheren met Azure Logic Apps

Met Azure Logic Apps en de uitzonderings connector kunt u geautomatiseerde taken en werk stromen maken die uw toegestane vertragings bestanden controleren en uw toegestane vertragings kanalen, berichten, groepen enzovoort beheren, bijvoorbeeld:

* Controleren wanneer nieuwe bestanden worden gemaakt.
* Kanalen maken, weer geven en koppelen 
* Berichten posten.
* Maken van groepen en instellen niet Stores.

U kunt triggers gebruiken die antwoorden krijgen van uw toegestane account en de uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken om taken uit te voeren met uw toegestane vertragings account. U kunt ook andere acties uitvoeren op basis van de actie uitvoer van vertraging. Als er bijvoorbeeld een nieuw bestand wordt gemaakt, kunt u een e-mail verzenden met de Office 365 Outlook-Connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw [toegestane vertragings](https://slack.com/) account en gebruikers referenties

  Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw toegestane vertragings account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw toegestane vertragings account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een toegestane marge-trigger. Als u een actie voor een toegestane vertraging wilt gebruiken, start u uw logische app met een trigger, zoals een toegestane vertragings trigger of een andere trigger, zoals de trigger voor **terugkeer patroon** .

## <a name="connect-to-slack"></a>Verbinding maken met toegestane vertraging

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voer voor lege logische apps in het zoekvak ' toegestane vertraging ' in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers. 

   -of-

   Voor bestaande Logic apps, onder de laatste stap waar u een actie wilt toevoegen, kiest u **nieuwe stap**. 
   Voer in het zoekvak ' toegestane vertraging ' in als uw filter. 
   Selecteer in de lijst acties de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Als u wordt gevraagd om u aan te melden bij toegestane vertraging, meldt u zich aan bij uw werk ruimte voor vertraging. 

   ![Meld u aan bij de werk ruimte toegestane vertraging](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Machtig de toegang voor uw logische app.

   ![Toegang tot toegestane vertraging toestaan](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie. Voeg meer acties toe om door te gaan met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/slack/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)

