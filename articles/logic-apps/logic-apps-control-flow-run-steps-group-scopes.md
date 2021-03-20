---
title: Acties groeperen en uitvoeren op basis van het bereik
description: Bereik acties maken die worden uitgevoerd op basis van de groeps status in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 95b5cc191ac6857bf8e1b09e70b22d928473fe03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92314848"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Acties uitvoeren op basis van de groeps status met behulp van scopes in Azure Logic Apps

Als u alleen acties wilt uitvoeren nadat een andere groep acties is geslaagd of mislukt, groepeert u deze acties binnen een *bereik*. Deze structuur is handig als u acties wilt ordenen als logische groep, de status van die groep wilt evalueren en acties wilt uitvoeren die zijn gebaseerd op de status van het bereik. Nadat alle acties in een bereik zijn uitgevoerd, krijgt de scope ook een eigen status. U kunt bijvoorbeeld bereiken gebruiken als u [uitzonde ringen en fout afhandeling](../logic-apps/logic-apps-exception-handling.md#scopes)wilt implementeren. 

Als u de status van een bereik wilt controleren, kunt u dezelfde criteria gebruiken die u gebruikt om de uitvoerings status van een Logic apps, zoals **geslaagd**, **mislukt**, **geannuleerd**, te bepalen. Wanneer alle acties van het bereik slagen, is de status van het bereik standaard gemarkeerd als **geslaagd**. Maar wanneer een actie in de scope mislukt of wordt geannuleerd, wordt de status van het bereik gemarkeerd als **mislukt**. Zie [limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md)voor limieten voor scopes. 

Dit is bijvoorbeeld een logische app op hoog niveau die gebruikmaakt van een bereik voor het uitvoeren van specifieke acties en een voor waarde om de status van het bereik te controleren. Als een of meer acties in de scope onverwacht zijn mislukt of eindigen, wordt het bereik gemarkeerd als **mislukt** of **afgebroken** , en verzendt de logische app een bericht ' scope failed '. Als alle acties binnen het bereik zijn geslaagd, verzendt de logische app een bericht ' scope geslaagd '.

![Diagram toont de bereik stroom van de logische app met voor beelden van ' scope failed ' en ' scope geslaagd '.](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Vereisten

Als u het voor beeld in dit artikel wilt volgen, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een e-mail account van een e-mail provider die door Logic Apps wordt ondersteund. In dit voor beeld wordt Outlook.com gebruikt. Als u een andere provider gebruikt, blijft de algemene stroom hetzelfde, maar uw gebruikers interface ziet er anders uit.

* Een Bing Maps-sleutel. Als u deze sleutel wilt ophalen, raadpleegt u <a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">een Bing Maps-sleutel ophalen</a>.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Een logische voor beeld-app maken

Maak eerst deze voorbeeld logische app, zodat u later een bereik kunt toevoegen:

![Een logische voor beeld-app maken](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Een trigger voor het plannen van een **terugkeer patroon** waarmee de Bing Maps-service wordt gecontroleerd met een opgegeven interval
* Een **Bing Maps-route actie ophalen** waarmee de reis tijd tussen twee locaties wordt gecontroleerd
* Een voorwaardelijke instructie waarmee wordt gecontroleerd of de reis tijd de opgegeven reis tijd overschrijdt
* Een actie die u een e-mail stuurt dat de huidige reis tijd de opgegeven tijd overschrijdt

U kunt uw logische app op elk gewenst moment opslaan, zodat u uw werk regel matig opslaat.

1. Meld u aan bij de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, als u dat nog niet hebt gedaan. Een lege, logische app maken.

1. Voeg de trigger **schema-terugkeer patroon** toe met de volgende instellingen: **interval** = "1" en **frequentie** = "minuut"

   ![Trigger voor planning-terugkeer patroon instellen](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Als u de weer gave visueel wilt vereenvoudigen en de details van elke actie in de ontwerp functie wilt verbergen, vouwt u de vorm van elke actie samen terwijl u deze stappen uitvoert.

1. Voeg de **Bing Maps-route actie Get** toe. 

   1. Als u nog geen Bing Maps-verbinding hebt, wordt u gevraagd om een verbinding te maken.

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Verbindingsnaam** | BingMapsConnection | Geef een naam op voor uw verbinding. | 
      | **API-sleutel** | <*your-Bing-Maps-key*> | Voer de sleutel voor Bing Kaarten in die u eerder hebt ontvangen. | 
      ||||  

   1. Stel de actie **route ophalen** in, zoals wordt weer gegeven in de tabel onder deze afbeelding:

      ![De actie Bing Kaarten-route ophalen instellen](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Zie [Een route berekenen](/bingmaps/rest-services/routes/calculate-a-route) voor meer informatie over deze parameters.

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Routepunt 1** | <*starten*> | Voer de oorsprong van uw route in. | 
      | **Routepunt 2** | <*endsystemen*> | Voer de bestemming van uw route in. | 
      | **Vermijden** | Geen | Voer items in om te voor komen op uw route, zoals snelwegen, gestuurde berichten, enzovoort. Zie [een route berekenen](/bingmaps/rest-services/routes/calculate-a-route)voor mogelijke waarden. | 
      | **Optimaliseren** | timeWithTraffic | Selecteer een para meter voor het optimaliseren van uw route, zoals de afstand, tijd met de huidige verkeers informatie, enzovoort. In dit voor beeld wordt deze waarde gebruikt: ' timeWithTraffic ' | 
      | **Afstandseenheid** | <*your-preference*> | Voer de afstands eenheid in om uw route te berekenen. In dit voor beeld wordt deze waarde gebruikt: ' mijl ' | 
      | **Vervoermiddel** | Auto | Voer de reis wijze in voor uw route. In dit voor beeld wordt deze waarde ' aangedreven ' gebruikt | 
      | **Datum/tijd openbaar vervoer** | Geen | Is alleen van toepassing op de doorvoer modus. | 
      | **Type door Voer Date-Type** | Geen | Is alleen van toepassing op de doorvoer modus. | 
      ||||  

1. [Voeg een voor waarde toe](../logic-apps/logic-apps-control-flow-conditional-statement.md) waarmee wordt gecontroleerd of de huidige reis tijd met het verkeer een opgegeven tijd overschrijdt. 
   Voor dit voor beeld voert u de volgende stappen uit:

   1. Wijzig de naam van de voor waarde met deze beschrijving: **als de verkeers tijd meer is dan de opgegeven tijd**

   1. Klik in de kolom uiterst links in het vak **een waarde kiezen** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst het veld **reis duur verkeer** in seconden. 

      ![Voorwaarde bouwen](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Selecteer deze operator in het middelste vak: **is groter dan**

   1. Voer in de meest rechtse kolom de vergelijkings waarde in seconden in en komt overeen met 10 minuten: **600**

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Voor waarde voltooid](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Voeg in de vertakking **Indien waar** de actie ' e-mail verzenden ' toe voor uw e-mail provider. 
   Stel deze actie in door de stappen onder deze installatie kopie te volgen:

   ![De actie ' een e-mail verzenden ' toevoegen aan de vertakking indien waar](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Voer in het veld **aan** uw e-mail adres in voor test doeleinden.

   1. Voer in het veld **onderwerp** deze tekst in:

      ```Time to leave: Traffic more than 10 minutes```

   1. Voer in het veld **bericht** tekst de volgende tekst in met een spatie: 

      ```Travel time:```

      Terwijl uw cursor wordt weer gegeven in het veld **hoofd tekst** , blijft de lijst met dynamische inhoud geopend, zodat u alle para meters kunt selecteren die op dit moment beschikbaar zijn.

   1. Kies in de lijst met dynamische inhoud voor **Expressie**.

   1. Zoek en selecteer de functie **div ()** . 
      Plaats de cursor binnen de haakjes van de functie.

   1. Terwijl de cursor zich binnen de haakjes van de functie bevindt, kiest u **dynamische inhoud** zodat de lijst met dynamische inhoud wordt weer gegeven. 
   
   1. Selecteer in de sectie **route ophalen** het veld verkeer **duur verkeer** .

      ![Selecteer ' verkeer duur verkeer '](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Nadat het veld is omgezet in JSON-indeling, voegt u een **komma** () toe, ```,``` gevolgd door het getal ```60``` , zodat u de waarde in **verkeers duur verkeer** van seconden naar minuten converteert. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Uw expressie ziet er nu uit als in dit voor beeld:

      ![Expressie voor volt ooien](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Als u klaar bent, kiest u **Done**.

   <!-- markdownlint-disable MD038 -->
   1. Nadat de expressie is opgelost, voegt u deze tekst toe met een voorloop spatie: ``` minutes```
  
       Uw **tekst** veld ziet er nu uit als in dit voor beeld:

       ![Het veld Body is voltooid](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Sla uw logische app op.

Vervolgens voegt u een bereik toe zodat u specifieke acties kunt groeperen en de status ervan evalueert.

## <a name="add-a-scope"></a>Een bereik toevoegen

1. Als u dat nog niet hebt gedaan, opent u de logische app in de ontwerp functie voor logische apps. 

1. Voeg een bereik toe op de gewenste werk stroom locatie. Als u bijvoorbeeld een bereik wilt toevoegen tussen de bestaande stappen in de werk stroom van de logische app, voert u de volgende stappen uit: 

   1. Beweeg de aanwijzer over de pijl waar u het bereik wilt toevoegen. 
   Kies het **plus teken** ( **+** ) > **een actie toe te voegen**.

      ![Een bereik toevoegen](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Voer in het zoekvak ' scope ' in als uw filter. 
   Selecteer de **bereik** actie.

## <a name="add-steps-to-scope"></a>Stappen toevoegen aan het bereik

1. Voeg nu de stappen toe of sleep de bestaande stappen die u binnen het bereik wilt uitvoeren. Voor dit voor beeld sleept u deze acties naar het bereik:
      
   * **Route ophalen**
   * **Als de verkeers tijd meer is dan de opgegeven tijd**, inclusief de vertakkingen **waar** en **Onwaar**

   Uw logische app ziet er nu uit als in dit voor beeld:

   ![Bereik toegevoegd](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Voeg onder het bereik een voor waarde toe waarmee de status van het bereik wordt gecontroleerd. Wijzig de naam van de voor waarde met deze beschrijving: **als het bereik is mislukt**

   ![Voor waarde toevoegen om de scope status te controleren](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Voeg in de voor waarde deze expressies toe om te controleren of de status van het bereik gelijk is aan "Failed" of "Aborted". 

   1. Kies **toevoegen** om een andere rij toe te voegen. 

   1. In elke rij klikt u in het linkervak zodat de lijst met dynamische inhoud wordt weer gegeven. 
   Kies **expressie** in de lijst met dynamische inhoud. Voer in het invoervak deze expressie in en kies vervolgens **OK**: 
   
      `result('Scope')[0]['status']`

      ![Scherm opname van het vak ' expressie ' waarin de resultaat expressie is gemarkeerd.](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Voor beide rijen selecteert u **is gelijk aan** als de operator. 
   
   1. Voer in de eerste rij de waarde in voor de vergelijkings waarden `Failed` . 
   Voer in de tweede rij in `Aborted` . 

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Expressie toevoegen waarmee de status van het bereik wordt gecontroleerd](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Stel nu de eigenschap van de voor waarde in `runAfter` zodat de voor waarde de scope status controleert en voert de overeenkomende actie uit die u in latere stappen hebt gedefinieerd.

   1. Kies de knop met **weglatings** tekens (...) op de voor waarde **als het bereik is mislukt** en kies vervolgens **uitvoeren na**.

      ![De eigenschap runAfter configureren](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Alle volgende Scope statussen selecteren: **is geslaagd**, is **mislukt**, **wordt overgeslagen** en er is **een time-out opgetreden**

      ![Scope status selecteren](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Wanneer u klaar bent, kiest u **Gereed**. 
   De voor waarde bevat nu een pictogram ' informatie '.

1. Voeg in de vertakkingen **waar** en **indien onwaar** , de acties toe die u wilt uitvoeren op basis van de scope status, bijvoorbeeld een e-mail of bericht verzenden.

   ![Acties toevoegen die moeten worden uitgevoerd op basis van de scope status](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Sla uw logische app op.

Uw voltooide logische app ziet er nu uit als in dit voor beeld:

![Voltooide logische app met bereik](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Uw werk testen

Kies **uitvoeren** op de werk balk van de ontwerp functie. Als alle acties binnen het bereik zijn geslaagd, wordt het bericht ' scope geslaagd ' weer gegeven. Als er geen acties met een bereik zijn voltooid, krijgt u het bericht ' scope failed '. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definitie

Als u in de code weergave werkt, kunt u in plaats daarvan een bereik structuur definiëren in de JSON-definitie van de logische app. Hier volgt een voor beeld van de JSON-definitie voor trigger en acties in de vorige logische app:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Ga naar de [site voor Azure Logic Apps gebruikers feedback](https://aka.ms/logicapps-wish)om functies en suggesties te verzenden of hierop te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voor waarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (switch-instructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samen voegen (vertakkingen)](../logic-apps/logic-apps-control-flow-branches.md)