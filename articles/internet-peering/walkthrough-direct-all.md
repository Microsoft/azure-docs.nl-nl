---
title: Overzicht van Direct peering
titleSuffix: Azure
description: Overzicht van Direct peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592378"
---
# <a name="direct-peering-walkthrough"></a>Overzicht van Direct peering

In deze sectie worden de stappen beschreven die u moet volgen om een directe peering in te stellen en te beheren.

## <a name="create-a-direct-peering"></a>Een directe peering maken
> [!div class="mx-imgBorder"]
> ![Werk stroom voor directe peering en verbindings status](./media/direct-peering.png)

De volgende stappen moeten worden gevolgd om een rechtstreekse peering in te richten:
1. Bekijk het micro soft- [beleid voor peering](https://peering.azurewebsites.net/peering) om inzicht te krijgen in de vereisten voor directe peering.
1. Volg de instructies in [een directe peering maken of wijzigen](howto-direct-powershell.md) om een peering-aanvraag in te dienen.
1. Nadat u een aanvraag voor peering hebt ingediend, neemt micro soft contact met uw geregistreerde e-mail adres op om LOA (Authorization) of voor andere informatie te bieden.
1. Zodra een peering-aanvraag is goedgekeurd, wordt de verbindings status gewijzigd in ProvisioningStarted.
1. U moet het volgende doen:
    1. bekabeling volt ooien volgens de LOA
    1. (optioneel) Voer een koppelings test uit met 169.254.0.0/16
    1. Configureer de BGP-sessie en informeer ons.
1. Micro soft richt zich op de BGP-sessie met alle beleids regels weigeren en end-to-end valideren.
1. Als dit lukt, ontvangt u een melding dat de verbindings status van de peering actief is.
1. Het verkeer wordt dan via de nieuwe peering toegestaan.

Houd er rekening mee dat verbindings statussen niet moeten worden verward met standaard [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) -sessie statussen.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Een verouderde Direct-peering converteren in een Azure-resource
De volgende stappen moeten worden gevolgd om een verouderde directe peering te converteren naar Azure-resource:
1. Volg de instructies in [een verouderde directe peering converteren naar Azure-resource](howto-legacy-direct-powershell.md)
1. Nadat u de conversie aanvraag hebt ingediend, wordt de aanvraag door micro soft gecontroleerd en wordt u indien nodig contact met u opgenomen.
1. Na goed keuring worden uw directe peering met een verbindings status actief weer geven.

## <a name="deprovision-direct-peering"></a>Inrichting direct-peering ongedaan maken
Neem contact op met het [micro soft-peering](mailto:peering@microsoft.com) -team om de inrichting van rechtstreekse peering ongedaan

Wanneer een directe peering is ingesteld voor het ongedaan maken van de inrichting, wordt de verbindings status weer geven als **PendingRemove**

> [!NOTE]
> Als u de Power shell-cmdlet uitvoert om de directe peering te verwijderen wanneer de ConnectionState ProvisioningStarted of ProvisioningCompleted is, mislukt de bewerking.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [vereisten voor het instellen van peering met micro soft](prerequisites.md).
