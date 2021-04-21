---
title: Preview Logic Apps werkstromen maken in Visual Studio Code
description: Werkstromen bouwen en uitvoeren voor automatiserings- en integratiescenario's in Visual Studio Code met de extensie Azure Logic Apps (preview).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 4010f7e2d0d20216107a45109056478694c940ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772501"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Stateful en stateless werkstromen maken in Visual Studio Code met de extensie Azure Logic Apps (preview)

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met [Azure Logic Apps Preview](logic-apps-overview-preview.md)kunt u automatiserings- en integratieoplossingen bouwen voor apps, gegevens, cloudservices en systemen door logische apps te maken en uit te voeren die [ *stateful* en *stateless*](logic-apps-overview-preview.md#stateful-stateless) werkstromen in Visual Studio Code bevatten met behulp van de extensie Azure Logic Apps (preview). Met behulp van dit nieuwe type logische app kunt u meerdere werkstromen bouwen die powered by de opnieuw ontworpen Azure Logic Apps Preview-runtime, die draagbaarheid, betere prestaties en flexibiliteit biedt voor het implementeren en uitvoeren in verschillende hostingomgevingen, niet alleen Azure, maar ook Docker-containers. Zie Overzicht voor Azure Logic Apps Preview voor meer informatie over [het nieuwe type logische app.](logic-apps-overview-preview.md)

![Schermopname van Visual Studio Code, een logic app-project en een werkstroom.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

In Visual Studio Code kunt u beginnen met het maken  van een project waarin u de werkstromen van uw logische app lokaal kunt bouwen en uitvoeren in uw ontwikkelomgeving met behulp van de extensie Azure Logic Apps (preview). Hoewel u ook kunt beginnen met het maken van een nieuwe resource voor een logische [app **(preview)** in](create-stateful-stateless-workflows-azure-portal.md)de Azure Portal, bieden beide benaderingen u de mogelijkheid om uw logische app te implementeren en uit te voeren in hetzelfde soort hostingomgevingen.

Ondertussen kunt u nog steeds het oorspronkelijke type logische app maken. Hoewel de ontwikkelingservaringen in Visual Studio Code verschillen tussen de oorspronkelijke en nieuwe typen logische apps, kan uw Azure-abonnement beide typen bevatten. U kunt alle geïmplementeerde logische apps in uw Azure-abonnement bekijken en openen, maar de apps zijn ingedeeld in hun eigen categorieën en secties.

In dit artikel wordt beschreven hoe u uw logische app en een werkstroom in Visual Studio Code maakt met behulp van de extensie Azure Logic Apps (preview) en deze taken op hoog niveau uitvoert:

* Maak een project voor uw logische app en werkstroom.

* Voeg een trigger en een actie toe.

* Lokaal uitvoeren, testen, fouten opsporen en de geschiedenis van de run controleren.

* Zoek de domeinnaamgegevens voor toegang tot de firewall.

* Implementeer in Azure. Dit omvat optioneel het inschakelen Application Insights.

* Beheer uw geïmplementeerde logische app in Visual Studio Code en de Azure Portal.

* Schakel de run history in voor staatloze werkstromen.

* Schakel de toepassing in of Application Insights na de implementatie.

* Implementeer in een Docker-container die u overal kunt uitvoeren.

> [!NOTE]
> Voor informatie over huidige bekende problemen, bekijkt u de Logic Apps public preview Known Issues (Bekende problemen met openbare [preview) in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Vereisten

### <a name="access-and-connectivity"></a>Toegang en connectiviteit

* Toegang tot internet, zodat u de vereisten kunt downloaden, vanuit Visual Studio Code verbinding kunt maken met uw Azure-account en vanuit Visual Studio Code kunt publiceren naar Azure, een Docker-container of een andere omgeving.

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Als u dezelfde logische voorbeeld-app in dit artikel wilt maken, hebt u een e-mailaccount van Office 365 Outlook nodig dat gebruikmaakt van een werk- of schoolaccount van Microsoft om u aan te melden.

  Als u ervoor kiest om een andere e-mailconnector te gebruiken die wordt ondersteund door [Azure Logic Apps,](/connectors/)zoals Outlook.com of [Gmail,](../connectors/connectors-google-data-security-privacy-policy.md)kunt u het voorbeeld nog steeds volgen en zijn de algemene stappen hetzelfde, maar uw gebruikersinterface en opties kunnen op een aantal manieren verschillen. Als u bijvoorbeeld de connector Outlook.com, gebruikt u uw persoonlijke Microsoft-account om u aan te melden.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Opslagvereisten

#### <a name="windows"></a>Windows

Als u uw logische app-project lokaal wilt bouwen en uitvoeren in Visual Studio Code wanneer u Windows gebruikt, volgt u deze stappen om de Emulator Azure Storage instellen:

1. Download en installeer [Azure Storage Emulator 5.10.](https://go.microsoft.com/fwlink/p/?linkid=717179)

1. Als u er nog geen hebt, moet u een lokale SQL DB-installatie hebben, zoals de gratis [SQL Server 2019 Express Edition,](https://go.microsoft.com/fwlink/p/?linkid=866658)zodat de emulator kan worden uitgevoerd.

   Zie Use [the Azure Storage emulator for development and testing (De emulator gebruiken voor ontwikkeling en testen) voor meer informatie.](../storage/common/storage-use-emulator.md)

1. Voordat u uw project kunt uitvoeren, moet u de emulator starten.

   ![Schermopname van de Azure Storage emulator wordt uitgevoerd.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS en Linux

Als u uw logische app-project lokaal wilt bouwen en uitvoeren in Visual Studio Code wanneer u macOS of Linux gebruikt, volgt u deze stappen om een Azure Storage maken en instellen.

> [!NOTE]
> Momenteel werkt de ontwerpfunctie in Visual Studio Code niet in het Linux-besturingssysteem, maar u kunt nog steeds logische apps die gebruikmaken van de Logic Apps Preview-runtime, bouwen, uitvoeren en implementeren op virtuele Linux-machines. Op dit moment kunt u uw logische apps bouwen in Visual Studio Code in Windows of macOS en vervolgens implementeren op een virtuele Linux-machine.

1. Meld u aan [bij Azure Portal](https://portal.azure.com)en maak [een Azure Storage account.](../storage/common/storage-account-create.md?tabs=azure-portal)Dit is een [vereiste voor Azure Functions](../azure-functions/storage-considerations.md).

1. Selecteer in het menu van het opslagaccount **onder Instellingen** de optie **Toegangssleutels.**

1. Zoek en **kopieer in het** deelvenster Toegangssleutels de opslagaccountsleutels connection string, die er ongeveer als in dit voorbeeld uitziet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Schermopname van de Azure Portal met toegangssleutels voor het opslagaccount en connection string gekopieerd.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Bekijk Sleutels voor opslagaccounts beheren [voor meer informatie.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Sla de connection string op een veilige plek. Nadat u uw logische app-project in Visual Studio Code hebt maken, moet u de tekenreeks toevoegen aan de **local.settings.js** in het bestand in de hoofdmap van uw project.

   > [!IMPORTANT]
   > Als u van plan bent te implementeren naar een Docker-container, moet u deze connection string ook gebruiken met het Docker-bestand dat u voor de implementatie gebruikt. Zorg er voor productiescenario's voor dat u dergelijke geheimen en gevoelige informatie beveiligt en beveiligt, bijvoorbeeld met behulp van een sleutelkluis.
  
### <a name="tools"></a>Hulpprogramma's

* [Visual Studio code 1.30.1 (januari 2019)](https://code.visualstudio.com/)of hoger, die gratis is. Download en installeer deze hulpprogramma's voor Visual Studio Code, als u deze nog niet hebt:

  * [Azure-accountextensie,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)die één algemene ervaring biedt voor het filteren van Azure-aanmeldingen en -abonnementen voor alle andere Azure-extensies in Visual Studio Code.

  * [C# voor Visual Studio Code-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), waarmee F5-functionaliteit uw logische app kan uitvoeren.

  * [Azure Functions Core Tools versie 3.0.3245 of hoger](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) met behulp van de MSI-versie (Microsoft Installer). Dit is `func-cli-3.0.3245-x*.msi` .

    Deze hulpprogramma's bevatten een versie van dezelfde runtime die de Azure Functions runtime aan, die door de preview-extensie wordt gebruikt in Visual Studio Code.

    > [!IMPORTANT]
    > Als u een eerdere installatie dan deze versies hebt, verwijdert u die versie eerst of zorgt u ervoor dat de omgevingsvariabele PATH naar de versie wijst die u downloadt en installeert.

  * [Azure Logic Apps (preview)-extensie voor Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Deze extensie biedt u de mogelijkheid om logische apps te maken waar u stateful en stateless werkstromen kunt bouwen die lokaal worden uitgevoerd in Visual Studio Code en deze logische apps vervolgens rechtstreeks naar Azure of naar Docker-containers kunt implementeren.

    Op dit moment kunt u zowel de oorspronkelijke extensie Azure Logic Apps als de openbare preview-extensie installeren in Visual Studio Code. Hoewel de ontwikkelingservaringen tussen de extensies op een aantal manieren verschillen, kan uw Azure-abonnement beide typen logische apps bevatten die u met de extensies maakt. Visual Studio Code toont alle geïmplementeerde logische apps in uw Azure-abonnement, maar organiseert deze in verschillende secties op extensienamen, **Logic Apps** **en Azure Logic Apps (preview)**.

    > [!IMPORTANT]
    > Als u logische app-projecten hebt gemaakt met de eerdere private preview-extensie, werken deze projecten niet met de openbare preview-extensie. U kunt deze projecten echter migreren nadat u de persoonlijke preview-extensie hebt verwijderd, de bijbehorende bestanden hebt verwijderd en de openbare preview-extensie hebt geïnstalleerd. Vervolgens maakt u een nieuw project in Visual Studio Code en kopieert u het bestand **workflow.definition** van uw eerder gemaakte logische app naar uw nieuwe project. Zie Migreren vanuit de [persoonlijke preview-extensie voor meer informatie.](#migrate-private-preview)
    > 
    > Als u logische app-projecten hebt gemaakt met de eerdere openbare preview-extensie, kunt u deze projecten blijven gebruiken zonder migratiestappen.

    **Als u de **extensie Azure Logic Apps (preview)** wilt installeren, volgt u deze stappen:**

    1. In Visual Studio Code selecteert u extensies op de **linkerwerkbalk.**

    1. Voer in het zoekvak voor extensies `azure logic apps preview` in. Selecteer in de lijst met resultaten **Azure Logic Apps (preview)** **>** **Installeren.**

       Nadat de installatie is voltooid, wordt de preview-extensie weergegeven in de lijst **Extensies:** geïnstalleerd.

       ![Schermopname van Visual Studio de lijst met geïnstalleerde extensies van Code met de extensie 'Azure Logic Apps (preview)' onderstreept.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Als de extensie niet wordt weergegeven in de geïnstalleerde lijst, start u de Visual Studio code opnieuw.

* Als u de [actie Inline Code Operations](../logic-apps/logic-apps-add-run-inline-code.md) met JavaScript wilt gebruiken, installeert uNode.js versies [ 10.x.x, 11.x.x of 12.x.x.](https://nodejs.org/en/download/releases/).

  > [!TIP] 
  > Download voor Windows de MSI-versie. Als u in plaats daarvan de ZIP-versie gebruikt, moet u deze handmatig Node.js met behulp van een PATH-omgevingsvariabele voor uw besturingssysteem.

* Als u op webhook gebaseerde triggers en acties, zoals de [ingebouwde HTTP Webhook-trigger,](../connectors/connectors-native-webhook.md)lokaal wilt uitvoeren in Visual Studio Code, moet u doorsturen instellen voor de [callback-URL](#webhook-setup).

* Als u de voorbeeldlogica-app wilt testen die u in dit artikel maakt, hebt u een hulpprogramma nodig dat aanroepen naar de aanvraagtrigger kan verzenden. Dit is de eerste stap in de voorbeeldlogica-app. Als u niet zo'n hulpprogramma hebt, kunt u Postman downloaden, installeren [en gebruiken.](https://www.postman.com/downloads/)

* Als u uw logische app maakt en implementeert met instellingen die ondersteuning bieden voor [Application Insights,](../azure-monitor/app/app-insights-overview.md)kunt u desgewenst diagnostische logboekregistratie en tracering inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app implementeert vanuit Visual Studio Code of na de implementatie. U moet een Application Insights hebben, maar u kunt deze resource vooraf [maken,](../azure-monitor/app/create-workspace-resource.md)wanneer u uw logische app implementeert of na de implementatie.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migreren vanuit persoonlijke preview-extensie

Alle logische app-projecten die u met de **extensie Azure Logic Apps (private preview)** hebt gemaakt, werken niet met de openbare preview-extensie. U kunt deze projecten echter migreren naar nieuwe projecten door de volgende stappen uit te voeren:

1. Verwijder de persoonlijke preview-extensie.

1. Verwijder alle bijbehorende extensiebundels en NuGet-pakketmappen op deze locaties:

   * De **map Microsoft.Azure.Functions.ExtensionBundle.Workflows,** die eerdere extensiebundels bevat en zich op beide paden bevindt:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * De **map microsoft.azure.workflows.webjobs.extension.** Dit is de [NuGet-cache](/nuget/what-is-nuget) voor de persoonlijke preview-extensie en bevindt zich op dit pad:

     `C:\Users\{userName}\.nuget\packages`

1. Installeer de **extensie Azure Logic Apps (preview).**

1. Maak een nieuw project in Visual Studio Code.

1. Kopieer het bestand **workflow.definition** van uw eerder gemaakte logische app naar uw nieuwe project.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code instellen

1. Als u ervoor wilt zorgen dat alle extensies correct zijn geïnstalleerd, laadt u de Visual Studio code opnieuw.

1. Controleer of Visual Studio code extensie-updates automatisch zoekt en installeert, zodat uw Preview-extensie de meest recente updates ontvangt. Anders moet u de verouderde versie handmatig verwijderen en de nieuwste versie installeren.

   1. Ga in **het** menu Bestand naar Instellingen **voor** **>** **voorkeuren.**

   1. Ga op **het tabblad** Gebruiker naar **Onderdelenextensies.** **>** 

   1. Controleer of **Updates automatisch controleren** en Automatisch **bijwerken** zijn geselecteerd.

Bovendien zijn de volgende instellingen standaard ingeschakeld en ingesteld voor de preview Logic Apps extensie:

* **Azure Logic Apps V2: Project Runtime,** dat is ingesteld op versie **~3**

  > [!NOTE]
  > Deze versie is vereist voor het gebruik van [de Inline Code Operations-acties](../logic-apps/logic-apps-add-run-inline-code.md).

* **Azure Logic Apps V2: Experimental View Manager,** waarmee de nieuwste ontwerpfunctie in Visual Studio Code. Als u problemen hebt met de ontwerpfunctie, zoals het slepen en verwijderen van items, moet u deze instelling uitschakelen.

Volg deze stappen om deze instellingen te zoeken en te bevestigen:

1. Ga in **het** menu  Bestand naar **>** **VoorkeurenInstellingen.**

1. Ga op **het** tabblad Gebruiker naar **>** **Extensies** **>** **Azure Logic Apps (preview)**.

   U kunt bijvoorbeeld de instelling Azure Logic Apps **V2: Project Runtime** vinden of het zoekvak gebruiken om andere instellingen te vinden:

   ![Schermopname van Visual Studio Code-instellingen voor de extensie Azure Logic Apps (preview).](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

1. Selecteer op Visual Studio codeactiviteitbalk het pictogram Azure.

   ![Schermopname met Visual Studio codeactiviteitbalk en het geselecteerde Azure-pictogram.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Selecteer in het deelvenster Azure onder **Azure: Logic Apps (preview)** de **optie Aanmelden bij Azure.** Wanneer de Visual Studio Code-verificatie wordt weergegeven, meld u zich aan met uw Azure-account.

   ![Schermopname van het Azure-deelvenster en de geselecteerde koppeling voor aanmelden bij Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Nadat u zich hebt aanmelden, worden in het deelvenster Azure de abonnementen in uw Azure-account weergegeven. Als u ook de openbaar uitgebrachte extensie hebt, kunt u logische apps die u met die extensie hebt gemaakt, vinden in de **sectie Logic Apps,** niet in de **sectie Logic Apps (preview).**
   
   Als de verwachte abonnementen niet worden weergegeven of als u wilt dat in het deelvenster alleen specifieke abonnementen worden weergegeven, volgt u deze stappen:

   1. Verplaats in de lijst met abonnementen de aanwijzer naast het eerste abonnement totdat de knop Abonnementen **selecteren** (filterpictogram) wordt weergegeven. Selecteer het filterpictogram.

      ![Schermopname van het Azure-deelvenster en het geselecteerde filterpictogram.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Of selecteer uw Azure-account Visual Studio de statusbalk van de code. 

   1. Wanneer er een andere lijst met abonnementen wordt weergegeven, selecteert u de abonnementen die u wilt en zorgt u ervoor dat u **OK selecteert.**

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Een lokaal project maken

Voordat u uw logische app kunt maken, maakt u een lokaal project zodat u uw logische app kunt beheren, uitvoeren en implementeren vanuit Visual Studio Code. Het onderliggende project is vergelijkbaar met een Azure Functions project, ook wel een functie-app-project genoemd. Deze projecttypen staan echter los van elkaar, zodat logische apps en functie-apps niet in hetzelfde project kunnen bestaan.

1. Maak op uw computer een *lege lokale* map om te gebruiken voor het project dat u later in Visual Studio code.

1. Sluit Visual Studio alle geopende mappen in Visual Studio Code.

1. Selecteer in het deelvenster Azure naast **Azure: Logic Apps (preview)** de optie **Nieuw project maken** (pictogram met een map en bliksemschicht).

   ![Schermopname van de werkbalk van het Azure-deelvenster met 'Nieuw project maken' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Als Windows Defender Firewall u vraagt om netwerktoegang te verlenen voor , wat Visual Studio Code is, en voor , wat de Azure Functions Core Tools is, selecteert u Particuliere netwerken, zoals Mijn thuisnetwerk of werknetwerk Toegang `Code.exe` `func.exe`  **>** **toestaan.**

1. Blader naar de locatie waar u de projectmap hebt gemaakt, selecteer die map en ga door.

   ![Schermopname van het dialoogvenster Map selecteren met een nieuw gemaakte projectmap en de knop Selecteren geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Selecteer stateful workflow of **Stateless Workflow** in de lijst met sjablonen die **wordt weergegeven.** In dit voorbeeld wordt **Stateful Workflow geselecteerd.**

   ![Schermopname van de lijst met werkstroomsjablonen met 'Stateful Workflow' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Geef een naam op voor uw werkstroom en druk op Enter. In dit voorbeeld wordt `Fabrikam-Stateful-Workflow` gebruikt als de naam.

   ![Schermopname van het vak 'Nieuwe stateful werkstroom maken (3/4)' en 'Fabrikam-Stateful-Workflow' als werkstroomnaam.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code is klaar met het maken van uw project en opent deworkflow.js **voor** uw werkstroom in de code-editor.

   > [!NOTE]
   > Als u wordt gevraagd om te selecteren hoe u uw project wilt openen, selecteert u **Openen in** het huidige venster als u uw project wilt openen in het huidige Visual Studio Code-venster. Als u een nieuw exemplaar voor Visual Studio Code wilt openen, **selecteert u Openen in nieuw venster**.

1. Open op Visual Studio werkbalk het deelvenster Explorer, als dit nog niet is geopend.

   In het deelvenster Explorer ziet u uw project, dat nu automatisch gegenereerde projectbestanden bevat. Het project heeft bijvoorbeeld een map met de naam van uw werkstroom. In deze map bevatworkflow.js **bestand de** onderliggende JSON-definitie van uw werkstroom.

   ![Schermopname van het deelvenster Explorer met projectmap, werkstroommap en workflow.jsbestand.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Als u macOS of Linux gebruikt, stelt u toegang tot uw opslagaccount in door de volgende stappen uit te voeren. Deze stappen zijn vereist voor het lokaal uitvoeren van uw project:

   1. Open in de hoofdmap van uw project delocal.settings.js **bestand.**

      ![Schermopname van het deelvenster Explorer en het bestand 'local.settings.jsaan' in uw project.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Vervang de waarde van de eigenschap door de connection string die u eerder `AzureWebJobsStorage` hebt opgeslagen, bijvoorbeeld:

      Voor:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Na:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

      > [!IMPORTANT]
      > Zorg er voor productiescenario's voor dat u dergelijke geheimen en gevoelige informatie beveiligt en beveiligt, bijvoorbeeld met behulp van een sleutelkluis.

   1. Wanneer u klaar bent, moet u de wijzigingen opslaan.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Ingebouwde connectors maken inschakelen

U kunt uw eigen ingebouwde connectors maken voor elke service die u nodig hebt met behulp van het framework voor extensibility van de [preview-versie.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) Net als bij ingebouwde connectors, zoals Azure Service Bus en SQL Server, bieden deze connectors een hogere doorvoer, lage latentie, lokale connectiviteit en worden ze in hetzelfde proces uitgevoerd als de preview-runtime.

De ontwerpmogelijkheid is momenteel alleen beschikbaar in Visual Studio Code, maar is niet standaard ingeschakeld. Als u deze connectors wilt maken, moet u eerst uw project converteren van extensiebundels (Node.js) naar NuGet-pakketgebaseerd (.NET).

> [!IMPORTANT]
> Deze actie is een een way-bewerking die u niet ongedaan kunt maken.

1. Beweeg in het deelvenster Explorer in de hoofdmap van uw project de muisaanwijzer over een leeg gebied onder alle andere bestanden en mappen, open het snelmenu en selecteer Converteren naar op **Nuget gebaseerd Logic App-project**.

   ![Schermopname van het deelvenster Explorer met het snelmenu van het project geopend vanuit een leeg gebied in het projectvenster.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Wanneer de prompt wordt weergegeven, bevestigt u de projectconversie.

1. Als u wilt doorgaan, bekijkt en volgt u de stappen in Azure Logic Apps [Running Anywhere - Built-in connector extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Open het werkstroomdefinitiebestand in de ontwerpfunctie

1. Controleer de versies die op uw computer zijn geïnstalleerd door deze opdracht uit te voeren:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Als u een .NET Core SDK versie 5.x hebt, kan deze versie verhinderen dat u de onderliggende werkstroomdefinitie van de logische app opent in de ontwerpfunctie. In plaats van deze versie te verwijderen, maakt u in de hoofdmap van uw project een **global.jsop** bestand dat verwijst naar de .NET Core runtime 3.x-versie die u later hebt dan 3.1.201, bijvoorbeeld:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Zorg ervoor dat u  het bestand explicietglobal.jstoevoegt in de hoofdmap van uw project vanuit Visual Studio Code. Anders wordt de ontwerpfunctie niet geopend.

1. Vouw de projectmap voor uw werkstroom uit. Open de **workflow.jsin** het snelmenu van het bestand en selecteer Openen **in Designer.**

   ![Schermopname van het deelvenster Explorer en het snelkoppelingsvenster voor de workflow.jsin het bestand met 'Openen in designer' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Selecteer in de lijst **Connectors in Azure** inschakelen de optie **Connectors** van Azure gebruiken. Deze optie is van toepassing op alle beheerde connectors die beschikbaar zijn en zijn geïmplementeerd in Azure, niet alleen connectors voor Azure-services.

   ![Schermopname van het deelvenster Explorer waarin de lijst Connectors in Azure inschakelen is geopend en 'Connectors gebruiken vanuit Azure' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Staatloze werkstromen ondersteunen momenteel alleen *acties* voor [beheerde connectors,](../connectors/managed.md)die zijn geïmplementeerd in Azure, en niet voor triggers. Hoewel u connectors in Azure kunt inschakelen voor uw staatloze werkstroom, geeft de ontwerpfunctie geen beheerde connectortriggers weer die u kunt selecteren.

1. Selecteer in **de lijst** Abonnement selecteren het Azure-abonnement dat u wilt gebruiken voor uw logische app-project.

   ![Schermopname van het deelvenster Explorer met het vak Abonnement selecteren en uw abonnement geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Selecteer Nieuwe resourcegroep maken in **de lijst met resourcegroepen.**

   ![Schermopname van het deelvenster Explorer met de lijst met resourcegroepen en 'Nieuwe resourcegroep maken' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Geef een naam op voor de resourcegroep en druk op Enter. In dit voorbeeld wordt `Fabrikam-Workflows-RG` gebruikt.

   ![Schermopname van het deelvenster Explorer en het vak resourcegroepnaam.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Zoek en selecteer in de lijst met locaties de Azure-regio die u wilt gebruiken bij het maken van uw resourcegroep en resources. In dit voorbeeld wordt **VS - west-centraal gebruikt.**

   ![Schermopname van het deelvenster Explorer met de lijst met locaties en 'VS - west-centraal' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Nadat u deze stap hebt Visual Studio code de ontwerpfunctie voor werkstromen geopend.

   > [!NOTE]
   > Wanneer Visual Studio Code de API voor werkstroomontwerp start, krijgt u mogelijk een bericht dat het opstarten enkele seconden kan duren. U kunt dit bericht negeren of **OK selecteren.**
   >
   > Als de ontwerpfunctie niet wordt geopend, bekijkt u de sectie Probleemoplossing. [Designer kan niet openen.](#designer-fails-to-open)

   Nadat de ontwerpfunctie wordt weergegeven, wordt de **prompt** Een bewerking kiezen weergegeven in de ontwerpfunctie en is deze standaard geselecteerd. Het deelvenster Een actie **toevoegen wordt** weergegeven.

   ![Schermopname van de ontwerpfunctie voor werkstromen.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Voeg vervolgens [een trigger en acties toe](#add-trigger-actions) aan uw werkstroom.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Een trigger en acties toevoegen

Nadat u de ontwerpfunctie hebt geopend, wordt de prompt Een **bewerking** kiezen weergegeven in de ontwerpfunctie en is deze standaard geselecteerd. U kunt nu beginnen met het maken van uw werkstroom door een trigger en acties toe te voegen.

De werkstroom in dit voorbeeld maakt gebruik van deze trigger en deze acties:

* De ingebouwde [aanvraagtrigger](../connectors/connectors-native-reqres.md), Wanneer een **HTTP-aanvraag** wordt ontvangen, die binnenkomende aanroepen of aanvragen ontvangt en een eindpunt maakt dat andere services of logische apps kunnen aanroepen.

* De [office 365 Outlook-actie](../connectors/connectors-create-api-office365-outlook.md), **een e-mail verzenden.**

* De ingebouwde [responsactie ,](../connectors/connectors-native-reqres.md)die u gebruikt om een antwoord te verzenden en gegevens terug te sturen naar de aanroeper.

### <a name="add-the-request-trigger"></a>De aanvraagtrigger toevoegen

1. Zorg er naast de ontwerpfunctie in het  deelvenster Een **trigger** toevoegen onder het zoekvak Een bewerking kiezen voor dat **Ingebouwd** is geselecteerd, zodat u een trigger kunt selecteren die standaard wordt uitgevoerd.

1. Voer in **het zoekvak** Een bewerking kiezen in en selecteer de ingebouwde aanvraagtrigger met de naam `when a http request` Wanneer een **HTTP-aanvraag wordt ontvangen.**

   ![Schermopname van de werkstroomontwerper en het deelvenster **Een trigger toevoegen** met de trigger 'Wanneer een HTTP-aanvraag wordt ontvangen' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Wanneer de trigger wordt weergegeven in de ontwerpfunctie, wordt het detailvenster van de trigger geopend om de eigenschappen, instellingen en andere acties van de trigger weer te geven.

   ![Schermopname van de werkstroomontwerper met de trigger Wanneer een HTTP-aanvraag is ontvangen geselecteerd en het deelvenster Details activeren is geopend.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Als het detailvenster niet wordt weergegeven, zorgt u ervoor dat de trigger is geselecteerd in de ontwerpfunctie.

1. Als u een item uit de ontwerpfunctie wilt verwijderen, volgt u deze stappen voor het verwijderen [van items uit de ontwerpfunctie](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>De actie Office 365 Outlook toevoegen

1. Selecteer in de ontwerpfunctie onder de trigger die u hebt toegevoegd de **optie Nieuwe stap.**

   De **prompt Een bewerking** kiezen wordt weergegeven  in de ontwerpfunctie en het deelvenster Een actie toevoegen wordt opnieuw geopend, zodat u de volgende actie kunt selecteren.

1. Selecteer in **het deelvenster** Een  actie toevoegen onder het zoekvak Een bewerking kiezen de optie **Azure,** zodat u een actie kunt vinden en selecteren voor een beheerde connector die in Azure is geïmplementeerd.

   In dit voorbeeld wordt de office 365 Outlook-actie Een e-mail verzenden **(V2) geselecteerd en gebruikt.**

   ![Schermopname van de werkstroomontwerper en het deelvenster **Een actie toevoegen** met de actie 'Een e-mail verzenden' van Office 365 Outlook geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Selecteer aanmelden in het detailvenster van de **actie,** zodat u verbinding kunt maken met uw e-mailaccount.

   ![Schermopname van de werkstroomontwerper en het deelvenster **Een e-mail verzenden (V2)** met 'Aanmelden' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Wanneer Visual Studio code u vraagt om toestemming voor toegang tot uw e-mailaccount, selecteert u **Openen.**

   ![Schermopname van de Visual Studio codeprompt om toegang toe te staan.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Als u toekomstige vragen wilt voorkomen, selecteert u Vertrouwde domeinen **configureren** zodat u de verificatiepagina als een vertrouwd domein kunt toevoegen.

1. Volg de volgende prompts om u aan te melden, toegang te verlenen en terug te keren naar Visual Studio Code.

   > [!NOTE]
   > Als er te veel tijd verstrijkt voordat u de prompts voltooit, teert het verificatieproces een time-out en mislukt het. In dit geval gaat u terug naar de ontwerpfunctie en doet u zich opnieuw aan om de verbinding te maken.

1. Wanneer de extensie Azure Logic Apps (preview) u vraagt om toestemming voor toegang tot uw e-mailaccount, selecteert **u Openen.** Volg de volgende prompt om toegang toe te staan.

   ![Schermopname van de preview-extensieprompt om toegang toe te staan.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Als u toekomstige prompts wilt voorkomen, **selecteert u Niet opnieuw vragen om deze extensie.**

   Nadat Visual Studio code uw verbinding heeft gemaakt, wordt in sommige connectors het bericht weergegeven dat `The connection will be valid for {n} days only` . Deze tijdslimiet is alleen van toepassing op de duur tijdens het schrijven van uw logische app in Visual Studio Code. Na de implementatie is deze limiet niet meer van toepassing omdat uw logische app tijdens runtime kan worden geverifieerd met behulp van de automatisch ingeschakelde door het systeem [toegewezen beheerde identiteit](../logic-apps/create-managed-service-identity.md). Deze beheerde identiteit wijkt af van de verificatiereferenties of connection string u gebruikt wanneer u een verbinding maakt. Als u deze door het systeem toegewezen beheerde identiteit uit schakelen, werken verbindingen niet tijdens runtime.

1. Als in de ontwerpfunctie de **actie Een e-mail** verzenden niet wordt weergegeven geselecteerd, selecteert u die actie.

1. Geef in het detailvenster van de actie op **het tabblad Parameters** de vereiste informatie voor de actie op, bijvoorbeeld:

   ![Schermopname van de werkstroomontwerper met details voor de actie 'Een e-mail verzenden' van Office 365 Outlook.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | De ontvanger van de e-mail. Dit kan uw e-mailadres zijn voor testdoeleinden. In dit voorbeeld wordt het fictieve e-mailbericht gebruikt, `sophiaowen@fabrikam.com` . |
   | **Onderwerp** | Ja | `An email from your example workflow` | Het onderwerp van de e-mail |
   | **Hoofdtekst** | Ja | `Hello from your example workflow!` | De inhoud van de e-mail |
   ||||

   > [!NOTE]
   > Als u wijzigingen wilt aanbrengen in het detailvenster op  het tabblad **Instellingen,** Statisch  resultaat of Uitvoeren na, moet u Klaar selecteren om deze wijzigingen door te voeren voordat u overschakelt naar tabbladen of de focus wijzigt in de ontwerpfunctie.  Anders blijven Visual Studio wijzigingen niet behouden in uw code. Bekijk voor meer informatie de pagina [Logic Apps bekende problemen met openbare preview in GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

1. Selecteer Opslaan in de **ontwerpfunctie.**

> [!IMPORTANT]
> Als u een werkstroom lokaal wilt uitvoeren die gebruikmaakt van een webhooktrigger of -acties, zoals de [ingebouwde HTTP-webhooktrigger of -actie,](../connectors/connectors-native-webhook.md)moet u deze mogelijkheid inschakelen door doorsturen in te stellen voor de [callback-URL](#webhook-setup)van de webhook.

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Lokaal uitvoerende webhooks inschakelen

Wanneer u een trigger of actie op basis van een webhook gebruikt, zoals **HTTP Webhook,** met een logische app die wordt uitgevoerd in Azure, abonneert de Logic Apps-runtime zich op het service-eindpunt door een callback-URL met dat eindpunt te genereren en te registreren. De trigger of actie wacht vervolgens tot het service-eindpunt de URL aanroept. Wanneer u echter in Visual Studio Code werkt, begint de gegenereerde callback-URL met `http://localhost:7071/...` . Deze URL is voor uw localhost-server, die privé is, zodat het service-eindpunt deze URL niet kan aanroepen.

Als u op webhook gebaseerde triggers en acties lokaal wilt uitvoeren in Visual Studio Code, moet u een openbare URL instellen die uw localhost-server beschikbaar maakt en veilig aanroepen van het service-eindpunt doorsturen naar de callback-URL van de webhook. U kunt een doorstabiliteitsservice en hulpprogramma gebruiken, zoals [**ngrok**](https://ngrok.com/), waarmee een HTTP-tunnel naar uw localhost-poort wordt geopend, of u kunt uw eigen hulpprogramma gebruiken.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Call Forwarding instellen met **behulp van ngrok**

1. [Meld u aan voor **een ngrok-account**](https://dashboard.ngrok.com/signup) als u er nog geen hebt. Meld u [anders aan bij uw account](https://dashboard.ngrok.com/login).

1. Haal uw persoonlijke verificatie token op, dat uw **ngrok-client** nodig heeft om verbinding te maken en toegang tot uw account te verifiëren.

   1. Als u de pagina [met uw verificatietoken wilt vinden,](https://dashboard.ngrok.com/auth/your-authtoken)vouwt u in het menu van het accountdashboard **de** optie Verificatie uit en selecteert u **Uw verificatietoken.**

   1. Kopieer in **het vak Uw authtoken** het token naar een veilige locatie.

1. Download op [ **de ngrok-downloadpagina**](https://ngrok.com/download) of uw [accountdashboard](https://dashboard.ngrok.com/get-started/setup)de **ngrok-versie** die u wilt en extraheert het ZIP-bestand. Zie voor meer informatie [Stap 1: Uitsetsen om te installeren.](https://ngrok.com/download)

1. Open het opdrachtprompthulpprogramma op uw computer. Blader naar de locatie waar u het **ngrok.exe** hebt.

1. Verbind de **ngrok-client** met uw **ngrok-account** door de volgende opdracht uit te voeren. Zie voor meer informatie [Stap 2: verbinding maken met uw account.](https://ngrok.com/download)

   `ngrok authtoken <your_auth_token>`

1. Open de HTTP-tunnel naar localhost-poort 7071 door de volgende opdracht uit te voeren. Zie voor meer informatie [Stap 3: fire it up](https://ngrok.com/download).

   `ngrok http 7071`

1. Zoek in de uitvoer de volgende regel:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Kopieer de URL met deze indeling en sla deze op: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>De doorsturende URL instellen in uw app-instellingen

1. Voeg in Visual Studio Code in de ontwerpfunctie de **http+ webhooktrigger of** -actie toe.

1. Wanneer de prompt wordt weergegeven voor de locatie van het host-eindpunt, voert u de doorsturende URL (omleiding) in die u eerder hebt gemaakt.

   > [!NOTE]
   > Als u de prompt negeert, wordt er een waarschuwing weergegeven dat u de doorsturende URL moet opgeven. Selecteer daarom **Configureren** en voer de URL in. Nadat u deze stap hebt voltooien, wordt de prompt niet opnieuw voor volgende webhooktriggers of acties die u kunt toevoegen.
   >
   > Als u de prompt opnieuw wilt weergeven, opent u op het hoofdniveau van uw project de **local.settings.js** in het snelmenu van het bestand en selecteert u **Configure Webhook Redirect Endpoint**. De prompt wordt nu weergegeven, zodat u de doorsturende URL kunt opgeven.

   Visual Studio Code wordt de doorstijl-URL toegevoegd **aanlocal.settings.jsbestand** in de hoofdmap van uw project. In het `Values` object wordt nu de eigenschap met de naam weergegeven en ingesteld op de `Workflows.WebhookRedirectHostUri` doorsturende URL, bijvoorbeeld:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

De eerste keer dat u een lokale debugging-sessie start of de werkstroom zonder debuggen uit te voeren, registreert de Logic Apps-runtime de werkstroom bij het service-eindpunt en abonneert de Logic Apps zich op dat eindpunt voor het melden van de webhookbewerkingen. De volgende keer dat uw werkstroom wordt uitgevoerd, wordt de runtime niet geregistreerd of opnieuw geregistreerd omdat de abonnementsregistratie al bestaat in de lokale opslag.

Wanneer u de debugging-sessie stopt voor een werkstroom die gebruikmaakt van lokaal uitgevoerd webhook-triggers of -acties, worden de bestaande abonnementsregistraties niet verwijderd. Als u de registratie ongedaan wilt maken, moet u de abonnementsregistraties handmatig verwijderen of verwijderen.

> [!NOTE]
> Nadat de werkstroom is gestart, kan het terminalvenster fouten bevatten zoals in dit voorbeeld:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Open in dit geval de **local.settings.jsbestand** in de hoofdmap van uw project en zorg ervoor dat de eigenschap is ingesteld op `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Onderbrekingspunten voor het debuggen beheren

Voordat u de werkstroom van uw logische app gaat uitvoeren en testen door een debuggingssessie te starten, kunt **u** [onderbrekingspunten](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) instellen in hetworkflow.jsvoor elke werkstroom. Er is geen andere installatie vereist. 

Op dit moment worden onderbrekingspunten alleen ondersteund voor acties, niet voor triggers. Elke actiedefinitie heeft de volgende onderbrekingspuntlocaties:

* Stel het begin-onderbrekingspunt in op de regel met de naam van de actie. Wanneer dit onderbrekingspunt raakt tijdens de debuggingssessie, kunt u de invoer van de actie controleren voordat deze wordt geëvalueerd.

* Stel het eind onderbrekingspunt in op de regel met de afsluitende accolade van de actie (**}**). Wanneer dit onderbrekingspunt raakt tijdens de debuggingssessie, kunt u de resultaten van de actie bekijken voordat de actie is uitgevoerd.

Volg deze stappen om een onderbrekingspunt toe te voegen:

1. Open het **workflow.jsbestand** voor de werkstroom die u wilt debuggen.

1. Selecteer in de linkerkolom op de regel waar u het onderbrekingspunt wilt instellen in die kolom. Als u het onderbrekingspunt wilt verwijderen, selecteert u dat onderbrekingspunt.

   Wanneer u de foutopsporingssessie start, wordt de weergave Uitvoeren weergegeven aan de linkerkant van het codevenster, terwijl de werkbalk Foutopsporing bovenaan wordt weergegeven.

   > [!NOTE]
   > Als de weergave Uitvoeren niet automatisch wordt weergegeven, drukt u op Ctrl+Shift+D.

1. Als u de beschikbare informatie wilt bekijken wanneer een onderbrekingspunt binnen komt, bekijkt u in de weergave Uitvoeren het **deelvenster** Variabelen.

1. Als u wilt doorgaan met de uitvoering van de werkstroom, selecteert u op de werkbalk Fouten opsporen de optie **Doorgaan** (knop Afspelen).

U kunt onderbrekingspunten op elk moment tijdens de werkstroomrun toevoegen en verwijderen. Als u echter deworkflow.js **op het** bestand bij te werken nadat de run is gestart, worden onderbrekingspunten niet automatisch bijgewerkt. Start de logische app opnieuw om de onderbrekingspunten bij te werken.

Zie Onderbrekingspunten - [Visual Studio Code voor algemene informatie.](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Lokaal uitvoeren, testen en fouten opsporen

Als u uw logische app wilt testen, volgt u deze stappen om een debuggingssessie te starten en de URL te vinden voor het eindpunt dat is gemaakt met de aanvraagtrigger. U hebt deze URL nodig, zodat u later een aanvraag naar dat eindpunt kunt verzenden.

1. Als u gemakkelijker fouten in een staatloze werkstroom wilt opsporen, kunt u de [run history voor die werkstroom inschakelen.](#enable-run-history-stateless)

1. Open op Visual Studio codeactiviteitbalk het menu **Uitvoeren** en selecteer **Start Debugging** (F5).

   Het **venster Terminal** wordt geopend, zodat u de sessie voor het debuggen kunt bekijken.

   > [!NOTE]
   > Als de fout 'Fout bestaat na het uitvoeren van **preLaunchTask 'generateDebugSymbols'** wordt weergegeven, gaat u naar de sectie Probleemoplossing, foutopsporingssessie wordt niet [start.](#debugging-fails-to-start)

1. Zoek nu de callback-URL voor het eindpunt op de aanvraagtrigger.

   1. Open het deelvenster Explorer opnieuw, zodat u uw project kunt bekijken.

   1. Selecteer overzicht **workflow.jsin** het snelmenu van **het bestand.**

      ![Schermopname van het deelvenster Explorer en het snelkoppelingsvenster voor de workflow.jsin het bestand met 'Overzicht' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Zoek de **waarde van callback-URL,** die lijkt op deze URL voor de voorbeeld-aanvraagtrigger:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Schermopname van de overzichtspagina van uw werkstroom met callback-URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Als u de callback-URL wilt testen door de werkstroom van de logische app te activeren, opent u [Postman](https://www.postman.com/downloads/) of het hulpprogramma van uw voorkeur voor het maken en verzenden van aanvragen.

   In dit voorbeeld wordt gebruik gemaakt van Postman. Zie Postman Aan de slag voor [meer informatie.](https://learning.postman.com/docs/getting-started/introduction/)

   1. Selecteer Op de Postman-werkbalk de optie **Nieuw.**

      ![Schermopname van Postman met de knop Nieuw geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Selecteer in **het deelvenster Nieuwe** maken onder **Bouwstenen** de optie **Aanvraag.**

   1. Geef in **het venster Aanvraag** opslaan onder **Aanvraagnaam** een naam op voor de aanvraag, bijvoorbeeld `Test workflow trigger` .

   1. Selecteer **onder Een verzameling of map selecteren die u wilt opslaan in** de optie Verzameling **maken.**

   1. Geef **onder Alle verzamelingen** een naam op voor de verzameling die u wilt maken voor het ordenen van uw aanvragen, druk op Enter en selecteer Opslaan <naam van **de *verzameling.* >** In dit voorbeeld wordt `Logic Apps requests` gebruikt als de naam van de verzameling.

      Het aanvraagvenster van Postman wordt geopend, zodat u een aanvraag kunt verzenden naar de callback-URL voor de aanvraagtrigger.

      ![Schermopname van Postman met het geopende aanvraagvenster](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Ga terug naar Visual Studio Code. kopieer op de overzichtspagina van de werkstroom de **eigenschapswaarde van de callback-URL.**

   1. Ga terug naar Postman. Plak in het aanvraagvenster naast de lijst met methoden, die momenteel **GET** als de standaardaanvraagmethode toont, de callback-URL die u eerder hebt gekopieerd in het adresvak en selecteer **Verzenden.**

      ![Schermopname van Postman en callback-URL in het adresvak met de knop Verzenden geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      De voorbeeldwerkstroom van de logische app verzendt een e-mailbericht dat lijkt op het volgende voorbeeld:

      ![Schermopname van Outlook-e-mail zoals beschreven in het voorbeeld](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Ga Visual Studio Code terug naar de overzichtspagina van uw werkstroom.

   Als u een stateful werkstroom hebt gemaakt nadat de aanvraag die u hebt verzonden de werkstroom activeert, wordt op de overzichtspagina de status en geschiedenis van de werkstroom weergegeven.

   > [!TIP]
   > Als de status van de run niet wordt weergegeven, vernieuwt u de overzichtspagina door **Vernieuwen te selecteren.** Er vindt geen run plaats voor een trigger die wordt overgeslagen vanwege niet-voldaan criteria of het vinden van geen gegevens.

   ![Schermopname van de overzichtspagina van de werkstroom met de status en geschiedenis van de run](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Status van de run | Description |
   |------------|-------------|
   | **Aborted** | De run is gestopt of is niet uitgevoerd vanwege externe problemen, bijvoorbeeld een systeemstoring of een beschadigd Azure-abonnement. |
   | **Geannuleerd** | De run is geactiveerd en gestart, maar heeft een annuleringsaanvraag ontvangen. |
   | **Mislukt** | Ten minste één actie in de run is mislukt. Er zijn geen volgende acties in de werkstroom ingesteld om de fout te verwerken. |
   | **Wordt uitgevoerd** | De uitvoering is geactiveerd en wordt uitgevoerd, maar deze status kan ook [](logic-apps-limits-and-config.md) worden weergegeven voor een uitvoering die wordt beperkt vanwege actielimieten of het [huidige prijsplan](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tip:** als u logboekregistratie van [diagnostische gegevens hebt](monitor-logic-apps-log-analytics.md)ingesteld, kunt u informatie krijgen over vertragingsgebeurtenissen die plaatsvinden. |
   | **Geslaagd** | De run is geslaagd. Als een actie is mislukt, heeft een volgende actie in de werkstroom die fout afgehandeld. |
   | **Time-out** | Er is een time-out van de run uitgevoerd omdat de huidige duur de limiet voor de duur van de run heeft overschreden. Dit wordt bepaald door de instelling Run [ **history retention in days**](logic-apps-limits-and-config.md#run-duration-retention-limits). De duur van een run wordt berekend met behulp van de begintijd en duurlimiet van de run op die starttijd. <p><p>**Opmerking:** als de duur van de run ook de retentielimiet voor de huidige run history overschrijdt, wat ook wordt bepaald door de instelling Bewaarperiode van de rungeschiedenis [ **in**](logic-apps-limits-and-config.md#run-duration-retention-limits)dagen, wordt de run door een dagelijkse opschoonactie uit de geschiedenis van de run geweken. Of de run nu een time-out heeft of is voltooid, de retentieperiode wordt altijd berekend met behulp van de begintijd en de *huidige bewaarlimiet* van de run. Dus als u de duurlimiet voor een in-flight run verlaagt, tót er een times-out is voor de run. De run blijft echter of wordt uit de geschiedenis van de run geweerd op basis van of de duur van de run de bewaarlimiet heeft overschreden. |
   | **Wachten** | De run is niet gestart of onderbroken, bijvoorbeeld vanwege een eerdere werkstroom die nog wordt uitgevoerd. |
   |||

1. Als u de statussen voor elke stap in een specifieke run en de in- en uitvoer van de stap wilt controleren, selecteert u de knop met weglatingangen **(...**) voor die run en selecteert u **Uitvoeren weergeven.**

   ![Schermopname van de rij met de run history van uw werkstroom met de knop met weglatingsknop en 'Uitvoeren weergeven' geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code wordt de bewakingsweergave geopend en wordt de status voor elke stap in de run weergegeven.

   ![Schermopname met elke stap in de werkstroomuit voeren en hun status](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Als een run is mislukt en in een stap in de bewakingsweergave de fout wordt weergegeven, kan dit probleem worden veroorzaakt door een langere triggernaam of actienaam waardoor de onderliggende URI (Uniform Resource Identifier) de standaardtekenlimiet `400 Bad Request` overschrijdt. Zie ['400 Bad Request' voor meer informatie.](#400-bad-request)

   Hier ziet u de mogelijke statussen die elke stap in de werkstroom kan hebben:

   | Actiestatus | Pictogram | Description |
   |---------------|------|-------------|
   | **Aborted** | ![Pictogram voor de actiestatus Afgebroken][aborted-icon] | De actie is gestopt of is niet uitgevoerd vanwege externe problemen, bijvoorbeeld een systeemstoring of een beschadigd Azure-abonnement. |
   | **Geannuleerd** | ![Pictogram voor de actiestatus Geannuleerd][cancelled-icon] | De actie werd uitgevoerd, maar er is een aanvraag ontvangen om te annuleren. |
   | **Mislukt** | ![Pictogram voor de actiestatus Mislukt][failed-icon] | De actie is mislukt. |
   | **Wordt uitgevoerd** | ![Pictogram voor de actiestatus 'Wordt uitgevoerd'][running-icon] | De actie wordt momenteel uitgevoerd. |
   | **Overgeslagen** | ![Pictogram voor de actiestatus 'Skipped'][skipped-icon] | De actie is overgeslagen omdat de direct voorgaande actie is mislukt. Een actie heeft een voorwaarde die vereist dat de voorgaande actie is `runAfter` voltooid voordat de huidige actie kan worden uitgevoerd. |
   | **Geslaagd** | ![Pictogram voor de actiestatus Geslaagd][succeeded-icon] | De actie is geslaagd. |
   | **Geslaagd met nieuwe proberen** | ![Pictogram voor de actiestatus Geslaagd met nieuwe acties][succeeded-with-retries-icon] | De actie is geslaagd, maar pas na een of meer nieuwe acties. Als u de geschiedenis van de nieuwe poging wilt bekijken, selecteert u die actie in de weergave details van de uitvoergeschiedenis, zodat u de invoer en uitvoer kunt bekijken. |
   | **Time-out** | ![Pictogram voor de actiestatus 'Time-out'][timed-out-icon] | De actie is gestopt vanwege de time-outlimiet die is opgegeven door de instellingen van die actie. |
   | **Wachten** | ![Pictogram voor de actiestatus Wachten][waiting-icon] | Is van toepassing op een webhookactie die wacht op een inkomende aanvraag van een aanroeper. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Als u de invoer en uitvoer voor elke stap wilt controleren, selecteert u de stap die u wilt inspecteren.

   ![Schermopname van de status voor elke stap in de werkstroom plus de invoer en uitvoer in de uit uitgebreide actie 'Een e-mail verzenden'](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Als u de onbewerkte invoer en uitvoer voor die stap verder wilt bekijken, selecteert u **Onbewerkte** invoer tonen of **Onbewerkte uitvoer tonen.**

1. Als u de debugging-sessie wilt stoppen, selecteert u in het menu **Uitvoeren** de optie **Stop Debugging** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Een antwoord retourneren

Als u een antwoord wilt retourneren aan de aanroeper die een aanvraag naar uw logische app heeft verzonden, kunt u de [ingebouwde](../connectors/connectors-native-reqres.md) actie Antwoord gebruiken voor een werkstroom die begint met de aanvraagtrigger.

1. Selecteer in de werkstroomontwerper onder **de actie Een e-mail** verzenden de **optie Nieuwe stap.**

   De **prompt Een bewerking** kiezen wordt weergegeven  in de ontwerpfunctie en het deelvenster Een actie toevoegen wordt opnieuw geopend, zodat u de volgende actie kunt selecteren.

1. Zorg ervoor **dat ingebouwde is** geselecteerd in **het** deelvenster Een actie toevoegen onder het zoekvak Een **actie** kiezen. Voer in het zoekvak in `response` en selecteer de **actie** Antwoord.

   ![Schermopname van de werkstroomontwerper met de actie Antwoord geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Wanneer de **actie Antwoord** wordt weergegeven in de ontwerpfunctie, wordt het detailvenster van de actie automatisch geopend.

   ![Schermopname van de ontwerpfunctie van de werkstroom met het detailvenster 'Antwoord' geopend en de eigenschap Body ingesteld op de eigenschap 'Een e-mail verzenden' van de eigenschap 'Hoofdwaarde'.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Geef op **het** tabblad Parameters de vereiste informatie op voor de functie die u wilt aanroepen.

   In dit voorbeeld wordt de **eigenschapswaarde Body** die wordt uitgevoerd door de **actie Een e-mail verzenden,** retourneert.

   1. Klik in het **vak Eigenschap** hoofdvak, zodat de lijst met dynamische inhoud wordt weergegeven en de beschikbare uitvoerwaarden van de voorgaande trigger en acties in de werkstroom worden weergegeven.

      ![Schermopname van het detailvenster van de actie 'Antwoord' met de muisaanwijzer in de eigenschap 'Body' zodat de lijst met dynamische inhoud wordt weergegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Selecteer in de lijst met dynamische inhoud onder **Een e-mail verzenden** de optie **Body**.

      ![Schermopname van de lijst met geopende dynamische inhoud. In de lijst, onder de kop 'Een e-mail verzenden', wordt de uitvoerwaarde 'Hoofdtekst' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Wanneer u klaar bent, is de eigenschap Body van  de actie Antwoord  nu ingesteld op de uitvoerwaarde Een e-mailactie verzenden. 

      ![Schermopname met de status van elke stap in de werkstroom plus de invoer en uitvoer in de uitvuitde actie 'Antwoord'.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Selecteer Opslaan in de **ontwerpfunctie.**

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Uw logische app opnieuw testen

Nadat u updates hebt uitgevoerd voor uw logische app, kunt u nog een test uitvoeren door het foutopsporingsygger opnieuw uit te voeren in Visual Studio en een andere aanvraag te verzenden om uw bijgewerkte logische app te activeren, vergelijkbaar met de stappen in Lokaal [uitvoeren,](#run-test-debug-locally)testen en fouten opsporen .

1. Open op Visual Studio codeactiviteitbalk het menu **Uitvoeren** en selecteer **Start Debugging** (F5).

1. Verzend in Postman of uw hulpprogramma voor het maken en verzenden van aanvragen een andere aanvraag om uw werkstroom te activeren.

1. Als u een stateful werkstroom hebt gemaakt, controleert u op de overzichtspagina van de werkstroom de status van de meest recente run. Als u de status, invoer en uitvoer voor elke stap in die run wilt weergeven, selecteert u de knop met weglatingangen (**...**) voor die run en selecteert u **Uitvoeren weergeven.**

   Dit is bijvoorbeeld de stapsgewijse status van een run nadat de voorbeeldwerkstroom is bijgewerkt met de actie Antwoord.

   ![Schermopname met de status van elke stap in de bijgewerkte werkstroom plus de invoer en uitvoer in de uitvuitde actie 'Antwoord'.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Als u de debugging-sessie wilt stoppen, selecteert u in het **menu** Uitvoeren de optie **Stop Debugging** (Shift + F5).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Domeinnamen zoeken voor firewalltoegang

Voordat u de werkstroom van uw logische app implementeert en in de Azure Portal, moet u machtigingen instellen voor trigger- of actieverbindingen die in uw werkstroom bestaan als uw omgeving strikte netwerkvereisten of firewalls heeft die het verkeer beperken.

Volg deze stappen om de FQDN's (Fully Qualified Domain Names) voor deze verbindingen te vinden:

1. Open in uw logische app-project de **connections.json-file,** die wordt gemaakt nadat u de eerste trigger of actie op basis van een verbinding aan uw werkstroom hebt toegevoegd en zoek het `managedApiConnections` -object.

1. Voor elke verbinding die u hebt gemaakt, moet u de eigenschapswaarde op een veilige plaats zoeken, kopiëren en opslaan, zodat u uw firewall met `connectionRuntimeUrl` deze informatie kunt instellen.

   Dit voorbeeld **connections.jsbestand** bevat twee verbindingen: een AS2-verbinding en een Office 365-verbinding met deze `connectionRuntimeUrl` waarden:

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Implementeren op Azure

Vanuit Visual Studio Code kunt u uw project rechtstreeks publiceren naar Azure, waarmee uw logische app wordt geïmplementeerd met behulp van het nieuwe resourcetype Logische app **(preview).** Net als bij de functie-app-resource in Azure Functions, moet u voor de implementatie voor dit nieuwe resourcetype een [hostingplan](../app-service/overview-hosting-plans.md)en prijscategorie selecteren die u tijdens de implementatie kunt instellen. Lees de volgende onderwerpen voor meer informatie over hostingplannen en prijzen:

* [Een in-Azure App Service](../app-service/manage-scale-up.md)
* [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)

U kunt uw logische app publiceren als een nieuwe resource, waarmee automatisch alle benodigde resources worden gemaakt, zoals een Azure Storage-account, vergelijkbaar met de vereisten voor [functie-apps.](../azure-functions/storage-considerations.md) U kunt uw logische app ook publiceren naar een eerder geïmplementeerde **logic app-resource (preview),** die die logische app overschrijft.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publiceren naar een nieuwe logische app-resource (preview)

1. Selecteer op Visual Studio balk codeactiviteit het pictogram Azure.

1. Selecteer implementeren naar logische app Logic Apps de werkbalk van het deelvenster **Azure:** Logic Apps **(preview)**.

   ![Schermopname van het deelvenster 'Azure: Logic Apps (preview)' en de werkbalk van het deelvenster met 'Implementeren naar logische app' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Als u hier om wordt gevraagd, selecteert u het Azure-abonnement dat u wilt gebruiken voor de implementatie van uw logische app.

1. In de lijst die Visual Studio Code wordt geopend, selecteert u een van de volgende opties:

   * **Nieuwe logische app (preview) maken in Azure** (snel)
   * **Nieuwe logische app (preview) maken in Azure Advanced**
   * Een eerder geïmplementeerde **resource voor een logische app (preview),** indien aanwezig

   Dit voorbeeld gaat verder met **Nieuwe logische app maken (preview) in Azure Advanced.**

   ![Schermopname van het deelvenster 'Azure: Logic Apps (preview)' met een lijst met 'Nieuwe logische app maken (preview) in Azure' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Volg deze stappen om uw nieuwe **logische app-resource (preview)** te maken:

   1. Geef een wereldwijd unieke naam op voor uw nieuwe logische app. Dit is de naam die moet worden gebruikt voor de resource van de logische app **(preview).** In dit voorbeeld wordt `Fabrikam-Workflows-App` gebruikt.

      ![Schermopname van het deelvenster 'Azure: Logic Apps (preview)' en een prompt om een naam op te geven voor de nieuwe logische app die moet worden gemaakt.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selecteer een [hostingplan](../app-service/overview-hosting-plans.md) voor uw nieuwe logische app, [ **App Service Abonnement** (toegewezen)](../azure-functions/dedicated-plan.md) of [**Premium.**](../azure-functions/functions-premium-plan.md)

      > [!IMPORTANT]
      > Verbruiksplannen worden niet ondersteund en zijn niet beschikbaar voor dit resourcetype. Uw geselecteerde abonnement is van invloed op de mogelijkheden en prijslagen die later voor u beschikbaar zijn. Lees de volgende onderwerpen voor meer informatie: 
      >
      > * [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)
      > * [App Service prijsinformatie](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Het Premium-abonnement biedt bijvoorbeeld toegang tot netwerkmogelijkheden, zoals privé verbinding maken en integreren met virtuele Azure-netwerken, vergelijkbaar met Azure Functions wanneer u logische apps maakt en implementeert. 
      > Lees de volgende onderwerpen voor meer informatie:
      > 
      > * [Netwerkopties van Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps Running Anywhere - Netwerkmogelijkheden met Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      In dit voorbeeld wordt de **App Service Plan gebruikt.**

      ![Schermopname van het deelvenster 'Azure: Logic Apps (preview)' en een prompt om 'App Service Plan' of 'Premium' te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Maak een nieuw App Service of selecteer een bestaand abonnement. In dit voorbeeld selecteert **u Nieuwe App Service maken.**

      ![Schermopname met het deelvenster 'Azure: Logic Apps (preview)' en een prompt om een nieuw App Service-abonnement te maken of een bestaand App Service selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Geef een naam op voor App Service abonnement en selecteer vervolgens een [prijscategorie](../app-service/overview-hosting-plans.md) voor het abonnement. In dit voorbeeld wordt het **gratis F1-abonnement** geselecteerd.

      ![Schermopname met het deelvenster 'Azure: Logic Apps (preview)' en een prompt om een prijscategorie te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Zoek en selecteer voor optimale prestaties dezelfde resourcegroep als uw project voor de implementatie.

      > [!NOTE]
      > Hoewel u een andere resourcegroep kunt maken of gebruiken, kan dit van invloed zijn op de prestaties. Als u een andere resourcegroep maakt of kiest, maar annuleert nadat de bevestigingsprompt wordt weergegeven, wordt uw implementatie ook geannuleerd.

   1. Voor stateful werkstromen selecteert **u Nieuw opslagaccount of** een bestaand opslagaccount maken.

      ![Schermopname van het deelvenster 'Azure: Logic Apps (preview)' en een prompt om een opslagaccount te maken of te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Als de instellingen voor het maken en implementeren van uw logische app ondersteuning bieden [Application Insights,](../azure-monitor/app/app-insights-overview.md)kunt u optioneel logboekregistratie en tracering van diagnostische gegevens inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app implementeert vanuit Visual Studio Code of na de implementatie. U moet een Application Insights hebben, maar u kunt deze resource vooraf [maken,](../azure-monitor/app/create-workspace-resource.md)wanneer u uw logische app implementeert of na de implementatie.

      Als u logboekregistratie en tracering nu wilt inschakelen, volgt u deze stappen:

      1. Selecteer een bestaande resource Application Insights of Nieuwe resource **Application Insights maken.**

      1. Ga in [Azure Portal](https://portal.azure.com)naar uw Application Insights resource.

      1. Selecteer Overzicht in het **resourcemenu.** Zoek en kopieer de **waarde van de instrumentatiesleutel.**

      1. Open Visual Studio Code in de hoofdmap van uw project de **local.settings.json** file.

      1. Voeg in `Values` het -object de `APPINSIGHTS_INSTRUMENTATIONKEY` eigenschap toe en stel de waarde in op de instrumentatiesleutel, bijvoorbeeld:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > U kunt controleren of de trigger- en actienamen correct worden weergegeven in uw Application Insights exemplaar.
         >
         > 1. Ga in Azure Portal naar uw Application Insights resource.
         >
         > 2. Selecteer in het resourceresourcemenu onder **Onderzoeken** de optie **Toepassingskaart.**
         >
         > 3. Controleer de namen van de bewerking die op de kaart worden weergegeven.
         >
         > Sommige binnenkomende aanvragen van ingebouwde triggers kunnen worden weergegeven als dubbele aanvragen in het toepassingskaart. 
         > In plaats van de indeling te gebruiken, gebruiken deze duplicaten de werkstroomnaam als de naam van de bewerking en zijn ze afkomstig `WorkflowName.ActionName` van Azure Functions host.

      1. Vervolgens kunt u eventueel het ernstniveau aanpassen voor de traceringsgegevens die uw logische app verzamelt en naar uw Application Insights verzendt.

         Telkens wanneer een werkstroomgebeurtenis zich voordeed, zoals wanneer een werkstroom wordt geactiveerd of wanneer een actie wordt uitgevoerd, worden door de runtime verschillende traceringen uitgevoerd. Deze traceringen hebben betrekking op de levensduur van de werkstroom en bevatten, maar zijn niet beperkt tot, de volgende gebeurtenistypen:

         * Serviceactiviteit, zoals starten, stoppen en fouten.
         * Taken en dispatcheractiviteit.
         * Werkstroomactiviteit, zoals trigger, actie en uitvoeren.
         * Activiteit van opslagaanvraag, zoals geslaagd of mislukt.
         * HTTP-aanvraagactiviteit, zoals binnenkomende, uitgaande, succesvolle en mislukte aanvragen.
         * Alle ontwikkelings traceringen, zoals foutopsporingsberichten.

         Elk gebeurtenistype wordt toegewezen aan een ernstniveau. Het niveau legt bijvoorbeeld de meest gedetailleerde berichten vast, terwijl het niveau algemene activiteit in uw werkstroom vast legt, zoals wanneer uw logische app, werkstroom, trigger en acties starten `Trace` `Information` en stoppen. In deze tabel worden de ernstniveaus en de traceertypen beschreven:

         | Ernstniveau | Traceertype |
         |----------------|------------|
         | Kritiek | Logboeken waarin een onherkenbare fout in uw logische app wordt beschreven. |
         | Fouten opsporen | Logboeken die u kunt gebruiken voor onderzoek tijdens de ontwikkeling, bijvoorbeeld inkomende en uitgaande HTTP-aanroepen. |
         | Fout | Logboeken die wijzen op een fout in de uitvoering van de werkstroom, maar geen algemene fout in uw logische app. |
         | Informatie | Logboeken die de algemene activiteit in uw logische app of werkstroom bijhouden, bijvoorbeeld: <p><p>- Wanneer een trigger, actie of run wordt gestart en beëindigd. <br>- Wanneer uw logische app wordt gestart of beëindigd. |
         | Tracering | Logboeken die de meest gedetailleerde berichten bevatten, bijvoorbeeld opslagaanvragen of dispatcheractiviteit, plus alle berichten die zijn gerelateerd aan de uitvoering van de werkstroom. |
         | Waarschuwing | Logboeken waarin een abnormale status in uw logische app wordt belicht, maar die niet voorkomen dat deze wordt uitgevoerd. |
         |||

         Als u het ernstniveau wilt instellen, opent u op het hoofdniveau van uw project dehost.js **in** het bestand en gaat u naar het `logging` -object. Dit object bepaalt het filteren van logboeken voor alle werkstromen in uw logische app en volgt de [ASP.NET Core-indeling voor het filteren van logboektype.](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Als het object geen object bevat dat de `logging` `logLevel` eigenschap `Host.Triggers.Workflow` bevat, voegt u deze items toe. Stel de eigenschap in op het ernstniveau voor het traceertype dat u wilt, bijvoorbeeld:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Wanneer u klaar bent met de implementatiestappen, begint Visual Studio code met het maken en implementeren van de resources die nodig zijn voor het publiceren van uw logische app.

1. Als u het implementatieproces wilt controleren en controleren, selecteert u uitvoer in **het** menu **Weergave.** Selecteer in de werkbalklijst Uitvoervenster **de optie Azure Logic Apps.**

   ![Schermopname van het venster Uitvoer met het Azure Logic Apps geselecteerd in de werkbalklijst, samen met de voortgang en statussen van de implementatie.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Wanneer Visual Studio code klaar is met het implementeren van uw logische app in Azure, wordt het volgende bericht weergegeven:

   ![Schermopname van een bericht dat de implementatie naar Azure is voltooid.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gefeliciteerd, uw logische app is nu live in Azure en is standaard ingeschakeld.

Hierna leert u hoe u deze taken uitvoert:

* [Voeg een lege werkstroom toe aan uw project](#add-workflow-existing-project).

* [Geïmplementeerde logische apps beheren in Visual Studio Code](#manage-deployed-apps-vs-code) of met behulp van de [Azure Portal](#manage-deployed-apps-portal).

* [Schakel de run history in op staatloze werkstromen](#enable-run-history-stateless).

* [Schakel de bewakingsweergave in de Azure Portal voor een geïmplementeerde logische app](#enable-monitoring)in.

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Een lege werkstroom toevoegen aan het project

U kunt meerdere werkstromen hebben in uw logische app-project. Als u een lege werkstroom aan uw project wilt toevoegen, volgt u deze stappen:

1. Selecteer op Visual Studio codeactiviteitbalk het pictogram Azure.

1. Selecteer in het deelvenster Azure naast **Azure: Logic Apps (preview)** de optie **Werkstroom maken** (pictogram voor Azure Logic Apps).

1. Selecteer het werkstroomtype dat u wilt toevoegen: **Stateful** **of Stateless**

1. Geef een naam op voor uw werkstroom.

Wanneer u klaar bent, wordt er een nieuwe werkstroommap weergegeven in uw project, samen met een **workflow.jsvoor** de werkstroomdefinitie.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Geïmplementeerde logische apps beheren in Visual Studio Code

In Visual Studio Code kunt u alle geïmplementeerde logische apps in uw Azure-abonnement weergeven, of dit nu het oorspronkelijke Logic Apps of het resourcetype logische app **(preview)** **is,** en taken selecteren waarmee u deze logische apps kunt beheren. Voor toegang tot beide resourcetypen hebt u echter zowel de extensie **Azure Logic Apps** als de extensie **Azure Logic Apps (preview)** voor Visual Studio Code nodig.

1. Selecteer op de linkerwerkbalk het pictogram Azure. Vouw in **het deelvenster Azure: Logic Apps (preview)** uw abonnement uit, waarin alle geïmplementeerde logische apps voor dat abonnement worden weergegeven.

1. Open de logische app die u wilt beheren. Selecteer in het snelmenu van de logische app de taak die u wilt uitvoeren.

   U kunt bijvoorbeeld taken selecteren zoals het stoppen, starten, opnieuw starten of verwijderen van uw geïmplementeerde logische app.

   ![Schermopname van Visual Studio Code met het geopende extensiedeelvenster 'Azure Logic Apps (preview)' en de geïmplementeerde werkstroom.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Als u alle werkstromen in de logische app wilt weergeven, vouwt u uw logische app uit en vouwt u vervolgens het **knooppunt Werkstromen** uit.

1. Als u een specifieke werkstroom wilt weergeven, opent u het snelmenu van de werkstroom en selecteert u **Openen in** designer, waarmee de werkstroom wordt geopend in de modus Alleen-lezen.

   U hebt de volgende opties om de werkstroom te bewerken:

   * Open Visual Studio Code deworkflow.js **on** file van uw project in de werkstroomontwerper, bewerk uw wijzigingen en de logische app opnieuw in Azure.

   * Zoek in Azure Portal [logische app en open deze.](#manage-deployed-apps-portal) Zoek, bewerk en sla de werkstroom op.

1. Als u de geïmplementeerde logische app wilt openen in Azure Portal, opent u het snelmenu van de logische app en selecteert **u Openen in portal**.

   De Azure Portal wordt geopend in uw browser, u wordt automatisch aangemeld bij de portal als u bent aangemeld bij Visual Studio Code en uw logische app wordt weergegeven.

   ![Schermopname van de Azure Portal pagina voor uw logische app in Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   U kunt zich ook afzonderlijk aanmelden bij de Azure Portal, het zoekvak van de portal gebruiken om uw logische app te zoeken en vervolgens uw logische app selecteren in de lijst met resultaten.

   ![Schermopname van de Azure Portal en de zoekbalk met zoekresultaten voor de geïmplementeerde logische app, die wordt geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Geïmplementeerde logische apps beheren in de portal

In de Azure Portal kunt u alle geïmplementeerde logische apps bekijken die zich in uw Azure-abonnement hebben, ongeacht of ze het oorspronkelijke Logic Apps-resourcetype **of** het resourcetype Logische app **(preview)** zijn. Momenteel wordt elk resourcetype ingedeeld en beheerd als afzonderlijke categorieën in Azure. Als u logische apps wilt zoeken die het resourcetype **Logische app (preview)** hebben, volgt u deze stappen:

1. Voer in Azure Portal zoekvak `logic app preview` in. Wanneer de lijst met resultaten wordt weergegeven, selecteert u onder **Services** **de optie Logische app (preview)**.

   ![Schermopname van het Azure Portal zoekvak met de zoektekst 'voorbeeld van logische app'.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Zoek en selecteer in het deelvenster Logische app **(preview)** de logische app die u hebt geïmplementeerd vanuit Visual Studio Code.

   ![Schermopname van de Azure Portal en de resources van de logische app (preview) die in Azure zijn geïmplementeerd.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Met Azure Portal wordt de afzonderlijke resourcepagina voor de geselecteerde logische app geopend.

   ![Schermopname van de resourcepagina van uw logische app-werkstroom in de Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Als u de werkstromen in deze logische app wilt weergeven, selecteert u Werkstromen in het menu van **de** logische app.

   In **het deelvenster Werkstromen** worden alle werkstromen in de huidige logische app weergegeven. In dit voorbeeld ziet u de werkstroom die u hebt gemaakt in Visual Studio Code.

   ![Schermopname van de resourcepagina 'Logische app (preview)' met het deelvenster Werkstromen geopend en de geïmplementeerde werkstroom](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Als u een werkstroom wilt weergeven, **selecteert u** die werkstroom in het deelvenster Werkstromen.

   Het deelvenster Werkstroom wordt geopend en u ziet meer informatie en taken die u op die werkstroom kunt uitvoeren.

   Als u bijvoorbeeld de stappen in de werkstroom wilt weergeven, selecteert u **Designer**.

   ![Schermopname van het deelvenster Overzicht van de geselecteerde werkstroom, terwijl in het werkstroommenu de geselecteerde opdracht Designer wordt weergegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   De werkstroomontwerper wordt geopend en toont de werkstroom die u hebt gemaakt in Visual Studio Code. U kunt nu wijzigingen aanbrengen in deze werkstroom in Azure Portal.

   ![Schermopname van de werkstroomontwerper en werkstroom die zijn geïmplementeerd vanuit Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Een andere werkstroom toevoegen in de portal

Via de Azure Portal kunt u lege werkstromen toevoegen aan een **logic app-resource (preview)** die u hebt geïmplementeerd vanuit Visual Studio Code en deze werkstromen in de Azure Portal.

1. Zoek en [Azure Portal](https://portal.azure.com)de geïmplementeerde logische **app-resource (preview)** in de Azure Portal.

1. Selecteer Werkstromen in het menu van de **logische app.** Selecteer in **het deelvenster Werkstromen** de optie **Toevoegen.**

   ![Schermopname van het deelvenster 'Werkstromen' van de geselecteerde logische app en de werkbalk met de opdracht Toevoegen geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Geef in **het deelvenster Nieuwe werkstroom** een naam op voor de werkstroom. Selecteer **Stateful of** **Stateless** **>** **Create.**

   Nadat Azure uw nieuwe werkstroom heeft  geïmplementeerd, die wordt weergegeven in het deelvenster Werkstromen, selecteert u die werkstroom zodat u andere taken kunt beheren en uitvoeren, zoals het openen van de ontwerpfunctie of codeweergave.

   ![Schermopname van de geselecteerde werkstroom met opties voor beheer en controle.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Als u bijvoorbeeld de ontwerpfunctie voor een nieuwe werkstroom opent, ziet u een leeg canvas. U kunt deze werkstroom nu in de Azure Portal.

   ![Schermopname van de werkstroomontwerper en een lege werkstroom.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>De run history inschakelen voor staatloze werkstromen

Als u gemakkelijker fouten in een staatloze werkstroom wilt opsporen, kunt u de run history voor die werkstroom inschakelen en vervolgens de run history uitschakelen wanneer u klaar bent. Volg deze stappen voor Visual Studio Code. Als u in de Azure Portal werkt, zie Stateful en [stateless](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)werkstromen maken in de Azure Portal .

1. Vouw in Visual Studio Code-project de map **workflow-designtime** uit en open **delocal.settings.json** file.

1. Voeg de `Workflows.{yourWorkflowName}.operationOptions` eigenschap toe en stel de waarde in op , `WithStatelessRunHistory` bijvoorbeeld:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS of Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Als u de run history wilt uitschakelen wanneer u klaar bent, stelt u de eigenschap in `Workflows.{yourWorkflowName}.OperationOptions` op of verwijdert u de eigenschap en de waarde `None` ervan.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Bewakingsweergave inschakelen in de Azure Portal

Nadat u een resource voor een logische app **(preview)** hebt geïmplementeerd vanuit Visual Studio Code in Azure, kunt u de beschikbare rungeschiedenis en details voor een werkstroom in die resource controleren met behulp van de Azure Portal en de **Monitor-ervaring** voor die werkstroom. U moet echter eerst de weergavefunctie **Bewaken** inschakelen voor die logische app-resource.

1. Zoek en [Azure Portal](https://portal.azure.com)de geïmplementeerde logische **app -resource (preview)** in de Azure Portal.

1. Selecteer in het menu van die resource, onder **API,** **CORS.**

1. Voeg in **het deelvenster CORS** onder **Toegestane oorsprongen** het jokerteken (*) toe.

1. Wanneer u klaar bent, selecteert u Opslaan op de **CORS-werkbalk.**

   ![Schermopname van de Azure Portal met een geïmplementeerde logic app-resource (preview). In het resourcemenu is 'CORS' geselecteerd, met een nieuwe vermelding voor 'Toegestane oorsprongen' ingesteld op het jokerteken '*'.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Na de implementatie Application Insights of openen

Tijdens het uitvoeren van de werkstroom stuurt uw logische app telemetrie en andere gebeurtenissen. U kunt deze telemetrie gebruiken om beter inzicht te krijgen in hoe goed uw werkstroom wordt uitgevoerd en hoe Logic Apps runtime op verschillende manieren werkt. U kunt uw werkstroom bewaken met [behulp Application Insights](../azure-monitor/app/app-insights-overview.md), die bijna realtime telemetrie (live metrische gegevens) biedt. Deze mogelijkheid kan u helpen fouten en prestatieproblemen gemakkelijker te onderzoeken wanneer u deze gegevens gebruikt om problemen vast te stellen, waarschuwingen in te stellen en grafieken te maken.

Als de instellingen voor het maken en implementeren van uw logische app ondersteuning bieden [Application Insights,](../azure-monitor/app/app-insights-overview.md)kunt u optioneel diagnostische logboekregistratie en tracering inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app implementeert vanuit Visual Studio Code of na de implementatie. U moet een Application Insights hebben, maar u kunt deze resource vooraf [maken,](../azure-monitor/app/create-workspace-resource.md)wanneer u uw logische app implementeert of na de implementatie.

Als u Application Insights een geïmplementeerde logische app wilt inschakelen of als u de Application Insights wilt controleren wanneer deze al is ingeschakeld, volgt u deze stappen:

1. Zoek in Azure Portal de geïmplementeerde logische app.

1. Selecteer in het menu van de logische app **onder Instellingen** de **optie Application Insights.**

1. Als Application Insights niet is ingeschakeld, selecteert  u in Application Insights **deelvenster Inschakelen Application Insights.** Nadat het deelvenster is bijgewerkt, selecteert u onderaan **Toepassen.**

   Als Application Insights is ingeschakeld, selecteert u in **Application Insights** deelvenster Weergave **Application Insights gegevens.**

Nadat Application Insights geopend, kunt u verschillende metrische gegevens voor uw logische app bekijken. Lees de volgende onderwerpen voor meer informatie:

* [Azure Logic Apps Running Anywhere - Bewaken met Application Insights - deel 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps Running Anywhere - Bewaken met Application Insights - deel 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Implementeren in Docker

U kunt uw logische app implementeren in een [Docker-container](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) als de hostingomgeving met behulp van [de .NET CLI.](/dotnet/core/tools/) Met deze opdrachten kunt u het project van uw logische app bouwen en publiceren. Vervolgens kunt u uw Docker-container bouwen en uitvoeren als de bestemming voor het implementeren van uw logische app.

Als u niet bekend bent met Docker, bekijkt u deze onderwerpen:

* [Wat is Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Inleiding tot Containers en Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Inleiding tot .NET en Docker](/dotnet/core/docker/introduction)
* [Docker-containers, -afbeeldingen en -registers](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Zelfstudie: Aan de slag met Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Vereisten

* Het Azure Storage account dat uw logische app gebruikt voor implementatie

* Een Docker-bestand voor de werkstroom die u gebruikt bij het bouwen van uw Docker-container

  Met dit Docker-voorbeeldbestand wordt bijvoorbeeld een logische app geïmplementeerd en wordt de connection string opgegeven die de toegangssleutel bevat voor het Azure Storage-account dat is gebruikt voor het publiceren van de logische app naar de Azure Portal. Zie Opslagaccounts connection string om [deze tekenreeks connection string.](#find-storage-account-connection-string) Zie Best practices for [writing Docker files (Best practices voor het schrijven van Docker-bestanden) voor meer informatie.](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
  
  > [!IMPORTANT]
  > Zorg er voor productiescenario's voor dat u dergelijke geheimen en gevoelige informatie beveiligt en beveiligt, bijvoorbeeld met behulp van een sleutelkluis. Zie Build [images with BuildKit (Afbeeldingen bouwen met BuildKit)](https://docs.docker.com/develop/develop-images/build_enhancements/) en Manage sensitive data with Docker Secrets (Afbeeldingen bouwen met BuildKit) en Gevoelige gegevens beheren met Docker Secrets voor specifieke [Docker-bestanden.](https://docs.docker.com/engine/swarm/secrets/)

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Opslagaccountgegevens connection string

Voordat u uw Docker-containerafbeelding kunt bouwen en uitvoeren, moet u de connection string die de toegangssleutel voor uw opslagaccount bevat. Eerder hebt u dit opslagaccount gemaakt om de extensie te gebruiken in macOS of Linux, of toen u uw logische app naar de Azure Portal.

Volg deze stappen om deze connection string te zoeken en te kopiëren:

1. Selecteer in Azure Portal menu van het opslagaccount onder **Instellingen** de optie **Toegangssleutels.** 

1. Zoek en **kopieer in het** deelvenster Toegangssleutels de opslagaccountsleutels connection string, die er ongeveer als in dit voorbeeld uitziet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Schermopname van de Azure Portal met toegangssleutels voor het opslagaccount en connection string gekopieerd.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Bekijk Sleutels voor opslagaccounts beheren [voor meer informatie.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Sla de connection string op een veilige plaats, zodat u deze tekenreeks kunt toevoegen aan het Docker-bestand dat u voor de implementatie gebruikt. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Hoofdsleutel voor opslagaccount zoeken

Wanneer uw werkstroom een aanvraagtrigger bevat, moet u de [callback-URL](#get-callback-url-request-trigger) van de trigger krijgen nadat u de Docker-containerafbeelding hebt gemaakt en uitgevoerd. Voor deze taak moet u ook de hoofdsleutelwaarde opgeven voor het opslagaccount dat u voor de implementatie gebruikt.

1. Als u deze hoofdsleutel wilt vinden, opent u in uw project het bestand **azure-webjobs-secrets/{deployment-name}/host.json** file.

1. Zoek de `AzureWebJobsStorage` eigenschap en kopieer de sleutelwaarde uit deze sectie:

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. Sla deze sleutelwaarde op een veilige plek op voor later gebruik.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Uw Docker-containerafbeelding bouwen en uitvoeren

1. Bouw uw Docker-containerafbeelding met behulp van uw Docker-bestand en voer deze opdracht uit:

   `docker build --tag local/workflowcontainer .`

   Zie [docker build voor meer informatie.](https://docs.docker.com/engine/reference/commandline/build/)

1. Voer de container lokaal uit met behulp van deze opdracht:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Zie [docker run voor meer informatie.](https://docs.docker.com/engine/reference/commandline/run/)

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Callback-URL voor aanvraagtriggers

Voor een werkstroom die gebruikmaakt van de aanvraagtrigger, vraagt u de callback-URL van de trigger op door deze aanvraag te verzenden:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

De `{trigger-name}` waarde is de naam van de aanvraagtrigger die wordt weergegeven in de JSON-definitie van de werkstroom. De waarde wordt gedefinieerd in het Azure Storage-account dat u hebt ingesteld voor de eigenschap in het bestand `{master-key}` `AzureWebJobsStorage` **azure-webjobs-secrets/{deployment-name}/host.jsop**. Zie Hoofdsleutel voor opslagaccount [zoeken voor meer informatie.](#find-storage-account-master-key)

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Items verwijderen uit de ontwerpfunctie

Als u een item in uw werkstroom wilt verwijderen uit de ontwerpfunctie, volgt u een van deze stappen:

* Selecteer het item, open het snelmenu van het item (Shift+F10) en selecteer **Verwijderen.** Selecteer **OK** om de opdracht te bevestigen.

* Selecteer het item en druk op de verwijdertoets. Selecteer **OK** om de opdracht te bevestigen.

* Selecteer het item zodat het detailvenster voor dat item wordt geopend. Open in de rechterbovenhoek van het deelvenster het menu met weglatingsleden (**...**) en selecteer **Verwijderen.** Selecteer **OK** om de opdracht te bevestigen.

  ![Schermopname van een geselecteerd item in de ontwerpfunctie met het geopende detailvenster plus de geselecteerde knop met weglatingsleden en de opdracht Verwijderen.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Als het menu met weglatingsleden niet zichtbaar is, vouwt u Visual Studio Code-venster breed genoeg uit, zodat in het detailvenster de knop met weglatingsleden **(...**) in de rechterbovenhoek wordt weergegeven.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Fouten en problemen oplossen

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Designer kan niet worden geopend

Wanneer u de ontwerpfunctie probeert te openen, krijgt u de volgende foutmelding: **'Werkstroomontwerptijd kan niet worden gestart'**. Als u eerder hebt geprobeerd de ontwerpfunctie te openen en uw project vervolgens hebt stopgezet of verwijderd, wordt de extensiebundel mogelijk niet correct gedownload. Volg deze stappen om te controleren of dit het probleem is:

  1. Open Visual Studio code het venster Uitvoer. Selecteer uitvoer in **het** menu **Weergave.**

  1. Selecteer in de lijst in de titelbalk van het venster Uitvoer de optie **Azure Logic Apps (preview) zodat** u de uitvoer van de extensie kunt bekijken, bijvoorbeeld:

     ![Schermopname van het venster Uitvoer met 'Azure Logic Apps' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Controleer de uitvoer en controleer of dit foutbericht wordt weergegeven:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Als u deze fout wilt oplossen, verwijdert u de map **ExtensionBundles** op deze locatie **...\Users \{ your-username}\AppData\Local\Temp\Functions\ExtensionBundles** en opent u de **workflow.js** opnieuw in het bestand in de ontwerpfunctie.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Er ontbreken nieuwe triggers en acties in de ontwerpfunctie voor eerder gemaakte werkstromen

Azure Logic Apps Preview ondersteunt ingebouwde acties voor Azure Function-bewerkingen, Liquid-bewerkingen en XML-bewerkingen, zoals **XML-validatie** en **XML-transformatie.** Voor eerder gemaakte logische apps worden deze acties echter mogelijk niet weergegeven in de ontwerpfunctie die u kunt selecteren als Visual Studio Code een verouderde versie van de extensiebundel, , `Microsoft.Azure.Functions.ExtensionBundle.Workflows` gebruikt.

Bovendien worden de **Azure Function Operations-connector** en -acties niet weergegeven in de ontwerpfunctie, tenzij u **Connectors** uit Azure gebruiken hebt ingeschakeld of geselecteerd bij het maken van uw logische app. Als u de door Azure geïmplementeerde connectors niet hebt ingeschakeld tijdens het maken van de app, kunt u deze inschakelen vanuit uw project in Visual Studio Code. Open het **workflow.jsin het** snelmenu en selecteer **Connectors gebruiken vanuit Azure.**

Als u de verouderde bundel wilt herstellen, volgt u deze stappen om de verouderde bundel te verwijderen, waardoor Visual Studio Code de extensiebundel automatisch bij te werken naar de nieuwste versie.

> [!NOTE]
> Deze oplossing geldt alleen voor logische apps die u maakt en implementeert met Visual Studio Code met de extensie Azure Logic Apps (preview), niet de logische apps die u hebt gemaakt met behulp van de Azure Portal. Zie [Ondersteunde triggers en acties ontbreken in de ontwerpfunctie in Azure Portal](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Sla werk op dat u niet wilt verliezen en sluit Visual Studio.

1. Blader op uw computer naar de volgende map, die versies van mappen voor de bestaande bundel bevat:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Verwijder de versiemap voor de eerdere bundel. Als u bijvoorbeeld een map voor versie 1.1.3 hebt, verwijdert u die map.

1. Blader nu naar de volgende map, die mappen met versies bevat voor het vereiste NuGet-pakket:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Verwijder de versiemap voor het eerdere pakket. Als u bijvoorbeeld een map voor versie 1.0.0.8-preview hebt, verwijdert u die map.

1. Open Visual Studio Code, uw project en deworkflow.js **bestand** opnieuw in de ontwerpfunctie.

De ontbrekende triggers en acties worden nu weergegeven in de ontwerpfunctie.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>'400 Bad Request' wordt weergegeven bij een trigger of actie

Wanneer een run mislukt en u de run inspecteert in de bewakingsweergave, kan deze fout worden weergegeven bij een trigger of actie met een langere naam, waardoor de onderliggende Uniform Resource Identifier (URI) de standaardtekenlimiet overschrijdt.

Als u dit probleem wilt oplossen en de langere URI wilt aanpassen, bewerkt u de registersleutels en op uw computer door `UrlSegmentMaxCount` `UrlSegmentMaxLength` de onderstaande stappen te volgen. De standaardwaarden van deze sleutel worden in dit onderwerp beschreven,Http.sys [ registerinstellingen voor Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Voordat u begint, moet u ervoor zorgen dat u uw werk op slaan. Voor deze oplossing moet u de computer opnieuw opstarten nadat u klaar bent, zodat de wijzigingen van kracht kunnen worden.

1. Open op uw computer het **venster Uitvoeren** en voer de opdracht `regedit` uit, waarmee de registereditor wordt geopend.

1. Selecteer ja **in het vak Gebruikersaccountbeheer** **om** uw wijzigingen op uw computer toe te staan.

1. Vouw in het linkerdeelvenster **onder Computer** de knooppunten langs het pad **uit,** HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parametersen selecteer **vervolgens Parameters.**

1. Zoek in het rechterdeelvenster de `UrlSegmentMaxCount` registersleutels en `UrlSegmentMaxLength` .

1. Verhoog deze sleutelwaarden voldoende zodat de URI's geschikt zijn voor de namen die u wilt gebruiken. Als deze sleutels niet bestaan, voegt u deze toe aan de **map Parameters** door de volgende stappen uit te voeren:

   1. Selecteer in **het** snelmenu Parameters **de optie Nieuwe**  >  **DWORD-waarde (32-bits).**

   1. Voer in het bewerkingsvak dat wordt weergegeven `UrlSegmentMaxCount` in als de naam van de nieuwe sleutel.

   1. Open het snelmenu van de nieuwe sleutel en selecteer **Wijzigen.**

   1. Voer in **het vak Tekenreeks**  bewerken dat wordt weergegeven de waarde van de waarde voor de gegevenssleutel in die u in hexadecimale of decimale notatie wilt invoeren. In `400` hexadecimaal is bijvoorbeeld gelijk aan `1024` in decimaal.

   1. Herhaal deze stappen `UrlSegmentMaxLength` om de sleutelwaarde toe te voegen.

   Nadat u deze sleutelwaarden hebt vergroot of toevoegt, ziet de registereditor eruit als in dit voorbeeld:

   ![Schermopname van de registereditor.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Wanneer u klaar bent, start u de computer opnieuw op, zodat de wijzigingen van kracht kunnen worden.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Foutopsporingssessie kan niet worden starten

Wanneer u probeert een foutopsporingssessie te starten, wordt de fout 'Fout bestaat na het uitvoeren van **preLaunchTask 'generateDebugSymbols' weergegeven.** Als u dit probleem wilt oplossen, bewerkt u **tasks.jsbestand** in uw project om het genereren van symbolen over te slaan.

1. Vouw in uw project de **map .vscode** uit en open de **tasks.jsin het** bestand.

1. Verwijder in de volgende taak de regel , , samen met de komma die de voorgaande `"dependsOn: "generateDebugSymbols"` regel beëindigt, bijvoorbeeld:

   Voor:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Na:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Volgende stappen

We horen graag van u over uw ervaringen met de extensie Azure Logic Apps (preview).

* Maak uw problemen in [GitHub voor fouten of problemen.](https://github.com/Azure/logicapps/issues)
* Gebruik dit feedbackformulier voor vragen, aanvragen, opmerkingen en [andere feedback.](https://aka.ms/lafeedback)
