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
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 84764e73ec5b4ada8c204147def310326a3c7bdd
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948422"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Azure Network Watcher-resources verplaatsen tussen regio's

## <a name="moving-the-network-watcher-resource"></a>De Network Watcher resource verplaatsen
De Network Watcher resource vertegenwoordigt de back-end-service voor Network Watcher en wordt volledig beheerd door Azure. Klanten hoeven deze niet te beheren. De Move-bewerking wordt niet ondersteund voor deze resource.

## <a name="moving-child-resources-of-network-watcher"></a>Onderliggende resources van Network Watcher verplaatsen
Het verplaatsen van resources tussen regio's wordt momenteel niet ondersteund voor een onderliggende resource van het `*networkWatcher*` resource type.

## <a name="next-steps"></a>Volgende stappen
* Lees het [Network Watcher overzicht](./network-watcher-monitoring-overview.md)
* Raadpleeg de [Veelgestelde vragen over Network Watcher](./frequently-asked-questions.md)