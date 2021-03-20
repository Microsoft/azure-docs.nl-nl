---
title: Verbinding maken met SendGrid vanuit Azure Logic Apps
description: Taken en werk stromen automatiseren waarmee e-mails worden verzonden en mailing lijsten worden beheerd in SendGrid met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87290433"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>E-mails verzenden en mailing lijsten beheren in SendGrid met behulp van Azure Logic Apps

Met Azure Logic Apps en de SendGrid-connector kunt u geautomatiseerde taken en werk stromen maken waarmee e-mails worden verzonden en uw ontvangers lijsten worden beheerd, bijvoorbeeld:

* E-mail verzenden.
* Ontvangers toevoegen aan lijsten.
* Wereld wijde onderdrukking ophalen, toevoegen en beheren.

U kunt SendGrid-acties in uw Logic apps gebruiken om deze taken uit te voeren. U kunt ook andere acties uitvoeren met de uitvoer van SendGrid-acties. 

Deze connector biedt alleen acties, dus als u uw logische app wilt starten, gebruikt u een afzonderlijke trigger, zoals een **terugkeer patroon** trigger. Als u bijvoorbeeld regel matig ontvangers toevoegt aan uw lijsten, kunt u via de Office 365 Outlook Connector of de Outlook.com-connector e-mail verzenden over ontvangers en lijsten.
Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een [SendGrid-account](https://www.sendgrid.com/) en een [SendGrid-API-sleutel](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Uw API-sleutel geeft uw logische app toestemming om een verbinding te maken en toegang te krijgen tot uw SendGrid-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw SendGrid-account. Als u een SendGrid-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-sendgrid"></a>Verbinding maken met SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Kies **nieuwe stap** onder de laatste stap waarin u een actie wilt toevoegen. 

     -of-

   * Als u een actie wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' sendgrid ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

1. Geef een naam op voor uw verbinding. 

1. Voer uw SendGrid-API-sleutel in en kies vervolgens **maken**.

1. Geef de benodigde gegevens voor de geselecteerde actie op en blijf door gaan met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/sendgrid/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
