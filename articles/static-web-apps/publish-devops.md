---
title: 'Zelf studie: Azure static-Web Apps publiceren met Azure DevOps'
description: Meer informatie over het gebruik van Azure DevOps voor het publiceren van statische Web Apps van Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 4745cf9d60b58e01beb29a640b1282265c23b13c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105051109"
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
   
2. Gebruik een bestaande opslag plaats of _Importeer een opslag plaats_ zoals hieronder wordt weer gegeven.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps opslag plaats":::

3. Maak een nieuw bestand voor uw front-end-web-app.
   
4. Kopieer en plak de volgende HTML-opmaak in het nieuwe bestand:

  ```HTML
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

5. Sla het bestand op.

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Navigeer naar [Azure Portal](https://portal.azure.com).
   
2. Selecteer **Een resource maken**.
   
3. Zoek **Static Web Apps**.
   
4. Selecteer **statische web apps (preview-versie)**.
   
5. Selecteer **Maken**.
   
6. Onder _Implementatie Details_ moet u **andere** selecteren. Hiermee kunt u de code in uw Azure DevOps-opslag plaats gebruiken.
  > [!NOTE]
  > De functionaliteit voor het selecteren van _andere_ wordt momenteel geïmplementeerd en is mogelijk nog niet beschikbaar in alle Azure-abonnementen.

  :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Implementatie Details-Overig":::

7. Wanneer de implementatie is voltooid, selecteert u **implementatie token beheren**.

8. Kopieer het **implementatie token** en plak het in een tekst editor voor gebruik in een ander scherm.

  > [!NOTE]
  > Deze waarde wordt voor Taan gereserveerd omdat u meer waarden in de volgende stappen kopieert en plakt.

  :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Implementatie token"::: 

## <a name="create-the-pipeline-task-in-azure-devops"></a>De pijplijn taak maken in azure DevOps

1. Ga naar het Azure DevOps-project dat u eerder hebt gemaakt.

2. Maak een nieuwe **Build-pijp lijn** en selecteer **Build instellen**.

  :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Build-pipeline"::: 

3. Kopieer en plak de volgende YAML in de pijp lijn.
> [!NOTE]
> De waarden die zijn ingevoerd voor _app_location_, _api_location_ en _output_location_ moeten worden gewijzigd voor uw app.  

```YAML
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
Configureer de invoer van de _statische Azure-web-app_ op basis van de mapstructuur van uw toepassing.

| Eigenschap | Beschrijving | Vereist |
|---|---|---|
| `app_location` | Locatie van de toepassings code.<br><br>Voer bijvoorbeeld `/` in als de bron code van uw toepassing zich in de hoofdmap van de opslag plaats bevindt, of `/app` als de code van uw toepassing zich in de map bevindt `app` . | Ja |
| `api_location` | De locatie van uw Azure Functions-code.<br><br>Voer bijvoorbeeld `/api` in als uw app-code zich in een map met de naam bevindt `api` . Als er geen Azure Functions-app wordt gedetecteerd in de map, mislukt de build, wordt ervan uitgegaan dat u geen API wilt. | Nee |
| `output_location` | Locatie van de map voor het build-uitvoer ten opzichte van de `app_location` .<br><br>Bijvoorbeeld, als de bron code van uw toepassing zich in bevindt `/app` en het build-script bestanden naar de `/app/build` map levert, vervolgens `build` als `output_location` waarde instellen. | Nee |

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
