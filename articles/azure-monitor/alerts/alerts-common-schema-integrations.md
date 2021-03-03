---
title: Het algemene waarschuwings schema integreren met Logic Apps
description: Meer informatie over het maken van een logische app die gebruikmaakt van het algemene waarschuwings schema voor het afhandelen van al uw waarschuwingen.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 4824c5ab1826260ee1eb3639712d7138c7c85bfe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738664"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Het algemene waarschuwings schema integreren met Logic Apps

In dit artikel wordt beschreven hoe u een logische app maakt die gebruikmaakt van het algemene waarschuwings schema voor het afhandelen van al uw waarschuwingen.

## <a name="overview"></a>Overzicht

Het [algemene waarschuwings schema](./alerts-common-schema.md) voorziet in een gestandaardiseerd en uitbreidbaar JSON-schema voor al uw verschillende typen waarschuwingen. Het algemene waarschuwings schema is het nuttigst wanneer u programmatisch gebruikmaakt van webhooks, runbooks en Logic apps. In dit artikel laten we zien hoe een enkele logische app kan worden ontworpen om al uw waarschuwingen te verwerken. Dezelfde principes kunnen worden toegepast op andere programmatische methoden. De logische app die in dit artikel wordt beschreven, maakt duidelijk gedefinieerde variabelen voor de [essentiële velden](alerts-common-schema-definitions.md#essentials)en beschrijft ook hoe u de specifieke logica van [waarschuwings typen](alerts-common-schema-definitions.md#alert-context) kunt verwerken.


## <a name="prerequisites"></a>Vereisten 

In dit artikel wordt ervan uitgegaan dat de lezer bekend is met 
* Waarschuwings regels instellen ([metrische gegevens](../alerts/alerts-metric.md), [Logboeken](./alerts-log.md), [activiteiten logboeken](./alerts-activity-log.md))
* [Actie groepen](./action-groups.md) instellen
* Het [algemene waarschuwings schema](./alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) inschakelen vanuit actie groepen

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Een logische app maken met behulp van het gemeen schappelijke waarschuwings schema

1. Volg de [stappen die worden beschreven om uw logische app te maken](./action-groups-logic-app.md). 

1.  Selecteer de trigger: **Wanneer een HTTP-aanvraag wordt ontvangen**.

    ![Triggers voor logische app](media/action-groups-logic-app/logic-app-triggers.png "Triggers voor logische app")

1.  Selecteer **bewerken** om de HTTP-aanvraag trigger te wijzigen.

    ![HTTP-aanvraag triggers](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-aanvraag triggers")


1.  Kopieer en plak het volgende schema:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Selecteer **+** **nieuwe stap** en kies vervolgens **een actie toevoegen**.

    ![Een actie toevoegen](media/action-groups-logic-app/add-action.png "Een actie toevoegen")

1. In deze fase kunt u een verscheidenheid aan connectors (micro soft teams, toegestane vertraging, Sales Force, enz.) toevoegen op basis van uw specifieke bedrijfs vereisten. U kunt het vak ' essentiële velden ' gebruiken. 

    ![Essentiële velden](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Essentiële velden")
    
    U kunt ook voorwaardelijke logica ontwerpen op basis van het waarschuwings type met behulp van de optie ' Expression '.

    ![Expressie voor logische app](media/alerts-common-schema-integrations/logic-app-expressions.png "Expressie voor logische app")
    
     In het [veld ' monitoringService '](alerts-common-schema-definitions.md#alert-context) kunt u het waarschuwings type uniek identificeren, op basis waarvan u de voorwaardelijke logica kunt maken.

    
    In het onderstaande fragment wordt bijvoorbeeld gecontroleerd of de waarschuwing een Application Insights op basis van een logboek waarschuwing is, en als dit het geval is, worden de zoek resultaten afgedrukt. Anders wordt ' N.V.T. ' afgedrukt.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Meer informatie over het [schrijven van expressies voor logische apps](../../logic-apps/workflow-definition-language-functions-reference.md#logical-comparison-functions).

    


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over actiegroepen](./action-groups.md).
* Meer [informatie over het algemene waarschuwings schema](./alerts-common-schema.md).