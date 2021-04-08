---
title: Informatie over instellingen voor automatisch schalen in Azure Monitor
description: Een gedetailleerde uitsplitsing van instellingen voor automatisch schalen en hoe ze werken. Is van toepassing op Virtual Machines, Cloud Services Web Apps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100610860"
---
# <a name="understand-autoscale-settings"></a>Inzicht in instellingen voor automatisch schalen
Met instellingen voor automatisch schalen kunt u ervoor zorgen dat u over de juiste hoeveelheid resources beschikt voor het afhandelen van de schommeling van uw toepassing. U kunt instellingen voor automatisch schalen configureren om te worden geactiveerd op basis van metrische gegevens die belasting of prestaties aangeven, of op een geplande datum en tijd. In dit artikel vindt u gedetailleerde informatie over de anatomie van een instelling voor automatisch schalen. Het artikel begint met het schema en de eigenschappen van een instelling en gaat vervolgens door met de verschillende profiel typen die kunnen worden geconfigureerd. Ten slotte wordt in het artikel beschreven hoe de functie voor automatisch schalen in azure evalueert welk profiel op een bepaald moment moet worden uitgevoerd.

## <a name="autoscale-setting-schema"></a>Schema voor automatisch schalen instellen
Ter illustratie van het instellings schema voor automatisch schalen wordt de volgende instelling voor automatisch schalen gebruikt. Het is belang rijk te weten dat deze instelling voor automatisch schalen:
- Eén profiel. 
- Twee metrische regels in dit profiel: één voor uitschalen en één voor schalen in.
  - De scale-out-regel wordt geactiveerd wanneer het gemiddelde percentage CPU-gegevens van de virtuele-machine schaalset groter is dan 85 procent gedurende de afgelopen 10 minuten.
  - De scale-in regel wordt geactiveerd wanneer het gemiddelde van de virtuele-machine schaalset minder is dan 60 procent voor de afgelopen minuut.

> [!NOTE]
> Een instelling kan meerdere profielen hebben. Zie de sectie [profielen](#autoscale-profiles) voor meer informatie. Een profiel kan ook meerdere regels voor uitschalen en de gedefinieerde regels voor de schaaling bevatten. Zie de sectie [evaluatie](#autoscale-evaluation) voor meer informatie over hoe deze worden geëvalueerd.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Sectie | Elementnaam | Beschrijving |
| --- | --- | --- |
| Instelling | Id | De resource-ID van de instelling voor automatisch schalen. Instellingen voor automatisch schalen zijn een Azure Resource Manager resource. |
| Instelling | naam | De naam van de instelling voor automatisch schalen. |
| Instelling | location | De locatie van de instelling voor automatisch schalen. Deze locatie kan afwijken van de locatie van de resource die wordt geschaald. |
| properties | targetResourceUri | De resource-ID van de resource die wordt geschaald. U kunt slechts één instelling voor automatisch schalen per resource hebben. |
| properties | profielen | Een instelling voor automatisch schalen bestaat uit een of meer profielen. Telkens wanneer de engine voor automatisch schalen wordt uitgevoerd, wordt er één profiel uitgevoerd. |
| profiel | naam | De naam van het profiel. U kunt een wille keurige naam kiezen die u helpt bij het identificeren van het profiel. |
| profiel | Capaciteit. maximum | De Maxi maal toegestane capaciteit. Het zorgt ervoor dat automatisch schalen tijdens het uitvoeren van dit profiel uw resource boven dit nummer niet kan schalen. |
| profiel | Capaciteit. minimum | De toegestane minimum capaciteit. Het zorgt ervoor dat automatisch schalen tijdens het uitvoeren van dit profiel de resource onder dit nummer niet kan schalen. |
| profiel | Capaciteit. standaard | Als er een probleem is opgetreden bij het lezen van de bron metriek (in dit geval de CPU van ' vmss1 ') en de huidige capaciteit lager is dan de standaard waarde, schaalt automatisch schalen naar de standaard waarde. Hiermee wordt de beschik baarheid van de resource gegarandeerd. Als de huidige capaciteit al hoger is dan de standaard capaciteit, wordt automatisch schalen niet geschaald in. |
| profiel | regels | Automatisch schalen geschaald tussen de maximale en minimale capaciteit, door gebruik te maken van de regels in het profiel. U kunt meerdere regels in een profiel hebben. Normaal gesp roken zijn er twee regels: een om te bepalen wanneer moet worden uitgeschaald en de andere om te bepalen wanneer moet worden geschaald. |
| budgetoverboekingsregel | metricTrigger | Definieert de metrische voor waarde van de regel. |
| metricTrigger | metricName | De naam van de metriek. |
| metricTrigger |  metricResourceUri | De resource-ID van de resource die de metriek verzendt. In de meeste gevallen is dit hetzelfde als de resource die wordt geschaald. In sommige gevallen kan het verschillend zijn. U kunt bijvoorbeeld een schaalset voor virtuele machines schalen op basis van het aantal berichten in een opslag wachtrij. |
| metricTrigger | timeGrain | De metrische sampling duur. **TimeGrain = "PT1M"** betekent bijvoorbeeld dat de metrische gegevens elke 1 minuut moeten worden geaggregeerd met behulp van de aggregatie methode die is opgegeven in het statistische element. |
| metricTrigger | statistiek | De aggregatie methode binnen de timeGrain-periode. Bijvoorbeeld **Statistiek = "gemiddelde"** en **TIMEGRAIN = "PT1M"** betekent dat de metrische gegevens elke 1 minuut moeten worden geaggregeerd door het gemiddelde te nemen. Deze eigenschap bepaalt hoe de metrische gegevens worden gesampled. |
| metricTrigger | timeWindow | De tijd die moet worden weer gegeven voor metrische gegevens. Zo betekent **timeWindow = "PT10M"** dat elke keer dat automatisch schalen wordt uitgevoerd, de metrische gegevens voor de afgelopen 10 minuten worden opgevraagd. In het tijd venster kunnen uw metrische gegevens worden genormaliseerd en wordt voor komen dat er tijdelijke pieken worden gecommuniceerd. |
| metricTrigger | timeAggregation | De aggregatie methode die wordt gebruikt om de metrische gegevens van de steek proef samen te voegen. Voor beeld: **TimeAggregation = "Average"** moet de steekproef waarden worden geaggregeerd door het gemiddelde te nemen. In het vorige geval neemt u de voor beelden van 10 1 minuten en berekent u het gemiddelde. |
| budgetoverboekingsregel | scaleAction | De actie die moet worden uitgevoerd wanneer de metricTrigger van de regel wordt geactiveerd. |
| scaleAction | richting | "Verg Roten" om uit te schalen of "verkleinen" om in te schalen.|
| scaleAction | waarde | Hoeveel u de capaciteit van de resource wilt verg Roten of verkleinen. |
| scaleAction | cooldown | De hoeveelheid tijd die moet worden gewacht na een schaal bewerking voordat deze opnieuw wordt geschaald. Als bijvoorbeeld **cooldown = "PT10M"** is, wordt automatisch schalen niet meer dan tien minuten opnieuw geprobeerd te schalen. De cooldown is om de metrische gegevens te stabiliseren nadat het toevoegen of verwijderen van exemplaren is toegestaan. |

## <a name="autoscale-profiles"></a>Profielen automatisch schalen

Er zijn drie soorten profielen voor automatisch schalen:

- **Regulier profiel:** Het meest voorkomende profiel. Als u uw resource niet hoeft te schalen op basis van de dag van de week of op een bepaalde dag, kunt u een gewoon profiel gebruiken. Dit profiel kan vervolgens worden geconfigureerd met metrische regels die bepalen wanneer moet worden uitgeschaald en wanneer moet worden geschaald. Er mag slechts één standaard profiel worden gedefinieerd.

    Het voorbeeld profiel dat eerder in dit artikel wordt gebruikt, is een voor beeld van een gewoon profiel. Het is ook mogelijk om een profiel in te stellen dat moet worden geschaald naar een statisch aantal exemplaren voor uw resource.

- **Profiel voor vaste datum:** Dit profiel is voor speciale gevallen. Stel dat u een belang rijke gebeurtenis hebt die op 26 december 2017 (PST) wordt weer geven. U wilt dat de minimale en maximale capaciteit van uw resource op die dag verschillend zijn, maar nog steeds op dezelfde metrische waarden worden geschaald. In dit geval moet u een vast datum profiel toevoegen aan de lijst met profielen van uw instelling. Het profiel is geconfigureerd om alleen op de dag van de gebeurtenis te worden uitgevoerd. Voor elke andere dag maakt automatisch schalen gebruik van het reguliere profiel.

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **Terugkeer profiel:** Met dit type profiel kunt u ervoor zorgen dat dit profiel altijd wordt gebruikt op een bepaalde dag van de week. Terugkerende profielen hebben alleen een begin tijd. Ze worden uitgevoerd tot het volgende profiel voor terugkeer patroon of vaste datum is ingesteld op Start. Voor een instelling voor automatisch schalen met slechts één terugkeer profiel wordt dat profiel uitgevoerd, zelfs als er een standaard profiel in dezelfde instelling is gedefinieerd. De volgende twee voor beelden laten zien hoe dit profiel wordt gebruikt:

    **Voor beeld 1: week dagen versus weekends**
    
    Stel dat u in het weekend wilt dat uw maximum capaciteit 4 is. Op werk dagen, omdat u meer belasting verwacht, wilt u dat de maximum capaciteit 10 is. In dit geval bevat uw instelling twee terugkeer profielen, één om te worden uitgevoerd in weekends en de andere op werk dagen.
    De instelling ziet er als volgt uit:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    De voor gaande instelling toont aan dat elk terugkeer profiel een schema heeft. Dit schema bepaalt wanneer het profiel wordt gestart. Het profiel stopt wanneer het tijd is om een ander profiel uit te voeren.

    In de voor gaande instelling is bijvoorbeeld ' weekdayProfile ' ingesteld op starten op maandag om 12:00 uur. Dit betekent dat dit profiel wordt gestart op maandag om 12:00 uur. Het proces wordt voortgezet tot zaterdag om 12:00 uur, wanneer ' weekendProfile ' is gepland om te worden uitgevoerd.

    **Voor beeld 2: kantoor uren**
    
    Stel dat u één metrische drempel waarde wilt instellen tijdens kantoor uren (9:00 uur 5:00 uur) en een andere voor de andere tijden. De instelling ziet er als volgt uit:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Met de voor gaande instelling wordt aangegeven dat "businessHoursProfile" begint op maandag om 9:00 uur en blijft 5:00 PM. Dat is het geval wanneer ' nonBusinessHoursProfile ' wordt gestart. De "nonBusinessHoursProfile" wordt uitgevoerd tot 9:00 uur en vervolgens wordt het ' businessHoursProfile ' opnieuw gebruikt. Dit wordt herhaald tot vrijdag om 5:00 uur. Op dat moment wordt ' nonBusinessHoursProfile ' overal op maandag uitgevoerd om 9:00 uur.
    
> [!Note]
> De gebruikers interface voor automatisch schalen in het Azure Portal afdwingt eind tijden voor terugkerende profielen en begint met het uitvoeren van het standaard profiel voor de instelling voor automatisch schalen in tussen terugkeer patroon profielen.
    
## <a name="autoscale-evaluation"></a>Evaluatie van automatisch schalen
Gezien dat instellingen voor automatisch schalen meerdere profielen kunnen hebben en elk profiel meerdere metrische regels kan hebben, is het belang rijk om te begrijpen hoe een instelling voor automatisch schalen wordt geëvalueerd. Telkens wanneer de taak voor automatisch schalen wordt uitgevoerd, wordt eerst het profiel gekozen dat van toepassing is. Vervolgens evalueert automatisch schalen de minimum-en maximum waarden en alle metrische regels in het profiel en bepaalt of een schaal actie nood zakelijk is.

### <a name="which-profile-will-autoscale-pick"></a>Welk profiel kiest u voor automatisch schalen?

Automatisch schalen maakt gebruik van de volgende volg orde om het profiel te kiezen:
1. Eerst wordt gezocht naar een profiel voor vaste datums dat is geconfigureerd om nu te worden uitgevoerd. Als dat het geval is, wordt het automatisch geschaald. Als er meerdere profielen voor vaste datums zijn die moeten worden uitgevoerd, selecteert automatisch schalen het eerste profiel.
2. Als er geen vaste-datum profielen zijn, wordt door automatisch schalen gekeken naar de terugkeer profielen. Als er een terugkeer profiel wordt gevonden, wordt het uitgevoerd.
3. Als er geen vaste datum-of terugkeer profielen zijn, voert automatisch schalen het reguliere profiel uit.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hoe evalueert automatisch schalen meerdere regels?

Wanneer automatisch schalen bepaalt welk profiel moet worden uitgevoerd, worden alle scale-out regels in het profiel geëvalueerd (dit zijn regels met **Direction = "toename"**).

Als een of meer Scale-outregels worden geactiveerd, berekent automatisch schalen de nieuwe capaciteit die wordt bepaald door de **scaleAction** van elk van deze regels. Vervolgens wordt de schaal uitgebreid naar het maximum van deze capaciteit om de beschik baarheid van de service te garanderen.

Stel dat er een schaalset voor virtuele machines is met een huidige capaciteit van 10. Er zijn twee uitzonderings regels: een die de capaciteit met 10 procent verhoogt en één waarmee de capaciteit met drie aantallen wordt verhoogd. De eerste regel resulteert in een nieuwe capaciteit van 11 en de tweede regel resulteert in een capaciteit van 13. Om ervoor te zorgen dat de service beschikbaar is, kiest automatisch schalen de actie die de maximale capaciteit als resultaat heeft, zodat de tweede regel wordt gekozen.

Als er geen scale-out-regels worden geactiveerd, worden alle regels voor inschalen geëvalueerd (regels met **Direction = "verminderen"**). Automatisch schalen vereist alleen een inschaal actie als alle scale-in regels worden geactiveerd.

Automatisch schalen berekent de nieuwe capaciteit die wordt bepaald door de **scaleAction** van elk van deze regels. Vervolgens wordt de schaal actie gekozen die het maximum van deze capaciteit oplevert om de beschik baarheid van de service te garanderen.

Stel dat er een schaalset voor virtuele machines is met een huidige capaciteit van 10. Er zijn twee regels voor het inschalen: een die de capaciteit met 50 procent verlaagt en één die de capaciteit met 3 telt. De eerste regel resulteert in een nieuwe capaciteit van 5 en de tweede regel resulteert in een capaciteit van 7. Om ervoor te zorgen dat de service beschikbaar is, kiest automatisch schalen de actie die de maximale capaciteit als resultaat heeft, zodat de tweede regel wordt gekozen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over automatisch schalen door te verwijzen naar het volgende:

* [Overzicht van automatisch schalen](./autoscale-overview.md)
* [Azure Monitor automatisch schalen van algemene metrische gegevens](./autoscale-common-metrics.md)
* [Best practices voor automatische schaalaanpassing in Azure Monitor](./autoscale-best-practices.md)
* [Acties voor automatisch schalen gebruiken om e-mail berichten en webhook-waarschuwings meldingen te verzenden](./autoscale-webhook-email.md)
* [REST API automatisch schalen](/rest/api/monitor/autoscalesettings)

