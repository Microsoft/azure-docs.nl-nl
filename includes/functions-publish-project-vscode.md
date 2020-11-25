---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a525d1e14e642a64235c263ba29bf7a181bf9e30
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008165"
---
## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en de bijbehorende resources in uw Azure-abonnement en implementeert u vervolgens uw code.

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven.


1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: In het gebied** Functies kiest u de knop **Implementeren in functie-app ...** .

    ![Uw project naar Azure publiceren](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    - **Selecteer map**: Kies een map in uw werkruimte of blader naar een map die de functie-app bevat. Dit wordt niet weergegeven als u al een geldige functie-app hebt geopend.

    - **Selecteer abonnement**: Kies het abonnement dat u wilt gebruiken. Dit ziet u niet als u maar één abonnement hebt.

    - **Selecteer functie-app in Azure**: Kies `- Create new Function App`. (Kies niet de optie `Advanced` die niet in dit artikel wordt behandeld.)
      
    - **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze uniek is in Azure Functions.
    
    - **Selecteer een locatie voor nieuwe resources**:  Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 
    
1.  Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt met behulp van namen op basis van de naam van uw functie-app:
    
    - Een resourcegroep, wat een logische container is voor gerelateerde resources.
    - Een standaard Azure Storage-account, waarin de status en andere gegevens van uw projecten worden onderhouden.
    - Een verbruiksplan dat de onderliggende host definieert voor uw serverloze functie-app. 
    - Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen binnen hetzelfde hostingabonnement.
    - Een Application Insights-exemplaar dat is verbonden met de functie-app, die het gebruik van uw serverloze functie bijhoudt.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 
    
1. Selecteer in deze melding de optie **Uitvoer weergeven** om de resultaten van het maken en implementeren te bekijken, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u het belpictogram in de rechterbenedenhoek om deze opnieuw weer te geven.

    ![Volledige melding maken](media/functions-publish-project-vscode/function-create-notifications.png)
