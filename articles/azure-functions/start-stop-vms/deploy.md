---
title: Vm's voor starten/stoppen v2 (preview-versie) implementeren
description: In dit artikel leest u hoe u de functie Vm's van het soort starten/stoppen (preview) voor uw Azure-Vm's in uw Azure-abonnement implementeert.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111729"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Vm's voor starten/stoppen v2 (preview-versie) implementeren

Voer de stappen in dit onderwerp uit om de functie Vm's starten/stoppen v2 (preview) te installeren. Nadat het installatie proces is voltooid, configureert u de planningen om deze aan uw vereisten aan te passen.

## <a name="deploy-feature"></a>Functie implementeren

De implementatie wordt [hier](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)gestart vanaf de GitHub-organisatie voor het starten/stoppen van vm's v2. Hoewel deze functie is bedoeld voor het beheren van al uw virtuele machines in uw abonnement op alle resource groepen van één implementatie binnen het abonnement, kunt u een ander exemplaar hiervan installeren op basis van het Operations-model of de vereisten van uw organisatie. Het kan ook worden geconfigureerd om Vm's centraal te beheren in meerdere abonnementen.

Om het beheer te vereenvoudigen en te verwijderen, raden we u aan om virtuele machines van het VM-netwerk (preview) te implementeren en te stoppen met een speciale resource groep.

> [!NOTE]
> Momenteel biedt deze preview geen ondersteuning voor het opgeven van een bestaand opslag account of Application Insights bron.

1. Open uw browser en navigeer naar de [github-organisatie](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)voor het starten/stoppen van vm's.
1. Selecteer de implementatie optie op basis van de Azure-cloud omgeving waarin uw Azure-Vm's zijn gemaakt. Hiermee opent u de pagina aangepaste implementatie van Azure Resource Manager in het Azure Portal.
1. Meld u aan bij de [Azure Portal](https://portal.azure.com)als u hierom wordt gevraagd.
1. Voer de volgende waarden in:

    |Naam |Waarde |
    |-----|------|
    |Region |Selecteer een regio bij u in de buurt voor nieuwe resources.|
    |Naam resourcegroep |Geef de naam van de resource groep op die de afzonderlijke resources bevat voor het starten/stoppen van Vm's. |
    |Resource groeps regio |Geef de regio voor de resource groep op. Bijvoorbeeld **VS - centraal**.|
    |Naam van Azure-functie-app |Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze uniek is in Azure Functions. |
    |Application Insights naam |Geef de naam op van uw Application Insights-exemplaar dat de analyse voor start/stop-Vm's zal bevatten. |
    |Application Insights regio |Geef de regio voor het Application Insights exemplaar op.|
    |Naam van opslagaccount |Geef de naam op van het Azure Storage account voor het opslaan van de telemetrie voor het starten/stoppen van Vm's. |
    |E-mailadres |Geef een of meer e-mail adressen op voor het ontvangen van status meldingen, gescheiden door een komma (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Implementatie configuratie van Vm's sjabloon starten/stoppen":::

1. Selecteer onder aan de pagina de optie **controleren + maken** .
1. Selecteer **maken** om de implementatie te starten.
1. Selecteer het belpictogram (meldingen) boven in het scherm om de implementatiestatus te zien. U ziet **Implementatie wordt uitgevoerd**. Wacht tot de implementatie is voltooid.
1. Selecteer **Ga naar de resourcegroep** in het deelvenster meldingen. U ziet een scherm dat vergelijkbaar is met:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Resource lijst voor implementatie van Vm's sjabloon starten/stoppen":::

## <a name="enable-multiple-subscriptions"></a>Meerdere abonnementen inschakelen

Nadat de implementatie is gestart/gestopt, voert u de volgende stappen uit om Vm's v2 (preview) te starten/stoppen om actie te ondernemen voor meerdere abonnementen.

1. Kopieer de waarde voor de naam van de Azure-functie-app die u tijdens de implementatie hebt opgegeven.

1. Navigeer in de portal naar uw secundaire abonnement. Selecteer het abonnement en selecteer vervolgens **Access Control (IAM)**

1. Selecteer **toevoegen** en selecteer vervolgens **functie toewijzing toevoegen**.

1. Selecteer de rol **Inzender** in de vervolg keuzelijst **rol** .

1. Voer de naam van de Azure-functie toepassing in het veld **selecteren** in. Selecteer de functie naam in de resultaten.

1. Selecteer **Opslaan** om uw wijzigingen door te voeren.

## <a name="configure-schedules-overview"></a>Plannings overzicht configureren

Als u de automatiserings methode wilt beheren om het starten en stoppen van uw Vm's te bepalen, configureert u een of meer van de opgenomen logische apps op basis van uw vereisten.

- Geplande start-en stop acties zijn gebaseerd op een schema dat u opgeeft voor Azure Resource Manager en klassieke Vm's.**ststv2_vms_Scheduled_start** en **ststv2_vms_Scheduled_stop** de geplande start en stoppen configureren.

- Sequentieel-start-en stop acties zijn gebaseerd op een planning gericht op Vm's met vooraf gedefinieerde labels voor sequentiëren. Er worden slechts twee benoemde Tags ondersteund: **sequencestart** en **sequencestop**. **ststv2_vms_Sequenced_start** en **ststv2_vms_Sequenced_stop** de geordende start en stop configureren.

    > [!NOTE]
    > Dit scenario ondersteunt alleen Azure Resource Manager Vm's.

- Autostop: deze functionaliteit wordt alleen gebruikt voor het uitvoeren van een stop actie voor zowel Azure Resource Manager als klassieke Vm's op basis van het CPU-gebruik. Het kan ook een *actie ondernemen* op basis van een planning, waarmee waarschuwingen worden gemaakt op vm's en op basis van de voor waarde, de waarschuwing wordt geactiveerd om de actie stoppen uit te voeren.**ststv2_vms_AutoStop** configureert de functionaliteit voor automatisch stoppen.

Als u aanvullende schema's nodig hebt, kunt u een van de Logic Apps die wordt gegeven, dupliceren met de optie **klonen** in de Azure Portal.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Selecteer de kloon optie om een logische app te dupliceren":::

## <a name="scheduled-start-and-stop-scenario"></a>Gepland start-en stop scenario

Voer de volgende stappen uit om de geplande start-en stop actie te configureren voor Azure Resource Manager en klassieke Vm's. U kunt bijvoorbeeld het **ststv2_vms_Scheduled_start** schema zo configureren dat deze 's morgens worden gestart wanneer u op kantoor bent, en alle virtuele machines in een abonnement stoppen wanneer u de werk tijd op basis van het **ststv2_vms_Scheduled_stop** schema verlaat.

Het configureren van de logische app om alleen de Vm's te starten, wordt ondersteund.

Voor elk scenario kunt u de actie richten op een of meer abonnementen, één of meerdere resource groepen en een of meer virtuele machines opgeven in een lijst met insluitings-of uitsluitingen. U kunt ze niet samen opgeven in dezelfde logische app.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer vervolgens naar **Logic apps**.

1. Selecteer **ststv2_vms_Scheduled_start** in de lijst met Logic apps om een geplande start te configureren. Als u geplande stop wilt configureren, selecteert u **ststv2_vms_Scheduled_stop**.

1. Selecteer **Logic app Designer** in het linkerdeel venster.

1. Nadat Logic app Designer wordt weer gegeven, selecteert u in het deel venster ontwerp de optie **terugkeer patroon** om het schema van de logische app te configureren. Zie [terugkerende taak plannen](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)voor meer informatie over de specifieke terugkeer opties.

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="De terugkeer frequentie voor de logische app configureren":::

1. Selecteer in het deel venster ontwerp **functie-probeer** de doel instellingen te configureren. Als u in de hoofd tekst van de aanvraag Vm's wilt beheren voor alle resource groepen in het abonnement, wijzigt u de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Geef meerdere abonnementen op in de `subscriptions` matrix waarbij elke waarde wordt gescheiden door een komma, zoals in het volgende voor beeld.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Als u Vm's voor specifieke resource groepen wilt beheren, wijzigt u in de hoofd tekst van de aanvraag de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven. Elk opgegeven bronpad moet worden gescheiden door een komma. Als dat nodig is, kunt u één resource groep of meer opgeven.

    In dit voor beeld wordt ook gedemonstreerd dat een virtuele machine wordt uitgesloten. U kunt de virtuele machine uitsluiten door het resource-pad van de Vm's of het Joker teken op te geven.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Hier wordt de actie uitgevoerd op alle virtuele machines, behalve op de naam van de virtuele machine begint met AZ en BZ in beide abonnementen.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    Als u in de hoofd tekst van de aanvraag een specifieke set virtuele machines binnen het abonnement wilt beheren, wijzigt u de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven. Elk opgegeven bronpad moet worden gescheiden door een komma. U kunt indien nodig één virtuele machine opgeven.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Geordend start-en stop scenario

In een omgeving met twee of meer onderdelen op meerdere Azure Resource Manager Vm's in een gedistribueerde toepassings architectuur, die de volg orde ondersteunt waarin de onderdelen worden gestart en gestopt, is belang rijk.

1. Selecteer in de lijst met Logic apps de optie voor het configureren van een geordende start **ststv2_vms_Sequenced_start**. Als u een doorlopende stop wilt configureren, selecteert u **ststv2_vms_Sequenced_stop**.

1. Selecteer **Logic app Designer** in het linkerdeel venster.

1. Nadat Logic app Designer wordt weer gegeven, selecteert u in het deel venster ontwerp de optie **terugkeer patroon** om het schema van de logische app te configureren. Zie [terugkerende taak plannen](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)voor meer informatie over de specifieke terugkeer opties.

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="De terugkeer frequentie voor de logische app configureren":::

1. Selecteer in het deel venster ontwerp **functie-probeer** de doel instellingen te configureren. Als u in de hoofd tekst van de aanvraag Vm's wilt beheren voor alle resource groepen in het abonnement, wijzigt u de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Geef meerdere abonnementen op in de `subscriptions` matrix waarbij elke waarde wordt gescheiden door een komma, zoals in het volgende voor beeld.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Als u Vm's voor specifieke resource groepen wilt beheren, wijzigt u in de hoofd tekst van de aanvraag de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven. Elk opgegeven bronpad moet worden gescheiden door een komma. U kunt indien nodig een resource groep opgeven.

    In dit voor beeld wordt ook gedemonstreerd dat een virtuele machine wordt uitgesloten van het bronpad, vergeleken met het voor beeld voor geplande start/stop, waarvoor joker tekens worden gebruikt.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    Als u in de hoofd tekst van de aanvraag een specifieke set Vm's binnen een abonnement wilt beheren, wijzigt u de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven. Elk opgegeven bronpad moet worden gescheiden door een komma. U kunt indien nodig één virtuele machine opgeven.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Scenario voor automatisch stoppen

Vm's (preview) starten/stoppen met kunt u de kosten van het uitvoeren van Azure Resource Manager en klassieke Vm's in uw abonnement helpen beheren door computers te evalueren die niet worden gebruikt tijdens niet-piek perioden, zoals na uur, en ze automatisch af te sluiten als het processor gebruik minder is dan een opgegeven percentage.

De volgende eigenschappen van de metrische waarschuwing in de hoofd tekst van de aanvraag ondersteunen aanpassing:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Zie [metrische waarschuwingen in azure monitor](../../azure-monitor/alerts/alerts-metric-overview.md)voor meer informatie over hoe Azure monitor metrische waarschuwingen werken en hoe u ze configureert.

1. Selecteer **ststv2_vms_AutoStop** in de lijst met Logic apps om automatisch te stoppen te configureren.

1. Selecteer **Logic app Designer** in het linkerdeel venster.

1. Nadat Logic app Designer wordt weer gegeven, selecteert u in het deel venster ontwerp de optie **terugkeer patroon** om het schema van de logische app te configureren. Zie [terugkerende taak plannen](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)voor meer informatie over de specifieke terugkeer opties.

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="De terugkeer frequentie voor de logische app configureren":::

1. Selecteer in het deel venster ontwerp **functie-probeer** de doel instellingen te configureren. Als u in de hoofd tekst van de aanvraag Vm's wilt beheren voor alle resource groepen in het abonnement, wijzigt u de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    Als u Vm's voor specifieke resource groepen wilt beheren, wijzigt u in de hoofd tekst van de aanvraag de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven. Elk opgegeven bronpad moet worden gescheiden door een komma. U kunt indien nodig een resource groep opgeven.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    Als u in de hoofd tekst van de aanvraag een specifieke set virtuele machines binnen het abonnement wilt beheren, wijzigt u de hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt weer gegeven. Elk opgegeven bronpad moet worden gescheiden door een komma. U kunt indien nodig één virtuele machine opgeven.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het controleren van de status van uw virtuele Azure-machines die worden beheerd door de functie Vm's voor starten/stoppen v2 (preview) en andere beheer taken uitvoeren het artikel [Start/Stop Vm's beheren](manage.md) .