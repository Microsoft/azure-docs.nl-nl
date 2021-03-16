---
title: Connectiviteits instellingen voor Azure Synapse
description: Een artikel waarin u de connectiviteits instellingen in azure Synapse Analytics kunt configureren
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473084"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Connectiviteits instellingen voor Azure Synapse Analytics

In dit artikel worden connectiviteits instellingen in azure Synapse Analytics beschreven en wordt uitgelegd hoe u deze kunt configureren, indien van toepassing.


## <a name="connection-policy"></a>Verbindings beleid
Het verbindings beleid voor Synapse SQL in azure Synapse Analytics is ingesteld op *standaard*. U kunt dit niet wijzigen in azure Synapse Analytics. U vindt [hier](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy)meer informatie over de werking van Synapse SQL in azure Synapse Analytics. 

## <a name="minimal-tls-version"></a>Minimale TLS-versie
Met Synapse SQL in azure Synapse Analytics worden verbindingen met alle TLS-versies toegestaan. U kunt de minimale TLS-versie voor Synapse SQL in azure Synapse Analytics niet instellen.

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werkruimte](./synapse-workspace-ip-firewall.md) maken
