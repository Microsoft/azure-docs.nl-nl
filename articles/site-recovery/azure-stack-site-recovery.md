---
title: Azure Stack Vm's repliceren naar Azure met behulp van Azure Site Recovery | Microsoft Docs
description: Meer informatie over het instellen van herstel na nood gevallen voor Azure voor Azure Stack Vm's met de Azure Site Recovery-service.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 300b239de74c7d21e03eb0c3b059663b5074964c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728382"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Virtuele Azure-machines repliceren naar Azure

In dit artikel wordt beschreven hoe u met behulp van de [Azure site Recovery-service](site-recovery-overview.md)herstel na nood gevallen instelt Azure stack Vm's naar Azure.

Site Recovery draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR). De service zorgt ervoor dat de werk belasting van uw virtuele machines beschikbaar blijven als verwacht en onverwachte storingen optreden.

- Site Recovery organiseert en beheert de replicatie van virtuele machines naar Azure Storage.
- Wanneer er een storing optreedt in de primaire site, gebruikt u Site Recovery voor het uitvoeren van een failover naar Azure.
- Bij een failover worden virtuele Azure-machines gemaakt op basis van de opgeslagen VM-gegevens en kunnen gebruikers de workloads blijven gebruiken die worden uitgevoerd op die virtuele machines van Azure.
- Als alles weer actief is, kunt u failback van virtuele Azure-machines naar uw primaire site uitvoeren en de replicatie naar Azure-opslag starten.


In dit artikel leert u het volgende:

> [!div class="checklist"]
> * **Stap 1: virtuele Azure stack-Vm's voorbereiden voor replicatie**. Controleer of de Vm's voldoen aan de Site Recovery vereisten en bereid u voor op de installatie van de Site Recovery Mobility-service. Deze service wordt geïnstalleerd op elke virtuele machine die u wilt repliceren.
> * **Stap 2: Stel een Recovery Services kluis** in. Stel een kluis in voor Site Recovery en geef op wat u wilt repliceren. Site Recovery onderdelen en acties worden geconfigureerd en beheerd in de kluis.
> * **Stap 3: de bron replicatie omgeving instellen**. Stel een Site Recovery configuratie server in. De configuratie server is een enkele Azure Stack VM waarop alle onderdelen worden uitgevoerd die nodig zijn voor Site Recovery. Nadat u de configuratie server hebt ingesteld, registreert u deze in de kluis.
> * **Stap 4: Stel de doel replicatie omgeving** in. Selecteer uw Azure-account en het Azure-opslag account en het netwerk dat u wilt gebruiken. Tijdens de replicatie worden VM-gegevens gekopieerd naar Azure Storage. Na een failover worden virtuele Azure-machines toegevoegd aan het opgegeven netwerk.
> * **Stap 5: replicatie inschakelen**. Configureer replicatie-instellingen en Schakel replicatie in voor Vm's. De Mobility-service wordt geïnstalleerd op een virtuele machine wanneer replicatie is ingeschakeld. Site Recovery voert een initiële replicatie van de virtuele machine uit, waarna de replicatie wordt gestart.
> * **Stap 6: een nood herstel analyse uitvoeren**: nadat de replicatie is uitgevoerd, controleert u of de failover werkt zoals verwacht door een analyse uit te voeren. Als u de analyse wilt initiëren, voert u een testfailover uit in Site Recovery. De testfailover heeft geen invloed op uw productie omgeving.

Wanneer u deze stappen hebt voltooid, kunt u een volledige failover uitvoeren naar Azure als en wanneer dat nodig is.

## <a name="architecture"></a>Architectuur

![In het diagram worden Recovery Services kluizen weer gegeven voor twee tenants in Clouds die zijn gekoppeld aan Tenant abonnementen, zowel op een gemeen schappelijke Azure Stack-infra structuur.](./media/azure-stack-site-recovery/architecture.png)

**Locatie** | **Onderdeel** |**Details**
--- | --- | ---
**Configuratieserver** | Wordt uitgevoerd op een enkele Azure Stack VM. | In elk abonnement stelt u een VM van een configuratie server in. Deze VM voert de volgende Site Recovery onderdelen uit:<br/><br/> -Configuratie server: coördineert de communicatie tussen on-premises en Azure, en beheert de gegevens replicatie. -Proces server: fungeert als replicatie gateway. Het ontvangt replicatie gegevens, optimaliseert met caching, compressie en versleuteling. en verzendt deze naar Azure Storage.<br/><br/> Als de Vm's die u wilt repliceren de hieronder vermelde limieten overschrijden, kunt u een afzonderlijke zelfstandige proces server instellen. [Meer informatie](vmware-azure-set-up-process-server-scale.md).
**Mobility-service** | Geïnstalleerd op elke virtuele machine die u wilt repliceren. | In de stappen in dit artikel wordt een account voor bereid zodat de Mobility-service automatisch op een VM wordt geïnstalleerd wanneer replicatie is ingeschakeld. Als u de service niet automatisch wilt installeren, zijn er een aantal andere methoden die u kunt gebruiken. [Meer informatie](vmware-azure-install-mobility-service.md).
**Azure** | In azure hebt u een Recovery Services kluis, een opslag account en een virtueel netwerk nodig. |  Gerepliceerde gegevens worden opgeslagen in het opslag account. Virtuele Azure-machines worden toegevoegd aan het Azure-netwerk wanneer er een failover wordt uitgevoerd.


De replicatie werkt als volgt:

1. In de kluis geeft u de bron en het doel van de replicatie op, stelt u de configuratie server in, maakt u een replicatie beleid en schakelt u replicatie in.
2. De Mobility-service is op de computer geïnstalleerd (als u de push-installatie hebt gebruikt) en de computers beginnen met de replicatie volgens het replicatie beleid.
3. Er wordt een eerste kopie van de server gegevens gerepliceerd naar Azure Storage.
4. Wanneer de initiële replicatie is voltooid, begint de replicatie van Delta wijzigingen naar Azure. Bijgehouden wijzigingen voor een machine worden opgeslagen in een .hrl-bestand.
5. De configuratie Server organiseert het replicatie beheer met Azure (poort HTTPS 443 uitgaand).
6. De proces server ontvangt gegevens van de bron machines, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage (poort 443 uitgaand).
7. Gerepliceerde computers communiceren met de configuratie server (poort HTTPS 443 inkomend voor replicatie beheer). Computers verzenden replicatie gegevens naar de proces server (poort HTTPS 9443 inkomend-kan worden gewijzigd).
8. Verkeer wordt via internet gerepliceerd naar de openbare eindpunten van Azure Storage. U kunt ook openbare peering van Azure ExpressRoute gebruiken. Het repliceren van verkeer via een site-naar-site-VPN van een on-premises site naar Azure wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Hier ziet u wat u nodig hebt om dit scenario in te stellen.

**Vereiste** | **Details**
--- | ---
**Azure-abonnements account** | Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan.
**Machtigingen voor het Azure-account** | Het Azure-account dat u gebruikt, heeft machtigingen nodig voor het volgende:<br/><br/> -Een Recovery service-kluis maken<br/><br/> -Maak een virtuele machine in de resource groep en het virtuele netwerk dat u gebruikt voor het scenario<br/><br/> -Schrijf naar het opslag account dat u opgeeft<br/><br/> Opmerking:<br/><br/> -Als u een account maakt, bent u de beheerder van uw abonnement en kunt u alle acties uitvoeren.<br/><br/> -Als u een bestaand abonnement gebruikt en u niet de beheerder bent, moet u met de beheerder samen werken om u machtigingen voor eigenaar of Inzender toe te wijzen.<br/><br/> -Als u meer gedetailleerde machtigingen nodig hebt, raadpleegt u [dit artikel](site-recovery-role-based-linked-access-control.md).
**Azure Stack VM** | U hebt een Azure Stack VM nodig in het Tenant abonnement, die wordt geïmplementeerd als de Site Recovery-configuratie server.


### <a name="prerequisites-for-the-configuration-server"></a>Vereisten voor de configuratie server

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Stap 1: Azure Stack Vm's voorbereiden

### <a name="verify-the-operating-system"></a>Het besturings systeem controleren

Zorg ervoor dat op de Vm's een van de besturings systemen wordt uitgevoerd die in de tabel worden samen vatting.


**Besturingssysteem** | **Details**
--- | ---
**64-bits Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (van SP1)
**CentOS** | 5,2 tot 5,11, 6,1 tot 6,9, 7,0 tot 7,3
**Ubuntu** | 14,04 LTS-server, 16,04 LTS-server. [Ondersteunde kernels](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions) controleren

### <a name="prepare-for-mobility-service-installation"></a>Voorbereiden op de installatie van de Mobility-service

Op elke VM die u wilt repliceren, moet de Mobility-service zijn geïnstalleerd. Controleer de VM-instellingen, zodat de proces server de service automatisch op de VM installeert wanneer replicatie is ingeschakeld.

#### <a name="windows-machines"></a>Windows-machines

- U hebt een netwerk verbinding nodig tussen de virtuele machine waarop u replicatie wilt inschakelen en de computer waarop de proces server wordt uitgevoerd (dit is standaard de configuratie Server-VM).
- U hebt een account met beheerders rechten (domein of lokaal) nodig op de computer waarvoor u replicatie inschakelt.
    - U geeft dit account op wanneer u Site Recovery instelt. De proces server gebruikt dit account voor het installeren van de Mobility-service wanneer replicatie is ingeschakeld.
    - Dit account wordt alleen gebruikt door Site Recovery voor de push-installatie en bij het bijwerken van de Mobility-service.
    - Als u geen domein account gebruikt, moet u toegangs beheer voor externe gebruikers uitschakelen op de virtuele machine:
        - Maak in het REGI ster DWORD-waarde **LocalAccountTokenFilterPolicy** onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Stel de waarde in op 1.
        - Als u dit wilt doen vanaf de opdracht prompt, typt u het volgende: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/t REG_DWORD/d 1**.
- In de Windows Firewall op de virtuele machine die u wilt repliceren, kunt u bestands-en printer deling en WMI toestaan.
    - U doet dit door **WF. msc** uit te voeren om de Windows Firewall-console te openen. Klik met de rechter muisknop op **inkomende regels**  >  **nieuwe regel**. Selecteer **vooraf gedefinieerd** en kies **Bestands-en printer deling** in de lijst. Voltooi de wizard en selecteer deze om de verbinding toe > **volt ooien**.
    - Voor domein computers kunt u een groeps beleidsobject gebruiken om dit te doen.


#### <a name="linux-machines"></a>Linux-machines

- Zorg ervoor dat er een netwerk verbinding is tussen de Linux-computer en de proces server.
- Op de computer waarvoor u replicatie inschakelt, hebt u een account nodig dat een hoofd gebruiker is op de Linux-bron server:
    - U geeft dit account op wanneer u Site Recovery instelt. De proces server gebruikt dit account voor het installeren van de Mobility-service wanneer replicatie is ingeschakeld.
    - Dit account wordt alleen gebruikt door Site Recovery voor de push-installatie en bij het bijwerken van de Mobility-service.
- Controleer of het bestand /etc/hosts op de Linux-bronserver vermeldingen bevat die de lokale hostnaam toewijzen aan IP-adressen die zijn gekoppeld aan alle netwerkadapters.
- Installeer de meest recente openssh, openssh-server en openssl-pakketten op de computer die u wilt repliceren.
- Zorg ervoor dat SSH (Secure Shell) is ingeschakeld en wordt uitgevoerd op poort 22.
- Schakel het SFTP-subsysteem en wachtwoordverificatie in het bestand sshd_config in:
    1. Als u dit wilt doen, meldt u zich aan als root.
    2. Zoek de regel die begint met **PasswordAuthentication**, in het bestand/etc/ssh/sshd_config. Verwijder opmerkingen bij de regel en wijzig de waarde in **ja**.
    3. Zoek de regel die begint met **Subsystem** en verwijder opmerkingen bij de regel.

        ![Linux Mobility service](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Start de service sshd opnieuw.


### <a name="note-the-vm-private-ip-address"></a>Noteer het privé-IP-adres van de VM

Voor elke computer die u wilt repliceren, zoekt u het IP-adres:

1. Klik in de Azure Stack Portal op de virtuele machine.
2. Klik in het menu **resource** op **netwerk interfaces**.
3. Noteer het privé-IP-adres.

    ![Privé IP-adres](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Stap 2: een kluis maken en een replicatie doel selecteren

1. Selecteer in de Azure Portal **een**  >  back-up van resource **beheer tools** maken  >  **en site Recovery**.
2. Voer in **Naam** een beschrijvende naam in om de kluis aan te duiden.
3. Maak of selecteer een resource groep in **resource groep**. We gebruiken **contosoRG**.
4. Voer in **locatie** de Azure-regio in. gebruiken we **Europa - west**.
5. Voor snelle toegang tot de kluis vanuit het dashboard, selecteert u **Aan dashboard vastmaken** > **Maken**.

   ![Een nieuwe kluis maken](./media/azure-stack-site-recovery/new-vault-settings.png)

   De nieuwe kluis wordt weergegeven in **Dashboard** > **Alle resources** en op de hoofdpagina **Recovery Services-kluizen**.

### <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. In **Recovery Services kluizen** > een kluis naam opgeven. We gebruiken **ContosoVMVault**.
2. Selecteer in **Aan de slag** Site Recovery. Selecteer vervolgens **Infrastructuur voorbereiden**.
3. In **Beveiligingsdoel** > **Waar bevinden de machines zich**, selecteert u **On-premises**.
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. In **zijn uw machines gevirtualiseerd**, selecteert u **niet gevirtualiseerd/Overig**. Selecteer vervolgens **OK**.

    ![Beveiligingsdoel](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Stap 3: de bron omgeving instellen

Stel de configuratie server machine in, Registreer deze in de kluis en ontdek de computers die u wilt repliceren.

1. Klik op **infra structuur**  >  **bron** voorbereiden.
2. Klik in **Bron voorbereiden** op **+Configuratieserver**.

    ![Scherm afbeelding van het dialoog venster + Configuratie server met het bericht "Klik op + Configuratie server in de opdracht balk hierboven om een..." te installeren.](./media/azure-stack-site-recovery/plus-config-srv.png)

3. Controleer bij **Server toevoegen** of **Configuratieserver** wordt weergegeven in **Servertype**.
5. Download het installatiebestand voor de Geïntegreerde Setup van Site Recovery.
6. Download de registratiesleutel voor de kluis. U hebt de registratie sleutel nodig wanneer u Unified Setup uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Scherm afbeelding van het dialoog venster server toevoegen met het server type ingesteld op Configuratie server en de knop kluis registratie sleutel downloaden gemarkeerd.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Run Azure Site Recovery Unified Setup

Als u de configuratie server wilt installeren en registreren, voert u een RDP-verbinding met de virtuele machine die u wilt gebruiken voor de configuratie server uit en voert u Unified Setup uit.

Voordat u begint, moet u ervoor zorgen dat de klok is [gesynchroniseerd met een tijd server](/windows-server/networking/windows-time-service/windows-time-service-top) op de VM voordat u begint. De installatie mislukt als de tijd meer dan vijf minuten van de lokale tijd is.

Installeer nu de configuratie server:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> De configuratie server kan ook worden geïnstalleerd vanaf de opdracht regel. [Meer informatie](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Het duurt 15 minuten of langer voordat de accountnaam wordt weergegeven in de portal. Selecteer **Configuratieservers** > **_servernaam_ *_ > _* Server vernieuwen**.

## <a name="step-4-set-up-the-target-environment"></a>Stap 4: de doel omgeving instellen

Selecteer en controleer doelbronnen.

1. Selecteer in **infrastructuur**  >  **doel** voorbereiden het Azure-abonnement dat u wilt gebruiken.
2. Geef het doelimplementatiemodel op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt. Als deze niet worden gevonden, moet u ten minste één opslag account en een virtueel netwerk maken om de wizard te volt ooien.


## <a name="step-5-enable-replication"></a>Stap 5: replicatie inschakelen

### <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Klik op **infra structuur**  >  **replicatie-instellingen** voorbereiden.
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op.
    - Herstel punten voor gerepliceerde gegevens worden gemaakt op basis van de tijd die is ingesteld.
    - Deze instelling heeft geen invloed op replicatie, die doorlopend is. Er wordt alleen een waarschuwing gegenereerd als de drempel waarde is bereikt zonder dat er een herstel punt wordt gemaakt.
4. Geef bij **Bewaar periode van het herstel punt** op hoe lang elk herstel punt moet worden bewaard. Gerepliceerde Vm's kunnen worden hersteld naar elk gewenst punt in het opgegeven tijd venster.
5. Geef in de frequentie van de **app-consistente moment opname** op hoe vaak toepassings consistente moment opnamen worden gemaakt.

    - Een app-consistente moment opname is een tijdgebonden moment opname van de app-gegevens in de virtuele machine.
    - Volume Shadow Copy Service (VSS) zorgt ervoor dat apps op de virtuele machine een consistente status hebben wanneer de moment opname wordt gemaakt.
6. Selecteer **OK** om het beleid te maken.


### <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

U kunt deze stap nu overs Laan. Klik in de vervolg keuzelijst **implementatie planning** op **Ja, ik heb dit gedaan**.



### <a name="enable-replication"></a>Replicatie inschakelen

Zorg ervoor dat u alle taken in [stap 1: computer voorbereiden](#step-1-prepare-azure-stack-vms)hebt voltooid. Schakel vervolgens replicatie als volgt in:

1. Selecteer **Toepassing repliceren** > **Bron**.
2. Selecteer in **Bron** de configuratieserver.
3. Selecteer bij **Type machine** de optie **Fysieke machines**.
4. Selecteer de processerver (configuratieserver). Klik vervolgens op **OK**.
5. Selecteer in **doel** het abonnement en de resource groep waarin u de vm's wilt maken na een failover. Kies het implementatie model dat u wilt gebruiken voor de virtuele machines waarvoor failover is uitgevoerd.
6. Selecteer het Azure-opslag account waarin u de gerepliceerde gegevens wilt opslaan.
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **nu configureren voor geselecteerde machines** om de netwerk instelling toe te passen op alle machines die u voor beveiliging selecteert. Selecteer **later configureren** als u het Azure-netwerk voor elke machine afzonderlijk wilt selecteren.
9. Klik op **fysieke computers** op **+ fysieke machine**. Geef de naam, het IP-adres en het type besturings systeem op van elke computer die u wilt repliceren.

    - Gebruik het interne IP-adres van de computer.
    - Als u het open bare IP-adres opgeeft, werkt de replicatie mogelijk niet zoals verwacht.

10. Selecteer in **Eigenschappen**  >  **eigenschappen configureren** het account dat door de proces server wordt gebruikt om de Mobility-service automatisch op de computer te installeren.
11. Controleer in **replicatie-instellingen**  >  **replicatie-instellingen configureren** of het juiste replicatie beleid is geselecteerd.
12. Klik op **Replicatie inschakelen**.
13. Volg de voortgang van de taak **beveiliging inschakelen** in **instellingen**  >  **taken**  >  **site Recovery taken**. Nadat de taak **Beveiliging voltooien** is uitgevoerd, is de machine klaar voor een mogelijke failover.

> [!NOTE]
> Site Recovery installeert Mobility Service wanneer replicatie wordt ingeschakeld voor een VM.
>
> Het kan 15 minuten of langer duren voordat wijzigingen zijn doorgevoerd en in de portal worden weergegeven.
>
> Voor het bewaken van virtuele machines die u toevoegt, controleert u de laatste detectietijd voor VM's in **Configuratieservers** > **Laatst contact om**. Als u VM's wilt toevoegen zonder te wachten op de geplande detectie, markeert u de configuratieserver (zonder deze te selecteren), en selecteert u **Vernieuwen**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Stap 6: een nood herstel analyse uitvoeren

U voert een testfailover naar Azure uit om ervoor te zorgen dat alles werkt zoals verwacht. Deze failover heeft geen invloed op uw productie omgeving.

### <a name="verify-machine-properties"></a>Computer eigenschappen verifiëren

Controleer voordat u een testfailover uitvoert de computer eigenschappen en zorg ervoor dat deze voldoen aan de [vereisten van Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). U kunt als volgt eigenschappen weer geven en wijzigen:

1. Klik in **Beveiligde items** op **Gerepliceerde items** > VM.
2. In het deelvenster **Gerepliceerd item** bevindt zich een overzicht van VM-informatie, status, en de laatste beschikbare herstelpunten. Klik op **Eigenschappen** om meer details te bekijken.
3. Wijzig in **Compute en netwerk** de instellingen naar wens.

    - U kunt de naam van de Azure-VM, de resource groep, de doel grootte, [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md)en de instellingen voor beheerde schijven wijzigen.
    - U kunt ook netwerk instellingen weer geven en wijzigen. Hieronder vallen het netwerk/subnet waaraan de Azure-VM is gekoppeld na een failover, en het IP-adres dat aan de virtuele machine wordt toegewezen.
1. Bekijk op **schijven** informatie over het besturings systeem en de gegevens schijven op de virtuele machine.


### <a name="run-a-test-failover"></a>Een testfailover uitvoeren

Wanneer u een testfailover uitvoert, gebeurt het volgende:

1. Er wordt een controle uitgevoerd om na te gaan of aan alle vereiste voorwaarden voor failover wordt voldaan.
2. De gegevens worden door de failover verwerkt met het opgegeven herstel punt:
    - **Laatst verwerkte**: er wordt een failover uitgevoerd van de machine naar het laatste herstel punt dat is verwerkt door site Recovery. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er een lage RTO (Recovery Time Objective) is.
    - **Nieuwste app-consistent**: er wordt een failover uitgevoerd van de computer naar het nieuwste toepassings consistente herstel punt.
    - **Aangepast**: Selecteer het herstel punt dat wordt gebruikt voor failover.

3. Er wordt een virtuele machine van Azure gemaakt met behulp van de verwerkte gegevens.
4. Met testfailover kunnen Azure-Vm's die tijdens de analyse zijn gemaakt, automatisch worden opgeruimd.

Voer als volgt een testfailover uit voor een virtuele machine:

1. Klik in **Instellingen** > **Gerepliceerde items** op de VM **+Failover testen**.
2. Voor dit scenario selecteren we het **meest recente verwerkte** herstel punt.
3. Selecteer in **failover testen** het doel-Azure-netwerk.
4. Klik op **OK** om te beginnen met de failover.
5. Houd de voortgang bij door te klikken op de virtuele machine om de eigenschappen te openen. U kunt ook op de taak **failover testen** in *kluis naam*  >  **instellingen**  >  **taken**  > **site Recovery taken**.
6. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de virtuele machine de juiste grootte heeft, is verbonden met het juiste netwerk en wordt uitgevoerd.
7. Nu moet u verbinding maken met de gerepliceerde virtuele machine in Azure. [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Als u VM's van Azure wilt verwijderen die tijdens de failovertest zijn gemaakt, klikt u in de VM op **Failovertest opschonen**. Sla in **notities** alle opmerkingen op die zijn gekoppeld aan de testfailover.

## <a name="fail-over-and-fail-back"></a>Failover en failback

Nadat u de replicatie hebt ingesteld en een analyse hebt uitgevoerd om te controleren of alles werkt, kunt u de machines naar Azure mislukken als dat nodig is.

Als u na de failover een failover wilt uitvoeren, moet u eerst [verbinding maken](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) met de machine voordat u begint.

Voer een failover als volgt uit:


1. Klik in **instellingen**  >  **gerepliceerde items** op de machine > **failover**.
2. Selecteer het herstel punt dat u wilt gebruiken.
3. Selecteer in **failover testen** het doel-Azure-netwerk.
4. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Met deze instelling probeert Site Recovery de bron machine af te sluiten voordat de failover wordt gestart. Failover wordt echter voortgezet, zelfs als afsluiten mislukt.
5. Klik op **OK** om te beginnen met de failover. U kunt de voortgang van de failover volgen op de pagina **Taken**.
6. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Als u hebt voor bereid om verbinding te maken na een failover, controleert u of de virtuele machine de juiste grootte heeft, is verbonden met het juiste netwerk en wordt uitgevoerd.
7. Nadat u de virtuele machine hebt geverifieerd, klikt u op **door voeren** om de failover te volt ooien. Hiermee worden alle beschik bare herstel punten verwijderd.

> [!WARNING]
> Annuleer een failover die in voortgang is niet: voordat de failover wordt gestart, wordt de VM-replicatie gestopt. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.


### <a name="fail-back-to-azure-stack"></a>Failback naar Azure Stack

Als uw primaire site weer actief is, kunt u een failback uitvoeren van Azure naar Azure Stack. Volg hiervoor de stappen die [hier](/azure-stack/operator/site-recovery-failback)worden beschreven.

## <a name="conclusion"></a>Conclusie

In dit artikel worden Azure Stack Vm's gerepliceerd naar Azure. Met replicatie op locatie hebben we een herstel analyse voor nood gevallen uitgevoerd om ervoor te zorgen dat de failover naar Azure goed werkt zoals verwacht. Het artikel bevat ook stappen voor het uitvoeren van een volledige failover naar Azure en het uitvoeren van een failback naar Azure Stack.

## <a name="next-steps"></a>Volgende stappen

Nadat de failover is uitgevoerd, kunt u de virtuele machine opnieuw beveiligen en deze opnieuw repliceren naar Azure om dit te doen. Herhaal de stappen in dit artikel.