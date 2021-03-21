---
title: Splunk integreren met behulp van Azure Monitor | Microsoft Docs
description: Meer informatie over het integreren van Azure Active Directory-logboeken met Splunk met behulp van Azure Monitor.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb6a597d4fd58646f56e271cb6027fb46db1e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634223"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Procedure: Azure Active Directory-logboeken integreren met Splunk met behulp van Azure Monitor

In dit artikel leert u hoe u Azure Active Directory (Azure AD)-Logboeken kunt integreren met Splunk met behulp van Azure Monitor. U stuurt de logboeken eerst naar een Azure Event Hub en integreert de Event Hub vervolgens met Splunk.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:

- Een Azure-Event Hub die Azure AD-activiteiten Logboeken bevat. Meer informatie over het [streamen van uw activiteiten logboeken naar een event hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  De [Splunk-invoeg toepassing voor Microsoft Cloud Services](https://splunkbase.splunk.com/app/3110/#/details). 

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory-logboeken integreren 

1. Open uw Splunk-exemplaar en selecteer **gegevens overzicht**.

    ![De knop gegevens overzicht](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecteer het tabblad **Sourcetypes** en selecteer vervolgens **Amal: aadal: audit**

    ![Het tabblad gegevens overzicht Sourcetypes](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    De activiteiten logboeken van Azure AD worden weer gegeven in de volgende afbeelding:

    ![Activiteitenlogboeken](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Als u een invoeg toepassing niet kunt installeren in uw Splunk-exemplaar (bijvoorbeeld als u een proxy gebruikt of wordt uitgevoerd op de Splunk-Cloud), kunt u deze gebeurtenissen door sturen naar de HTTP-gebeurtenis verzamelaar van Splunk. Als u dit wilt doen, gebruikt u deze [Azure-functie](https://github.com/Microsoft/AzureFunctionforSplunkVS), die wordt geactiveerd door nieuwe berichten in de Event hub. 
>

## <a name="next-steps"></a>Volgende stappen

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Auditlogboekenschema interpreteren in Azure Monitor)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Aanmeldingslogboekenschema interpreteren in Azure Monitor)
* [Veelgestelde vragen en bekende problemen](concept-activity-logs-azure-monitor.md#frequently-asked-questions)