---
title: Verbinding maken met Outlook.com
description: Taken en werk stromen automatiseren waarmee e-mail, agenda's en contact personen in Outlook.com kunnen worden beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75707183"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>E-mail, agenda's en contact personen in Outlook.com beheren met Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [Outlook.com-connector](/connectors/outlook/)kunt u geautomatiseerde taken en werk stromen maken die uw @outlook.com @hotmail.com account beheren door logische apps te bouwen. U kunt bijvoorbeeld de volgende taken automatiseren:

* E-mail ontvangen, verzenden en beantwoorden.
* Plan vergaderingen in uw agenda.
* Contact personen toevoegen en bewerken.

U kunt elke trigger gebruiken om uw werk stroom te starten, bijvoorbeeld wanneer er een nieuwe e-mail binnenkomt, wanneer een agenda-item wordt bijgewerkt of wanneer er een gebeurtenis in een diff-service optreedt. U kunt acties gebruiken die reageren op de trigger gebeurtenis, bijvoorbeeld een e-mail bericht verzenden of een nieuwe agenda gebeurtenis maken.

> [!NOTE]
> @fabrikam.onmicrosoft.comGebruik de [Office 365 Outlook-Connector](../connectors/connectors-create-api-office365-outlook.md)om taken te automatiseren voor een micro soft-werk account, zoals.

## <a name="prerequisites"></a>Vereisten

* Een [Outlook.com-account](https://outlook.live.com/owa/)

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De logische app waartoe u toegang wilt krijgen tot uw Outlook.com-account. U moet een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)hebben om uw werk stroom te starten met een Outlook.com-trigger. Als u een Outlook.com-actie wilt toevoegen aan uw werk stroom, moet uw logische app al een trigger hebben.

## <a name="add-a-trigger"></a>Een trigger toevoegen

Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een gebeurtenis waarmee de werk stroom in uw logische app wordt gestart. Deze logische app maakt gebruik van een polling-trigger waarmee wordt gecontroleerd op nieuwe e-mail in uw e-mail account, op basis van de opgegeven interval en frequentie.

1. Open in de [Azure Portal](https://portal.azure.com)uw lege logische app in de ontwerp functie voor logische apps.

1. Voer in het zoekvak ' outlook.com ' in als uw filter. Voor dit voor beeld selecteert u **wanneer er een nieuwe e-mail binnenkomt**.

1. Als u wordt gevraagd om u aan te melden, geeft u uw Outlook.com-referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding al bestaat, geeft u de informatie voor de trigger eigenschappen op:

1. Stel in de trigger de **frequentie** -en **interval** waarden in.

   Als u bijvoorbeeld wilt dat de trigger om de 15 minuten vraagt, stelt u de **frequentie** in op **minuut** en stelt u het **interval** in op **15**.

1. Selecteer op de werk balk ontwerpen de optie **Opslaan**, waarmee u uw logische app opslaat.

Voeg een andere actie toe om te reageren op de trigger. U kunt bijvoorbeeld de Twilio-bericht actie voor **verzenden** toevoegen, waarmee een tekst wordt verzonden wanneer een e-mail binnenkomt.

## <a name="add-an-action"></a>Een actie toevoegen

Een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een bewerking die door de werk stroom in uw logische app wordt uitgevoerd. Dit voor beeld van een logische app verzendt een e-mail bericht van uw Outlook.com-account. U kunt de uitvoer van een andere trigger gebruiken om de actie te vullen. Stel bijvoorbeeld dat uw logische app gebruikmaakt van het Sales Force **Wanneer een object wordt gemaakt** . U kunt de actie Outlook.com **een E-mail verzenden** toevoegen en de uitvoer van de Sales Force-trigger in het e-mail bericht gebruiken.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap** om een actie toe te voegen als de laatste stap in uw werk stroom. 

   Als u een actie tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen deze stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' outlook.com ' in als uw filter. Selecteer voor dit voor beeld **een E-mail verzenden**. 

1. Als u wordt gevraagd om u aan te melden, geeft u uw Outlook.com-referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding al bestaat, geeft u de informatie op voor de actie-eigenschappen.

1. Selecteer op de werk balk ontwerpen de optie **Opslaan**, waarmee u uw logische app opslaat.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/outlook/)voor technische details, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
