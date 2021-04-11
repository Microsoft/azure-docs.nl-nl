---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745663"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Een doorlopende Webtaak maken

1. Ontvangen op de [Azure Portal](https://portal.azure.com).
1. Ga naar uw **app service** van uw <abbr title="Uw app-resource kan een web-app, API-app of mobiele app zijn.">App-resource</abbr>.
1. Selecteer **webjobs**.

   ![Webjobs selecteren](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Selecteer op de pagina **webjobs** de optie **toevoegen**.

    ![Pagina Webtaak](../media/web-sites-create-web-jobs/wjblade.png)

1. Gebruik de instellingen voor het **toevoegen van Webtaaks** zoals opgegeven in de tabel.

   ![Scherm afbeelding met de instellingen voor het toevoegen van webtaaken die u moet configureren.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Instelling      | Voorbeeldwaarde   | 
   | ------------ | ----------------- | 
   | <abbr title="Een naam die uniek is binnen een App Service-app. Moet beginnen met een letter of een cijfer en mag geen speciale tekens bevatten `-` , behalve en `_` .">Name</abbr> | myContinuousWebJob | 
   | <abbr title=" Een *zip* -bestand dat uw uitvoer bare bestand of script bestanden bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren.">Bestand uploaden</abbr> | ConsoleApp.zip |
   | <abbr title="Typen bevatten doorlopend, geactiveerd.">Type</abbr> | Continu | 
   | <abbr title="Alleen beschikbaar voor doorlopende webjobs. Hiermee wordt bepaald of het programma of script wordt uitgevoerd op alle exemplaren of op slechts één exemplaar. De optie voor het uitvoeren van meerdere exemplaren is niet van toepassing op de prijs categorieën gratis of gedeeld.">Schalen</abbr> | Meerdere exemplaren | 

1. Klik op **OK**.

    De nieuwe Webtaak wordt weer gegeven op de pagina **webjobs** .

    ![Lijst met webjobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. Als u een doorlopende Webtaak **wilt stoppen of opnieuw wilt starten** , klikt u met de rechter muisknop in de lijst en klikt u op **stoppen** of **starten**.

   ![Een doorlopende Webtaak stoppen](../media/web-sites-create-web-jobs/continuousstop.png)
