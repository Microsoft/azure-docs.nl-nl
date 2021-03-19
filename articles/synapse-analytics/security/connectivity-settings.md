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
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587930"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Connectiviteits instellingen voor Azure Synapse Analytics

In dit artikel worden connectiviteits instellingen in azure Synapse Analytics beschreven en wordt uitgelegd hoe u deze kunt configureren, indien van toepassing.


## <a name="connection-policy"></a>Verbindings beleid
Het verbindings beleid voor Synapse SQL in azure Synapse Analytics is ingesteld op *standaard*. U kunt dit niet wijzigen in azure Synapse Analytics. U vindt [hier](../../azure-sql/database/connectivity-architecture.md#connection-policy)meer informatie over de werking van Synapse SQL in azure Synapse Analytics. 

## <a name="minimal-tls-version"></a>Minimale TLS-versie
Met Synapse SQL in azure Synapse Analytics worden verbindingen met alle TLS-versies toegestaan. U kunt de minimale TLS-versie voor Synapse SQL in azure Synapse Analytics niet instellen.

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werkruimte](./synapse-workspace-ip-firewall.md) maken