---
title: Ondersteuning van beschikbaarheids zone voor App Service omgevingen
description: Meer informatie over het implementeren van uw App Service omgevingen zodat uw apps zone redundant zijn.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624722"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Ondersteuning van beschikbaarheids zone voor App Service omgevingen

App Service omgevingen (ASE) kunnen in Beschikbaarheidszones (AZ) worden geïmplementeerd.  Klanten kunnen een interne load balancer-as (ILB) implementeren in een specifieke AZ binnen een Azure-regio. Als u de ILB-ASE vastmaakt aan een specifieke AZ, worden de resources die worden gebruikt door een ILB ASE vastgemaakt aan de opgegeven AZ of worden ze op een zone redundante manier geïmplementeerd.  

Een ILB-ASE die expliciet in een AZ wordt geïmplementeerd, wordt beschouwd als een zonegebonden-bron, omdat de ILB ASE is vastgemaakt aan een specifieke zone. De volgende ILB ASE-afhankelijkheden worden vastgemaakt aan de opgegeven zone:

- het interne load balancer IP-adres van de ASE
- de reken resources die worden gebruikt door de ASE voor het beheren en uitvoeren van webtoepassingen

De externe bestands opslag voor webtoepassingen die zijn geïmplementeerd op een zonegebonden ILB ASE maakt gebruik van zone redundant Storage (ZRS).

Tenzij de stappen die in dit artikel worden beschreven, worden gevolgd, worden ILB as niet automatisch op een zonegebonden-manier geïmplementeerd. U kunt een extern ASE met een openbaar IP-adres niet vastmaken aan een specifieke beschikbaarheids zone. 

Zonegebonden ILB as kunnen worden gemaakt in een van de volgende regio's:

- Australië - oost
- Canada - midden
- VS - centraal
- VS - oost
- VS - oost 2
- VS-Oost 2 (EUAP)
- Frankrijk - centraal 
- Japan - oost
- Europa - noord
- Europa -west
- Azië - zuidoost
- Verenigd Koninkrijk Zuid
- West US 2

Toepassingen die zijn geïmplementeerd op een zonegebonden ILB ASE blijven actief en verwerken verkeer op die ASE, zelfs als andere zones in dezelfde regio een storing ondergaan.  Het is mogelijk dat niet-runtime-gedrag, inclusief; het schalen van het toepassings service plan, het maken van toepassingen, toepassings configuratie en het publiceren van toepassingen kan nog steeds worden beïnvloed door een storing in andere beschikbaarheids zones. De door de zone vastgemaakte implementatie van een zonegebonden ILB ASE garandeert alleen voortdurende uptime voor al geïmplementeerde toepassingen.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Een App Service Environment implementeren in een beschikbaarheids zone ##

Zonegebonden ILB as moet worden gemaakt met ARM-sjablonen. Zodra een zonegebonden ILB-ASE is gemaakt via een ARM-sjabloon, kan deze worden weer gegeven en ermee worden gecommuniceerd via de Azure Portal en CLI.  Een ARM-sjabloon is alleen nodig voor het eerst maken van een zonegebonden ILB ASE.

De enige wijziging die nodig is in een ARM-sjabloon voor het opgeven van een zonegebonden ILB ASE is de nieuwe *-**zones** _ eigenschap. De eigenschap _*_zones_*_ moet worden ingesteld op de waarde 1, 2 of 3, afhankelijk van de logische beschikbaarheids zone waarnaar de ILB ASE moet worden vastgemaakt.

In het volgende voor beeld van een ARM-sjabloon fragment ziet u de eigenschap nieuwe _*_zones_*_ waarmee wordt aangegeven dat de ILB ASE moet worden vastgemaakt aan zone 2.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Als u uw apps zone redundant wilt maken, moet u twee zonegebonden ILB as implementeren. De twee zonegebonden ILB as moeten zich in afzonderlijke beschikbaarheids zones bevinden. U moet uw apps vervolgens implementeren in elk van de ILB-as. Nadat uw apps zijn gemaakt, moet u een oplossing voor taak verdeling configureren. De aanbevolen oplossing is het implementeren van een [zone redundant Application Gateway](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) upstream van de zonegebonden ILB as. 

## <a name="in-region-data-residency"></a>In regio gegevens locatie ##

Met ILB as geïmplementeerd in een beschikbaarheids zone worden alleen klant gegevens opgeslagen in de regio waarin de zonegebonden ILB-ASE is geïmplementeerd. Zowel de inhoud van de website bestanden als de door de klant verstrekte instellingen en geheimen die zijn opgeslagen in App Service blijven binnen de regio waar de zonegebonden ILB ASE wordt geïmplementeerd.

Klanten zorgen ervoor dat de gegevens locatie van één regio worden uitgevoerd door de stappen te volgen die eerder zijn beschreven in de sectie "een App Service Environment in een beschikbaarheids zone implementeren". Door het configureren van een App Service Environment volgens deze stappen, voldoet een App Service Environment geïmplementeerd in een beschikbaarheids zone met betrekking tot de vereisten voor de locatie van gegevens, waaronder die welke zijn opgegeven in de [Vertrouwenscentrum van Azure](https://azuredatacentermap.azurewebsites.net/).

Klanten kunnen controleren of een App Service Environment op de juiste wijze is geconfigureerd voor het opslaan van gegevens in één regio door de volgende stappen te volgen: 

1. Ga in [resource Explorer](https://resources.azure.com)naar de arm-resource voor de app service environment.  As worden vermeld onder _providers/Microsoft.Web/hostingEnvironments *.
2. Als er een *zone* -eigenschap bestaat in de weer gave van de JSON-syntaxis van de arm en deze een JSON-matrix met één waarde bevat met de waarde ' 1 ', ' 2 ' of ' 3 ', dan wordt de ASE zonally geïmplementeerd en blijven de gegevens van de klant in dezelfde regio.
2. Als een *eigenschap van een zone niet* bestaat, of als de eigenschap niet is opgegeven, is de waarde van het ASE niet zonally geïmplementeerd en worden de klant gegevens niet alleen in dezelfde regio opgeslagen.
