---
title: Webjobs ontwikkelen en implementeren met behulp van Visual Studio
description: Meer informatie over het ontwikkelen van Azure WebJobs in Visual Studio en het implementeren ervan naar Azure App Service, inclusief het maken van een geplande taak.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89226026"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Webjobs ontwikkelen en implementeren met behulp van Visual Studio

In dit artikel wordt uitgelegd hoe u met Visual Studio een console-app-project implementeert in een web-app in [Azure app service](overview.md) als een [Azure-Webtaak](https://go.microsoft.com/fwlink/?LinkId=390226). Zie [achtergrond taken uitvoeren met webjobs in azure app service](webjobs-create.md)voor informatie over het implementeren van webjobs met behulp van de [Azure Portal](https://portal.azure.com).

U kunt kiezen voor het ontwikkelen van een Webtaak die wordt uitgevoerd als een [.net core-app](#webjobs-as-net-core-console-apps) of een [.NET Framework-app](#webjobs-as-net-framework-console-apps). Met versie 3. x van de [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) kunt u webjobs ontwikkelen die worden uitgevoerd als .net core-apps of .NET Framework-apps, terwijl versie 2. x alleen de .NET Framework ondersteunt. De manier waarop u een webjobs-project implementeert, wijkt af van .NET core-projecten dan voor .NET Framework projecten.

U kunt meerdere webjobs publiceren naar één web-app, op voor waarde dat elke Webtaak in een web-app een unieke naam heeft.

## <a name="webjobs-as-net-core-console-apps"></a>Webjobs als .NET core-console-apps

Met versie 3. x van de Azure WebJobs SDK kunt u webjobs maken en publiceren als .NET core console-apps. Zie [aan de slag met de Azure WEBJOBS SDK voor op gebeurtenissen gebaseerde achtergrond verwerking](webjobs-sdk-get-started.md)voor stapsgewijze instructies voor het maken en publiceren van een .net Core-Console-app naar Azure als Webtaak.

> [!NOTE]
> .NET core-webjobs kunnen niet worden gekoppeld aan webprojecten. Als u uw Webtaak wilt implementeren met een web-app, [maakt u uw webjobs als een .NET Framework console-app](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementeren naar Azure App Service

Het publiceren van een .NET core-Webtaak naar Azure App Service vanuit Visual Studio maakt gebruik van dezelfde hulp middelen als voor het publiceren van een ASP.NET Core-app.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Webjobs als .NET Framework-console-apps  

Als u Visual Studio gebruikt om een .NET Framework console-app-project met webjobs te implementeren, worden runtime-bestanden gekopieerd naar de juiste map in de web-app (*App_Data/Jobs/Continuous* voor doorlopende webjobs en *App_Data/Jobs/triggered* voor geplande of on-demand webjobs).

Visual Studio voegt de volgende items toe aan een project met webjobs:

* Het NuGet-pakket [micro soft. Web. webjobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Een [webjob-publish-settings.jsin](#publishsettings) het bestand met instellingen voor implementatie en scheduler. 

![Diagram waarin wordt weer gegeven wat er wordt toegevoegd aan een console-app om implementatie als een Webtaak in te scha kelen](./media/webjobs-dotnet-deploy-vs/convert.png)

U kunt deze items toevoegen aan een bestaand console-app-project of een sjabloon gebruiken voor het maken van een nieuwe console-app project met webtaken. 

Implementeer een project zelf als Webtaak of koppel dit aan een webproject, zodat het automatisch wordt geïmplementeerd wanneer u het webproject implementeert. Voor het koppelen van projecten bevat Visual Studio de naam van het project met webtaken in een [webjobs-list.jsvoor](#webjobslist) het bestand in het webproject.

![Diagram van het project taak koppeling naar webproject wordt weer gegeven](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Vereisten

Installeer Visual Studio 2017 of Visual Studio 2019 met de [werk belasting van Azure Development](/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> De implementatie van webjobs inschakelen voor een bestaand console-app-project

U hebt hiervoor twee opties:

* [Automatische implementatie met een webproject inschakelen](#convertlink).

  Configureer een bestaand console-app-project zodat het automatisch als Webtaak wordt geïmplementeerd wanneer u een webproject implementeert. Gebruik deze optie wanneer u uw Webtaak wilt uitvoeren in dezelfde Web-app waarin u de gerelateerde webtoepassing uitvoert.

* [Implementatie inschakelen zonder een webproject](#convertnolink).

  Een bestaand console-app-project configureren om te implementeren als een Webtaak zelf, zonder een koppeling naar een webproject. Gebruik deze optie als u een Webtaak zelf wilt uitvoeren in een web-app, zonder dat er een webtoepassing in de web-app wordt uitgevoerd. U kunt dit doen om de resources van uw webtoepassing onafhankelijk van uw webtoepassingsgegevens te schalen.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Automatische implementatie van webjobs met een webproject inschakelen

1. Klik met de rechter muisknop op het webproject in **Solution Explorer** en Selecteer vervolgens  >  **bestaand project als Azure-Webtaak** toevoegen.
   
    ![Bestaand project als Azure-Webtaak](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Het dialoog venster [Azure-Webtaak toevoegen](#configure) wordt weer gegeven.
2. Selecteer in de vervolg keuzelijst **project naam** het project van de console-app dat u als Webtaak wilt toevoegen.
   
    ![Project selecteren in het dialoog venster een Azure-Webtaak toevoegen](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Voltooi het dialoog venster [Azure-Webtaak toevoegen](#configure) en selecteer vervolgens **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> De implementatie van webjobs zonder een webproject inschakelen
1. Klik met de rechter muisknop op het console-app-project in **Solution Explorer** en selecteer vervolgens **publiceren als Azure-Webtaak**. 
   
    ![Publiceren als Azure-Webtaak](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Het dialoog venster [Azure-Webtaak toevoegen](#configure) wordt weer gegeven, met het project geselecteerd in het vak **project naam** .
2. Voltooi het dialoog venster [Azure-Webtaak toevoegen](#configure) en selecteer vervolgens **OK**.
   
   De wizard **Publish Web** wordt weer gegeven. Als u niet onmiddellijk wilt publiceren, sluit u de wizard. De instellingen die u hebt ingevoerd, worden opgeslagen voor wanneer u [het project wilt implementeren](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Een nieuw project maken dat is ingeschakeld voor webjobs
Als u een nieuw project met webjobs wilt maken, gebruikt u de sjabloon console-app project en schakelt u de implementatie van webjobs in zoals beschreven in [de vorige sectie](#convert). Als alternatief kunt u de sjabloon webjobs nieuw-project gebruiken:

* [De sjabloon webjobs New-Project gebruiken voor een onafhankelijke Webtaak](#createnolink)
  
    Een project maken en configureren voor implementatie door zichzelf als Webtaak, zonder een koppeling naar een webproject. Gebruik deze optie als u een Webtaak zelf wilt uitvoeren in een web-app, zonder dat er een webtoepassing in de web-app wordt uitgevoerd. U kunt dit doen om de resources van uw webtoepassing onafhankelijk van uw webtoepassingsgegevens te schalen.
* [De sjabloon webjobs New-Project gebruiken voor een Webtaak die is gekoppeld aan een webproject](#createlink)
  
    Een project maken dat is geconfigureerd om automatisch te worden geïmplementeerd als Webtaak wanneer u een webproject implementeert in dezelfde oplossing. Gebruik deze optie wanneer u uw Webtaak wilt uitvoeren in dezelfde Web-app waarin u de gerelateerde webtoepassing uitvoert.

> [!NOTE]
> De sjabloon webjobs New-Project installeert automatisch NuGet-pakketten en bevat code in *programma. cs* voor de [webjobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Als u de webjobs SDK niet wilt gebruiken, verwijdert of wijzigt u de `host.RunAndBlock` instructie in *Program. cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> De sjabloon webjobs New-Project gebruiken voor een onafhankelijke Webtaak
1. Selecteer **Bestand** > **Nieuw** > **Project**. Zoek in het dialoog venster **Crete een nieuw project** naar en selecteer **Azure webtaak (.NET Framework)** voor C#.
   
2. Volg de vorige instructies om [de console-app project een onafhankelijk Webjobs-project te maken](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> De sjabloon webjobs New-Project gebruiken voor een Webtaak die is gekoppeld aan een webproject
1. Klik met de rechter muisknop op het webproject in **Solution Explorer** en Selecteer vervolgens  >  **Nieuw Azure-project voor Webtaak** toevoegen.
   
    ![Nieuw menu-item van Azure Webtaak-project](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Het dialoog venster [Azure-Webtaak toevoegen](#configure) wordt weer gegeven.
2. Voltooi het dialoog venster [Azure-Webtaak toevoegen](#configure) en selecteer vervolgens **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.jsvoor bestand
Wanneer u een console-app voor de implementatie van webjobs configureert, installeert Visual Studio het [micro soft. Web. webjobs. Publiceer](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) het NuGet-pakket en slaat de plannings gegevens op in een *webjob-publish-settings.js* in een bestand in de map project *Eigenschappen* van het webjobs-project. Hier volgt een voor beeld van dat bestand:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

U kunt dit bestand rechtstreeks bewerken en Visual Studio biedt IntelliSense. Het bestands schema wordt opgeslagen op [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) en kan daar worden weer gegeven.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.jsvoor bestand
Wanneer u een project met webjobs koppelt aan een webproject, slaat Visual Studio de naam van het webjobs-project op in een *webjobs-list.js* in het bestand in de map *Eigenschappen* van het webproject. De lijst bevat mogelijk meerdere webjobs-projecten, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

U kunt dit bestand rechtstreeks in Visual Studio bewerken met IntelliSense. Het bestands schema wordt opgeslagen op [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Een webjobs-project implementeren
Een webjobs-project dat u hebt gekoppeld aan een webproject, wordt automatisch geïmplementeerd met het webproject. Zie **procedures** voor  >  **het implementeren van de app** in de linkernavigatiebalk voor meer informatie over de implementatie van webprojecten.

Als u een webjobs-project zelf wilt implementeren, klikt u met de rechter muisknop op het project in **Solution Explorer** en selecteert u **publiceren als Azure-Webtaak**. 

![Publiceren als Azure-Webtaak](./media/webjobs-dotnet-deploy-vs/paw.png)

Voor een onafhankelijke Webtaak wordt dezelfde wizard **Publish Web** die wordt gebruikt voor webprojecten weer gegeven, maar er zijn minder instellingen beschikbaar die kunnen worden gewijzigd.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Het dialoog venster Azure-Webtaak toevoegen
In het dialoog venster **Azure-Webtaak toevoegen** kunt u de naam van de Webtaak en de instelling voor de uitvoerings modus voor uw Webtaak invoeren. 

![Het dialoog venster Azure-Webtaak toevoegen](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Sommige van de velden in dit dialoog venster komen overeen met de velden in het dialoog venster **Webtaak toevoegen** van de Azure Portal. Zie [achtergrond taken uitvoeren met webjobs in azure app service](webjobs-create.md)voor meer informatie.

Implementatie gegevens van Webtaak:

* Zie [opdracht regel of continue levering van Azure WebJobs inschakelen](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)voor meer informatie over de implementatie van de opdracht regel.

* Als u een Webtaak implementeert en vervolgens besluit dat u het type Webtaak wilt wijzigen en opnieuw wilt implementeren, verwijdert u de *webjobs-publish-settings.jsin* het bestand. Als u dat doet, worden de publicatie opties opnieuw weer gegeven in Visual Studio, zodat u het type Webtaak kunt wijzigen.

* Als u een Webtaak implementeert en later de uitvoerings modus wijzigt van doorlopend naar niet-doorlopend of omgekeerd, maakt Visual Studio een nieuwe Webtaak in azure wanneer u de implementatie opnieuw uitvoert. Als u andere plannings instellingen wijzigt, maar de modus uitvoeren hetzelfde of overschakelen tussen gepland en op aanvraag, wordt de bestaande taak in Visual Studio bijgewerkt in plaats van een nieuwe te maken.

## <a name="webjob-types"></a>Webtaak typen

Het type van een Webtaak kan worden *geactiveerd* of *doorlopend* zijn:

- Geactiveerd (standaard): een geactiveerde Webtaak wordt gestart op basis van een bindings gebeurtenis, volgens een [planning](#scheduling-a-triggered-webjob)of wanneer u deze hand matig (op aanvraag) aanstuurt. Het wordt uitgevoerd op één exemplaar waarop de web-app wordt uitgevoerd.

- Doorlopend: een [doorlopende](#continuous-execution) Webtaak wordt onmiddellijk gestart wanneer de Webtaak wordt gemaakt. De service wordt standaard uitgevoerd op alle geschaalde exemplaren van de web-app, maar kan worden geconfigureerd om te worden uitgevoerd als één exemplaar via *instellingen. Job*.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Een geactiveerde webtaak plannen

Wanneer u een console-app naar Azure publiceert, wordt in Visual Studio het type Webtaak ingesteld op standaard **geactiveerd** en wordt er een nieuw bestand met de *instellingen. Job* toegevoegd aan het project. Voor geactiveerde Webtaak typen kunt u dit bestand gebruiken om een uitvoerings schema voor uw Webtaak in te stellen.

Gebruik het bestand *Settings. Job* om een uitvoerings schema voor uw Webtaak in te stellen. In het volgende voor beeld wordt elk uur van 9 tot 5 uur uitgevoerd:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Dit bestand bevindt zich in de hoofdmap van de map webjobs met het script van de Webtaak, zoals `wwwroot\app_data\jobs\triggered\{job name}` of `wwwroot\app_data\jobs\continuous\{job name}` . Wanneer u een Webtaak implementeert vanuit Visual Studio, markeert u uw *instellingen.* de bestands eigenschappen van de taak in Visual Studio als **kopiëren indien nieuwer**.

Als u [een Webtaak maakt op basis van de Azure Portal](webjobs-create.md), wordt het bestand *Settings. Job* voor u gemaakt.

#### <a name="cron-expressions"></a>CRON-expressies

Webjobs maakt gebruik van dezelfde CRON-expressies voor planning als de trigger timer in Azure Functions. Zie [Timer trigger voor Azure functions voor](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)meer informatie over cron-ondersteuning.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>Naslag informatie over Settings. job

De volgende instellingen worden ondersteund door webjobs:

| **Instelling** | **Type**  | **Beschrijving** |
| ----------- | --------- | --------------- |
| `is_in_place` | Alles | Hiermee kan de Webtaak op locatie worden uitgevoerd zonder eerst naar een tijdelijke map te worden gekopieerd. Zie voor meer informatie [werk directory Webtaak](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Continu | Voer de Webtaak alleen uit op één instantie wanneer deze is uitgeschaald. Zie [een continue taak als Singleton instellen](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)voor meer informatie. |
| `schedule` | Geactiveerd | Voer de Webtaak uit op basis van een CRON schema. Zie [NCRONTAB-expressies](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)voor meer informatie. |
| `stopping_wait_time`| Alles | Hiermee staat u het beheer van het afsluit gedrag toe. Zie voor meer informatie [correct afsluiten](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Doorlopende uitvoering

Als u AlwaysOn **in azure inschakelt** , kunt u Visual Studio gebruiken om de Webtaak continu uit te voeren:

1. Als u dit nog niet hebt gedaan, [kunt u het project publiceren naar Azure](#deploy-to-azure-app-service).

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Kies op het tabblad **publiceren** de optie **bewerken**. 

1. Kies in het dialoog venster **Profiel instellingen** **doorlopend** voor **type Webtaak** en kies vervolgens **Opslaan**.

    ![Het dialoog venster publicatie-instellingen voor een Webtaak](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecteer **publiceren** op het tabblad **publiceren** om de Webtaak opnieuw te publiceren met de bijgewerkte instellingen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de webjobs SDK](webjobs-sdk-how-to.md)