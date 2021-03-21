---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5687fb99c27b8b2141e0a2a817327cfbb124951a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109368"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Een hand matig geactiveerde Webtaak maken

1. Ontvangen op de [Azure Portal](https://portal.azure.com).
1. Ga naar uw **app service** van uw <abbr title="Uw app-resource kan een web-app, API-app of mobiele app zijn.">App-resource</abbr>.
1. Selecteer **webjobs**.

    ![Webjobs selecteren](../media/web-sites-create-web-jobs/select-webjobs.png)

2. Selecteer op de pagina **webjobs** de optie **toevoegen**.

   ![Pagina Webtaak](../media/web-sites-create-web-jobs/wjblade.png)

3. Gebruik de instellingen voor het **toevoegen van Webtaaks** zoals opgegeven in de tabel.

    ![Scherm afbeelding met de instellingen die moeten worden ingesteld voor het maken van een hand matig geactiveerde Webtaak.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Instelling      | Voorbeeldwaarde   | 
    | ------------ | ----------------- | 
   | <abbr title="Een naam die uniek is binnen een App Service-app. Moet beginnen met een letter of een cijfer en mag geen speciale tekens bevatten `-` , behalve en `_` .">Name</abbr> | myTriggeredWebJob | 
    | <abbr title="Een *zip* -bestand dat uw uitvoer bare bestand of script bestanden bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren.">Bestand uploaden</abbr> | ConsoleApp.zip |
    | <abbr title="Typen bevatten doorlopend, geactiveerd.">Type</abbr> | Geactiveerd | 
    | <abbr title="Typen bevatten gepland of hand matig">Triggers</a> | Handmatig | |

4. Klik op **OK**. 

   De nieuwe Webtaak wordt weer gegeven op de pagina **webjobs** .

   ![Lijst met webjobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Als u een hand matige Webtaak wilt uitvoeren**, klikt u met de rechter muisknop op de naam in de lijst en klikt u op **uitvoeren**.
   
    ![Webtaak uitvoeren](../media/web-sites-create-web-jobs/runondemand.png)

