---
title: Een Microsoft Azure FXT Edge-eenheid afsluiten
description: Meer informatie over de procedures voor het opstarten en veilig afsluiten van een Azure FXT Edge-knoop punt met behulp van de software van het configuratie scherm van het cluster.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 01c34304ac0e3e7faa42611758d77893e149a2f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218728"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>De hardware van Azure FXT edge-apparaat veilig uitzetten

Hoewel u de fysieke aan/uit-knop kunt gebruiken om over te scha kelen op een afzonderlijk knoop punt, moet u dit niet gebruiken om de eenheid onder normale omstandigheden af te sluiten.

Nadat een Azure FXT Edge-knoop punt wordt gebruikt als onderdeel van een cluster, moet u de software in het configuratie scherm van het cluster gebruiken om de hardware af te sluiten.

> [!NOTE]
> Als u wilt voor komen dat gegevens verloren gaan of beschadigd raken, gebruikt u altijd de software van het configuratie scherm om een Azure FXT Edge-bestand af te sluiten. Gebruik niet de fysieke energie knop voor afsluiten, tenzij u dit doet door de klanten service en ondersteuning van micro soft.
>
> Verbreek in een elektrische nood voeding of gebruik het energie verbrekings mechanisme van uw Data Center.

## <a name="shut-down-a-node-from-the-control-panel"></a>Een knoop punt afsluiten via het configuratie scherm

Volg deze instructies om een Azure FXT Edge-knoop punt veilig uit te scha kelen:

1. Meld u aan bij het configuratie scherm van het cluster. (De instructies in [de pagina's met instellingen openen](fxt-cluster-create.md#open-the-settings-pages))
1. Klik op het tabblad **instellingen** en laad vervolgens de pagina **cluster**  >  **FXT-knoop punten** .
1. Zoek in de lijst met cluster knooppunten het account dat u wilt afsluiten. Klik op de knop aan **/uit** in de kolom **acties** .
1. Wacht even. Het knoop punt wordt afgesloten en Power is uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de status-Led's en andere indica toren in de status van de [Azure FXT Edge-hardwareprovider bewaken](fxt-monitor.md).
* Lees meer over de voeding van Azure FXT Edge-bestanden in [Connect stroom kabels](fxt-network-power.md#connect-power-cables).
