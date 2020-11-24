---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555497"
---
 Azure Machine Learning compute-clusters ondersteunen ook [beheerde identiteiten](../articles/active-directory/managed-identities-azure-resources/overview.md) voor het verifiëren van toegang tot Azure-bronnen zonder referenties in uw code op te nemen. Er zijn twee typen beheerde identiteit:

* Een door het **systeem toegewezen beheerde identiteit** wordt rechtstreeks ingeschakeld op het Azure machine learning Compute-Cluster. De levens cyclus van een door het systeem toegewezen identiteit is rechtstreeks gebonden aan het berekenings cluster. Als het berekenings cluster wordt verwijderd, worden de referenties en de identiteit in azure AD automatisch door Azure opgeschoond.
* Een door de **gebruiker toegewezen beheerde identiteit** is een zelfstandige Azure-resource die wordt verschaft via een door Azure beheerde identiteits service. U kunt een door de gebruiker toegewezen beheerde identiteit toewijzen aan meerdere resources en deze blijft zo lang als u wilt.