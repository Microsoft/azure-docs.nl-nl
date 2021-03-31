---
title: Back-ups maken van VMware-Vm's met Azure Backup Server
description: In dit artikel leert u hoe u Azure Backup Server kunt gebruiken om een back-up te maken van virtuele VMware-machines die op een VMware vCenter/ESXi-server worden uitgevoerd.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: db5e5c4bdac64e2faf5babb107ecec61a02d6468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002950"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Back-ups maken van VMware-Vm's met Azure Backup Server

In dit artikel wordt uitgelegd hoe u een back-up maakt van virtuele VMware-machines die worden uitgevoerd op VMware ESXi hosts/vCenter Server naar Azure met behulp van Azure Backup Server (MABS).

In dit artikel wordt uitgelegd hoe u:

- Stel een beveiligd kanaal zodanig in dat Azure Backup Server kan communiceren met VMware-servers via HTTPS.
- Stel een VMware-account in dat Azure Backup Server gebruikt voor toegang tot de VMware-Server.
- Voeg de account referenties toe aan Azure Backup.
- Voeg de vCenter-of ESXi-server toe aan Azure Backup Server.
- Stel een beveiligings groep in die de VMware-Vm's bevat waarvan u een back-up wilt maken, geef back-upinstellingen op en plan de back-up.

## <a name="supported-vmware-features"></a>Ondersteunde VMware-functies

MABS biedt de volgende functies voor het maken van een back-up van virtuele VMware-machines:

- Back-up zonder agent: voor MABS hoeft er geen agent te worden geïnstalleerd op de vCenter-of ESXi-server om een back-up van de virtuele machine te maken. Geef in plaats daarvan alleen het IP-adres of de Fully Qualified Domain Name (FQDN) op en aanmeldings referenties die worden gebruikt voor het verifiëren van de VMware-Server met MABS.
- In de Cloud geïntegreerde back-up: MABS beschermt workloads op schijf en in de Cloud. De werk stroom back-up en herstel van MABS helpt u bij het beheren van lange termijn retentie en externe back-ups.
- Vm's detecteren en beveiligen die worden beheerd door vCenter: MABS detecteert en beveiligt Vm's die zijn geïmplementeerd op een VMware-Server (vCenter of ESXi-server). Als uw implementatie grootte groeit, gebruikt u vCenter voor het beheren van uw VMware-omgeving. MABS detecteert ook Vm's die worden beheerd door vCenter, zodat u grote implementaties kunt beveiligen.
- Automatische beveiliging op mapniveau: met vCenter kunt u uw virtuele machines in VM-mappen ordenen. MABS detecteert deze mappen en biedt u de mogelijkheid om Vm's op mapniveau te beschermen en alle submappen te bevatten. Bij het beveiligen van mappen beschermt MABS niet alleen de virtuele machines in die map, maar beveiligt Vm's ook later. MABS detecteert dagelijks nieuwe Vm's en beveiligt deze automatisch. Wanneer u uw Vm's in recursieve mappen organiseert, detecteert en beveiligt MABS automatisch de nieuwe Vm's die in de recursieve mappen zijn geïmplementeerd.
- MABS beveiligt Vm's die zijn opgeslagen op een lokale schijf, NFS (Network File System) of cluster opslag.
- MABS beveiligt Vm's die zijn gemigreerd voor taak verdeling: als er Vm's worden gemigreerd voor taak verdeling, detecteert MABS automatisch de beveiliging van virtuele machines en gaat deze door.
- MABS kan bestanden/mappen van een Windows-VM herstellen zonder de volledige VM te herstellen, waardoor de benodigde bestanden sneller kunnen worden hersteld.

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

Voordat u een back-up van een virtuele VMware-machine maakt, controleert u de volgende lijst met beperkingen en vereisten.

- Als u MABS hebt gebruikt om een vCenter-Server (die op Windows wordt uitgevoerd) te beveiligen als een Windows-Server met behulp van de FQDN van de server, kunt u die vCenter-Server niet beveiligen als een VMware-Server met behulp van de FQDN-naam van de server.
  - U kunt het statische IP-adres van vCenter Server als tijdelijke oplossing gebruiken.
  - Als u de FQDN wilt gebruiken, moet u de beveiliging stoppen als een Windows-Server, de beveiligings agent verwijderen en vervolgens toevoegen als een VMware-Server met behulp van FQDN.
- Als u vCenter gebruikt voor het beheren van ESXi-servers in uw omgeving, voegt u vCenter (en niet ESXi) toe aan de MABS-beveiligings groep.
- U kunt geen back-up maken van moment opnamen van de gebruiker voor de eerste MABS-back-up. Zodra MABS de eerste back-up heeft voltooid, kunt u back-ups maken van gebruikers momentopnamen.
- MABS kan geen virtuele VMware-machines beveiligen met doorgangs schijven en fysieke RAW-apparaattoewijzing (pRDM).
- MABS kan VMware vApps niet detecteren of beveiligen.
- MABS kan geen virtuele VMware-machines beveiligen met bestaande moment opnamen.

## <a name="before-you-start"></a>Voordat u begint

- Controleer of u een versie van vCenter-ESXi gebruikt die wordt ondersteund voor back-up. Raadpleeg [hier](./backup-mabs-protection-matrix.md)de ondersteunings matrix.
- Zorg ervoor dat u Azure Backup Server hebt ingesteld. Als u dat nog [niet hebt gedaan, moet u dat doen](backup-azure-microsoft-azure-backup.md) voordat u begint. U moet Azure Backup Server uitvoeren met de nieuwste updates.
- Controleer of de volgende netwerk poorten zijn geopend:
  - TCP 443 tussen MABS en vCenter
  - TCP 443 en TCP 902 tussen de MABS-en ESXi-host

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Een beveiligde verbinding met de vCenter Server maken

Azure Backup Server communiceert standaard met VMware-servers via HTTPS. Als u de HTTPS-verbinding wilt instellen, downloadt u het CA-certificaat (VMware Certificate Authority) en importeert u het op de Azure Backup Server.

### <a name="before-you-begin"></a>Voordat u begint

- Als u HTTPS niet wilt gebruiken, kunt u [HTTPS-certificaat validatie uitschakelen voor alle VMware-servers](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- U maakt doorgaans vanuit een browser op de Azure Backup Server machine een verbinding met de vCenter/ESXi-server met behulp van de vSphere-webclient. De eerste keer dat u dit doet, is de verbinding niet beveiligd en ziet u het volgende.
- Het is belang rijk om te begrijpen hoe Azure Backup Server back-ups verwerkt.
  - Als eerste stap Azure Backup Server back-ups maken van gegevens naar de lokale schijf opslag. Azure Backup Server gebruikt een opslag groep, een set schijven en volumes waarop Azure Backup Server schijf herstel punten voor de beveiligde gegevens opslaat. De opslag groep kan direct Attached Storage (DAS), een Fibre Channel-SAN of een iSCSI-opslag apparaat of SAN zijn. Het is belang rijk om ervoor te zorgen dat u voldoende opslag ruimte hebt voor lokale back-ups van uw VMware VM-gegevens.
  - Azure Backup Server vervolgens een back-up van de lokale schijf opslag naar Azure.
  - [Krijg hulp](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need) bij het bepalen van de hoeveelheid opslag ruimte die u nodig hebt. De informatie is voor DPM, maar kan ook worden gebruikt voor Azure Backup Server.

### <a name="set-up-the-certificate"></a>Het certificaat instellen

Stel als volgt een beveiligd kanaal in:

1. Voer in de browser op Azure Backup Server de URL van de vSphere-webclient in. Als de aanmeldings pagina niet wordt weer gegeven, controleert u de instellingen voor de verbinding en browser proxy.

    ![vSphere-webclient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Selecteer op de aanmeldings pagina van de vSphere-webclient de optie **vertrouwde basis-CA-certificaten downloaden**.

    ![Vertrouwd basis-CA-certificaat downloaden](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Er wordt een bestand met de naam **down load** gedownload. Afhankelijk van uw browser krijgt u een bericht waarin u wordt gevraagd of u het bestand wilt openen of opslaan.

    ![CA-certificaat downloaden](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Sla het bestand op de Azure Backup Server machine op met de extensie. zip.

5. Klik met de rechter muisknop op **download.zip**  >  **Alles uitpakken**. Het zip-bestand extraheert de inhoud naar de map **certs** , die de volgende bevat:
   - Het basis certificaat bestand met een extensie die begint met een genummerde reeks, zoals 0 en 1.
   - Het CRL-bestand heeft een extensie die begint met een Sequence zoals. R0 of. R1. Het CRL-bestand is gekoppeld aan een certificaat.

    ![Gedownloade certificaten](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Klik in de map **certificaten** met de rechter muisknop op het basis certificaat bestand > **naam wijzigen**.

    ![Naam van basis certificaat wijzigen](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Wijzig de extensie van het basis certificaat in. CRT en bevestig dit. Het bestands pictogram wordt gewijzigd in een bestand dat een basis certificaat vertegenwoordigt.

8. Klik met de rechter muisknop op het basis certificaat en selecteer **certificaat installeren** in het pop-upmenu.

9. Selecteer in de **wizard Certificaat importeren** de optie **lokale computer** als doel voor het certificaat en selecteer vervolgens **volgende**. Bevestig of u wordt gevraagd of u wijzigingen op de computer wilt toestaan.

    ![Welkom bij de wizard](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Selecteer op de pagina **certificaat archief** **alle certificaten in het onderstaande archief opslaan** en selecteer vervolgens **Bladeren** om het certificaat archief te kiezen.

    ![Certificaat opslag](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Selecteer in **certificaat archief selecteren** de optie **vertrouwde basis certificerings instanties** als doelmap voor de certificaten en selecteer vervolgens **OK**.

    ![Doelmap van certificaat](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Controleer in de **wizard Certificaat importeren** de map en selecteer vervolgens **volt ooien**.

    ![Controleren of het certificaat zich in de juiste map bevindt](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Nadat het importeren van het certificaat is bevestigd, meldt u zich aan bij de vCenter Server om te bevestigen dat uw verbinding is beveiligd.

### <a name="disable-https-certificate-validation"></a>HTTPS-certificaat validatie uitschakelen

Als u binnen uw organisatie beveiligde grenzen hebt en u het HTTPS-protocol niet wilt gebruiken tussen VMware-servers en de Azure Backup Server machine, schakelt u HTTPS als volgt uit:

1. Kopieer en plak de volgende tekst in een txt-bestand.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Sla het bestand op de Azure Backup Server computer op met de naam **DisableSecureAuthentication. reg**.

3. Dubbel klik op het bestand om de register vermelding te activeren.

## <a name="create-a-vmware-role"></a>Een VMware-rol maken

De Azure Backup Server moet een gebruikers account met machtigingen hebben voor toegang tot de ESXi-host van de v-Center-Server. Maak een VMware-rol met specifieke bevoegdheden en koppel vervolgens een gebruikers account aan de rol.

1. Meld u aan bij de vCenter Server (of ESXi-host als u geen gebruik maakt van vCenter Server).
2. Selecteer **beheer** in het deel venster **Navigator** .

    ![Beheer](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Selecteer in **beheerders**  >  **rollen** het pictogram rol toevoegen (het plus teken).

    ![Rol toevoegen](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Voer BackupAdminRole in bij naam van **Rolnaam van rol maken**  >  .  De rolnaam kan wille keurig zijn, maar deze moet herkenbaar zijn voor het doel van de functie.

5. Selecteer in de onderstaande tabel de bevoegdheden die u wilt samenvatten en selecteer vervolgens **OK**.  De nieuwe rol wordt weer gegeven in de lijst in het deel venster **rollen** .
   - Selecteer het pictogram naast het bovenliggende label om het bovenliggende item uit te vouwen en de onderliggende bevoegdheden weer te geven.
   - Als u de VirtualMachine-bevoegdheden wilt selecteren, moet u verschillende niveaus naar de bovenliggende onderliggende hiërarchie gaan.
   - U hoeft niet alle onderliggende bevoegdheden binnen een bovenliggende bevoegdheid te selecteren.

    ![Hiërarchie bovenliggende onderliggende bevoegdheden](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Rolmachtigingen

In de volgende tabel worden de bevoegdheden vastgelegd die u moet toewijzen aan het gebruikers account dat u maakt:

| Bevoegdheden voor het vCenter 6,5-gebruikers account                          | Bevoegdheden voor het vCenter 6,7-gebruikers account                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Data Store cluster.Configureren een Data Store-cluster                           | Data Store cluster.Configureren een Data Store-cluster                           |
| Data Store. AllocateSpace                                                    | Data Store. AllocateSpace                                                    |
| Data Store. bladeren in Data Store                                                 | Data Store. bladeren in Data Store                                                 |
| Bestands bewerkingen op laag niveau                                        | Bestands bewerkingen op laag niveau                                        |
| Global. Disable-methoden                                                     | Global. Disable-methoden                                                     |
| Global. enable-methoden                                                      | Global. enable-methoden                                                      |
| Global. licenties                                                            | Global. licenties                                                            |
| Global. log-gebeurtenis                                                           | Global. log-gebeurtenis                                                           |
| Global. aangepaste kenmerken beheren                                            | Global. aangepaste kenmerken beheren                                            |
| Global. Set Custom-kenmerk                                                | Global. Set Custom-kenmerk                                                |
| Host. lokale bewerkingen. Virtuele machine maken                               | Host. lokale bewerkingen. Virtuele machine maken                               |
| Netwerk. netwerk toewijzen                                                     | Netwerk. netwerk toewijzen                                                     |
| Resource. Virtuele machine aan resource groep toewijzen                          | Resource. Virtuele machine aan resource groep toewijzen                          |
| vApp. virtuele machine toevoegen                                                   | vApp. virtuele machine toevoegen                                                   |
| vApp. resource groep toewijzen                                                  | vApp. resource groep toewijzen                                                  |
| vApp. registratie ongedaan maken                                                            | vApp. registratie ongedaan maken                                                            |
| VirtualMachine.Configuratie. Apparaat toevoegen of verwijderen                         | VirtualMachine.Configuratie. Apparaat toevoegen of verwijderen                         |
| Virtuele machine.Configuratie. Lease van schijf                                   | Virtuele machine.Configuratie. Lease van schijf verkrijgen                           |
| Virtuele machine.Configuratie. Nieuwe schijf toevoegen                                 | Virtuele machine.Configuratie. Nieuwe schijf toevoegen                                 |
| Virtuele machine.Configuratie. Gevanceerde                                     | Virtuele machine.Configuratie. Geavanceerde configuratie                       |
| Virtuele machine.Configuratie. Bijhouden van schijf wijzigingen                         | Virtuele machine.Configuratie. Bijhouden van schijf wijzigingen in-/uitschakelen                  |
| Virtuele machine.Configuratie. Host-USB-apparaat                              | USB-apparaat virtuele machine.Configuration.Configureren host                    |
| Virtuele machine.Configuratie. Virtuele schijf uitbreiden                          | Virtuele machine.Configuratie. Virtuele schijf uitbreiden                          |
| Virtuele machine.Configuratie. Niet-eigendoms bestanden opvragen                          | Virtuele machine.Configuratie. Niet-eigendoms bestanden opvragen                          |
| Virtuele machine.Configuratie. Swapfile-plaatsing                           | Virtuele machine.Configuratie. Swapfile-plaatsing wijzigen                    |
| Virtuele machine. Gast bewerkingen. uitvoering van gast bewerkings programma         | Virtuele machine. Gast bewerkingen. uitvoering van gast bewerkings programma         |
| Virtuele machine. Gast bewerkingen. wijzigingen in de gast bewerking             | Virtuele machine. Gast bewerkingen. wijzigingen in de gast bewerking             |
| Virtuele machine. Gast bewerkingen. Query's voor de gast bewerking                   | Virtuele machine. Gast bewerkingen. Query's voor de gast bewerking                   |
| Virtuele machine. Communiceer. Apparaat-verbinding                            | Virtuele machine. Communiceer. Apparaat-verbinding                            |
| Virtuele machine. Communiceer. Beheer van gast besturingssystemen met VIX-API | Virtuele machine. Communiceer. Beheer van gast besturingssystemen met VIX-API |
| Virtuele machine. Communiceer. Uitschakelen                                    | Virtuele machine. Communiceer. Uitschakelen                                    |
| Virtuele machine. Inventaris. nieuwe maken                                      | Virtuele machine. Inventaris. nieuwe maken                                      |
| Virtuele machine. Inventaris. Remove                                          | Virtuele machine. Inventaris. Remove                                          |
| Virtuele machine. Inventarisatie. REGI ster                                        | Virtuele machine. Inventarisatie. REGI ster                                        |
| Virtuele machine. Inrichten. schijf toegang toestaan                            | Virtuele machine. Inrichten. schijf toegang toestaan                            |
| Virtuele machine. Inrichten. toegang tot bestand toestaan                            | Virtuele machine. Inrichten. toegang tot bestand toestaan                            |
| Virtuele machine. Inrichting. alleen-lezen schijf toegang toestaan                  | Virtuele machine. Inrichting. alleen-lezen schijf toegang toestaan                  |
| Virtuele machine. Inrichten. het downloaden van virtuele machines toestaan               | Virtuele machine. Inrichten. het downloaden van virtuele machines toestaan               |
| Virtuele machine. Beheer van moment opnamen. Momentopname maken                      | Virtuele machine. Beheer van moment opnamen. Momentopname maken                      |
| Virtuele machine. Beheer van moment opnamen. Moment opname verwijderen                       | Virtuele machine. Beheer van moment opnamen. Moment opname verwijderen                       |
| Virtuele machine. Beheer van moment opnamen. Herstellen naar moment opname                    | Virtuele machine. Beheer van moment opnamen. Herstellen naar moment opname                    |

> [!NOTE]
> De volgende tabel bevat de bevoegdheden voor de gebruikers accounts vCenter 6,0 en vCenter 5,5.

| Bevoegdheden voor het vCenter 6,0-gebruikers account | Bevoegdheden voor het vCenter 5,5-gebruikers account |
| --- | --- |
| Data Store. AllocateSpace | Netwerk. assign |
| Global. aangepaste kenmerken beheren | Data Store. AllocateSpace |
| Global. Set Custom-kenmerk | VirtualMachine.Config. Change tracking |
| Host. lokale bewerkingen. Virtuele machine maken | VirtualMachine. State. RemoveSnapshot |
| Netwerk. Netwerk toewijzen | VirtualMachine. State. CreateSnapshot |
| Resource. Virtuele machine aan resource groep toewijzen | VirtualMachine. provisioning. DiskRandomRead |
| Virtuele machine.Configuratie. Nieuwe schijf toevoegen | VirtualMachine. interactie. uitgeschakeld |
| Virtuele machine.Configuratie. Gevanceerde | VirtualMachine. Inventory. Create |
| Virtuele machine.Configuratie. Bijhouden van schijf wijzigingen | VirtualMachine.Config. AddNewDisk |
| Virtuele machine.Configuratie. Host-USB-apparaat | VirtualMachine.Config. HostUSBDevice |
| Virtuele machine.Configuratie. Niet-eigendoms bestanden opvragen | VirtualMachine.Config. AdvancedConfig |
| Virtuele machine.Configuratie. Swapfile-plaatsing | VirtualMachine.Config. SwapPlacement |
| Virtuele machine. Interactie. uit | Global. ManageCustomFields |
| Virtuele machine. Tell. Nieuwe maken |   |
| Virtuele machine. Inrichten. schijf toegang toestaan |   |
| Virtuele machine. Inrichtings. Alleen-lezen schijf toegang toestaan |   |
| Virtuele machine. Beheer van moment opnamen. Moment opname maken |   |
| Virtuele machine. Beheer van moment opnamen. Moment opname verwijderen |   |

## <a name="create-a-vmware-account"></a>Een VMware-account maken

1. Selecteer in vCenter Server **Navigator** venster **gebruikers en groepen**. Als u vCenter Server niet gebruikt, maakt u het account op de juiste ESXi-host.

    ![Optie gebruikers en groepen](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Het deel venster **vCenter-gebruikers en groepen** wordt weer gegeven.

2. Selecteer in het deel venster **vCenter-gebruikers en-groepen** het tabblad **gebruikers** en selecteer vervolgens het pictogram gebruikers toevoegen (het plus teken).

    ![het deel venster vCenter-gebruikers en-groepen](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Voeg in het dialoog venster **nieuwe gebruiker** de gebruikers gegevens > **OK** toe. In deze procedure is de gebruikers naam BackupAdmin.

    ![Het dialoog venster nieuwe gebruiker](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Als u het gebruikers account wilt koppelen aan de rol, selecteert u **algemene machtigingen** in het deel venster **Navigator** . Selecteer in het deel venster **algemene machtigingen** het tabblad **beheren** en selecteer vervolgens het pictogram toevoegen (het plus teken).

    ![Paneel voor algemene machtigingen](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Selecteer in **algemene machtiging hoofdmap-toevoegen machtiging** **toevoegen** om de gebruiker of groep te kiezen.

    ![Gebruiker of groep selecteren](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Kies in **gebruikers/groepen selecteren** de optie **BackupAdmin**  >  **toevoegen**. In **gebruikers** wordt de notatie *domein\gebruikersnaam* gebruikt voor het gebruikers account. Als u een ander domein wilt gebruiken, kiest u het in de lijst **domein** . Selecteer **OK** om de geselecteerde gebruikers toe te voegen aan het dialoog venster **machtiging toevoegen** .

    ![BackupAdmin-gebruiker toevoegen](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Selecteer in **toegewezen rol** in de vervolg keuzelijst de optie **BackupAdminRole**  >  **OK**.

    ![Gebruiker toewijzen aan rol](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Op het tabblad **beheren** in het deel venster **algemene machtigingen** worden het nieuwe gebruikers account en de bijbehorende rol weer gegeven in de lijst.

## <a name="add-the-account-on-azure-backup-server"></a>Het account toevoegen op Azure Backup Server

1. Open Azure Backup Server. Als u het pictogram niet op het bureau blad kunt vinden, opent u Microsoft Azure Backup in de lijst met apps.

    ![Azure Backup Server pictogram](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Selecteer in de console Azure backup server **beheer**  >   **productie servers**  >  **VMware beheren**.

    ![Azure Backup Server-console](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Selecteer in het dialoog venster **referenties beheren** de optie **toevoegen**.

    ![Het dialoog venster referenties beheren](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Voer bij **referentie toevoegen** een naam en een beschrijving in voor de nieuwe referentie en geef de gebruikers naam en het wacht woord op die u hebt gedefinieerd op de VMware-Server. De naam *Contoso vCenter-referentie* wordt gebruikt om de referentie in deze procedure te identificeren. Als de VMware-Server en Azure Backup Server zich niet in hetzelfde domein bevinden, geeft u het domein op in de gebruikers naam.

    ![Dialoog venster Azure Backup Server referentie toevoegen](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Selecteer **toevoegen** om de nieuwe referentie toe te voegen.

    ![Nieuwe referenties toevoegen](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Voeg de vCenter Server toe

Voeg de vCenter Server toe aan Azure Backup Server.

1. Selecteer in de Azure backup server-console de optie **beheer**  >  **productie servers**  >  **toevoegen**.

    ![Wizard voor het toevoegen van productie servers](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Selecteer in de **wizard productie server toevoegen** de  >  pagina **type productie server** , selecteer VMware- **servers** en selecteer vervolgens **volgende**.

    ![Wizard voor het toevoegen van productie servers](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Geef in **computers selecteren**  **Server naam/IP-adres** de FQDN of het IP-adres van de VMware-Server op. Als alle ESXi-servers worden beheerd door dezelfde vCenter, geeft u de vCenter-naam op. Voeg anders de ESXi-host toe.

    ![VMware-Server opgeven](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Voer bij **SSL-poort** de poort in die wordt gebruikt om te communiceren met de VMware-Server. 443 is de standaard poort, maar u kunt deze wijzigen als uw VMware-Server luistert op een andere poort.

5. Selecteer in **referenties opgeven** de referentie die u eerder hebt gemaakt.

    ![Referentie opgeven](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Selecteer **toevoegen** om de VMware-Server toe te voegen aan de lijst met servers. Selecteer vervolgens **Volgende**.

    ![VMWare-Server en-referentie toevoegen](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Selecteer op de pagina **samen vatting** **toevoegen** om de VMware-Server toe te voegen aan Azure backup server. De nieuwe server wordt onmiddellijk toegevoegd, maar er is geen agent nodig op de VMware-Server.

    ![VMware-Server toevoegen aan Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Controleer de instellingen op de pagina **volt ooien** .

   ![Pagina volt ooien](./media/backup-azure-backup-server-vmware/summary-screen.png)

Als u meerdere ESXi-hosts hebt die niet worden beheerd door de vCenter-Server of als u meerdere exemplaren van vCenter Server hebt, moet u de wizard opnieuw uitvoeren om de servers toe te voegen.

## <a name="configure-a-protection-group"></a>Een beveiligings groep configureren

VMware-Vm's toevoegen voor back-up. Beveiligings groepen verzamelen meerdere Vm's en passen dezelfde gegevens retentie en back-upinstellingen toe op alle virtuele machines in de groep.

1. Selecteer in de Azure Backup Server-console de optie **beveiliging**> **Nieuw**.

    ![Open de wizard nieuwe beveiligings groep maken](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Selecteer **volgende** op de welkomst pagina van de wizard **nieuwe beveiligings groep maken** .

    ![Dialoog venster Wizard nieuwe beveiligings groep maken](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Selecteer op de pagina **type beveiligings groep selecteren** de optie **servers** en selecteer vervolgens **volgende**. De pagina **groeps leden selecteren** wordt weer gegeven.

1. In **groeps leden selecteren** selecteert u de virtuele machines (of VM-mappen) waarvan u een back-up wilt maken. Selecteer vervolgens **Volgende**.

    - Wanneer u een map selecteert, worden er ook Vm's of mappen in die map geselecteerd voor back-up. U kunt de selectie van mappen of Vm's die u niet wilt maken, uitschakelen.
1. Als er al een back-up van een virtuele machine of map wordt gemaakt, kunt u deze niet selecteren. Dit zorgt ervoor dat er geen dubbele herstel punten worden gemaakt voor een virtuele machine.

    ![Groeps leden selecteren](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Voer op de pagina **methode voor gegevens beveiliging selecteren** een naam in voor de beveiligings groep en beveiligings instellingen. Als u een back-up wilt maken naar Azure, stelt u kortetermijnbeveiliging op **schijf** in en schakelt u online beveiliging in. Selecteer vervolgens **Volgende**.

    ![Methode voor gegevensbeveiliging selecteren](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. In **Short-Term doelen opgeven** geeft u op hoe lang u een back-up van gegevens wilt opslaan op schijf.
   - Geef in **Bewaar termijn** op hoeveel dagen schijf herstel punten moeten worden bewaard.
   - Geef bij **synchronisatie frequentie** op hoe vaak schijf herstel punten worden gemaakt.
       - Als u geen back-upinterval wilt instellen, kunt u **net vóór een herstel punt** controleren of een back-up wordt uitgevoerd vlak voordat elk herstel punt is gepland.
       - Back-ups op korte termijn zijn volledige back-ups en niet incrementeel.
       - Selecteer **wijzigen** om de tijden of datums te wijzigen wanneer back-ups op korte termijn worden uitgevoerd.

         ![Doelen voor de korte termijn opgeven](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Controleer in **schijf toewijzing controleren** de schijf ruimte die is vereist voor de back-ups van de virtuele machine. voor de Vm's.

   - De aanbevolen schijf toewijzingen zijn gebaseerd op de Bewaar termijn die u hebt opgegeven, het type werk belasting en de grootte van de beveiligde gegevens. Breng de gewenste wijzigingen aan en selecteer **volgende**.
   - **Gegevens grootte:** Grootte van de gegevens in de beveiligings groep.
   - **Schijf ruimte:** De aanbevolen hoeveelheid schijf ruimte voor de beveiligings groep. Als u deze instelling wilt wijzigen, moet u de totale ruimte toewijzen die iets groter is dan de hoeveelheid waarmee u de gegevens bronnen wilt schatten.
   - **Gegevens opzoeken:** Als u co-locatie inschakelt, kunnen meerdere gegevens bronnen in de beveiliging worden toegewezen aan één replica-en herstel punt volume. De co-locatie wordt niet ondersteund voor alle werk belastingen.
   - **Automatisch verg Roten:** Als u deze instelling inschakelt en de gegevens in de beveiligde groep groter worden dan de initiële toewijzing, Azure Backup Server probeert de schijf grootte met 25 procent te verhogen.
   - **Details van opslag groep:** Hier wordt de status van de opslag groep weer gegeven, met inbegrip van de totale en resterende schijf grootte.

    ![Schijf toewijzing controleren](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Geef in de pagina **methode voor maken van replica selecteren** op hoe u de eerste back-up wilt maken en selecteer vervolgens **volgende**.
   - De standaard instelling is **automatisch via het netwerk** en **nu**.
   - Als u de standaard waarde gebruikt, raden we u aan een rustige tijd op te geven. Kies **later** en geef een dag en tijd op.
   - Voor grote hoeveel heden gegevens of minder dan optimale netwerk omstandigheden kunt u overwegen om de gegevens offline te repliceren met behulp van Verwissel bare media.

    ![Methode voor maken van replica selecteren](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Selecteer in **Opties voor consistentie controle** hoe en wanneer de consistentie controles moeten worden geautomatiseerd. Selecteer vervolgens **Volgende**.
      - U kunt consistentie controles uitvoeren wanneer de replica gegevens inconsistent worden, of volgens een ingesteld schema.
      - Als u geen automatische consistentie controles wilt configureren, kunt u een hand matige controle uitvoeren. U doet dit door met de rechter muisknop op de beveiligings groep te klikken > **consistentie controle uit te voeren**.

1. Selecteer op de pagina **gegevens voor online beveiliging opgeven** de vm's of de mappen van de virtuele machine waarvan u een back-up wilt maken. U kunt de leden afzonderlijk selecteren, of selecteer **Alles selecteren** om alle leden te kiezen. Selecteer vervolgens **Volgende**.

    ![Gegevens voor online beveiliging opgeven](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Op de pagina **online back-upschema opgeven** geeft u op hoe vaak u een back-up wilt maken van gegevens van de lokale opslag naar Azure.

    - De Cloud herstel punten voor de gegevens worden gegenereerd volgens het schema. Selecteer vervolgens **Volgende**.
    - Nadat het herstel punt is gegenereerd, wordt het overgedragen naar de Recovery Services kluis in Azure.

    ![Online back-upschema opgeven](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geef op de pagina **online retentie beleid opgeven** op hoe lang u de herstel punten wilt bewaren die zijn gemaakt op basis van de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups naar Azure. Selecteer **volgende**.

    - Er is geen tijds limiet voor hoe lang u gegevens in azure kunt blijven gebruiken.
    - De enige limiet is dat u niet meer dan 9999 herstel punten per beveiligd exemplaar kunt hebben. In dit voor beeld is het beveiligde exemplaar de VMware-Server.

    ![Online Bewaar beleid opgeven](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Controleer de instellingen op de pagina **samen vatting** en selecteer vervolgens **groep maken**.

    ![Lid van beveiligings groep en samen vatting van instellingen](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>VMware parallelle back-ups

>[!NOTE]
> Deze functie is van toepassing op MABS v3 UR1.

Met eerdere versies van MABS zijn parallelle back-ups alleen uitgevoerd in verschillende beveiligings groepen. Met MABS v3 UR1 zijn alle back-ups van uw VMWare-Vm's binnen één beveiligings groep parallel, waardoor back-ups van virtuele machines sneller kunnen worden uitgevoerd. Alle VMWare Delta-replicatie taken worden parallel uitgevoerd. Het aantal taken dat parallel moet worden uitgevoerd, is standaard ingesteld op 8.

U kunt het aantal taken wijzigen met behulp van de register sleutel zoals hieronder wordt weer gegeven (dit is standaard niet aanwezig):

**Sleutelpad:**`Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**Sleutel type**: DWORD-waarde (32-bits).

> [!NOTE]
> U kunt het aantal taken naar een hogere waarde wijzigen. Als u het aantal taken instelt op 1, worden replicatie taken serieel uitgevoerd. Als u het getal wilt verhogen naar een hogere waarde, moet u rekening houden met de VMWare-prestaties. Houd rekening met het aantal gebruikte resources en aanvullende gebruiks vereisten op VMWare vSphere server en bepaal het aantal Delta-replicatie taken dat parallel moet worden uitgevoerd. Deze wijziging is ook van invloed op de nieuw gemaakte beveiligings groepen. Voor bestaande beveiligings groepen moet u tijdelijk een andere virtuele machine toevoegen aan de beveiligings groep. Hiermee wordt de configuratie van de beveiligings groep dienovereenkomstig bijgewerkt. U kunt deze virtuele machine uit de beveiligings groep verwijderen nadat de procedure is voltooid.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Ga als volgt te werk om een back-up te maken van vSphere 6,7:

- TLS 1,2 inschakelen op de MABS-server

>[!NOTE]
>Voor VMWare 6,7 werd TLS ingeschakeld als communicatie protocol.

- Stel de register sleutels als volgt in:

```text
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

## <a name="exclude-disk-from-vmware-vm-backup"></a>Schijf uitsluiten van de back-up van de VMware-VM

> [!NOTE]
> Deze functie is van toepassing op MABS v3 UR1.

Met MABS v3 UR1 kunt u de specifieke schijf uitsluiten van de back-up van de virtuele VMware-machine. Het configuratie script **ExcludeDisk.ps1** bevindt zich in de `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` .

Volg de onderstaande stappen om de schijf uitsluiting te configureren:

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>De VMWare-VM en schijf Details identificeren die moeten worden uitgesloten

  1. Ga in de VMware-console naar de VM-instellingen waarvoor u de schijf wilt uitsluiten.
  2. Selecteer de schijf die u wilt uitsluiten en noteer het pad voor de schijf.

        Als u bijvoorbeeld de harde schijf 2 wilt uitsluiten van de TestVM4, is het pad voor harde schijf 2 **[datastore1] TestVM4/TestVM4 \_ 1. vmdk**.

        ![De vaste schijf die moet worden uitgesloten](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>MABS-server configureren

Ga naar de MABS-server waarop de virtuele VMware-machine is geconfigureerd voor beveiliging om schijf uitsluiting te configureren.

  1. Bekijk de details van de VMware-host die wordt beveiligd op de MABS-server.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. Selecteer de VMware-host en geef een lijst van de Vm's beveiliging voor de VMware-host op.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. Selecteer de virtuele machine waarvoor u een schijf wilt uitsluiten.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. Als u de schijf wilt uitsluiten, gaat u naar de `Bin` map en voert u het *ExcludeDisk.ps1* script uit met de volgende para meters:

        > [!NOTE]
        > Voordat u deze opdracht uitvoert, stopt u de DPMRA-service op de MABS-server. Als dat niet het geval is, wordt de uitsluitings lijst niet bijgewerkt met het script. Zorg ervoor dat er geen taken worden uitgevoerd voordat u de service stopt.

     **Als u de schijf wilt toevoegen aan of verwijderen uit de uitsluiting, voert u de volgende opdracht uit:**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **Voorbeeld**:

     Voer de volgende opdracht uit om de schijf uitsluiting voor TestVM4 toe te voegen:

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. Controleer of de schijf is toegevoegd voor uitsluiting.

     **Als u de bestaande uitsluiting voor specifieke Vm's wilt weer geven, voert u de volgende opdracht uit:**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **Voorbeeld**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     Wanneer u de beveiliging voor deze virtuele machine configureert, wordt de uitgesloten schijf niet weer gegeven tijdens de beveiliging.

        > [!NOTE]
        > Als u deze stappen uitvoert voor een al beveiligde VM, moet u de consistentie controle hand matig uitvoeren na het toevoegen van de schijf voor uitsluiting.

### <a name="remove-the-disk-from-exclusion"></a>De schijf uit de uitsluiting verwijderen

Als u de schijf uit de uitsluiting wilt verwijderen, voert u de volgende opdracht uit:

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [hand leiding voor probleem oplossing voor Azure backup server](./backup-azure-mabs-troubleshoot.md)voor het oplossen van problemen met het instellen van back-ups.
