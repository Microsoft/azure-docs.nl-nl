---
title: Herstel na nood geval voor Hyper-V instellen op een secundaire site met Azure Site Recovery
description: Informatie over het instellen van herstel na noodgevallen voor Hyper-V-VM's tussen uw on-premises sites met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: how-to
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: b2164f8927e5c3224f8b07c30d057f48fb7bbc32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87495965"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Herstel na noodgevallen instellen voor Hyper-V-VM's naar een secundaire on-premises site

De [Azure site Recovery](site-recovery-overview.md) -service draagt bij aan uw strategie voor herstel na nood gevallen door replicatie, failover en failback van on-premises machines en virtuele Azure-machines (vm's) te beheren en te organiseren.

In dit artikel wordt uitgelegd hoe u herstel na noodgevallen naar een secundaire site instelt voor on-premises Hyper-V-VM's die worden beheerd in System Center VMM-clouds (Virtual Machine Manager). In dit artikel leert u het volgende:

> [!div class="checklist"]
> * On-premises VMM-servers en Hyper-V-hosts voorbereiden
> * Een Recovery Services-kluis maken voor Site Recovery 
> * De replicatiebron- en doelomgeving instellen. 
> * Netwerktoewijzing instellen 
> * Een replicatiebeleid maken
> * Replicatie inschakelen voor een VM


## <a name="prerequisites"></a>Vereisten

Vereisten om dit scenario te voltooien:

- De [architectuur en onderdelen voor dit scenario](hyper-v-vmm-architecture.md) doornemen.
- Controleren of de VMM-servers en Hyper-V-hosts voldoen aan [ondersteuningsvereisten](hyper-v-vmm-secondary-support-matrix.md).
- Controleren of de virtuele machines die u wilt repliceren voldoen aan [ondersteuning voor gerepliceerde machines](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- VMM-servers voorbereiden op netwerktoewijzing.

### <a name="prepare-for-network-mapping"></a>Voorbereiden op netwerktoewijzing

Bij [netwerktoewijzing](hyper-v-vmm-network-mapping.md) worden on-premises VMM-netwerken met virtuele machines toegewezen aan bron- en doelclouds. Bij toewijzing gebeurt het volgende:

- Virtuele machines worden verbonden met de juiste VM-doelnetwerken na een failover. 
- Gerepliceerde VM's worden optimaal op Hyper-V-doelhostservers geplaatst. 
- Als u geen netwerk toewijzing configureert, worden replica-Vm's na een failover niet verbonden met een VM-netwerk.

Bereid VMM als volgt voor:

1. Zorg ervoor dat u [logische VMM-netwerken](/system-center/vmm/network-logical) op de VMM-bron- en doelserver hebt.
    - Het logische netwerk op de bronserver moet worden gekoppeld aan de broncloud waarin de Hyper-V-hosts zich bevinden.
    - Het logische netwerk op de doelserver moet worden gekoppeld aan de doelcloud.
1. Zorg ervoor dat u [VMM-netwerken](/system-center/vmm/network-virtual) op de VMM-bron- en doelserver hebt. VM-netwerken moeten zijn gekoppeld aan het logische netwerk op elke locatie.
2. Verbind VM's op de Hyper-V-bronhosts met het VM-bronnetwerk. 


## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Een beveiligingsdoel kiezen

Selecteer wat u wilt repliceren en waar u naar wilt repliceren.

1. Klik op **site Recovery**  >  **stap 1:**  >  **doel infrastructuur beveiliging** voorbereiden.
2. Selecteer **Naar herstelsite** en selecteer **Ja, met Hyper-V**.
3. Selecteer **Ja** om aan te geven dat u VMM gebruikt voor het beheren van de Hyper-V-hosts.
4. Selecteer **Ja** als u een secundaire VMM-server hebt. Als u replicatie tussen clouds op een enkele VMM-server implementeert, klikt u op **Nee**. Klik vervolgens op **OK**.


## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Installeer de Azure Site Recovery-provider op de VMM-servers en detecteer en registreer servers in de kluis.

1. Klik op **infra structuur**  >  **bron** voorbereiden.
2. Klik in **Bron voorbereiden** op **+ VMM** om een VMM-server toe te voegen.
3. Controleer in **Server toevoegen** of **System Center VMM-server** wordt weergegeven bij **Servertype**.
4. Download het installatieprogramma voor de Azure Site Recovery-provider.
5. Download de registratiesleutel. U hebt deze nodig tijdens de installatie van de provider. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Scherm afbeelding van de opties voor het downloaden van provider-en registratie sleutel.](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Installeer de provider op elke VMM-server. U hoeft niets expliciet te installeren op de Hyper-V-hosts.

### <a name="install-the-azure-site-recovery-provider"></a>De Azure Site Recovery-provider installeren

1. Voer het providerinstallatiebestand uit op elke VMM-server. Als VMM is geïmplementeerd in een cluster, voert u de installatie de eerste keer als volgt uit:
    -  Installeer de provider op een actief knooppunt en voltooi de installatie voor het registreren van de VMM-server in de kluis.
    - Installeer de provider vervolgens op de andere knooppunten. Clusterknooppunten moeten alle dezelfde versie van de provider uitvoeren.
2. Tijdens de installatie wordt een aantal controles uitgevoerd en om toestemming gevraagd om de VMM-service te stoppen. De VMM-service wordt automatisch opnieuw gestart wanneer de installatie is voltooid. Als u op een VMM-cluster installeert, wordt u gevraagd om de clusterrol te stoppen.
3. In **Microsoft Update** kunt u updates inschakelen om aan te geven dat de providerupdates volgens uw Microsoft Update-beleid worden geïnstalleerd.
4. Accepteer of wijzig in **Installatie** de standaardinstallatielocatie en klik op **Installeren**.
5. Wanneer de installatie is voltooid, klikt u op **Registreren** om de server in de kluis te registreren.

    ![Scherm afbeelding van de installatie van de provider, inclusief de installatie locatie.](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. Controleer in **Kluisnaam** de naam van de kluis waarin de server wordt geregistreerd. Klik op **Volgende**.
7. Geef bij **Proxyverbinding** op hoe de provider die wordt uitgevoerd op de VMM-server verbinding maakt met Azure.
   - U kunt opgeven dat de provider rechtstreeks met internet of via een proxyserver verbinding moet maken. Geef indien nodig proxyinstellingen op.
   - Als u een proxy gebruikt, wordt er automatisch een VMM RunAs-account (DRAProxyAccount) gemaakt met de opgegeven proxyreferenties. Configureer de proxyserver zodanig dat dit account kan worden geverifieerd. De RunAs-account instellingen kunnen worden gewijzigd in de VMM-console > **instellingen**  >  **beveiliging**  >  **uitvoeren als-accounts**.
   - Start de VMM-service opnieuw om de wijzigingen door te voeren.
8. Selecteer in **Registratiesleutel** de sleutel die u hebt gedownload en naar de VMM-server hebt gekopieerd.
9. De coderingsinstelling is niet relevant in dit scenario. 
10. Geef in **Servernaam** een beschrijvende naam op om de VMM-server in de kluis te identificeren. Geef in een cluster de VMM-clusterrolnaam op.
11. Geef bij **Cloudmetagegevens synchroniseren** aan of u metagegevens voor alle clouds op de VMM-server wilt synchroniseren. Deze actie hoeft op elke server slechts één keer te worden uitgevoerd. Als u niet alle clouds wilt synchroniseren, laat u deze instelling uitgeschakeld. U kunt elke cloud afzonderlijk synchroniseren in de cloudeigenschappen in de VMM-console.
12. Klik op **Volgende** om het proces te voltooien. Na de registratie worden door Site Recovery metagegevens van de VMM-server opgehaald. De server wordt weer gegeven in **servers**  >  **VMM-servers** in de kluis.
13. Wanneer de server in de kluis wordt weer gegeven ,  >  selecteert u de VMM-server in bron voor **bereiding bron** en selecteert u de Cloud waarin de Hyper-V-host zich bevindt. Klik vervolgens op **OK**.


## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer de VMM-doelserver en -cloud:

1. Klik op **infrastructuur**  >  **doel** voorbereiden en selecteer de doel-VMM-server.
2. VMM-clouds die zijn gesynchroniseerd met Site Recovery worden weergegeven. Selecteer de doelcloud.

   ![Scherm afbeelding van de doel-VMM-server en de Cloud selecties.](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

Controleer voordat u begint of alle hosts die het beleid gebruiken hetzelfde besturingssysteem hebben. Als op hosts verschillende versies van Windows Server worden uitgevoerd, hebt u meerdere soorten replicatiebeleid nodig.

1. Als u een nieuw replicatie beleid wilt maken, klikt u op **infra structuur voorbereiden**  >  **replicatie-instellingen**  >  **+ maken en koppelen**.
2. Geef in **Beleid maken en koppelen** een beleidsnaam op. Het type bron en doel moet **Hyper-V** zijn.
3. Bij **Versie Hyper-V-host** selecteert u het besturingssysteem dat op de host wordt uitgevoerd.
4. Bij **Verificatietype** en **Verificatiepoort** geeft u op hoe verkeer tussen de primaire en Hyper-V-hostserver en Hyper-V-hostserver voor herstel wordt geverifieerd.
    - Selecteer **Certificaat**, tenzij u een werkende Kerberos-omgeving hebt. Azure Site Recovery configureert automatisch certificaten voor HTTPS-verificatie. U hoeft niets handmatig te ondernemen.
    - Standaard worden poort 8083 en 8084 (voor certificaten) in de Windows Firewall op de Hyper-V-hostservers geopend.
    - Als u **Kerberos** selecteert, wordt een Kerberos-ticket gebruikt voor wederzijdse verificatie van de hostservers. Kerberos is alleen relevant voor Hyper-V-hostservers die worden uitgevoerd op Windows Server 2012 R2 of hoger.
1. Geef in **Kopieer frequentie** op hoe vaak u de Delta gegevens na de initiële replicatie wilt repliceren (elke 30 seconden, 5 of 15 minuten).
2. Geef in **Bewaarperiode van het herstelpunt** op hoeveel uur elk herstelpunt moet worden bewaard binnen een tijdsvenster. Gerepliceerde machines kunnen te allen tijde worden hersteld naar een willekeurig punt binnen een tijdsvenster.
3. Geef in **Frequentie van de app-consistente momentopname** op hoe vaak (elke 1-12 uur) er herstelpunten moeten worden gemaakt met toepassingsconsistente momentopnamen. Hyper-V maakt gebruik van twee soorten momentopnamen:
    - **Standaardmomentopname**: biedt een incrementele momentopname van de volledige virtuele machine.
    - **App-consistente momentopname**: biedt een momentopname van de toepassingsgegevens in de VM op een bepaald tijdstip. Met Volume Shadow Copy Service (VSS) wordt ervoor gezorgd dat apps een consistente status hebben wanneer de momentopname wordt gemaakt. Het inschakelen van app-consistente momentopnamen is van invloed op prestaties van de app op de bron-VM's. Stel een waarde in die lager is dan het aantal aanvullende herstelpunten dat u configureert.
4. Geef bij **Compressie tijdens gegevensoverdracht** op of overgedragen replicatiegegevens moeten worden gecomprimeerd.
5. Selecteer **Gerepliceerde VM verwijderen** om op te geven dat de gerepliceerde virtuele machine moet worden verwijderd als u de beveiliging voor de bron-VM uitschakelt. Als u deze instelling inschakelt, wordt deze als u de beveiliging voor de bron-VM uitschakelt uit de Site Recovery-console verwijderd, worden Site Recovery-instellingen voor de VMM verwijderd uit de VMM-console en wordt de replica verwijderd.
6. Geef, als u via het netwerk repliceert, bij **Initiële replicatiemethode** op of de initiële replicatie moet worden gestart of plan deze in. Om netwerkbandbreedte te besparen, wilt u deze mogelijk buiten de drukste tijden plannen. Klik vervolgens op **OK**.

     ![Scherm afbeelding van de opties voor het replicatie beleid.](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Het nieuwe beleid wordt automatisch gekoppeld aan de VMM-cloud. Klik bij **Replicatiebeleid** op **OK**. 


## <a name="enable-replication"></a>Replicatie inschakelen

1. Klik op **Toepassing repliceren** > **Bron**. 
2. Selecteer bij **Bron** de VMM-server en de cloud waarin de Hyper-V-hosts die u wilt repliceren zich bevinden. Klik vervolgens op **OK**.
3. Verifieer bij **Doel** de secundaire VMM-server en cloud.
4. Selecteer in **Virtuele machines** de VM's die u wilt beveiligen in de lijst.


U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Wanneer de taak **De beveiliging voltooien** is voltooid, is de initiële replicatie voltooid en is de virtuele machine klaar voor failover.

## <a name="next-steps"></a>Volgende stappen

[Noodherstelanalyse uitvoeren](hyper-v-vmm-test-failover.md)
