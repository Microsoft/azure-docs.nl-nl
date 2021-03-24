---
title: Apps zoals verwacht met ARM implementeren
description: Meer informatie over het implementeren van meerdere Azure App Service-apps als één eenheid en op een voorspel bare manier met behulp van Azure resource management-sjablonen en Power shell-scripts.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: aec23c28e075dd38fa65f1315f9abd9e21cdc9cb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951467"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Micro services zoals verwacht inrichten en implementeren in azure
Deze zelf studie laat zien hoe u een toepassing kunt inrichten en implementeren die is samengesteld uit micro [Services](https://en.wikipedia.org/wiki/Microservices) in [Azure app service](https://azure.microsoft.com/services/app-service/) als één eenheid en op een voorspel bare manier met behulp van JSON-resource groeps sjablonen en Power shell-scripts. 

Bij het inrichten en implementeren van grootschalige toepassingen die bestaan uit zeer ontkoppelde micro Services, zijn Herhaal baarheid en voorspel baarheid cruciaal voor succes. Met [Azure app service](https://azure.microsoft.com/services/app-service/) kunt u micro Services maken die web-apps, mobiele back-ends en API apps bevatten. Met [Azure Resource Manager](../azure-resource-manager/management/overview.md) kunt u alle micro Services als eenheid beheren, samen met bron afhankelijkheden zoals data base-en broncode beheer instellingen. Nu kunt u een dergelijke toepassing ook implementeren met behulp van JSON-sjablonen en eenvoudige Power shell-scripts. 

## <a name="what-you-will-do"></a>Wat u moet doen
In de zelf studie gaat u een toepassing implementeren die het volgende bevat:

* Twee App Service-apps (bijvoorbeeld twee micro Services)
* Een back-end-SQL Database
* App-instellingen, verbindings reeksen en broncode beheer
* Application Insights, waarschuwingen, instellingen voor automatisch schalen

## <a name="tools-you-will-use"></a>Hulpprogram ma's die u gaat gebruiken
In deze zelf studie gebruikt u de volgende hulpprogram ma's. Omdat het geen uitgebreide discussie over hulpprogram ma's is, gaan we naar het end-to-end-scenario en geven we u een korte inleiding tot elke en waar u er meer informatie over kunt vinden. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager sjablonen (JSON)
Telkens wanneer u een app maakt in Azure App Service, gebruikt Azure Resource Manager bijvoorbeeld een JSON-sjabloon om de hele resource groep te maken met de onderdeel resources. Een complexe sjabloon van [Azure Marketplace](../marketplace/index.yml) kan de data base, opslag accounts, het app service plan, de app zelf, waarschuwings regels, app-instellingen, instellingen voor automatisch schalen en meer bevatten, en al deze sjablonen zijn beschikbaar voor u via Power shell. Zie [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md) voor meer informatie over de Azure Resource Manager sjablonen

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2,6 voor Visual Studio
De nieuwste SDK bevat verbeteringen in de ondersteuning van Resource Manager-sjablonen in de JSON-editor. U kunt deze gebruiken om snel een volledig nieuwe sjabloon voor een resource groep te maken of een bestaande JSON-sjabloon (zoals een gedownloade galerie sjabloon) te openen voor wijziging, het parameter bestand in te vullen en de resource groep zelfs rechtstreeks vanuit een Azure-resource groep-oplossing te implementeren.

Zie [Azure SDK 2,6 voor Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/)voor meer informatie.

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 of hoger
Vanaf versie 0.8.0 bevat de Azure PowerShell-installatie naast de Azure-module ook de module Azure Resource Manager. Met deze nieuwe module kunt u een script maken voor de implementatie van resource groepen.

Zie [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) voor meer informatie.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Met dit [Preview-programma](https://resources.azure.com) kunt u de JSON-definities van alle resource groepen in uw abonnement en de afzonderlijke resources verkennen. In het hulp programma kunt u de JSON-definities van een resource bewerken, een volledige hiërarchie van resources verwijderen en nieuwe resources maken.  De informatie die beschikbaar is in dit hulp programma is zeer nuttig voor het ontwerpen van sjablonen, omdat u kunt zien welke eigenschappen u moet instellen voor een bepaald type resource, de juiste waarden, enzovoort. U kunt zelfs uw resource groep in [Azure Portal](https://portal.azure.com/)maken en vervolgens de bijbehorende JSON-definities in het Explorer-hulp programma inspecteren om u te helpen bij het Templatize van de resource groep.

### <a name="deploy-to-azure-button"></a>De knop Implementeren in Azure
Als u GitHub gebruikt voor broncode beheer, kunt u een [knop voor implementeren naar Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) in uw Leesmij-bestand plaatsen. MD, waarmee de gebruikers interface voor het maken van een inschakel functie kan worden geïmplementeerd in Azure. Hoewel u dit kunt doen voor elke eenvoudige app, kunt u deze uitbreiden om het implementeren van een hele resource groep mogelijk te maken door een azuredeploy.jsin te voegen in de hoofdmap van de opslag plaats. Dit JSON-bestand, dat de sjabloon resource groep bevat, wordt gebruikt door de knop implementeren naar Azure om de resource groep te maken. Zie voor een voor beeld het [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -voor beeld, dat u in deze zelf studie gaat gebruiken.

## <a name="get-the-sample-resource-group-template"></a>De voorbeeld sjabloon voor de resource groep ophalen
Nu gaan we hier meteen aan de slag.

1. Navigeer naar het [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) app service-voor beeld.
2. Klik in readme.md op **implementeren naar Azure**.
3. U wordt naar de site [Deploy-to-Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-appservice-samples%2FToDoApp%2Fmaster%2Fazuredeploy.json) geleid en u hebt gevraagd om implementatie parameters in te voeren. U ziet dat de meeste velden zijn gevuld met de naam van de opslag plaats en enkele wille keurige teken reeksen. U kunt alle velden wijzigen als u wilt, maar de enige dingen die u moet invoeren zijn de SQL Server beheerders aanmelding en het wacht woord. Klik vervolgens op **volgende**.
   
   ![Geeft de para meters voor invoer implementatie op de site Deploy-to-Azure.](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klik vervolgens op **implementeren** om het implementatie proces te starten. Zodra het proces is uitgevoerd om te worden voltooid, klikt u op de http://todoapp koppeling *XXXX*. azurewebsites.net om door de geïmplementeerde toepassing te bladeren. 
   
   ![Toont het implementatie proces van uw toepassing.](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   De gebruikers interface zou een beetje langzaam zijn wanneer u er voor het eerst naartoe bladert omdat de apps net worden gestart, maar u er wel van overtuigen dat het een volledig functionele toepassing is.
5. Klik op de pagina implementeren op de koppeling **beheren** om de nieuwe toepassing in azure portal te bekijken.
6. Klik in de vervolg keuzelijst **Essentials** op de koppeling resource groep. Houd er rekening mee dat de app al is verbonden met de GitHub-opslag plaats onder **extern project**. 
   
   ![Hiermee wordt de koppeling van de resource groep weer gegeven in de vervolg keuzelijst Essentials.](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Houd er rekening mee dat er in de Blade van de resource groep al twee apps zijn en een SQL Database in de resource groep.
   
   ![Hiermee worden de beschik bare resources in de resource groep weer gegeven.](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Alles wat u zojuist hebt gezien in een paar korte minuten is een volledig geïmplementeerde toepassing met twee toepassingen, met alle onderdelen, afhankelijkheden, instellingen, data base en doorlopende publicatie, ingesteld met een automatische indeling in Azure Resource Manager. Dit alles is op twee dingen gedaan:

* De knop implementeren naar Azure
* azuredeploy.jsin de hoofdmap opslag plaats

U kunt dezelfde toepassing tien tallen, honderden of duizenden keren implementeren en elke keer exact dezelfde configuratie hebben. De Herhaal baarheid en voorspel baarheid van deze aanpak kunt u grootschalige toepassingen implementeren met gemak en vertrouwen.

## <a name="examine-or-edit-azuredeployjson"></a>AZUREDEPLOY.JSbekijken (of bewerken) op
Laten we nu eens kijken hoe de GitHub-opslag plaats is ingesteld. U gebruikt de JSON-editor in de Azure .NET SDK, dus als u [Azure .net sdk 2,6](https://azure.microsoft.com/downloads/)nog niet hebt geïnstalleerd, doet u dat nu.

1. Kloon de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -opslag plaats met uw favoriete Git-hulp programma. In de onderstaande scherm afbeelding ziet u dit in de team Verkenner van Visual Studio 2013.
   
   ![Laat zien hoe u een Git-hulp programma gebruikt om de ToDoApp-opslag plaats te klonen.](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Open in de hoofdmap van de opslag plaats azuredeploy.jsin Visual Studio. Als u het deel venster JSON-overzicht niet ziet, moet u de Azure .NET SDK installeren.
   
   ![Hiermee wordt het deel venster JSON-overzicht weer gegeven in Visual Studio.](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Ik wil niet alle details van de JSON-indeling beschrijven, maar de sectie [meer resources](#resources) bevat koppelingen voor het leren van de taal van de sjabloon voor de resource groep. Hier ziet u nu de interessante functies waarmee u aan de slag kunt met het maken van uw eigen aangepaste sjabloon voor app-implementatie.

### <a name="parameters"></a>Parameters
Bekijk het gedeelte para meters om te zien dat de meeste van deze para meters de knop **implementeren naar Azure** u vraagt om in te voeren. De site achter de knop **implementeren naar Azure** vult de gebruikers interface in met behulp van de para meters die zijn gedefinieerd in azuredeploy.jsop. Deze para meters worden gebruikt in de resource definities, zoals resource namen, eigenschaps waarden enzovoort.

### <a name="resources"></a>Resources
In het knoop punt resources ziet u dat er vier resources op het hoogste niveau zijn gedefinieerd, met inbegrip van een SQL Server exemplaar, een App Service plan en twee apps. 

#### <a name="app-service-plan"></a>App Service-plan
Laten we beginnen met een eenvoudige resource op hoofd niveau in de JSON. Klik in de JSON-overzicht op het App Service plan met de naam **[hostingPlanName]** om de bijbehorende JSON-code te markeren. 

![Toont de sectie [hostingPlanName] van de JSON-code.](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Houd er rekening mee dat met het `type` element de teken reeks voor een app service plan wordt opgegeven (dit is een lange, lange tijd geleden een server farm genoemd) en andere elementen en eigenschappen worden gevuld met de para meters die zijn gedefinieerd in het JSON-bestand en deze resource heeft geen geneste resources.

> [!NOTE]
> Houd er rekening mee dat de waarde van `apiVersion` Azure aangeeft welke versie van de rest API de JSON-resource definitie moet gebruiken. Dit kan van invloed zijn op hoe de resource moet worden geformatteerd in de `{}` . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Klik vervolgens op de SQL Server Resource met de naam **sqlserver** in de JSON-overzicht.

![Toont de SQL Server Resource met de naam SQLServer in de JSON-overzicht.](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Let op het volgende over de gemarkeerde JSON-code:

* Het gebruik van para meters zorgt ervoor dat de gemaakte bronnen worden benoemd en geconfigureerd op een manier waardoor ze consistent zijn met elkaar.
* De SQLServer-resource heeft twee geneste resources, waarbij elk een andere waarde is voor `type` .
* De geneste resources in `“resources”: […]` , waarbij de data base en de firewall regels zijn gedefinieerd, hebben een `dependsOn` -element dat de resource-id van de sqlserver-resource op hoofd niveau specificeert. Dit geeft aan Azure Resource Manager, ' voordat u deze resource maakt, moet deze andere resource al bestaan. en als de andere bron in de sjabloon is gedefinieerd, moet u die eerst een maken.
  
  > [!NOTE]
  > `resourceId()`Zie [Azure Resource Manager-sjabloon functies](../azure-resource-manager/templates/template-functions-resource.md#resourceid)voor meer informatie over het gebruik van de functie.
  > 
  > 
* Het effect van het `dependsOn` element is dat Azure Resource Manager kan weten welke resources parallel kunnen worden gemaakt en welke resources opeenvolgend moeten worden gemaakt. 

#### <a name="app-service-app"></a>App Service-app
Nu gaan we door naar de daad werkelijke apps zelf, wat gecompliceerder is. Klik op de App [Varia BLES (' apiSiteName ')] in het JSON-overzicht om de JSON-code te markeren. U zult merken dat u veel meer interessante dingen krijgt. Daarom bespreken we de functies één voor één:

##### <a name="root-resource"></a>Hoofd resource
De app is afhankelijk van twee verschillende bronnen. Dit betekent dat Azure Resource Manager de app alleen maakt nadat zowel het App Service plan als het SQL Server-exemplaar is gemaakt.

![Hier worden de app-afhankelijkheden weer gegeven voor het App Service plan en het SQL Server-exemplaar.](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>App-instellingen
De app-instellingen worden ook gedefinieerd als een geneste resource.

![Hier worden de app-instellingen weer gegeven die zijn gedefinieerd als een geneste resource in de JSON-code.](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

In het `properties` element voor `config/appsettings` hebt u twee app-instellingen in de indeling `"<name>" : "<value>"` .

* `PROJECT` is een [KUDU-instelling](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) die de Azure-implementatie vertelt die in een Visual Studio-oplossing met meerdere projecten moet worden gebruikt. Ik laat u later weten hoe broncode beheer is geconfigureerd, maar omdat de ToDoApp-code in een multi-project Visual Studio-oplossing is, is deze instelling vereist.
* `clientUrl` is een app-instelling die wordt gebruikt door de toepassings code.

##### <a name="connection-strings"></a>Verbindingsreeksen
De verbindings reeksen worden ook gedefinieerd als een geneste resource.

![Laat zien hoe de verbindings reeksen worden gedefinieerd als een geneste resource in de JSON-code.](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

In het `properties` element voor `config/connectionstrings` wordt elke Connection String ook gedefinieerd als een naam: waardepaar, met de specifieke indeling van `"<name>" : {"value": "…", "type": "…"}` . Voor het `type` element zijn mogelijke waarden `MySql` ,, `SQLServer` `SQLAzure` en `Custom` .

> [!TIP]
> Voor een definitieve lijst van de connection string typen voert u de volgende opdracht uit in Azure PowerShell: \[ Enum]:: GetNames ("micro soft. WindowsAzure. commands. Utilities. websites. Services. webentities. DatabaseType")
> 
> 

##### <a name="source-control"></a>Broncodebeheer
De instellingen voor broncode beheer worden ook gedefinieerd als een geneste resource. Azure Resource Manager maakt gebruik van deze resource om doorlopende publicatie te configureren (Zie aanvullende informatie over de `IsManualIntegration` toekomst) en om de implementatie van toepassings code automatisch uit te voeren tijdens de verwerking van het JSON-bestand.

![Laat zien hoe de instellingen voor broncode beheer worden gedefinieerd als een geneste resource in de JSON-code.](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` en `branch` moet goed intuïtief zijn en moet verwijzen naar de Git-opslag plaats en de naam van de vertakking waaruit u wilt publiceren. Deze worden vervolgens gedefinieerd door invoer parameters. 

Houd er rekening mee `dependsOn` dat, naast de app-resource zelf, `sourcecontrols/web` ook afhankelijk is van `config/appsettings` en `config/connectionstrings` . Dit komt doordat `sourcecontrols/web` het Azure-implementatie proces automatisch probeert om de toepassings code te implementeren, te bouwen en te starten, omdat er eenmaal is geconfigureerd. Door deze afhankelijkheid in te voegen, kunt u er daarom voor zorgen dat de toepassing toegang heeft tot de vereiste app-instellingen en verbindings reeksen voordat de toepassings code wordt uitgevoerd. 

> [!NOTE]
> Houd er rekening mee dat `IsManualIntegration` is ingesteld op `true` . Deze eigenschap is nodig in deze zelf studie omdat u geen eigenaar bent van de GitHub-opslag plaats en daarom geen machtiging voor Azure kunt verlenen voor het configureren van continue publicatie vanuit [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (d.w.z. push-updates voor automatische opslag plaatsen naar Azure). U kunt de standaard waarde `false` voor de opgegeven opslag plaats alleen gebruiken als u de GitHub-referenties van de eigenaar eerder hebt geconfigureerd in de [Azure Portal](https://portal.azure.com/) . Met andere woorden, als u broncode beheer hebt ingesteld op GitHub of BitBucket voor elke app in de [Azure-Portal](https://portal.azure.com/) , met behulp van uw gebruikers referenties, onthouden Azure de referenties en gebruiken ze telkens wanneer u in de toekomst een app uit github of BitBucket implementeert. Als u dit echter nog niet hebt gedaan, mislukt de implementatie van de JSON-sjabloon wanneer Azure Resource Manager probeert de instellingen voor broncode beheer van de app te configureren, omdat deze niet kan worden aangemeld bij GitHub of BitBucket met de referenties van de opslagplaats eigenaar.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>De JSON-sjabloon met geïmplementeerde resource groep vergelijken
Hier kunt u alle Blades van de app door lopen in de [Azure-Portal](https://portal.azure.com/), maar er is nog een hulp programma dat net zo handig is, indien niet meer. Ga naar het [Azure resource Explorer](https://resources.azure.com) preview-hulp programma, waarmee u een JSON-weer gave van alle resource groepen in uw abonnementen krijgt, zoals ze daad werkelijk bestaan in de Azure-back-end. U kunt ook zien hoe de JSON-hiërarchie van de resource groep in azure overeenkomt met de hiërarchie in het sjabloon bestand dat wordt gebruikt om het te maken.

Als ik bijvoorbeeld naar het [Azure resource Explorer](https://resources.azure.com) -hulp programma ga, de knoop punten in de Explorer uitvouwen, zie ik de resource groep en de resources op het hoofd niveau die zijn verzameld onder hun respectieve resource typen.

![Bekijk de resource groep en de resources op het hoofd niveau in het uitgevouwen hulp programma Azure resource Explorer.](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Als u inzoomt op een app, kunt u de details van de app-configuratie bekijken, vergelijkbaar met de onderstaande scherm afbeelding:

![Inzoomen om de configuratie gegevens in de app weer te geven.](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Opnieuw, de geneste resources moeten een hiërarchie hebben die lijkt op die in het JSON-sjabloon bestand en u ziet dat de app-instellingen, verbindings reeksen, enzovoort, goed worden weer gegeven in het JSON-deel venster. Het ontbreken van instellingen kan duiden op een probleem met uw JSON-bestand en helpt u bij het oplossen van problemen met het JSON-sjabloon bestand.

## <a name="deploy-the-resource-group-template-yourself"></a>Zelf de sjabloon voor de resource groep implementeren
De knop **implementeren in azure** is geweldig, maar u kunt de sjabloon voor de resource groep alleen in azuredeploy.jsalleen implementeren als u al hebt gepusht azuredeploy.jsop github. De Azure .NET SDK biedt ook de hulp middelen waarmee u elk JSON-sjabloon bestand rechtstreeks vanaf uw lokale computer kunt implementeren. Volg hiervoor de volgende stappen:

1. Klik in Visual Studio op **File** > **New** > **Project**.
2. Klik op **Visual C#**  >  **Cloud**  >  **Azure resource groep** en klik vervolgens op **OK**.
   
   ![Maak een nieuw project als een Azure-resource groep in de Azure .NET SDK.](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Selecteer in **Azure-sjabloon selecteren** de optie **lege sjabloon** en klik op **OK**.
4. Sleep azuredeploy.jsnaar de map met **sjablonen** van het nieuwe project.
   
   ![Hier ziet u het resultaat van het slepen van de azuredeploy.jsnaar een bestand in de map Temp late van het project.](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Open in Solution Explorer de gekopieerde azuredeploy.jsop.
6. We voegen net zo eenvoudig een aantal standaard bronnen voor toepassings inzichten toe aan het JSON-bestand door te klikken op **resource toevoegen**. Als u alleen geïnteresseerd bent in het implementeren van het JSON-bestand, gaat u verder met de stappen voor het implementeren.
   
   ![Hiermee wordt de knop resource toevoegen weer gegeven die u kunt gebruiken om standaard Application Insight-resources toe te voegen aan uw JSON-bestand.](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecteer **Application Insights voor web apps** en controleer of een bestaand app service plan en een bestaande app is geselecteerd en klik vervolgens op **toevoegen**.
   
   ![Toont de selectie van Application Insights voor Web Apps, naam, App Service plan en web-app.](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   U kunt nu verschillende nieuwe resources zien die, afhankelijk van de resource en wat er gebeurt, afhankelijkheden hebben voor het App Service plan of de app. Deze resources worden niet ingeschakeld door de bestaande definitie en u gaat deze wijzigen.
   
   ![Bekijk de nieuwe resources met afhankelijkheden voor het App Service plan of de app.](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Klik in het JSON-overzicht op **AppInsights automatisch schalen** om de bijbehorende JSON-code te markeren. Dit is de schaal instelling voor uw App Service-abonnement.
9. Zoek in de gemarkeerde JSON-code de `location` Eigenschappen en en `enabled` Stel deze in zoals hieronder wordt weer gegeven.
   
   ![Hier worden de locatie en ingeschakelde eigenschappen weer gegeven in de JSON-code appInsights automatisch schalen en de waarden die u moet instellen.](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Klik in het JSON-overzicht op **CPUHigh appInsights** om de bijbehorende JSON-code te markeren. Dit is een waarschuwing.
11. Zoek de `location` `isEnabled` Eigenschappen en stel deze in zoals hieronder wordt weer gegeven. Doe hetzelfde voor de andere drie waarschuwingen (paars bollen).
    
    ![Hier worden de locatie-en isEnabled-eigenschappen weer gegeven in de appInsights JSON-code CPUHigh en de waarden die u moet instellen.](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. U bent nu klaar om te implementeren. Klik met de rechter muisknop op het project en selecteer  >  **nieuwe implementatie** implementeren.
    
    ![Laat zien hoe u uw nieuwe project implementeert.](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Meld u aan bij uw Azure-account als u dit nog niet hebt gedaan.
14. Selecteer een bestaande resource groep in uw abonnement of maak een nieuwe, selecteer **azuredeploy.jsaan** en klik vervolgens op **para meters bewerken**.
    
    ![Laat zien hoe u de para meters in de azuredeploy.jsin het bestand kunt bewerken.](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    U kunt nu alle gedefinieerde para meters in het sjabloon bestand bewerken in een tabel leuk. Para meters die standaard waarden definiëren, hebben al hun standaard waarde, en para meters die een lijst met toegestane waarden definiëren, worden weer gegeven als vervolg keuzelijsten.
    
    ![Geeft para meters weer die een lijst met toegestane waarden definiëren als vervolg keuzelijst.](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Vul alle lege para meters in en gebruik het [github opslag plaats-adres voor ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) in het veld **disgegoten**. Klik vervolgens op **Opslaan**.
    
    ![Toont de zojuist ingevulde para meters voor de azuredeploy.jsvoor het bestand.](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatisch schalen is een functie die wordt aangeboden in een **Standard** -laag of hoger, en waarschuwingen op plan niveau zijn functies die worden aangeboden in de laag **basis** of hoger. u moet de **SKU** -para meter instellen op **Standard** of **Premium** om alle nieuwe app Insights-resources licht omhoog te bekijken.
    > 
    > 
16. Klik op **Implementeren**. Als u **wacht woorden opslaan** hebt geselecteerd, wordt het wacht woord opgeslagen in het parameter bestand **in tekst zonder opmaak**. Anders wordt u gevraagd het wacht woord voor de data base in te voeren tijdens het implementatie proces.

Dat is alles. Nu hoeft u alleen maar naar [Azure Portal](https://portal.azure.com/) en het [Azure resource Explorer](https://resources.azure.com) -hulp programma te gaan om de nieuwe instellingen voor waarschuwingen en automatisch schalen toe te voegen aan uw JSON geïmplementeerde toepassing.

De stappen in deze sectie hebben voornamelijk het volgende gerealiseerd:

1. Het sjabloon bestand is voor bereid
2. Er is een parameter bestand gemaakt om te gaan met het sjabloon bestand
3. Het sjabloon bestand met het parameter bestand geïmplementeerd

De laatste stap is eenvoudig gedaan door een Power shell-cmdlet. Open Scripts\Deploy-AzureResourceGroup.ps1 om te zien wat Visual Studio had toen u uw toepassing implementeerde. Er is een groot aantal code, maar ik Markeer alleen de relevante code die u nodig hebt om het sjabloon bestand te implementeren met het parameter bestand.

![Toont de relevante code in het script dat u nodig hebt om het sjabloon bestand met het parameter bestand te implementeren.](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

De laatste cmdlet, `New-AzureResourceGroup` , is het account waarmee de actie daad werkelijk wordt uitgevoerd. Hier ziet u dat u met de hulp van hulp middelen relatief eenvoudig de zoals verwacht van uw Cloud toepassing kunt implementeren. Telkens wanneer u de cmdlet uitvoert op dezelfde sjabloon met hetzelfde parameter bestand, krijgt u hetzelfde resultaat.

## <a name="summary"></a>Samenvatting
DevOps, REPEAT baarheid en voorspel baarheid zijn sleutels voor een succes volle implementatie van een grootschalige toepassing die bestaat uit micro Services. In deze zelf studie hebt u een twee-micro service-toepassing geïmplementeerd in azure als één resource groep met behulp van de Azure Resource Manager sjabloon. Hopelijk heeft de IT u de kennis die u nodig hebt om te beginnen met het converteren van uw toepassing in azure naar een sjabloon en kunt u deze zoals verwacht inrichten en implementeren. 

<a name="resources"></a>

## <a name="more-resources"></a>Meer bronnen
* [Taal van Azure Resource Manager sjabloon](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager-sjabloon functies](../azure-resource-manager/templates/template-functions.md)
* [Een toepassing implementeren met Azure Resource Manager sjabloon](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)
* [Problemen met de implementatie van resource groepen in azure oplossen](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de JSON-syntaxis en eigenschappen voor de resource typen die in dit artikel zijn geïmplementeerd:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Micro soft. web/server farms](/azure/templates/microsoft.web/serverfarms)
* [Micro soft. web/sites](/azure/templates/microsoft.web/sites)
* [Micro soft. web/sites/sleuven](/azure/templates/microsoft.web/sites/slots)
* [Micro soft. Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)