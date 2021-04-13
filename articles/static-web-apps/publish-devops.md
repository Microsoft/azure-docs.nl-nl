---
title: 'Zelfstudie: Azure Static Web Apps publiceren met Azure DevOps'
description: Meer informatie over het gebruik van Azure DevOps om uw Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: f82ae60ab7f57b20a727deefa6e286d698ee5b6c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365753"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Zelfstudie: Azure Static Web Apps publiceren met Azure DevOps

In dit artikel wordt beschreven hoe u implementeert [in Azure Static Web Apps](./overview.md) met behulp van [Azure DevOps.](https://dev.azure.com/)

In deze zelfstudie leert u het volgende:

- Een Azure Static Web Apps instellen
- Een Azure DevOps-pijplijn maken om een statische web-app te bouwen en te publiceren

## <a name="prerequisites"></a>Vereisten

- **Actief Azure-account:** Als u nog geen account hebt, kunt u [gratis een account maken.](https://azure.microsoft.com/free/)
- **Azure DevOps-project:** Als u nog geen project hebt, kunt u [gratis een project maken.](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)
- **Azure DevOps-pijplijn:** Zie Uw eerste pijplijn maken als u hulp nodig hebt om aan de slag [te gaan.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true)

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Een statische web-app maken in een Azure DevOps-opslagplaats

  > [!NOTE]
  > Als u een bestaande app in uw opslagplaats hebt, kunt u naar de volgende sectie gaan.

1. Navigeer naar uw Azure DevOps-opslagplaats.

1. Selecteer **Importeren om** te beginnen met het importeren van een voorbeeldtoepassing.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps-repo":::

1. Voer **in Kloon-URL** `https://github.com/staticwebdev/vanilla-api.git` in.

1. Selecteer **Importeren**.

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Navigeer naar [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken**.

1. Zoek **Static Web Apps**.

1. Selecteer **Static Web Apps (preview)**.

1. Selecteer **Maken**.

1. Zorg _ervoor dat u_ onder Implementatiedetails Overige **selecteert.** Hiermee kunt u de code in uw Azure DevOps-opslagplaats gebruiken.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Implementatiedetails - overige":::

1. Zodra de implementatie is geslaagd, gaat u naar de nieuwe Static Web Apps resource.

1. Selecteer **Implementatie-token beheren.**

1. Kopieer het **implementatie-token** en plak het in een teksteditor voor gebruik in een ander scherm.

    > [!NOTE]
    > Deze waarde wordt nu gereserveerd omdat u in de komende stappen meer waarden gaat kopiëren en plakken.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Implementatie-token":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>De pijplijntaak maken in Azure DevOps

1. Navigeer naar de Azure DevOps-opslagplaats die eerder is gemaakt.

1. Selecteer **Build instellen.**

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Build-pipeline":::

1. Selecteer in *het scherm Uw pijplijn configureren* de **optie Starter-pijplijn**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Pijplijn configureren":::

1. Kopieer en plak de volgende YAML in uw pijplijn.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Als u de voorbeeld-app niet gebruikt, moeten de waarden voor , en worden gewijzigd om overeen te komen `app_location` met de waarden in uw `api_location` `output_location` toepassing.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    De `azure_static_web_apps_api_token` waarde wordt zelf beheerd en handmatig geconfigureerd.

1. Selecteer **Variabelen**.

1. Maak een nieuwe variabele.

1. Noem de variabele **deployment_token** (die overeenkomt met de naam in de werkstroom).

1. Kopieer het implementatie-token dat u eerder in een teksteditor hebt geseed.

1. Plak het implementatie-token in het _vak_ Waarde.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabele token":::

1. Selecteer **Deze waarde geheim houden.**

1. Selecteer **OK**.

1. Selecteer **Opslaan om** terug te keren naar de YAML van uw pijplijn.

1. Selecteer **Opslaan en uitvoeren om** het dialoogvenster Opslaan en uitvoeren _te_ openen.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pijplijn":::

1. Selecteer **Opslaan en uitvoeren om** de pijplijn uit te voeren.

1. Zodra de implementatie is geslaagd, gaat u naar het Azure Static Web Apps **overzicht** dat koppelingen naar de implementatieconfiguratie bevat. De koppeling _Bron wijst_ nu naar de vertakking en locatie van de Azure DevOps-opslagplaats.

1. Selecteer de **URL om** uw zojuist geïmplementeerde website weer te geven.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Implementatielocatie":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Static Web Apps](./configuration.md)
