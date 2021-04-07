---
title: Een sjabloon van een leslokaal Lab beheren in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken en beheren van een sjabloon voor een leslokaal Lab in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434274"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Een leslokaal sjabloon maken en beheren in Azure Lab Services
Een sjabloon in een lab is een basiskopie van de virtuele machine waarvan de virtuele machines van alle gebruikers worden gemaakt. Stel de sjabloon voor de virtuele machine zo in dat deze is geconfigureerd met precies wat u de labgebruikers wilt bieden. U kunt een naam en een beschrijving opgeven voor de sjabloon die de gebruikers van het lab kunnen zien. Vervolgens publiceert u de sjabloon om exemplaren van de sjabloon-VM beschikbaar te maken voor uw test gebruikers. Wanneer u een sjabloon publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Het aantal virtuele machines dat in dit proces wordt gemaakt, is hetzelfde als het maximum aantal gebruikers dat in het lab is toegestaan. Dit kunt u instellen in het gebruiksbeleid van het lab. Alle virtuele machines hebben dezelfde configuratie als de sjabloon.

In dit artikel wordt beschreven hoe u een virtuele-machine sjabloon maakt en beheert in een leslokaal Lab van Azure Lab Services. 

> [!NOTE]
> Wanneer u een Lab maakt, wordt de VM van de sjabloon gemaakt, maar niet gestart. U kunt het starten, er verbinding mee maken en eventuele vereiste software voor het lab installeren en vervolgens publiceren. Wanneer u de sjabloon-VM publiceert, wordt deze automatisch afgesloten als u dat nog niet hebt gedaan. 
> 
> Voor sjabloon-VM’s worden **kosten** in rekening gebracht wanneer ze worden uitgevoerd, dus zorg ervoor dat de sjabloon-VM wordt gesloten wanneer deze niet hoeft worden uitgevoerd. 


## <a name="set-or-update-template-title-and-description"></a>Titel en beschrijving van de sjabloon instellen of bijwerken
Gebruik de volgende stappen om de titel en beschrijving voor de eerste keer in te stellen en deze later bij te werken. 

1. Voer op de pagina **sjabloon** de nieuwe **titel** in voor het lab.  
2. Voer de nieuwe **Beschrijving** in voor de sjabloon. Wanneer u de focus uit het tekstvak verplaatst, wordt deze automatisch opgeslagen. 

    ![Naam en beschrijving van sjabloon](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Een sjabloon-VM bijwerken
Gebruik de volgende stappen om een sjabloon-VM bij te werken.  

1. Wacht totdat de sjabloon-VM is gestart en selecteer vervolgens **verbinding maken met sjabloon** op de werk balk om verbinding te maken met de sjabloon-VM en volg de instructies. Als het een Windows-computer is, ziet u een optie voor het downloaden van het RDP-bestand. 
1. Zodra u verbinding hebt gemaakt met de sjabloon en wijzigingen aanbrengt, heeft deze niet langer dezelfde instellingen als de virtuele machines die voor het laatst zijn gepubliceerd naar uw gebruikers. Sjabloon wijzigingen worden pas weer gegeven op de bestaande virtuele machines van uw gebruikers nadat u de publicatie opnieuw hebt gepubliceerd.

    ![Verbinding maken met de sjabloon-VM](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Installeer alle software die de studenten nodig hebben om het lab te doorlopen (bijvoorbeeld Visual Studio, Azure Storage Explorer, enzovoort). 
1. Verbreek de verbinding met de sjabloon-VM (sluit de Extern bureaublad-sessie). 
1. **Stop** de sjabloon-VM door **sjabloon stoppen** te selecteren. 
1. Volg de stappen in de volgende sectie om de bijgewerkte sjabloon-VM te **publiceren** . 

## <a name="publish-the-template-vm"></a>De sjabloon-VM publiceren  
In deze stap publiceert u de sjabloon-VM. Wanneer u de sjabloon-VM publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Alle virtuele machines hebben dezelfde configuratie als de sjabloon.


1. Op de pagina **Sjabloon** selecteert u in de werkbalk **Publiceren**. 

    ![Knop Sjabloon publiceren](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
2. Voer op de pagina **Sjabloon publiceren** het aantal virtuele machines in dat u wilt maken in het lab en selecteer vervolgens **Publiceren**. 

    ![Sjabloon publiceren - aantal VM's](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. U ziet de **publicatiestatus** van de sjabloon op de pagina. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Wacht tot de publicatie is voltooid en schakel vervolgens over naar de pagina **Groep van virtuele machines** door **Virtuele machines** te selecteren in het menu links of door de tegel **Virtuele machines** te selecteren. Controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt de virtuele machines zelf starten vanaf deze pagina of ze laten starten door de studenten. 

    ![Virtuele machines met de status Gestopt](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker, Access Labs](how-to-use-classroom-lab.md)
