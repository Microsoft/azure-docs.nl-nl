---
title: Azure-resources automatisch schalen op basis van gegevens of een planning
description: Een instelling maken voor automatisch schalen voor een App Service-plan met behulp van metrische gegevens en een planning
author: anirudhcavale
services: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: b5bcc68ddf26dd1fb8421224307c5aa20179afab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627329"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Een instelling maken voor automatisch schalen van Azure-resources op basis van prestatiegegevens of een planning

Instellingen voor automatisch schalen maken het u mogelijk service-instanties toe te voegen of te verwijderen op basis van vooraf gedefinieerde voorwaarden. Deze instellingen kunnen via de portal worden gemaakt. Deze methode biedt een gebruikersinterface voor het maken en configureren van een instelling voor automatisch schalen, op basis van een browser. 

In deze zelfstudie leert u 
> [!div class="checklist"]
> * Een Web-App en een App Service-plan maken
> * Regels configureren voor het automatisch inschalen en uitschalen op basis van het aantal aanvragen die een Web-App ontvangt
> * Een uitschaalactie activeren en bekijken hoe het aantal instanties toeneemt
> * Een inschaalactie activeren en bekijken hoe het aantal instanties afneemt
> * Uw resources opschonen

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Een Web-App en een App Service-plan maken
1. Klik op de optie **Een resource maken** in het navigatievenster aan de linkerkant.
2. Zoek en selecteer het item *Web-App* en klik op **Maken**.
3. Selecteer een app-naam zoals *MyTestScaleWebApp*. Maak een nieuwe resourcegroep 'myResourceGroup' of plaats deze in de resourcegroep van uw keuze.

Binnen een paar minuten moeten uw resources zijn ingericht. Gebruik de Web-App en het bijbehorende App Service-plan in de rest van deze zelfstudie.

   ![Een nieuw App Service-abonnement maken in de portal](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navigeer naar de instellingen voor Automatisch schalen
1. Selecteer de optie **Controleren** in het navigatiedeelvenster aan de linkerkant. Selecteer het tabblad **Automatisch schalen** zodra de pagina wordt geladen.
2. Hier wordt een lijst weergegeven met de resources in uw abonnement die automatisch schalen ondersteunen. Zoek het App Service-Plan dat eerder in de zelfstudie is gemaakt en klik erop.

    ![Schermopname toont de Azure Portal met Monitor en vervolgens Automatisch schalen geselecteerd.](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. In de instelling voor automatisch schalen klikt u op de knop **Automatisch schalen inschakelen**.

De volgende stappen helpen u met het invullen van het scherm Automatisch schalen, zodat het eruitziet als de volgende afbeelding:

   ![Instelling voor automatisch schalen opslaan](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Standaardprofiel configureren
1. Geef een **Naam** op voor de instelling voor automatisch schalen.
2. In het standaardprofiel zorgt u ervoor dat de **Schaalmodus** is ingesteld op 'Schalen naar een specifiek aantal instanties'.
3. Stel het aantal instanties in op **1**. Deze instelling zorgt ervoor dat, wanneer er geen ander profiel actief is, of in feite in het standaardprofiel is geselecteerd, het aantal instanties op 1 wordt gezet.

   ![Schermopname toont de instellingspagina voor Automatisch schalen met een naam die is ingevoerd voor de instelling.](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Terugkeerpatroonprofiel maken

1. Klik op de link **Een schaalvoorwaarde toevoegen** onder het standaardprofiel.

2. Verander de **Naam** van dit profiel in 'Maandag tot vrijdag-profiel'.

3. Zorg ervoor dat de **Schaalmodus** is ingesteld op 'Schalen op basis van een metrische waarde'.

4. Voor **Instantielimieten** stelt u het **Minimum** in op '1', het **Maximum** op '2' en de **Standaard** op '1'. Deze instelling zorgt ervoor dat dit profiel niet automatisch het serviceabonnement naar minder dan 1 exemplaar of meer dan 2 exemplaren schaalt. Als het profiel niet voldoende gegevens heeft om een beslissing te nemen, wordt het standaard aantal instanties gebruikt (in dit geval 1).

5. Voor **Planning**, selecteert u 'Herhalen op specifieke dagen'.

6. Het profiel moet worden ingesteld op herhalen van maandag tot en met vrijdag, van 09:00 PST tot 18:00 PST. Deze instelling zorgt ervoor dat dit profiel alleen actief en van toepassing is van 9:00 uur tot 18:00 uur, maandag tot en met vrijdag. Het profiel 'Standaard' is tijdens alle andere tijden het profiel waarvan de instelling voor automatisch schalen gebruikmaakt.

## <a name="create-a-scale-out-rule"></a>Een regel voor uitschalen maken

1. In het 'Maandag tot vrijdag-profiel'.

2. Klik op de link **Een regel toevoegen**.

3. Stel de **Metrische bron** in op 'andere resource'. Stel het **Resourcetype** in op ‘App Services’ en de **Resource** als de Web-App die eerder in deze zelfstudie is gemaakt.

4. Stel de **Tijdaggregatie** in op 'Totaal', de **Metrische naam** op 'Aanvragen' en de **Tijdsinterval-statistiek** op 'Som'.

5. Stel de **Operator** in op 'Groter dan', de **Drempelwaarde** op "10" en de **Duur** op '5' minuten.

6. Selecteer de **Bewerking** 'Aantal verhogen met', een **Aantal instanties** van '1' en een **Cooldown** van '5' minuten.

7. Klik op de knop **Toevoegen**.

Deze regel zorgt ervoor dat als uw Web-App meer dan 10 aanvragen binnen 5 minuten of minder ontvangt, een extra exemplaar aan uw App Service-plan wordt toegevoegd om de belasting te reguleren.

   ![Een regel voor uitschalen maken](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Een regel voor inschalen maken
Wij raden u aan om een uitschaalregel altijd door een inschaalregel vergezeld te laten gaan. Beide zorgen ervoor dat er geen overschot in uw resources is. Een overschot betekent dat er meer instanties worden uitgevoerd dan nodig is om de huidige belasting te verwerken. 

1. In het 'Maandag tot vrijdag-profiel'.

2. Klik op de link **Een regel toevoegen**.

3. Stel de **Metrische bron** in op 'andere resource'. Stel het **Resourcetype** in op ‘App Services’ en de **Resource** als de Web-App die eerder in deze zelfstudie is gemaakt.

4. Stel de **Tijdaggregatie** in op 'Totaal', de **Metrische naam** op 'Aanvragen' en de **Tijdsinterval-statistiek** op 'Gemiddeld'.

5. Stel de **Operator** in op 'Kleiner dan', de **Drempelwaarde** op ‘5’ en de **Duur** op '5' minuten.

6. Selecteer de **Bewerking** 'Aantal verlagen met', een **Aantal instanties** van '1' en een **Cooldown** van '5' minuten.

7. Klik op de knop **Toevoegen**.

    ![Een regel voor inschalen maken](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. Sla de instelling voor automatisch schalen **op**.

    ![Instelling voor automatisch schalen opslaan](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Uitschaalactie activeren
De Web-App moet meer dan 10 aanvragen hebben in minder dan 5 minuten om de zojuist gemaakte voorwaarde voor de uitschaalactie in de instelling voor automatisch schalen te activeren.

1. Open een browservenster en navigeer naar de Web-App die eerder in deze zelfstudie is gemaakt. U vindt de URL voor uw Web-App in de Azure Portal door te navigeren naar uw Web-App-resource en te klikken op de knop **Bladeren** op het tabblad 'Overzicht'.

2. Laad de pagina minstens 10 keer snel achter elkaar opnieuw.

3. Selecteer de optie **Controleren** in het navigatiedeelvenster aan de linkerkant. Selecteer het tabblad **Automatisch schalen** zodra de pagina wordt geladen.

4. Selecteer in de lijst het App Service-plan dat in deze zelfstudie wordt gebruikt.

5. Klik in de instelling voor automatisch schalen op het tabblad **Uitvoeringsgeschiedenis**.

6. U ziet een grafiek dat het aantal instanties van het App Service-plan in de loop van de tijd weergeeft.

7. Het aantal instanties moet binnen een paar minuten oplopen van 1 naar 2.

8. Onder de grafiek ziet u de vermeldingen in het activiteitenlogboek voor elke schaalactie, die door deze instelling voor automatisch schalen is uitgevoerd.

## <a name="trigger-scale-in-action"></a>Inschaalactie activeren
De inschaalvoorwaarde in de instelling voor automatisch schalen wordt geactiveerd als er gedurende een periode van 10 minuten minder dan 5 aanvragen naar de Web-App zijn. 

1. Zorg ervoor dat er geen aanvragen worden verzonden naar uw web-app.

2. Laad de Azure Portal.

3. Selecteer de optie **Controleren** in het navigatiedeelvenster aan de linkerkant. Selecteer het tabblad **Automatisch schalen** zodra de pagina wordt geladen.

4. Selecteer in de lijst het App Service-plan dat in deze zelfstudie wordt gebruikt.

5. Klik in de instelling voor automatisch schalen op het tabblad **Uitvoeringsgeschiedenis**.

6. U ziet een grafiek dat het aantal instanties van het App Service-plan in de loop van de tijd weergeeft.

7. Het aantal instanties moet binnen een paar minuten zakken van 2 naar 1. Het proces duurt minimaal 100 minuten.  

8. Onder de grafiek ziet u de vermeldingen in het activiteitenlogboek voor elke schaalactie, die door deze instelling voor automatisch schalen is uitgevoerd.

    ![Inschaalactie weergeven](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Resources opschonen

1. Klik in het linkermenu van de Azure Portal op **Alle resources** en selecteer de Web-App die u in deze zelfstudie heeft gemaakt.

2. Klik in uw resourcepagina op **Verwijderen**, bevestig het verwijderen door **ja** in het tekstvak te typen en klik vervolgens op **Verwijderen**.

3. Vervolgens selecteert u de resource App Service-plan en klikt u op **Verwijderen**.

4. Bevestig het verwijderen door **ja** in het tekstvak te typen en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u  
> [!div class="checklist"]
> * Een Web-App en een App Service-plan gemaakt
> * Regels geconfigureerd voor het automatisch inschalen en uitschalen op basis van het aantal aanvragen die een Web-App ontvangt
> * Een uitschaalactie geactiveerd en het aantal instanties zien verhogen
> * Een inschaalactie geactiveerd en het aantal instanties zien verlagen
> * Uw resources opgeschoond


Voor meer informatie over instellingen voor automatisch schalen gaat u naar het [Overzicht automatisch schalen](../autoscale/autoscale-overview.md).

> [!div class="nextstepaction"]
> [Bewakingsgegevens archiveren](../essentials/platform-logs-overview.md)

