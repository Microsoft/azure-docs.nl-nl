---
title: 'Quickstart: Azure Automanage inschakelen voor virtuele machines in de Azure Portal'
description: Meer informatie over het snel inschakelen van automatisch beheer voor virtuele machines op een nieuwe of bestaande virtuele machine in de Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: d00a9c6012da7ad8d1566ef82bce628c7d47e7a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686981"
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

## <a name="enable-automanage-for-a-single-vm"></a>Automanage inschakelen voor één virtuele machine

1. Blader naar de virtuele machine die u wilt inschakelen.

2. Klik op de vermelding voor **auto beheer (preview)** in de inhouds opgave onder **bewerkingen**.

3. Selecteer **aan de slag**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Aan de slag met één VM.":::

4. Kies uw instellingen voor automanage (omgeving, voor keuren, automanage-account) en klik op **inschakelen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Schakel deze optie in op één virtuele machine.":::

## <a name="enable-automanage-for-multiple-vms"></a>Automanage inschakelen voor meerdere Vm's

1. Zoek in de zoek balk naar en selecteer automatisch **beheer – aanbevolen procedures voor Azure-machines**.

2. Selecteer **Op bestaande VM inschakelen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Op bestaande VM inschakelen.":::

3. Ga als volgt te werk op de blade **Machines selecteren**:
    1. Filter de lijst met virtuele machines op uw **Abonnement** en **Resourcegroep**.
    1. Schakel het selectievakje in van elke virtuele machine die u wilt onboarden.
    1. Klik op de knop **Selecteren**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Bestaande virtuele machine selecteren in de lijst met beschikbare virtuele machines.":::

4. Onder **omgeving** selecteert u uw omgevings type: **dev/test** of **productie**. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Omgevingen selecteren.":::

   Klik op **omgevings Details vergelijken** om de verschillen tussen de omgevingen te bekijken.
    1. Selecteer een omgeving in de vervolg keuzelijst: *dev/test* voor testen, *productie* voor productie.
    1. Klik op de knop **OK**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Blader door de productie omgeving.":::

5. Standaard is de voor keur voor **Aanbevolen procedures voor Azure** geselecteerd voor de configuratie voorkeuren. Als u dit wilt wijzigen, maakt u een nieuwe voor keur of selecteert u een bestaande. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Voor keur maken.":::

6. Klik op de knop **Inschakelen**.


## <a name="enable-automanage-for-a-new-vm"></a>Automanage inschakelen voor een nieuwe virtuele machine

Meld u [hier](https://aka.ms/AzureAutomanagePreview) aan bij het Azure-portaal om een nieuwe virtuele machine te maken en Automatisch beheer in te schakelen.

1. Vul de details van uw virtuele machine in op het tabblad **basis beginselen** .

> [!NOTE]
> Controleer de [ondersteunde regio's](automanage-virtual-machines#supported-regions) voor automanage en de [distributies](automanage-linux.md#supported-linux-distributions-and-versions) -en [Windows Server-versies](automanage-windows-server.md#supported-windows-server-versions)die door automanage worden ondersteund.

2. Ga naar het tabblad **beheer** en kies uw **omgeving** voor zelf beheer.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Schakel het tabblad beheer van automanage in.":::

3. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.

4. Wanneer u het bericht ziet dat de validatie is voltooid, selecteert u **maken**.

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
> [Azure automanage voor Vm's-aangepaste configuratie voorkeuren](virtual-machines-custom-preferences.md)
