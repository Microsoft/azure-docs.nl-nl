---
title: 'Zelf studie: Azure static-Web Apps publiceren met Azure DevOps'
description: Meer informatie over het gebruik van Azure DevOps voor het publiceren van statische Web Apps van Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 472cf7b69078b3247c393ff65139bc29e5683a32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639366"
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

1. Selecteer **importeren** om te beginnen met het importeren van een voorbeeld toepassing.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps opslag plaats":::

1. Voer in **kloon-URL** in `https://github.com/staticwebdev/vanilla-api.git` .

1. Selecteer **Importeren**.

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Navigeer naar [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken**.

1. Zoek **Static Web Apps**.

1. Selecteer **statische web apps (preview-versie)**.

1. Selecteer **Maken**.

1. Onder _Implementatie Details_ moet u **andere** selecteren. Hiermee kunt u de code in uw Azure DevOps-opslag plaats gebruiken.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Implementatie Details-Overig":::

1. Zodra de implementatie is voltooid, gaat u naar de nieuwe statische Web Apps resource.

1. Selecteer **implementatie token beheren**.

1. Kopieer het **implementatie token** en plak het in een tekst editor voor gebruik in een ander scherm.

    > [!NOTE]
    > Deze waarde wordt voor Taan gereserveerd omdat u meer waarden in de volgende stappen kopieert en plakt.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Implementatie token":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>De pijplijn taak maken in azure DevOps

1. Ga naar de Azure DevOps-opslag plaats die u eerder hebt gemaakt.

1. Selecteer **Build instellen**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Build-pipeline":::

1. Selecteer in het scherm *uw pijp lijn configureren* de optie **Start pijp lijn**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Pijp lijn configureren":::

1. Kopieer en plak de volgende YAML in de pijp lijn.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Als u de voor beeld-app niet gebruikt, worden de waarden voor en `app_location` `api_location` `output_location` gewijzigd zodat deze overeenkomen met de waarden in uw toepassing.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    De `azure_static_web_apps_api_token` waarde is zelf beheerd en is hand matig geconfigureerd.

1. Selecteer **variabelen**.

1. Maak een nieuwe variabele.

1. Geef een naam op voor de variabele **deployment_token** (die overeenkomt met de naam in de werk stroom).

1. Kopieer het implementatie token dat u eerder hebt geplakt in een tekst editor.

1. Plak het implementatie token in het vak _waarde_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabele token":::

1. Selecteer **geheim van deze waarde blijven**.

1. Selecteer **OK**.

1. Selecteer **Opslaan** om terug te keren naar de pijplijn YAML.

1. Selecteer **opslaan en uitvoeren** om het dialoog venster _opslaan en uitvoeren_ te openen.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pijplijn":::

1. Selecteer **opslaan en uitvoeren** om de pijp lijn uit te voeren.

1. Zodra de implementatie is voltooid, gaat u naar het **overzicht** van statische web apps van Azure met koppelingen naar de implementatie configuratie. U ziet dat de _bron_ koppeling nu verwijst naar de vertakking en locatie van de Azure DevOps-opslag plaats.

1. Selecteer de **URL** om de zojuist geïmplementeerde website weer te geven.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Implementatie locatie":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Statische Azure-Web Apps configureren](./configuration.md)
