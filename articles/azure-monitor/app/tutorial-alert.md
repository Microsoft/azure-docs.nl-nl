---
title: Waarschuwingen verzenden vanuit Azure Application Insights | Microsoft Docs
description: Zelfstudie over het verzenden van waarschuwingen als reactie op fouten in uw toepassing met behulp van Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 6b8cc951301ec315f758478eda1c0736882e66f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100627261"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Toepassingsstatus bewaken en waarschuwingen erover verzenden met Azure Application Insights

Met Azure Application Insights kunt u uw toepassing bewaken en waarschuwingen ontvangen wanneer deze niet beschikbaar is, wanneer er fouten optreden, of wanneer er sprake is van prestatieproblemen.  In deze zelfstudie wordt u begeleid bij het maken van tests voor het continu controleren van de beschikbaarheid van uw toepassing.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Beschikbaarheidstest maken voor het continu controleren van de reacties van de toepassing
> * E-mail verzenden naar beheerders wanneer zich een probleem voordoet

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

Een [Application Insights-resource](../app/create-new-resource.md) maken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Beschikbaarheidstest maken

Met beschikbaarheidstests in Application Insights kunt u uw toepassing automatisch testen vanuit verschillende locaties over de hele wereld.   In deze zelfstudie voert u een URL-test uit om te controleren of uw webtoepassing beschikbaar is.  U kunt ook een volledig overzicht maken om de werking van de toepassing gedetailleerd te testen. 

1. Selecteer **Application Insights** en selecteer vervolgens uw abonnement.  

2. Selecteer in het menu **Onderzoeken** de optie **Beschikbaarheid**, en klik vervolgens op **Test maken**.

    ![Beschikbaarheidstest toevoegen](media/tutorial-alert/add-test-001.png)

3. Typ een naam voor de test en laat de andere standaardinstellingen ongewijzigd.  Met deze selectie worden elke 5 minuten aanvragen voor de toepassings-URL geactiveerd vanaf vijf verschillende geografische locaties.

4. Selecteer **Waarschuwingen** om de vervolgkeuzelijst **Waarschuwingen** te openen. Hier kunt u de details definiëren voor de gewenste reactie indien de test mislukt. Kies **Bijna realtime** en stel de status in op **Ingeschakeld.**

    Typ een e-mailadres waarnaar een melding moet worden verzonden, wanneer wordt voldaan aan de waarschuwingscriteria.  U kunt optioneel ook het adres van een webhook typen dat moet worden aangeroepen wanneer wordt voldaan aan de waarschuwingscriteria.

    ![Test maken](media/tutorial-alert/create-test-001.png)

5. Ga terug naar het testpaneel, selecteer het beletselteken en bewerk de waarschuwing om de configuratie voor uw bijna realtime waarschuwing in te voeren.

    ![Waarschuwing bewerken](media/tutorial-alert/edit-alert-001.png)

6. Stel mislukte locaties in op groter dan of gelijk aan 3. Maak een [actiegroep](../alerts/action-groups.md) om te configureren wie een melding krijgt wanneer uw waarschuwingsdrempel wordt overschreden.

    ![Gebruikersinterface voor het opslaan van een waarschuwing](media/tutorial-alert/save-alert-001.png)

7. Klik nadat u de waarschuwing hebt geconfigureerd, op de testnaam om de details van elke locatie weer te geven. Tests kunnen zowel in lijngrafiek- als spreidingsdiagramweergave worden bekeken om de geslaagde/mislukte poging voor een bepaald tijdsbereik te visualiseren.

    ![Testdetails](media/tutorial-alert/test-details-001.png)

8. U kunt inzoomen op de details van elke gewenste test door op de bijbehorende stip in het spreidingsdiagram te klikken. Hiermee wordt de weergave met end-to-end-transactiedetails geopend. In het onderstaande voorbeeld ziet u de details van een mislukte aanvraag.

    ![Testresultaat](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u waarschuwingen voor problemen kunt genereren, gaat u verder met de volgende zelfstudie waarin u leert hoe u hoe kunt analyseren hoe uw gebruikers de toepassing gebruiken.

> [!div class="nextstepaction"]
> [Gebruikers begrijpen](./tutorial-users.md)

