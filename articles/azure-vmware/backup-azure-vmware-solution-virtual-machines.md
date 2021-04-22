---
title: Back-up maken Azure VMware Solution VM's met Azure Backup Server
description: Configureer uw Azure VMware Solution om een back-up te maken van virtuele machines met behulp van Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 92affbf883215f0d051115fb64e9e7bf7a1430b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871773"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Back-up maken Azure VMware Solution VM's met Azure Backup Server

In dit artikel maken we een back-up van virtuele VMware-machines (VM's) die worden uitgevoerd op Azure VMware Solution met Azure Backup Server. Ga eerst grondig door Microsoft Azure Backup [Server instellen voor Azure VMware Solution.](set-up-backup-server-for-azure-vmware-solution.md)

Vervolgens doorlopen we alle benodigde procedures voor het volgende:

> [!div class="checklist"] 
> * Stel een beveiligd kanaal in zodat Azure Backup Server kunnen communiceren met VMware-servers via HTTPS. 
> * Voeg de accountreferenties toe aan Azure Backup Server. 
> * Voeg het vCenter toe aan Azure Backup Server. 
> * Stel een beveiligingsgroep in die de VMware-VM's bevat waar u een back-up van wilt maken, geef back-upinstellingen op en plan de back-up.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Een beveiligde verbinding maken met de vCenter-server

Standaard communiceert Azure Backup Server met VMware-servers via HTTPS. Als u de HTTPS-verbinding wilt instellen, downloadt u het certificaat van de VMware-certificeringsinstantie (CA) en importeert u het op de Azure Backup Server.

### <a name="set-up-the-certificate"></a>Het certificaat instellen

1. Voer in de browser op de Azure Backup Server de URL van de vSphere-webclient in.

   > [!NOTE] 
   > Als de VMware Aan de slag pagina niet wordt weergegeven, controleert u de verbindings- en browserproxy-instellingen en probeert u het opnieuw. 

1. Selecteer op de pagina VMware **Aan de slag** vertrouwde **basis-CA-certificaten downloaden.**

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere-webclient":::

1. Sla het **download.zip** op de Azure Backup Server machine en extraheert de inhoud ervan naar de map **certs,** die het volgende bevat:

   - Basiscertificaatbestand met een extensie die begint met een genummerde reeks, zoals .0 en .1.
   - CRL-bestand met een extensie die begint met een reeks zoals .r0 of .r1.

1. Klik in **de map certificaten met** de rechtermuisknop op het basiscertificaatbestand en selecteer **Naam** wijzigen om de extensie te wijzigen in **.crt**.

   Het bestandspictogram wordt gewijzigd in een pictogram dat een basiscertificaat vertegenwoordigt.

1. Klik met de rechtermuisknop op het basiscertificaat en selecteer **Certificaat installeren.**

1. Selecteer in **de wizard Certificaat** importeren de optie Lokale **computer** als de bestemming voor het certificaat en selecteer **Volgende.**

   ![Welkomstpagina van wizard](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Bevestig desgevraagd dat u wijzigingen op de computer wilt toestaan.

1. Selecteer **Alle certificaten in het volgende winkel plaatsen en** selecteer Bladeren **om** het certificaatopslag te kiezen.

   ![Certificaatopslag](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selecteer **Vertrouwde basiscertificeringsinstanties** als de doelmap en selecteer **OK.**

1. Controleer de instellingen en selecteer **Voltooien om** te beginnen met het importeren van het certificaat.

   ![Controleren of het certificaat zich in de juiste map](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Nadat het importeren van het certificaat is bevestigd, meld u zich aan bij de vCenter-server om te bevestigen dat uw verbinding veilig is.

### <a name="enable-tls-12-on-azure-backup-server"></a>TLS 1.2 inschakelen op Azure Backup Server

Voor VMware 6.7 was TLS ingeschakeld als communicatieprotocol. 

1. Kopieer de volgende registerinstellingen en plak deze in Kladblok. Sla het bestand vervolgens op als TLS. Reg zonder de extensie .txt.

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

1. Klik met de rechtermuisknop op de TLS. REG-bestand en selecteer **Samenvoegen** of **Openen om** de instellingen toe te voegen aan het register.


## <a name="add-the-account-on-azure-backup-server"></a>Het account toevoegen aan Azure Backup Server

1. Open Azure Backup Server en selecteer in Azure Backup Server-console   >  **beheerproductieservers**  >  **VMware beheren.**

   ![Azure Backup Server-console](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Selecteer in **het dialoogvenster Referenties** beheren de optie **Toevoegen.**

   ![Selecteer in het dialoogvenster Referenties beheren de optie Toevoegen.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Voer in **het dialoogvenster Referentie** toevoegen een naam en een beschrijving in voor de nieuwe referentie. Geef de gebruikersnaam en het wachtwoord op die u hebt gedefinieerd op de VMware-server.

   > [!NOTE] 
   > Als de VMware-server en Azure Backup Server zich niet in hetzelfde domein, geeft u het domein op in **het vak** Gebruikersnaam.

   ![Azure Backup Server dialoogvenster Referentie toevoegen](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Selecteer **Toevoegen om** de nieuwe referentie toe te voegen.

   ![Schermopname van Azure Backup Server dialoogvenster Referenties beheren met nieuwe referenties weergegeven.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>De vCenter-server toevoegen aan Azure Backup Server

1. Selecteer in Azure Backup Server-console   >  **BeheerProductieservers**  >  **toevoegen.**

   ![Wizard Toevoegen aan productieserver openen](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selecteer **VMware-servers** en selecteer **Volgende.**

   ![Wizard Toevoegen van productieserver](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Geef het IP-adres van het vCenter op.

   ![VMware-server opgeven](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Voer in **het vak SSL-poort** de poort in die wordt gebruikt om te communiceren met het vCenter.

   > [!TIP] 
   > Poort 443 is de standaardpoort, maar u kunt deze wijzigen als uw vCenter op een andere poort luistert.

1. Selecteer in **het vak Referentie** opgeven de referentie die u in de vorige sectie hebt gemaakt.

1. Selecteer **Toevoegen** om het vCenter toe te voegen aan de lijst met servers en selecteer **Volgende.**

   ![VMware-server en -referenties toevoegen](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Selecteer op **de pagina** Samenvatting de optie **Toevoegen om** het vCenter toe te voegen aan Azure Backup Server.

   De nieuwe server wordt onmiddellijk toegevoegd. vCenter heeft geen agent nodig.

   ![VMware-server toevoegen aan Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Controleer de **instellingen** op de pagina Voltooien en selecteer **Sluiten.**

   ![Pagina Voltooien](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   De vCenter-server wordt vermeld onder **Productieserver** met:
   - Typ als **VMware Server** 
   - Agentstatus als **OK** 
   
      Als agentstatus **Onbekend wordt** **weergeven, selecteert** u **Vernieuwen.**

## <a name="configure-a-protection-group"></a>Een beveiligingsgroep configureren

Beveiligingsgroepen verzamelen meerdere VM's en passen dezelfde instellingen voor gegevensretentie en back-up toe op alle VM's in de groep.

1. Selecteer in Azure Backup Server-console **De beveiliging**  >  **Nieuw.**

   ![Open de wizard Nieuwe beveiligingsgroep maken](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Selecteer op **de welkomstpagina van de** wizard Nieuwe beveiligingsgroep maken de optie **Volgende.**

   ![Dialoogvenster Wizard Nieuwe beveiligingsgroep maken](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Selecteer op **de pagina Type beveiligingsgroep** selecteren **de optie Servers** en selecteer vervolgens **Volgende.** De **pagina Groepsleden selecteren** wordt weergegeven.

1. Selecteer op **de pagina Groepsleden** selecteren de VM's (of VM-mappen) van wie u een back-up wilt maken en selecteer **vervolgens Volgende.**

   > [!NOTE]
   > Wanneer u een map of VM's selecteert, worden mappen in die map ook geselecteerd voor back-up. U kunt mappen of VM's waar u geen back-up van wilt maken, uitvinken. Als er al een back-up van een VM of map wordt gemaakt, kunt u deze niet selecteren. Dit zorgt ervoor dat er geen dubbele herstelpunten worden gemaakt voor een VM.

   ![Groepsleden selecteren](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Voer op **de pagina Methode voor gegevensbeveiliging** selecteren een naam in voor de beveiligingsgroep en beveiligingsinstellingen. 

1. Stel de kortetermijnbeveiliging in op **Schijf,** schakel onlinebeveiliging in en selecteer **volgende.**

   ![Methode voor gegevensbeveiliging selecteren](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Geef op hoelang u een back-up van gegevens op schijf wilt houden.

   - **Bewaartermijn:** het aantal dagen dat schijfherstelpunten worden bewaard.
   - **Express Full Backup:** hoe vaak schijfherstelpunten worden gemaakt. Als u de tijden of datums wilt wijzigen waarop back-ups voor de korte termijn worden gemaakt, selecteert u **Wijzigen.**

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Uw kortetermijndoelen voor schijfbeveiliging opgeven":::

1. Op de **pagina Disk Storage controleren controleert** u de schijfruimte die is opgegeven voor de VM-back-ups.

   - De aanbevolen schijftoewijzingen zijn gebaseerd op de bewaartermijn die u hebt opgegeven, het type werkbelasting en de grootte van de beveiligde gegevens. Wijzig de vereiste wijzigingen en selecteer **Volgende.**
   - **Gegevensgrootte:** Grootte van de gegevens in de beveiligingsgroep.
   - **Schijfruimte:** Aanbevolen hoeveelheid schijfruimte voor de beveiligingsgroep. Als u deze instelling wilt wijzigen, selecteert u de ruimte iets groter dan de hoeveelheid die u schat dat elke gegevensbron groeit.
   - **Details van opslaggroep:** Toont de status van de opslaggroep, waaronder de totale en resterende schijfgrootte.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Schijfruimte in de opslaggroep controleren":::

   > [!NOTE]
   > In sommige scenario's is de gerapporteerde gegevensgrootte groter dan de werkelijke VM-grootte. We zijn op de hoogte van het probleem en onderzoeken het momenteel.

1. Geef op **de pagina Methode voor** het maken van replica kiezen aan hoe u de eerste back-up wilt maken en selecteer **Volgende.**

   - De standaardwaarde is **Automatisch via het netwerk en** **Nu.** Als u de standaardwaarde gebruikt, geeft u een daltijd op. Als u Later **kiest,** geeft u een dag en tijd op.
   - Voor grote hoeveelheden gegevens of minder optimale netwerkomstandigheden kunt u overwegen om de gegevens offline te repliceren met behulp van verwisselbare media.

   ![Methode voor het maken van replica's kiezen](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Bij **Opties voor consistentiecontrole** selecteert u hoe en wanneer u de consistentiecontroles wilt automatiseren en selecteert u **Volgende.**

   - U kunt consistentiecontroles uitvoeren wanneer replicagegevens inconsistent worden of volgens een vast schema.
   - Als u geen automatische consistentiecontroles wilt configureren, kunt u een handmatige controle uitvoeren door met de rechtermuisknop op de beveiligingsgroep **Consistentiecontrole uitvoeren te klikken.**

1. Selecteer op **de pagina Onlinebeveiligingsgegevens** opgeven de VM's of VM-mappen waar u een back-up van wilt maken en selecteer **vervolgens Volgende.** 

   > [!TIP]
   > U kunt de leden afzonderlijk selecteren of Alles **selecteren om** alle leden te kiezen.

   ![Onlinebeveiligingsgegevens opgeven](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Geef op **de pagina Online back-upschema** opgeven aan hoe vaak u een back-up wilt maken van gegevens van lokale opslag naar Azure. 

   - Cloudherstelpunten voor de gegevens die volgens de planning moeten worden gegenereerd. 
   - Nadat het herstelpunt is gegenereerd, wordt het overgedragen naar de Recovery Services-kluis in Azure.

   ![Online back-upschema opgeven](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geef op **de pagina Onlineretentiebeleid** opgeven aan hoelang u de herstelpunten die zijn gemaakt van de back-ups naar Azure wilt behouden.

   - Er is geen tijdslimiet voor hoe lang u gegevens in Azure kunt bewaren.
   - De enige limiet is dat u niet meer dan 9999 herstelpunten per beveiligd exemplaar kunt hebben. In dit voorbeeld is het beveiligde exemplaar de VMware-server.

   ![Onlineretentiebeleid opgeven](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Controleer de **instellingen** op de pagina Samenvatting en selecteer vervolgens **Groep maken.**

   ![Samenvatting van beveiligingsgroepslid en instelling](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Bewaken met de Azure Backup Server console

Nadat u de beveiligingsgroep hebt geconfigureerd om een back-up te maken van Azure VMware Solution-VM's, kunt u de status van de back-up job controleren en een waarschuwing ontvangen met behulp van Azure Backup Server console. Dit is wat u kunt controleren.

- In het **taakgebied** Bewaking:
   - Onder **Waarschuwingen** kunt u fouten, waarschuwingen en algemene informatie controleren.  U kunt actieve en inactieve waarschuwingen weergeven en e-mailmeldingen instellen.
   - Onder **Taken** kunt u taken weergeven die zijn gestart Azure Backup Server voor een specifieke beveiligde gegevensbron of beveiligingsgroep. U kunt de voortgang van de taak volgen of resources controleren die door taken worden verbruikt.
- In het **taakgebied** Beveiliging kunt u de status van volumes en shares in de beveiligingsgroep controleren. U kunt ook configuratie-instellingen controleren, zoals herstelinstellingen, schijftoewijzing en het back-upschema.
- In het **taakgebied** Beheer kunt u de tabbladen **Schijven, Online** en **Agents** weergeven om de status van schijven in de opslaggroep, registratie bij Azure en de status van de geïmplementeerde DPM-agent te controleren.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="De status van back-uptaken in Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Virtuele VMware-machines herstellen

In de Azure Backup Server Administrator-console zijn er twee manieren om herstelbare gegevens te vinden. U kunt zoeken of bladeren. Wanneer u gegevens herstelt, wilt u al dan niet gegevens of een VM op dezelfde locatie herstellen. Daarom ondersteunt Azure Backup Server drie herstelopties voor back-ups van VMware-VM's:

- **Herstel van oorspronkelijke locatie (OLR)**: gebruik OLR om een beveiligde VM te herstellen naar de oorspronkelijke locatie. U kunt een VM alleen herstellen op de oorspronkelijke locatie als er geen schijven zijn toegevoegd of verwijderd sinds de back-up is gemaakt. Als schijven zijn toegevoegd of verwijderd, moet u alternatieve locatieherstel gebruiken.
- **Alternatieve locatieherstel (ALR)**: gebruik deze als de oorspronkelijke VM ontbreekt of als u de oorspronkelijke VM niet wilt verstoren. Geef de locatie van een ESXi-host, resourcegroep, map en de opslaggegevensopslag en het pad op. Als u de herstelde VM wilt onderscheiden van de oorspronkelijke VM, Azure Backup Server *'-Hersteld'* aan de naam van de VM.
- Herstel van afzonderlijke bestandslocatie **(ILR)**: als de beveiligde VM een Windows Server-VM is, kunnen afzonderlijke bestanden of mappen in de VM worden hersteld met behulp van de ILR-mogelijkheid van Azure Backup Server. Zie de procedure verder op dit artikel als u afzonderlijke bestanden wilt herstellen. Het herstellen van een afzonderlijk bestand vanaf een VM is alleen beschikbaar voor Windows-VM's en schijfherstelpunten.

### <a name="restore-a-recovery-point"></a>Een herstelpunt herstellen

1. Selecteer in Azure Backup Server Administrator-console de **weergave** Herstel. 

1. Blader of **filter** in het deelvenster Bladeren om de VM te vinden die u wilt herstellen. Nadat u een VM of map hebt geselecteerd, worden in het deelvenster **Herstelpunten voor de beschikbare herstelpunten weergegeven.

   ![Beschikbare herstelpunten](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. Selecteer in **het deelvenster Herstelpunten** voor een datum waarop een herstelpunt is gemaakt. De vetgedrukte kalenderdatums hebben beschikbare herstelpunten. U kunt ook met de rechtermuisknop op de VM klikken en **Alle herstelpunten** tonen selecteren en vervolgens het herstelpunt in de lijst selecteren.

   > [!NOTE] 
   > Voor kortetermijnbeveiliging selecteert u een herstelpunt op basis van een schijf voor sneller herstel. Nadat herstelpunten voor de korte termijn zijn verlopen, ziet u alleen **Online** herstelpunten om te herstellen.

1. Voordat u herstelt vanaf een online herstelpunt, moet u ervoor zorgen dat de faseringslocatie voldoende vrije ruimte bevat voor de volledige niet-gecomprimeerde grootte van de VM die u wilt herstellen. De faseringslocatie kan worden bekeken of gewijzigd door de wizard **Abonnementsinstellingen configureren uit te uitvoeren.**

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Azure Backup Server Recovery Folder Settings":::

1. Selecteer **Herstellen om** de wizard Herstellen te **openen.**

   ![Wizard Herstel, pagina Herstelselectie controleren](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Selecteer **Volgende om** naar het scherm **Herstelopties opgeven te** gaan. Selecteer **opnieuw Volgende** om naar het scherm **Hersteltype selecteren te** gaan. 

   > [!NOTE]
   > VMware-workloads bieden geen ondersteuning voor het inschakelen van netwerkbandbreedtebeperking.

1. Op de **pagina Hersteltype selecteren** herstelt u naar het oorspronkelijke exemplaar of een nieuwe locatie.

   - Als u **Herstellen naar het oorspronkelijke exemplaar kiest,** hoeft u geen keuzes meer te maken in de wizard. De gegevens voor het oorspronkelijke exemplaar worden gebruikt.
   - Als u op **een host** als virtuele  machine herstellen kiest, geeft u in het scherm Doel opgeven de informatie op voor **ESXi-host,** **resourcegroep,** map en **pad.** 

   ![De pagina Hersteltype selecteren](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Controleer uw **instellingen op** de pagina Samenvatting en selecteer **Herstellen om** het herstelproces te starten. 

   Op **het scherm Herstelstatus** wordt de voortgang van de herstelbewerking weergegeven.

### <a name="restore-an-individual-file-from-a-vm"></a>Een afzonderlijk bestand herstellen vanaf een VM

U kunt afzonderlijke bestanden herstellen vanaf een beveiligd VM-herstelpunt. Deze functie is alleen beschikbaar voor Windows Server-VM's. Het herstellen van afzonderlijke bestanden is vergelijkbaar met het herstellen van de hele VM, behalve dat u naar de VMDK bladert en de bestanden vindt die u wilt voordat u het herstelproces start. 

> [!NOTE]
> Het herstellen van een afzonderlijk bestand vanaf een VM is alleen beschikbaar voor Windows-VM's en schijfherstelpunten.

1. Selecteer in Azure Backup Server Administrator-console de **weergave** Herstel.

1. Blader of **filter** in het deelvenster Bladeren om de VM te vinden die u wilt herstellen. Nadat u een VM of map hebt geselecteerd, worden in het deelvenster **Herstelpunten voor de beschikbare herstelpunten weergegeven.

   ![Herstelpunten beschikbaar](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. In het **deelvenster Herstelpunten voor** gebruikt u de agenda om de datum te selecteren die de gezochte herstelpunten bevat. Afhankelijk van hoe het back-upbeleid is geconfigureerd, kunnen datums meer dan één herstelpunt hebben. 

1. Nadat u de dag hebt geselecteerd waarop het herstelpunt is genomen, moet u ervoor zorgen dat u de juiste **hersteltijd kiest.** 

   > [!NOTE]
   > Als de geselecteerde datum meerdere herstelpunten heeft, kiest  u het herstelpunt door dit te selecteren in de vervolgkeuzelijst Hersteltijd. 

   Nadat u het herstelpunt hebt gekozen, wordt de lijst met herstelbare items weergegeven in het **deelvenster Pad.**

1. Als u de bestanden wilt vinden  die u wilt herstellen, dubbelklikt u in het deelvenster Pad op het item in de kolom **Herstelbaar item** om het te openen. Selecteer vervolgens het bestand of de mappen die u wilt herstellen. Als u meerdere items wilt selecteren, selecteert u **de Ctrl-toets** terwijl u elk item selecteert. Gebruik het **deelvenster Pad** om te zoeken in de lijst met bestanden of mappen die worden weergegeven in de **kolom Herstelbaar** item.
    
   > [!NOTE]
   > **In de onderstaande** zoeklijst wordt niet in submappen gezocht. Dubbelklik op de map om te zoeken in submappen. Gebruik de **knop Omhoog** om van een onderliggende map naar de bovenliggende map te gaan. U kunt meerdere items (bestanden en mappen) selecteren, maar ze moeten zich in dezelfde bovenliggende map. U kunt geen items uit meerdere mappen in dezelfde herstel job herstellen.

   ![Selectie van herstel controleren](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Wanneer u de items voor herstel hebt geselecteerd, selecteert u herstellen op het lint van het hulpprogramma Administrator-console om **de** wizard **Herstellen te openen.** In de **wizard Herstel toont** het scherm Selectie **van** herstel controleren de geselecteerde items die moeten worden hersteld.

1. In het **scherm Herstelopties** opgeven gaat u een van de volgende stappen uit:

   - Selecteer **Wijzigen om** netwerkbandbreedtebeperking in te stellen. Selecteer in **het dialoogvenster** Beperken de optie Beperking **van netwerkbandbreedtegebruik** inschakelen om dit in te stellen. Zodra deze is ingeschakeld, configureert u **de instellingen** en **het werkschema.**
   - Selecteer **Volgende om** netwerkbeperking uitgeschakeld te laten.

1. Selecteer in **het scherm Hersteltype** selecteren de optie **Volgende.** U kunt alleen uw bestanden of mappen herstellen naar een netwerkmap.

1. Selecteer in **het scherm Doel** opgeven de optie **Bladeren** om een netwerklocatie voor uw bestanden of mappen te vinden. Azure Backup Server maakt u een map waarin alle herstelde items worden gekopieerd. De mapnaam heeft het voorvoegsel MABS_day-maand-jaar. Wanneer u een locatie voor de herstelde bestanden of map selecteert, worden de details voor die locatie opgegeven.

   ![Locatie opgeven voor het herstellen van bestanden](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Kies in **het scherm Herstelopties** opgeven welke beveiligingsinstelling u wilt toepassen. U kunt ervoor kiezen om de bandbreedtebeperking voor het netwerk te wijzigen, maar beperking is standaard uitgeschakeld. San **Recovery en** **Notification** zijn ook niet ingeschakeld.

1. Controleer uw **instellingen in** het scherm Samenvatting en selecteer **Herstellen om** het herstelproces te starten. Op **het scherm Herstelstatus** wordt de voortgang van de herstelbewerking weergegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u het maken van back-up van uw virtuele Azure VMware Solution's met Azure Backup Server hebt behandeld, wilt u mogelijk meer informatie over: 

- [Problemen oplossen bij het instellen van back-ups in Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
- [Levenscyclusbeheer van Azure VMware Solution-VM's](lifecycle-management-of-azure-vmware-solution-vms.md)
