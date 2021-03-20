---
title: Azure Network Watcher-resources verplaatsen | Microsoft Docs
description: Azure Network Watcher-resources verplaatsen naar verschillende regio's
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
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 4853f485e4424c3c3263a18d27834d0f9ae94918
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019649"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Azure Network Watcher-resources verplaatsen tussen regio's

## <a name="moving-the-network-watcher-resource"></a>De Network Watcher resource verplaatsen
De Network Watcher resource vertegenwoordigt de back-end-service voor Network Watcher en wordt volledig beheerd door Azure. Klanten hoeven deze niet te beheren. De Move-bewerking wordt niet ondersteund voor deze resource.

## <a name="moving-child-resources-of-network-watcher"></a>Onderliggende resources van Network Watcher verplaatsen
Het verplaatsen van resources tussen regio's wordt momenteel niet ondersteund voor een onderliggende resource van het `*networkWatcher*` resource type.

## <a name="next-steps"></a>Volgende stappen
* Lees het [Network Watcher overzicht](./network-watcher-monitoring-overview.md)
* Raadpleeg de [Veelgestelde vragen over Network Watcher](./frequently-asked-questions.md)