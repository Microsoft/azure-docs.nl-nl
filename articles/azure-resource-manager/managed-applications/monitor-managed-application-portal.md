---
title: Azure Portal gebruiken om een beheerde app te bewaken
description: Laat zien hoe u de Azure Portal kunt gebruiken om de beschik baarheid en waarschuwingen voor een beheerde toepassing te bewaken.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a02062edd1a940bcc6588ab53457e0af91fedd9a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578270"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Een geïmplementeerd exemplaar van een beheerde toepassing bewaken

Nadat u een beheerde toepassing hebt geïmplementeerd voor uw Azure-abonnement, wilt u mogelijk de status van de toepassing controleren. In dit artikel ziet u de opties in de Azure Portal om de status te controleren. U kunt de beschik baarheid van de resources in uw beheerde toepassing bewaken. U kunt ook waarschuwingen instellen en weer geven.

## <a name="view-resource-health"></a>Resourcestatus weergeven

1. Selecteer uw beheerde toepassings exemplaar.

   ![Beheerde toepassing selecteren](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selecteer **Resource Health**.

   ![Resource status selecteren](./media/monitor-managed-application-portal/select-resource-health.png)

1. Bekijk de beschik baarheid van de resources in uw beheerde toepassing.

   ![Resourcestatus weergeven](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Waarschuwingen weergeven

1. Selecteer **waarschuwingen**.

   ![Waarschuwingen selecteren](./media/monitor-managed-application-portal/select-alerts.png)

1. Als er waarschuwings regels zijn geconfigureerd, wordt er informatie weer gegeven over waarschuwingen die zijn gegenereerd.

   ![Waarschuwingen weergeven](./media/monitor-managed-application-portal/view-alerts.png)

1. Als u waarschuwings regels wilt toevoegen, selecteert u **+ nieuwe waarschuwings regel**.

   ![Waarschuwing maken](./media/monitor-managed-application-portal/create-new-alert.png)

U kunt waarschuwingen maken voor uw beheerde toepassings exemplaar of van de resources in de beheerde toepassing. Zie [overzicht van waarschuwingen in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)voor meer informatie over het maken van waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

* Zie [voorbeeld projecten voor door Azure beheerde toepassingen](sample-projects.md)voor voor beelden van beheerde toepassingen.
* Als u een beheerde toepassing wilt implementeren, raadpleegt u [Service Catalog-app implementeren via Azure Portal](deploy-service-catalog-quickstart.md).