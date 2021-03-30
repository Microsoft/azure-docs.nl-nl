---
title: VMware-Vm's herstellen met Azure Backup Server
description: Gebruik Azure Backup Server (MABS) om VMware-Vm's te herstellen die worden uitgevoerd op een VMware vCenter/ESXi-server.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: b3f61aa828db39aeb11b1ce46a850d9a5b868653
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88263517"
---
# <a name="restore-vmware-virtual-machines"></a>Virtuele VMware-machines herstellen

In dit artikel wordt uitgelegd hoe u Microsoft Azure Backup Server (MABS) gebruikt om de herstel punten voor VMware-VM'S te herstellen. Zie [beveiligde gegevens herstellen](./backup-azure-alternate-dpm-server.md)voor een overzicht van het gebruik van MABS om gegevens te herstellen. In de MABS-beheer console kunt u op twee manieren herstel bare gegevens vinden: zoeken of bladeren. Wanneer u gegevens herstelt, is het mogelijk dat u de gegevens of een virtuele machine niet op dezelfde locatie wilt herstellen. Daarom ondersteunt MABS drie herstel opties voor VMware VM-back-ups:

* **Herstel van de oorspronkelijke locatie (herstellen)** : gebruik herstellen om een beveiligde VM naar de oorspronkelijke locatie te herstellen. U kunt een virtuele machine alleen op de oorspronkelijke locatie herstellen als er geen schijven zijn toegevoegd of verwijderd, omdat de back-up is uitgevoerd. Als er schijven zijn toegevoegd of verwijderd, moet u alternatieve locatie herstel gebruiken.

* **Alternatieve locatie herstel (ALR)** : wanneer de oorspronkelijke VM ontbreekt of als u de oorspronkelijke VM niet wilt verstoren, herstelt u de virtuele machine naar een andere locatie. Als u een virtuele machine naar een alternatieve locatie wilt herstellen, moet u de locatie van een ESXi-host, resource groep, map en de opslag-Data Store en het pad opgeven. Om de herstelde VM te onderscheiden van de oorspronkelijke VM, voegt MABS '-hersteld ' toe aan de naam van de virtuele machine.

* **Herstel van afzonderlijke bestands locaties (ILR)** : als de beveiligde virtuele machine een Windows Server-VM is, kunnen afzonderlijke bestanden/mappen in de virtuele machine worden hersteld met de ILR-functie van MABS. Zie de procedure verderop in dit artikel voor meer informatie over het herstellen van afzonderlijke bestanden.

## <a name="restore-a-recovery-point"></a>Herstel punt herstellen

1. Selecteer in de MABS Administrator-console de optie **herstel weergave**.

2. Blader in het Blader venster en filter om de virtuele machine te vinden die u wilt herstellen. Wanneer u een virtuele machine of map selecteert, worden in het deel venster herstel punten voor de beschik bare herstel punten weer gegeven.

    ![Beschik bare herstel punten](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. In het veld **herstel punten voor** kunt u de kalender en vervolg keuzemenu's gebruiken om een datum te selecteren waarop een herstel punt is gemaakt. De gemarkeerde kalender datums bevatten beschik bare herstel punten.

4. Klik op het lint met hulp middelen op **herstellen** om de **wizard herstellen** te openen.

    ![Wizard herstel, herstel selectie controleren](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Selecteer **volgende** om door te gaan naar het scherm **herstel opties opgeven** .

6. Als u de netwerk bandbreedte regeling wilt inschakelen, selecteert u in het scherm **herstel opties opgeven** de optie **wijzigen**. Als u netwerk beperking wilt uitschakelen, selecteert u **volgende**. Er zijn geen andere opties op dit scherm van de wizard beschikbaar voor virtuele VMware-machines. Als u ervoor kiest de netwerk bandbreedte beperking te wijzigen, selecteert u in het dialoog venster gashendel de optie **beperking van netwerk bandbreedte gebruik inschakelen** om deze in te scha kelen. Wanneer deze functie is ingeschakeld, configureert u de **instellingen** en het **werk schema**.

7. Kies in het scherm **type herstel bewerking selecteren** of u wilt herstellen naar het oorspronkelijke exemplaar of naar een nieuwe locatie. Selecteer vervolgens **Volgende**.

     * Als u **herstellen naar oorspronkelijk exemplaar** kiest, hoeft u geen keuzes meer te maken in de wizard. De gegevens voor de oorspronkelijke instantie worden gebruikt.

     * Als u **herstellen als virtuele machine op een wille keurige host** kiest, geeft u op het scherm **doel opgeven** de gegevens op voor de **ESXi-host, de resource groep** , de map en het **pad**.

      ![Herstel type selecteren](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Controleer uw instellingen op het scherm **samen vatting** en selecteer **herstellen** om het herstel proces te starten. Het scherm **herstel status** toont de voortgang van de herstel bewerking.

## <a name="restore-an-individual-file-from-a-vm"></a>Een afzonderlijk bestand herstellen vanaf een virtuele machine

U kunt afzonderlijke bestanden herstellen vanaf een herstel punt van een beveiligde virtuele machine. Deze functie is alleen beschikbaar voor virtuele machines met Windows Server. Het herstellen van afzonderlijke bestanden is vergelijkbaar met het herstellen van de volledige virtuele machine, behalve dat u naar de VMDK bladert en de gewenste bestanden vindt, voordat u het herstel proces start. Een afzonderlijk bestand herstellen of bestanden selecteren van een Windows Server-VM:

>[!NOTE]
>Het herstellen van een afzonderlijk bestand van een virtuele machine is alleen beschikbaar voor Windows-VM'S en schijf herstel punten.

1. Selecteer in de MABS Administrator-console de optie **herstel** weergave.

2. Blader in het **Blader** venster en filter om de virtuele machine te vinden die u wilt herstellen. Wanneer u een virtuele machine of map selecteert, worden **in het deel venster herstel punten voor** de beschik bare herstel punten weer gegeven.

    ![Het deel venster herstel punten voor](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. In het deel venster **herstel punten voor:** gebruikt u de kalender om de datum te selecteren die de gewenste herstel punten bevat. Afhankelijk van hoe het back-upbeleid is geconfigureerd, kunnen de datums meer dan één herstel punt hebben. Wanneer u de dag hebt geselecteerd waarop het herstel punt is gemaakt, controleert u of u de juiste **herstel tijd** hebt gekozen. Als de geselecteerde datum meerdere herstel punten heeft, kiest u het herstel punt door deze te selecteren in de vervolg keuzelijst herstel tijd. Wanneer u het herstel punt hebt gekozen, wordt de lijst met herstel bare items weer gegeven in het **pad:** deel venster.

4. Als u de bestanden wilt vinden die u wilt herstellen, dubbelklikt u in het deel venster **pad** op het item in de kolom **herstel bare items** om het te openen. Selecteer het bestand, de bestanden of de mappen die u wilt herstellen. Als u meerdere items wilt selecteren, houdt u de **CTRL** -toets ingedrukt terwijl u elk item selecteert. In het deel venster **pad** kunt u zoeken naar de lijst met bestanden of mappen die worden weer gegeven in de kolom **herstelbaar item** . In de **onderstaande Zoek lijst** worden geen submappen doorzocht. Dubbel klik op de map om in submappen te zoeken. Gebruik de knop **omhoog** om van een onderliggende map naar de bovenliggende map te gaan. U kunt meerdere items selecteren (bestanden en mappen), maar ze moeten zich in dezelfde bovenliggende map bevinden. U kunt geen items uit meerdere mappen in dezelfde herstel taak herstellen.

    ![Selectie voor herstel controleren](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Wanneer u de item (s) voor herstel hebt geselecteerd, selecteert u in het lint van de Administrator-console op **herstellen** om de **wizard herstellen** te openen. In de herstel wizard ziet u in het scherm **herstel selectie controleren** de geselecteerde items die moeten worden hersteld.

6. Als u de netwerk bandbreedte regeling wilt inschakelen, selecteert u in het scherm **herstel opties opgeven** de optie **wijzigen**. Als u netwerk beperking wilt uitschakelen, selecteert u **volgende**. Er zijn geen andere opties op dit scherm van de wizard beschikbaar voor virtuele VMware-machines. Als u ervoor kiest de netwerk bandbreedte beperking te wijzigen, selecteert u in het dialoog venster gashendel de optie **beperking van netwerk bandbreedte gebruik inschakelen** om deze in te scha kelen. Wanneer deze functie is ingeschakeld, configureert u de **instellingen** en het **werk schema**.
7. Selecteer **volgende** in het scherm **herstel type selecteren** . U kunt uw bestanden of mappen alleen herstellen naar een netwerkmap.
8. Selecteer op het scherm **doel opgeven** de optie **Bladeren** om een netwerk locatie te zoeken voor uw bestanden of mappen. MABS maakt een map waarin alle herstelde items worden gekopieerd. De mapnaam heeft het voor voegsel, MABS_day-maand-jaar. Wanneer u een locatie voor de herstelde bestanden of map selecteert, worden de gegevens voor die locatie (bestemming, doelpad en beschik bare ruimte) weer gegeven.

    ![Geef de locatie op voor het herstellen van bestanden](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Kies op het scherm **herstel opties opgeven** welke beveiligings instelling u wilt Toep assen. U kunt ervoor kiezen om de beperking van het netwerk bandbreedte gebruik te wijzigen, maar de beperking is standaard uitgeschakeld. Ook **San-herstel** en- **meldingen** zijn niet ingeschakeld.
10. Controleer uw instellingen op het scherm **samen vatting** en selecteer **herstellen** om het herstel proces te starten. Het scherm **herstel status** toont de voortgang van de herstel bewerking.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [gids voor probleem oplossing voor Azure backup server](./backup-azure-mabs-troubleshoot.md)voor informatie over het oplossen van problemen met Azure backup server.
