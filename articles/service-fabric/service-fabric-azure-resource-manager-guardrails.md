---
title: Service Fabric Azure Resource Manager implementatie Guardrails
description: Dit artikel bevat een overzicht van veelvoorkomende fouten bij het implementeren van een Service Fabric cluster via Azure Resource Manager en hoe u dit kunt voor komen.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247821"
---
# <a name="service-fabric-guardrails"></a>Service Fabric Guardrails 
Bij het implementeren van een Service Fabric cluster worden Guardrails geplaatst, waardoor een Azure Resource Manager implementatie in het geval van een ongeldige cluster configuratie mislukt. In de volgende secties vindt u een overzicht van algemene problemen met de cluster configuratie en de stappen die nodig zijn om deze problemen te verhelpen. 

## <a name="durability-mismatch"></a>Duurzaamheid komt niet overeen
### <a name="overview"></a>Overzicht
De duurzaamheids waarde voor een Service Fabric knooppunt type wordt gedefinieerd in twee verschillende secties van een Azure Resource Manager sjabloon. Het gedeelte extensie van de virtuele-machine Schaalset van de resource voor de Schaalset van de virtuele machine en de sectie type knoop punt van de Service Fabric cluster bron. Het is een vereiste dat de duurzaamheids waarde in deze secties overeenkomt, anders mislukt de implementatie van de resource.

De volgende sectie bevat een voor beeld van een duurzaamheid die niet overeenkomt tussen de instelling duurzaamheid van de Schaalset voor virtuele machines en de instelling duurzaamheid van het Service Fabric knooppunt type:  

**Duurzaamheids instelling voor virtuele-machine schaal sets**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Instelling duurzaamheid van Service Fabric knooppunt type** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Foutberichten
* De duurzaamheid van de Schaalset van de virtuele machine komt niet overeen met het huidige niveau van de duurzaamheid van het Service Fabric knooppunt type
* Duurzaamheid van de Schaalset van de virtuele machine komt niet overeen met het doel Service Fabric duurzaamheids niveau van het knooppunt type
* De duurzaamheid van de virtuele-machine Schaalset komt overeen met het huidige Service Fabric duurzaamheids niveau of het doel type duurzaamheids niveau van het Service Fabric knooppunt 

### <a name="mitigation"></a>Oplossing
Het oplossen van een niet-overeenkomend duurzaamheid, dat wordt aangegeven door een van de bovenstaande fout berichten:
1. Werk het duurzaamheids niveau bij in de uitbrei ding voor de virtuele-machine Schaalset of het gedeelte Service Fabric knooppunt type van de Azure Resource Manager sjabloon om ervoor te zorgen dat de waarden overeenkomen.
2. Implementeer de Azure Resource Manager sjabloon opnieuw met de bijgewerkte waarden.


## <a name="seed-node-deletion"></a>Seed-knoop punt verwijderen 
### <a name="overview"></a>Overzicht
Een Service Fabric cluster heeft een eigenschap van een [betrouwbaarheids categorie](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) die wordt gebruikt om te bepalen hoeveel replica's van systeem services worden uitgevoerd op het primaire knooppunt type van het cluster. Het aantal vereiste replica's bepaalt het minimum aantal knoop punten dat moet worden onderhouden in het primaire knooppunt type van het cluster. Als het aantal knoop punten in het primaire knooppunt type onder het vereiste minimum voor de betrouwbaarheids categorie komt, wordt het cluster Insta Biel.  

### <a name="error-messages"></a>Foutberichten 
Er is een bewerking voor het verwijderen van een Seed-knoop punt gedetecteerd en deze wordt geweigerd. 
* Deze bewerking zou ertoe leiden {0} dat er alleen potentiële Seed-knoop punten in het cluster aanwezig blijven, terwijl het {1} Mini maal nodig is.
* {0}Als u de Seed-knoop punten verwijdert, is {1} het cluster niet meer mogelijk vanwege het verlies van het Seed-knooppunt quorum. Het maximum aantal Seed-knoop punten dat tegelijk kan worden verwijderd is {2} .
 
### <a name="mitigation"></a>Oplossing 
Zorg ervoor dat het type van het primaire knoop punt voldoende Virtual Machines heeft voor de betrouw baarheid die is opgegeven op uw cluster. U kunt een virtuele machine niet verwijderen als hiermee de Schaalset voor virtuele machines onder het minimum aantal knoop punten voor de opgegeven betrouwbaarheids categorie wordt ingesteld.
* Als de betrouwbaarheids laag correct is opgegeven, moet u ervoor zorgen dat u over voldoende knoop punten in het primaire knooppunt type beschikt als dat nodig is voor de betrouwbaarheids categorie. 
* Als de betrouwbaarheids laag onjuist is, initieert u een wijziging op de Service Fabric resource om eerst het betrouwbaarheids niveau te verlagen voordat u bewerkingen voor de Schaalset van virtuele machines initieert, en wacht u totdat het is voltooid.
* Als de betrouwbaarheids categorie brons is, volgt u deze [stappen](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) om uw cluster op de juiste wijze te schalen.

## <a name="next-steps"></a>Volgende stappen
* Een cluster maken op Vm's of computers met Windows Server: [service Fabric cluster maken voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op Vm's of computers met Linux: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Problemen oplossen Service Fabric: [richt lijnen voor probleem oplossing](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
