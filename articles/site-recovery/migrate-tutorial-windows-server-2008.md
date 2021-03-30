---
title: Windows Server 2008-servers migreren naar Azure met Azure Migrate/Site Recovery
description: In dit artikel wordt beschreven hoe u on-premises Windows Server 2008-machines naar Azure migreert en wordt Azure Migrate aanbevolen.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6cc0855d3a4540de780a566a4613b4dbc647cfc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369485"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Servers waarop Windows Server 2008 wordt uitgevoerd, naar Azure migreren

In deze zelfstudie leert u hoe u on-premises servers waarop Windows Server 2008 of 2008 R2 wordt uitgevoerd, migreert naar Azure met behulp van Azure Site Recovery. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw on-premises omgeving voorbereiden op de migratie
> * Stel de doelomgeving in.
> * Stel een replicatiebeleid in.
> * Schakel replicatie in.
> * Voer een testmigratie uit om te controleren of alles goed werkt.
> * Voer een Failover-overschakeling naar Azure uit en voltooi de migratie.

## <a name="migrate-with-azure-migrate"></a>Migreren met Azure Migrate

We raden aan dat u machines naar Azure migreert met behulp van de service [Azure Migrate](../migrate/migrate-services-overview.md). 

- Azure Migrate is speciaal ontworpen voor servermigratie.
- Azure Migrate biedt een centrale hub voor detectie, evaluatie en migratie van on-premises machines naar Azure. Azure Site Recovery moet alleen worden gebruikt voor herstel na noodgevallen, niet voor migratie.
- Azure Migrate ondersteunt de migratie van servers met Windows Server 2008.


## <a name="migrate-with-site-recovery"></a>Migreren met Site Recovery

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen


|Besturingssysteem  | Omgeving  |
|---------|---------|
|Windows Server 2008 SP2: 32-bits en 64-bits (IA-32 en x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMware-VM's, Hyper-V-VM's en fysieke servers    |
|Windows Server 2008 R2 SP1: 64-bits</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMware-VM's, Hyper-V-VM's en fysieke servers|

> [!WARNING]
> - Migratie van servers waarop Server Core wordt uitgevoerd, wordt niet ondersteund.
> - Controleer vóór de migratie of het nieuwste servicepack en Windows-updates zijn geïnstalleerd.


### <a name="prerequisites"></a>Vereisten

Voordat u begint, is het handig om de Azure Site Recovery-architectuur te controleren voor de [migratie van VMware- en fysieke servers](vmware-azure-architecture.md) of de [migratie van virtuele Hyper-V-machines](hyper-v-azure-architecture.md) 

Voor het migreren van virtuele Hyper-V-machines waarop Windows Server 2008 of Windows Server 2008 R2 wordt uitgevoerd, volgt u de stappen in de zelfstudie [On-premises machines migreren naar Azure](migrate-tutorial-on-premises-azure.md).

In het vervolg van deze zelfstudie kunt u zien hoe u on-premises virtuele VMware-machines en fysieke servers kunt migreren waarop Windows Server 2008 of 2008 R2 wordt uitgevoerd.
> [!TIP]
> Bent u op zoek naar een manier om zonder agents VMware-VM's te migreren naar Azure? [Klik hier](../migrate/tutorial-migrate-vmware.md)


### <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen

- Op de configuratieserver, aanvullende processervers en de Mobility Service waarmee Windows Server 2008 SP2-servers worden gemigreerd, moet versie 9.19.0.0 of hoger van de Azure Site Recovery-software worden uitgevoerd.

- Toepassingsconsistente herstelpunten en de Multi-VM-consistentiefunctie worden niet ondersteund voor replicatie van servers waarop Windows Server 2008 SP2 wordt uitgevoerd. Windows Server 2008 SP2-servers moeten worden gemigreerd naar een crash-consistent herstelpunt. Crashconsistente herstelpunten worden standaard elke vijf minuten gegenereerd. Door gebruik te maken van een replicatiebeleid met een geconfigureerde, toepassingsconsistente momentopnamefrequentie, wordt de replicatiestatus kritiek vanwege het gebrek aan toepassingsconsistente herstelpunten. Om fout-positieven te voorkomen, stelt u de toepassingsconsistente momentopnamefrequentie in het replicatiebeleid in op Uit.

- Op de te migreren servers moet .NET Framework 3.5 Service Pack 1 zijn geïnstalleerd, opdat de Mobility Service kan functioneren.

- Als uw server dynamische schijven bevat, kan het in bepaalde configuraties voorkomen dat deze schijven op de server waarvoor failover is uitgevoerd, offline wordt gemarkeerd of als afwijkende schijven worden weergegeven. Het kan ook zijn dat de status van de gespiegelde set voor gespiegelde volumes op de dynamische schijven met Mislukte redundantie is gemarkeerd. U kunt dit probleem oplossen met diskmgmt.msc door deze schijven handmatig te importeren en ze opnieuw te activeren.

- De te migreren servers moeten het stuurprogramma vmstorfl.sys hebben. Failover kan mislukken als het stuurprogramma niet op de te migreren server aanwezig is. 
  > [!TIP]
  >Controleer of het stuurprogramma aanwezig is op C:\Windows\system32\drivers\vmstorfl.sys. Als u het stuurprogramma niet kunt vinden, kunt u als tijdelijke oplossing een dummybestand gebruiken. 
  >
  > Open de opdrachtprompt (run > cmd) en voer de volgende opdracht uit: copy nul c:\Windows\system32\drivers\vmstorfl.sys

- Mogelijk kan RDP niet onmiddellijk worden uitgevoerd op Windows Server 2008 SP2-servers met het 32-bits besturingssysteem als er een failover of test-failover naar Azure op is uitgevoerd. Start de virtuele machine waarvoor failover is uitgevoerd, opnieuw op vanuit de Azure-portal en maak opnieuw verbinding. Als u nog steeds geen verbinding kunt maken, controleert u of de server is geconfigureerd zodat verbindingen via extern bureaublad zijn toegestaan. Controleer ook of er geen firewallregels of netwerkbeveiligingsgroepen de oorzaak zijn van het blokkeren van de verbinding. 
  > [!TIP]
  > Voordat u de servers gaat migreren, wordt het uitvoeren van een testfailover sterk aangeraden. Zorg dat u minstens één testfailover hebt uitgevoerd op elke server die u wilt migreren. Als onderdeel van de testfailover, maakt u verbinding met de machine waarvoor de testfailover is uitgevoerd en controleert u of alles naar behoren werkt.
  >
  >Het testfailoverbewerking veroorzaakt geen storingen en u kunt er migraties mee testen door virtuele machines te maken in een geïsoleerd netwerk van uw keuze. Tijdens de testfailoverbewerking kan het repliceren van gegevens gewoon doorgaan, dit in tegenstelling tot bij een failoverbewerking. Voordat u gaat migreren, kunt u net zo veel testfailovers uitvoeren als u wilt. 
  >
  


### <a name="get-started"></a>Aan de slag

Voer de volgende taken uit om het Azure-abonnement en de on-premises VMware-/fysieke omgeving voor te bereiden:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. On-premises [VMware](vmware-azure-tutorial-prepare-on-premises.md) voorbereiden


### <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klik op **Een resource maken** > **Beheerhulpprogramma's** > **Backup en Site Recovery**.
3. Bij **Naam** geeft u de beschrijvende naam **W2K8-migration** op. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
4. Maak resourcegroep **w2k8migrate**.
5. Geef een Azure-regio op. Zie Geografische beschikbaarheid in [Prijsinformatie voor Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) om na te gaan welke regio's er worden ondersteund.
6. Als u de kluis snel wilt kunnen openen via het dashboard, klikt u op **Vastmaken aan dashboard** en vervolgens op **Maken**.

   ![Schermopname van nieuwe opties voor maken van kluis.](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

De nieuwe kluis wordt toegevoegd op het **Dashboard** onder **Alle resources** en op de hoofdpagina van **Recovery Services-kluizen**.


### <a name="prepare-your-on-premises-environment-for-migration"></a>Uw on-premises omgeving voorbereiden op migratie

- Als u virtuele Windows Server 2008-machines wilt migreren die onder VMware worden uitgevoerd, [stelt u de on-premises configuratieserver in onder VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Als de configuratieserver niet kan worden ingesteld als een virtuele VMware-machine, [stelt u de configuratieserver in op een on-premises fysieke server of virtuele machine](physical-azure-disaster-recovery.md#set-up-the-source-environment).

### <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Klik op **Infrastructuur voorbereiden** > **Doel** en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het Resource Manager-implementatiemodel op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.


### <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Klik op **Infrastructuur voor Site Recovery** > **Herstelbeleid** >  **+Herstelbeleid** om een nieuw replicatiebeleid te maken.
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op. Wanneer de replicatie-RPO deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
4. Geef in **Bewaarperiode van het herstelpunt** op hoelang (in uren) de bewaarperiode voor elk herstelpunt is. Gerepliceerde servers kunnen worden hersteld naar een willekeurig punt in dit tijdvenster. Voor computers die worden gerepliceerd naar Premium Storage, wordt een bewaarperiode van maximaal 24 uur ondersteund, en 72 uur voor computers die naar Standard Storage worden gerepliceerd.
5. Geef in **Frequentie van de app-consistente momentopname** **Uit** op. Klik op **OK** om het beleid te maken.

Het beleid wordt automatisch gekoppeld aan de configuratieserver.

> [!WARNING]
> Controleer of u **Uit** hebt opgegeven in de instelling Frequentie van de app-consistente momentopname van het replicatiebeleid. Alleen crashconsistente herstelpunten worden ondersteund tijdens het repliceren van servers waarop Windows Server 2008 wordt uitgevoerd. Als u een andere waarde opgeeft voor de frequentie van de app-consistente momentopname, leidt dat tot foutieve waarschuwingen. Het gevolg is dat de replicatiestatus van de server kritiek wordt vanwege gebrek aan app-consistente herstelpunten.

   ![Schermopname met opties voor maken van replicatiebeleid.](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>Replicatie inschakelen

[Schakel replicatie in](physical-azure-disaster-recovery.md#enable-replication) voor de Windows Server 2008 SP2-/Windows Server 2008 R2 SP1-server die wordt gemigreerd.
   
   ![Schermopname met opties voor toevoegen van fysieke machines.](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Schermopname met opties voor inschakelen van replicatie.](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>Een testmigratie uitvoeren

U kunt een testfailover van de replicatieservers uitvoeren als de initiële replicatie is voltooid en de server de status **Beschermd** heeft gekregen.

Voer een [testfailover](tutorial-dr-drill-azure.md) naar Azure uit om te controleren of alles goed werkt.

   ![Schermopname met de opdracht voor testen van failover.](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Migreren naar Azure

Een failover uitvoeren voor de machines die u wilt migreren.

1. Klik in **Instellingen** > **Gerepliceerde items** op de machine > **Failover**.
2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. Selecteer het meest recente herstelpunt.
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery tracht de virtuele bronmachine af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
4. Controleer of de virtuele Azure-machine in Azure wordt weergegeven zoals verwacht.
5. Klik in **Gerepliceerde items** met de rechtermuisknop op de server > **Migratie voltooien**. Er gebeurt nu het volgende:

    - Het migratieproces wordt voltooid, de replicatie voor de server wordt gestopt en Site Recovery-facturering voor de server wordt gestopt.
    - Met deze stap worden de replicatiegegevens opgeschoond. De gemigreerde VM's worden niet verwijderd.

   ![Schermopname met de opdracht voor voltooien van migratie.](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Annuleer nooit een failover die in uitvoering is**: De replicatie van de server wordt gestopt voordat de failover start. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar wordt de server niet meer gerepliceerd.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Lees de veelgestelde vragen](../migrate/resources-faq.md) over Azure Migrate.