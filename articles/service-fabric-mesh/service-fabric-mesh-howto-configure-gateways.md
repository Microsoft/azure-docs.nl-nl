---
title: Een gateway configureren om aanvragen te routeren
description: Informatie over het configureren van de gateway die inkomend verkeer afhandelt voor uw toepassingen die worden uitgevoerd op Service Fabric net.
author: georgewallace
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 40bba03c35568c013e383c08f0a13f765c1cc8bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625851"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Een gateway bron configureren om aanvragen te routeren

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Een gateway resource wordt gebruikt om binnenkomend verkeer te routeren naar het netwerk dat uw toepassing bevat. Configureer het om regels op te geven waarmee aanvragen worden omgeleid naar specifieke services of eind punten op basis van de structuur van de aanvraag. Zie [Inleiding tot netwerken in service Fabric mesh](service-fabric-mesh-networks-and-gateways.md) voor meer informatie over netwerken en gateways in net. 

Gateway resources moeten worden gedeclareerd als onderdeel van uw implementatie sjabloon (JSON of YAML) en zijn afhankelijk van een netwerk bron. In dit document vindt u een overzicht van de verschillende eigenschappen die kunnen worden ingesteld voor uw gateway en wordt een voor beeld van een gateway configuratie beschreven.

## <a name="options-for-configuring-your-gateway-resource"></a>Opties voor het configureren van uw gateway bron

Omdat de gateway resource fungeert als een brug tussen het netwerk van uw toepassing en het netwerk van de onderliggende infra structuur (het `open` netwerk). U hoeft slechts één te configureren (in de preview-versie van het net-voor beeld is er een limiet van één gateway per app). De declaratie voor de resource bestaat uit twee hoofd onderdelen: de meta gegevens van de bron en de eigenschappen. 

### <a name="gateway-resource-metadata"></a>Meta gegevens van Gateway bron

Een gateway is gedeclareerd met de volgende meta gegevens:
* `apiVersion` -moet worden ingesteld op 2018-09-01-Preview (of later in de toekomst)
* `name` -een teken reeks naam voor deze gateway
* `type` -"Micro soft. ServiceFabricMesh/gateways"
* `location` -moet worden ingesteld op de locatie van uw app/netwerk. doorgaans is dit een verwijzing naar de locatie parameter in uw implementatie
* `dependsOn` -het netwerk waarvoor deze gateway fungeert als ingangs punt voor

Hier ziet u hoe deze wordt weer gegeven in een Azure Resource Manager-implementatie sjabloon (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Gateway-eigenschappen

De sectie eigenschappen wordt gebruikt voor het definiëren van de netwerken waartussen de gateway zich bevindt en de regels voor het routeren van aanvragen. 

#### <a name="source-and-destination-network"></a>Bron-en doelnet netwerk 

Elke gateway vereist a `sourceNetwork` en `destinationNetwork` . Het bron netwerk wordt gedefinieerd als het netwerk van waaruit uw app binnenkomende aanvragen ontvangt. De eigenschap name moet altijd worden ingesteld op open. Het doelnet netwerk is het netwerk waarop de aanvragen zijn gericht. De naam waarde voor dit moet worden ingesteld op de resource naam van het lokale netwerk van uw app (moet volledige verwijzing naar de resource bevatten). Hieronder vindt u een voor beeld van een configuratie in een netwerk met de naam ' myNetwork '.

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regels 

Een gateway kan meerdere routerings regels hebben die bepalen hoe binnenkomend verkeer wordt verwerkt. Een routerings regel definieert de relatie tussen de luisterende poort en het doel eindpunt voor een bepaalde toepassing. Voor TCP-routerings regels is er een 1:1-toewijzing tussen poort: eind punt. Voor HTTP-routerings regels kunt u complexere routerings regels instellen om het pad van de aanvraag en eventueel headers te controleren om te bepalen hoe de aanvraag wordt doorgestuurd. 

Routerings regels worden opgegeven per poort. Elke ingangs poort heeft een eigen matrix met regels in de sectie eigenschappen van uw gateway configuratie. 

#### <a name="tcp-routing-rules"></a>TCP-routerings regels 

Een TCP-routerings regel bestaat uit de volgende eigenschappen: 
* `name` -verwijzing naar de regel die een wille keurige teken reeks van uw keuze kan zijn 
* `port` -poort voor het Luis teren naar binnenkomende aanvragen 
* `destination` -eindpunt specificatie met `applicationName` , `serviceName` en, `endpointName` voor waar de aanvragen moeten worden doorgestuurd

Hier volgt een voor beeld van een TCP-routerings regel:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP-routerings regels 

Een HTTP-routerings regel bestaat uit de volgende eigenschappen: 
* `name` -verwijzing naar de regel die een wille keurige teken reeks van uw keuze kan zijn 
* `port` -poort voor het Luis teren naar binnenkomende aanvragen 
* `hosts` -een matrix met beleids regels die van toepassing zijn op aanvragen die afkomstig zijn van de verschillende ' hosts ' op de hierboven opgegeven poort. Hosts zijn de set toepassingen en services die in het netwerk kunnen worden uitgevoerd en die inkomende aanvragen kan verwerken, dat wil zeggen een web-app. Hostbeleid worden in de aangegeven volg orde geïnterpreteerd. u moet het volgende in aflopende mate van specificiteit maken
    * `name` -de DNS-naam van de host waarvoor de volgende routerings regels zijn opgegeven. Door ' * ' te gebruiken, worden er routerings regels voor alle hosts gemaakt.
    * `routes` -een matrix met beleids regels voor deze specifieke host
        * `match` -specificatie van de structuur van de binnenkomende aanvraag voor deze regel die moet worden toegepast, op basis van een `path`
            * `path` -bevat een `value` (binnenkomende URI), `rewrite` (hoe u de aanvraag wilt door sturen) en een (op `type` dit moment kan alleen ' prefix ' zijn)
            * `header` -is een optionele matrix met koptekst waarden die overeenkomen in de koptekst van de aanvraag, wanneer de aanvraag overeenkomt met de padspecificatie (hierboven).
              * elke vermelding bevat `name` (teken reeks naam van de header die moet worden vergeleken), `value` (teken reeks waarde van de koptekst in de aanvraag) en een `type` (op dit moment kan alleen ' exact ' zijn)
        * `destination` -Als de aanvraag overeenkomt, wordt deze doorgestuurd naar deze bestemming, die wordt opgegeven met behulp van een `applicationName` , `serviceName` en `endpointName`

Hier volgt een voor beeld van een HTTP-routerings regel die van toepassing zou zijn op aanvragen die afkomstig zijn van poort 80, naar alle hosts die worden aangeboden door apps in dit netwerk. Als de aanvraag-URL een structuur heeft die overeenkomt met de padspecificatie, dat wil zeggen, `<IPAddress>:80/pickme/<requestContent>` wordt deze omgeleid naar het `myListener` eind punt.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Voorbeeld configuratie voor een gateway bron 

Hier ziet u hoe een volledige gateway bron configuratie eruitziet (dit is gebaseerd op het voor beeld van de ingang in de mesh-voor [beelden opslag plaats](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Deze gateway is geconfigureerd voor een Linux-toepassing, ' meshAppLinux ', die bestaat uit ten minste twee services, ' helloWorldService ' en ' counterService ', die luistert op poort 80. Afhankelijk van de URL-structuur van de binnenkomende aanvraag, stuurt de aanvraag door naar een van deze services. 
* " \<IPAddress> : 80/helloWorld/ \<request\> " zou ertoe leiden dat een aanvraag wordt doorgestuurd naar de "helloWorldListener" in de helloWorldService. 
* " \<IPAddress> : 80/Counter/ \<request\> " zou ertoe leiden dat een aanvraag wordt doorgestuurd naar de "counterListener" in de counterService. 

## <a name="next-steps"></a>Volgende stappen
* Het voor [beeld](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) van de ingang implementeren om gateways in actie te zien
