---
title: Logic Apps Preview-werk stromen maken in de Azure Portal
description: Werk stromen bouwen en uitvoeren voor automatiserings-en integratie scenario's met Azure Logic Apps Preview in de Azure Portal.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 3cf5047dbb79f6d8b35b0fe089069a20ab4a50a6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736336"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Stateful en stateless werk stromen maken in de Azure Portal met Azure Logic Apps Preview

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met [Azure Logic Apps Preview](logic-apps-overview-preview.md)kunt u automatiserings-en integratie oplossingen maken voor apps, gegevens, Cloud Services en systemen door logische apps te maken en uit te voeren die [ *stateful* en *stateless* werk stromen](logic-apps-overview-preview.md#stateful-stateless) bevatten in de Azure portal door te beginnen met het nieuwe resource type **Logic app (preview)** . Met dit nieuwe type logische app kunt u meerdere werk stromen bouwen die worden aangedreven door de opnieuw ontworpen Azure Logic Apps Preview-runtime, die draag baarheid, betere prestaties en flexibiliteit biedt voor het implementeren en uitvoeren in verschillende hosting omgevingen, niet alleen Azure, maar ook docker-containers. Zie [overzicht van Azure Logic Apps Preview voor](logic-apps-overview-preview.md)meer informatie over het type van de nieuwe logische app.

![Scherm opname van de Azure Portal met de werk stroom ontwerper voor de resource ' Logic app (preview) '.](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

In de Azure Portal kunt u beginnen met het maken van een nieuwe **logische app (preview)** -resource. U kunt ook beginnen met [het maken van een project in Visual Studio code met de extensie Azure Logic apps (preview)](create-stateful-stateless-workflows-visual-studio-code.md), maar beide benaderingen bieden u de mogelijkheid om uw logische app te implementeren en uit te voeren in dezelfde soorten hosting omgevingen.

Ondertussen kunt u nog steeds het type van de oorspronkelijke logische app maken. Hoewel de ontwikkelings ervaring in de portal verschilt van de oorspronkelijke en nieuwe typen logische apps, kan uw Azure-abonnement beide typen bevatten. U kunt alle geïmplementeerde Logic apps in uw Azure-abonnement weer geven en openen, maar de apps zijn ingedeeld in hun eigen categorieën en secties.

In dit artikel wordt uitgelegd hoe u uw logische app en werk stroom in de Azure Portal bouwt met behulp van het resource type **Logic app (preview)** en de volgende taken op hoog niveau uitvoert:

* Maak de nieuwe logische app-resource en voeg een lege werk stroom toe.

* Voeg een trigger en actie toe.

* Een uitvoering van een werk stroom activeren.

* De uitvoerings-en trigger geschiedenis van de werk stroom weer geven.

* Schakel de Application Insights na de implementatie in of open deze.

* Schakel de uitvoerings geschiedenis voor stateless werk stromen in.

> [!NOTE]
> Raadpleeg de [pagina met bekende problemen Logic apps open bare preview in github](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)voor meer informatie over bekende problemen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Een [Azure Storage-account](../storage/common/storage-account-overview.md) omdat de resource van de **logische app (preview)** wordt aangedreven door Azure functions en [opslag vereisten heeft die vergelijkbaar zijn met functie-apps](../azure-functions/storage-considerations.md). U kunt een bestaand opslag account gebruiken of u kunt een opslag account vooraf maken of tijdens het maken van een logische app.

  > [!NOTE]
  > [Stateful Logic apps](logic-apps-overview-preview.md#stateful-stateless) voeren opslag transacties uit, zoals het gebruiken van wacht rijen voor het plannen en opslaan van werk stroom statussen in tabellen en blobs. Bij deze trans acties worden [Azure Storage kosten](https://azure.microsoft.com/pricing/details/storage/)in rekening gebracht. Zie [stateful versus stateless](logic-apps-overview-preview.md#stateful-stateless)voor meer informatie over hoe stateful Logic apps gegevens opslaat in externe opslag.

* Als u wilt implementeren in een docker-container, hebt u een bestaande docker-container installatie kopie nodig. U kunt deze installatie kopie bijvoorbeeld maken via [Azure container Registry](../container-registry/container-registry-intro.md), [app service](../app-service/overview.md)of [Azure container instance](../container-instances/container-instances-overview.md). 

* Als u in dit artikel dezelfde voorbeeld logische app wilt maken, hebt u een Office 365 Outlook-e-mail account nodig dat gebruikmaakt van een werk-of school account van micro soft om u aan te melden.

  Als u een andere [e-mail connector wilt gebruiken die wordt ondersteund door Azure Logic apps](/connectors/), zoals Outlook.com of [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), kunt u nog steeds het voor beeld volgen en zijn de algemene stappen hetzelfde, maar uw gebruikers interface en opties kunnen op sommige manieren verschillen. Als u bijvoorbeeld de Outlook.com-connector gebruikt, gebruikt u uw persoonlijke Microsoft-account in plaats van u aan te melden.

* Als u de logische app wilt testen die u in dit artikel hebt gemaakt, hebt u een hulp programma nodig waarmee u aanroepen kunt verzenden naar de trigger voor aanvragen. Dit is de eerste stap in de logische app. Als u zo'n hulp programma niet hebt, kunt u [postman](https://www.postman.com/downloads/)downloaden, installeren en gebruiken.

* Als u uw logische app maakt met instellingen die gebruikmaken van [Application Insights](../azure-monitor/app/app-insights-overview.md), kunt u optioneel diagnostische logboek registratie en tracering inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app maakt of na de implementatie. U moet een Application Insights-exemplaar hebben, maar u kunt deze resource [vooraf](../azure-monitor/app/create-workspace-resource.md)maken wanneer u uw logische app maakt, of na de implementatie.

## <a name="create-the-logic-app-resource"></a>De logische app-resource maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Voer in het zoekvak van Azure Portal een `logic app preview` logische app in en selecteer deze **(preview)**.

   ![Scherm opname van het zoekvak van Azure Portal met de zoek term ' Logic app preview ' en de resource ' Logic app (preview) ' geselecteerd.](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. Selecteer op de pagina **logische app (voor beeld)** de optie **toevoegen**.

1. Geef op de pagina **logische app maken (voor beeld)** op het tabblad **basis beginselen** deze informatie over uw logische app op.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat moet worden gebruikt voor uw logische app. |
   | **Resourcegroep** | Ja | <*Naam-van-Azure-resourcegroep*> | De Azure-resource groep waar u uw logische app en gerelateerde resources maakt. Deze resource naam moet uniek zijn tussen regio's en mag alleen letters, cijfers, afbreek streepjes ( **-** ), onderstrepings tekens (**_**), haakjes (**()**) en punten (**.**) bevatten. <p><p>In dit voor beeld wordt een resource groep met de naam gemaakt `Fabrikam-Workflows-RG` . |
   | **Naam van logische app** | Ja | <*naam-van-logische-app*> | De te gebruiken naam voor uw logische app. Deze resource naam moet uniek zijn tussen regio's en mag alleen letters, cijfers, afbreek streepjes ( **-** ), onderstrepings tekens (**_**), haakjes (**()**) en punten (**.**) bevatten. <p><p>In dit voor beeld wordt een logische app met de naam gemaakt `Fabrikam-Workflows` . <p><p>**Opmerking**: de naam van de logische app haalt automatisch het achtervoegsel op, `.azurewebsites.net` omdat de resource van de **logische app (preview)** wordt ingeschakeld door Azure functions, die gebruikmaakt van dezelfde app-naam Conventie. |
   | **Publiceren** | Ja | <*implementatie-omgeving*> | De implementatie bestemming voor uw logische app. U kunt implementeren in azure door een **werk stroom** of **docker-container** te selecteren. <p><p>In dit voor beeld wordt de **werk stroom** gebruikt, waarmee de **logische app (preview-resource)** wordt geïmplementeerd op de Azure Portal. <p><p>**Opmerking**: voordat u **docker-container** selecteert, moet u ervoor zorgen dat u de docker-container installatie kopie maakt. U kunt deze installatie kopie bijvoorbeeld maken via [Azure container Registry](../container-registry/container-registry-intro.md), [app service](../app-service/overview.md)of [Azure container instance](../container-instances/container-instances-overview.md). Op die manier kunt u, nadat u **docker-container** hebt geselecteerd, [de container opgeven die u wilt gebruiken in de instellingen van de logische app](#set-docker-container). |
   | **Regio** | Ja | <*Azure-regio*> | De Azure-regio die moet worden gebruikt bij het maken van uw resource groep en resources. <p><p>In dit voorbeeld wordt **US - west** gebruikt. |
   |||||

   Hier volgt een voorbeeld:

   ![Scherm opname van de pagina Azure Portal en ' Logic app maken (preview) '.](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Geef vervolgens op het tabblad **Hosting** deze informatie over de opslag oplossing en het hosting plan op die u wilt gebruiken voor uw logische app.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Opslagaccount** | Ja | <*Azure-storage-account-name*> | Het [Azure Storage-account](../storage/common/storage-account-overview.md) dat moet worden gebruikt voor opslag transacties. Deze resource naam moet uniek zijn voor alle regio's en mag 3-24 tekens bevatten met alleen cijfers en kleine letters. Selecteer een bestaand account of maak een nieuw account. <p><p>In dit voor beeld wordt een opslag account gemaakt met de naam `fabrikamstorageacct` . |
   | **Plantype** | Ja | <*Azure-hosting-plan*> | Het [hosting plan](../app-service/overview-hosting-plans.md) dat moet worden gebruikt voor het implementeren van uw logische app, een van de [**functies Premium**](../azure-functions/functions-premium-plan.md) of [ **app service-plan** (toegewezen)](../azure-functions/dedicated-plan.md). Uw keuze heeft invloed op de mogelijkheden en prijs categorieën die later beschikbaar zijn voor u. <p><p>In dit voor beeld wordt het **app service-abonnement** gebruikt. <p><p>**Opmerking**: dit is vergelijkbaar met Azure functions. voor het resource type **Logic app (preview)** is een hosting plan en een prijs categorie vereist. Verbruiks abonnementen worden niet ondersteund of zijn niet beschikbaar voor dit resource type. Raadpleeg de volgende onderwerpen voor meer informatie: <p><p>- [Azure Functions schalen en hosten](../azure-functions/functions-scale.md) <br>- [Prijs informatie voor App Service](https://azure.microsoft.com/pricing/details/app-service/) <p><p>Het plan functions Premium biedt bijvoorbeeld toegang tot netwerk mogelijkheden, zoals verbinding maken en integreren met Azure Virtual Networks, vergelijkbaar met Azure Functions wanneer u uw Logic apps maakt en implementeert. Raadpleeg de volgende onderwerpen voor meer informatie: <p><p>- [Azure Functions-netwerk opties](../azure-functions/functions-networking-options.md) <br>- [Azure Logic Apps Anywhere-netwerk mogelijkheden uitvoeren met Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Windows-abonnement** | Ja | <*plan-naam*> | De naam van het plan dat moet worden gebruikt. Selecteer een bestaand plan of geef de naam voor een nieuw plan op. <p><p>In dit voor beeld wordt de naam gebruikt `Fabrikam-Service-Plan` . |
   | **SKU en grootte** | Ja | <*prijs categorie*> | De [prijs categorie](../app-service/overview-hosting-plans.md) die moet worden gebruikt voor het hosten van uw logische app. Uw keuzes worden beïnvloed door het type abonnement dat u eerder hebt gekozen. Als u de standaardlaag wilt wijzigen, selecteert u **grootte wijzigen**. U kunt vervolgens andere prijs categorieën selecteren op basis van de werk belasting die u nodig hebt. <p><p>In dit voor beeld wordt de gratis **F1-prijs categorie** gebruikt voor werk belastingen voor ontwikkelen **en testen** . Bekijk [app service prijs informatie](https://azure.microsoft.com/pricing/details/app-service/)voor meer informatie. |
   |||||

1. Als de instellingen voor maken en implementeren worden ondersteund met behulp van [Application Insights](../azure-monitor/app/app-insights-overview.md), kunt u optioneel diagnostische logboek registratie en tracering inschakelen voor uw logische app.

   1. Stel op het tabblad **controle** , onder **Application Insights**, **Application Insights** in op **Ja** als deze optie nog niet is geselecteerd.

   1. Voor de instelling **Application Insights** selecteert u een bestaand Application Insights exemplaar of als u een nieuw exemplaar wilt maken, selecteert u **nieuwe maken** en geeft u de naam op die u wilt gebruiken.

1. Wanneer Azure de instellingen van uw logische app valideert, selecteert u **maken** op het tabblad **controleren + maken** .

   Bijvoorbeeld:

   ![Scherm afbeelding met de Azure Portal en de nieuwe logische app-resource-instellingen.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Als er een validatie fout optreedt nadat u **maken**, openen en de fout details controleren hebt geselecteerd. Als de geselecteerde regio bijvoorbeeld een quotum bereikt voor resources die u probeert te maken, moet u mogelijk een andere regio proberen.

   Nadat de implementatie van Azure is voltooid, wordt uw logische app automatisch Live en uitgevoerd, maar nog niets, omdat er geen werk stromen zijn.

1. Selecteer op de pagina voltooiing van implementatie de optie **Ga naar resource** zodat u kunt beginnen met het bouwen van uw werk stroom. Als u **docker-container** voor het implementeren van uw logische app hebt geselecteerd, gaat u door met de [stappen om informatie over die docker-container op te geven](#set-docker-container).

   ![Scherm opname van de Azure Portal en de voltooide implementatie.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Docker-container voor implementatie opgeven

Voordat u met deze stappen begint, hebt u een docker-container installatie kopie nodig. U kunt deze installatie kopie bijvoorbeeld maken via [Azure container Registry](../container-registry/container-registry-intro.md), [app service](../app-service/overview.md)of [Azure container instance](../container-instances/container-instances-overview.md). U kunt vervolgens informatie over uw docker-container opgeven nadat u uw logische app hebt gemaakt.

1. Ga in het Azure Portal naar de resource van de logische app.

1. Selecteer in het menu van de logische app onder **instellingen** de optie **implementatie centrum**.

1. Volg de instructies in het deel venster **implementatie centrum** om de Details voor uw docker-container op te geven en te beheren.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Een lege werk stroom toevoegen

1. Nadat Azure de resource heeft geopend, selecteert u **werk stromen** in het menu van de logische app. Selecteer **toevoegen** op de werk balk van werk **stromen** .

   ![Scherm opname van het resource menu van de logische app waarin "werk stromen" is geselecteerd en vervolgens op de werk balk, "toevoegen" is geselecteerd.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Wanneer het deel venster **nieuwe werk stroom** wordt geopend, geeft u een naam op voor uw werk stroom en kiest u het werk stroom type [ **stateful** of **stateless**](logic-apps-overview-preview.md#stateful-stateless) . Selecteer **Maken** als u klaar bent.

   In dit voor beeld wordt een lege stateful werk stroom met de naam toegevoegd `Fabrikam-Stateful-Workflow` . De werk stroom is standaard ingeschakeld, maar doet niets totdat u een trigger en acties toevoegt.

   ![Scherm opname waarin de zojuist toegevoegde lege stateful werk stroom "fabrikam-stateful-workflow" wordt weer gegeven.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Open vervolgens de lege werk stroom in de ontwerp functie om een trigger en een actie toe te voegen.

   1. Selecteer de lege werk stroom in de lijst met werk stromen.

   1. Selecteer **ontwerp** onder **ontwikkelaar** in het menu werk stroom.

      Op het ontwerp oppervlak wordt de prompt **een bewerking kiezen** al weer gegeven en is deze optie standaard ingeschakeld, zodat het deel venster **een trigger toevoegen** ook wordt geopend.

      ![Scherm afbeelding met de geopende werk stroom ontwerper met ' Kies een bewerking ' geselecteerd op het ontwerp oppervlak.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Een trigger en een actie toevoegen

In dit voor beeld wordt een werk stroom gebouwd met de volgende stappen:

* De ingebouwde [aanvraag trigger](../connectors/connectors-native-reqres.md), **wanneer er een HTTP-aanvraag wordt ontvangen**, die inkomende aanroepen of aanvragen ontvangt en een eind punt maakt dat andere services of logische apps kan aanroepen.

* De [Office 365 Outlook-actie](../connectors/connectors-create-api-office365-outlook.md), **een e-mail verzenden**.

* De ingebouwde [reactie actie](../connectors/connectors-native-reqres.md)die u gebruikt om een antwoord te verzenden en gegevens terug te sturen naar de aanroeper.

### <a name="add-the-request-trigger"></a>De aanvraag trigger toevoegen

Voordat u een trigger aan een lege werk stroom kunt toevoegen, moet u ervoor zorgen dat de werk stroom ontwerper is geopend en dat de prompt voor het **kiezen van een bewerking** is geselecteerd op het ontwerp oppervlak.

1. Naast het ontwerp oppervlak, in het deel venster **een trigger toevoegen** onder het zoekvak **een bewerking kiezen** , controleert u of het **ingebouwde** tabblad is geselecteerd. Dit tabblad bevat triggers die systeem eigen worden uitgevoerd in Azure Logic Apps.

1. Voer in het zoekvak **een bewerking kiezen** het selectie vakje in `when a http request` en selecteer de ingebouwde aanvraag trigger die **wordt genoemd wanneer een HTTP-aanvraag wordt ontvangen**.

   ![Scherm opname van de ontwerp functie en * * een trigger toevoegen * * deel venster met de trigger wanneer een HTTP-aanvraag is ontvangen.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Wanneer de trigger wordt weer gegeven op de Designer, wordt het detail venster van de trigger geopend om de eigenschappen, instellingen en andere acties van de trigger weer te geven.

   ![Scherm opname van de ontwerper met de trigger geselecteerd wanneer een HTTP-aanvraag is ontvangen en het deel venster trigger Details geopend.](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Als het detail venster niet wordt weer gegeven, zorgt u ervoor dat de trigger is geselecteerd in de ontwerp functie.

1. Als u een item uit de ontwerp functie wilt verwijderen, [voert u de volgende stappen uit om items uit de ontwerp functie te verwijderen](#delete-from-designer).

1. Als u uw werk wilt opslaan, selecteert u **Opslaan** op de werk balk van de ontwerp functie.

   Wanneer u een werk stroom voor het eerst opslaat en deze werk stroom begint met een aanvraag trigger, wordt door de Logic Apps-service automatisch een URL gegenereerd voor een eind punt dat is gemaakt door de trigger van de aanvraag. Wanneer u later uw werk stroom test, verzendt u een aanvraag naar deze URL, waarmee de trigger wordt geactiveerd en de uitvoering van de werk stroom wordt gestart.

### <a name="add-the-office-365-outlook-action"></a>De Office 365 Outlook-actie toevoegen

1. Selecteer in de ontwerp functie, onder de trigger die u hebt toegevoegd, de optie **nieuwe stap**.

   De prompt **een bewerking kiezen** wordt weer gegeven in de ontwerp functie en het deel venster **actie toevoegen** wordt opnieuw geopend, zodat u de volgende actie kunt selecteren.

   > [!NOTE]
   > Als in het deel venster **actie toevoegen** het fout bericht wordt weer gegeven, kan eigenschap filter niet worden gelezen. Sla uw werk stroom op, laad de pagina opnieuw, open de werk stroom opnieuw en probeer het opnieuw.

1. Selecteer in het deel venster **actie toevoegen** onder het zoekvak **een bewerking kiezen** de optie **Azure**. Dit tabblad bevat de beheerde connectors die beschikbaar zijn en zijn geïmplementeerd in Azure.

   > [!NOTE]
   > Als het fout bericht wordt weer gegeven in het deel venster **actie toevoegen** , `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` slaat u de werk stroom op, laadt u de pagina opnieuw, opent u de werk stroom opnieuw en probeert u de actie opnieuw toe te voegen.

   In dit voor beeld wordt de Office 365 Outlook-actie met de naam **een E-mail verzenden (v2)** gebruikt.

   ![Scherm afbeelding met de ontwerp functie en het * * deel venster actie toevoegen * * met de actie Office 365 Outlook "een e-mail verzenden" geselecteerd.](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. In het detail venster van de actie klikt u op het tabblad **verbinding maken** op **Aanmelden** zodat u een verbinding kunt maken met uw e-mail account.

   ![Scherm afbeelding met de ontwerp functie en het detail venster ' een e-mail (v2) verzenden ' waarbij ' Aanmelden ' is geselecteerd.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. Wanneer u wordt gevraagd om toestemming te geven voor toegang tot uw e-mail account, meldt u zich aan met uw account referenties.

   > [!NOTE]
   > Als u het fout bericht ontvangt, `Failed with error: 'The browser is closed.'. Please sign in again` controleert u of uw browser cookies van derden blokkeert. Als deze cookies zijn geblokkeerd, voegt `https://portal.azure.com` u toe aan de lijst met sites waarvoor cookies kunnen worden gebruikt. Als u de Incognito-modus gebruikt, moet u ervoor zorgen dat cookies van derden niet worden geblokkeerd tijdens het werken in deze modus.
   > 
   > Indien nodig, laadt u de pagina opnieuw, opent u de werk stroom, voegt u de e-mail actie opnieuw toe en probeert u de verbinding te maken.

   Nadat de verbinding door Azure is gemaakt, wordt de actie **een E-mail verzenden** weer gegeven in de ontwerp functie en is deze standaard geselecteerd. Als de actie niet is geselecteerd, selecteert u de actie zodat het detail venster ook wordt geopend.

1. Geef in het detail venster van de actie op het tabblad **para meters** de vereiste informatie voor de actie op, bijvoorbeeld:

   ![Scherm afbeelding met de ontwerp functie en het detail venster e-mail verzenden met het tabblad para meters geselecteerd.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | De e-mail ontvanger, die uw e-mail adres kan zijn voor test doeleinden. In dit voor beeld wordt het fictieve e-mail adres gebruikt `sophiaowen@fabrikam.com` . |
   | **Onderwerp** | Ja | `An email from your example workflow` | Het onderwerp van de e-mail |
   | **Hoofdtekst** | Ja | `Hello from your example workflow!` | De inhoud van de hoofd tekst van de e-mail |
   ||||

   > [!NOTE]
   > Wanneer u wijzigingen aanbrengt in het detail venster op de tabbladen **instellingen**, **statisch resultaat** of **uitvoeren na** , moet u ervoor zorgen dat u **klaar bent** om deze wijzigingen door te voeren voordat u de tabbladen verwisselt of de focus naar de ontwerper wijzigt. Als dat niet het geval is, worden uw wijzigingen niet bewaard.

1. Sla uw werk op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Activeer vervolgens hand matig een uitvoering om uw werk stroom te testen.

## <a name="trigger-the-workflow"></a>De werk stroom activeren

In dit voor beeld wordt de werk stroom uitgevoerd wanneer de trigger van de aanvraag een inkomende aanvraag ontvangt, die wordt verzonden naar de URL voor het eind punt dat door de trigger wordt gemaakt. Wanneer u de werk stroom voor de eerste keer hebt opgeslagen, heeft de Logic Apps-service automatisch deze URL gegenereerd. Daarom moet u deze URL vinden voordat u deze aanvraag kunt verzenden om de werk stroom te activeren.

1. Selecteer in de werk stroom ontwerper de aanvraag trigger die wordt genoemd **Wanneer een HTTP-aanvraag wordt ontvangen**.

1. Nadat het detail venster is geopend, zoekt u op het tabblad **para meters** de eigenschap **http post-URL** . Als u de gegenereerde URL wilt kopiëren, selecteert u de **Kopieer-URL** (pictogram bestand kopiëren) en slaat u de URL nu ergens anders op. De URL heeft de volgende notatie:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Scherm opname van de ontwerper met de aanvraag trigger en eind punt-URL in de eigenschap HTTP POST-URL.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   Voor dit voor beeld ziet de URL er als volgt uit:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > U kunt ook de eind punt-URL vinden in het deel venster **overzicht** van de logische app in de eigenschap **werk stroom-URL** .
   >
   > 1. Selecteer **overzicht** in het menu resource.
   > 1. Zoek in het deel venster **overzicht** de eigenschap **werk stroom-URL** .
   > 1. Als u de eind punt-URL wilt kopiëren, plaatst u de muis aanwijzer boven het einde van de tekst van het eind punt-URL en selecteert **u kopiëren naar klem bord** (pictogram bestand kopiëren).

1. Als u de URL wilt testen door een aanvraag te verzenden, opent u [postman](https://www.postman.com/downloads/) of uw favoriete hulp programma voor het maken en verzenden van aanvragen.

   In dit voor beeld wordt het gebruik van Postman voortgezet. Zie voor meer informatie [verzen ding aan de slag](https://learning.postman.com/docs/getting-started/introduction/).

   1. Selecteer op de werk balk van Postman de optie **Nieuw**.

      ![Scherm afbeelding met de knop Nieuw is geselecteerd](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. Selecteer in het deel venster **Nieuw** , onder **bouw stenen**, **aanvraag**.

   1. Geef in het venster **aanvraag opslaan** onder **naam van aanvraag** een naam op voor de aanvraag, bijvoorbeeld `Test workflow trigger` .

   1. Onder **Selecteer een verzameling of map om op te slaan**, selecteert u **verzameling maken**.

   1. Geef onder **alle verzamelingen** een naam op voor de verzameling die u wilt maken voor het organiseren van uw aanvragen, druk op ENTER en selecteer **opslaan in <*verzameling-naam* >**. In dit voor beeld wordt `Logic Apps requests` de naam van de verzameling gebruikt.

      Het aanvraag venster van Postman wordt geopend, zodat u een aanvraag naar de eind punt-URL voor de aanvraag trigger kunt verzenden.

      ![Scherm opname van de weer gave met het geopende aanvraag venster](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. In het deel venster aanvraag in het adresvak dat naast de lijst met methoden wordt weer gegeven, die momenteel de methode **Get** als standaard aanvraag bevat, plakt u de URL die u eerder hebt gekopieerd en selecteert u **verzenden**.

      ![Scherm afbeelding waarin de URL voor de Postman en eind punt wordt weer gegeven in het adresvak waarin de knop verzenden is geselecteerd](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Wanneer de trigger wordt geactiveerd, wordt de voorbeeld werk stroom uitgevoerd en wordt er een e-mail bericht verzonden dat lijkt op dit voor beeld:

      ![Scherm opname van Outlook-e-mail, zoals beschreven in het voor beeld](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Uitvoeringsgeschiedenis controleren

Voor een stateful werk stroom kunt u na elke uitvoering van elke workflow de uitvoerings geschiedenis bekijken, met inbegrip van de status van de algemene uitvoering, voor de trigger en voor elke actie samen met de invoer en uitvoer. In de Azure Portal worden de uitvoerings geschiedenis en trigger GESCHIEDENISS weer gegeven op werk stroom niveau, niet op het niveau van de logische app. Zie [trigger geschiedenis controleren](#view-trigger-histories)als u de trigger geschiedenis wilt bekijken buiten de context van de uitvoerings historie.

1. Selecteer in het Azure Portal, in het menu van de werk stroom, de optie **monitor**.

   In het deel venster **monitor** wordt de uitvoerings geschiedenis voor de werk stroom weer gegeven.

   ![Scherm opname van het deel venster Monitor van de werk stroom en de uitvoerings geschiedenis.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Als de meest recente uitvoerings status niet wordt weer gegeven, selecteert u op de werk balk van het **monitor** paneel **vernieuwen**. Er vindt geen uitvoering plaats voor een trigger die wordt overgeslagen vanwege unmet criteria of het vinden van geen gegevens.

   | Uitvoerings status | Beschrijving |
   |------------|-------------|
   | **Aborted** | De uitvoering is gestopt of niet voltooid vanwege externe problemen, bijvoorbeeld een systeem storing of een vervallen Azure-abonnement. |
   | **Gevraagd** | De uitvoering is geactiveerd en gestart, maar er is een annulerings aanvraag ontvangen. |
   | **Mislukt** | Ten minste één bewerking in de uitvoering is mislukt. Er zijn geen verdere acties in de werk stroom ingesteld voor het afhandelen van de fout. |
   | **Wordt uitgevoerd** | De uitvoering is geactiveerd en wordt uitgevoerd, maar deze status kan ook worden weer gegeven voor een uitvoering die wordt beperkt als gevolg van [actie limieten](logic-apps-limits-and-config.md) of het [huidige prijs plan](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tip**: als u [logboek registratie van diagnostische gegevens](monitor-logic-apps-log-analytics.md)instelt, kunt u informatie ophalen over eventuele vertragings gebeurtenissen die plaatsvinden. |
   | **Geslaagd** | De uitvoering is voltooid. Als een actie is mislukt, wordt die fout door een volgende actie in de werk stroom verwerkt. |
   | **Time-out opgetreden** | Er is een time-out opgetreden tijdens het uitvoeren omdat de huidige duur de limiet voor de uitvoerings duur heeft overschreden, die wordt bepaald door de [ **Bewaar periode voor het bewaren van uitvoerings geschiedenis in dagen**](logic-apps-limits-and-config.md#run-duration-retention-limits). De duur van een uitvoering wordt berekend met behulp van de begin tijd van de uitvoering en de duur limiet voor uitvoering op die begin tijd. <p><p>**Opmerking**: als de duur van de uitvoering ook de huidige *Bewaar limiet voor de uitvoerings geschiedenis* overschrijdt, die ook wordt bepaald door de Bewaar periode voor het bewaren van de [ **uitvoerings geschiedenis in dagen**](logic-apps-limits-and-config.md#run-duration-retention-limits), wordt de uitvoering uit de geschiedenis van uitvoeringen door een dagelijkse opschoon taak gewist. Of de uitvoerings tijd wordt uitgecheckt of voltooid, de Bewaar periode wordt altijd berekend met behulp van de begin tijd en de *huidige* Bewaar limiet van de uitvoering. Als u de limiet voor de duur van een in-Flight-uitvoering verkort, wordt er dus een time-out uitgevoerd. De uitvoering blijft echter behouden of wordt gewist uit de geschiedenis van de uitvoering, op basis van het feit of de duur van de uitvoering de Bewaar limiet heeft overschreden. |
   | **Wachten** | De uitvoering is niet gestart of wordt onderbroken, bijvoorbeeld door een eerder werk stroom exemplaar dat nog steeds actief is. |
   |||

1. Als u de status van elke stap in een uitvoering wilt controleren, selecteert u de run die u wilt controleren.

   De detail weergave uitvoeren wordt geopend en toont de status voor elke stap in de uitvoering.

   ![Scherm opname van de weer gave uitvoerings Details met de status voor elke stap in de werk stroom.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   Hier volgen de mogelijke statussen die elke stap in de werk stroom kan hebben:

   | Actie status | Pictogram | Beschrijving |
   |---------------|------|-------------|
   | **Aborted** | ![Pictogram voor de actie status ' afgebroken '][aborted-icon] | De actie is gestopt of niet voltooid vanwege externe problemen, bijvoorbeeld een systeem storing of een vervallen Azure-abonnement. |
   | **Gevraagd** | ![Pictogram voor de actie status geannuleerd][cancelled-icon] | De actie is uitgevoerd, maar er is een annulerings aanvraag ontvangen. |
   | **Mislukt** | ![Pictogram voor de actie status ' mislukt '][failed-icon] | De actie is mislukt. |
   | **Wordt uitgevoerd** | ![Pictogram voor de actie status ' actief '][running-icon] | De actie wordt momenteel uitgevoerd. |
   | **Overgeslagen** | ![Pictogram voor de actie status "overgeslagen"][skipped-icon] | De actie is overgeslagen omdat de onmiddellijk voorafgaande actie is mislukt. Een actie heeft een `runAfter` voor waarde die vereist dat de voorafgaande actie is voltooid voordat de huidige actie kan worden uitgevoerd. |
   | **Geslaagd** | ![Pictogram voor de actie status geslaagd][succeeded-icon] | De actie is geslaagd. |
   | **Geslaagd met nieuwe pogingen** | ![Pictogram voor de actie status geslaagd met nieuwe pogingen][succeeded-with-retries-icon] | De actie is voltooid, maar alleen na een of meer pogingen. Als u de geschiedenis van de nieuwe poging wilt bekijken, selecteert u in de detail weergave uitvoerings geschiedenis die actie zodat u de invoer en uitvoer kunt bekijken. |
   | **Time-out opgetreden** | ![Pictogram voor de actie status time-out][timed-out-icon] | De actie is gestopt vanwege de time-outlimiet die is opgegeven door de instellingen van die actie. |
   | **Wachten** | ![Pictogram voor de actie status ' wachten '][waiting-icon] | Is van toepassing op een webhook-actie die wordt gewacht op een inkomende aanvraag van een aanroeper. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Als u de invoer en uitvoer voor een specifieke stap wilt bekijken, selecteert u die stap.

   ![Scherm opname van de invoer en uitvoer in de geselecteerde actie een e-mail verzenden.](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Selecteer onbewerkte **invoer weer geven** of **onbewerkte uitvoer weer geven** om de onbewerkte invoer en uitvoer voor die stap verder te bekijken.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Trigger GESCHIEDENISS controleren

Voor een stateful werk stroom kunt u de trigger geschiedenis voor elke uitvoering bekijken, met inbegrip van de trigger status en de invoer en uitvoer, los van de context van de [uitvoerings geschiedenis](#view-run-history). In de Azure Portal worden de trigger geschiedenis en de uitvoerings geschiedenis weer gegeven op werk stroom niveau, niet op het niveau van de logische app. Voer de volgende stappen uit om deze historische gegevens te vinden:

1. Selecteer in het Azure Portal, in het menu van uw werk stroom, onder **ontwikkelaar**, de optie **trigger GESCHIEDENISS**.

   In het deel venster **trigger geschiedenis** worden de trigger GESCHIEDENISS weer gegeven voor de uitvoeringen van uw werk stroom.

1. Als u een specifieke trigger geschiedenis wilt bekijken, selecteert u de ID voor die uitvoering.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Application Insights na implementatie inschakelen of openen

Tijdens de uitvoering van de werk stroom verzendt uw logische app telemetrie en andere gebeurtenissen. U kunt deze telemetrie gebruiken om beter inzicht te krijgen in hoe goed uw werk stroom wordt uitgevoerd en hoe de Logic Apps runtime op verschillende manieren werkt. U kunt uw werk stroom bewaken met behulp van [Application Insights](../azure-monitor/app/app-insights-overview.md), die bijna realtime-telemetrie (Live Metrics) biedt. Deze mogelijkheid kan u helpen fouten en prestatie problemen gemakkelijker te onderzoeken wanneer u deze gegevens gebruikt om problemen te onderzoeken, waarschuwingen in te stellen en grafieken te maken.

Als de instellingen voor het maken en implementeren van uw logische app gebruikmaken van [Application Insights](../azure-monitor/app/app-insights-overview.md), kunt u optioneel diagnostische logboek registratie en tracering inschakelen voor uw logische app. U kunt dit doen wanneer u uw logische app maakt in de Azure Portal of na de implementatie. U moet een Application Insights-exemplaar hebben, maar u kunt deze resource [vooraf](../azure-monitor/app/create-workspace-resource.md)maken wanneer u uw logische app maakt, of na de implementatie.

Ga als volgt te werk om Application Insights in te scha kelen op een geïmplementeerde logische app of het Application Insights dash board te openen als dit al is ingeschakeld:

1. Zoek in de Azure Portal uw geïmplementeerde logische app.

1. Selecteer **Application Insights** onder **instellingen** in het menu van de logische app.

1. Als Application Insights niet is ingeschakeld, selecteert u **Application Insights inschakelen** in het deel venster **Application Insights** . Nadat het deel venster is bijgewerkt, selecteert u onderaan **Toep assen**.

   Als Application Insights is ingeschakeld, selecteert u in het deel venster **Application Insights** **Application Insights gegevens weer geven**.

Nadat Application Insights is geopend, kunt u verschillende metrische gegevens voor uw logische app bekijken. Raadpleeg de volgende onderwerpen voor meer informatie:

* [Azure Logic Apps Anywhere-monitor uitvoeren met Application Insights-deel 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps Anywhere-monitor uitvoeren met Application Insights-deel 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Uitvoerings geschiedenis voor stateless werk stromen inschakelen

Als u een stateless werk stroom eenvoudiger wilt opsporen, kunt u de uitvoerings geschiedenis voor die werk stroom inschakelen en de uitvoerings geschiedenis uitschakelen wanneer u klaar bent. Ga als volgt te werk voor de Azure Portal, of als u werkt met Visual Studio code, Raadpleeg [stateful en stateless werk stromen maken in Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

1. Zoek en open de resource van de **logische app (preview)** In de [Azure Portal](https://portal.azure.com).

1. Selecteer in het menu van de logische app onder **instellingen** de optie **configuratie**.

1. Op het tabblad **Toepassings instellingen** selecteert u **nieuwe toepassings instelling**.

1. Voer in het deel venster **toepassings instelling toevoegen/bewerken** in het vak **naam** de naam in van de bewerkings optie: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Voer in het vak **waarde** de volgende waarde in: `WithStatelessRunHistory`

   Bijvoorbeeld:

   ![Scherm opname van de Azure Portal-en Logic app-resource (preview) met de ' configuratie ' > ' nieuwe toepassings instelling ' < ' deel venster toepassings instelling toevoegen/bewerken ' van het open en de werk stromen. {yourWorkflowName}. OperationOptions ' is ingesteld op ' WithStatelessRunHistory '.](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Selecteer **OK** om deze taak te volt ooien. Selecteer **Opslaan** op de werk balk van het **configuratie** deel venster.

1. Als u de uitvoerings geschiedenis wilt uitschakelen wanneer u klaar bent, stelt u de `Workflows.{yourWorkflowName}.OperationOptions` eigenschap in op `None` of verwijdert u de eigenschap en de waarde ervan.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Items uit de ontwerp functie verwijderen

Voer een van de volgende stappen uit om een item in uw werk stroom te verwijderen uit de ontwerp functie:

* Selecteer het item, open het snelmenu van het item (SHIFT + F10) en selecteer **verwijderen**. Selecteer **OK** om de opdracht te bevestigen.

* Selecteer het item en druk op de toets DELETE. Selecteer **OK** om de opdracht te bevestigen.

* Selecteer het item, zodat het detail venster voor dat item wordt geopend. Open in de rechter bovenhoek van het deel venster het menu met weglatings tekens (**...**) en selecteer **verwijderen**. Selecteer **OK** om de opdracht te bevestigen.

  ![Scherm afbeelding met een geselecteerd item in de ontwerp functie met het deel venster geopende Details plus de geselecteerde knop met weglatings tekens en de opdracht verwijderen.](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Als het menu met weglatings tekens niet wordt weer gegeven, vouwt u het browser venster breed genoeg zodat het detail venster de knop met weglatings tekens (**...**) in de rechter bovenhoek wordt weer gegeven.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Problemen en fouten oplossen

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Er ontbreken nieuwe triggers en acties in de ontwerp kiezer voor eerder gemaakte werk stromen

Azure Logic Apps Preview ondersteunt ingebouwde acties voor Azure function-bewerkingen, vloeistof bewerkingen en XML-bewerkingen, zoals **XML-validatie** en **XML-trans formatie**. Voor eerder gemaakte Logic apps kunnen deze acties echter niet worden weer gegeven in de ontwerp functie die u kunt selecteren als uw logische app gebruikmaakt van een verouderde versie van de uitbreidings bundel, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Ga als volgt te werk om dit probleem op te lossen om de verouderde versie te verwijderen, zodat de uitbreidings bundel automatisch kan worden bijgewerkt naar de nieuwste versie.

> [!NOTE]
> Deze specifieke oplossing is alleen van toepassing op de **logische app (preview)** -resources die u maakt met behulp van de Azure Portal, niet de Logic apps die u maakt en implementeert met behulp van Visual Studio code en de uitbrei ding Azure Logic apps (preview). Zie [ondersteunde triggers en er ontbreken acties in de ontwerp functie van Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. Stop uw logische app in het Azure Portal.

   1. Selecteer **Overzicht** in het menu van de logische app.

   1. Op de werk balk van het **overzichts** venster, selecteert u **stoppen**.

1. Selecteer in het menu van de logische app onder **ontwikkelingsprogram Ma's** **geavanceerde hulp** middelen.

1. Selecteer in het deel venster **Geavanceerde Hulpprogram Ma's** **Go**, waarmee de kudu-omgeving voor uw logische app wordt geopend.

1. Open in de kudu-werk balk het menu **fout opsporing console** en selecteer **cmd**. 

   Een console venster wordt geopend, zodat u naar de map bundel kunt bladeren met behulp van de opdracht prompt. U kunt ook bladeren door de mapstructuur die het console venster wordt weer gegeven.

1. Blader naar de volgende map die versie mappen bevat voor de bestaande bundel:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Verwijder de versie map voor de bestaande bundel. In het console venster kunt u deze opdracht uitvoeren, waarbij u vervangt door `{bundle-version}` de bestaande versie:

   `rm -rf {bundle-version}`

   Bijvoorbeeld: `rm -rf 1.1.3`

   > [!TIP]
   > Als er een fout optreedt zoals ' toestemming geweigerd ' of ' bestand in gebruik ', vernieuw dan de pagina in uw browser en probeer de vorige stappen opnieuw uit te voeren totdat de map wordt verwijderd.

1. Ga in het Azure Portal terug naar de pagina **overzicht** van uw logische app en selecteer **opnieuw starten**.

   De portal haalt automatisch de meest recente bundel op en gebruikt deze.

## <a name="next-steps"></a>Volgende stappen

We horen graag van u over uw ervaringen met deze open bare preview.

* Voor fouten of problemen [maakt u uw problemen in github](https://github.com/Azure/logicapps/issues).
* Voor vragen, aanvragen, opmerkingen en andere feedback [gebruikt u dit feedback formulier](https://aka.ms/lafeedback).
