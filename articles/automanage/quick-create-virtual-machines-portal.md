---
title: 'Quickstart: Azure Automanage inschakelen voor virtuele machines in de Azure Portal'
description: Meer informatie over het snel inschakelen van automatisch beheer voor virtuele machines op een nieuwe of bestaande virtuele machine in de Azure Portal.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 7121d83f9401fe985966324afe6a61cf8396b2bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534070"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Quickstart: Azure Automanage inschakelen voor virtuele machines in de Azure Portal

Ga aan de slag met Azure Automanage voor virtuele machines door de Azure Portal te gebruiken om automatisch beheer in te schakelen op een nieuwe of bestaande virtuele machine.


## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.

> [!IMPORTANT]
> U moet de rol **Inzender** voor de resourcegroep met uw VM's hebben om Automanage in te schakelen met een bestaand account voor Automanage. Als u Automatisch beheer inschakelt met een nieuw account voor Automatisch beheer, hebt u de volgende machtigingen nodig: De rol van **Eigenaar** of **Inzender** en de rollen **Beheerders voor gebruikerstoegang** in uw abonnement.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Automanage inschakelen voor één VM

1. Blader naar de virtuele machine die u wilt inschakelen.

2. Klik op de **vermelding Automanage (Preview)** in de inhoudsopgave onder **Bewerkingen**.

3. Selecteer **Aan de slag.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Eén VM aan de slag.":::

4. Kies uw Instellingen voor automatisch beheren (Omgeving, Voorkeuren, Account automatisch beheren) en druk op **Inschakelen.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Inschakelen op één VM.":::

## <a name="enable-automanage-for-multiple-vms"></a>Automatischmanage inschakelen voor meerdere VM's

1. Zoek en selecteer in de zoekbalk **Automanage – Best practices voor Azure-machines.**

2. Selecteer **Op bestaande VM inschakelen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Op bestaande VM inschakelen.":::

3. Ga als volgt te werk op de blade **Machines selecteren**:
    1. Filter de lijst met virtuele machines op uw **Abonnement** en **Resourcegroep**.
    1. Schakel het selectievakje in van elke virtuele machine die u wilt onboarden.
    1. Klik op de knop **Selecteren**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Bestaande virtuele machine selecteren in de lijst met beschikbare virtuele machines.":::

4. Selecteer **onder Omgeving** uw omgevingstype: **Dev/Test** of **Production.** 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Selecteer omgevingen.":::

   Klik **op Omgevingsdetails** vergelijken om de verschillen tussen de omgevingen te bekijken.
    1. Selecteer een omgeving in de vervolgkeuzelijn: *Dev/Test* voor testen, *Productie* voor productie.
    1. Klik op de knop **OK**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Blader door de productieomgeving.":::

5. Standaard wordt de azure **Best Practices-voorkeur** geselecteerd voor de configuratievoorkeuren. Als u dit wilt wijzigen, maakt u een nieuwe voorkeur of selecteert u een bestaande. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Voorkeur maken.":::

6. Klik op de knop **Inschakelen**.


## <a name="enable-automanage-for-a-new-vm"></a>Automatischmanage inschakelen voor een nieuwe VM

Meld u [hier](https://aka.ms/AzureAutomanagePreview) aan bij het Azure-portaal om een nieuwe virtuele machine te maken en Automatisch beheer in te schakelen.

1. Vul op het **tabblad Basisinformatie** uw VM-gegevens in.

> [!NOTE]
> Controleer de ondersteunde [regio's Automanage](automanage-virtual-machines.md#supported-regions) en de door Automanage ondersteunde [Linux-distributies](automanage-linux.md#supported-linux-distributions-and-versions) en [Windows Server-versies.](automanage-windows-server.md#supported-windows-server-versions)

2. Blader naar het **tabblad Beheer** en kies **uw Automanage Environment.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Schakel Automatisch beheer in op het tabblad Beheer.":::

3. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.

4. Wanneer u het bericht ziet dat de validatie is geslaagd, selecteert **u Maken.**

## <a name="disable-automanage-for-vms"></a>Automanage uitschakelen voor virtuele machines

Stop snel het gebruik van Azure Automanage voor virtuele machines door automatisch beheer uit te schakelen.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Automanage uitschakelen op een virtuele machine.":::

1. Ga naar de pagina **Automatisch beheer: aanbevolen procedures voor virtuele Azure-machines**. U vindt daar een lijst met alle automatisch beheerde virtuele machines.
1. Schakel het selectievakje in naast de virtuele machine die u wilt uitschakelen.
1. Klik op de knop **Automatisch beheer uitschakelen**.
1. Lees aandachtig door de berichten in het pop-upvenster voordat u akkoord gaat met het **uitschakelen**.


## <a name="clean-up-resources"></a>Resources opschonen

Als u een nieuwe resourcegroep hebt gemaakt om Azure Automanage te proberen voor virtuele machines en deze niet meer nodig hebt, kunt u de resourcegroep verwijderen. Als u de groep verwijdert, worden ook de virtuele machines en alle resources in de resourcegroep verwijderd.

Azure Automanage maakt standaard resourcegroepen voor het opslaan van resources. Controleer de resourcegroepen met de naamconventie 'DefaultResourceGroupRegionName' en 'AzureBackupRGRegionName' om alle resources op te schonen.

1. Selecteer de **Resourcegroep**.
1. Selecteer **Verwijderen** op de pagina van de resourcegroep.
1. Bevestig de naam van de resourcegroep als daar om wordt gevraagd. Selecteer vervolgens **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Azure Automanage ingeschakeld voor virtuele machines.

Ontdek hoe u aangepaste voorkeuren kunt maken en toepassen wanneer u automatisch beheer op uw virtuele machine inschakelt.

> [!div class="nextstepaction"]
> [Azure Automanage voor VM's - Aangepaste configuratievoorkeuren](virtual-machines-custom-preferences.md)
