---
title: Verbindings monitor maken-ARM-sjabloon
titleSuffix: Azure Network Watcher
description: Meer informatie over het maken van een verbindings monitor met behulp van de ARMClient.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: c3b228d2652d5f7dcf7c6596ee5425b3f5f9a4d8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544000"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Een verbindings monitor maken met de ARM-sjabloon

Meer informatie over het maken van een verbindings monitor voor het bewaken van de communicatie tussen uw resources met behulp van de ARMClient. Het ondersteunt hybride en Azure-Cloud implementaties.


## <a name="before-you-begin"></a>Voordat u begint 

In verbindings monitors die u maakt in verbindings beheer, kunt u on-premises machines en Azure-Vm's als bronnen toevoegen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

De verbindings monitor bevat de volgende entiteiten:

* **Bron van de verbindings monitor** : een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt** – een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn Azure-Vm's, on-premises agents, Url's en Ip's.
* **Test configuratie** : een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u hebt gekozen, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep** : de groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Testen** : de combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

    ![Diagram van een verbindings monitor, waarbij de relatie tussen test groepen en tests wordt gedefinieerd](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Stappen voor het maken van een voor beeld van een ARM-sjabloon

Gebruik de volgende code om een verbindings monitor te maken met behulp van ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Hier volgt de implementatie opdracht:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Beschrijving van eigenschappen

* connectionMonitorName: naam van de bron van de verbindings monitor

* Subabonnement-ID van het abonnement waarvoor u de verbindings monitor wilt maken

* NW-Network Watcher Resource-ID waarin CM wordt gemaakt 

* locatie-regio waarin de verbindings monitor wordt gemaakt

* Eindpunten
    * naam: unieke naam voor elk eind punt
    * resourceId: voor Azure-eind punten verwijst de resource-ID naar de Azure Resource Manager Resource-ID voor virtuele machines. Voor niet-Azure-eind punten verwijst Resource-ID naar de Azure Resource Manager Resource-ID voor de Log Analytics-werk ruimte die is gekoppeld aan niet-Azure-agents.
    * adres: alleen van toepassing als de resource-ID niet is opgegeven of als de resource-ID Log Analytics werk ruimte is. Als dit wordt gebruikt in combi natie met Log Analytics Resource-ID, verwijst dit naar de FQDN van de agent die kan worden gebruikt voor de bewaking. Als u zonder Resource-ID gebruikt, kan dit de URL of het IP-adres van een openbaar eind punt zijn.
    * filter: voor niet-Azure-eind punten gebruikt u filter om agents te selecteren uit Log Analytics werk ruimte die wordt gebruikt voor de bewaking van de bron van de verbindings monitor. Als er geen filters zijn ingesteld, kunnen alle agents die deel uitmaken van de Log Analytics-werk ruimte worden gebruikt voor de bewaking
        * type: Stel type in als agent adres
        * adres: Stel adres in als de FQDN van uw on-premises agent

* Test groepen
    * name: naam van de test groep.
    * testConfigurations-test configuraties op basis waarvan de bron-eind punten verbinding maken met doel eindpunten
    * bronnen: Kies uit de hierboven gemaakte eind punten. Op Azure-bron eindpunten moet Azure Network Watcher-extensie zijn geïnstalleerd en op niet-Azure gebaseerde bron-eind punten moet Log Analytics agent zijn geïnstalleerd. Zie [bewakings agenten installeren](./connection-monitor-overview.md#install-monitoring-agents)voor informatie over het installeren van een agent voor uw bron.
    * doelen: Kies uit de hierboven gemaakte eind punten. U kunt de verbinding met virtuele machines van Azure of een eind punt (een openbaar IP-adres, URL of FQDN) bewaken door ze als bestemming op te geven. In één test groep kunt u Azure-Vm's, Office 365 Url's, Dynamics 365-Url's en aangepaste eind punten toevoegen.
    * uitschakelen: gebruik dit veld om de bewaking uit te scha kelen voor alle bronnen en doelen die door de test groep worden opgegeven.

* Test configuraties
    * naam: naam van de test configuratie.
    * testFrequencySec: Geef op hoe vaak bronnen worden gepingd op het protocol en de poort die u hebt opgegeven. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest. Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste eenmaal in een periode van 30 seconden gecontroleerd.
    * Protocol: u kunt TCP, ICMP, HTTP of HTTPS kiezen. Afhankelijk van het protocol kunt u bepaalde configuraties van specifieke protocollen uitvoeren
    
        * preferHTTPS: Geef op of HTTPS over HTTP moet worden gebruikt wanneer de gebruikte poort niet 80 of 443 is
        * poort: Geef de doel poort van uw keuze op.
        * disableTraceRoute: dit is van toepassing op test configuraties waarvan het protocol TCP of ICMP is. Het stoppen van bronnen van het detecteren van topologie en hop-by-Hop RTT wordt gestopt.
        * methode: dit wordt toegepast op test configuraties waarvan het Protocol HTTP is. Selecteer de HTTP-aanvraag methode: GET of POST
        * pad-Geef para meters op die moeten worden toegevoegd aan de URL
        * validStatusCodes: Kies toepasselijke status codes. Als de antwoord code niet overeenkomt met deze lijst, wordt een diagnostisch bericht weer gegeven
        * requestHeaders: Geef aangepaste aanvraag header teken reeksen op die worden door gegeven aan de bestemming
        
    * successThreshold-u kunt drempels instellen voor de volgende netwerk parameters:
        * checksFailedPercent: Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren met behulp van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol kan het percentage mislukte controles worden vergeleken met het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
        * roundTripTimeMs: Stel de RTT in milliseconden in om te bepalen hoe lang bronnen kunnen worden verbonden met het doel via de test configuratie.

## <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 20 via ARMClient

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het analyseren van bewakings gegevens en het instellen van waarschuwingen](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Meer informatie [over het vaststellen van problemen in uw netwerk](./connection-monitor-overview.md#diagnose-issues-in-your-network)