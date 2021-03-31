---
title: Verbinding maken met SMTP vanuit Azure Logic Apps
description: Taken en werk stromen automatiseren waarmee e-mail via uw SMTP-account (Simple Mail Transfer Protocol) wordt verzonden met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283942"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>E-mail verzenden vanuit uw SMTP-account met Azure Logic Apps

Met Azure Logic Apps en de Simple Mail Transfer Protocol (SMTP)-connector kunt u geautomatiseerde taken en werk stromen maken waarmee e-mail berichten worden verzonden vanuit uw SMTP-account. U kunt ook andere acties uitvoeren met de uitvoer van SMTP-acties. Nadat uw SMTP bijvoorbeeld een e-mail heeft verzonden, kunt u uw team in de toegestane vertraging melden met de uitzonderings connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw SMTP-account en gebruikers referenties

  Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw SMTP-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw SMTP-account. Als u een SMTP-actie wilt gebruiken, start u uw logische app met een trigger, zoals een Sales Force-trigger, als u een Sales Force-account hebt.

  U kunt bijvoorbeeld uw logische app starten met de trigger **Wanneer een record wordt gemaakt** Sales Force. 
  Deze trigger wordt geactiveerd elke keer dat een nieuwe record, zoals een lead, wordt gemaakt in Sales Force. 
  U kunt deze trigger vervolgens volgen met de actie SMTP- **e-mail verzenden** . Op die manier verzendt de logische app een e-mail van uw SMTP-account over de nieuwe record wanneer de nieuwe record wordt gemaakt.

## <a name="connect-to-smtp"></a>Verbinding maken met SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies **nieuwe stap** onder de laatste stap waarin u een SMTP-actie wilt toevoegen. 

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' SMTP ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

1. Wanneer u hierom wordt gevraagd, geeft u de volgende verbindings gegevens op:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Verbindingsnaam** | Ja | Een naam voor de verbinding met de SMTP-server | 
   | **SMTP-server adres** | Ja | Het adres voor de SMTP-server | 
   | **Gebruikersnaam** | Ja | Uw gebruikers naam voor uw SMTP-account | 
   | **Wachtwoord** | Ja | Uw wacht woord voor uw SMTP-account | 
   | **SMTP-server poort** | Nee | Een specifieke poort op de SMTP-server die u wilt gebruiken | 
   | **SSL inschakelen?** | Nee | Schakel TLS/SSL-versleuteling in of uit. | 
   |||| 

1. Geef de benodigde gegevens op voor de geselecteerde actie. 

1. Sla de logische app op of ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/smtpconnector/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
