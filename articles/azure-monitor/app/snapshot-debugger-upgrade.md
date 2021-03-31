---
title: Azure-toepassing Insights-Snapshot Debugger bijwerken
description: Snapshot Debugger voor .NET-Apps upgraden naar de nieuwste versie op Azure-app-Services of via Nuget-pakketten
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77671389"
---
# <a name="upgrading-the-snapshot-debugger"></a>De Snapshot Debugger bijwerken

Micro soft gaat over naar de best mogelijke beveiliging voor uw gegevens door de TLS 1,0 en TLS 1,1 te verplaatsen, die zijn aangetoond dat ze kwetsbaar zijn voor bepaalde aanvallers. Als u een oudere versie van de site-uitbrei ding gebruikt, moet er een upgrade worden uitgevoerd om verder te werken. In dit document vindt u een overzicht van de stappen die nodig zijn om uw momentopname fout opsporing te upgraden naar de nieuwste versie. Er zijn twee primaire upgrade paden, afhankelijk van of u de Snapshot Debugger hebt ingeschakeld met behulp van een site-uitbrei ding of als u een SDK-Nuget hebt gebruikt die aan uw toepassing is toegevoegd. Beide upgrade paden worden hieronder besproken. 

## <a name="upgrading-the-site-extension"></a>De site-uitbrei ding bijwerken

> [!IMPORTANT]
> Oudere versies van Application Insights gebruikte een persoonlijke site-extensie met de naam _Application Insights extensie voor Azure app service_. De huidige Application Insights-ervaring wordt ingeschakeld door app-instellingen in te stellen om een vooraf geïnstalleerde site-uitbrei ding lichter te maken.
> Om conflicten te voor komen, waardoor het mogelijk is dat uw site niet meer werkt, is het belang rijk om eerst de extensie van de persoonlijke site te verwijderen. Zie stap 4 hieronder.

Als u het fout opsporingsprogramma voor moment opnamen met de site-uitbrei ding hebt ingeschakeld, kunt u een upgrade uitvoeren met behulp van de volgende procedure:

1. Meld u aan bij Azure Portal.
2. Navigeer naar uw resource waarvoor Application Insights en snap shot-fout opsporing is ingeschakeld. Bijvoorbeeld: voor een web-app gaat u naar de App Service-Resource.

   ![Scherm afbeelding van de afzonderlijke App Service resource met de naam DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Zodra u naar uw resource hebt genavigeerd, klikt u op de Blade extensies en wacht u op de lijst met extensies die moeten worden ingevuld:

   ![Scherm opname van App Service uitbrei dingen met Application Insights extensie voor Azure App Service geïnstalleerd](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Als er een versie van _Application Insights extensie voor Azure app service_ is geïnstalleerd, selecteert u deze en klikt u op verwijderen. Bevestig **Ja** om de uitbrei ding te verwijderen en wacht totdat het verwijderen is voltooid voordat u verdergaat naar de volgende stap.

   ![Scherm afbeelding van App Service uitbrei dingen met Application Insights extensie voor Azure App Service met de knop verwijderen gemarkeerd](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Ga naar de Blade overzicht van uw resource en klik op Application Insights:

   ![Scherm afbeelding van drie knoppen. Knop Centreren met naam Application Insights is geselecteerd](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Als dit de eerste keer is dat u de Blade Application Insights voor deze App Service hebt bekeken, wordt u gevraagd om Application Insights in te scha kelen. Selecteer **Application Insights inschakelen**.
 
   ![Scherm afbeelding van de eerste ervaring voor de Application Insights Blade met de knop Application Insights inschakelen gemarkeerd](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. De huidige Application Insights-instellingen worden weer gegeven. Tenzij u de kans wilt maken om uw instellingen te wijzigen, kunt u ze ongewijzigd laten. De knop **Toep assen** aan de onderkant van de Blade is niet standaard ingeschakeld en u moet een van de instellingen in-of uitschakelen om de knop te activeren. U hoeft geen echte instellingen te wijzigen. in plaats daarvan kunt u de instelling wijzigen en direct weer wijzigen. Het is raadzaam om de instelling van de Profiler in te scha kelen en vervolgens **Toep assen** te selecteren.

   ![Scherm afbeelding van de pagina configuratie van Application Insights App Service met de knop Toep assen rood gemarkeerd](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Zodra u op **Toep assen** klikt, wordt u gevraagd om de wijzigingen te bevestigen.

    > [!NOTE]
    > De site wordt opnieuw opgestart als onderdeel van het upgrade proces.

   ![Scherm afbeelding van de opdracht prompt voor het Toep assen van App Service. In het tekstvak wordt het volgende bericht weer gegeven: "We zullen nu wijzigingen Toep assen op de app-instellingen en de hulpprogram ma's installeren om uw Application Insights-resource aan de web-app te koppelen. Hiermee wordt de site opnieuw opgestart. Wilt u door gaan? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Klik op **Ja** om de wijzigingen toe te passen en te wachten tot het proces is voltooid.

De site is nu bijgewerkt en is klaar voor gebruik.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Snapshot Debugger bijwerken met SDK-Nuget

Als de toepassing gebruikmaakt van een versie van `Microsoft.ApplicationInsights.SnapshotCollector` onder 1.3.1, moet u een upgrade uitvoeren naar een [nieuwere versie](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) om verder te werken.
