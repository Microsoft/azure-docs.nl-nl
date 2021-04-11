---
title: Logic Apps Preview-werk stromen maken in Visual Studio code
description: Werk stromen bouwen en uitvoeren voor automatiserings-en integratie scenario's in Visual Studio code met de extensie Azure Logic Apps (preview).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 491d5f14cc8f456d228a5bc6efaa6686575979c1
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078737"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Stateful en stateless werk stromen maken in Visual Studio code met de extensie Azure Logic Apps (preview)

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met [Azure Logic Apps Preview](logic-apps-overview-preview.md)kunt u automatiserings-en integratie oplossingen bouwen in apps, gegevens, Cloud Services en systemen door logische apps te maken en uit te voeren die [ *stateful* en *stateless* werk stromen](logic-apps-overview-preview.md#stateful-stateless) bevatten in Visual Studio code met behulp van de extensie Azure Logic apps (preview). Door dit nieuwe type logische app te gebruiken, kunt u meerdere werk stromen bouwen die worden aangedreven door de opnieuw ontworpen Azure Logic Apps Preview-runtime, die draag baarheid, betere prestaties en flexibiliteit biedt voor het implementeren en uitvoeren in verschillende hosting omgevingen, niet alleen Azure, maar ook docker-containers. Zie [overzicht van Azure Logic Apps Preview voor](logic-apps-overview-preview.md)meer informatie over het type van de nieuwe logische app.

![Scherm opname van Visual Studio code, Logic app project en werk stroom.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

In Visual Studio code kunt u beginnen met het maken van een project waarbij u de werk stromen van uw logische app *lokaal* kunt bouwen en uitvoeren in uw ontwikkel omgeving met behulp van de extensie Azure Logic apps (preview). U kunt ook beginnen met [het maken van een nieuwe **logische app (preview)** in de Azure Portal](create-stateful-stateless-workflows-azure-portal.md), maar beide benaderingen bieden u de mogelijkheid om uw logische app te implementeren en uit te voeren in dezelfde soorten hosting omgevingen.

Ondertussen kunt u nog steeds het type van de oorspronkelijke logische app maken. Hoewel de ontwikkelings ervaring in Visual Studio code verschilt van de oorspronkelijke en nieuwe logische app-typen, kan uw Azure-abonnement beide typen bevatten. U kunt alle geïmplementeerde Logic apps in uw Azure-abonnement weer geven en openen, maar de apps zijn ingedeeld in hun eigen categorieën en secties.

In dit artikel wordt uitgelegd hoe u een logische app en een werk stroom maakt in Visual Studio code met behulp van de extensie Azure Logic Apps (preview) en de volgende taken op hoog niveau uitvoert:

* Maak een project voor uw logische app en werk stroom.

* Voeg een trigger en een actie toe.

* Uitvoeren, testen, fouten opsporen en de uitvoerings geschiedenis lokaal controleren.

* Details van de domein naam voor toegang tot de firewall zoeken.

* Implementeren naar Azure, inclusief optioneel inschakelen Application Insights.

* Uw geïmplementeerde logische app beheren in Visual Studio code en de Azure Portal.

* Schakel de uitvoerings geschiedenis voor stateless werk stromen in.

* Schakel de Application Insights na de implementatie in of open deze.

* Implementeer op een docker-container die u overal kunt uitvoeren.

> [!NOTE]
> Raadpleeg de [pagina met bekende problemen Logic apps open bare preview in github](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)voor meer informatie over bekende problemen.

## <a name="prerequisites"></a>Vereisten

### <a name="access-and-connectivity"></a>Toegang en connectiviteit

* Toegang tot internet, zodat u de vereisten kunt downloaden, vanuit Visual Studio code verbinding moet maken met uw Azure-account en publiceert vanuit Visual Studio code naar Azure, een docker-container of een andere omgeving.

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Als u in dit artikel dezelfde voorbeeld logische app wilt maken, hebt u een Office 365 Outlook-e-mail account nodig dat gebruikmaakt van een werk-of school account van micro soft om u aan te melden.

  Als u een andere [e-mail connector wilt gebruiken die wordt ondersteund door Azure Logic apps](/connectors/), zoals Outlook.com of [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), kunt u nog steeds het voor beeld volgen en zijn de algemene stappen hetzelfde, maar uw gebruikers interface en opties kunnen op sommige manieren verschillen. Als u bijvoorbeeld de Outlook.com-connector gebruikt, gebruikt u uw persoonlijke Microsoft-account in plaats van u aan te melden.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Opslag vereisten

#### <a name="windows"></a>Windows

Als u uw Logic app-project lokaal wilt bouwen en uitvoeren in Visual Studio code wanneer u Windows gebruikt, voert u de volgende stappen uit om de Azure Storage-emulator in te stellen:

1. Down load en Installeer [Azure Storage Emulator 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Als u er nog geen hebt, moet u een lokale installatie van SQL DB hebben, zoals de gratis [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658), zodat de emulator kan worden uitgevoerd.

   Zie [de Azure Storage-emulator gebruiken voor ontwikkeling en testen](../storage/common/storage-use-emulator.md)voor meer informatie.

1. Voordat u uw project kunt uitvoeren, moet u de emulator starten.

   ![Scherm opname van de Azure Storage-emulator wordt uitgevoerd.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS en Linux

Voer de volgende stappen uit om een Azure Storage-account te maken en in te stellen om uw Logic app-project lokaal te bouwen en uit te voeren in Visual Studio code wanneer u macOS of Linux gebruikt.

> [!NOTE]
> De ontwerper in Visual Studio code werkt momenteel niet in Linux-besturings systeem, maar u kunt nog steeds Logic apps bouwen, uitvoeren en implementeren die gebruikmaken van de Logic Apps Preview-runtime voor op Linux gebaseerde virtuele machines. Voor Taan kunt u uw Logic apps in Visual Studio code op Windows of macOS bouwen en vervolgens implementeren op een virtuele machine op basis van Linux.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en [Maak een Azure Storage-account](../storage/common/storage-account-create.md?tabs=azure-portal). Dit is een [vereiste voor Azure functions](../azure-functions/storage-considerations.md).

1. Selecteer in het menu voor het opslag account onder **instellingen** de optie **toegangs sleutels**.

1. Zoek in het deel venster **toegangs toetsen** de Connection String van het opslag account, die er ongeveer als volgt uitziet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Scherm opname van de Azure Portal met toegangs sleutels voor het opslag account en connection string gekopieerd.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Raadpleeg voor meer informatie [beheer van opslag accounts beheren](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Sla de connection string op een veilige plek op. Nadat u uw Logic app-project in Visual Studio code hebt gemaakt, moet u de teken reeks toevoegen aan de **local.settings.jsvoor** het bestand in de hoofdmap van uw project.

   > [!IMPORTANT]
   > Als u van plan bent om te implementeren op een docker-container, moet u deze connection string ook gebruiken met het docker-bestand dat u voor de implementatie gebruikt. Voor productie scenario's moet u ervoor zorgen dat u deze geheimen en gevoelige informatie beveiligt en beveiligt, bijvoorbeeld door gebruik te maken van een sleutel kluis.
  
### <a name="tools"></a>Hulpprogramma's

* [Visual Studio code 1.30.1 (januari 2019) of hoger](https://code.visualstudio.com/). Dit is gratis. Down load en installeer ook deze hulpprogram ma's voor Visual Studio code, als u deze nog niet hebt:

  * [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), die een gemeen schappelijke Azure-aanmelding en-filter ervaring biedt voor alle andere Azure-extensies in Visual Studio code.

  * [C# voor Visual Studio code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), waarmee u met de functie F5 uw logische app kunt uitvoeren.

  * [Azure functions core tools 3.0.3245 of hoger](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) met behulp van de versie van het micro soft Installer (MSI), dat wil zeggen `func-cli-3.0.3245-x*.msi` .

    Deze hulpprogram ma's bevatten een versie van dezelfde runtime die de Azure Functions-runtime aanstuurt, die door de preview-uitbrei ding wordt gebruikt in Visual Studio code.

    > [!IMPORTANT]
    > Als u een installatie hebt die ouder is dan deze versies, moet u eerst die versie verwijderen of ervoor zorgen dat de omgevings variabele PATH verwijst naar de versie die u downloadt en installeert.

  * [Azure Logic apps (preview)-extensie voor Visual Studio code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Deze uitbrei ding biedt u de mogelijkheid om Logic apps te maken waarin u stateful en stateless werk stromen kunt bouwen die lokaal worden uitgevoerd in Visual Studio code en die Logic apps vervolgens rechtstreeks op Azure of op docker-containers implementeren.

    Op dit moment kunt u zowel de oorspronkelijke Azure Logic Apps extensie als de open bare preview-extensie hebben geïnstalleerd in Visual Studio code. Hoewel de ontwikkelings ervaring op een aantal manieren verschilt tussen de uitbrei dingen, kan uw Azure-abonnement zowel logische app-typen bevatten die u met de uitbrei dingen maakt. Visual Studio code toont alle geïmplementeerde Logic apps in uw Azure-abonnement, maar organiseert ze in verschillende secties op extensie namen, **Logic apps** en **Azure Logic apps (preview)**.

    > [!IMPORTANT]
    > Als u logische app-projecten hebt gemaakt met de extensie eerdere persoonlijke preview, werken deze projecten niet met de open bare preview-extensie. U kunt deze projecten echter migreren nadat u de uitbrei ding van de persoonlijke Preview hebt verwijderd, de gekoppelde bestanden hebt verwijderd en de uitbrei ding voor open bare preview-versie hebt geïnstalleerd. Vervolgens maakt u een nieuw project in Visual Studio code en kopieert u het **werk stroom. definitie** bestand van uw eerder gemaakte logische app naar het nieuwe project. Zie voor meer informatie [migreren vanuit de uitbrei ding voor de persoonlijke preview](#migrate-private-preview).
    > 
    > Als u logische app-projecten met de eerdere open bare preview-extensie hebt gemaakt, kunt u deze projecten blijven gebruiken zonder migratie stappen.

    **Voer de volgende stappen uit om de extensie **Azure Logic apps (preview)** te installeren:**

    1. Selecteer in Visual Studio code op de werk balk links de optie **uitbrei dingen**.

    1. Typ in het zoekvak voor extensies `azure logic apps preview` . Selecteer **Azure Logic apps (preview)** installeren in de lijst met resultaten **>** .

       Nadat de installatie is voltooid, wordt de preview-uitbrei ding weer gegeven in de lijst **extensies: geïnstalleerd** .

       ![Scherm opname van de lijst met geïnstalleerde extensies van Visual Studio code met de extensie ' Azure Logic Apps (preview) ' onderstreept.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Als de extensie niet wordt weer gegeven in de lijst met geïnstalleerde Program meren, start u Visual Studio code opnieuw op.

* Als u de [inline code bewerkingen actie](../logic-apps/logic-apps-add-run-inline-code.md) wilt gebruiken waarmee Java script wordt uitgevoerd, installeert u [Node.js versie 10. x. x, 11. x. x of 12. x. x](https://nodejs.org/en/download/releases/).

  > [!TIP] 
  > Down load de MSI-versie voor Windows. Als u in plaats daarvan de ZIP-versie gebruikt, moet u Node.js hand matig beschikbaar maken met behulp van een omgevings variabele PATH voor uw besturings systeem.

* Voor het lokaal uitvoeren van webhook-triggers en-acties, zoals de [ingebouwde HTTP-webhook-trigger](../connectors/connectors-native-webhook.md), in Visual Studio code moet u [door sturen instellen voor de call back-URL](#webhook-setup).

* Als u de logische app wilt testen die u in dit artikel hebt gemaakt, hebt u een hulp programma nodig waarmee u aanroepen kunt verzenden naar de trigger voor aanvragen. Dit is de eerste stap in de logische app. Als u zo'n hulp programma niet hebt, kunt u [postman](https://www.postman.com/downloads/)downloaden, installeren en gebruiken.

* Als u uw logische app maakt en implementeert met instellingen die ondersteuning bieden voor het gebruik van [Application Insights](../azure-monitor/app/app-insights-overview.md), kunt u optioneel diagnostische logboek registratie en tracering inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app implementeert vanuit Visual Studio code of na de implementatie. U moet een Application Insights-exemplaar hebben, maar u kunt deze resource [vooraf](../azure-monitor/app/create-workspace-resource.md)maken wanneer u uw logische app implementeert of na de implementatie.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migreren vanuit een persoonlijke preview-extensie

De logische app-projecten die u met de extensie **Azure Logic apps (private preview)** hebt gemaakt, werken niet met de extensie open bare preview. U kunt deze projecten echter migreren naar nieuwe projecten door de volgende stappen uit te voeren:

1. Verwijder de extensie van de persoonlijke preview.

1. Verwijder alle gekoppelde uitbreidings bundel-en NuGet-pakket mappen op de volgende locaties:

   * De map **micro soft. Azure. functions. ExtensionBundle. workflows** , die eerdere uitbreidings bundels bevat en die zich in een van beide paden bevindt:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * De map **micro soft. Azure. workflows. webjobs. extension** , de [NuGet](/nuget/what-is-nuget) cache voor de extensie van de persoonlijke preview en bevindt zich op dit pad:

     `C:\Users\{userName}\.nuget\packages`

1. Installeer de extensie **Azure Logic apps (preview)** .

1. Maak een nieuw project in Visual Studio code.

1. Kopieer het **werk stroom. definitie** bestand van uw eerder gemaakte logische app naar het nieuwe project.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code instellen

1. Als u er zeker van wilt zijn dat alle uitbrei dingen correct zijn geïnstalleerd, laadt of start u Visual Studio code opnieuw.

1. Controleer of Visual Studio code automatisch extensie-updates zoekt en installeert zodat uw preview-uitbrei ding de meest recente updates ontvangt. Als dat niet het geval is, moet u de verouderde versie hand matig verwijderen en de nieuwste versie installeren.

   1. Ga in het menu **bestand** naar **voor keur** **>** **instellingen**.

   1. Ga op het tabblad **gebruiker** naar **onderdelen** **>** **extensies**.

   1. Controleer of **automatisch controleren van updates** en **automatisch bijwerken** is geselecteerd.

De volgende instellingen zijn standaard ingeschakeld en ingesteld voor de uitbrei ding Logic Apps Preview:

* **Azure Logic apps v2: Project runtime**, dat is ingesteld op versie **~ 3**

  > [!NOTE]
  > Deze versie is vereist voor het gebruik van de [inline code bewerkingen acties](../logic-apps/logic-apps-add-run-inline-code.md).

* **Azure Logic apps v2: experimentele weergave beheer**, waarmee u de nieuwste versie van Visual Studio code kunt ontwerpen. Als u problemen ondervindt met de ontwerp functie, zoals het slepen en neerzetten van items, schakelt u deze instelling uit.

Voer de volgende stappen uit om deze instellingen te zoeken en te bevestigen:

1. Ga in het menu **bestand** naar **voor keur** **>** **instellingen**.

1. Ga op het tabblad **gebruiker** naar **>** **extensies** **>** **Azure Logic apps (preview)**.

   U kunt bijvoorbeeld de **Azure Logic apps v2: Project runtime** -instelling vinden of het zoekvak gebruiken voor het zoeken naar andere instellingen:

   ![Scherm opname van Visual Studio code-instellingen voor de extensie Azure Logic Apps (preview).](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure.

   ![Scherm afbeelding van de activiteiten balk en het geselecteerde Azure-pictogram van Visual Studio code.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. In het deel venster Azure, onder **Azure: Logic apps (preview)**, selecteert **u aanmelden bij Azure**. Wanneer de pagina Visual Studio-code verificatie wordt weer gegeven, meldt u zich aan met uw Azure-account.

   ![Scherm opname van het Azure-venster en de geselecteerde koppeling voor Azure-aanmelding.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Nadat u zich hebt aangemeld, worden in het deel venster Azure de abonnementen in uw Azure-account weer gegeven. Als u ook de openbaar uitgebrachte uitbrei ding hebt, kunt u alle Logic apps die u hebt gemaakt met die extensie, vinden in de sectie **Logic apps** , niet in de sectie **Logic apps (preview)** .
   
   Als de verwachte abonnementen niet worden weer gegeven of als u wilt dat in het deel venster alleen specifieke abonnementen worden weer gegeven, volgt u deze stappen:

   1. Verplaats de aanwijzer in de lijst abonnementen naast het eerste abonnement totdat de knop **abonnementen selecteren** (filter pictogram) wordt weer gegeven. Selecteer het filter pictogram.

      ![Scherm opname van het Azure-venster en het geselecteerde filter pictogram.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Of Selecteer uw Azure-account in de status balk van Visual Studio code. 

   1. Wanneer er een andere abonnementen lijst wordt weer gegeven, selecteert u de gewenste abonnementen en selecteert u **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Een lokaal project maken

Voordat u uw logische app kunt maken, moet u een lokaal project maken, zodat u uw logische app kunt beheren, uitvoeren en implementeren vanuit Visual Studio code. Het onderliggende project is vergelijkbaar met een Azure Functions project, ook wel bekend als een functie-app-project. Deze project typen zijn echter gescheiden van elkaar, waardoor Logic apps en functie-apps niet in hetzelfde project kunnen voor komen.

1. Maak op uw computer een *lege* lokale map die moet worden gebruikt voor het project dat u later in Visual Studio code gaat maken.

1. Sluit alle geopende mappen in Visual Studio code.

1. Selecteer in het deel venster Azure naast **Azure: Logic apps (preview)** de optie **Nieuw project maken** (pictogram dat een map en bliksem Schicht weer geven).

   ![Scherm opname van de Azure-venster werkbalk met ' nieuw project maken ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Als Windows Defender firewall u vraagt om netwerk toegang te verlenen voor `Code.exe` , dat wil zeggen Visual Studio code, en voor `func.exe` , de Azure functions core tools, selecteert u **particuliere netwerken, zoals mijn thuis-of bedrijfs netwerk,** **>** **toegang toestaan**.

1. Blader naar de locatie waar u de projectmap hebt gemaakt, Selecteer deze map en ga door.

   ![Scherm opname van het dialoog venster ' map selecteren ' met een nieuw gemaakte projectmap en de knop selecteren geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Selecteer in de lijst met sjablonen die wordt weer gegeven, een **stateful werk stroom** of **stateless werk stroom**. In dit voor beeld wordt een **stateful werk stroom** geselecteerd.

   ![Scherm opname van de lijst met werk stroom sjablonen waarvoor een stateful werk stroom is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Geef een naam op voor de werk stroom en druk op ENTER. In dit voor beeld wordt `Fabrikam-Stateful-Workflow` de naam gebruikt.

   ![Scherm afbeelding met het vak ' nieuwe stateful werk stroom maken (3/4) ' en ' fabrikam-stateful-werk stroom ' als werk stroom naam.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio code voltooit het maken van het project en opent de **workflow.jsin** het bestand voor uw werk stroom in de code-editor.

   > [!NOTE]
   > Als u wordt gevraagd om te selecteren hoe u uw project wilt openen, selecteert u **openen in huidig venster** als u uw project wilt openen in het huidige venster van Visual Studio code. Als u een nieuw exemplaar voor Visual Studio code wilt openen, selecteert u **openen in nieuw venster**.

1. Open in de Visual Studio-werk balk het deel venster Verkenner als dit nog niet is geopend.

   In het deel venster Verkenner wordt uw project weer gegeven. Dit bevat nu automatisch gegenereerde project bestanden. Het project bevat bijvoorbeeld een map waarin de naam van uw werk stroom wordt weer gegeven. In deze map bevat de **workflow.jsin** het bestand de onderliggende JSON-definitie van uw werk stroom.

   ![Scherm opname van het deel venster Verkenner met projectmap, map werk stroom en het bestand workflow.jsop.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Als u macOS of Linux gebruikt, stelt u toegang tot uw opslag account in door de volgende stappen uit te voeren die nodig zijn voor het lokaal uitvoeren van uw project:

   1. Open in de hoofdmap van het project de **local.settings.jsin** het bestand.

      ![Scherm opname van het Verkenner-deel venster en het bestand local.settings.jsin uw project.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Vervang de `AzureWebJobsStorage` waarde van de eigenschap door de Connection String van het opslag account die u eerder hebt opgeslagen, bijvoorbeeld:

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
      > Voor productie scenario's moet u ervoor zorgen dat u deze geheimen en gevoelige informatie beveiligt en beveiligt, bijvoorbeeld door gebruik te maken van een sleutel kluis.

   1. Wanneer u klaar bent, moet u ervoor zorgen dat u de wijzigingen opslaat.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Ingebouwde connector ontwerpen inschakelen

U kunt uw eigen ingebouwde connectors maken voor elke service die u nodig hebt met behulp [van het uitbreidings raamwerk van de preview-versie](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Net als bij ingebouwde connectors, zoals Azure Service Bus en SQL Server, bieden deze connectors een hogere door Voer, lage latentie en lokale connectiviteit, en worden ze systeem eigen uitgevoerd in hetzelfde proces als de runtime voor de preview-versie.

De ontwerp functie is momenteel alleen beschikbaar in Visual Studio code, maar is niet standaard ingeschakeld. Als u deze connectors wilt maken, moet u het project eerst converteren van een op een op een basis gebaseerd op een op een Node.js op NuGet (op de gebaseerd op basis van een extensie).

> [!IMPORTANT]
> Deze actie is een eenrichtings bewerking die u niet ongedaan kunt maken.

1. Ga in het deel venster Verkenner naar de hoofdmap van het project, verplaats de muis aanwijzer over een leeg gebied onder alle andere bestanden en mappen, open het snelmenu en selecteer **converteren naar Nuget Logic app project**.

   ![Scherm opname van het deel venster Verkenner waarin het snelmenu van het project wordt geopend vanuit een leeg gebied in het project venster.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Wanneer de prompt wordt weer gegeven, bevestig de project conversie.

1. Als u wilt door gaan, bekijkt u de stappen in het artikel Azure Logic Apps de uitbreidings module voor de [hele ingebouwde connector](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)uit te voeren.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Het definitie bestand van de werk stroom in de ontwerp functie openen

1. Controleer de versies die op uw computer zijn geïnstalleerd door deze opdracht uit te voeren:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Als u .NET Core SDK 5. x hebt, is het mogelijk dat u met deze versie de onderliggende werk stroom definitie van de logische app in de ontwerp functie niet kunt openen. In plaats van deze versie te verwijderen, maakt u in de hoofdmap van het project een **global.jsvoor** het bestand dat verwijst naar de .net core runtime 3. x-versie die u later dan 3.1.201, bijvoorbeeld:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Zorg ervoor dat u de **global.jsvoor** het bestand in de hoofdmap van het project expliciet toevoegt vanuit Visual Studio code. Anders wordt de ontwerp functie niet geopend.

1. Vouw de projectmap voor uw werk stroom uit. Open de **workflow.jsin** het snelmenu van het bestand en selecteer **openen in Designer**.

   ![Scherm opname van het deel venster Verkenner en het snelkoppelings venster voor de workflow.jsin het bestand ' openen in Designer ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Selecteer in de lijst **connectors in azure inschakelen** de optie **connectors van Azure gebruiken**. Dit is van toepassing op alle beheerde connectors die beschikbaar zijn en worden geïmplementeerd in azure, niet alleen voor connectors voor Azure-Services.

   ![Scherm opname van het Verkenner-deel venster met de lijst ' connectors in azure inschakelen ' open en connectors van Azure gebruiken geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Stateless werk stromen ondersteunen momenteel alleen *acties* voor [beheerde connectors](../connectors/apis-list.md#managed-api-connectors), die worden geïmplementeerd in azure, en niet voor triggers. Hoewel u de mogelijkheid hebt om connectors in Azure in te scha kelen voor uw stateless werk stroom, worden in de ontwerp functie geen beheerde connector triggers weer gegeven die u kunt selecteren.

1. Selecteer in de lijst **abonnement selecteren** het Azure-abonnement dat u wilt gebruiken voor uw logische app-project.

   ![Scherm opname van het Verkenner-deel venster met het selectie vakje abonnement selecteren en uw abonnement geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Selecteer in de lijst resource groepen de optie **nieuwe resource groep maken**.

   ![Scherm opname van het Verkenner-deel venster met de lijst resource groepen en ' nieuwe resource groep maken ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Geef een naam op voor de resource groep en druk op ENTER. In dit voorbeeld wordt `Fabrikam-Workflows-RG` gebruikt.

   ![Scherm opname van het deel venster Verkenner en de naam van de resource groep.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Zoek en selecteer in de lijst locaties de Azure-regio die u wilt gebruiken bij het maken van uw resource groep en resources. In dit voor beeld wordt **West-Centraal VS** gebruikt.

   ![Scherm afbeelding met het Verkenner-deel venster met de lijst met locaties en ' West-Centraal VS ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Nadat u deze stap hebt uitgevoerd, opent Visual Studio code de werk stroom ontwerper.

   > [!NOTE]
   > Wanneer Visual Studio code de ontwerp tijd API van de werk stroom start, wordt mogelijk een bericht weer gegeven dat het opstarten enkele seconden kan duren. U kunt dit bericht negeren of **OK** selecteren.
   >
   > Als de ontwerper niet kan worden geopend, raadpleegt u de sectie probleem oplossing. de [ontwerp functie kan niet worden geopend](#designer-fails-to-open).

   Nadat de ontwerp functie wordt weer gegeven, wordt de prompt **een bewerking kiezen** wordt weer gegeven op de ontwerp functie en is deze standaard ingeschakeld, waarin het deel venster **actie toevoegen** wordt weer gegeven.

   ![Scherm opname van de werk stroom ontwerper.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Voeg vervolgens [een trigger en acties](#add-trigger-actions) toe aan uw werk stroom.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Een trigger en acties toevoegen

Nadat u de ontwerp functie hebt geopend, wordt de prompt **een bewerking kiezen** weer gegeven in de ontwerp functie en is deze standaard geselecteerd. U kunt nu beginnen met het maken van uw werk stroom door een trigger en acties toe te voegen.

De werk stroom in dit voor beeld maakt gebruik van deze trigger en deze acties:

* De ingebouwde [aanvraag trigger](../connectors/connectors-native-reqres.md), **wanneer er een HTTP-aanvraag wordt ontvangen**, die inkomende aanroepen of aanvragen ontvangt en een eind punt maakt dat andere services of logische apps kan aanroepen.

* De [Office 365 Outlook-actie](../connectors/connectors-create-api-office365-outlook.md), **een e-mail verzenden**.

* De ingebouwde [reactie actie](../connectors/connectors-native-reqres.md)die u gebruikt om een antwoord te verzenden en gegevens terug te sturen naar de aanroeper.

### <a name="add-the-request-trigger"></a>De aanvraag trigger toevoegen

1. Zorg ervoor dat naast de ontwerp functie in het deel venster **een trigger toevoegen** onder het zoekvak **een bewerking kiezen de optie** **ingebouwd** is geselecteerd, zodat u een trigger kunt selecteren die systeem eigen wordt uitgevoerd.

1. Voer in het zoekvak **een bewerking kiezen** het selectie vakje in `when a http request` en selecteer de ingebouwde aanvraag trigger die **wordt genoemd wanneer een HTTP-aanvraag wordt ontvangen**.

   ![Scherm opname van de werk stroom ontwerper en * * een trigger toevoegen * * deel venster met de trigger wanneer een HTTP-aanvraag is ontvangen geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Wanneer de trigger wordt weer gegeven op de Designer, wordt het detail venster van de trigger geopend om de eigenschappen, instellingen en andere acties van de trigger weer te geven.

   ![Scherm opname van de werk stroom ontwerper met de trigger geselecteerd wanneer een HTTP-aanvraag is ontvangen en het deel venster trigger Details geopend.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Als het detail venster niet wordt weer gegeven, zorgt u ervoor dat de trigger is geselecteerd in de ontwerp functie.

1. Als u een item uit de ontwerp functie wilt verwijderen, [voert u de volgende stappen uit om items uit de ontwerp functie te verwijderen](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>De Office 365 Outlook-actie toevoegen

1. Selecteer in de ontwerp functie, onder de trigger die u hebt toegevoegd, de optie **nieuwe stap**.

   De prompt **een bewerking kiezen** wordt weer gegeven in de ontwerp functie en het deel venster **actie toevoegen** wordt opnieuw geopend, zodat u de volgende actie kunt selecteren.

1. Selecteer in het deel venster **actie toevoegen** onder het zoekvak **een bewerking kiezen** de optie **Azure** zodat u een actie kunt vinden en selecteren voor een beheerde connector die in Azure is geïmplementeerd.

   In dit voor beeld wordt de Office 365 Outlook-actie geselecteerd en gebruikt, **een e-mail (v2) verzenden**.

   ![Scherm opname van de werk stroom ontwerper en * * een actie toevoegen * * deel venster met Office 365 Outlook de actie een e-mail verzenden is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Selecteer **Aanmelden** in het detail venster van de actie zodat u een verbinding kunt maken met uw e-mail account.

   ![Scherm opname van de werk stroom ontwerper en * * een deel venster e-mail (v2) * * verzenden als ' Aanmelden ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Wanneer Visual Studio code u vraagt om toestemming voor toegang tot uw e-mail account, selecteert u **openen**.

   ![Scherm opname van de Visual Studio-code prompt om toegang toe te staan.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Om toekomstige prompts te voor komen, selecteert u **vertrouwde domeinen configureren** zodat u de verificatie pagina als vertrouwd domein kunt toevoegen.

1. Volg de volgende vragen om u aan te melden, toegang toe te staan en terug te keren naar Visual Studio code.

   > [!NOTE]
   > Als er te veel tijd wordt door gegeven voordat u de prompts voltooit, wordt het verificatie proces geduurd en mislukt. In dit geval gaat u terug naar de Designer en probeert u opnieuw aan te melden om de verbinding te maken.

1. Wanneer de uitbrei ding Azure Logic Apps (preview) u vraagt om toestemming voor toegang tot uw e-mail account, selecteert u **openen**. Volg de volgende prompt om toegang toe te staan.

   ![Scherm opname van de prompt van de voorbeeld uitbreiding om toegang toe te staan.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Selecteer **niet opnieuw vragen voor deze extensie** om toekomstige prompts te voor komen.

   Nadat de verbinding met Visual Studio code is gemaakt, wordt in sommige connectors het bericht weer gegeven dat `The connection will be valid for {n} days only` . Deze tijds limiet geldt alleen voor de duur tijdens het ontwerpen van uw logische app in Visual Studio code. Na de implementatie is deze limiet niet langer van toepassing omdat uw logische app kan verifiëren tijdens runtime door gebruik te maken van de automatisch ingeschakelde door het [systeem toegewezen beheerde identiteit](../logic-apps/create-managed-service-identity.md). Deze beheerde identiteit wijkt af van de verificatie referenties of connection string die u gebruikt bij het maken van een verbinding. Als u deze door het systeem toegewezen beheerde identiteit uitschakelt, werken de verbindingen niet tijdens runtime.

1. Als de actie **een E-mail verzenden** niet wordt weer gegeven in de ontwerp functie, selecteert u deze actie.

1. Geef op het tabblad **para meters** van het detail venster van de actie de vereiste informatie voor de actie op, bijvoorbeeld:

   ![Scherm opname van de werk stroom ontwerper met Details voor Office 365 Outlook "een e-mail verzenden".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | De e-mail ontvanger, die uw e-mail adres kan zijn voor test doeleinden. In dit voor beeld wordt het fictieve e-mail adres gebruikt `sophiaowen@fabrikam.com` . |
   | **Onderwerp** | Ja | `An email from your example workflow` | Het onderwerp van de e-mail |
   | **Hoofdtekst** | Ja | `Hello from your example workflow!` | De inhoud van de hoofd tekst van de e-mail |
   ||||

   > [!NOTE]
   > Als u wijzigingen wilt aanbrengen in het detail venster op het tabblad **instellingen**, **statisch resultaat** of **uitvoeren na** , moet u ervoor zorgen dat u **klaar bent** om deze wijzigingen door te voeren voordat u de tabbladen verwisselt of de focus naar de ontwerper wijzigt. Als dat niet het geval is, blijven de wijzigingen niet behouden in Visual Studio code. Raadpleeg de [pagina met bekende problemen met Logic apps open bare preview in github](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)voor meer informatie.

1. Selecteer in de ontwerp functie **Opslaan**.

> [!IMPORTANT]
> Als u lokaal een werk stroom wilt uitvoeren die gebruikmaakt van een op webhooks gebaseerde trigger of acties, zoals de [ingebouwde trigger of actie van de http-webhook](../connectors/connectors-native-webhook.md), moet u deze mogelijkheid inschakelen door [door sturen in te stellen voor de call back-URL van de webhook](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Lokaal uitgevoerde webhooks inschakelen

Wanneer u een op webhook gebaseerde trigger of actie gebruikt, zoals **http-webhook**, met een logische app die in azure wordt uitgevoerd, wordt de Logic apps-runtime geabonneerd op het service-eind punt door het genereren en registreren van een call back-URL met dat eind punt. De trigger of actie wacht vervolgens op het service-eind punt om de URL aan te roepen. Als u in Visual Studio code werkt, wordt de gegenereerde call back-URL echter gestart met `http://localhost:7071/...` . Deze URL is voor uw localhost-server, die privé is, zodat het service-eind punt deze URL niet kan aanroepen.

Voor het lokaal uitvoeren van webhook-triggers en acties in Visual Studio code moet u een open bare URL instellen waarmee uw localhost-server wordt weer gegeven en worden de aanroepen van het service-eind punt veilig doorgestuurd naar de call back-URL van de webhook. U kunt een doorstuur service en een hulp programma gebruiken, zoals [**ngrok**](https://ngrok.com/), waarmee een http-tunnel naar uw localhost-poort wordt geopend, of u kunt uw eigen hulp programma gebruiken.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Oproep doorschakelen instellen met behulp van **ngrok**

1. [Meld u aan voor een **ngrok** -account](https://dashboard.ngrok.com/signup) als u er nog geen hebt. Als dat niet het geval is, [meldt u zich aan bij uw account](https://dashboard.ngrok.com/login).

1. Haal uw persoonlijke verificatie token op, wat uw **ngrok** -client nodig heeft om verbinding te maken en de toegang tot uw account te verifiëren.

   1. Als u de [pagina met het verificatie token](https://dashboard.ngrok.com/auth/your-authtoken)wilt zoeken, vouwt u in het menu van het account dashboard **verificatie** uit en selecteert **u uw Authtoken**.

   1. Kopieer het token naar een veilige locatie in het vak **uw Authtoken** .

1. Down load de gewenste **ngrok** -versie van de [ **ngrok** -download pagina](https://ngrok.com/download) of [uw account dashboard](https://dashboard.ngrok.com/get-started/setup)en pak het zip-bestand uit. Zie voor meer informatie [stap 1: unzip om te installeren](https://ngrok.com/download).

1. Open het hulp programma voor de opdracht prompt op de computer. Blader naar de locatie waar u het **ngrok.exe** bestand hebt.

1. Verbind de **ngrok** -client met uw **ngrok** -account door de volgende opdracht uit te voeren. Zie voor meer informatie [stap 2: verbinding maken met uw account](https://ngrok.com/download).

   `ngrok authtoken <your_auth_token>`

1. Open de HTTP-tunnel om poort 7071 te localhost door de volgende opdracht uit te voeren. Zie voor meer informatie [stap 3: het apparaat starten](https://ngrok.com/download).

   `ngrok http 7071`

1. Zoek in de uitvoer de volgende regel:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Kopieer de URL met deze indeling en sla deze op: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>De doorstuur-URL instellen in de app-instellingen

1. Voeg in Visual Studio code, in de ontwerp functie, de trigger of actie **http + webhook** toe.

1. Wanneer de prompt wordt weer gegeven voor de locatie van het host-eind punt, voert u de URL voor door sturen (omleiding) in die u eerder hebt gemaakt.

   > [!NOTE]
   > Als u de prompt negeert, wordt een waarschuwing weer gegeven dat u de doorstuur-URL moet opgeven, dus Selecteer **configureren** en voer de URL in. Nadat u deze stap hebt voltooid, wordt de prompt niet opnieuw weer gegeven voor de volgende webhook-triggers of-acties die u kunt toevoegen.
   >
   > Als u wilt dat de prompt opnieuw wordt weer gegeven, opent u op het hoofd niveau van het project het **local.settings.js** snelmenu van het bestand en selecteert **u het eind punt webhook-omleiding configureren**. De prompt wordt nu weer gegeven zodat u de doorstuur-URL kunt opgeven.

   Visual Studio code voegt de doorstuur-URL toe aan de **local.settings.jsvoor** het bestand in de hoofdmap van uw project. In het `Values` -object wordt de eigenschap met de naam `Workflows.WebhookRedirectHostUri` nu weer gegeven en wordt deze ingesteld op de DOORSTUUR-URL, bijvoorbeeld:
   
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

De eerste keer dat u een lokale foutopsporingssessie start of de werk stroom uitvoert zonder fouten op te sporen, registreert de Logic Apps runtime de werk stroom met het service-eind punt en abonneert dit op het eind punt om de webhook-bewerkingen op de hoogte te stellen. De volgende keer dat de werk stroom wordt uitgevoerd, wordt de runtime niet geregistreerd of opnieuw geabonneerd omdat de registratie van het abonnement al in de lokale opslag bestaat.

Wanneer u de foutopsporingssessie stopt voor een werk stroom die wordt uitgevoerd op lokaal uitvoeren webhook-triggers of-acties, worden de bestaande abonnements registraties niet verwijderd. Als u de registratie ongedaan wilt maken, moet u de registraties van het abonnement hand matig verwijderen of verwijderen.

> [!NOTE]
> Nadat de werk stroom is gestart, kunnen er in het Terminal venster fouten worden weer gegeven, zoals in het volgende voor beeld:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Open in dit geval de **local.settings.js** in het bestand in de hoofdmap van uw project en controleer of de eigenschap is ingesteld op `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Onderbrekings punten voor fout opsporing beheren

Voordat u de werk stroom van uw logische app uitvoert en test, kunt u in de **workflow.js** voor elke werk stroom [onderbrekings punten](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) instellen in het bestand. Er is geen andere installatie vereist. 

Op dit moment worden onderbrekings punten alleen ondersteund voor acties, niet voor triggers. Elke actie definitie heeft de volgende locaties voor onderbrekings punten:

* Stel het begin onderbrekings punt in op de regel waarin de naam van de actie wordt weer gegeven. Wanneer dit onderbrekings punt tijdens de foutopsporingssessie wordt gevonden, kunt u de invoer van de actie controleren voordat ze worden geëvalueerd.

* Stel het eind punt in op de regel waarin de accolade sluiten (**}**) wordt weer gegeven. Wanneer dit onderbrekings punt tijdens de foutopsporingssessie wordt gevonden, kunt u de resultaten van de actie controleren voordat de actie wordt uitgevoerd.

Voer de volgende stappen uit om een onderbrekings punt toe te voegen:

1. Open de **workflow.jsin** het bestand voor de werk stroom waarvoor u fouten wilt opsporen.

1. Selecteer op de regel waar u het onderbrekings punt wilt instellen in de kolom links de optie in die kolom. Als u het onderbrekings punt wilt verwijderen, selecteert u het onderbrekings punt.

   Wanneer u de foutopsporingssessie start, wordt de weer gave uitvoeren aan de linkerkant van het code venster weer gegeven, terwijl de werk balk fout opsporing bovenaan wordt weer gegeven.

   > [!NOTE]
   > Als de weer gave uitvoeren niet automatisch wordt weer gegeven, drukt u op CTRL + SHIFT + D.

1. Als u de beschik bare informatie wilt bekijken wanneer een onderbrekings punt wordt gevonden, bekijkt u in de weer gave uitvoeren het deel venster **variabelen** .

1. Als u wilt door gaan met het uitvoeren van de werk stroom, selecteert u op de werk balk fout opsporing **door gaan** (knop afspelen).

U kunt op elk gewenst moment onderbrekings punten toevoegen en verwijderen tijdens de uitvoering van de werk stroom. Als u de **workflow.jsin** het bestand bijwerkt nadat de uitvoering is gestart, worden onderbrekings punten niet automatisch bijgewerkt. Als u de onderbrekings punten wilt bijwerken, start u de logische app opnieuw.

Zie voor algemene informatie [onderbrekings punten-Visual Studio code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Lokaal uitvoeren, testen en fouten opsporen

Als u uw logische app wilt testen, voert u de volgende stappen uit om een foutopsporingssessie te starten en vindt u de URL voor het eind punt dat is gemaakt door de trigger voor aanvragen. U hebt deze URL nodig zodat u later een aanvraag kunt verzenden naar dat eind punt.

1. Als u een stateless werk stroom eenvoudiger wilt debuggen, kunt u [de uitvoerings geschiedenis voor die werk stroom inschakelen](#enable-run-history-stateless).

1. Open het menu **uitvoeren** op de activiteiten balk van Visual Studio en selecteer **Start Debugging** (F5).

   Het **Terminal** venster wordt geopend, zodat u de foutopsporingssessie kunt controleren.

   > [!NOTE]
   > Als u de fout melding **' fout bestaat na het uitvoeren van preLaunchTask ' generateDebugSymbols ' '** krijgt, raadpleegt u de sectie probleem oplossing. [Fout bij het opsporen van de sessie kan niet worden gestart](#debugging-fails-to-start).

1. Zoek nu de call back-URL voor het eind punt op de aanvraag trigger.

   1. Open het deel venster Verkenner opnieuw zodat u het project kunt weer geven.

   1. Selecteer **overzicht** in het snelmenu **workflow.js** van het bestand.

      ![Scherm opname van het deel venster Verkenner en het snelkoppelings venster voor de workflow.jsin het bestand ' overzicht ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Zoek de waarde van de **call back-URL** , die er ongeveer zo uitziet als deze URL voor de voorbeeld aanvraag trigger:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Scherm opname van de overzichts pagina van uw werk stroom met call back-URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Als u de call back-URL wilt testen door de werk stroom van de logische app te activeren, opent u [postman](https://www.postman.com/downloads/) of uw favoriete hulp programma voor het maken en verzenden van aanvragen.

   In dit voor beeld wordt het gebruik van Postman voortgezet. Zie voor meer informatie [verzen ding aan de slag](https://learning.postman.com/docs/getting-started/introduction/).

   1. Selecteer op de werk balk van Postman de optie **Nieuw**.

      ![Scherm afbeelding met de knop Nieuw is geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Selecteer in het deel venster **Nieuw** , onder **bouw stenen**, **aanvraag**.

   1. Geef in het venster **aanvraag opslaan** onder **naam van aanvraag** een naam op voor de aanvraag, bijvoorbeeld `Test workflow trigger` .

   1. Onder **Selecteer een verzameling of map om op te slaan**, selecteert u **verzameling maken**.

   1. Geef onder **alle verzamelingen** een naam op voor de verzameling die u wilt maken voor het organiseren van uw aanvragen, druk op ENTER en selecteer **opslaan in <*verzameling-naam* >**. In dit voor beeld wordt `Logic Apps requests` de naam van de verzameling gebruikt.

      Het aanvraag venster van Postman wordt geopend, zodat u een aanvraag kunt verzenden naar de call back-URL voor de aanvraag trigger.

      ![Scherm opname van de weer gave met het geopende aanvraag venster](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Ga terug naar Visual Studio code. Kopieer op de pagina overzicht van de werk stroom de eigenschaps waarde van de **call back-URL** .

   1. Ga terug naar de Postman. In het deel venster aanvraag, in de lijst met methoden, die momenteel de methode **Get** als standaard aanvraag bevat, plakt u de call back-URL die u eerder hebt gekopieerd in het vak Adres en selecteert u **verzenden**.

      ![Scherm afbeelding waarin de URL van de Postman en de retour aanroep wordt weer gegeven in het adresvak met de knop verzenden geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      De voorbeeld werk stroom voor logische apps verzendt een e-mail bericht dat lijkt op dit voor beeld:

      ![Scherm opname van Outlook-e-mail, zoals beschreven in het voor beeld](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Ga in Visual Studio code terug naar de overzichts pagina van uw werk stroom.

   Als u een stateful werk stroom hebt gemaakt, worden op de pagina overzicht de status en geschiedenis van de werk stroom weer gegeven nadat de aanvraag die u hebt verzonden, de werk stroom heeft geactiveerd.

   > [!TIP]
   > Als de uitvoerings status niet wordt weer gegeven, kunt u de pagina overzicht vernieuwen door **vernieuwen** te selecteren. Er vindt geen uitvoering plaats voor een trigger die wordt overgeslagen vanwege unmet criteria of het vinden van geen gegevens.

   ![Scherm afbeelding van de overzichts pagina van de werk stroom met de status en geschiedenis van de uitvoering](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Uitvoerings status | Description |
   |------------|-------------|
   | **Aborted** | De uitvoering is gestopt of niet voltooid vanwege externe problemen, bijvoorbeeld een systeem storing of een vervallen Azure-abonnement. |
   | **Gevraagd** | De uitvoering is geactiveerd en gestart, maar er is een annulerings aanvraag ontvangen. |
   | **Mislukt** | Ten minste één bewerking in de uitvoering is mislukt. Er zijn geen verdere acties in de werk stroom ingesteld voor het afhandelen van de fout. |
   | **Wordt uitgevoerd** | De uitvoering is geactiveerd en wordt uitgevoerd, maar deze status kan ook worden weer gegeven voor een uitvoering die wordt beperkt als gevolg van [actie limieten](logic-apps-limits-and-config.md) of het [huidige prijs plan](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tip**: als u [logboek registratie van diagnostische gegevens](monitor-logic-apps-log-analytics.md)instelt, kunt u informatie ophalen over eventuele vertragings gebeurtenissen die plaatsvinden. |
   | **Geslaagd** | De uitvoering is voltooid. Als een actie is mislukt, wordt die fout door een volgende actie in de werk stroom verwerkt. |
   | **Time-out opgetreden** | Er is een time-out opgetreden tijdens het uitvoeren omdat de huidige duur de limiet voor de uitvoerings duur heeft overschreden, die wordt bepaald door de [ **Bewaar periode voor het bewaren van uitvoerings geschiedenis in dagen**](logic-apps-limits-and-config.md#run-duration-retention-limits). De duur van een uitvoering wordt berekend met behulp van de begin tijd van de uitvoering en de duur limiet voor uitvoering op die begin tijd. <p><p>**Opmerking**: als de duur van de uitvoering ook de huidige *Bewaar limiet voor de uitvoerings geschiedenis* overschrijdt, die ook wordt bepaald door de Bewaar periode voor het bewaren van de [ **uitvoerings geschiedenis in dagen**](logic-apps-limits-and-config.md#run-duration-retention-limits), wordt de uitvoering uit de geschiedenis van uitvoeringen door een dagelijkse opschoon taak gewist. Of de uitvoerings tijd wordt uitgecheckt of voltooid, de Bewaar periode wordt altijd berekend met behulp van de begin tijd en de *huidige* Bewaar limiet van de uitvoering. Als u de limiet voor de duur van een in-Flight-uitvoering verkort, wordt er dus een time-out uitgevoerd. De uitvoering blijft echter behouden of wordt gewist uit de geschiedenis van de uitvoering, op basis van het feit of de duur van de uitvoering de Bewaar limiet heeft overschreden. |
   | **Wachten** | De uitvoering is niet gestart of wordt onderbroken, bijvoorbeeld door een eerder werk stroom exemplaar dat nog steeds actief is. |
   |||

1. Als u de statussen voor elke stap in een specifieke uitvoering en de invoer en uitvoer van de stap wilt bekijken, selecteert u de knop met weglatings tekens (**...**) voor die uitvoering en selecteert u vervolgens **uitvoeren weer geven**.

   ![Scherm afbeelding van de rij met de uitvoerings geschiedenis van de werk stroom met de knop met weglatings tekens en ' show run ' geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio code opent de weer gave bewaking en toont de status van elke stap in de uitvoering.

   ![Scherm opname van elke stap in de uitvoering van de werk stroom en hun status](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Als een uitvoering is mislukt en een stap in de bewakings weergave de `400 Bad Request` fout toont, kan dit probleem worden veroorzaakt door een langere trigger naam of actie naam die ervoor zorgt dat de onderliggende URI (Uniform Resource Identifier) de standaard teken limiet overschrijdt. Zie ["400-ongeldige aanvraag"](#400-bad-request)voor meer informatie.

   Hier volgen de mogelijke statussen die elke stap in de werk stroom kan hebben:

   | Actie status | Pictogram | Description |
   |---------------|------|-------------|
   | **Aborted** | ![Pictogram voor de actie status ' afgebroken '][aborted-icon] | De actie is gestopt of niet voltooid vanwege externe problemen, bijvoorbeeld een systeem storing of een vervallen Azure-abonnement. |
   | **Gevraagd** | ![Pictogram voor de actie status geannuleerd][cancelled-icon] | De actie is uitgevoerd, maar er is een aanvraag ontvangen om te annuleren. |
   | **Mislukt** | ![Pictogram voor de actie status ' mislukt '][failed-icon] | De actie is mislukt. |
   | **Wordt uitgevoerd** | ![Pictogram voor de actie status ' actief '][running-icon] | De actie wordt momenteel uitgevoerd. |
   | **Overgeslagen** | ![Pictogram voor de actie status "overgeslagen"][skipped-icon] | De actie is overgeslagen omdat de onmiddellijk voorafgaande actie is mislukt. Een actie heeft een `runAfter` voor waarde die vereist dat de voorafgaande actie is voltooid voordat de huidige actie kan worden uitgevoerd. |
   | **Geslaagd** | ![Pictogram voor de actie status geslaagd][succeeded-icon] | De actie is geslaagd. |
   | **Geslaagd met nieuwe pogingen** | ![Pictogram voor de actie status geslaagd met nieuwe pogingen][succeeded-with-retries-icon] | De actie is voltooid, maar alleen na een of meer pogingen. Als u de geschiedenis van de nieuwe poging wilt bekijken, selecteert u in de detail weergave uitvoerings geschiedenis die actie zodat u de invoer en uitvoer kunt bekijken. |
   | **Time-out opgetreden** | ![Pictogram voor de actie status time-out][timed-out-icon] | De actie is gestopt vanwege de time-outlimiet die is opgegeven door de instellingen van die actie. |
   | **Wachten** | ![Pictogram voor de actie status ' wachten '][waiting-icon] | Is van toepassing op een webhook-actie die wordt gewacht op een inkomende aanvraag van een aanroeper. |
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

1. Als u de invoer en uitvoer voor elke stap wilt bekijken, selecteert u de stap die u wilt inspecteren.

   ![Scherm opname van de status van elke stap in de werk stroom plus de invoer en uitvoer in de uitgevouwen actie ' een e-mail verzenden '](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Selecteer onbewerkte **invoer weer geven** of **onbewerkte uitvoer weer geven** om de onbewerkte invoer en uitvoer voor die stap verder te bekijken.

1. Als u de foutopsporingssessie wilt stoppen, selecteert u in het menu **uitvoeren** de optie **fout opsporing stoppen** (SHIFT + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Een antwoord retour neren

Om een antwoord te retour neren naar de aanroeper die een aanvraag naar uw logische app heeft verzonden, kunt u de ingebouwde [reactie actie](../connectors/connectors-native-reqres.md) gebruiken voor een werk stroom die begint met de trigger voor aanvragen.

1. Selecteer in de werk stroom ontwerper, onder de actie **een E-mail verzenden** , de optie **nieuwe stap**.

   De prompt **een bewerking kiezen** wordt weer gegeven in de ontwerp functie en het **deel venster actie toevoegen** wordt opnieuw geopend, zodat u de volgende actie kunt selecteren.

1. Zorg ervoor dat in het deel venster **actie toevoegen** onder het zoekvak **een actie kiezen de optie** **ingebouwd** is geselecteerd. Typ in het zoekvak `response` en selecteer de actie **antwoord** .

   ![Scherm opname van de werk stroom ontwerper met de geselecteerde reactie actie.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Wanneer de **reactie** actie wordt weer gegeven in de ontwerp functie, wordt het detail venster van de actie automatisch geopend.

   ![Scherm opname van de werk stroom ontwerper met het detail venster ' respons ' van de actie ' antwoord ' en de eigenschap ' Body ' die is ingesteld op de waarde van de eigenschap Body van de actie ' e-mail verzenden '.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Geef op het tabblad **para meters** de vereiste gegevens op voor de functie die u wilt aanroepen.

   In dit voor beeld wordt de eigenschaps waarde **Body** geretourneerd die de uitvoer is van de actie **een e-mail verzenden** .

   1. Klik in het eigenschappenvak van de **hoofd tekst** , zodat de lijst met dynamische inhoud wordt weer gegeven en de beschik bare uitvoer waarden van de voor gaande trigger en acties in de werk stroom worden getoond.

      ![Scherm opname van het detail venster van de actie ' respons ' met de muis aanwijzer binnen de eigenschap ' Body ' zodat de lijst met dynamische inhoud wordt weer gegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Selecteer **hoofd tekst** in de lijst met dynamische inhoud onder **een e-mail verzenden**.

      ![Scherm opname van de lijst met open dynamische inhoud. In de lijst, onder de kop ' e-mail verzenden ', is de uitvoer waarde ' Body ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Wanneer u klaar bent, wordt de eigenschap **Body** van de reactie actie nu ingesteld op **de uitvoer waarde** van de actie **een e-mail bericht verzenden** .

      ![Scherm opname van de status van elke stap in de werk stroom plus de invoer en uitvoer in de uitgevouwen reactie actie.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Selecteer in de ontwerp functie **Opslaan**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Uw logische app opnieuw testen

Nadat u updates hebt gemaakt voor uw logische app, kunt u een andere test uitvoeren door de debugger opnieuw uit te voeren in Visual Studio en een andere aanvraag te verzenden om uw bijgewerkte logische app te activeren, vergelijkbaar met de stappen in [uitvoeren, testen en fout opsporing op lokaal](#run-test-debug-locally).

1. Open het menu **uitvoeren** op de activiteiten balk van Visual Studio en selecteer **Start Debugging** (F5).

1. Stuur in postman of uw hulp programma voor het maken en verzenden van aanvragen een andere aanvraag om uw werk stroom te activeren.

1. Als u een stateful werk stroom hebt gemaakt, controleert u de status van de meest recente uitvoering op de pagina overzicht van de werk stroom. Als u de status, invoer en uitvoer voor elke stap in die uitvoering wilt weer geven, selecteert u de knop met weglatings tekens (**...**) voor die uitvoering en selecteert u vervolgens **uitvoeren weer geven**.

   Hier volgt een voor beeld van de stap-voor-stap-status voor een uitvoering nadat de voorbeeld werk stroom is bijgewerkt met de reactie actie.

   ![Scherm opname van de status van elke stap in de bijgewerkte werk stroom plus de invoer en uitvoer in de uitgevouwen reactie actie.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Als u de foutopsporingssessie wilt stoppen, selecteert u in het menu **uitvoeren** de optie **fout opsporing stoppen** (SHIFT + F5).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Domein namen voor toegang tot de firewall zoeken

Voordat u uw werk stroom voor de logische app implementeert en uitvoert in de Azure Portal, moet u, als uw omgeving strikte netwerk vereisten of firewalls heeft die verkeer beperken, machtigingen instellen voor elke trigger of actie verbinding die in uw werk stroom voor komt.

Voer de volgende stappen uit om de FQDN-namen (FULLy Qualified Domain names) voor deze verbindingen te vinden:

1. Open in uw logische app-project de **connections.jsin** het bestand, dat wordt gemaakt nadat u de eerste trigger of actie op basis van een verbinding hebt toegevoegd aan uw werk stroom en zoek het `managedApiConnections` object op.

1. Voor elke verbinding die u hebt gemaakt, zoekt, kopieert en slaat u de waarde van de eigenschap op een veilige manier op, `connectionRuntimeUrl` zodat u uw firewall met deze gegevens kunt instellen.

   Dit voor beeld **connections.js** een bestand bevat twee verbindingen, een AS2-verbinding en een Office 365-verbinding met deze `connectionRuntimeUrl` waarden:

   * AS2 `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

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

Vanuit Visual Studio code kunt u uw project rechtstreeks naar Azure publiceren, waarmee u uw logische app implementeert met het resource type van de nieuwe **logische app (preview)** . Net als bij de resource van de functie-app in Azure Functions moet u voor dit nieuwe bron type een [hosting-abonnement en prijs categorie](../app-service/overview-hosting-plans.md)selecteren, die u tijdens de implementatie kunt instellen. Raadpleeg de volgende onderwerpen voor meer informatie over het hosten van plannen en prijzen:

* [Een in Azure App Service omhoog schalen](../app-service/manage-scale-up.md)
* [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)

U kunt uw logische app als een nieuwe resource publiceren, waarmee automatisch alle benodigde resources worden gemaakt, zoals een [Azure Storage account, vergelijkbaar met de vereisten van de functie-app](../azure-functions/storage-considerations.md). Of u kunt uw logische app publiceren naar een eerder geïmplementeerde **Logic app (preview)** -resource, die de logische app overschrijft.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publiceren naar een nieuwe logische app (preview)-resource

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure.

1. Selecteer op de werk balk van het deel venster **Azure: Logic apps (preview)** de optie **implementeren naar logische app**.

   ![Scherm opname van het deel venster Azure: Logic Apps (preview) en de werk balk van het deel venster met ' implementeren op logische app ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Als u hierom wordt gevraagd, selecteert u het Azure-abonnement dat u wilt gebruiken voor de implementatie van de logische app.

1. Selecteer in de lijst die door Visual Studio code wordt geopend, een van de volgende opties:

   * **Een nieuwe logische app maken (preview) in azure** (snel)
   * **Een nieuwe logische app maken (preview) in azure Advanced**
   * Een eerder geïmplementeerde **logische app (preview)** -resource, indien aanwezig

   Dit voor beeld gaat verder met het **maken van een nieuwe logische app (preview) in azure Advanced**.

   ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' met een lijst waarin ' nieuwe logische app maken (preview) in azure ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Voer de volgende stappen uit om een nieuwe resource **voor een logische app (preview)** te maken:

   1. Geef een wereld wijd unieke naam op voor de nieuwe logische app. Dit is de naam die u moet gebruiken voor de **logische app (preview)** -resource. In dit voorbeeld wordt `Fabrikam-Workflows-App` gebruikt.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt om een naam op te geven voor de nieuwe logische app die u wilt maken.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selecteer een [hosting abonnement](../app-service/overview-hosting-plans.md) voor uw nieuwe logische app, een [ **app service plan** (toegewezen)](../azure-functions/dedicated-plan.md) of [**Premium**](../azure-functions/functions-premium-plan.md).

      > [!IMPORTANT]
      > Verbruiks abonnementen worden niet ondersteund of zijn niet beschikbaar voor dit resource type. Het geselecteerde abonnement is van invloed op de mogelijkheden en prijs categorieën die later beschikbaar zijn voor u. Raadpleeg de volgende onderwerpen voor meer informatie: 
      >
      > * [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)
      > * [Prijs informatie voor App Service](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Het Premium-abonnement biedt bijvoorbeeld toegang tot netwerk mogelijkheden, zoals verbinding maken en integreren met Azure Virtual Networks, vergelijkbaar met Azure Functions wanneer u uw Logic apps maakt en implementeert. 
      > Raadpleeg de volgende onderwerpen voor meer informatie:
      > 
      > * [Netwerkopties van Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps Anywhere-netwerk mogelijkheden uitvoeren met Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      In dit voor beeld wordt het **app service-abonnement** gebruikt.

      ![Scherm opname van het deel venster "Azure: Logic Apps (preview)" en een prompt om "App Service plan" of "Premium" te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Maak een nieuw App Service plan of selecteer een bestaand abonnement. In dit voor beeld wordt **nieuw app service plan maken** geselecteerd.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt om een nieuw App Service plan te maken of een bestaand App Service plan te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Geef een naam op voor uw App Service plan en selecteer vervolgens een [prijs categorie](../app-service/overview-hosting-plans.md) voor het plan. In dit voor beeld wordt het **gratis abonnement F1** geselecteerd.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt om een prijs categorie te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Voor optimale prestaties zoekt en selecteert u dezelfde resource groep als uw project voor de implementatie.

      > [!NOTE]
      > Hoewel u een andere resource groep kunt maken of gebruiken, kan dit van invloed zijn op de prestaties. Als u een andere resource groep maakt of kiest, maar niet annuleert nadat de bevestigings prompt wordt weer gegeven, wordt uw implementatie ook geannuleerd.

   1. Voor stateful werk stromen selecteert u **Nieuw opslag account maken** of een bestaand opslag account.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt voor het maken of selecteren van een opslag account.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Als de instellingen voor het maken en implementeren van uw logische app gebruikmaken van [Application Insights](../azure-monitor/app/app-insights-overview.md), kunt u optioneel diagnostische logboek registratie en tracering inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app implementeert vanuit Visual Studio code of na de implementatie. U moet een Application Insights-exemplaar hebben, maar u kunt deze resource [vooraf](../azure-monitor/app/create-workspace-resource.md)maken wanneer u uw logische app implementeert of na de implementatie.

      Voer de volgende stappen uit om logboek registratie en tracering nu in te scha kelen:

      1. Selecteer een bestaande Application Insights resource of **Maak een nieuwe Application Insights resource**.

      1. Ga in het [Azure Portal](https://portal.azure.com)naar uw Application Insights resource.

      1. Selecteer **overzicht** in het menu resource. Zoek en kopieer de waarde van de **instrumentatie sleutel** .

      1. Open in Visual Studio code in de hoofdmap van het project de **local.settings.jsin** het bestand.

      1. Voeg in het `Values` -object de `APPINSIGHTS_INSTRUMENTATIONKEY` eigenschap toe en stel de waarde in op de instrumentatie sleutel, bijvoorbeeld:

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
         > U kunt controleren of de trigger-en actie namen correct worden weer gegeven in uw Application Insights-exemplaar.
         >
         > 1. Ga in het Azure Portal naar uw Application Insights resource.
         >
         > 2. Selecteer in het menu resource resource onder **onderzoeken** de optie **toepassings overzicht**.
         >
         > 3. Controleer de namen van de bewerkingen die worden weer gegeven in de kaart.
         >
         > Sommige inkomende aanvragen van ingebouwde triggers kunnen als dubbele waarden worden weer gegeven in de toepassings toewijzing. 
         > In plaats van de `WorkflowName.ActionName` notatie te gebruiken, gebruiken deze dubbele items de naam van de werk stroom als de bewerkings naam en afkomstig van de Azure functions host.

      1. Vervolgens kunt u eventueel het Ernst niveau aanpassen voor de tracerings gegevens die uw logische app verzamelt en naar uw Application Insights-exemplaar verzendt.

         Telkens wanneer een gebeurtenis met betrekking tot een werk stroom plaatsvindt, bijvoorbeeld wanneer een werk stroom wordt geactiveerd of wanneer een actie wordt uitgevoerd, worden verschillende traceringen door de runtime gegenereerd. Deze traceringen dekken de levens duur van de werk stroom en bevatten, maar zijn niet beperkt tot, de volgende gebeurtenis typen:

         * Service activiteit, zoals starten, stoppen en fouten.
         * Activiteit van taken en dispatcher.
         * Werk stroom activiteit, zoals trigger, actie en uitvoeren.
         * Activiteit van de opslag aanvraag, zoals geslaagd of mislukt.
         * Activiteit van de HTTP-aanvraag, zoals inkomend, uitgaand, geslaagd en mislukt.
         * Eventuele ontwikkelings traceringen, zoals fout opsporingsberichten.

         Elk gebeurtenis type wordt toegewezen aan een Ernst niveau. Zo `Trace` legt het niveau de meest gedetailleerde berichten vast, terwijl `Information` in het niveau algemene activiteiten in uw werk stroom worden vastgelegd, zoals wanneer uw logische app, werk stroom, trigger en acties worden gestart en gestopt. In deze tabel worden de ernst niveaus en de bijbehorende tracerings typen beschreven:

         | Ernstniveau | Traceer type |
         |----------------|------------|
         | Kritiek | Logboeken waarin een onherstelbare fout wordt beschreven in uw logische app. |
         | Fouten opsporen | Logboeken die u tijdens de ontwikkeling kunt gebruiken om te onderzoeken, bijvoorbeeld inkomende en uitgaande HTTP-aanroepen. |
         | Fout | Logboeken die duiden op een fout in de uitvoering van de werk stroom, maar geen algemene fout in uw logische app. |
         | Informatie | Logboeken die de algemene activiteit in uw logische app of werk stroom volgen, bijvoorbeeld: <p><p>-Wanneer een trigger, actie of run wordt gestart en eindigt. <br>-Wanneer uw logische app begint of eindigt. |
         | Tracering | Logboeken die de meest gedetailleerde berichten bevatten, bijvoorbeeld opslag aanvragen of verzendings activiteit, plus alle berichten die betrekking hebben op activiteit voor het uitvoeren van de werk stroom. |
         | Waarschuwing | Logboeken die een abnormale status in uw logische app markeren, maar die niet voor komen. |
         |||

         Als u het Ernst niveau wilt instellen, opent u op het hoofd niveau van het project de **host.jsin** het bestand en zoekt u het- `logging` object. Dit object regelt de logboek filtering voor alle werk stromen in uw logische app en volgt de [ASP.net core indeling voor het filteren van logboek typen](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

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

         Als het `logging` object geen object bevat `logLevel` dat de eigenschap bevat `Host.Triggers.Workflow` , voegt u deze items toe. Stel de eigenschap in op het Ernst niveau voor het gewenste tracerings type, bijvoorbeeld:

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

   Wanneer u klaar bent met de implementaties tappen, begint Visual Studio code het maken en implementeren van de resources die nodig zijn voor het publiceren van uw logische app.

1. Als u het implementatie proces wilt controleren en controleren, selecteert u in het menu **weer gave** de optie **uitvoer**. Selecteer in de lijst werk balk van uitvoer venster **Azure Logic apps**.

   ![Scherm opname van het uitvoer venster met de Azure Logic Apps geselecteerd in de lijst met werk balken, samen met de voortgang en statussen van de implementatie.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Wanneer Visual Studio code de implementatie van uw logische app naar Azure voltooit, wordt het volgende bericht weer gegeven:

   ![Scherm afbeelding met een bericht dat de implementatie naar Azure is voltooid.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gefeliciteerd, uw logische app is nu live in Azure en is standaard ingeschakeld.

Vervolgens kunt u leren hoe u deze taken uitvoert:

* [Voeg een lege werk stroom toe aan uw project](#add-workflow-existing-project).

* [Geïmplementeerde Logic apps beheren in Visual Studio code](#manage-deployed-apps-vs-code) of met behulp van de [Azure Portal](#manage-deployed-apps-portal).

* [Schakel de uitvoerings geschiedenis in op stateless werk stromen](#enable-run-history-stateless).

* [Schakel de weer gave bewaking in het Azure Portal in voor een geïmplementeerde logische app](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Lege werk stroom toevoegen aan project

U kunt meerdere werk stromen hebben in uw logische app-project. Voer de volgende stappen uit om een lege werk stroom toe te voegen aan uw project:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure.

1. Selecteer in het deel venster Azure, naast **Azure: Logic apps (preview)**, **werk stroom maken** (pictogram voor Azure Logic apps).

1. Selecteer het werk stroom type dat u wilt toevoegen: **stateful** of **stateless**

1. Geef een naam op voor uw werk stroom.

Wanneer u klaar bent, wordt er in uw project een nieuwe map werk stroom weer gegeven, samen met een **workflow.jsin** het bestand voor de definitie van de werk stroom.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Geïmplementeerde Logic apps beheren in Visual Studio code

In Visual Studio code kunt u alle geïmplementeerde Logic apps in uw Azure-abonnement weer geven, of het nu de oorspronkelijke **Logic apps** of het resource type van de **logische app (preview)** is en taken selecteren die u helpen bij het beheren van deze Logic apps. Voor toegang tot beide resource typen hebt u echter zowel de **Azure Logic apps** als de **Azure Logic apps (preview)-** uitbrei dingen voor Visual Studio code nodig.

1. Selecteer op de werk balk links het pictogram van Azure. Vouw in het deel venster **Azure: Logic apps (preview)** uw abonnement uit, waarin alle geïmplementeerde Logic apps voor dat abonnement worden weer gegeven.

1. Open de logische app die u wilt beheren. Selecteer in het snelmenu van de logische app de taak die u wilt uitvoeren.

   U kunt bijvoorbeeld taken selecteren, zoals stoppen, starten, opnieuw starten of verwijderen van uw geïmplementeerde logische app.

   ![Scherm opname van Visual Studio code met het geopende uitbreidings venster Azure Logic Apps (preview) en de geïmplementeerde werk stroom.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Als u alle werk stromen in de logische app wilt weer geven, vouwt u uw logische app uit en vouwt u vervolgens het knoop punt **werk stromen** uit.

1. Als u een specifieke werk stroom wilt weer geven, opent u het snelmenu van de werk stroom en selecteert u **openen in Designer**, waarmee de werk stroom wordt geopend in de modus alleen-lezen.

   Als u de werk stroom wilt bewerken, hebt u de volgende opties:

   * In Visual Studio code opent u de **workflow.js** van uw project in een bestand in de werk stroom ontwerper, maakt u uw bewerkingen en implementeert u uw logische app in Azure.

   * [Zoek en open uw logische app](#manage-deployed-apps-portal)In de Azure Portal. Zoek, bewerk en sla de werk stroom op.

1. Als u de geïmplementeerde logische app wilt openen in de Azure Portal, opent u het snelmenu van de logische app en selecteert u **openen in portal**.

   De Azure Portal in uw browser wordt geopend, meldt u automatisch aan bij de portal als u bent aangemeld bij Visual Studio code en toont uw logische app.

   ![Scherm opname van de Azure Portal-pagina voor uw logische app in Visual Studio code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   U kunt zich ook afzonderlijk aanmelden bij de Azure Portal, het zoekvak van de portal gebruiken om uw logische app te zoeken en vervolgens uw logische app in de lijst met resultaten te selecteren.

   ![Scherm afbeelding met de Azure Portal en de zoek balk met zoek resultaten voor geïmplementeerde logische app, die wordt weer gegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Geïmplementeerde Logic apps beheren in de portal

In de Azure Portal kunt u alle geïmplementeerde Logic apps bekijken die zich in uw Azure-abonnement bevinden, ongeacht of ze het oorspronkelijke **Logic apps** bron type of het bron type van de **logische app (preview-versie)** zijn. Op dit moment wordt elk resource type geordend en beheerd als afzonderlijke categorieën in Azure. Voer de volgende stappen uit om logische apps te vinden die het resource type **logische app (preview)** hebben:

1. Voer in het zoekvak van Azure Portal in `logic app preview` . Wanneer de lijst met resultaten wordt weer gegeven, selecteert u onder **Services** de optie **logische app (preview)**.

   ![Scherm opname van het zoekvak van Azure Portal met de Zoek tekst ' Logic app preview '.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Zoek en selecteer in het deel venster **logische app (preview)** de logische app die u hebt geïmplementeerd vanuit Visual Studio code.

   ![Scherm opname van de Azure Portal en de logische app-resources (preview) die in azure zijn geïmplementeerd.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   De Azure Portal de afzonderlijke resource pagina voor de geselecteerde logische app wordt geopend.

   ![Scherm opname van de resource pagina van de werk stroom van uw logische app in de Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Als u de werk stromen in deze logische app wilt weer geven, selecteert u **werk stromen** in het menu van de logische app.

   In het deel venster **werk stromen** worden alle werk stromen weer gegeven in de huidige logische app. In dit voor beeld ziet u de werk stroom die u hebt gemaakt in Visual Studio code.

   ![Scherm opname van de resource pagina ' Logic app (preview) ' met het deel venster werk stromen open en de geïmplementeerde werk stroom](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Als u een werk stroom wilt weer geven, selecteert u in het deel venster **werk stromen** die werk stroom.

   Het werk stroom venster wordt geopend en toont meer informatie en taken die u kunt uitvoeren op die werk stroom.

   Als u bijvoorbeeld de stappen in de werk stroom wilt weer geven, selecteert u **Designer**.

   ![Scherm opname van het geselecteerde werk stroom venster Overzicht, terwijl in het werk stroom menu de geselecteerde opdracht "Designer" wordt weer gegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   De werk stroom ontwerper wordt geopend en toont de werk stroom die u hebt gemaakt in Visual Studio code. U kunt nu wijzigingen aanbrengen in deze werk stroom in de Azure Portal.

   ![Scherm opname van de werk stroom ontwerper en werk stroom die is geïmplementeerd vanuit Visual Studio code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Een andere werk stroom toevoegen in de portal

Via de Azure Portal kunt u lege werk stromen toevoegen aan een **logische app (preview)** -resource die u hebt geïmplementeerd vanuit Visual Studio code en deze werk stromen samen stellen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar de resource van de geïmplementeerde **logische app (preview)** en selecteer deze.

1. Selecteer **werk stromen** in het menu van de logische app. Selecteer **toevoegen** in het deel venster **werk stromen** .

   ![Scherm opname van het deel venster werk stromen en de werk balk van de geselecteerde logische app met de opdracht toevoegen is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Geef in het deel venster **nieuwe werk stroom** naam op voor de werk stroom. Selecteer **stateful** of **stateless** **>** **Create**.

   Nadat Azure uw nieuwe werk stroom heeft geïmplementeerd, die wordt weer gegeven in het deel venster **werk stromen** , selecteert u die werk stroom zodat u andere taken kunt beheren en uitvoeren, zoals het openen van de ontwerp functie of de code weergave.

   ![Scherm opname van de geselecteerde werk stroom met beheer-en controle opties.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Als u bijvoorbeeld de ontwerp functie voor een nieuwe werk stroom opent, ziet u een leeg canvas. U kunt deze werk stroom nu maken in de Azure Portal.

   ![Scherm afbeelding waarin de werk stroom ontwerper en een lege werk stroom worden weer gegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Uitvoerings geschiedenis voor stateless werk stromen inschakelen

Als u een stateless werk stroom eenvoudiger wilt opsporen, kunt u de uitvoerings geschiedenis voor die werk stroom inschakelen en de uitvoerings geschiedenis uitschakelen wanneer u klaar bent. Volg deze stappen voor Visual Studio code, of als u werkt in de Azure Portal, Zie [stateful en stateless werk stromen maken in de Azure Portal](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. Vouw in uw Visual Studio code-project de map **werk stroom-Designtime** uit en open de **local.settings.jsin** het bestand.

1. Voeg de `Workflows.{yourWorkflowName}.operationOptions` eigenschap toe en stel de waarde in op `WithStatelessRunHistory` , bijvoorbeeld:

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

1. Als u de uitvoerings geschiedenis wilt uitschakelen wanneer u klaar bent, stelt u de `Workflows.{yourWorkflowName}.OperationOptions` eigenschap in op `None` of verwijdert u de eigenschap en de waarde ervan.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Schakel de weer gave controle in de Azure Portal

Nadat u een **logische app (preview)** hebt geïmplementeerd vanuit Visual Studio code naar Azure, kunt u alle beschik bare uitvoerings geschiedenis en-Details voor een werk stroom in die resource bekijken met behulp van de Azure Portal en de **monitor** ervaring voor die werk stroom. U moet echter eerst de weergave capaciteit van de **monitor** inschakelen op de logische app-resource.

1. Zoek en selecteer in de [Azure Portal](https://portal.azure.com)de geïmplementeerde **logische app (preview)** -resource.

1. Selecteer **CORS** in het menu van de resource onder **API**.

1. Voeg het Joker teken (*) toe in het deel venster **CORS** onder **toegestane oorsprongen**.

1. Wanneer u klaar bent, selecteert u op de **CORS** -werk balk de optie **Opslaan**.

   ![Scherm opname van de Azure Portal met een resource met geïmplementeerde logische apps (preview). In het menu resource is ' CORS ' geselecteerd met een nieuwe vermelding voor ' toegestane oorsprongen ' ingesteld op het Joker teken ' * '.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Application Insights na implementatie inschakelen of openen

Tijdens de uitvoering van de werk stroom verzendt uw logische app telemetrie en andere gebeurtenissen. U kunt deze telemetrie gebruiken om beter inzicht te krijgen in hoe goed uw werk stroom wordt uitgevoerd en hoe de Logic Apps runtime op verschillende manieren werkt. U kunt uw werk stroom bewaken met behulp van [Application Insights](../azure-monitor/app/app-insights-overview.md), die bijna realtime-telemetrie (Live Metrics) biedt. Deze mogelijkheid kan u helpen fouten en prestatie problemen gemakkelijker te onderzoeken wanneer u deze gegevens gebruikt om problemen te onderzoeken, waarschuwingen in te stellen en grafieken te maken.

Als de instellingen voor het maken en implementeren van uw logische app gebruikmaken van [Application Insights](../azure-monitor/app/app-insights-overview.md), kunt u optioneel diagnostische logboek registratie en tracering inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app implementeert vanuit Visual Studio code of na de implementatie. U moet een Application Insights-exemplaar hebben, maar u kunt deze resource [vooraf](../azure-monitor/app/create-workspace-resource.md)maken wanneer u uw logische app implementeert of na de implementatie.

Voer de volgende stappen uit om Application Insights in te scha kelen op een geïmplementeerde logische app of Application Insights gegevens te controleren wanneer deze al zijn ingeschakeld:

1. Zoek in de Azure Portal uw geïmplementeerde logische app.

1. Selecteer **Application Insights** onder **instellingen** in het menu van de logische app.

1. Als Application Insights niet is ingeschakeld, selecteert u **Application Insights inschakelen** in het deel venster **Application Insights** . Nadat het deel venster is bijgewerkt, selecteert u onderaan **Toep assen**.

   Als Application Insights is ingeschakeld, selecteert u in het deel venster **Application Insights** **Application Insights gegevens weer geven**.

Nadat Application Insights is geopend, kunt u verschillende metrische gegevens voor uw logische app bekijken. Raadpleeg de volgende onderwerpen voor meer informatie:

* [Azure Logic Apps Anywhere-monitor uitvoeren met Application Insights-deel 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps Anywhere-monitor uitvoeren met Application Insights-deel 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Implementeren naar docker

U kunt uw logische app implementeren in een [docker-container](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) als hostomgeving met behulp van de [.net cli](/dotnet/core/tools/). Met deze opdrachten kunt u het project van de logische app maken en publiceren. U kunt vervolgens uw docker-container bouwen en uitvoeren als het doel voor het implementeren van uw logische app.

Als u niet bekend bent met docker, raadpleegt u de volgende onderwerpen:

* [Wat is Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Inleiding tot containers en docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Inleiding tot .NET en docker](/dotnet/core/docker/introduction)
* [Docker-containers, installatie kopieën en registers](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Zelf studie: aan de slag met docker (Visual Studio code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Vereisten

* Het Azure Storage account dat door uw logische app wordt gebruikt voor implementatie

* Een docker-bestand voor de werk stroom die u gebruikt bij het bouwen van uw docker-container

  Dit voor beeld van een docker-bestand implementeert bijvoorbeeld een logische app en specificeert de connection string die de toegangs sleutel bevat voor het Azure Storage account dat is gebruikt voor het publiceren van de logische app naar de Azure Portal. Zie [opslag account ophalen Connection String](#find-storage-account-connection-string)om deze teken reeks te vinden. Raadpleeg [Aanbevolen procedures voor het schrijven van docker-bestanden](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)voor meer informatie.
  
  > [!IMPORTANT]
  > Voor productie scenario's moet u ervoor zorgen dat u deze geheimen en gevoelige informatie beveiligt en beveiligt, bijvoorbeeld door gebruik te maken van een sleutel kluis. Voor docker-bestanden raadpleegt u [installatie kopieën bouwen met BuildKit](https://docs.docker.com/develop/develop-images/build_enhancements/) en [gevoelige gegevens beheren met docker-geheimen](https://docs.docker.com/engine/swarm/secrets/).

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

### <a name="get-storage-account-connection-string"></a>connection string van opslag account ophalen

Voordat u uw docker-container installatie kopie kunt bouwen en uitvoeren, moet u de connection string die de toegangs sleutel bevat, ophalen naar uw opslag account. U hebt dit opslag account eerder gemaakt, zoals het gebruik van de uitbrei ding voor macOS of Linux, of wanneer u uw logische app hebt geïmplementeerd op de Azure Portal.

Ga als volgt te werk om deze connection string te zoeken en te kopiëren:

1. Selecteer in het Azure Portal, in het menu van het opslag account, onder **instellingen** de optie **toegangs sleutels**. 

1. Zoek in het deel venster **toegangs toetsen** de Connection String van het opslag account, die er ongeveer als volgt uitziet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Scherm opname van de Azure Portal met toegangs sleutels voor het opslag account en connection string gekopieerd.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Raadpleeg voor meer informatie [beheer van opslag accounts beheren](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Sla de connection string op een veilige plek op, zodat u deze teken reeks kunt toevoegen aan het docker-bestand dat u voor de implementatie gebruikt. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Hoofd sleutel voor opslag account zoeken

Als uw werk stroom een aanvraag trigger bevat, moet u [de retour-URL van de trigger ophalen](#get-callback-url-request-trigger) nadat u uw docker-container installatie kopie hebt gemaakt en uitgevoerd. Voor deze taak moet u ook de waarde van de hoofd sleutel opgeven voor het opslag account dat u voor de implementatie gebruikt.

1. Als u deze hoofd sleutel wilt vinden, opent u in uw project het bestand **Azure-webjobs-geheimen/{Deployment-name}/host.js** .

1. Zoek de `AzureWebJobsStorage` eigenschap en kopieer de sleutel waarde uit deze sectie:

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

1. Sla deze sleutel waarde op een veilige plek op, zodat u deze later kunt gebruiken.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Uw docker-container installatie kopie bouwen en uitvoeren

1. Bouw uw docker-container installatie kopie met behulp van uw docker-bestand en voer deze opdracht uit:

   `docker build --tag local/workflowcontainer .`

   Zie [docker build](https://docs.docker.com/engine/reference/commandline/build/)voor meer informatie.

1. Voer de container lokaal uit met behulp van deze opdracht:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Zie [docker run](https://docs.docker.com/engine/reference/commandline/run/)(Engelstalig) voor meer informatie.

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Retour-URL ophalen voor aanvraag trigger

Voor een werk stroom die gebruikmaakt van de aanvraag trigger, de retour-URL van de trigger ophalen door deze aanvraag te verzenden:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

De `{trigger-name}` waarde is de naam van de aanvraag trigger die wordt weer gegeven in de JSON-definitie van de werk stroom. De `{master-key}` waarde wordt gedefinieerd in het Azure Storage account dat u voor de `AzureWebJobsStorage` eigenschap in het bestand hebt ingesteld, **Azure-webjobs-geheimen/{Deployment-name}/host.jsop**. Zie voor meer informatie de [hoofd sleutel opslag account zoeken](#find-storage-account-master-key).

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Items uit de ontwerp functie verwijderen

Voer een van de volgende stappen uit om een item in uw werk stroom te verwijderen uit de ontwerp functie:

* Selecteer het item, open het snelmenu van het item (SHIFT + F10) en selecteer **verwijderen**. Selecteer **OK** om de opdracht te bevestigen.

* Selecteer het item en druk op de toets DELETE. Selecteer **OK** om de opdracht te bevestigen.

* Selecteer het item, zodat het detail venster voor dat item wordt geopend. Open in de rechter bovenhoek van het deel venster het menu met weglatings tekens (**...**) en selecteer **verwijderen**. Selecteer **OK** om de opdracht te bevestigen.

  ![Scherm afbeelding met een geselecteerd item in de ontwerp functie met het deel venster geopende Details plus de geselecteerde knop met weglatings tekens en de opdracht verwijderen.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Als het menu met weglatings tekens niet zichtbaar is, vouwt u Visual Studio code Window breed genoeg uit zodat in het detail venster de knop met weglatings tekens (**...**) in de rechter bovenhoek wordt weer gegeven.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Fouten en problemen oplossen

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Het openen van de ontwerp functie is mislukt

Wanneer u de ontwerp functie probeert te openen, wordt deze fout weer geven: **' de werk stroom ontwerp tijd kan niet worden gestart '**. Als u eerder probeerde de ontwerp functie te openen en vervolgens het project hebt stopgezet of verwijderd, wordt de uitbreidings bundel mogelijk niet correct gedownload. Voer de volgende stappen uit om te controleren of dit de oorzaak van het probleem is:

  1. Open in Visual Studio code het venster uitvoer. Selecteer in het menu **weer gave** de optie **uitvoer**.

  1. Selecteer in de lijst in de titel balk van het uitvoer venster **Azure Logic apps (preview)** zodat u de uitvoer van de uitbrei ding kunt controleren, bijvoorbeeld:

     ![Scherm opname van het uitvoer venster waarin ' Azure Logic Apps ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Controleer de uitvoer en controleer of dit fout bericht wordt weer gegeven:

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

   Om deze fout op te lossen, verwijdert u de map **ExtensionBundles** op deze locatie **. ..\Users \{ uw-gebruikers naam} \AppData\Local\Temp\Functions\ExtensionBundles** en probeer het **workflow.js** opnieuw te openen in het bestand in de ontwerp functie.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Er ontbreken nieuwe triggers en acties in de ontwerp kiezer voor eerder gemaakte werk stromen

Azure Logic Apps Preview ondersteunt ingebouwde acties voor Azure function-bewerkingen, vloeistof bewerkingen en XML-bewerkingen, zoals **XML-validatie** en **XML-trans formatie**. Voor eerder gemaakte Logic apps kunnen deze acties echter niet worden weer gegeven in de ontwerp kiezer, zodat u kunt selecteren of Visual Studio code een verouderde versie van de uitbreidings bundel gebruikt `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

De functies van de **Azure functions** -connector en acties worden niet weer gegeven in de ontwerp kiezer, tenzij u **connectors van Azure gebruiken** hebt ingeschakeld of geselecteerd tijdens het maken van uw logische app. Als u de door Azure geïmplementeerde connectors tijdens het maken van de app niet hebt ingeschakeld, kunt u ze in Visual Studio code inschakelen voor uw project. Open de **workflow.jsin** het snelmenu en selecteer **Connect oren van Azure gebruiken**.

Als u de verouderde bundel wilt herstellen, volgt u deze stappen om de verouderde bundel te verwijderen, waardoor Visual Studio code de uitbreidings bundel automatisch bijwerkt naar de nieuwste versie.

> [!NOTE]
> Deze oplossing is alleen van toepassing op Logic apps die u maakt en implementeert met behulp van Visual Studio code met de extensie Azure Logic Apps (preview), niet de Logic apps die u hebt gemaakt met behulp van de Azure Portal. Zie [ondersteunde triggers en er ontbreken acties in de ontwerp functie van de Azure Portal](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Sla uw werk op dat u niet wilt kwijt raken en sluit Visual Studio.

1. Blader op de computer naar de volgende map die versie mappen bevat voor de bestaande bundel:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Verwijder de versie map voor de eerdere bundel, bijvoorbeeld als u een map hebt voor versie 1.1.3, verwijder de map.

1. Blader nu naar de volgende map die versie mappen bevat voor het vereiste NuGet-pakket:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Verwijder de versie map voor het oudere pakket, bijvoorbeeld als u een map voor versie 1.0.0.8-Preview hebt, verwijdert u die map.

1. Open Visual Studio code, het project en de **workflow.js** in het bestand in de ontwerp functie opnieuw.

De ontbrekende triggers en acties worden nu weer gegeven in de ontwerp functie.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 onjuiste aanvraag" wordt weer gegeven bij een trigger of actie

Wanneer een uitvoering mislukt en u de weer gave controle in bewaking inspecteert, kan deze fout worden weer gegeven bij een trigger of actie met een langere naam, waardoor de onderliggende URI (Uniform Resource Identifier) de standaard teken limiet overschrijdt.

U kunt dit probleem oplossen door de- `UrlSegmentMaxCount` en `UrlSegmentMaxLength` register sleutels op uw computer te bewerken door de volgende stappen uit te voeren. De standaard waarden van deze sleutel worden in dit onderwerp [Http.sys register instellingen voor Windows](/troubleshoot/iis/httpsys-registry-windows)beschreven.

> [!IMPORTANT]
> Voordat u begint, moet u ervoor zorgen dat u uw werk opslaat. Voor deze oplossing moet u de computer opnieuw opstarten nadat u klaar bent om de wijzigingen van kracht te laten worden.

1. Open het venster **uitvoeren** op uw computer en voer de opdracht uit om `regedit` de REGI ster-editor te openen.

1. Selecteer in het vak **gebruikers account** de optie **Ja** om uw wijzigingen aan uw computer toe te staan.

1. Vouw in het linkerdeel venster, onder **computer**, de knoop punten langs het pad uit, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** en selecteer vervolgens **para meters**.

1. Zoek in het rechterdeel venster de- `UrlSegmentMaxCount` en- `UrlSegmentMaxLength` register sleutels.

1. Verhoog deze sleutel waarden genoeg zodat de Uri's de namen kunnen bevatten die u wilt gebruiken. Als deze sleutels niet bestaan, voegt u deze toe aan de map **para meters** door de volgende stappen uit te voeren:

   1. Selecteer in het snelmenu **para meters** de optie **nieuwe**  >  **DWORD-waarde (32-bits)**.

   1. Typ in het invoervak dat wordt weer gegeven, `UrlSegmentMaxCount` de naam van de nieuwe sleutel.

   1. Open het snelmenu van de nieuwe sleutel en selecteer **wijzigen**.

   1. Geef in het vak **teken reeks bewerken** een waarde op voor de sleutel waarde voor **waarden** die u wilt weer geven in hexadecimale of decimale notatie. `400`In hexadecimale notatie is bijvoorbeeld gelijk aan `1024` in decimale notatie.

   1. Herhaal deze stappen om de sleutel waarde toe te voegen `UrlSegmentMaxLength` .

   Nadat u deze sleutel waarden hebt verhoogd of toegevoegd, ziet de REGI ster-editor eruit als in dit voor beeld:

   ![Scherm opname van de REGI ster-editor.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Wanneer u klaar bent, start u de computer opnieuw op om de wijzigingen van kracht te laten worden.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Sessie voor fout opsporing kan niet worden gestart

Wanneer u probeert een foutopsporingssessie te starten, wordt de fout **' fout bestaat na het uitvoeren van preLaunchTask ' generateDebugSymbols ' '** weer geven. Om dit probleem op te lossen, bewerkt u de **tasks.jsin** het bestand in uw project om het genereren van symbolen over te slaan.

1. Vouw in uw project de map **. vscode** uit en open de **tasks.jsin** het bestand.

1. Verwijder in de volgende taak de regel, `"dependsOn: "generateDebugSymbols"` samen met de komma die de voor gaande regel eindigt, bijvoorbeeld:

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

We horen graag van u over uw ervaringen met de uitbrei ding Azure Logic Apps (preview).

* Voor fouten of problemen [maakt u uw problemen in github](https://github.com/Azure/logicapps/issues).
* Voor vragen, aanvragen, opmerkingen en andere feedback [gebruikt u dit feedback formulier](https://aka.ms/lafeedback).
