---
title: Geavanceerd automatisch schalen met Azure Virtual Machines
description: Maakt gebruik van Resource Manager-en VM-schaal sets met meerdere regels en profielen, waarmee e-mail berichten worden verzonden en Url's van webhooks kunnen worden aangeroepen met schaal acties.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 26cfdf05847b01d28a8574483acc89cfeced0ffa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717737"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Geavanceerde configuratie voor automatisch schalen met Resource Manager-sjablonen voor VM Scale Sets
U kunt in Virtual Machine Scale Sets schalen en uitschalen op basis van de drempel waarden voor prestatie waarden, een terugkerend schema of een bepaalde datum. U kunt ook e-mail en webhook-meldingen configureren voor schaal acties. In dit scenario ziet u een voor beeld van het configureren van al deze objecten met behulp van een resource manager-sjabloon op een VM-Schaalset.

> [!NOTE]
> Hoewel in dit overzicht de stappen voor VM Scale Sets worden uitgelegd, is dezelfde informatie van toepassing op het automatisch schalen van [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/)en [API Management Services](../../api-management/api-management-key-concepts.md) voor een eenvoudige instelling voor schaal in/uit voor een VM-schaalset op basis van een eenvoudige prestatie metriek, zoals CPU, raadpleegt u de [Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) -en [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) -documenten
>
>

## <a name="walkthrough"></a>Walkthrough
In dit scenario gebruiken we [Azure resource Explorer](https://resources.azure.com/) om de instelling voor automatisch schalen voor een schaalset te configureren en bij te werken. Azure Resource Explorer is een eenvoudige manier om Azure-resources te beheren via Resource Manager-sjablonen. Lees [deze inleiding](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)als u geen ervaring hebt met Azure resource Explorer hulp programma.

1. Implementeer een nieuwe schaalset met een basis instelling voor automatisch schalen. In dit artikel wordt de versie van de Azure Quick Start-Galerie gebruikt, die een Windows-schaalset met een basis sjabloon voor automatisch schalen heeft. Linux-schaal sets werken op dezelfde manier.
2. Nadat de schaalset is gemaakt, gaat u naar de resource voor de schaalset van Azure Resource Explorer. U ziet het volgende onder het knoop punt micro soft. Insights.

    ![Azure Verkenner](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Tijdens het uitvoeren van de sjabloon is een standaard instelling voor automatisch schalen gemaakt met de naam **autoscalewad**. Aan de rechter kant kunt u de volledige definitie van deze instelling voor automatisch schalen weer geven. In dit geval wordt de standaard instelling voor automatisch schalen geleverd met een scale-out-en scale-out-regel op basis van CPU.  

3. U kunt nu meer profielen en regels toevoegen op basis van de planning of specifieke vereisten. We maken een instelling voor automatisch schalen met drie profielen. Zie [Aanbevolen procedures voor automatisch schalen](autoscale-best-practices.md)voor meer informatie over profielen en regels in automatisch schalen.  

    | Profielen & regels | Beschrijving |
    |--- | --- |
    | **Profiel** |**Op basis van prestaties/metriek** |
    | Regel |Service Bus aantal wachtrij berichten > x |
    | Regel |Aantal Service Bus wachtrij berichten < y |
    | Regel |CPU% > n |
    | Regel |CPU% < p |
    | **Profiel** |**Weekdag morgen uur (geen regels)** |
    | **Profiel** |**Dag van product lancering (geen regels)** |

4. Hier volgt een voor beeld van een hypothetisch schaal scenario dat wordt gebruikt voor deze procedure.

   * **Op basis** van de belasting van mijn toepassing die op mijn schaalset wordt gehost, is op de hoogte. *
   * **Grootte van berichten wachtrij** -ik gebruik een service bus wachtrij voor inkomende berichten voor mijn toepassing. Ik gebruik het aantal berichten van de wachtrij en het CPU-percentage en configureer een standaard profiel om een schaal actie te activeren als een van de aantal berichten of CPU de drempel waarde overschrijdt.\*
   * **Tijdstip van de week en dag** : Ik wil een wekelijkse terugkerende tijd van het profiel op basis van de dag met de naam ' weekdag ochtend uur '. Op basis van historische gegevens weet ik dat het beter is om een bepaald aantal VM-exemplaren te hebben om de belasting van mijn toepassing te verwerken tijdens deze periode.\*
   * **Speciale datums** : Ik heb een ' product lancerings profiel ' toegevoegd. Ik plan vooruit voor specifieke datums zodat mijn toepassing gereed is voor het verwerken van de verwerkte Marketing aankondigingen voor belastingen en wanneer we een nieuw product in de toepassing plaatsen.\*
   * *De laatste twee profielen kunnen ook andere regels voor prestatie metrieke waarden bevatten. In dit geval heeft ik besloten geen en te vertrouwen op de standaard regels op basis van de prestatie metriek. Regels zijn optioneel voor de terugkerende en op de datum gebaseerde profielen.*

     De prioriteits volgorde van de profielen en regels van de engine voor automatisch schalen wordt ook vastgelegd in het artikel [Aanbevolen procedures](autoscale-best-practices.md) voor automatisch schalen.
     Voor een lijst met algemene metrische gegevens voor automatisch schalen, verwijzen wij u naar [algemene metrische gegevens voor automatisch schalen](autoscale-common-metrics.md)

5. Zorg ervoor dat u zich in de modus **lezen/schrijven** bevindt in resource Explorer

    ![Autoscalewad, standaard instelling voor automatisch schalen](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klik op Bewerken. **Vervang** het element ' Profiles ' in de instelling voor automatisch schalen door de volgende configuratie:

    ![Scherm afbeelding toont het element Profiles.](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
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
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Voor ondersteunde velden en hun waarden raadpleegt u de [documentatie voor automatisch schalen rest API](/rest/api/monitor/autoscalesettings). De instelling voor automatisch schalen bevat nu de drie eerder beschreven profielen.

7. Bekijk ten slotte de sectie **melding over** automatisch schalen. Met meldingen voor automatisch schalen kunt u drie dingen doen wanneer een uitschalen of in actie wordt geactiveerd.
   - De beheerder en mede beheerders van uw abonnement op de hoogte stellen
   - Een set gebruikers per e-mail verzenden
   - Activeer een webhook-aanroep. Als deze webhook wordt geactiveerd, worden meta gegevens over de voor waarde voor automatisch schalen en de resource voor de schaalset verzonden. Zie [webhook configureren & e-mail meldingen voor automatisch schalen voor](autoscale-webhook-email.md)meer informatie over de payload van de webhook voor automatisch schalen.

   Voeg het volgende toe aan de instelling voor automatisch schalen en vervang uw **meldings** element waarvan de waarde Null is

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Klik op de knop **put** in resource Explorer om de instelling voor automatisch schalen bij te werken.

U hebt een instelling voor automatisch schalen bijgewerkt op een VM-Schaalset die meerdere schaal profielen en schaal meldingen bevat.

## <a name="next-steps"></a>Volgende stappen
Gebruik deze koppelingen voor meer informatie over automatisch schalen.

[Problemen met automatisch schalen oplossen met Virtual Machine Scale Sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Algemene metrische gegevens voor automatisch schalen](autoscale-common-metrics.md)

[Aanbevolen procedures voor het automatisch schalen van Azure](autoscale-best-practices.md)

[Automatisch schalen beheren met Power shell](../powershell-samples.md#create-and-manage-autoscale-settings)

[Automatisch schalen beheren met CLI](../cli-samples.md#autoscale)

[Webhook & e-mail meldingen configureren voor automatisch schalen](autoscale-webhook-email.md)

Naslag informatie over [micro soft. Insights/autoscalesettings-](/azure/templates/microsoft.insights/autoscalesettings) sjablonen