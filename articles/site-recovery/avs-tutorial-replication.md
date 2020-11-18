---
title: Azure Site Recovery instellen voor VM's van Azure VMware Solution
description: Leer hoe u herstel na noodgeval naar Azure instelt voor Azure VMware Solution-VM’s met Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 3ac1f5bd3d44b7f98284cead60b34689f3d7be30
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395492"
---
# <a name="setup-azure-site-recovery-for-azure-vmware-solution-vms"></a>Azure Site Recovery instellen voor VM's van Azure VMware Solution

In dit artikel wordt beschreven hoe u replicatie voor Azure VMware Solution-VM's kunt inschakelen op herstel na noodgeval naar Azure met behulp van de [Azure Site Recovery](site-recovery-overview.md)-service.

Dit is de derde zelfstudie in een reeks waarin u ziet hoe u herstel na noodgeval kunt instellen naar Azure voor Azure VMware Solution-VM's. In de vorige zelfstudie hebben we [de Azure VMware Solution-omgeving ](avs-tutorial-prepare-avs.md) voorbereid op herstel na noodgeval naar Azure.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Configureer de instellingen voor de resource-replicatie en een Site Recovery-configuratieserver in de Azure VMware Solution-privécloud
> * Configureer de instellingen voor het replicatiedoel.
> * Een replicatiebeleid maken.
> * Schakel replicatie voor een VMware-VM in.

> [!NOTE]
> In zelfstudies ziet u steeds het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Voor gedetailleerde instructies bekijkt u het artikel in de instructiesectie van de Site Recovery-inhoudsopgave.

## <a name="before-you-start"></a>Voordat u begint

Voltooi de vorige zelfstudies:

1. Zorg ervoor dat u [Azure hebt ingesteld](avs-tutorial-prepare-azure.md) voor herstel na noodgeval naar Azure.
2. Volg [deze stappen](avs-tutorial-prepare-avs.md) om uw Azure VMware Solution-implementatie voor te bereiden op herstel na noodgeval naar Azure.
3. In deze zelfstudie wordt uitgelegd hoe u een virtuele machine repliceert. Als u meerdere VM's implementeert, moet u het [Hulpprogramma Deployment Planner](https://aka.ms/asr-deployment-planner) gebruiken. [Meer informatie](site-recovery-deployment-planner.md) over dit hulpprogramma.
4. Deze zelfstudie maakt gebruik van een aantal opties die u wellicht wilt aanpassen:
    - De zelfstudie gebruikt een OVA-sjabloon om de VM van de VMware-configuratieserver te maken. Als u dit om een of andere reden niet kunt doen, volgt u [deze instructies](physical-manage-configuration-server.md) om de configuratieserver handmatig in te stellen.
    - In deze zelfstudie wordt MySQL met behulp van Site Recovery automatisch op de configuratieserver gedownload en geïnstalleerd. U kunt dit ook handmatig instellen, als u dat wilt. [Meer informatie](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

1. Selecteer de naam van de kluis in **Recovery Services-kluizen**. We gebruiken **ContosoVMVault** voor dit scenario.
2. Selecteer in **Aan de slag** Site Recovery. Selecteer vervolgens **Infrastructuur voorbereiden**.
3. In **Beveiligingsdoel** > **Waar bevinden de machines zich**, selecteert u **On-premises**.
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. In **Zijn de machines gevirtualiseerd** selecteert **Ja, met VMware vSphere Hypervisor**. Selecteer vervolgens **OK**.



## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

In uw bronomgeving hebt u één maximaal beschikbare on-premises computer nodig om de on-premises Site Recovery-onderdelen te hosten:

- **Configuratieserver**: De configuratieserver coördineert de communicatie tussen de Azure VMware Solution-privécloud en Azure, en beheert de gegevensreplicatie.
- **Processerver**: De processerver fungeert als replicatiegateway. Deze ontvangt replicatiegegevens, optimaliseert de gegevens met caching, compressie en versleuteling, en verzendt ze naar het account voor cacheopslag in Azure. De processerver installeert ook de agent Mobility-service op VM’s die u wilt repliceren en detecteert automatisch Azure VMware Solution-VM’s.
- **Hoofddoelserver**: Op de hoofddoelserver worden de replicatiegegevens tijdens de failback vanuit Azure afgehandeld.


Al deze componenten worden samen geïnstalleerd op een enkele Azure VMware Solution-machine die bekend staan als de *configuratieserver*. Voor herstel na noodgeval voor Azure VMware Solution hebben we de configuratieserver standaard ingesteld als een zeer beschikbare VMware-VM. U kunt dit doen door een voorbereide OVA-sjabloon (Open Virtualization Application) te downloaden en de sjabloon in VMware te importeren om de VM te maken.

- De meest recente versie van de configuratieserver is beschikbaar in de portal. U kunt deze ook rechtstreeks downloaden uit het [Microsoft Downloadcentrum](https://aka.ms/asrconfigurationserver).
- Als u om een of andere reden geen OVA-sjabloon kunt gebruiken om een VM in te stellen, volgt u [deze instructies](physical-manage-configuration-server.md) om de configuratieserver handmatig in te stellen.
- De licentie die wordt geleverd bij de OVF-sjabloon is een evaluatielicentie die 180 dagen geldig is. Windows dat op de VM draait, moet worden geactiveerd met de vereiste licentie.


### <a name="download-the-vm-template"></a>De VM-sjabloon downloaden

1. Ga in de kluis naar **Infrastructuur voorbereiden** > **Bron**.
2. Selecteer **+Configuratieserver** in **Bron voorbereiden**.
3. Controleer in **Server toevoegen** of **Configuratieserver voor VMware** wordt weergegeven in **Servertype**.
4. Download de OVA-sjabloon voor de configuratieserver.



## <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren


1. Meld u aan bij de VMware vCenter-server of vSphere ESXi-host met behulp van de VMware vSphere-client.
2. Selecteer in het menu **Bestand** de optie **OVF-sjabloon implementeren** om de **wizard voor het implementeren van OVF-sjablonen** te starten.

     ![Schermopname van de opdracht OVF-sjabloon implementeren in de VMWare vSphere-client.](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Voer in **Bron selecteren** de locatie van de gedownloade OVF in.
4. Selecteer **Volgende** in **Beoordelingsdetails**.
5. Accepteer de standaardinstellingen in **Naam en map selecteren** en **Configuratie selecteren**.
6. Selecteer in **Opslag selecteren** **Thick Provision Eager Zeroed** in **Indeling virtuele schijf selecteren**.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina's.
8. Bij **Gereed om te voltooien** selecteert u **Inschakelen na de implementatie** > **Voltooien** om de virtuele machine in te stellen met de standaardinstellingen.

   > [!TIP]
   > Als u een extra NIC wilt toevoegen, wist u **Inschakelen na de implementatie** > **Voltooien**. De sjabloon bevat standaard één NIC. Na de implementatie kunt u meer NIC’s toevoegen.

## <a name="add-an-additional-adapter"></a>Een extra adapter toevoegen

Als u een extra NIC aan de configuratieserver wilt toevoegen, moet u dit doen voordat u de server in de kluis registreert. Het toevoegen van extra adapters wordt niet ondersteund na registratie.

1. Klik in de vSphere Client-inventaris met de rechtermuisknop op de VM en selecteer **Instellingen bewerken**.
2. Selecteer **Toevoegen** > **Ethernet-adapter** bij **Hardware**. Selecteer vervolgens **Volgende**.
3. Selecteer een adaptertype en een netwerk.
4. Als u verbinding wilt maken met de virtuele NIC verbinding wanneer de VM wordt ingeschakeld, selecteert u **Verbinding maken bij inschakelen**. Selecteer **Volgende** > **voltooien**. Selecteer vervolgens **OK**.


## <a name="register-the-configuration-server"></a>De configuratieserver registreren

Na het instellen van de configuratieserver registreert u deze in de kluis.

1. Schakel de VM in vanuit de VMware vSphere Client-console.
2. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Meld u nadat de installatie is voltooid bij de virtuele machine aan als de administrator.
4. De eerste keer dat u zich aanmeldt, wordt het configuratieprogramma van Azure Site Recovery binnen enkele seconden gestart.
5. Voer een naam in die wordt gebruikt voor het registreren van de configuratieserver bij Site Recovery. Selecteer vervolgens **Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement. De referenties moeten toegang hebben tot de kluis waarin u de configuratieserver wilt registreren. Zorg ervoor dat de nodige [rollen](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) aan deze gebruiker worden toegewezen.
7. Het hulpprogramma voert enkele configuratietaken uit en start opnieuw op.
8. Meld u opnieuw aan bij de machine. Na enkele seconden wordt de wizard voor het beheer van de configuratieserver automatisch gestart.


### <a name="configure-settings-and-add-the-vmware-server"></a>Instellingen configureren en de VMware-server toevoegen

Voltooi het instellen en registreren van de configuratieserver. Voordat u doorgaat, moet u er zeker van zijn dat aan alle [vereisten](vmware-azure-deploy-configuration-server.md#prerequisites) is voldaan voor het installeren van de configuratieserver.


1. Selecteer in de wizard voor het beheer van de configuratieserver **Connectiviteit instellen**. Selecteer in de vervolgkeuzemenu's eerst de NIC die de ingebouwde processerver gebruikt voor herstel na noodgeval en push installatie van de mobiliteitsservice op de bronmachines. Selecteer vervolgens de NIC die de configuratieserver gebruikt voor de connectiviteit met Azure. Selecteer vervolgens **Opslaan**. U kunt deze instelling niet wijzigen nadat deze is geconfigureerd.
2. Selecteer in **Selecteer Recovery Services-kluis** uw Azure-abonnement en de relevante resourcegroep en kluis.
3. Accepteer de gebruiksrechtovereenkomst in **Software van derden installeren**. Selecteer **Downloaden en installeren** om MySQL Server te installeren. Als u MySQL in het pad hebt geplaatst, kan deze stap worden overgeslagen. [Meer informatie](vmware-azure-deploy-configuration-server.md#configure-settings)
4. In **De configuratie van het apparaat valideren** worden de vereisten gecontroleerd voordat u doorgaat.
5. In **vCenter Server vSphere/ESXi-server configureren** voert u de FQDN of het IP-adres van de vCenter-server, of vSphere-host, in waar de VM's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
6. Voer gebruikersreferenties in die door de configuratieserver moeten worden gebruikt voor verbinding met de VMware-server. Zorg ervoor dat de gebruikersnaam en het wachtwoord juist zijn en deel uitmaken van de groep Administrators van de virtuele machine die moet worden beveiligd. Site Recovery gebruikt deze referenties voor het automatisch detecteren van VMware-VM’s die beschikbaar zijn voor replicatie. Selecteer **Toevoegen** en vervolgens **Doorgaan**.
7. Voer in **Referenties voor virtuele machine configureren** de gebruikersnaam en het wachtwoord in die worden gebruikt om Mobility Service automatisch op virtuele machines te installeren wanneer replicatie wordt ingeschakeld.
    - Voor Windows-machines moet het account lokale administrator-machtigingen hebben op de machines die u wilt repliceren.
    - Geef voor Linux de details voor de superuser op.
8. Selecteer **Configuratie voltooien** om de registratie te voltooien.
9. Nadat de registratie is voltooid, opent u Azure Portal en controleert u of de configuratieserver en de VMware-server worden weergegeven op **Recovery Services-kluis** > **Beheer** > **Site Recovery-infrastructuur** > **Configuratieservers**.


Nadat de configuratieserver is geregistreerd, maakt Site Recovery verbinding met de VMware-servers met behulp van de opgegeven instellingen, waarna de VM's worden gedetecteerd.

> [!NOTE]
> Het duurt 15 minuten of langer voordat de accountnaam wordt weergegeven in de portal. Selecteer **Configuratieservers** > **_servernaam_ *_ > _* Server vernieuwen**.

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Selecteer **Infrastructuur voorbereiden** > **Doel**. Selecteer het Azure-abonnement dat u wilt gebruiken. Er wordt gebruikgemaakt van een Resource Manager-model.
2. Site Recovery controleert of u een of meer virtuele netwerken hebt. U zou deze moeten hebben als u de Azure-onderdelen in het [eerste deel](tutorial-prepare-azure.md) van deze reeks zelfstudies hebt ingesteld.

   ![Schermopname van de opties voor De infrastructuur voorbereiden > Doel.](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Open de [Azure Portal](https://portal.azure.com). Zoek en selecteer **Recovery Services-kluizen**.
2. Selecteer de Recovery Services-kluis (**ContosoVMVault** in deze zelfstudie).
3. Selecteer **Infrastructuur voor Site Recovery** > **Herstelbeleid** >  **+Herstelbeleid** om een replicatiebeleid te maken.
4. Voer bij **Replicatiebeleid maken** de naam van het beleid in. Hiervoor wordt **VMwareRepPolicy** gebruikt.
5. Gebruik in **RPO-drempelwaarde** de standaardwaarde van 60 minuten. Deze waarde bepaalt hoe vaak herstelpunten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
6. Bij **Bewaarperiode van het herstelpunt** geeft u op hoe lang elk herstelpunt moet worden bewaard. Voor deze zelfstudie wordt 72 uur gebruikt. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster.
7. Bij **Frequentie van de app-consistente momentopname** geeft u op hoe vaak app-consistente momentopnamen moeten worden gemaakt. Gebruik de standaardwaarde van 60 minuten. Selecteer **OK** om het beleid te maken.

   ![Schermopname van de opties voor het maken van het replicatiebeleid.](./media/vmware-azure-tutorial/replication-policy.png)

- Het beleid wordt automatisch gekoppeld aan de configuratieserver.
- Standaard wordt automatisch een bijbehorend beleid voor failback gemaakt. Als het replicatiebeleid bijvoorbeeld **repbeleid** is, is het failbackbeleid **repbeleid-failback**. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

Opmerking: In het scenario VMware-naar-Azure wordt de crash-consistente schaduwkopie gemaakt met een interval van 5 minuten.

## <a name="enable-replication"></a>Replicatie inschakelen

Schakel replicatie als volgt in voor VM's:

1. Selecteer **Toepassing repliceren** > **Bron**.
2. Bij **Bron** selecteert u **On-premises** en selecteert u de configuratieserver bij **Bronlocatie**.
3. Selecteer **Virtuele machines** in **Type machine**.
4. Selecteer bij **vCenter/vSphere-hypervisor** de vSphere-host of de vCenter-server waarmee de host wordt beheerd.
5. Selecteer de processerver (standaard geïnstalleerd op de VM met de rol van configuratieserver). Selecteer vervolgens **OK**. De integriteitsstatus van elke processerver wordt aangegeven volgens de aanbevolen limieten en andere parameters. Kies een integriteitsprocesserver. Kan geen [kritieke](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processerver kiezen. U kunt [problemen oplossen en de fouten](vmware-physical-azure-troubleshoot-process-server.md) corrigeren **of** een [scale-out processerver](vmware-azure-set-up-process-server-scale.md) instellen.
6. Selecteer in **Doel** het abonnement en de resourcegroep waarin u de failover-VM's wilt maken. Het implementatiemodel van Resource Manager wordt gebruikt.
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle virtuele machines waarop u replicatie inschakelt. Selecteer **Later configureren** om per machine een Azure-netwerk te selecteren.
9. Selecteer in **Virtuele machines** > **Virtuele machines selecteren** alle machines die u wilt repliceren. U kunt alleen machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**. Als u een bepaalde virtuele machine niet kunt weergeven of selecteren, vindt u hier [meer informatie](./vmware-azure-troubleshoot-replication.md) om het probleem te verhelpen.
10. Selecteer in **Eigenschappen** > **Eigenschappen configureren** het account dat door de processerver moet worden gebruikt om automatisch Mobility Service op de computer te installeren.
11. Controleer of het juiste replicatiebeleid is geselecteerd in **Replicatie-instellingen** > **Replicatie-instellingen configureren**.
12. Selecteer **Replicatie inschakelen**. Site Recovery installeert de Mobility-service wanneer replicatie wordt ingeschakeld voor een VM.
13. U kunt de voortgang van de taak **Beveiliging inschakelen** volgen via **Instellingen** > **Taken** > **Site Recovery-taken**. Nadat de taak **Beveiliging voltooien** wordt uitgevoerd en een herstelpunt is gegenereerd, is de machine klaar voor failover.
14. Het kan 15 minuten of langer duren voordat wijzigingen zijn doorgevoerd en in de portal worden weergegeven.
15. Voor het bewaken van virtuele machines die u toevoegt, controleert u de laatste detectietijd voor VM's in **Configuratieservers** > **Laatst contact om**. Als u VM's wilt toevoegen zonder te wachten op de geplande detectie, markeert u de configuratieserver (zonder deze te selecteren), en selecteert u **Vernieuwen**.

## <a name="next-steps"></a>Volgende stappen
Nadat de replicatie is ingeschakeld, voert u een analyse uit om te controleren of alles werkt zoals verwacht.
> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](avs-tutorial-dr-drill-azure.md)
