---
title: Back-ups maken van Azure VMware-oplossing Vm's met Azure Backup Server
description: Configureer uw Azure VMware-oplossings omgeving voor het maken van back-ups van virtuele machines met behulp van Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 163065556b7dbc979d68613744ea827c209d9fda
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618913"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Back-ups maken van Azure VMware-oplossing Vm's met Azure Backup Server

In dit artikel gaan we een back-up maken van virtuele VMware-machines (Vm's) die worden uitgevoerd op de Azure VMware-oplossing met Azure Backup Server. Eerst moet [u de Microsoft Azure backup server instellen voor de Azure VMware-oplossing](set-up-backup-server-for-azure-vmware-solution.md).

Vervolgens worden alle benodigde procedures door lopen voor het volgende:

> [!div class="checklist"] 
> * Stel een beveiligd kanaal zodanig in dat Azure Backup Server kan communiceren met VMware-servers via HTTPS. 
> * Voeg de account referenties toe aan Azure Backup Server. 
> * Voeg de vCenter toe aan Azure Backup Server. 
> * Stel een beveiligings groep in die de VMware-Vm's bevat waarvan u een back-up wilt maken, geef back-upinstellingen op en plan de back-up.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Een beveiligde verbinding maken met de vCenter-Server

Azure Backup Server communiceert standaard met VMware-servers via HTTPS. Als u de HTTPS-verbinding wilt instellen, downloadt u het CA-certificaat (VMware Certificate Authority) en importeert u het op de Azure Backup Server.

### <a name="set-up-the-certificate"></a>Het certificaat instellen

1. Voer in de browser op de Azure Backup Server machine de URL van de vSphere-webclient in.

   > [!NOTE] 
   > Als de pagina **aan** de slag met VMware niet wordt weer gegeven, controleert u de instellingen voor de verbinding en browser proxy en probeert u het opnieuw.

1. Selecteer op de pagina VMware **aan de slag** de optie **vertrouwde basis-CA-certificaten downloaden**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere-webclient":::

1. Sla het **download.zip** bestand op de Azure backup server machine op en pak de inhoud vervolgens uit naar de map **certificaten** , die de volgende bevat:

   - Basis certificaat bestand met een extensie die begint met een genummerde reeks, zoals 0 en 1.
   - CRL-bestand met een extensie die begint met een volg orde zoals. R0 of. R1.

1. Klik in de map **certificaten** met de rechter muisknop op het basis certificaat bestand en selecteer **naam wijzigen** om de extensie te wijzigen in **. CRT**.

   Het bestands pictogram wordt gewijzigd in een bestand dat een basis certificaat vertegenwoordigt.

1. Klik met de rechter muisknop op het basis certificaat en selecteer **certificaat installeren**.

1. Selecteer in de **wizard Certificaat importeren** de optie **lokale computer** als doel voor het certificaat en selecteer **volgende**.

   ![Welkomst pagina van de wizard](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Als u daarom wordt gevraagd, bevestigt u dat u wijzigingen aan de computer wilt toestaan.

1. Selecteer **alle certificaten in het onderstaande archief opslaan** en selecteer **Bladeren** om het certificaat archief te kiezen.

   ![Certificaat opslag](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selecteer **vertrouwde basis certificerings instanties** als doelmap en selecteer **OK**.

1. Controleer de instellingen en selecteer **volt ooien** om het certificaat te importeren.

   ![Controleren of het certificaat zich in de juiste map bevindt](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Nadat het importeren van het certificaat is bevestigd, meldt u zich aan bij de vCenter-Server om te bevestigen dat uw verbinding is beveiligd.

### <a name="enable-tls-12-on-azure-backup-server"></a>TLS 1,2 inschakelen op Azure Backup Server

Voor VMware 6,7 werd TLS ingeschakeld als communicatie protocol. 

1. Kopieer de volgende register instellingen en plak deze in Klad blok. Sla het bestand vervolgens op als TLS. REG zonder de extensie. txt.

   ```
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Klik met de rechter muisknop op de TLS. REG-bestand en selecteer **samen voegen** of **openen** om de instellingen toe te voegen aan het REGI ster.


## <a name="add-the-account-on-azure-backup-server"></a>Het account toevoegen op Azure Backup Server

1. Open Azure backup server en selecteer in de Azure backup server-console **beheer**  >  **productie servers**  >  **Manage VMware**.

   ![Azure Backup Server-console](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Selecteer in het dialoog venster **referenties beheren** de optie **toevoegen**.

   ![Selecteer in het dialoog venster referenties beheren de optie toevoegen.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Voer in het dialoog venster **referentie toevoegen** een naam en een beschrijving in voor de nieuwe referentie. Geef de gebruikers naam en het wacht woord op die u op de VMware-Server hebt gedefinieerd.

   > [!NOTE] 
   > Als de VMware-Server en Azure Backup Server zich niet in hetzelfde domein bevinden, geeft u het domein op in het vak **gebruikers naam** .

   ![Dialoog venster Azure Backup Server referentie toevoegen](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Selecteer **toevoegen** om de nieuwe referentie toe te voegen.

   ![Scherm afbeelding toont het dialoog venster Azure Backup Server het beheren van referenties met nieuwe referenties weer gegeven.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>De vCenter-Server toevoegen aan Azure Backup Server

1. Selecteer in de Azure backup server-console de optie **beheer**  >  **productie servers**  >  **toevoegen**.

   ![Wizard voor het toevoegen van productie servers](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selecteer **VMware-servers** en selecteer **volgende**.

   ![Wizard voor het toevoegen van productie servers](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Geef het IP-adres van de vCenter op.

   ![VMware-Server opgeven](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Voer in het vak **SSL-poort** de poort in die wordt gebruikt om te communiceren met de vCenter.

   > [!TIP] 
   > Poort 443 is de standaard poort, maar u kunt deze wijzigen als uw vCenter luistert op een andere poort.

1. Selecteer in het vak **referenties opgeven** de referentie die u hebt gemaakt in de vorige sectie.

1. Selecteer **toevoegen** om de vCenter toe te voegen aan de lijst servers en selecteer **volgende**.

   ![VMware-Server en-referentie toevoegen](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Selecteer op de pagina **samen vatting** **toevoegen** om de vCenter toe te voegen aan Azure backup server.

   De nieuwe server wordt direct toegevoegd. vCenter heeft geen agent nodig.

   ![VMware-Server toevoegen aan Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Controleer de instellingen op de pagina **volt ooien** en selecteer vervolgens **sluiten**.

   ![Pagina volt ooien](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   De vCenter-Server wordt weer gegeven onder **productie server** met:
   - Typen als **VMware-Server** 
   - Agent status als **OK** 
   
      Als u de **agent status** **onbekend** ziet, selecteert u **vernieuwen**.

## <a name="configure-a-protection-group"></a>Een beveiligings groep configureren

Beveiligings groepen verzamelen meerdere Vm's en passen dezelfde gegevens retentie en back-upinstellingen toe op alle virtuele machines in de groep.

1. Selecteer in de Azure backup server-console de optie **beveiliging**  >  **Nieuw**.

   ![Open de wizard nieuwe beveiligings groep maken](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klik op de welkomst pagina van de wizard **nieuwe beveiligings groep maken** op **volgende**.

   ![Dialoog venster Wizard nieuwe beveiligings groep maken](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Selecteer op de pagina **type beveiligings groep selecteren** de optie **servers** en selecteer vervolgens **volgende**. De pagina **groeps leden selecteren** wordt weer gegeven.

1. Selecteer op de pagina **groeps leden selecteren** de vm's (of VM-mappen) waarvan u een back-up wilt maken en selecteer **volgende**.

   > [!NOTE]
   > Wanneer u een map of virtuele machines selecteert, worden mappen in die map ook geselecteerd voor back-up. U kunt de selectie van mappen of Vm's die u niet wilt maken, uitschakelen. Als er al een back-up van een virtuele machine of map wordt gemaakt, kunt u deze niet selecteren, waardoor er geen dubbele herstel punten worden gemaakt voor een virtuele machine.

   ![Groeps leden selecteren](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Voer op de pagina **methode voor gegevens beveiliging selecteren** een naam in voor de beveiligings groep en de beveiligings instellingen. 

1. Stel de beveiliging op de korte termijn in op **schijf**, schakel online beveiliging in en selecteer **volgende**.

   ![Methode voor gegevensbeveiliging selecteren](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Geef op hoelang u een back-up van de gegevens wilt maken op schijf.

   - **Bewaar termijn**: het aantal dagen dat schijf herstel punten worden bewaard.
   - **Snelle volledige back-up**: hoe vaak schijf herstel punten worden gemaakt. Als u de tijden of datums wilt wijzigen wanneer back-ups op korte termijn plaatsvinden, selecteert u **wijzigen**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Geef uw doelen voor de korte termijn voor beveiliging op basis van schijven op":::

1. Controleer op de pagina **toewijzing van Disk Storage** weer geven de schijf ruimte die is vereist voor de back-ups van de virtuele machine.

   - De aanbevolen schijf toewijzingen zijn gebaseerd op de Bewaar termijn die u hebt opgegeven, het type werk belasting en de grootte van de beveiligde gegevens. Breng de gewenste wijzigingen aan en selecteer **volgende**.
   - **Gegevens grootte:** Grootte van de gegevens in de beveiligings groep.
   - **Schijf ruimte:** Aanbevolen hoeveelheid schijf ruimte voor de beveiligings groep. Als u deze instelling wilt wijzigen, selecteert u spatie licht groter dan het aantal dat u voor elke gegevens bron wilt schatten.
   - **Details van opslag groep:** Hier wordt de status van de opslag groep weer gegeven, inclusief de totale en resterende schijf grootte.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="De benodigde schijf ruimte in de opslag groep controleren":::

   > [!NOTE]
   > In sommige scenario's is de gerapporteerde gegevens grootte hoger dan de werkelijke VM-grootte. We zijn op de hoogte van het probleem en kunnen momenteel worden onderzocht.

1. Geef op de pagina **methode voor maken van replica selecteren** op hoe u de eerste back-up wilt maken en selecteer **volgende**.

   - De standaard instelling is **automatisch via het netwerk** en **nu**. Als u de standaard waarde gebruikt, geeft u een rustige tijd op. Als u **later** kiest, geeft u een dag en tijd op.
   - Voor grote hoeveel heden gegevens of minder dan optimale netwerk omstandigheden kunt u overwegen om de gegevens offline te repliceren met behulp van Verwissel bare media.

   ![Methode voor maken van replica selecteren](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Selecteer bij **Opties voor consistentie controle** hoe en wanneer de consistentie controles moeten worden geautomatiseerd en selecteer **volgende**.

   - U kunt consistentie controles uitvoeren wanneer de replica gegevens inconsistent worden, of volgens een ingesteld schema.
   - Als u geen automatische consistentie controles wilt configureren, kunt u een hand matige controle uitvoeren door met de rechter muisknop op de beveiligings groep te klikken **consistentie controle uitvoeren**.

1. Selecteer op de pagina **online beveiligings gegevens opgeven** de VM'S of VM-mappen waarvan u een back-up wilt maken en selecteer **volgende**. 

   > [!TIP]
   > U kunt de leden afzonderlijk selecteren of **Alles selecteren** kiezen om alle leden te kiezen.

   ![Gegevens voor online beveiliging opgeven](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Geef op de pagina **online back-upschema opgeven** op hoe vaak u een back-up wilt maken van gegevens uit de lokale opslag naar Azure. 

   - Cloud herstel punten voor de gegevens die volgens het schema worden gegenereerd. 
   - Nadat het herstel punt is gegenereerd, wordt het overgebracht naar de Recovery Services kluis in Azure.

   ![Online back-upschema opgeven](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geef op de pagina **online retentie beleid opgeven** op hoe lang u wilt dat de herstel punten die zijn gemaakt van de back-ups naar Azure worden bewaard.

   - Er is geen tijds limiet voor hoe lang u gegevens in azure kunt blijven gebruiken.
   - De enige limiet is dat u niet meer dan 9.999 herstel punten per beveiligd exemplaar kunt hebben. In dit voor beeld is het beveiligde exemplaar de VMware-Server.

   ![Online Bewaar beleid opgeven](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Controleer de instellingen op de pagina **samen vatting** en selecteer vervolgens **groep maken**.

   ![Lid van beveiligings groep en samen vatting van instellingen](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Bewaken met de Azure Backup Server-console

Nadat u de beveiligings groep hebt geconfigureerd om een back-up te maken van Azure VMware-oplossingen Vm's, kunt u de status van de back-uptaak en waarschuwing controleren met behulp van de Azure Backup Server-console. Dit is wat u kunt bewaken.

- In het taak gebied **controle** :
   - Onder **waarschuwingen** kunt u fouten, waarschuwingen en algemene informatie bewaken.  U kunt actieve en inactieve waarschuwingen weer geven en e-mail meldingen instellen.
   - Onder **taken** kunt u taken weer geven die zijn gestart door Azure backup server voor een specifieke beveiligde gegevens bron of beveiligings groep. U kunt de voortgang van de taak volgen of resources die worden gebruikt door taken controleren.
- In het taak gebied **beveiliging** kunt u de status van volumes en shares in de beveiligings groep controleren. U kunt ook configuratie-instellingen controleren, zoals herstel instellingen, schijf toewijzing en het back-upschema.
- In het taak gebied **beheer** kunt u de tabbladen **schijven, online** en **agents** weer geven om de status van de schijven in de opslag groep te controleren, te registreren bij Azure en de status van de DPM-agent te implementeren.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="De status van back-uptaken in Azure Backup Server bewaken":::

## <a name="restore-vmware-virtual-machines"></a>Virtuele VMware-machines herstellen

In de Azure Backup Server Administrator-console zijn er twee manieren om herstel bare gegevens te vinden. U kunt zoeken of bladeren. Wanneer u gegevens herstelt, is het mogelijk dat u gegevens of een virtuele machine niet naar dezelfde locatie wilt herstellen. Daarom ondersteunt Azure Backup Server drie herstel opties voor VMware VM-back-ups:

- **Herstel van de oorspronkelijke locatie (herstellen)**: gebruik herstellen om een beveiligde virtuele machine terug te zetten op de oorspronkelijke locatie. U kunt een virtuele machine alleen op de oorspronkelijke locatie herstellen als er geen schijven zijn toegevoegd of verwijderd sinds de back-up is gemaakt. Als er schijven zijn toegevoegd of verwijderd, moet u alternatieve locatie herstel gebruiken.
- **Herstel naar een andere locatie (ALR)**: gebruik wanneer de oorspronkelijke VM ontbreekt of als u niet wilt dat de oorspronkelijke VM wordt verstoord. Geef de locatie op van een ESXi-host, resource groep, map en de opslag-Data Store en het-pad. Azure Backup Server voegt *'-hersteld '* toe aan de naam van de virtuele machine om te voor komen dat de herstelde VM wordt onderscheiden van de oorspronkelijke VM.
- **Herstel van afzonderlijke bestands locaties (ILR)**: als de beveiligde virtuele machine een Windows Server-VM is, kunnen afzonderlijke bestanden of mappen in de virtuele machine worden hersteld met behulp van de ILR-functie van Azure backup server. Zie de procedure verderop in dit artikel voor meer informatie over het herstellen van afzonderlijke bestanden. Het herstellen van een afzonderlijk bestand van een virtuele machine is alleen beschikbaar voor Windows-VM'S en schijf herstel punten.

### <a name="restore-a-recovery-point"></a>Herstel punt herstellen

1. Selecteer in de Azure Backup Server Administrator-console de weer gave **herstel** . 

1. Blader in het **Blader** venster en filter om de virtuele machine te vinden die u wilt herstellen. Nadat u een virtuele machine of map hebt geselecteerd, worden in het deel venster herstel punten voor de beschik bare herstel punten weer gegeven.

   ![Beschik bare herstel punten](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. Selecteer in het deel venster **herstel punten voor** de datum waarop een herstel punt is gemaakt. De gemarkeerde kalender datums bevatten beschik bare herstel punten. U kunt ook met de rechter muisknop op de virtuele machine klikken en **alle herstel punten weer geven** selecteren en vervolgens het herstel punt selecteren in de lijst.

   > [!NOTE] 
   > Voor beveiliging op korte termijn selecteert u een herstel punt op basis van een schijf voor sneller herstel. Nadat herstel punten voor de korte termijn zijn verlopen, ziet u alleen **online** herstel punten die moeten worden hersteld.

1. Voordat u herstelt vanaf een online herstel punt, moet u ervoor zorgen dat de faserings locatie voldoende beschik bare ruimte bevat voor de volledige niet-gecomprimeerde grootte van de virtuele machine die u wilt herstellen. De faserings locatie kan worden weer gegeven of gewijzigd door de **wizard abonnements instellingen configureren** uit te voeren.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Instellingen voor de Recovery-map Azure Backup Server":::

1. Selecteer **herstellen** om de **wizard herstellen** te openen.

   ![Wizard herstellen, selectie van herstel pagina controleren](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Selecteer **volgende** om naar het scherm **herstel opties opgeven** te gaan. Selecteer opnieuw **volgende** om naar het scherm **herstel type selecteren** te gaan. 

   > [!NOTE]
   > VMware-workloads bieden geen ondersteuning voor het inschakelen van netwerk bandbreedte regeling.

1. Op de pagina **type herstel bewerking selecteren** kunt u herstellen naar het oorspronkelijke exemplaar of een nieuwe locatie.

   - Als u **herstellen naar oorspronkelijk exemplaar** kiest, hoeft u geen keuzes meer te maken in de wizard. De gegevens voor de oorspronkelijke instantie worden gebruikt.
   - Als u **herstellen als virtuele machine op een wille keurige host** kiest, geeft u op het scherm **doel opgeven** de gegevens op voor de **ESXi-host**, de **resource groep**, de **map** en het **pad**.

   ![Herstel type pagina selecteren](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Controleer uw instellingen op de pagina **samen vatting** en selecteer **herstellen** om het herstel proces te starten. 

   Het scherm **herstel status** toont de voortgang van de herstel bewerking.

### <a name="restore-an-individual-file-from-a-vm"></a>Een afzonderlijk bestand herstellen vanaf een virtuele machine

U kunt afzonderlijke bestanden herstellen vanaf een herstel punt van een beveiligde virtuele machine. Deze functie is alleen beschikbaar voor virtuele machines met Windows Server. Het herstellen van afzonderlijke bestanden is vergelijkbaar met het herstellen van de hele virtuele machine, behalve dat u naar de VMDK bladert en de gewenste bestanden zoekt voordat u het herstel proces start. 

> [!NOTE]
> Het herstellen van een afzonderlijk bestand van een virtuele machine is alleen beschikbaar voor Windows-VM'S en schijf herstel punten.

1. Selecteer in de Azure Backup Server Administrator-console de weer gave **herstel** .

1. Blader in het **Blader** venster en filter om de virtuele machine te vinden die u wilt herstellen. Nadat u een virtuele machine of map hebt geselecteerd, worden in het deel venster herstel punten voor de beschik bare herstel punten weer gegeven.

   ![Beschik bare herstel punten](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. In het deel venster **herstel punten voor** kunt u de kalender gebruiken om de datum te selecteren die de gewenste herstel punten bevat. Afhankelijk van hoe het back-upbeleid is geconfigureerd, kunnen de datums meer dan één herstel punt hebben. 

1. Nadat u de dag hebt geselecteerd waarop het herstel punt is gemaakt, moet u ervoor zorgen dat u de juiste **herstel tijd** kiest. 

   > [!NOTE]
   > Als de geselecteerde datum meerdere herstel punten heeft, kiest u het herstel punt door deze te selecteren in de vervolg keuzelijst **herstel tijd** . 

   Nadat u het herstel punt hebt gekozen, wordt de lijst met herstel bare items weer gegeven in het deel venster **pad** .

1. Als u de bestanden wilt vinden die u wilt herstellen, dubbelklikt u in het deel venster **pad** op het item in de kolom **herstel bare items** om het te openen. Selecteer vervolgens het bestand of de mappen die u wilt herstellen. Als u meerdere items wilt selecteren, selecteert u de **CTRL** -toets terwijl u elk item selecteert. In het deel venster **pad** kunt u zoeken naar de lijst met bestanden of mappen die worden weer gegeven in de kolom **herstelbaar item** .
    
   > [!NOTE]
   > In de **onderstaande Zoek lijst** worden geen submappen doorzocht. Dubbel klik op de map om in submappen te zoeken. Gebruik de knop **omhoog** om van een onderliggende map naar de bovenliggende map te gaan. U kunt meerdere items selecteren (bestanden en mappen), maar ze moeten zich in dezelfde bovenliggende map bevinden. U kunt geen items uit meerdere mappen in dezelfde herstel taak herstellen.

   ![Selectie voor herstel controleren](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Wanneer u de items voor herstel hebt geselecteerd, selecteert u in het lint van de Administrator-console op **herstellen** om de **wizard herstellen** te openen. In de **herstel wizard** ziet u in het scherm **herstel selectie controleren** de geselecteerde items die moeten worden hersteld.

1. Voer op het scherm **herstel opties opgeven** een van de volgende stappen uit:

   - Selecteer **wijzigen** om netwerk bandbreedte regeling in te scha kelen. In het dialoog venster **gashendel** selecteert u **beperking van netwerk bandbreedte gebruik inschakelen** om deze in te scha kelen. Wanneer deze functie is ingeschakeld, configureert u de **instellingen** en het **werk schema**.
   - Selecteer **volgende** om netwerk beperking uitgeschakeld te laten.

1. Selecteer **volgende** in het scherm **herstel type selecteren** . U kunt uw bestanden of mappen alleen herstellen naar een netwerkmap.

1. Selecteer op het scherm **doel opgeven** de optie **Bladeren** om een netwerk locatie te zoeken voor uw bestanden of mappen. Azure Backup Server maakt een map waarin alle herstelde items worden gekopieerd. De mapnaam heeft het voor voegsel MABS_day-maand-jaar. Wanneer u een locatie voor de herstelde bestanden of map selecteert, worden de gegevens voor die locatie gegeven.

   ![Geef de locatie op voor het herstellen van bestanden](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Kies op het scherm **herstel opties opgeven** welke beveiligings instelling u wilt Toep assen. U kunt ervoor kiezen om de beperking van het netwerk bandbreedte gebruik te wijzigen, maar de beperking is standaard uitgeschakeld. Ook **San-herstel** en- **meldingen** zijn niet ingeschakeld.

1. Controleer uw instellingen op het scherm **samen vatting** en selecteer **herstellen** om het herstel proces te starten. Het scherm **herstel status** toont de voortgang van de herstel bewerking.

## <a name="next-steps"></a>Volgende stappen

Nu u een back-up hebt gemaakt van uw Azure VMware-oplossingen voor virtuele machines met Azure Backup Server, kunt u het volgende weten: 

- [Problemen oplossen bij het instellen van back-ups in azure backup server](../backup/backup-azure-mabs-troubleshoot.md).
- [Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen](lifecycle-management-of-azure-vmware-solution-vms.md).
