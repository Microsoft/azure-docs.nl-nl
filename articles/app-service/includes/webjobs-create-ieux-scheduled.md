---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745671"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Een geplande Webtaak maken


1. Ontvangen op de [Azure Portal](https://portal.azure.com).
1. Ga naar uw **app service** van uw <abbr title="Uw app-resource kan een web-app, API-app of mobiele app zijn.">App-resource</abbr>.
1. Selecteer **webjobs**.

   ![Webjobs selecteren](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Selecteer op de pagina **webjobs** de optie **toevoegen**.

    ![Pagina Webtaak](../media/web-sites-create-web-jobs/wjblade.png)

1. Gebruik de instellingen voor het **toevoegen van Webtaaks** zoals opgegeven in de tabel.

    ![Pagina Webtaak toevoegen](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Instelling      | Voorbeeldwaarde   |
    | ------------ | ----------------- | 
    | <abbr title="Een naam die uniek is binnen een App Service-app. Moet beginnen met een letter of een cijfer en mag geen speciale tekens bevatten `-` , behalve en `_` .">Name</a> | myScheduledWebJob |  |
    | <abbr title="Een *zip* -bestand dat uw uitvoer bare bestand of script bestanden bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren.">Bestand uploaden</abbr> | ConsoleApp.zip |
    | <abbr title="Typen bevatten doorlopend, geactiveerd.">Type</abbr> | Geactiveerd |
    | <abbr title="Schakel de functie altijd on in om de planning betrouwbaar te laten werken. Always on is alleen beschikbaar in de prijs categorieën Basic, Standard en Premium.">Triggers</a> | Gepland |
    | CRON-expressie</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>Meer informatie over CRON-expressies</summary>
     <a name="#ncrontab-expressions"></a>
    
     U kunt een [NCRONTAB-expressie](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) invoeren in de portal of een `settings.job` bestand toevoegen aan de hoofdmap van het *zip* -bestand van Webtaak, zoals in het volgende voor beeld:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Zie [een geactiveerde Webtaak plannen](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)voor meer informatie.
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Klik op **OK**.

    De nieuwe Webtaak wordt weer gegeven op de pagina **webjobs** .
    
    ![Lijst met webjobs](../media/web-sites-create-web-jobs/listallwebjobs.png)
