---
title: Verbinding maken met Sales Force vanuit Azure Logic Apps
description: Taken en werk stromen automatiseren waarmee Sales Force-records en-taken worden bewaakt, gemaakt en beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 9950951ab5189c8c7b72de78bca9465ec5f22748
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87290598"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Salesforce-bestanden bewaken, maken en beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de Sales Force-connector kunt u geautomatiseerde taken en werk stromen maken voor uw Sales Force-resources, zoals records, taken en objecten, bijvoorbeeld:

* Controleren wanneer records worden gemaakt of gewijzigd. 
* Taken en records maken, ophalen en beheren, inclusief acties voor invoegen, bijwerken en verwijderen.

U kunt Sales Force-triggers gebruiken die antwoorden krijgen van Sales Force en de uitvoer beschikbaar maken voor andere acties. U kunt acties in uw Logic apps gebruiken om taken uit te voeren met Sales Force-resources. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een [Sales Force-account](https://salesforce.com/)

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw Sales Force-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een Sales Force-trigger. Als u een Sales Force-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-salesforce"></a>Verbinding met Salesforce maken

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Voor lege Logic apps voert u in het zoekvak "Sales Force" in als uw filter. 
   Selecteer de gewenste trigger onder de lijst met triggers. 

     -of-

   * Kies **nieuwe stap** onder de stap waar u een actie wilt toevoegen voor bestaande Logic apps. In het zoekvak voert u "Sales Force" in als uw filter. Selecteer in de lijst acties de gewenste actie.

1. Als u wordt gevraagd om u aan te melden bij Sales Force, meldt u zich nu aan en verleent u toegang.

   Met uw referenties wordt uw logische app geautoriseerd om een verbinding met Sales Force te maken en toegang te krijgen tot uw gegevens.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/salesforce/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
