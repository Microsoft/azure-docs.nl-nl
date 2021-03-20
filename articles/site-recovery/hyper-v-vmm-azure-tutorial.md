---
title: Herstel na noodgevallen voor Hyper-V (met VMM) met Azure Site Recovery
description: Leer hoe u herstel na noodgevallen kunt instellen voor on-premises Hyper-V-VM's in System Center VMM-clouds naar Azure met Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89442817"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Herstel na noodgevallen voor on-premises Hyper-V-VM's in VMM-clouds naar Azure instellen

In deze zelfstudie wordt beschreven hoe u replicatie voor on-premises Hyper-V-VM’s (virtuele machines) die worden beheerd door System Center Virtual Machine Manager (VMM) naar Azure met [Azure Site Recovery](site-recovery-overview.md) kunt instellen. Als u VMM niet gebruikt, [volgt u deze zelfstudie](hyper-v-azure-tutorial.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De replicatiebron en het replicatiedoel selecteren.
> * De bronreplicatieomgeving, waaronder on-premises Site Recovery-onderdelen, en de replicatiedoelomgeving instellen.
> * Netwerktoewijzing tussen VMM-VM-netwerken en virtuele Azure-netwerken instellen.
> * Een replicatiebeleid maken.
> * Replicatie voor een VM inschakelen.

> [!NOTE]
> In zelfstudies ziet u steeds het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Voor gedetailleerde instructies bekijkt u de artikelen in de sectie **Instructies** van de [Site Recovery-documentatie](./index.yml).

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u de volgende zelfstudies al hebt voltooid:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
1. [On-premises Hyper-V-servers voorbereiden](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. Ga in de Azure-portal naar **Recovery Services-kluizen** en selecteer de kluis **ContosoVMVault** die in de zelfstudie [Azure voorbereiden](tutorial-prepare-azure.md#create-a-recovery-services-vault) is gemaakt.
1. Selecteer in **Aan de slag** de optie **Site Recovery** > **Infrastructuur voorbereiden** en configureer de volgende instellingen:
    1. In **Beveiligingsdoel** > **Waar bevinden de machines zich?** selecteert u **On-premises**.
    1. In **Waarnaartoe wilt u de machines repliceren?** selecteert u **Naar Azure**.
    1. In **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
    1. In **Gebruikt u System Center VMM om uw Hyper-V-hosts te beheren?** selecteert u **Ja**.
1. Selecteer **OK**.

   ![Replicatiedoel](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

1. Als u een grote implementatie plant, moet u in **Implementatieplanning** de Deployment Planner voor Hyper-V downloaden via de koppeling op de pagina. [Meer informatie](hyper-v-deployment-planner-overview.md) over de planning van de implementatie van Hyper-V.
1. Voor deze zelfstudie hebben we de Deployment Planner niet nodig. Selecteer in **Hebt u de implementatieplanning uitgevoerd?** de optie **Dat doe ik later**. Selecteer vervolgens **OK**.

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Wanneer u de bronomgeving instelt, installeert u de Azure Site Recovery Provider op de VMM-server en registreert u de server in de kluis. Installeer de Azure Recovery Services-agent op elke Hyper-V-host.

1. **Infrastructuur voorbereiden**. Selecteer **Bron**.
1. **Bron voorbereiden**. Selecteer **+ VMM** om een VMM-server toe te voegen. Controleer in **Server toevoegen** of **System Center VMM-server** wordt weergegeven bij **Servertype**.
1. Download het installatieprogramma voor de Microsoft Azure Site Recovery Provider.
1. Download de registratiesleutel voor de kluis. U hebt deze sleutel nodig wanneer u de Provider-installatie uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.
1. Download het installatieprogramma voor de Microsoft Azure Recovery Services-agent.

   ![De Provider, registratiesleutel en agent downloaden](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>De provider installeren op de VMM-server

1. In de Azure Site Recovery Provider-installatiewizard **Microsoft Update**. Kies ervoor Microsoft Update te gebruiken om op Provider-updates te controleren.
1. **Installatie**. Accepteer de standaardinstallatielocatie van de Provider en selecteer **Installeren**.
1. Na de installatie selecteert u in de wizard Registratie voor Microsoft Azure Site Recovery de optie **Kluisinstellingen** en klikt u op **Bladeren**. Selecteer in **Sleutelbestand** het gedownloade sleutelbestand van de kluis.
1. Geef het Azure Site Recovery-abonnement en de kluisnaam (**ContosoVMVault**) op. Geef een beschrijvende naam op voor de VMM-server om deze in de kluis te identificeren.
1. **Proxy-instellingen**. Selecteer **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxy**.
1. Accepteer de standaardlocatie voor het certificaat dat wordt gebruikt om gegevens te versleutelen. Versleutelde gegevens worden ontsleuteld wanneer u een failover uitvoert.
1. **Cloudmetagegevens synchroniseren**. Selecteer **De cloudmetagegevens synchroniseren naar de Site Recovery-portal**. Deze actie hoeft op elke server slechts één keer te worden uitgevoerd. Selecteer vervolgens **Registreren**.
1. Selecteer **Voltooien** nadat de server is geregistreerd in de kluis.

Nadat de registratie is voltooid, worden de metagegevens van de server opgehaald door Azure Site Recovery en wordt de VMM-server weergegeven in **Site Recovery-infrastructuur**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>De Recovery Services-agent installeren op Hyper-V-hosts

Installeer de agent op elke Hyper-V-host met VM’s die u wilt repliceren.

Configureer deze instellingen in de installatiewizard voor de Microsoft Azure Recovery Services-agent:

1. **Controle op vereisten**. Selecteer **Next**. Ontbrekende vereiste onderdelen worden automatisch geïnstalleerd.
1. **Installatie-instellingen**. Accepteer de installatielocatie. Selecteer vervolgens **Install**.

    >[!NOTE]
    >Voor Azure Site Recovery is de **locatie van de cache** niet vereist.

1. **Installatie**. Wanneer de installatie is voltooid, selecteert u **Sluiten** om de wizard te voltooien.

   ![Agent installeren](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

1. Selecteer **Infrastructuur voorbereiden** > **Doel**.
1. Selecteer het abonnement en de resourcegroep (**ContosoRG**), waarin de Azure-VM's na failover worden gemaakt.
1. Selecteer het **Resource Manager**-implementatiemodel.

Site Recovery controleert of er een of meer compatibele Azure-opslagaccounts en -netwerken zijn.

## <a name="configure-network-mapping"></a>Netwerktoewijzing configureren

1. **Site Recovery-infrastructuur** > **Netwerktoewijzingen** > **Netwerktoewijzing**. Selecteer het pictogram **+Netwerktoewijzing**.
1. **Netwerktoewijzing toevoegen**. Selecteer de bronserver **System Center VMM**. Selecteer bij **Doel** de optie Azure.
1. Controleer na het uitvoeren van een failover het abonnement en het implementatiemodel.
1. **Bronnetwerk**. Selecteer het on-premises VM-bronnetwerk.
1. **Doelnetwerk**. Selecteer het Azure-netwerk waarin de replica’s (Azure-VM’s) moeten worden geplaatst wanneer ze zijn gemaakt na failover. Selecteer vervolgens **OK**.

   ![Netwerktoewijzing](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Selecteer **Infrastructuur voorbereiden** > **Replicatie-instellingen** >  **+Maken en koppelen**.
1. Geef in **Beleid maken en koppelen** een beleidsnaam op. Wij gebruiken **ContosoReplicationPolicy**.
1. Accepteer de standaardinstellingen en selecteer **OK**:
   - **Kopieerfrequentie** geeft aan dat deltagegevens na de initiële replicatie om de vijf minuten worden gerepliceerd.
   - **Bewaarperiode van herstelpunt** geeft aan dat elk herstelpunt gedurende twee uur wordt bewaard.
   - **Frequentie van de app-consistente momentopname** geeft aan dat er elk uur herstelpunten met app-consistente momentopnamen worden gemaakt.
   - **Starttijd van de initiële replicatie** geeft aan dat de initiële replicatie direct wordt gestart.
   - **Gegevens versleutelen die zijn opgeslagen op Azure** is ingesteld op de standaardinstelling (**Uit**) en geeft aan dat inactieve gegevens in Azure niet worden versleuteld.
1. Nadat het beleid is gemaakt, selecteert u **OK**. Wanneer u nieuw beleid maakt, wordt dit automatisch gekoppeld aan de VMM-cloud.

## <a name="enable-replication"></a>Replicatie inschakelen

1. **Toepassing repliceren**. Selecteer **Bron**.
1. **Bron**. Selecteer de VMM-cloud. Selecteer vervolgens **OK**.
1. **Doel**. Verifieer de doelbestemming (Azure) en dat het juiste kluisabonnement wordt gebruikt, en selecteer het **Resource Manager**-model.
1. Selecteer het opslagaccount **contosovmsacct1910171607** en het Azure-netwerk **ContosoASRnet**.
1. **Virtuele machines** > **Selecteren**. Selecteer de VM die u wilt repliceren. Selecteer vervolgens **OK**.

   U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Wanneer de taak **De beveiliging voltooien** is voltooid, is de initiële replicatie voltooid en is de virtuele machine klaar voor failover.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)
