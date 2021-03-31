---
title: Verbinding maken met Twilio vanuit Azure Logic Apps
description: Automatiseer taken en werk stromen die algemene SMS-, MMS-en IP-berichten beheren via uw Twilio-account met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d144960972f5a1b45e88cc3a0ea015925cae3b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288137"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Berichten in Twilio beheren met Azure Logic Apps

Met Azure Logic Apps en de Twilio-connector kunt u geautomatiseerde taken en werk stromen maken waarmee berichten worden opgehaald, verzonden en weer geven in Twilio, waaronder algemene SMS-, MMS-en IP-berichten. U kunt deze acties gebruiken om taken uit te voeren met uw Twilio-account. U kunt ook andere acties uitvoeren met de uitvoer van Twilio-acties. Als er bijvoorbeeld een nieuw bericht binnenkomt, kunt u de inhoud van het bericht verzenden met de connector voor toegestane vertraging. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Van [Twilio](https://www.twilio.com/): 

  * Uw Twilio-account-ID en [verificatie token](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), die u op uw Twilio-dash board kunt vinden

    Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw Twilio-account vanuit uw logische app. 
    Als u een Twilio-proef account gebruikt, kunt u alleen SMS-berichten verzenden naar *geverifieerde* telefoon nummers.

  * Een geverifieerd Twilio-telefoon nummer waarmee SMS-berichten kunnen worden verzonden

  * Een geverifieerd Twilio-telefoon nummer dat SMS-berichten kan ontvangen

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw Twilio-account. Als u een Twilio-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-twilio"></a>Verbinding maken met Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

     * Kies **nieuwe stap** onder de laatste stap waarin u een actie wilt toevoegen. 

       -of-

     * Als u een actie wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
     Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.
     
       Voer in het zoekvak ' twilio ' in als uw filter. 
       Selecteer in de lijst acties de gewenste actie.

1. Geef de benodigde gegevens voor de verbinding op en kies vervolgens **maken**:

   * De naam die u voor de verbinding wilt gebruiken
   * Uw Twilio-account-ID 
   * Uw Twilio-toegangs token (verificatie)

1. Geef de benodigde gegevens voor de geselecteerde actie op en blijf door gaan met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/twilio/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
