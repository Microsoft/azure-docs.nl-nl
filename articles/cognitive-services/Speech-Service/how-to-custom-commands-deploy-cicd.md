---
title: Continue implementatie met Azure DevOps (preview)
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u doorlopende implementatie kunt instellen voor uw aangepaste opdrachten voor toepassingen. U maakt de scripts ter ondersteuning van de continue implementatie werk stromen.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927591"
---
# <a name="continuous-deployment-with-azure-devops"></a>Continue implementatie met Azure DevOps

In dit artikel leert u hoe u doorlopende implementatie kunt instellen voor uw aangepaste opdrachten voor toepassingen. De scripts ter ondersteuning van de CI/CD-werk stroom worden aan u door gegeven.

## <a name="prerequisite"></a>Vereiste
> [!div class = "checklist"]
> * Een aangepaste opdrachten voor het ontwikkelen van toepassingen (DEV)
> * Een aangepaste opdrachten toepassing voor productie (PROD)
> * Registreren voor [Azure-pijp lijnen](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>Exporteren/importeren/publiceren

De scripts worden gehost op [Cognitive Services Voice Assistant-aangepaste opdrachten](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Kloon de scripts in de bash-map naar uw opslag plaats. Zorg ervoor dat u hetzelfde pad behoudt.

### <a name="set-up-a-pipeline"></a>Een pijp lijn instellen 

1. Ga naar **Azure DevOps-pijp lijnen** en klik op nieuwe pijp lijn
1. Selecteer in de sectie **Connect** de locatie van de opslag plaats waar deze scripts zich bevinden
1. Selecteer in de sectie **selecteren** uw opslag plaats
1. Selecteer in de sectie **configureren** de optie ' Start pijplijn '
1. Vervolgens krijgt u een editor met een YAML-bestand. Vervang de sectie ' stappen ' door dit script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Houd er rekening mee dat deze scripts ervan uitgaan dat u de regio gebruikt `westus2` , als dat niet het geval is, worden de argumenten van de taken dienovereenkomstig bijgewerkt

    > [!div class="mx-imgBorder"]
    > ![Scherm opname die de regio waarde in de argumenten markeert.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. Open de vervolg keuzelijst in de knop Opslaan en uitvoeren en klik op opslaan

### <a name="hook-up-the-pipeline-with-your-application"></a>De pijp lijn aan uw toepassing koppelen

1. Ga naar de hoofd pagina van de pijp lijn.
1. Selecteer in de vervolg keuzelijst bovenhoek de optie **pijp lijn bewerken**. Hiermee krijgt u een YAML-editor. 
1. Selecteer **variabelen** in de rechter bovenhoek naast de knop uitvoeren. Klik op **nieuwe variabele**.
1. Voeg deze variabelen toe:
    
    | Variabele | Beschrijving |
    | ------- | --------------- | ----------- |
    | SourceAppId | ID van de DEV-toepassing |
    | TargetAppId | ID van de PROD-toepassing |
    | SubscriptionKey | De abonnements sleutel die voor beide toepassingen wordt gebruikt |
    | Cultuur | Cultuur van de toepassingen (en-US) |

    > [!div class="mx-imgBorder"]
    > ![Nettolading activiteit verzenden](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Klik op uitvoeren en klik vervolgens in de taak wordt uitgevoerd. 

    U ziet een lijst met taken die worden uitgevoerd met de volgende items: "exporteren van bron-app", importeren naar doel-app "&" Train en doel-app publiceren "

## <a name="deploy-from-source-code"></a>Implementeren vanuit broncode

Als u de definitie van uw toepassing in een opslag plaats wilt blijven gebruiken, bieden we de scripts voor implementaties van de bron code. Omdat de scripts zich in bash bevinden, moet u, als u Windows gebruikt, het [Linux-subsysteem](/windows/wsl/install-win10)installeren.

De scripts worden gehost op [Cognitive Services Voice Assistant-aangepaste opdrachten](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Kloon de scripts in de bash-map naar uw opslag plaats. Zorg ervoor dat u hetzelfde pad behoudt.

### <a name="prepare-your-repository"></a>Uw opslagplaats voorbereiden

1. Maak een map voor uw toepassing. in ons voor beeld wordt er een gemaakt met de naam ' apps '.
1. Werk de argumenten van het onderstaande bash-script bij en voer deze uit. Hiermee wordt het dialoog model van uw toepassing geïmporteerd in het bestand myapp.jsop
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumenten | Beschrijving |
    | ------- | --------------- | ----------- |
    | regio | de regio van de toepassing, d.w.z. westus2. |
    | subscriptionkey | de abonnements sleutel van uw spraak bron. |
    | AppID | de toepassings-ID van de aangepaste opdrachten die u wilt exporteren. |

1. Push deze wijzigingen naar uw opslag plaats.

### <a name="set-up-a-pipeline"></a>Een pijp lijn instellen 

1. Ga naar **Azure DevOps-pijp lijnen** en klik op nieuwe pijp lijn
1. Selecteer in de sectie **Connect** de locatie van de opslag plaats waar deze scripts zich bevinden
1. Selecteer in de sectie **selecteren** uw opslag plaats
1. Selecteer in de sectie **configureren** de optie ' Start pijplijn '
1. Vervolgens krijgt u een editor met een YAML-bestand. Vervang de sectie ' stappen ' door dit script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > bij deze scripts wordt ervan uitgegaan dat u de regio westus2 gebruikt, als dat niet het geval is, moet u de argumenten van de taken dienovereenkomstig bijwerken

1. Open de vervolg keuzelijst in de knop Opslaan en uitvoeren en klik op opslaan

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>De pijp lijn aansluiten met uw doel toepassingen

1. Ga naar de hoofd pagina van de pijp lijn.
1. Selecteer in de vervolg keuzelijst bovenhoek de optie **pijp lijn bewerken**. Hiermee krijgt u een YAML-editor. 
1. Selecteer **variabelen** in de rechter bovenhoek naast de knop uitvoeren. Klik op **nieuwe variabele**.
1. Voeg deze variabelen toe:

    | Variabele | Beschrijving |
    | ------- | --------------- | ----------- |
    | TargetAppId | ID van de PROD-toepassing |
    | SubscriptionKey | De abonnements sleutel die voor beide toepassingen wordt gebruikt |
    | Cultuur | Cultuur van de toepassingen (en-US) |

1. Klik op uitvoeren en klik vervolgens in de taak wordt uitgevoerd.
    U ziet een lijst met taken die worden uitgevoerd met de volgende opdrachten: "app importeren" & "Train en Publish app"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bekijk de voor beelden op GitHub](https://aka.ms/speech/cc-samples)
