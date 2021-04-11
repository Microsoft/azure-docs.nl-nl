---
title: Herstel na noodgevallen voor Hyper-V met Azure Site Recovery
description: Leer hoe u herstel na noodgevallen kunt instellen voor on-premises Hyper-V-VM's (zonder VMM) naar Azure met Site Recovery.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.custom: MVC
ms.openlocfilehash: 0c98728b0da92e198e158cef29e69b2f60818770
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580858"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Herstel na noodgevallen instellen voor on-premises Hyper-V-VM's naar Azure

De [Azure Site Recovery](site-recovery-overview.md)-service draagt bij aan uw strategie voor herstel na noodgeval, door de replicatie, failover en failback van on-premises machines en Azure-VM’s (virtuele machines) te beheren en in te delen.

Dit is de derde zelfstudie in een reeks. Hiermee leert u hoe u herstel na noodgevallen instelt voor on-premises Hyper-V-VM's naar Azure. De zelfstudie is van toepassing op Hyper-V-VM's die niet worden beheerd door Microsoft System Center Virtual Machine Manager (VMM).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De replicatiebron en het replicatiedoel selecteren.
> * De bronreplicatieomgeving, waaronder on-premises Site Recovery-onderdelen, en de replicatiedoelomgeving instellen.
> * Een replicatiebeleid maken.
> * Replicatie voor een VM inschakelen.

> [!NOTE]
> In zelfstudies ziet u steeds het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Voor gedetailleerde instructies bekijkt u het artikel in de sectie **Instructies** van de [Site Recovery-documentatie](./index.yml).



## <a name="before-you-begin"></a>Voordat u begint

Dit is de derde zelfstudie in een reeks. In deze zelfstudie wordt ervan uitgegaan dat u de taken in de vorige zelfstudies al hebt voltooid:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. [On-premises Hyper-V voorbereiden](./hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. Ga naar de Azure-portal naar **Recovery Services-kluizen** en selecteer uw kluis. In de vorige zelfstudie hebben we de kluis **ContosoVMVault** voorbereid.
2. In **Aan de slag** selecteert u **Site Recovery** en selecteert u vervolgens **De infrastructuur voorbereiden**.
3. In **Beveiligingsdoel** > **Waar bevinden de machines zich**, selecteert u **On-premises**.
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. In **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
6. Bij **Gebruikt u System Center VMM om uw Hyper-V-hosts te beheren** selecteert u **Nee**.
7. Selecteer **OK**.

    ![Schermopname van de opties voor Beveiligingsdoel in De infrastructuur voorbereiden.](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

1. Als u een grote implementatie plant, moet u in **Implementatieplanning** de Deployment Planner voor Hyper-V downloaden via de koppeling op de pagina. [Meer informatie](hyper-v-deployment-planner-overview.md) over de planning van de implementatie van Hyper-V.
2. Voor deze zelfstudie hebben we de Deployment Planner niet nodig. Selecteer in **Hebt u de implementatieplanning uitgevoerd?** de optie **Dat doe ik later**. Selecteer vervolgens **OK**.

    ![Schermopname van de opties voor Implementatieplanning in De infrastructuur voorbereiden.](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Als u de bronomgeving wilt instellen, maakt u een Hyper-V-site en voegt u de Hyper-V-hosts met de virtuele machines die u wilt repliceren toe aan die site. Download en installeer vervolgens op elke host de Azure Site Recovery Provider en de Azure Recovery Services-agent. Registreer daarna de Hyper-V-site in de kluis.

1. Selecteer **Bron** onder **De infrastructuur voorbereiden**.
2. Selecteer **+ Hyper-V-locatie** in **Bron voorbereiden**.
3. Geef in **Hyper-V-site maken** de naam van de site op. We gebruiken **ContosoHyperVSite**.

    ![Schermopname van de selectie van Hyper-V-locatie in De infrastructuur voorbereiden.](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Nadat de site is gemaakt, in **Bron voorbereiden** > **Stap 1: Selecteer Hyper-V-site** en selecteer de site die u hebt gemaakt.
5. Selecteer **+ Hyper-V-server**.

    ![Schermopname van de selectie van Hyper-V-server in De infrastructuur voorbereiden.](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Download het installatieprogramma voor Microsoft Azure Site Recovery Provider.
7. Download de registratiesleutel voor de kluis. U hebt deze sleutel nodig tijdens de installatie van de provider. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Schermopname van de opties voor het downloaden van de provider en registratiesleutel.](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Provider installeren

Installeer het gedownloade installatiebestand (AzureSiteRecoveryProvider.exe) op elke Hyper-V-host die u wilt toevoegen aan de Hyper-V-site. De Azure Site Recovery Provider en de Recovery Services-agent worden op elke Hyper-V-host geïnstalleerd.

1. Voer het installatiebestand uit.
2. Ga in de installatiewizard van Azure Site Recovery Provider naar **Microsoft Update**, en kies ervoor om Microsoft Update te gebruiken om te controleren op Provider-updates.
3. Accepteer in **Installatie** de standaardinstallatielocatie van de Provider en agent en selecteer **Installeren**.
4. Ga na de installatie naar de wizard Registratie voor Microsoft Azure Site Recovery en naar **Kluisinstellingen**, selecteer **Bladeren**. Selecteer in **Sleutelbestand** het gedownloade sleutelbestand van de kluis.
5. Geef het Azure Site Recovery-abonnement, de kluisnaam (**ContosoVMVault**) en de Hyper-V-site (**ContosoHyperVSite**) op waartoe de Hyper-V-server behoort.
6. Selecteer in **Proxy-instellingen** de optie **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**.
7. Selecteer nadat de server is geregistreerd in de kluis in **Registratie** **Voltooien**.

De metagegevens van de Hyper-V-server worden opgehaald door Azure Site Recovery en de server wordt weergegeven in **Infrastructuur voor Site Recovery** > **Hyper-V-hosts**. Dit proces duurt maximaal 30 minuten.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>De Provider op een Hyper-V-kernserver installeren

Als u een Hyper-V-kernserver gebruikt, downloadt u het installatiebestand en voert u de volgende stappen uit:

1. Pak de bestanden van AzureSiteRecoveryProvider.exe uit naar een lokale map door het uitvoeren van deze opdracht:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Voer `.\setupdr.exe /i` uit. De resultaten zijn vastgelegd in %Programdata%\ASRLogs\DRASetupWizard.log.

3. Registreer de server door de volgende opdracht uit te voeren:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen:

1. Selecteer **Infrastructuur voorbereiden** > **Doel**.
2. Selecteer het abonnement en de resourcegroep **ContosoRG**, waarin de Azure-VM's na failover worden gemaakt.
3. Selecteer het **Resource Manager**-implementatiemodel.

Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Selecteer **Infrastructuur voorbereiden** > **Replicatie-instellingen** >  **+Maken en koppelen**.
2. Geef in **Beleid maken en koppelen** een beleidsnaam op. We gebruiken **ContosoReplicationPolicy**.
3. We gebruiken voor deze zelfstudie de standaardinstellingen:
    - **Kopieerfrequentie** geeft aan hoe vaak deltagegevens (na de initiële replicatie) worden gerepliceerd. De standaardfrequentie is om de vijf minuten.
    - **Bewaarperiode van herstelpunt** geeft aan dat herstelpunten gedurende twee uur worden bewaard. De maximaal toegestane waarde voor retentie bij het beveiligen van virtuele machines die worden gehost op Hyper-V-hosts is 24 uur.
    - **Frequentie van de app-consistente momentopname** geeft aan dat er elk uur herstelpunten met app-consistente momentopnamen worden gemaakt.
    - **Starttijd van de initiële replicatie** geeft aan dat de initiële replicatie direct wordt gestart.
4. Nadat het beleid is gemaakt, selecteert u **OK**. Wanneer u een nieuw beleid maakt, wordt dit automatisch gekoppeld aan de opgegeven Hyper-V-site. In onze zelfstudie is dat **ContosoHyperVSite**.

    ![Beleid voor replicatie](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Replicatie inschakelen

1. Selecteer in **Toepassing repliceren** **Bron**.
2. Selecteer in **Bron** de site **ContosoHyperVSite**. Selecteer vervolgens **OK**.
3. Verifieer in **Doel** de doelbestemming (Azure), dat het juiste kluisabonnement wordt gebruikt en dat het **Resource Manager**-implementatiemodel wordt gebruikt.
4. Als u zelfstudie instellingen gebruikt, selecteert u het opslagaccount **contosovmsacct1910171607** dat u in de vorige zelfstudie hebt gemaakt voor gerepliceerde gegevens. Selecteer ook het netwerk **ContosoASRnet** waarin de virtuele Azure-machines worden opgeslagen na de failover.
5. Selecteer in **Virtuele machines** > **Selecteren** de VM's die u wilt repliceren. Selecteer vervolgens **OK**.

   U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Wanneer de taak **De beveiliging voltooien** is voltooid, is de initiële replicatie voltooid en is de virtuele machine klaar voor failover.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)
