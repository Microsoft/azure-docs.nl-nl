---
title: 'Zelf studie: Azure static-Web Apps publiceren met Azure DevOps'
description: Meer informatie over het gebruik van Azure DevOps voor het publiceren van statische Web Apps van Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: af359734ff5bfe90dedbb7f8389aecdc6e056654
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543552"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Zelf studie: Azure static-Web Apps publiceren met Azure DevOps

In dit artikel wordt beschreven hoe u kunt implementeren in [statische web apps Azure](./overview.md) met behulp van [Azure DevOps](https://dev.azure.com/).

In deze zelfstudie leert u het volgende:

- Een Azure static Web Apps-site instellen
- Een Azure DevOps-pijp lijn maken om een statische web-app te bouwen en publiceren

## <a name="prerequisites"></a>Vereisten

- **Actief Azure-account:** Als u er nog geen hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/).
- **Azure DevOps-project:** Als u er nog geen hebt, kunt u [gratis een project maken](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Azure DevOps-pijp lijn:** Als u hulp nodig hebt om aan de slag te gaan, raadpleegt u [uw eerste pijp lijn maken](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Een statische web-app maken in een Azure DevOps-opslag plaats

  > [!NOTE]
  > Als u een bestaande app in uw opslag plaats hebt, kunt u door gaan naar de volgende sectie.

1. Navigeer naar uw Azure DevOps-opslag plaats.

1. Gebruik een bestaande opslag plaats of _Importeer een opslag plaats_ zoals hieronder wordt weer gegeven.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps opslag plaats":::

1. Maak een nieuw bestand voor uw front-end-web-app.

1. Kopieer en plak de volgende HTML-opmaak in het nieuwe bestand:

    ```html
    <!DOCTYPE html>
    <html lang="en">
  
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" href="styles.css">
      <title>Hello World!</title>
    </head>
  
    <body>
      <main>
        <h1>Hello World!</h1>
      </main>
    </body>
  
    </html>
    ```

1. Sla het bestand op.

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Navigeer naar [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken**.

1. Zoek **Static Web Apps**.

1. Selecteer **statische web apps (preview-versie)**.

1. Selecteer **Maken**.

1. Onder _Implementatie Details_ moet u **andere** selecteren. Hiermee kunt u de code in uw Azure DevOps-opslag plaats gebruiken.

    > [!NOTE]
    > De functionaliteit voor het selecteren van _andere_ wordt momenteel geïmplementeerd en is mogelijk nog niet beschikbaar in alle Azure-abonnementen.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Implementatie Details-Overig":::

1. Wanneer de implementatie is voltooid, selecteert u **implementatie token beheren**.

1. Kopieer het **implementatie token** en plak het in een tekst editor voor gebruik in een ander scherm.

    > [!NOTE]
    > Deze waarde wordt voor Taan gereserveerd omdat u meer waarden in de volgende stappen kopieert en plakt.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Implementatie token":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>De pijplijn taak maken in azure DevOps

1. Ga naar het Azure DevOps-project dat u eerder hebt gemaakt.

2. Maak een nieuwe **Build-pijp lijn** en selecteer **Build instellen**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Build-pipeline":::

3. Kopieer en plak de volgende YAML in de pijp lijn.

    > [!NOTE]
    > De waarden die zijn ingevoerd voor _app_location_,_api_location_ en _output_location_ moeten worden gewijzigd voor uw app.  

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: frontend 
          api_location: api
          output_location: build
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    Configureer de invoer van de statische Azure-web-app op basis van de mapstructuur van uw toepassing.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    De `azure_static_web_apps_api_token` waarde is zelf beheerd en is hand matig geconfigureerd.

4. Selecteer **variabelen**.

5. Maak een nieuwe variabele.

6. Geef een naam op voor de variabele **deployment_token** (die overeenkomt met de naam in de werk stroom).

7. Kopieer het implementatie token dat u eerder hebt geplakt in een tekst editor.

8. Plak het implementatie token in het vak _waarde_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabele token":::

9. Selecteer **OK**.

10. Selecteer **opslaan en voer** de pijp lijn uit.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pijplijn":::

11. Zodra de implementatie is voltooid, gaat u naar het **overzicht** van statische web apps van Azure met koppelingen naar de implementatie configuratie.

12. Selecteer de **URL** om de zojuist geïmplementeerde website weer te geven. U ziet dat de _bron_ koppeling nu verwijst naar de vertakking en locatie van de Azure DevOps-opslag plaats.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Implementatie locatie":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Statische Azure-Web Apps configureren](./configuration.md)
