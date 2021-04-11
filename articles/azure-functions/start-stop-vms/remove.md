---
title: Overzicht van Vm's voor starten/stoppen v2 (preview) verwijderen
description: In dit artikel wordt beschreven hoe u de functie Vm's starten/stoppen v2 (preview) verwijdert.
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111638"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Het verwijderen van Vm's voor starten/stoppen v2 (preview-versie)

Nadat u de functie Vm's starten/stoppen v2 (preview) hebt ingeschakeld om de uitvoerings status van uw Azure-Vm's te beheren, kunt u ervoor kiezen om deze niet meer te gebruiken. Het verwijderen van deze functie kan worden uitgevoerd door de resource groep te verwijderen die specifiek is voor het opslaan van de volgende resources ter ondersteuning van Vm's voor starten/stoppen v2 (preview):

- De Azure Functions toepassingen
- Schema's in Azure Logic Apps
- Het Application Insights-exemplaar
- Azure Storage-account

## <a name="delete-the-dedicated-resource-group"></a>De speciale resource groep verwijderen

Als u de resource groep wilt verwijderen, voert u de stappen uit die worden beschreven in het artikel [Azure Resource Manager resource groep en resource verwijderen](../../azure-resource-manager/management/delete-resource-group.md) .

## <a name="next-steps"></a>Volgende stappen

Zie [Deploy start/stop v2](deploy.md) (preview) als u deze functie opnieuw wilt implementeren.