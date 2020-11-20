---
title: Data locatie voor Azure Network Watcher | Microsoft Docs
description: In dit artikel vindt u informatie over het locatie van gegevens voor de Azure Network Watcher-service.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 2e6a92a4d08f1603f480a990ad437a90302a8189
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966085"
---
# <a name="data-residency-for-azure-network-watcher"></a>Data locatie voor Azure Network Watcher
Met uitzonde ring van de service verbindings monitor (preview-versie), worden klant gegevens niet opgeslagen in azure Network Watcher.


## <a name="connection-monitor-preview-data-residency"></a>Verbindings monitor (preview)-gegevens locatie
Met de service verbindings controle (preview) worden klant gegevens opgeslagen. Deze gegevens worden automatisch opgeslagen door Network Watcher in één regio. De verbindings monitor (preview) wordt dus automatisch voldaan aan de vereisten voor de gegevens locatie van de regio, inclusief de vereisten die zijn opgegeven in het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Gegevenslocatie
In Azure is de functie waarmee klant gegevens in één regio kunnen worden opgeslagen, momenteel alleen beschikbaar in de regio Zuidoost-Azië (Singapore) van de Azië en Stille Oceaan geo-en Brazilië-zuid (Sao Paulo-staat) van de geo-geografische regio. Voor alle andere regio's worden klantgegevens opgeslagen in Geo. Zie het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Lees een overzicht van [Network Watcher](./network-watcher-monitoring-overview.md).