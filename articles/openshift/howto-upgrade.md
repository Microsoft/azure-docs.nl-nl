---
title: Een Azure Red Hat open Shift-cluster bijwerken
description: Meer informatie over het bijwerken van een Azure Red Hat open Shift-cluster met openshift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720882"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Een Azure Red Hat open Shift (ARO)-cluster bijwerken

Voor een deel van de levens cyclus van het ARO-cluster moeten periodieke upgrades worden uitgevoerd naar de nieuwste open Shift-versie. Het is belang rijk dat u de nieuwste beveiligings releases toepast of een upgrade uitvoert om de nieuwste functies op te halen. In dit artikel wordt beschreven hoe u de upgrade van alle onderdelen in een open Shift-cluster uitvoert met behulp van de open Shift-webconsole.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u de Azure CLI-versie 2.0.65 van later uitvoeren. Voer `az --version` uit om uw huidige versie te vinden. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren](/cli/azure/install-azure-cli).

In dit artikel wordt ervan uitgegaan dat u toegang hebt tot een bestaand Azure Red Hat open Shift-cluster als gebruiker met `admin` bevoegdheden.

## <a name="check-for-available-aro-cluster-upgrades"></a>Controleren op beschik bare ARO-cluster upgrades

Selecteer in de web-console open Shift de optie **beheer**  >  **cluster instellingen** en open het tabblad **Details** .

Als de **update status** voor uw cluster updates bevat die **beschikbaar zijn**, kunt u uw cluster bijwerken.

## <a name="upgrade-your-aro-cluster"></a>Uw ARO-cluster upgraden

Stel op de webconsole in de vorige stap het **kanaal** in op het juiste kanaal voor de versie die u wilt bijwerken, zoals `stable-4.5` .

Selecteer een versie om bij te werken naar en selecteert u **bijwerken**. U ziet de status wijziging van de update in: `Update to <product-version> in progress` . U kunt de voortgang van de cluster update controleren door de voortgangs balken voor de Opera tors en knoop punten te bekijken.

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het upgraden van een ARO-cluster met behulp van de OC CLI](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Meer informatie over beschik bare open Shift container platform advies en updates vindt u in de [sectie errata](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) van de klanten Portal.
