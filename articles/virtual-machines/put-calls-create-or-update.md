---
title: PUT-aanroepen om bewerkingen te maken of bij te werken
description: Aanroepen voor bewerkingen voor maken of bijwerken van reken resources
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978557"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>Aanroepen voor het maken of bijwerken van reken bronnen

`Microsoft.Compute` resources bieden geen ondersteuning voor de conventionele definitie van *http-opslag* semantiek. Deze resources gebruiken in plaats daarvan PATCH semantiek voor de werk woorden PUT en PATCH.

Met **Create** Operations worden standaard waarden toegepast wanneer dit van toepassing is. Bron **updates** die worden uitgevoerd via put of patch, zijn echter niet van toepassing op de standaard eigenschappen. **Update** bewerkingen apply strikte patch semantiek Toep assen.

Bijvoorbeeld: de eigenschap schijf `caching` van een virtuele machine wordt standaard ingesteld op `ReadWrite` als de bron een besturingssysteem schijf is.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Voor **Update** bewerkingen wanneer een eigenschap wordt wegge laten of een *Null* -waarde wordt door gegeven, blijft deze echter ongewijzigd en worden er geen waarden meer standaard ingesteld.

Dit is belang rijk wanneer u update bewerkingen naar een resource verzendt met de bedoeling een koppeling te verwijderen. Als die resource een `Microsoft.Compute` resource is, moet de bijbehorende eigenschap die u wilt verwijderen expliciet worden aangeroepen en een waarde worden toegewezen. Hiertoe kunnen gebruikers een lege teken reeks door geven, zoals **' '**. Dit geeft het platform de instructie om die koppeling te verwijderen.

> [!IMPORTANT]
> Er is geen ondersteuning voor ' patching ' voor een matrix element. In plaats daarvan moet de client een PUT-of PATCH-aanvraag uitvoeren met de volledige inhoud van de bijgewerkte matrix. Als u bijvoorbeeld een gegevens schijf wilt loskoppelen van een virtuele machine, voert u een GET-aanvraag uit om het huidige VM-model op te halen, verwijdert u de schijf die moet worden losgekoppeld `properties.storageProfile.dataDisks` en voert u een put-aanvraag uit met de bijgewerkte VM-entiteit.

## <a name="examples"></a>Voorbeelden

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>De payload corrigeren om een proximity-koppeling voor plaatsings groepen te verwijderen

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Onjuiste payloads voor het verwijderen van een proximity-koppeling voor plaatsings groepen

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het maken of bijwerken van aanroepen voor [virtual machines](/rest/api/compute/virtualmachines/createorupdate) en [Virtual Machine Scale sets](/rest/api/compute/virtualmachinescalesets/createorupdate)