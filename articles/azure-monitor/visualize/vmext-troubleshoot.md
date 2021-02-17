---
title: Problemen met Azure Log Analytics VM-extensie oplossen
description: Beschrijf de symptomen, oorzaken en oplossingen voor de meest voorkomende problemen met de Log Analytics VM-extensie voor virtuele Windows-en Linux Azure-machines.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: c499633931bd44fed5372f1f05521980f0b61a85
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609296"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Problemen met de Log Analytics VM-extensie in Azure Monitor oplossen
Dit artikel biedt hulp bij het oplossen van problemen die u mogelijk ondervindt met de Log Analytics VM-extensie voor virtuele Windows-en Linux-machines die worden uitgevoerd op Microsoft Azure, en stelt mogelijke oplossingen voor om deze op te lossen.

Als u de status van de uitbrei ding wilt controleren, voert u de volgende stappen uit vanaf de Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Klik in Azure Portal op **Alle services**. Typ **virtuele machines** in de lijst met resources. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Virtuele machines**.
3. Zoek en selecteer deze in de lijst met virtuele machines.
3. Klik op de virtuele machine op **uitbrei dingen**.
4. Controleer in de lijst of de uitbrei ding van de Log Analytics is ingeschakeld.  Voor Linux wordt de agent vermeld als **OMSAgentforLinux** en voor Windows, de agent wordt vermeld als **MicrosoftMonitoringAgent**.

   ![VM-extensie weergave](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klik op de uitbrei ding om details weer te geven. 

   ![Details VM-extensie](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Problemen met Azure Windows VM extension oplossen

Als de VM-extensie van *micro soft Monitoring Agent* niet wordt geïnstalleerd of gerapporteerd, kunt u de volgende stappen uitvoeren om het probleem op te lossen.

1. Controleer of de Azure VM-agent is geïnstalleerd en correct werkt door de stappen in [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)te gebruiken.
   * U kunt ook het logboek bestand van de VM-agent bekijken `C:\WindowsAzure\logs\WaAppAgent.log`
   * Als het logboek niet bestaat, is de VM-agent niet geïnstalleerd.
   * [De Azure VM-agent installeren](../vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Bekijk de logboek bestanden van de VM-extensie van micro soft monitoring agent in `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Zorg ervoor dat de virtuele machine Power shell-scripts kan uitvoeren
4. Controleren of de machtigingen voor C:\Windows\temp niet zijn gewijzigd
5. Bekijk de status van de micro soft monitoring agent door het volgende te typen in een Power shell-venster met verhoogde bevoegdheden op de virtuele machine `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Bekijk de logboek bestanden voor de installatie van micro soft monitoring agent in `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Zie [Troubleshooting Windows Extensions](../../virtual-machines/extensions/oms-windows.md)(Engelstalig) voor meer informatie.

## <a name="troubleshooting-linux-vm-extension"></a>Problemen met Linux VM extension oplossen
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Als de *log Analytics agent voor Linux* VM-extensie niet wordt geïnstalleerd of gerapporteerd, kunt u de volgende stappen uitvoeren om het probleem op te lossen.

1. Als de extensie status *onbekend* is, controleert u of de Azure VM-agent is geïnstalleerd en goed werkt door het logboek bestand van de VM-agent te controleren `/var/log/waagent.log`
   * Als het logboek niet bestaat, is de VM-agent niet geïnstalleerd.
   * [De Azure VM-agent installeren op virtuele Linux-machines](../../virtual-machines/extensions/agent-linux.md#installation)
2. Raadpleeg voor andere slechte statussen de Log Analytics agent voor Linux VM extension logboek bestanden in `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` en `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Als de status van de extensie in orde is, maar de gegevens niet worden geüpload, controleert u de Log Analytics agent voor Linux-logboek bestanden in `/var/opt/microsoft/omsagent/log/omsagent.log`

Zie [Troubleshooting Linux Extensions](../../virtual-machines/extensions/oms-linux.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met azure log Analytics Linux-agent oplossen](../agents/agent-linux-troubleshoot.md)voor aanvullende richt lijnen voor probleem oplossing met betrekking tot de log Analytics-agent voor Linux gehost op computers buiten Azure.