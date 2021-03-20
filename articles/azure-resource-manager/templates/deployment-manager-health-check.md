---
title: Implementatie van status integratie-Azure Configuratiebeheer
description: Hierin wordt beschreven hoe u een service implementeert over veel regio's met Azure Configuratiebeheer. Het bevat veilige implementatie procedures voor het controleren van de stabiliteit van uw implementatie voordat deze naar alle regio's wordt uitgevouwen.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536964"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Implementatie van de status integratie met Azure Configuratiebeheer introduceren (open bare preview)

Met [Azure Configuratiebeheer](./deployment-manager-overview.md) kunt u gefaseerde implementaties van Azure Resource Manager resources uitvoeren. De resources worden op een geordende manier door de regio geïmplementeerd. Met de geïntegreerde status controle van Azure Configuratiebeheer kunt u implementaties controleren en probleemloze implementaties automatisch stoppen, zodat u problemen kunt oplossen en de schaal van de impact vermindert. Deze functie kan de beschik baarheid van de service beperken, veroorzaakt door regressies in updates.

## <a name="health-monitoring-providers"></a>Providers voor status controle

Microsoft werkt samen met een aantal van de belangrijkste bedrijven op het gebied van servicestatusbewaking om u een eenvoudige kopieer- en plakoplossing te bieden voor de integratie van statuscontroles met uw implementaties, om statusintegratie zo eenvoudig mogelijk te maken. Als u nog geen Health Monitor gebruikt, zijn dit fantastische oplossingen om te beginnen met:

| ![Azure-monitor voor Health Monitor-provider Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![Azure Deployment Manager Health Monitor-provider datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Azure Deployment Manager Health Monitor-provider site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure Deployment Manager Health Monitor-provider Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor, het volledige stack-waarneem bare platform van micro soft voor Cloud native & hybride bewaking en analyse. |Datadog, het toonaangevende bewakings-en analyse platform voor moderne Cloud omgevingen. Bekijk [hoe Datadog integreert met Azure Configuratiebeheer](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, de alles-in-een privé-en open bare Cloud Services-bewakings oplossing. Bekijk [hoe Site24x7 integreert met Azure Configuratiebeheer](https://www.site24x7.com/azure/adm.html).| Wavefront, het bewakings-en analyse platform voor toepassings omgevingen met meerdere clouds. Bekijk [hoe Wavefront integreert met Azure Configuratiebeheer](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Hoe service status wordt bepaald

[Providers voor status controle](#health-monitoring-providers) bieden verschillende mechanismen voor het controleren van services en het melden van problemen met de service status. [Azure monitor](../../azure-monitor/overview.md) is een voor beeld van een dergelijke aanbieding. Azure Monitor kunnen worden gebruikt om waarschuwingen te maken wanneer bepaalde drempel waarden worden overschreden. Als u bijvoorbeeld een nieuwe update voor uw service implementeert, worden uw geheugen-en CPU-gebruik pieken boven de verwachte niveaus. Wanneer u een melding ontvangt, kunt u corrigerende maat regelen nemen.

Deze Health-providers bieden doorgaans REST-Api's zodat de status van de monitors van uw service programmatisch kan worden onderzocht. De REST-Api's kunnen worden weer gegeven met een eenvoudig gezond/beschadigd signaal (bepaald door de HTTP-antwoord code) en/of met gedetailleerde informatie over de signalen die het ontvangt.

Met de nieuwe `healthCheck` stap in Azure configuratiebeheer kunt u HTTP-codes declareren die duiden op een goede service. Voor complexe REST resultaten kunt u reguliere expressies opgeven die in overeenstemming zijn met een gezonde reactie.

De stroom voor het instellen van Azure Configuratiebeheer health checks:

1. Maak uw status monitors via een Health-Service provider van uw keuze.
1. Maak een of meer `healthCheck` stappen als onderdeel van uw Azure Configuratiebeheer-implementatie. Voer de `healthCheck` stappen uit met de volgende gegevens:

    1. De URI voor de REST API voor uw Health monitors (zoals gedefinieerd door uw Health Service-Provider).
    1. Verificatie-informatie. Momenteel wordt alleen de API-sleutel stijl verificatie ondersteund. Voor Azure Monitor moet het verificatie type worden ingesteld als `RolloutIdentity` de door de gebruiker toegewezen beheerde identiteit die wordt gebruikt voor Azure Configuratiebeheer-implementatie voor Azure monitor uitbreidt.
    1. [HTTP-status codes](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) of reguliere expressies waarmee een gezonde reactie wordt gedefinieerd. U kunt reguliere expressies opgeven, die allemaal moeten overeenkomen voor het antwoord dat als gezond moet worden beschouwd, of u kunt expressies opgeven waarvan de reactie moet overeenkomen om te worden beschouwd als in orde. Beide methoden worden ondersteund.

    De volgende JSON is een voor beeld om Azure Monitor te integreren met Azure Configuratiebeheer. In het voor beeld wordt `RolloutIdentity` een status controle gebruikt en gemaakt waarbij een implementatie wordt voortgezet als er geen waarschuwingen zijn. De enige ondersteunde Azure Monitor-API: [waarschuwingen – alles ophalen](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    De volgende JSON is een voor beeld van alle andere providers voor status bewaking:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Roep de `healthCheck` stappen op het juiste moment aan in uw Azure Configuratiebeheer-implementatie. In het volgende voor beeld `healthCheck` wordt een stap aangeroepen in `postDeploymentSteps` van `stepGroup2` .

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Zie [zelf studie: status controle gebruiken in Azure Configuratiebeheer](./deployment-manager-tutorial-health-check.md)voor een voor beeld.

## <a name="phases-of-a-health-check"></a>Fasen van een status controle

Op dit moment weet Azure Configuratiebeheer hoe u een query kunt uitvoeren voor de status van uw service en in welke fasen in uw implementatie dit moet doen. Azure Configuratiebeheer biedt echter ook een diepe configuratie van de timing van deze controles. Een `healthCheck` stap wordt uitgevoerd in drie opeenvolgende fasen, die allemaal Configureer bare duur hebben:

1. Wait

    1. Nadat een implementatie bewerking is voltooid, kunnen Vm's opnieuw worden opgestart, opnieuw worden geconfigureerd op basis van nieuwe gegevens of zelfs worden gestart voor de eerste keer. Het kan ook enige tijd duren voordat de status van de Services wordt gestart, zodat deze door de provider voor status controle kan worden geaggregeerd. Tijdens dit tumultuous-proces is het wellicht niet verstandig om te controleren of de service status is verstreken omdat de update nog niet een constante status heeft bereikt. Het is mogelijk dat de service in orde is en dat de status van de resources onbeschadigd is.
    1. Tijdens de wacht fase wordt de service status niet bewaakt. Dit wordt gebruikt om de geïmplementeerde resources de tijd maken te geven voordat het status controle proces wordt gestart.

1. Elastisch

    1. Omdat het niet mogelijk is om in alle gevallen te weten hoe lang het duurt voordat bronnen stabiel worden, kan de elastische fase een flexibele periode hebben tussen het moment waarop de bronnen mogelijk Insta Biel zijn en wanneer ze zijn vereist om een goede stabiele status te onderhouden.
    1. Wanneer de elastische fase begint, begint Azure Configuratiebeheer het aansturen van het meegeleverde REST-eind punt voor de service status regel matig. Het polling-interval kan worden geconfigureerd.
    1. Als de Health Monitor terugkomt met signalen die aangeven dat de service een slechte status heeft, worden deze signalen genegeerd, wordt de elastische fase voortgezet en wordt polling voortgezet.
    1. Wanneer de Health Monitor signalen retourneert die aangeven dat de service in orde is, wordt de elastische fase beëindigd en begint de goede status-fase.
    1. De duur die is opgegeven voor de elastische fase is de maximale hoeveelheid tijd die kan worden gebruikt voor het controleren van de status van de service voordat een gezonde respons als verplicht wordt beschouwd.

1. Goede status

    1. Tijdens de goede status-fase wordt de status van de service voortdurend gecontroleerd op hetzelfde interval als de elastische fase.
    1. De service is naar verwachting van de status bewakings provider goed te onderhouden voor de gehele opgegeven duur.
    1. Als er op een wille keurig moment een niet-gezond antwoord wordt gedetecteerd, wordt de volledige implementatie door Azure Configuratiebeheer gestopt en wordt het REST-antwoord geretourneerd, waarbij de beschadigde service signalen worden weer gegeven.
    1. Nadat de goede status-duur is beëindigd, `healthCheck` is de is voltooid en gaat de implementatie verder met de volgende stap.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de status controle integreert in azure Configuratiebeheer. Ga door naar het volgende artikel voor meer informatie over het implementeren met Configuratiebeheer.

> [!div class="nextstepaction"]
> [Zelf studie: status controle gebruiken in azure Configuratiebeheer](./deployment-manager-tutorial-health-check.md)
