---
title: Eerste serverloze app maken in Visual Studio
description: Een serverloze app bouwen, implementeren en beheren met behulp van Azure Logic Apps en Azure Functions in Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96749144"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Uw eerste serverloze app bouwen met behulp van Azure Logic Apps en Azure Functions in Visual Studio

U kunt Cloud-apps snel ontwikkelen en implementeren met behulp van de serverloze hulpprogram ma's en mogelijkheden in azure, zoals [Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Azure functions](../azure-functions/functions-overview.md). In dit artikel wordt beschreven hoe u een serverloze app bouwt die gebruikmaakt van een logische app die een Azure-functie aanroept in Visual Studio. Zie [Azure server zonder functies en Logic apps](../logic-apps/logic-apps-serverless-overview.md)voor meer informatie over serverloze oplossingen in Azure.

## <a name="prerequisites"></a>Vereisten

Als u een serverloze app wilt bouwen in Visual Studio, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De volgende hulpprogram ma's. Als u deze nog niet hebt, downloadt en installeert u deze.

  * [Visual Studio 2019, 2017 of 2015 (Community of andere editie)](https://aka.ms/download-visual-studio). 
  Deze snelstart maakt gebruik van Visual Studio Community 2017, dit is gratis.

    > [!IMPORTANT]
    > Als u Visual Studio 2019 of 2017 installeert, selecteer dan de workload **Azure development**.

  * [Microsoft Azure SDK voor .net (versie 2.9.1 of hoger)](https://azure.microsoft.com/downloads/). 
  Meer informatie over [Azure SDK voor .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps Hulpprogram Ma's voor de versie van Visual Studio die u wilt:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    U kunt hulpprogramma's van Azure Logic Apps ofwel rechtstreeks vanuit Visual Studio Marketplace downloaden en installeren, of leer [deze extensie te installeren vanuit Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Zorg ervoor dat u Visual Studio opnieuw opstart na de installatie.

  * [Azure functions core tools](https://www.npmjs.com/package/azure-functions-core-tools) voor functies voor lokaal fout opsporing.

* Toegang tot het web terwijl u de ontwerp functie voor logische apps gebruikt.

  De ontwerpfunctie moet over een internetverbinding beschikken om resources te maken in Azure en de eigenschappen en gegevens van connectoren in uw logische app te lezen. 
  Als u bijvoorbeeld de Dynamics CRM Online-connector gebruikt, controleert de ontwerpfunctie uw CRM-exemplaar voor de beschikbare standaardregels en aangepaste eigenschappen.

## <a name="create-a-resource-group-project"></a>Een resource groeps project maken

Om aan de slag te gaan, moet u een [Azure-resource groep-project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) voor uw serverloze app maken. In azure maakt u resources in een *resource groep*, een logische verzameling die u gebruikt voor het organiseren, beheren en implementeren van resources voor een hele app als één activum. Voor een serverloze app in azure bevat uw resource groep resources voor zowel Azure Logic Apps als Azure Functions. Meer informatie over [Azure-resourcegroepen en resources](../azure-resource-manager/management/overview.md).

1. Start Visual Studio en meld u aan met uw Azure-account.

1. In het menu **File** selecteert u **New** > **Project**.

   ![Nieuw project maken in Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Onder **Installed**, selecteert u **Visual C#** of **Visual Basic**. Selecteer vervolgens **Cloud**  >  **Azure-resource groep**.

   > [!NOTE]
   > Als de **Cloud** categorie of het project van de **Azure-resource groep** niet bestaat, moet u ervoor zorgen dat u de Azure SDK voor Visual Studio hebt geïnstalleerd.

   Als u Visual Studio 2019 gebruikt, volgt u deze stappen:

   1. Selecteer in het vak **een nieuw project maken** de project sjabloon **Azure-resource groep** voor Visual C# of Visual Basic en selecteer vervolgens **volgende**.

   1. Geef de naam en andere project gegevens op die u wilt gebruiken voor de Azure-resource groep. Selecteer **Maken** als u klaar bent.

1. Geef uw project een naam en een locatie en selecteer **OK**.

   Visual Studio vraagt u om een sjabloon te selecteren in de lijst met sjablonen. 
   In dit voor beeld wordt een Azure Quick Start-sjabloon gebruikt om een serverloze app te bouwen die een logische app bevat en een aanroep van een Azure-functie.

   > [!TIP]
   > In scenario's waarin u uw oplossing niet wilt implementeren in een Azure-resource groep, kunt u de sjabloon voor de lege **logische app** gebruiken, waarmee alleen een lege logische app wordt gemaakt.

1. Selecteer in de lijst **sjablonen uit deze locatie weer geven** de optie **Azure quick start (github.com/azure/Azure-QuickStart-templates)**.

1. Voer in het zoekvak ' Logic-app ' in als uw filter. Selecteer in de resultaten de sjabloon **101-Logic-app-en-Function-app** .

   ![Azure Quick Start-sjabloon selecteren](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Met Visual Studio maakt en opent u een oplossing voor het project van uw resource groep. 
   Met de Azure Quick Start-sjabloon die u hebt geselecteerd, wordt een implementatie sjabloon gemaakt met de naam azuredeploy.jsin het project van de resource groep. Deze implementatie sjabloon bevat de definitie voor een eenvoudige logische app die wordt geactiveerd door een HTTP-aanvraag, een Azure-functie aanroept en het resultaat als een HTTP-antwoord retourneert.

   ![Nieuwe serverloze oplossing](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Implementeer vervolgens uw oplossing naar Azure. U moet dit doen voordat u de implementatie sjabloon kunt openen en de resources voor uw app zonder server te controleren.

## <a name="deploy-your-solution"></a>Uw oplossing implementeren

Voordat u uw logische app kunt openen in de ontwerp functie voor logische apps in Visual Studio, moet u een Azure-resource groep hebben die al is geïmplementeerd in Azure. De ontwerper kan vervolgens verbindingen maken met bronnen en services in uw logische app. Voor deze taak voert u de volgende stappen uit om uw oplossing te implementeren vanuit Visual Studio naar de Azure Portal:

1. Selecteer in Solution Explorer, in het snelmenu van het resource project, de optie  >  **nieuwe** implementeren.

   ![Nieuwe implementatie voor resource groep maken](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Als deze nog niet zijn geselecteerd, selecteert u uw Azure-abonnement en de resource groep die u wilt implementeren. Selecteer vervolgens **implementeren**.

   ![Implementatie-instellingen](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Als het vak **para meters bewerken** wordt weer gegeven, geeft u de resource namen op die u wilt gebruiken voor uw logische app en uw Azure function-app tijdens de implementatie. vervolgens slaat u de instellingen op. Zorg ervoor dat u een wereld wijd unieke naam gebruikt voor uw functie-app.

   ![Namen opgeven voor uw logische app en functie-app](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Wanneer Visual Studio wordt gestart voor de implementatie naar de opgegeven resource groep, wordt de implementatie status van uw oplossing weer gegeven in het venster Visual Studio- **uitvoer** . 
   Nadat de implementatie is voltooid, is de logische app in het Azure Portal live.

## <a name="edit-your-logic-app-in-visual-studio"></a>Uw logische app in Visual Studio bewerken

Als u de logische app na de implementatie wilt bewerken, opent u de logische app met behulp van de ontwerp functie voor logische apps in Visual Studio.

1. Selecteer in Solution Explorer in het snelmenu van de azuredeploy.jsop bestand **openen met Logic app Designer**.

   ![azuredeploy.jsopenen in de ontwerp functie voor logische apps](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio hebt.

1. Nadat het dialoog venster **Eigenschappen van logische app** wordt weer gegeven, selecteert u bij **abonnement** het Azure-abonnement als dit nog niet is geselecteerd. Onder **resource groep** selecteert u de resource groep en de locatie waar u uw oplossing hebt geïmplementeerd en selecteert u **OK**.

   ![Eigenschappen van logische app](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Nadat de ontwerp functie voor logische apps is geopend, kunt u door gaan met het toevoegen van stappen of het wijzigen van de werk stroom en het opslaan van uw updates.

   ![Logische App geopend in de ontwerp functie voor logische apps](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Uw Azure Functions-project maken

Als u uw functions-project wilt maken en werken met Java script, Python, F #, Power shell, batch of bash, volgt u de stappen in [werken met Azure functions core tools](../azure-functions/functions-run-local.md). Als u uw Azure-functie wilt ontwikkelen met behulp van C# binnen uw oplossing, gebruikt u een C#-klassebibliotheek door de stappen in [een .net-klassebibliotheek publiceren als functie-app](https://azure.microsoft.com/blog/)te volgen.

## <a name="deploy-functions-from-visual-studio"></a>Functies implementeren vanuit Visual Studio

Uw implementatie sjabloon implementeert alle Azure-functies die u in uw oplossing hebt van de Git-opslag plaats die is opgegeven door de variabelen in de azuredeploy.jsin het bestand. Als u uw functions-project in uw oplossing maakt en inontwerpt, kunt u dat project controleren in Git-bron beheer (bijvoorbeeld GitHub of Azure DevOps) en vervolgens de `repo` variabele bijwerken, zodat de sjabloon uw Azure-functie implementeert.

## <a name="manage-logic-apps-and-view-run-history"></a>Logische apps beheren en de uitvoerings geschiedenis weer geven

Voor Logic apps die al zijn geïmplementeerd in azure, kunt u nog steeds de uitvoerings geschiedenis voor weer geven, beheren en uitschakelen in Visual Studio.

1. Open in het menu **weer gave** in Visual Studio **Cloud Explorer**.

1. Selecteer onder **alle abonnementen** het Azure-abonnement dat is gekoppeld aan de Logic apps die u wilt beheren en selecteer vervolgens **Toep assen**.

1. Selecteer onder **Logic apps** de logische app. Selecteer in het snelmenu van de app **openen met Logic app-editor**.

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio hebt.

U kunt nu de al gepubliceerde logische app downloaden naar het project van de resource groep. Hoewel u een logische app in de Azure Portal mogelijk hebt gestart, kunt u die app nog steeds importeren en beheren in Visual Studio. Zie [Logic apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Logische apps beheren met Visual Studio](manage-logic-apps-with-visual-studio.md)
