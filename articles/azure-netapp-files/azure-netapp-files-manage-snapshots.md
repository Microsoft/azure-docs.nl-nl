---
title: Moment opnamen beheren met behulp van Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u moment opnamen maakt, beheert en gebruikt met behulp van Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/20/2021
ms.author: b-juche
ms.openlocfilehash: a18c53d972fbb38dc0b0e557d14b2fbffbff15fa
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174356"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Momentopnamen beheren met behulp van Azure NetApp Files

Azure NetApp Files ondersteunt het maken van moment opnamen op aanvraag en het gebruik van momentopname beleid om het automatisch maken van moment opnamen te plannen. U kunt een moment opname ook herstellen naar een nieuw volume, een enkel bestand herstellen met behulp van een-client of een bestaand volume terugzetten met behulp van een moment opname.

> [!NOTE] 
> Zie [vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)voor overwegingen met betrekking tot het beheer van moment opnamen in replicatie tussen regio's.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Een moment opname op aanvraag maken voor een volume

U kunt op aanvraag volume momentopnamen maken. 

1.  Ga naar het volume waarvoor u een moment opname wilt maken. Klik op **moment opnamen**.

    ![Navigeren naar moment opnamen](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klik op **+ moment opname toevoegen** om een moment opname op aanvraag voor een volume te maken.

    ![Moment opname toevoegen](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Geef in het venster nieuwe moment opname een naam op voor de nieuwe moment opname die u maakt.   

    ![Nieuwe moment opname](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klik op **OK**. 

## <a name="manage-snapshot-policies"></a>Momentopname beleid beheren

U kunt plannen voor volume momentopnamen die automatisch moeten worden uitgevoerd met behulp van het momentopname beleid. U kunt ook een beleid voor moment opnamen naar wens aanpassen of een momentopname beleid verwijderen dat u niet meer nodig hebt.  

### <a name="register-the-feature"></a>De functie registreren

De functie voor het **momentopname beleid** is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie eerst registreren. 

1. De functie registreren: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Controleer de status van de functie registratie: 

    > [!NOTE]
    > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht totdat de status is **geregistreerd** voordat u doorgaat.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
U kunt ook [Azure cli-opdrachten](/cli/azure/feature) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

### <a name="create-a-snapshot-policy"></a>Een momentopname beleid maken 

Met een momentopname beleid kunt u de frequentie voor het maken van moment opnamen opgeven in elk uur, dagelijks, wekelijks of maandelijks. U moet ook het maximum aantal moment opnamen opgeven dat voor het volume moet worden bewaard.  

1.  Klik in de NetApp-account weergave op **beleid voor moment opnamen**.

    ![Navigatie van momentopname beleid](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Stel in het venster snap shot-beleid de beleids status in op **ingeschakeld**. 

3.  Klik op het tabblad **elk uur**, **dagelijks**, **wekelijks** of **maandelijks** om elk uur, dagelijks, wekelijks of maandelijks momentopname beleid te maken. Geef het **aantal moment opnamen op dat moet worden bewaard**.  

    Zie [resource limieten voor Azure NetApp files](azure-netapp-files-resource-limits.md) over het maximum aantal moment opnamen dat is toegestaan voor een volume. 

    In het volgende voor beeld wordt de configuratie van het beleid voor moment opnamen van uren weer gegeven. 

    ![Momentopname beleid per uur](../media/azure-netapp-files/snapshot-policy-hourly.png)

    In het volgende voor beeld wordt de configuratie van het dagelijkse momentopname beleid weer gegeven.

    ![Momentopname beleid dagelijks](../media/azure-netapp-files/snapshot-policy-daily.png)

    In het volgende voor beeld wordt de configuratie van een wekelijks momentopname beleid weer gegeven.

    ![Wekelijks momentopname beleid](../media/azure-netapp-files/snapshot-policy-weekly.png)

    In het volgende voor beeld wordt de configuratie van het maandelijkse momentopname beleid weer gegeven.

    ![Maandelijks momentopname beleid](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Klik op **Opslaan**.  

Als u extra momentopname beleid wilt maken, herhaalt u stap 3.
Het beleid dat u hebt gemaakt, wordt weer gegeven op de pagina snap shot Policy.

Als u wilt dat een volume het momentopname beleid gebruikt, moet u [het beleid Toep assen op het volume](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Een momentopname beleid Toep assen op een volume

Als u wilt dat een volume een momentopname beleid gebruikt dat u hebt gemaakt, moet u het beleid Toep assen op het volume. 

U kunt geen momentopname beleid Toep assen op een doel volume in replicatie tussen regio's.  

1.  Ga naar de pagina **volumes** , klik met de rechter muisknop op het volume waarop u een momentopname beleid wilt Toep assen en selecteer **bewerken**.

    ![Het menu voor volumes met de rechter muisknop](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Selecteer in het venster bewerken onder **momentopname beleid** het beleid dat u wilt gebruiken voor het volume.  Klik op **OK** om het beleid toe te passen.  

    ![Momentopname beleid bewerken](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Een momentopname beleid wijzigen 

U kunt een bestaand momentopname beleid wijzigen om de beleids status te wijzigen, de frequentie van moment opnamen (per uur, dagelijks, wekelijks of maandelijks) of het aantal moment opnamen dat moet worden bewaard.  
 
1.  Klik in de NetApp-account weergave op **beleid voor moment opnamen**.

2.  Klik met de rechter muisknop op het momentopname beleid dat u wilt wijzigen en selecteer vervolgens **bewerken**.

    ![Snelmenu voor momentopname beleid](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Breng de wijzigingen aan in het venster met het momentopname beleid dat wordt weer gegeven en klik vervolgens op **Opslaan**. 

### <a name="delete-a-snapshot-policy"></a>Een momentopname beleid verwijderen 

U kunt een momentopname beleid verwijderen dat u niet meer wilt blijven gebruiken.   

1.  Klik in de NetApp-account weergave op **beleid voor moment opnamen**.

2.  Klik met de rechter muisknop op het momentopname beleid dat u wilt wijzigen en selecteer vervolgens **verwijderen**.

    ![Snelmenu voor momentopname beleid](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Klik op **Ja** om te bevestigen dat u het momentopname beleid wilt verwijderen.   

    ![Verwijdering van momentopname beleid bevestigen](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>De optie pad voor moment opname verbergen bewerken
De optie pad naar moment opname verbergen bepaalt of het pad naar de moment opname van een volume wordt weer gegeven. Tijdens het maken van een [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) -of [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) -volume hebt u de optie om op te geven of het pad naar de moment opname moet worden verborgen. U kunt de optie voor het pad van de moment opname verbergen als dat nodig is.  

> [!NOTE]
> Voor een [doel volume](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) in replicatie tussen verschillende regio's is de optie pad naar moment opname verbergen standaard ingeschakeld en kan de instelling niet worden gewijzigd. 

1. Als u de optie voor het pad van de moment opname verbergen wilt weer geven, selecteert u het volume. In het veld **pad naar moment opname verbergen** wordt weer gegeven of de optie is ingeschakeld.   
    ![Scherm opname van het veld pad naar moment opname verbergen.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Als u de optie pad voor moment opname verbergen wilt bewerken, klikt u op **bewerken** op de pagina volume en wijzigt u de optie **pad naar moment opname verbergen** .   
    ![Scherm afbeelding waarin de optie volume momentopname bewerken wordt beschreven.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Een moment opname herstellen naar een nieuw volume

Op dit moment kunt u een moment opname alleen herstellen naar een nieuw volume. 
1. Selecteer **moment opnamen** op de Blade volume om de lijst met moment opnamen weer te geven. 
2. Klik met de rechter muisknop op de moment opname die u wilt herstellen en selecteer **herstellen naar nieuw volume** in de menu optie.  

    ![Moment opname terugzetten naar nieuw volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Geef in het venster een volume maken informatie op voor het nieuwe volume:  
    * **Naam**   
        Geef de naam op voor het volume dat u wilt maken.  
        
        De naam moet uniek zijn binnen de resourcegroep. De naam moet minstens drie tekens bevatten.  U kunt hiervoor alle alfanumerieke tekens gebruiken.

    * **Quota**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

    ![Herstellen naar nieuw volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Klik op **beoordeling + maken**.  Klik op **Create**.   
    Het nieuwe volume gebruikt hetzelfde protocol dat door de moment opname wordt gebruikt.   
    Het nieuwe volume waarnaar de moment opname wordt teruggezet, wordt weer gegeven op de Blade volumes.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Een bestand herstellen vanuit een moment opname met een client

Als u [de volledige moment opname niet wilt herstellen naar een volume](#restore-a-snapshot-to-a-new-volume), hebt u de optie om een bestand te herstellen vanuit een moment opname met behulp van een client waaraan het volume is gekoppeld.  

Het gekoppelde volume bevat een map met moment opnamen met  `.snapshot` de naam (in NFS-clients) of `~snapshot` (in SMB-clients) die toegankelijk is voor de client. De map met moment opnamen bevat submappen die overeenkomen met de moment opnamen van het volume. Elke submap bevat de bestanden van de moment opname. Als u per ongeluk een bestand verwijdert of overschrijft, kunt u het bestand herstellen naar de bovenliggende map voor lezen/schrijven door het bestand te kopiëren van een submap voor een moment opname naar de map lezen/schrijven. 

U kunt de toegang tot de mappen met moment opnamen beheren met de [optie pad naar moment opname verbergen](#edit-the-hide-snapshot-path-option). Met deze optie bepaalt u of de map moet worden verborgen voor de-clients. Daarom beheert het ook de toegang tot bestanden en mappen in de moment opnamen.  

NFSv 4.1 geeft de `.snapshot` Directory () niet weer `ls -la` . Als de optie pad naar moment opname verbergen echter niet is ingesteld, kunt u nog steeds toegang krijgen tot de `.snapshot` map via nfsv 4.1 met behulp `cd <snapshot-path>` van de opdracht vanaf de client opdracht regel. 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Een bestand herstellen met behulp van een Linux NFS-client 

1. Gebruik de `ls` Linux-opdracht om het bestand weer te geven dat u wilt herstellen vanuit de `.snapshot` map. 

    Bijvoorbeeld:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Gebruik de `cp` opdracht om het bestand te kopiëren naar de bovenliggende map.  

    Bijvoorbeeld: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Een bestand herstellen met behulp van een Windows-client 

1. Als de `~snapshot` map van het volume verborgen is, geeft u [verborgen items](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) in de bovenliggende map weer om weer te geven `~snapshot` .

    ![Verborgen items weergeven](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Ga naar de submap in `~snapshot` om het bestand te vinden dat u wilt herstellen.  Klik met de rechter muisknop op het bestand. Selecteer **Kopiëren**.  

    ![Bestand kopiëren om te herstellen](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Ga terug naar de bovenliggende map. Klik met de rechter muisknop in de bovenliggende map en selecteer `Paste` om het bestand in de map te plakken.

    ![Bestand plakken om te herstellen](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. U kunt ook met de rechter muisknop op de bovenliggende map klikken, **Eigenschappen** selecteren, klikken op het tabblad **vorige versies** om de lijst met moment opnamen weer te geven en vervolgens **herstellen** selecteren om een bestand te herstellen.  

    ![Eigenschappen vorige versies](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>Een volume herstellen met behulp van moment opname terugzetten

Met de functie voor het terugdraaien van momentopnamen kunt u een volume snel herstellen naar de status waarin het zich bevond toen een bepaalde momentopname werd gemaakt. In de meeste gevallen is het herstellen van een volume veel sneller dan het herstellen van afzonderlijke bestanden van een momentopname naar het actieve bestandssysteem. Het is ook meer ruimte-efficiënt vergeleken met het herstellen van een momentopname naar een nieuw volume. 

U kunt de optie volume herstellen vinden in het menu moment opnamen van een volume. Nadat u een moment opname voor de reversie hebt geselecteerd, wordt het volume door Azure NetApp Files teruggezet naar de gegevens en tijds tempels die het bevat toen de geselecteerde moment opname werd gemaakt. 

> [!IMPORTANT]
> Actieve bestandssysteem gegevens en moment opnamen die zijn gemaakt nadat de geselecteerde moment opname is gemaakt, gaan verloren. Met de bewerking voor het terugdraaien van moment opnamen worden *alle* gegevens in het doel volume vervangen door de gegevens in de geselecteerde moment opname. U moet aandacht best Eden aan de inhoud van de moment opname en de aanmaak datum wanneer u een moment opname selecteert. U kunt de bewerking voor het terugdraaien van moment opnamen niet ongedaan maken.

1. Ga naar het menu met **moment opnamen** van een volume.  Klik met de rechter muisknop op de moment opname die u wilt gebruiken voor de terugzet bewerking. Selecteer **volume herstellen**. 

    ![Scherm afbeelding waarmee het snelmenu van een moment opname wordt beschreven](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Typ in het venster volume naar moment opname terugzetten de naam van het volume en klik op **herstellen**.   

    Het volume wordt nu teruggezet naar het tijdstip van de geselecteerde moment opname.

    ![Scherm opname van het venster volume naar moment opname herstellen](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>Moment opnamen verwijderen  

U kunt moment opnamen verwijderen die u niet meer hoeft te hand haven. 

> [!IMPORTANT]
> De bewerking voor het verwijderen van de moment opname kan niet ongedaan worden gemaakt. Een verwijderde moment opname kan niet worden hersteld. 

1. Ga naar het menu met **moment opnamen** van een volume. Klik met de rechter muisknop op de moment opname die u wilt verwijderen. Selecteer **Verwijderen**.

    ![Scherm afbeelding waarmee het snelmenu van een moment opname wordt beschreven](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Bevestig in het venster moment opname verwijderen dat u de moment opname wilt verwijderen door op **Ja** te klikken. 

    ![Scherm opname van het verwijderen van de moment opname](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Volgende stappen

* [Problemen met momentopnamebeleid oplossen](troubleshoot-snapshot-policies.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video over Azure NetApp Files-moment opnamen 101](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Wat is het hulpprogramma Azure Application Consistent Snapshot](azacsnap-introduction.md)
