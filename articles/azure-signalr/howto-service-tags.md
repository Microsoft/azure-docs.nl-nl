---
title: Service tags gebruiken
titleSuffix: Azure SignalR Service
description: Service tags gebruiken om uitgaand verkeer naar uw Azure signalerings service toe te staan
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152327"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Service tags gebruiken voor de Azure signalerings service

U kunt [service Tags](../virtual-network/network-security-groups-overview.md#service-tags) voor Azure signalerings service gebruiken bij het configureren van de [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md#network-security-groups). U kunt hiermee de uitgaande netwerk beveiligings regel voor eind punten van de Azure signalerings service definiëren zonder dat er IP-adressen hoeven te worden voorlopig hardcoderen we.

Deze service tags worden beheerd door de Azure signalerings service. U kunt geen eigen servicetag maken of een bestaande wijzigen. Micro soft beheert deze adres voorvoegsels die overeenkomen met het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen.

## <a name="use-service-tag-on-portal"></a>Service label op Portal gebruiken

U kunt uitgaand verkeer naar de Azure signalerings service toestaan door een nieuwe regel voor uitgaand netwerk beveiliging toe te voegen:

1. Ga naar de netwerk beveiligings groep.

1. Klik op het menu instellingen met de naam **uitgaande beveiligings regels**.

1. Klik bovenaan op de knop **+ toevoegen** .

1. Kies **service-tag** onder **bestemming**.

1. Kies **AzureSignalR** onder **bestemmings servicetag**.

1. Vul **443** in als **doel poort bereik**.

    ![Een uitgaande beveiligings regel maken](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Pas andere velden aan op basis van uw behoeften.

1. Klik op **Add**.


## <a name="next-steps"></a>Volgende stappen

- [Netwerk beveiligings groepen: Service Tags](../virtual-network/network-security-groups-overview.md#security-rules)