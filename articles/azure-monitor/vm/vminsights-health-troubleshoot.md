---
title: Problemen met VM Insights-gast status oplossen (preview-versie)
description: Hierin worden de stappen beschreven die u kunt uitvoeren wanneer u problemen ondervindt met de status van de VM Insights.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 6ae0a9e20d6aad7200729419ece333d80e652c3c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703916"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Problemen met VM Insights-gast status oplossen (preview-versie)
In dit artikel worden de stappen beschreven die u kunt uitvoeren wanneer u problemen ondervindt met de status van de VM Insights.

## <a name="error-message-that-no-data-is-available"></a>Fout bericht dat er geen gegevens beschikbaar zijn 

![Geen gegevens](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Controleren of de virtuele machine voldoet aan de configuratie vereisten

1. Controleer of de virtuele machine een virtuele Azure-machine is. Azure Arc voor servers wordt momenteel niet ondersteund.
2. Controleer of op de actieve virtuele machine een [ondersteund besturingssysteem](vminsights-health-enable.md?current-limitations.md) wordt uitgevoerd.
3. Controleer of de virtuele machine is geïnstalleerd in een [ondersteunde regio](vminsights-health-enable.md?current-limitations.md).
4. Controleer of de Log Analytics-werkruimte is geïnstalleerd in een [ondersteunde regio](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Controleren of de virtuele machine correct is uitgevoerd
Controleer of de uitbrei ding van de Azure Monitor agent en de status agent van de gast-VM zijn ingericht op de virtuele machine. Selecteer **uitbrei dingen** in het menu van de virtuele machine in de Azure Portal en zorg ervoor dat de twee agents worden weer gegeven.

![VM-extensies](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Controleren of de door het systeem toegewezen identiteit is ingeschakeld op de virtuele machine
Controleer of de door het systeem toegewezen identiteit is ingeschakeld op de virtuele machine. Selecteer **identiteit** in het menu van de virtuele machine in de Azure Portal. Als door de gebruiker beheerde identiteit is ingeschakeld, kan Azure Monitor agent niet communiceren met de configuratie service, ongeacht de status van de door het systeem beheerde identiteit, en zal de uitbrei ding voor de gast status niet werken.

![Door het systeem toegewezen identiteit](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Regel voor verzamelen van gegevens verifiëren
Controleer of de regel voor het verzamelen van gegevens die de status extensie opgeeft als gegevens bron is gekoppeld aan de virtuele machine.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Fout bericht voor ongeldige aanvraag vanwege onvoldoende machtigingen
Deze fout geeft aan dat de resource provider **micro soft. WorkloadMonitor** niet is geregistreerd in het abonnement. Zie [Azure-resource providers en-typen](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) voor meer informatie over het registreren van deze resource provider. 

![Ongeldige aanvraag](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Volgende stappen

- [Bekijk een overzicht van de functie gast status van VM Insights](vminsights-health-overview.md)