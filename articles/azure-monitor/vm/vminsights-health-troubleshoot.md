---
title: Problemen met VM Insights-gast status oplossen (preview-versie)
description: Hierin worden de stappen beschreven die u kunt uitvoeren wanneer u problemen ondervindt met de status van de VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932774"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Problemen met VM Insights-gast status oplossen (preview-versie)
In dit artikel worden de stappen beschreven die u kunt uitvoeren wanneer u problemen ondervindt met de status van de VM Insights.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>Er wordt nog steeds een bericht voor upgrade beschikbaar weer gegeven na de upgrade van de gast status 

- Controleer of de virtuele machine wordt uitgevoerd in het wereld wijde Azure. Arc ingeschakelde servers worden nog niet ondersteund.
- Controleer of de regio en de versie van het besturings systeem van de virtuele machine worden ondersteund, zoals beschreven in [Azure monitor voor VM's gast status inschakelen (preview)](vminsights-health-enable.md).
- Controleer of de gast status uitbreiding is geïnstalleerd met een afsluit code van 0.
- Controleer of de uitbrei ding voor de Azure Monitor agent is geïnstalleerd.
- Controleer of de door het systeem toegewezen beheerde identiteit is ingeschakeld voor de virtuele machine.
- Controleer of er geen door de gebruiker toegewezen beheerde identiteiten zijn opgegeven voor de virtuele machine.
- Controleren op virtuele Windows-machines die een land instelling voor *Amerikaans Engels* is. Lokalisatie wordt momenteel niet ondersteund door Azure Monitor agent.
- Controleer of de virtuele machine geen gebruik maakt van de netwerk proxy. Azure Monitor-agent biedt momenteel geen ondersteuning voor proxy's.
- Controleer of de agent voor de status uitbreiding zonder fouten is gestart. Als de agent niet kan worden gestart, is de status van de agent mogelijk beschadigd. Verwijder de inhoud van de map Agent State en start de agent opnieuw.
  - Voor Linux: daemon is *vmGuestHealthAgent*. De status map is */var/opt/vmGuestHealthAgent/**
  - Voor Windows: de service is de *VM-gast status agent*. De status map _is \\ * %ProgramData%\Microsoft\VMGuestHealthAgent_.
- Controleer of de Azure Monitor-agent verbinding heeft met het netwerk. 
  - Probeer vanaf de virtuele machine ping _<region> . handler.Control.monitor.Azure.com_ uit te voeren. Bijvoorbeeld, voor een virtuele machine in Europa West, een poging om _westeurope.handler.Control.monitor.Azure.com:443_ te pingen.
- Controleer of de virtuele machine een koppeling heeft met een regel voor het verzamelen van gegevens in dezelfde regio als de Log Analytics-werk ruimte.
  -  Raadpleeg voor het maken van een **gegevens verzamelings regel (DCR)** in [inschakelen Azure monitor voor VM's gast status (preview)](vminsights-health-enable.md) om ervoor te zorgen dat de structuur van de DCR juist is. Let met name op de aanwezigheid van de sectie *Performance Counters* -gegevens bron die is ingesteld op voor beelden van drie tellers en aanwezigheid van de sectie *inputDataSources* in de configuratie van de status extensie om items naar de uitbrei ding te verzenden.
-  Controleer de virtuele machine op fouten met betrekking tot de status van gast uitbreidingen.
   -  Voor Linux: Raadpleeg de logboeken op _/var/log/Azure/Microsoft.Azure.monitor.VirtualMachines.GuestHealthLinuxAgent/*. log_.
   -  Voor Windows: Controleer de logboeken op de _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.monitor.VirtualMachines.GuestHealthWindowsAgent- \{ extensie versie} \* . log_.
-  Controleer de virtuele machine op Azure Monitor agent fouten.
   -  Voor Linux: Raadpleeg de logboeken op _/var/log/mdsd. *_.
   -  Voor Windows: Raadpleeg de logboeken op _C:\WindowsAzure\Resources \* {vmName}. AMADataStore_.
 



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

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>De status wordt weer gegeven als ' onbekend ' nadat de gast status is ingeschakeld.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Controleren of de prestatie meter items op Windows-knoop punten correct werken 
De status van de gast is afhankelijk van de agent die de prestatie meter items van het knoop punt kan verzamelen. de basisset van bibliotheken voor prestatie meter items kan beschadigd raken en moet mogelijk opnieuw worden opgebouwd. Volg de instructies bij het [hand matig opnieuw samen stellen van de bibliotheek waarden voor prestatie meter](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values) items om de prestatie meters opnieuw samen te stellen.





## <a name="next-steps"></a>Volgende stappen

- [Bekijk een overzicht van de functie gast status van VM Insights](vminsights-health-overview.md)