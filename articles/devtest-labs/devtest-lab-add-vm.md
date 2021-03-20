---
title: Een VM toevoegen aan een lab in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Portal om een virtuele machine toe te voegen aan een lab in Azure DevTest Labs. U kunt een basis kiezen die een aangepaste installatie kopie of een formule is.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07efcf93b963009494021b90e6c730c4615301ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91297620"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Een VM toevoegen aan een lab in Azure DevTest Labs
Als u [uw eerste virtuele machine](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)al hebt gemaakt, hebt u waarschijnlijk een vooraf geladen [installatie kopie](devtest-lab-configure-marketplace-images.md)van de Marketplace. Als u nu de volgende Vm's wilt toevoegen aan uw Lab, kunt u ook een *basis* kiezen die een [aangepaste installatie kopie](devtest-lab-create-template.md) of een [formule](devtest-lab-manage-formulas.md)is. In deze zelf studie wordt u begeleid bij het gebruik van de Azure Portal om een virtuele machine toe te voegen aan een lab in DevTest Labs.

In dit artikel leest u ook hoe u de artefacten voor een virtuele machine in uw Lab beheert.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Stappen voor het toevoegen van een virtuele machine aan een lab in Azure DevTest Labs
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de sectie **DEVOPS** . Als u * (Star) selecteert naast **DevTest Labs** in de sectie **DEVOPS** . Met deze actie worden **DevTest Labs** toegevoegd aan het navigatie menu aan de linkerkant, zodat u de volgende keer snel toegang kunt krijgen. Vervolgens kunt u **DevTest Labs** selecteren in het navigatie menu aan de linkerkant.

    ![Alle services: Selecteer DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Selecteer in de lijst met Labs het lab waarin u de virtuele machine wilt maken.
2. Op de **overzichts** pagina van het lab selecteert u **+ toevoegen**.

    ![Knop virtuele machine toevoegen](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Selecteer op de pagina **een basis kiezen** een Marketplace-installatie kopie voor de virtuele machine.
1. Voer op het tabblad **basis instellingen** van de pagina **virtuele machine** de volgende acties uit:
    1. Voer in het tekstvak **naam van virtuele machine** een naam in voor de VM. Het tekstvak wordt vooraf ingevuld met een unieke, automatisch gegenereerde naam. De naam komt overeen met de gebruikers naam in uw e-mail adres gevolgd door een uniek nummer van drie cijfers. Met deze functie slaat u de computer naam op en typt u deze telkens wanneer u een computer maakt. Als u wilt, kunt u deze automatisch ingevulde veld overschrijven met de gewenste naam. Als u de automatisch ingevulde naam voor de virtuele machine wilt overschrijven, voert u een naam in het tekstvak **naam** van de VM in.
    2. Voer een **gebruikers naam** in die Administrator bevoegdheden voor de virtuele machine heeft. De **gebruikers naam** voor de machine is vooraf ingevuld met een unieke automatisch gegenereerde naam. De naam komt overeen met de gebruikers naam in uw e-mail adres. Met deze functie bespaart u de tijd om elke keer dat u een nieuwe machine maakt een gebruikers naam te kiezen. U kunt dit automatisch gevulde veld vervangen door een gebruikers naam van uw keuze als u dat wilt. Als u de automatisch ingevulde waarde voor de gebruikers naam wilt overschrijven, voert u een waarde in het tekstvak **gebruikers naam** in. Aan deze gebruiker zijn **beheerders** bevoegdheden voor de virtuele machine verleend.
    3. Als u de eerste virtuele machine in het Lab maakt, voert u een **wacht woord** voor de gebruiker in. Als u dit wacht woord wilt opslaan als een standaard wachtwoord in de Azure-sleutel kluis die is gekoppeld aan het lab, selecteert u **Opslaan als standaard wachtwoord**. Het standaard wachtwoord wordt opgeslagen in de sleutel kluis met de naam: **VmPassword**. Wanneer u een volgende virtuele machine in het lab probeert te maken, wordt **VmPassword** automatisch geselecteerd voor het **wacht woord**. Als u de waarde wilt overschrijven, schakelt u het selectie vakje **een opgeslagen geheim gebruiken** uit en voert u een wacht woord in.

        ![Scherm opname van de pagina ' Lab-resource maken ' met de opties basis instellingen geselecteerd en de waarden voor gebruikers instellingen opgegeven.](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        U kunt ook geheimen opslaan in de sleutel kluis en deze vervolgens gebruiken tijdens het maken van een virtuele machine in het lab. Zie [geheimen opslaan in een sleutel kluis](devtest-lab-store-secrets-in-key-vault.md)voor meer informatie. Als u het wacht woord wilt gebruiken dat is opgeslagen in de sleutel kluis, selecteert u **een opgeslagen geheim gebruiken** en geeft u een sleutel waarde op die overeenkomt met uw geheim (wacht woord).
    4. Selecteer in het gedeelte **meer opties** de optie **grootte wijzigen**. Selecteer een van de vooraf gedefinieerde items waarmee de processor kernen, de RAM-grootte en de grootte van de harde schijf van de virtuele machine worden opgegeven die u wilt maken.
    5. Selecteer **artefacten toevoegen of verwijderen**. Selecteer en configureer de artefacten die u wilt toevoegen aan de basis installatie kopie.
    **Opmerking:** Als u geen ervaring hebt met DevTest Labs of als u artefacten configureert, raadpleegt u de sectie [een bestaand artefact toevoegen aan een VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) en vervolgens keert u terug wanneer u klaar bent.
2. Ga bovenaan naar het tabblad **Geavanceerde instellingen** en voer de volgende acties uit:
    1. Als u het virtuele netwerk wilt wijzigen waarin de VM zich bevindt, selecteert u **VNet wijzigen**.
    2. Als u het subnet wilt wijzigen, selecteert u **subnet wijzigen**.
    3. Geef op of het IP-adres van de virtuele machine **openbaar, privé of gedeeld** is.
    4. Als u de virtuele machine automatisch wilt verwijderen, geeft u de **verval datum en-tijd** op.
    5. Als u de VM wilt claimen door een test gebruiker, selecteert u **Ja** om **deze computer claimbaar te maken** .
    6. Geef het aantal **exemplaren van de virtuele machine** op dat u beschikbaar wilt maken voor uw Lab-gebruikers.

        ![Een basis kiezen](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Selecteer **maken** om de opgegeven virtuele machine toe te voegen aan het lab.

   Op de pagina Lab wordt de status van het maken van de virtuele machine weer gegeven: eerst als **Er wordt gemaakt** en vervolgens **uitgevoerd** nadat de virtuele machine is gestart.

    ![Status van het maken van de virtuele machine](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Een bestaand artefact toevoegen aan een VM
Tijdens het maken van een virtuele machine kunt u bestaande artefacten toevoegen. Elk lab bevat artefacten uit de open bare DevTest Labs-artefact opslagplaats en artefacten die u hebt gemaakt en toegevoegd aan uw eigen artefact opslagplaats.

* Met Azure DevTest Labs *artefacten* kunt u *acties* opgeven die worden uitgevoerd wanneer de virtuele machine wordt ingericht, zoals het uitvoeren van Windows Power shell-scripts, het uitvoeren van bash-opdrachten en het installeren van software.
* Met artefact *parameters* kunt u het artefact voor uw specifieke scenario aanpassen

Zie het artikel, [informatie over het maken van uw eigen artefacten voor gebruik met DevTest Labs](devtest-lab-artifact-author.md), om te ontdekken hoe u artefacten maakt.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met Labs het lab met de virtuele machine waarmee u wilt werken.
1. Selecteer **mijn virtuele machines**.
1. Selecteer de gewenste VM.
1. Selecteer **artefacten beheren**.
1. Selecteer **artefacten Toep assen**.
1. Selecteer in het deel venster **artefacten Toep assen** het artefact dat u aan de virtuele machine wilt toevoegen.
1. Voer in het deel venster **artefact toevoegen** de vereiste parameter waarden in en eventuele optionele para meters die u nodig hebt.
1. Selecteer **toevoegen** om het artefact toe te voegen en terug te keren naar het deel venster **artefacten Toep assen** .
1. Ga door met het toevoegen van artefacten die nodig zijn voor uw VM.
1. Wanneer u uw artefacten hebt toegevoegd, kunt u [de volg orde wijzigen waarin de artefacten worden uitgevoerd](#change-the-order-in-which-artifacts-are-run). U kunt ook terugkeren om [een artefact weer te geven of te wijzigen](#view-or-modify-an-artifact).
1. Wanneer u klaar bent met het toevoegen van artefacten, selecteert u **Toep assen**

## <a name="change-the-order-in-which-artifacts-are-run"></a>De volg orde wijzigen waarin artefacten worden uitgevoerd
De acties van de artefacten worden standaard uitgevoerd in de volg orde waarin ze worden toegevoegd aan de virtuele machine.
De volgende stappen laten zien hoe u de volg orde wijzigt waarin de artefacten worden uitgevoerd.

1. Selecteer boven in het deel venster **artefacten Toep assen** de koppeling waarmee het aantal artefacten wordt aangegeven dat aan de virtuele machine is toegevoegd.

    ![Aantal artefacten dat aan de virtuele machine is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sleep in het deel venster **geselecteerde artefacten** de artefacten naar de gewenste volg orde. **Opmerking:** Als u het artefact niet kunt slepen, moet u ervoor zorgen dat u vanaf de linkerkant van het artefact sleept.
1. Selecteer **OK** wanneer u gereed bent.

## <a name="view-or-modify-an-artifact"></a>Een artefact weer geven of wijzigen
De volgende stappen laten zien hoe u de para meters van een artefact kunt weer geven of wijzigen:

1. Selecteer boven in het deel venster **artefacten Toep assen** de koppeling waarmee het aantal artefacten wordt aangegeven dat aan de virtuele machine is toegevoegd.

    ![Aantal artefacten dat aan de virtuele machine is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Selecteer in het deel venster **geselecteerde artefacten** het artefact dat u wilt weer geven of bewerken.
1. Breng de gewenste wijzigingen aan in het deel venster **artefact toevoegen** en selecteer **OK** om het deel venster **artefact toevoegen** te sluiten.
1. Selecteer **OK** om het **geselecteerde artefacten** venster te sluiten.

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager sjabloon opslaan
Een Azure Resource Manager sjabloon biedt een declaratieve manier om een Herhaal bare implementatie te definiëren.
In de volgende stappen wordt uitgelegd hoe u de Azure Resource Manager sjabloon opslaat voor de virtuele machine die wordt gemaakt.
Nadat het is opgeslagen, kunt u de Azure Resource Manager-sjabloon gebruiken om [nieuwe vm's te implementeren met Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. Selecteer in het deel venster **virtuele machine** **Azure Resource Manager sjabloon weer geven**.
2. Selecteer in het deel venster **Azure Resource Manager sjabloon weer geven** de sjabloon tekst.
3. De geselecteerde tekst naar het klem bord kopiëren.
4. Selecteer **OK** om het **deel venster weer gave Azure Resource Manager sjabloon** te sluiten.
5. Open een teksteditor.
6. Plak de sjabloon tekst op het klem bord.
7. Sla het bestand op voor later gebruik.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
* Zodra de VM is gemaakt, kunt u verbinding maken met de virtuele machine door **verbinding maken** te selecteren in het deel venster van de virtuele machine.
* Meer informatie over het [maken van aangepaste artefacten voor uw DevTest Labs-VM](devtest-lab-artifact-author.md).
* Verken de [DevTest Labs Azure Resource Manager-sjabloon galerie voor Quick](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)start.
