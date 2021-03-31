---
title: Zelfstudie - Een back-up van meerdere virtuele Azure-machines maken
description: In deze zelfstudie leert u hoe u een Recovery Services-kluis maakt, een back-upbeleid definieert en een back-up van meerdere virtuele machines tegelijk maakt.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a9517ffc1e37d50f7c0e57b9ed53fb8bcf55fd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89180572"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Azure Portal gebruiken voor het maken van een back-up van meerdere virtuele machines

Wanneer u een back-up van gegevens in Azure maakt, kunt u die gegevens opslaan in een Azure-resource, een Recovery Services-kluis genaamd. De Recovery Services-kluisresource is beschikbaar via het menu Instellingen van de meeste Azure-services. De integratie van de Recovery Services-kluis in het menu Instellingen van de meeste Azure-services heeft als voordeel dat u heel eenvoudig een back-up van gegevens kunt maken. Het is omslachtig om afzonderlijk met elke database of virtuele machine in uw bedrijf te werken. Wat als u een back-up wilt uitvoeren van de gegevens voor alle virtuele machines op één afdeling of op één locatie? Als u een back-upbeleid maakt en dat beleid toepast op de gewenste virtuele machines, kunt u eenvoudig een back-up maken van meerdere virtuele machines. In deze zelfstudie wordt het volgende uitgelegd:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * Een back-upbeleid definiëren
> * Het back-upbeleid toepassen voor de beveiliging van meerdere virtuele machines
> * Een on-demand back-uptaak activeren voor de beveiligde virtuele machines

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De Recovery Services-kluis bevat de back-upgegevens en het back-upbeleid dat wordt toegepast op de beveiligde virtuele machines. Het maken van back-ups van virtuele machines is een lokaal proces. Het is niet mogelijk van virtuele machines op de ene locatie een back-up in een Recovery Services-kluis op een andere locatie te maken. Daarom moet er voor elke Azure-locatie met virtuele machines waarvoor u een back-up wilt maken, ten minste één Recovery Services-kluis op die locatie bestaan.

1. Selecteer in het menu links **Alle services**.

    ![Alle services selecteren](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. In het dialoogvenster **Alle services** voert u *Recovery Services* in. De lijst met resources wordt gefilterd op basis van uw invoer. In de lijst met resources selecteert u **Recovery Service-kluizen**.

    ![Recovery Services-kluizen invoeren en kiezen](./media/tutorial-backup-vm-at-scale/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

1. Op het dashboard van de **Recovery Services**-kluizen selecteert u **Toevoegen**.

    ![Een Recovery Services-kluis toevoegen](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. In het menu Recovery Services-kluis:

    * *myRecoveryServicesVault* in bij **Naam**.
    * De huidige abonnements-id wordt weergegeven in **Abonnement**. Als u meer abonnementen hebt, kunt u een ander abonnement kiezen voor de nieuwe kluis.
    * Selecteer voor **Resourcegroep** de optie **Bestaande gebruiken** en kies *myResourceGroup*. Als *myResourceGroup* niet bestaat, selecteert u **Nieuwe maken** en typt u *myResourceGroup*.
    * Kies in de vervolgkeuzelijst **Locatie** de optie *Europa - west*.

    ![Waarden van de Recovery Services-kluis](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    Een Recovery Services-kluis moet zich op dezelfde locatie bevinden als de virtuele machines die worden beveiligd. Als u virtuele machines in meerdere regio's hebt, moet u in elke regio een Recovery Services-kluis maken. In deze zelfstudie maakt u een Recovery Services-kluis in *Europa - west* omdat daar *myVM* (de virtuele machine die is gemaakt met de quickstart) is gemaakt.

1. Wanneer u klaar bent om de Recovery Services-kluis te maken, selecteert u **Maken**.

    ![De Recovery Services-kluis maken](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. Het kan even duren voordat de Recovery Services-kluis is gemaakt. Controleer de statusmeldingen rechtsboven in het gebied **Meldingen** in de rechterbovenhoek van de portal. Als de kluis is gemaakt, is deze zichtbaar in de lijst met Recovery Services-kluizen. Als uw kluis niet wordt weergegeven, selecteert u **Vernieuwen**.

     ![De lijst met back-upkluizen vernieuwen](./media/tutorial-backup-vm-at-scale/refresh-button.png)

Wanneer u een Recovery Services-kluis maakt, heeft de kluis standaard geografisch redundante opslag. Ten behoeve van de gegevenstolerantie worden de gegevens door de geografisch redundante opslag meerdere keren tussen twee Azure-regio's gerepliceerd.

## <a name="set-backup-policy-to-protect-vms"></a>Back-upbeleid voor het beveiligen van virtuele machines instellen

Nadat de Recovery Services-kluis is gemaakt, bestaat de volgende stap uit het configureren van de kluis voor het type gegevens en uit het instellen van het back-upbeleid. Het back-upbeleid is de planning voor hoe vaak en wanneer er herstelpunten worden gemaakt. Het beleid bevat ook de bewaartermijn voor de herstelpunten. Voor deze zelfstudie gaan we ervan uit dat uw bedrijf een sportcomplex is met een hotel, stadion en restaurants en contracten en dat u de gegevens op de virtuele machines beveiligt. Met de volgende stappen maakt u een back-upbeleid voor de financiële gegevens.

1. Selecteer **myRecoveryServicesVault** in de lijst met Recovery Services-kluizen om het dashboard ervan te openen.

   ![Het menu Scenario openen](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. Selecteer in het menu van het kluisdashboard de optie **Back-up** om het menu Back-up te openen.

1. Kies in de vervolgkeuzelijst **Waar wordt uw werkbelasting uitgevoerd?** van het menu Doel van de back-up de optie *Azure*. Kies in de vervolgkeuzelijst **Waarvan wilt u een back-up maken?** de optie *Virtuele machine* en selecteer **Back-up**.

    Met deze acties wordt de Recovery Services-kluis voorbereid op interactie met een virtuele machine. Recovery Services-kluizen hebben een standaardbeleid waarmee elke dag een herstelpunt wordt gemaakt en de herstelpunten gedurende 30 dagen worden behouden.

    ![Doel van de back-up](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Als u een nieuw beleid wilt maken in het menu Back-upbeleid, selecteert u in de vervolgkeuzelijst **Back-upbeleid kiezen** de optie *Een nieuw beleid maken*.

    ![Nieuw beleid maken](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. Het deelvenster **Back-upbeleid** wordt geopend. Vul de volgende gegevens in:
   * Bij **Beleidsnaam** typt u *Financiën*. Voer de volgende wijzigingen in voor het backup-beleid:
   * Stel voor **Back-upfrequentie** de tijdzone in op *Central Time*. Omdat het sportcomplex zich in Texas bevindt, wil de eigenaar de tijd op de lokale tijd instellen. Laat de back-upfrequentie ingesteld op elke dag om 03:30 uur.
   * Stel voor **Dagelijks back-uppunt bewaren** de periode in op 90 dagen.
   * Gebruik voor **Wekelijks back-uppunt bewaren** de *Maandag* als herstelpunt en behoudt deze gedurende 52 weken.
   * Voor **Maandelijks back-uppunt bewaren** gebruikt u het herstelpunt van de eerste zondag van de maand en behoudt u deze gedurende 36 maanden.
   * Schakel de optie **Jaarlijks back-uppunt bewaren** uit. De financieel manager wil gegevens niet langer bewaren dan 36 maanden.
   * Selecteer **OK** om het back-upbeleid te maken.

     ![Instellingen voor back-upbeleid](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Nadat het back-upbeleid is gemaakt, koppelt u het beleid aan de virtuele machines.

1. Selecteer onder **Virtuele machines** de optie **Toevoegen**.

     ![Virtuele machines toevoegen](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. Het deelvenster **Virtuele machines selecteren** wordt geopend. Selecteer *myVM* en vervolgens **OK** om het back-upbeleid op de virtuele machines te implementeren.

    Alle virtuele machines die zich op dezelfde locatie bevinden en waaraan nog geen back-upbeleid is gekoppeld, worden weergegeven. *myVMH1* en *myVMR1* zijn geselecteerd om aan het beleid *Financiën* te worden gekoppeld.

    ![VM’s kiezen om te beveiligen](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. Nadat de virtuele machines zijn gekozen, selecteert u **Back-up inschakelen**.

    Wanneer de implementatie is voltooid, ontvangt u een melding dat de implementatie is voltooid.

## <a name="initial-backup"></a>Eerste back-up

U hebt back-ups ingeschakeld voor de Recovery Services-kluizen, maar er is geen eerste back-up gemaakt. Het is een aanbevolen procedure voor herstel na noodgevallen om de eerste back-up te activeren, zodat uw gegevens beveiligd zijn.

Ga als volgt te werk om een on-demand back-uptaak uit te voeren:

1. Selecteer op het kluisdashboard de optie **3** onder **Back-upitems** om het menu Back-upitems te openen.

    ![Back-upitems](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Het menu **Back-upitems** wordt geopend.

1. Selecteer in het menu **Back-upitems** de optie **Virtuele Azure-machine** om de lijst met virtuele machines die aan de kluis zijn gekoppeld te openen.

    ![Lijst met virtuele machines](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. De lijst **Back-upitems** wordt geopend.

    ![Geactiveerde back-uptaak](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. Selecteer in de lijst **Back-upitems** het weglatingsteken **...** om het contextmenu te openen.

1. Selecteer in het contextmenu **Nu back-up maken**.

    ![Contextmenu: selecteer Nu back-up maken](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Het menu Nu back-up maken wordt geopend.

1. Voer in het menu Nu back-up maken de laatste dag in dat het herstelpunt moet worden bewaard en selecteer **OK**.

    ![De laatste dag instellen dat het herstelpunt van Nu back-up maken wordt bewaard](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Implementatiemeldingen laten u weten dat de back-uptaak is geactiveerd en dat u de voortgang van de taak op de pagina Back-uptaken kunt controleren. Afhankelijk van de grootte van de virtuele machine kan het maken van de eerste back-up even duren.

    Wanneer de eerste back-uptaak is voltooid, ziet u de status ervan in het menu Back-uptaak. Door de on-demand back-uptaak is het eerste herstelpunt voor *myVM* gemaakt. Als u een back-up wilt maken van andere virtuele machines, herhaalt u deze stappen voor elke virtuele machine.

    ![Tegel Back-uptaken](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u door wilt gaan met andere zelfstudies, verwijdert u de resources die u in deze zelfstudie hebt gemaakt niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze zelfstudie in de Azure-portal zijn gemaakt.

1. Selecteer op het dashboard **myRecoveryServicesVault** de optie **3** onder **Back-upitems** om het menu Back-upitems te openen.

    ![Het menu Back-upitems openen](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. Selecteer in het menu **Back-upitems** de optie **Virtuele Azure-machine** om de lijst met virtuele machines die aan de kluis zijn gekoppeld te openen.

    ![Lijst met virtuele machines](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    De lijst **Back-upitems** wordt geopend.

1. Selecteer in de lijst **Back-upitems** het weglatingsteken om het contextmenu te openen.

    ![Open in het menu Back-upitems het contextmenu](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. Selecteer in het contextmenu de optie **Back-up stoppen** om het menu Back-up stoppen te openen.

    ![Het menu Back-up stoppen](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. Selecteer in het menu **Back-up stoppen** het bovenste vervolgkeuzemenu en kies **Back-upgegevens verwijderen**.

1. Typ in het dialoogvenster **Typ de naam van het back-upitem** de tekst *myVM*.

1. Zodra het back-upitem is geverifieerd (er wordt een vinkje weergegeven), wordt de knop **Back-up stoppen** ingeschakeld. Selecteer **Back-up stoppen** om het beleid te stoppen en de herstelpunten te verwijderen.

    ![Selecteer Back-up stoppen om de kluis te verwijderen](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >Verwijderde items worden 14 dagen lang bewaard met de status Voorlopig verwijderen. Pas na die periode kan de kluis worden verwijderd. Zie [Een Azure Backup Recovery Services-kluis verwijderen](backup-azure-delete-vault.md) voor meer informatie.

1. Wanneer zich geen items meer in de kluis bevinden, selecteert u **Verwijderen**.

    ![Verwijderen selecteren](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Zodra de kluis is verwijderd, keert u terug naar de lijst met Recovery Services-kluizen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Portal gebruikt voor het volgende:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * De kluis instellen voor het beveiligen van virtuele machines
> * Een aangepast back-up- en bewaarbeleid maken
> * Het beleid toepassen voor de beveiliging van meerdere virtuele machines
> * Een on-demand back-up activeren voor virtuele machines

Ga verder met de volgende zelfstudie in Azure om een virtuele machine te herstellen vanaf schijf.

> [!div class="nextstepaction"]
> [Virtuele machines herstellen met CLI](./tutorial-restore-disk.md)
