---
title: Achtergrond taken uitvoeren met webjobs
description: Meer informatie over het gebruik van webjobs om achtergrond taken uit te voeren in Azure App Service. Kies uit verschillende script indelingen en voer deze uit met CRON-expressies.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 8afc8ca9b0dedb10ecdb30e8abb22a5d0986de5b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723874"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Achtergrond taken uitvoeren met webjobs in Azure App Service

In dit artikel wordt beschreven hoe u webjobs kunt implementeren met behulp van de [Azure Portal](https://portal.azure.com) voor het uploaden van een uitvoerbaar bestand of script. Zie [Webjobs implementeren met Visual Studio](webjobs-dotnet-deploy-vs.md)voor meer informatie over het ontwikkelen en implementeren van webjobs met Visual Studio.

## <a name="overview"></a>Overzicht
Webjobs is een functie van [Azure app service](index.yml) waarmee u een programma of script kunt uitvoeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. Er zijn geen extra kosten verbonden aan het gebruik van webjobs.

> [!IMPORTANT]
> Webjobs wordt nog niet ondersteund voor App Service in Linux.

De Azure WebJobs SDK kan worden gebruikt met webjobs om veel programmeer taken te vereenvoudigen. Zie [Wat is de Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)? voor meer informatie.

Azure Functions biedt een andere manier om Program ma's en scripts uit te voeren. Zie [kiezen tussen flow, Logic apps, functions en Webjobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)voor een vergelijking tussen webjobs en functies.

## <a name="webjob-types"></a>Webtaak typen

In de volgende tabel worden de verschillen tussen *doorlopende* en *geactiveerde* webjobs beschreven.


|Continu  |Geactiveerd  |
|---------|---------|
| Wordt onmiddellijk gestart wanneer de Webtaak wordt gemaakt. Om te voor komen dat de taak eindigt, wordt het programma of het script doorgaans binnen een oneindige lus uitgevoerd. Als de taak wordt beëindigd, kunt u deze opnieuw starten. | Wordt alleen gestart wanneer hand matig of volgens een planning wordt geactiveerd. |
| Wordt uitgevoerd op alle exemplaren waarop de web-app wordt uitgevoerd. U kunt de Webtaak optioneel beperken tot één exemplaar. |Wordt uitgevoerd op één exemplaar dat door Azure wordt geselecteerd voor taak verdeling.|
| Ondersteunt fout opsporing op afstand. | Biedt geen ondersteuning voor externe fout opsporing.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Ondersteunde bestands typen voor scripts of Program ma's

De volgende bestandstypen worden ondersteund:

* . cmd,. bat,. exe (met behulp van Windows CMD)
* . ps1 (met Power shell)
* . sh (met behulp van bash)
* . php (met PHP)
* . py (met python)
* . js (met behulp van Node.js)
* . jar (met behulp van Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Een doorlopende Webtaak maken

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Als u broncode beheer hebt geconfigureerd met uw toepassing, moeten de webjobs worden geïmplementeerd als onderdeel van de bron beheer integratie. Zodra broncode beheer is geconfigureerd met uw toepassing, kan een Webtaak niet vanuit Azure Portal worden toegevoegd.

1. Ga in het [Azure Portal](https://portal.azure.com)naar de pagina **App Service** van uw app service Web-app, API-app of mobiele app.

2. Selecteer **webjobs**.

   ![Webjobs selecteren](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Selecteer op de pagina **webjobs** de optie **toevoegen**.

    ![Pagina Webtaak](./media/web-sites-create-web-jobs/wjblade.png)

3. Gebruik de instellingen voor het **toevoegen van Webtaaks** zoals opgegeven in de tabel.

   ![Scherm afbeelding met de instellingen voor het toevoegen van webtaaken die u moet configureren.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Instelling      | Voorbeeldwaarde   | Beschrijving  |
   | ------------ | ----------------- | ------------ |
   | **Naam** | myContinuousWebJob | Een naam die uniek is binnen een App Service-app. Moet beginnen met een letter of een cijfer en mag geen speciale tekens bevatten, behalve '-' en ' _ '. |
   | **Bestand uploaden** | ConsoleApp.zip | Een *zip* -bestand dat uw uitvoer bare bestand of script bestanden bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren. De ondersteunde typen uitvoer bare bestanden of scripts worden vermeld in de sectie [ondersteunde bestands typen](#acceptablefiles) . |
   | **Type** | Continu | De [typen Webtaaks](#webjob-types) worden eerder in dit artikel beschreven. |
   | **Schalen** | Meerdere exemplaren | Alleen beschikbaar voor doorlopende webjobs. Hiermee wordt bepaald of het programma of script wordt uitgevoerd op alle exemplaren of op slechts één exemplaar. De optie voor het uitvoeren van meerdere exemplaren is niet van toepassing op de [prijs categorieën](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)gratis of gedeeld. | 

4. Klik op **OK**.

   De nieuwe Webtaak wordt weer gegeven op de pagina **webjobs** .

   ![Lijst met webjobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Als u een doorlopende Webtaak wilt stoppen of opnieuw wilt starten, klikt u met de rechter muisknop in de lijst en klikt u op **stoppen** of **starten**.

    ![Een doorlopende Webtaak stoppen](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Een hand matig geactiveerde Webtaak maken

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Ga in het [Azure Portal](https://portal.azure.com)naar de pagina **App Service** van uw app service Web-app, API-app of mobiele app.

2. Selecteer **webjobs**.

   ![Webjobs selecteren](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Selecteer op de pagina **webjobs** de optie **toevoegen**.

    ![Pagina Webtaak](./media/web-sites-create-web-jobs/wjblade.png)

3. Gebruik de instellingen voor het **toevoegen van Webtaaks** zoals opgegeven in de tabel.

   ![Scherm afbeelding met de instellingen die moeten worden ingesteld voor het maken van een hand matig geactiveerde Webtaak.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Instelling      | Voorbeeldwaarde   | Beschrijving  |
   | ------------ | ----------------- | ------------ |
   | **Naam** | myTriggeredWebJob | Een naam die uniek is binnen een App Service-app. Moet beginnen met een letter of een cijfer en mag geen speciale tekens bevatten, behalve '-' en ' _ '.|
   | **Bestand uploaden** | ConsoleApp.zip | Een *zip* -bestand dat uw uitvoer bare bestand of script bestanden bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren. De ondersteunde typen uitvoer bare bestanden of scripts worden vermeld in de sectie [ondersteunde bestands typen](#acceptablefiles) . |
   | **Type** | Geactiveerd | De [typen Webtaaks](#webjob-types) worden eerder in dit artikel beschreven. |
   | **Triggers** | Handmatig | |

4. Klik op **OK**.

   De nieuwe Webtaak wordt weer gegeven op de pagina **webjobs** .

   ![Lijst met webjobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Als u de Webtaak wilt uitvoeren, klikt u met de rechter muisknop op de naam in de lijst en klikt u op **uitvoeren**.
   
    ![Webtaak uitvoeren](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Een geplande Webtaak maken

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Ga in het [Azure Portal](https://portal.azure.com)naar de pagina **App Service** van uw app service Web-app, API-app of mobiele app.

2. Selecteer **webjobs**.

   ![Webjobs selecteren](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Selecteer op de pagina **webjobs** de optie **toevoegen**.

   ![Pagina Webtaak](./media/web-sites-create-web-jobs/wjblade.png)

3. Gebruik de instellingen voor het **toevoegen van Webtaaks** zoals opgegeven in de tabel.

   ![Pagina Webtaak toevoegen](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Instelling      | Voorbeeldwaarde   | Beschrijving  |
   | ------------ | ----------------- | ------------ |
   | **Naam** | myScheduledWebJob | Een naam die uniek is binnen een App Service-app. Moet beginnen met een letter of een cijfer en mag geen speciale tekens bevatten, behalve '-' en ' _ '. |
   | **Bestand uploaden** | ConsoleApp.zip | Een *zip* -bestand dat uw uitvoer bare bestand of script bestanden bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren. De ondersteunde typen uitvoer bare bestanden of scripts worden vermeld in de sectie [ondersteunde bestands typen](#acceptablefiles) . |
   | **Type** | Geactiveerd | De [typen Webtaaks](#webjob-types) worden eerder in dit artikel beschreven. |
   | **Triggers** | Gepland | Schakel de functie altijd on in om de planning betrouwbaar te laten werken. Always on is alleen beschikbaar in de prijs categorieën Basic, Standard en Premium.|
   | **CRON-expressie** | 0 0/20 * * * * | [Cron-expressies](#ncrontab-expressions) worden beschreven in de volgende sectie. |

4. Klik op **OK**.

   De nieuwe Webtaak wordt weer gegeven op de pagina **webjobs** .

   ![Lijst met webjobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB-expressies

U kunt een [NCRONTAB-expressie](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) invoeren in de portal of een `settings.job` bestand toevoegen aan de hoofdmap van het *zip* -bestand van Webtaak, zoals in het volgende voor beeld:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Zie [een geactiveerde Webtaak plannen](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)voor meer informatie.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> De taak geschiedenis weer geven

1. Selecteer de Webtaak waarvan u de geschiedenis wilt bekijken en selecteer vervolgens de knop **Logboeken** .
   
   ![Knop Logboeken](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Selecteer op de pagina **Details van Webtaak** een tijd om Details voor één uitvoering weer te geven.
   
   ![Details van Webtaak](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Selecteer in de detail pagina voor het **uitvoeren van Webtaaks** de optie uitvoer in- **/uitschakelen** om de tekst van de logboek inhoud te bekijken.
   
    ![Details van de uitvoering van de Webtaak](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Als u de uitvoer tekst in een afzonderlijk browser venster wilt zien, selecteert u **downloaden**. Als u de tekst zelf wilt downloaden, klikt u met de rechter muisknop op **downloaden** en gebruikt u uw browser opties om de inhoud van het bestand op te slaan.
   
5. Selecteer de navigatie koppeling **webjobs** boven aan de pagina om naar een lijst met webjobs te gaan.

    ![Webtaak-breadcrumbnavigatie](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lijst met webtaken in het dash board geschiedenis](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Volgende stappen

De Azure WebJobs SDK kan worden gebruikt met webjobs om veel programmeer taken te vereenvoudigen. Zie [Wat is de Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)? voor meer informatie.
