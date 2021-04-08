---
title: Bewering bare Vm's maken en beheren in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Portal om een claim bare virtuele machine toe te voegen in Azure DevTest Labs en de processen te zien die moeten worden gevolgd om een virtuele machine op te beweren/te declaimen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270975"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Bewering bare Vm's maken en beheren in Azure DevTest Labs
U voegt een claim bare virtuele machine toe aan een lab op ongeveer dezelfde manier om [een standaard-VM toe te voegen](devtest-lab-add-vm.md) : van een *basis* die een [aangepaste afbeelding](devtest-lab-create-template.md), [formule](devtest-lab-manage-formulas.md)of [Marketplace-installatie kopie](devtest-lab-configure-marketplace-images.md)is. In deze zelf studie wordt u begeleid bij het gebruik van de Azure Portal om een claim bare virtuele machine toe te voegen aan een lab in DevTest Labs en worden de processen weer gegeven die een gebruiker volgt om de virtuele machine te claimen en te claimen.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Stappen om een claim bare virtuele machine toe te voegen aan een lab in Azure DevTest Labs
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
3. Selecteer **maken** om de opgegeven virtuele machine toe te voegen aan het lab.

   Op de pagina Lab wordt de status van het maken van de virtuele machine weer gegeven: eerst als **Er wordt gemaakt** en vervolgens **uitgevoerd** nadat de virtuele machine is gestart.

> [!NOTE]
> Als u Lab-Vm's implementeert via [Azure Resource Manager sjablonen](devtest-lab-create-environment-from-arm.md), kunt u claim bare vm's maken door de eigenschap **allowClaim** in te stellen op waar in de sectie eigenschappen.


## <a name="using-a-claimable-vm"></a>Een claim bare VM gebruiken

Een gebruiker kan een van de volgende stappen uitvoeren om een wille keurige VM te claimen vanuit de lijst met ' claim bare virtuele machines ':

* Klik met de rechter muisknop op een van de virtuele machines in de lijst onder aan het deel venster Overzicht van de lijst met bewering bare Vm's en kies **computer claimen**.

  ![Vraag een specifieke claim bare VM aan.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Kies aan de bovenkant van het deel venster Overzicht de optie **claim elke**. Een wille keurige virtuele machine wordt toegewezen uit de lijst met claim bare Vm's.

  ![Een claim bare VM aanvragen.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Nadat een gebruiker een virtuele machine heeft geclaimd, wordt de machine door DevTest Labs gestart en wordt deze in de lijst met ' mijn virtuele machines ' van de test gebruiker geplaatst. Dit betekent dat de test gebruiker nu eigenaar bevoegdheden heeft op deze computer. De tijd die nodig is voor deze stap kan variëren, afhankelijk van de opstart tijd en eventuele andere aangepaste acties die worden uitgevoerd tijdens de claim gebeurtenis. Zodra de computer is geclaimd, is deze niet meer beschikbaar in de claim bare groep.  

## <a name="unclaim-a-vm"></a>De claim van een virtuele machine ongedaan maken

Wanneer een gebruiker klaar is met een geclaimde virtuele machine en deze beschikbaar wil maken voor iemand anders, kunnen ze de geclaimde VM retour neren naar de lijst met claim bare virtuele machines door een van de volgende stappen uit te voeren:

- Klik in de lijst met ' mijn virtuele machines ' met de rechter muisknop op een van de Vm's in de lijst. u kunt ook het weglatings teken (...) selecteren en vervolgens **unclaim** kiezen.

  ![Maak een virtuele machine in de lijst met virtuele machines op.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Selecteer in de lijst met mijn virtuele machines een virtuele machine om het deel venster beheer te openen en selecteer vervolgens **claim** vrijmaken in de bovenste menu balk.

  ![Maak een virtuele machine in het beheer deel venster van de virtuele machine vrij.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Wanneer een gebruiker een virtuele machine uitgeeft, hebben ze geen eigenaars machtigingen meer voor die specifieke Lab-VM en is deze beschikbaar om te worden geclaimd door een andere Lab-gebruiker in de status die de groep heeft geretourneerd. 

### <a name="transferring-the-data-disk"></a>De gegevens schijf overdragen
Als er aan een claim bare virtuele machine een gegevens schijf is gekoppeld en een gebruiker deze vrijgeeft, blijft de gegevens schijf bij de virtuele machine. Wanneer een andere gebruiker vervolgens die virtuele machine aanmeldt, claimt die nieuwe gebruiker de gegevens schijf en de virtuele machine.

Dit wordt ook wel ' de gegevens schijf overdragen ' genoemd. De gegevens schijf wordt vervolgens beschikbaar in de lijst met **mijn gegevens schijven** van de nieuwe gebruiker zodat deze kan worden beheerd.

![Gegevens schijven inwinnen.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Volgende stappen
* Zodra de VM is gemaakt, kunt u verbinding maken met de virtuele machine door **verbinding maken** te selecteren in het deel venster beheer.
* Verken de [DevTest Labs Azure Resource Manager-sjabloon galerie voor Quick](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)start.
