---
title: 'Zelfstudie: Azure Spring Cloud-resources bewaken met behulp van waarschuwingen en actiegroepen | Microsoft Docs'
description: Meer informatie over het gebruik van Spring Cloud-waarschuwingen.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: d12a48729616a5181f019f84f19779390e736cb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879147"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Zelf studie: lente-cloud resources bewaken met behulp van waarschuwingen en actie groepen

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Azure Spring Cloud-waarschuwingen bieden ondersteuning voor het bewaken van resources op basis van voorwaarden als beschikbare opslag, frequentie van aanvragen of gegevensgebruik. Er wordt een melding verzonden wanneer de frequenties of voorwaarden aan de gedefinieerde specificaties voldoen.

Er zijn twee stappen voor het instellen van een waarschuwingspijplijn: 
1. Stel een actiegroep in met de acties die moeten worden uitgevoerd wanneer een waarschuwing wordt geactiveerd, zoals een e-mail, sms, runbook of webhook. Actiegroepen kunnen opnieuw worden gebruikt voor verschillende waarschuwingen.
2. Stel waarschuwingsregels in. De regels verbinden metrische patronen met de actiegroepen op basis van doelresource, metrische gegevens, voorwaarde, tijdaggregatie, enzovoort.

## <a name="prerequisites"></a>Vereisten

De procedures in deze zelfstudie werken met een geïmplementeerd Azure Spring Cloud-exemplaar en moeten voldoen aan de vereisten voor Azure Spring.  Volg een [snelstart](spring-cloud-quickstart.md) om aan de slag te gaan.

Met de volgende procedures worden **Actiegroep** en **Waarschuwing** gestart vanaf de optie **Waarschuwingen** in het linkernavigatievenster van een Spring Cloud-exemplaar. (De procedure kan ook worden gestart op de pagina **Monitor - Overzicht** in de Azure-portal.) 

Navigeer van een resourcegroep naar uw Spring Cloud-exemplaar. Selecteer **Waarschuwingen** in het linkerdeelvenster en selecteer vervolgens **Acties beheren**:

![Schermopname van de pagina met de resourcegroep in de portal](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Actiegroep instellen

Als u de procedure wilt starten voor het initialiseren van een nieuwe **Actiegroep**, selecteert u **+ Actiegroep toevoegen**.

![Schermopname met Actiegroep toevoegen in de portal](media/alerts-action-groups/action-1.png)

Op de pagina **Actiegroep toevoegen** doet u het volgende:

 1. Geef een **naam van de actiegroep** en **korte naam** op.

 1. Geef uw **abonnement** en **resourcegroep** op.

 1. Geef de **actienaam** op.

 1. Selecteer **Actietype**.  Hiermee opent u een ander deelvenster aan de rechterkant om de actie te definiëren die tijdens de activering wordt uitgevoerd.

 1. Definieer de actie met behulp van de opties in het rechterdeelvenster.  Hiervoor wordt gebruikgemaakt van een melding per e-mail.

 1. Klik op **OK** in het rechterdeelvenster.

 1. Klik op **OK** in het dialoogvenster **Actiegroep toevoegen**. 

  ![Schermopname van het definiëren van een actie in de portal](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Waarschuwing instellen 

In de vorige stappen is een **Actiegroep** gemaakt die gebruikmaakt van e-mail. U kunt ook telefoon meldingen via telefoon, webhooks, Azure-functies, enzovoort gebruiken. Met de volgende stappen configureert u een **Waarschuwing**.

1. ga terig naar de pagina **Waarschuwingen** en klik op **Waarschuwingsregels beheren**.

   ![Schermopname van het definiëren van een waarschuwing in de portal](media/alerts-action-groups/alerts-2.png)

1. Selecteer de **resource** voor de waarschuwing.

1. Klik op **+ Nieuwe waarschuwingsregel**.

   ![Schermopname van nieuwe waarschuwingsregel in de portal](media/alerts-action-groups/alerts-3.png)

1. Geef op de pagina **Regel maken** de **RESOURCE** op.

1. De instelling **VOORWAARDE** biedt veel opties voor het bewaken van uw **Spring Cloud**-resources.  Klik op **Toevoegen** om het deelvenster **Signaallogica configureren** te openen.

1. Selecteer een voorwaarde. In dit voorbeeld wordt gebruikgemaakt van **Systeem-CPU-gebruik in procenten**.

   ![Schermopname van nieuwe waarschuwingsregel in de portal 2](media/alerts-action-groups/alerts-3-1.png)

1. Schuif omlaag in het deelvenster **Signaallogica configureren** om de te bewaken **Drempelwaarde** in te stellen.

   ![Schermopname van nieuwe waarschuwingsregel in de portal 3](media/alerts-action-groups/alerts-3-2.png)

1. Klik op **Gereed**.

   Zie [Opties voor metrische gegevens in gebruikersportal ](spring-cloud-concept-metrics.md#user-metrics-options)voor meer informatie over de beschikbare voorwaarden voor bewaking.

1. Klik onder **ACTIES** op **Actiegroep selecteren**. Selecteer in het deelvenster **ACTIES** de eerder gedefinieerde **Actiegroep**.

   ![Schermopname van nieuwe waarschuwingsregel in de portal 4](media/alerts-action-groups/alerts-3-3.png) 

1. Schuif omlaag en geef onder **WAARSCHUWINGSDETAILS** een naam op voor de waarschuwingsregel.

1. Stel **Ernst** in.

1. Klik op **Waarschuwingsregel maken**.

   ![Schermopname van nieuwe waarschuwingsregel in de portal 5](media/alerts-action-groups/alerts-3-4.png)

1. Controleer of de nieuwe waarschuwingsregel is ingeschakeld.

   ![Schermopname van nieuwe waarschuwingsregel in de portal 6](media/alerts-action-groups/alerts-4.png)

Een regel kan ook worden gemaakt op de pagina **Metrische gegevens**:

![Schermopname van nieuwe waarschuwingsregel in de portal 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u waarschuwingen en actiegroepen instelt voor een Azure Spring Cloud-toepassing. Voor meer informatie over actiegroepen raadpleegt u:

> [!div class="nextstepaction"]
> [Actiegroepen maken en beheren in de Azure-portal](../azure-monitor/alerts/action-groups.md)

> [!div class="nextstepaction"]
> [Gedrag van waarschuwingen via sms in actiegroepen](../azure-monitor/alerts/alerts-sms-behavior.md)