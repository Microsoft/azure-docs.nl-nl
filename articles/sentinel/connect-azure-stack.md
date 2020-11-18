---
title: De virtuele machines van Azure Stack hub onboarden naar de Azure-Sentinel | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de extensie van de virtuele machine voor Azure Monitor, update en configuratie beheer kunt inrichten op virtuele machines met Azure Stack hub en deze kunt volgen met Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 9ac4aa9e93ac296913d7a62e83b182d673015bae
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655966"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Azure Stack hub virtuele machines verbinden met Azure Sentinel




Met Azure Sentinel kunt u uw virtuele machines die worden uitgevoerd op Azure en Azure Stack hub op één plek bewaken. Als u uw Azure Stack-machines aan Azure Sentinel wilt toevoegen, moet u eerst de extensie van de virtuele machine aan uw bestaande virtuele machines met Azure Stack hub. 

Nadat u Azure Stack hub-machines hebt verbonden, kiest u uit een galerie met Dash boards die op basis van uw gegevens Blade inzichten hebben. Deze Dash boards kunnen eenvoudig worden aangepast aan uw behoeften.



## <a name="add-the-virtual-machine-extension"></a>De extensie van de virtuele machine toevoegen 

Voeg de extensie van de virtuele machine **Azure monitor, update en configuratie beheer** toe aan de virtuele machines die worden uitgevoerd op uw Azure stack hub. 

1. Meld u in een nieuw browser tabblad aan bij uw [Azure stack hub-Portal](/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Ga naar de pagina **virtuele machines** en selecteer de virtuele machine die u met Azure Sentinel wilt beveiligen. Zie voor meer informatie over het maken van een virtuele machine op Azure Stack hub [een Windows Server-VM maken met de Azure stack-hub Portal](/azure-stack/user/azure-stack-quick-windows-portal) of [een Linux-Server-VM maken met behulp van de Azure stack hub-Portal](/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecteer **Extensies**. De lijst met virtuele machine-extensies die op deze virtuele machine is geïnstalleerd, wordt weergegeven.
4. Klik op de knop **Add**. De menublade **Nieuwe resource** wordt geopend en toont de lijst met beschikbare extensies van virtuele machines. 
5. Selecteer de uitbrei ding **Azure monitor, update en configuratie beheer** en klik op **maken**. Het venster **extensie configuratie installeren** wordt geopend.

   ![Instellingen voor Azure Monitor-, update-en configuratie beheer](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Als u de **Azure monitor-, update-en configuratie beheer** uitbreiding die wordt vermeld in uw Marketplace niet ziet, neemt u contact op met uw Azure stack hub-operator om deze beschikbaar te maken.

6. In het menu Sentinel van Azure selecteert u **werkruimte instellingen** gevolgd door **Geavanceerd** en kopieert u de **werk ruimte-id** en de **werkruimte sleutel (primaire sleutel)**. 
1. Plak deze in het venster **extensie** van de Azure stack-hub in de aangegeven velden en klik op **OK**.
1. Nadat de installatie van de extensie is voltooid, wordt de status ervan weer gegeven als **inrichting geslaagd**. Het kan een uur duren voordat de virtuele machine wordt weer gegeven in de Azure-Sentinel-Portal.

Zie [Windows-computers verbinden](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)voor meer informatie over het installeren en configureren van de agent voor Windows.

Zie [Probleemoplossing voor Linux-agent voor Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md) voor meer informatie over het oplossen van problemen met agents in Linux.

In de Azure-Sentinel-Portal op Azure, onder **virtual machines**, hebt u een overzicht van alle vm's en computers, samen met hun status. 

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u deze niet meer nodig hebt, kunt u de extensie uit de virtuele machine verwijderen via de Azure Stack hub-Portal.

De extensie verwijderen:

1. Open de **Azure stack hub-Portal**.
2. Ga naar de pagina **Virtuele machines**, selecteer de virtuele machine waarvan u de extensie wilt verwijderen.
3. Selecteer **Extensies**, de extensie **Microsoft.EnterpriseCloud.Monitoring**.
4. Klik op **installatie ongedaan maken** en bevestig uw selectie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- Gegevens streamen van [algemene fout indelings toestellen](connect-common-event-format.md) in azure Sentinel.