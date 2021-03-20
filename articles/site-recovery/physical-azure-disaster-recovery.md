---
title: Herstel na nood gevallen instellen van fysieke on-premises servers met Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen voor Azure voor on-premises Windows-en Linux-servers, met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 9b05d9952628e550beae5cedc49e051936a9d633
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87927280"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Herstel na nood gevallen naar Azure instellen voor on-premises fysieke servers

De [Azure site Recovery](site-recovery-overview.md) -service draagt bij aan uw strategie voor herstel na nood gevallen door replicatie, failover en failback van on-premises machines en virtuele Azure-machines (vm's) te beheren en te organiseren.

Deze zelf studie laat zien hoe u herstel na nood gevallen instelt voor on-premises fysieke Windows-en Linux-servers naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure-en on-premises vereisten instellen
> * Een Recovery Services-kluis maken voor Site Recovery 
> * De bron-en doel replicatie omgevingen instellen
> * Een replicatiebeleid maken
> * Replicatie inschakelen voor een server

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen](physical-azure-architecture.md) voor dit scenario.
- Raadpleeg de [ondersteuningsvereisten](vmware-physical-secondary-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat de servers die u wilt repliceren aan de [vereisten voor Azure-VM'S](vmware-physical-secondary-support-matrix.md#replicated-vm-support) voldoen.
- Azure voorbereiden. U hebt een Azure-abonnement, een virtueel Azure-netwerk en een opslag account nodig.
- Bereid een account voor de automatische installatie van de Mobility-service voor op elke server die u wilt repliceren.

Houd rekening met het volgende voordat u begint:

- Na een failover naar Azure kunnen fysieke servers niet meer worden teruggezet naar on-premises fysieke machines. U kunt alleen een failback uitvoeren naar virtuele VMware-machines. 
- In deze zelf studie stelt u de nood herstel van fysieke servers in op Azure met de eenvoudigste instellingen. Als u meer wilt weten over andere opties, lees dan onze hand leidingen:
    - Stel de [replicatie bron](physical-azure-set-up-source.md)in, met inbegrip van de site Recovery configuratie server.
    - Stel het [replicatiedoel](physical-azure-set-up-target.md) in.
    - Configureer een [replicatiebeleid](vmware-azure-set-up-replication.md) en schakel [replicatie in](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Een Azure-account instellen

Een micro soft [Azure-account](https://azure.microsoft.com/)ophalen.

- U kunt beginnen met een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
- Meer informatie over de [prijzen van site Recovery](site-recovery-faq.md#pricing)en ontvang [prijs informatie](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ontdek welke [regio's worden ondersteund](https://azure.microsoft.com/pricing/details/site-recovery/) voor site Recovery.

### <a name="verify-azure-account-permissions"></a>Azure-accountmachtigingen verifiëren

Zorg ervoor dat uw Azure-account machtigingen heeft voor de replicatie van virtuele machines naar Azure.

- Bekijk de [machtigingen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) die u nodig hebt om machines naar Azure te repliceren.
- Controleer en wijzig de machtigingen voor [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). 



### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Stel een [Azure-netwerk](../virtual-network/quick-create-portal.md)in.

- Azure-VM's worden in dit netwerk geplaatst wanneer ze zijn gemaakt na de failover.
- Het netwerk moet zich in dezelfde regio bevinden als de Recovery Services-kluis


## <a name="set-up-an-azure-storage-account"></a>Een Azure-opslagaccount instellen

Stel een [Azure-opslagaccount](../storage/common/storage-account-create.md) in.

- Site Recovery repliceert on-premises machines naar Azure Storage. Virtuele Azure-machines worden gemaakt vanuit opslag nadat de failover heeft plaatsgevonden.
- Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobility-service moet worden geïnstalleerd op elke server die u wilt repliceren. Site Recovery installeert deze service automatisch wanneer u replicatie voor de server inschakelt. Voor automatische installatie moet u een account voorbereiden waarmee Site Recovery toegang kan krijgen tot de server.

- U kunt een domein- of lokaal account gebruiken
- Voor Windows-accounts schakelt u als u geen domeinaccount gebruikt toegangsbeheer voor externe gebruikers uit op de lokale computer. U doet dit door in het register onder **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** de DWORD-vermelding **LocalAccountTokenFilterPolicy** met een waarde van 1 toe te voegen.
- Als u de registervermelding wilt toevoegen om de instelling vanuit een CLI uit te schakelen, typt u:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Voor Linux moet het account een hoofdaccount zijn op de Linux-bronserver.


## <a name="create-a-vault"></a>Een kluis maken

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

Selecteer wat u wilt repliceren en waarnaar u het wilt repliceren.

1. Klik op **Recovery Services-kluizen** > kluis.
2. Klik in het resourcemenu op **Site Recovery** > **Infra structuur voorbereiden** > **Beveiligingsdoel**.
3. Selecteer in **doel van beveiliging** de optie **naar Azure**  >  **niet gevirtualiseerd/Overig**.

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Stel de configuratieserver in, registreer deze in de kluis en detecteer VM's.

1. Klik op **Site Recovery** > **Infrastructuur voorbereiden** > **Bron**.
2. Als u geen configuratieserver hebt, klikt u op **+ Configuratieserver**.
3. Controleer bij **Server toevoegen** of **Configuratieserver** wordt weergegeven in **Servertype**.
4. Download het installatiebestand voor de Geïntegreerde Setup van Site Recovery.
5. Download de registratiesleutel voor de kluis. U hebt dit nodig wanneer u de Geïntegreerde Setup uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.

   ![Scherm afbeelding met de opties voor het downloaden van het installatie bestand en de registratie sleutel.](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>De configuratieserver in de kluis registreren

Doe het volgende voordat u begint: 

#### <a name="verify-time-accuracy"></a>Nauwkeurigheid van tijd controleren
Zorg ervoor dat op de computer met de configuratieserver de systeemklok met een [tijdserver](/windows-server/networking/windows-time-service/windows-time-service-top) is gesynchroniseerd. Deze moeten overeenkomen. Als deze 15 minuten voor- of achterloopt, kan de installatie mislukken.

#### <a name="verify-connectivity"></a>Connectiviteit verifiëren
Zorg ervoor dat de computer toegang kan krijgen tot deze URL's op basis van uw omgeving: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Op IP-adres gebaseerde firewallregels moeten communicatie toestaan voor alle Azure-URL's die hierboven worden vermeld via de HTTPS-poort (443). Om het IP-bereik te vereenvoudigen en te beperken, wordt het aanbevolen om URL-filtering uit te voeren.

- **Commerciële IP's**: de [Azure Data Center IP-adresbereiken](https://www.microsoft.com/download/confirmation.aspx?id=41653) en de HTTPS-poort (443) toestaan. IP-adresbereiken toestaan voor de Azure-regio van uw abonnement ter ondersteuning van de AAD-, back-up-, replicatie-en opslag-URL's.  
- **Government-IP's**: de [Azure Government Data Center IP-bereiken](https://www.microsoft.com/en-us/download/details.aspx?id=57063) en de HTTPS-poort (443) toestaan voor alle USGov-regio's (Virginia, Texas, Arizona en Iowa) voor de ondersteuning van AAD-, back-up-, replicatie-en opslag-URL's.  

#### <a name="run-setup"></a>Setup uitvoeren
Voer Geïntegreerde Setup uit als een lokale beheerder om de configuratieserver te installeren. De processerver en de hoofddoelserver worden ook standaard geïnstalleerd op de configuratieserver.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]


## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Klik op **Infrastructuur voorbereiden** > **Doel** en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het doelimplementatiemodel op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

   ![Scherm afbeelding van de opties voor het instellen van de doel omgeving.](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Klik op **Infrastructuur voor Site Recovery** > **Herstelbeleid** >  **+Herstelbeleid** om een nieuw replicatiebeleid te maken.
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op. Deze waarde geeft aan hoe vaak gegevensherstelpunten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
4. Geef in **Bewaarperiode van het herstelpunt** op hoelang (in uren) de bewaarperiode voor elk herstelpunt is. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster. Voor computers die worden gerepliceerd naar Premium Storage, wordt een bewaarperiode van maximaal 24 uur ondersteund, en 72 uur voor computers die naar Standard Storage worden gerepliceerd.
5. Geef in **Frequentie van de app-consistente momentopname** op hoe vaak (in minuten) er herstelpunten moeten worden gemaakt met toepassingsconsistente momentopnamen. Klik op **OK** om het beleid te maken.

    ![Scherm afbeelding van de opties voor het maken van een replicatie beleid.](./media/physical-azure-disaster-recovery/replication-policy.png)


Het beleid wordt automatisch gekoppeld aan de configuratieserver. Standaard wordt automatisch een bijbehorend beleid gemaakt voor failback. Als het replicatiebeleid bijvoorbeeld **rep-policy** is, wordt het failbackbeleid **rep-policy-failback** gemaakt. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

## <a name="enable-replication"></a>Replicatie inschakelen

Schakel replicatie in voor elke server.

- Site Recovery installeert de Mobility-service wanneer replicatie wordt ingeschakeld.
- Wanneer u replicatie voor een server inschakelt, kan het 15 minuten of langer duren voordat wijzigingen zijn doorgevoerd en in de portal worden weergegeven.

1. Klik op **Toepassing repliceren** > **Bron**.
2. Selecteer in **Bron** de configuratieserver.
3. Selecteer bij **Type machine** de optie **Fysieke machines**.
4. Selecteer de processerver (de configuratieserver). Klik vervolgens op **OK**.
5. Selecteer in **Doel** het abonnement en de resourcegroep waarin u de Azure-VM's na failover wilt maken. Kies het implementatiemodel dat u wilt gebruiken in Azure (klassiek of resourcebeheer).
6. Selecteer het Azure-opslagaccount dat u wilt gebruiken voor het repliceren van gegevens. 
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle machines die u voor beveiliging selecteert. Selecteer **Later configureren** om per machine een Azure-netwerk te selecteren. 
9. Klik in **Fysieke machines** op **+Fysieke machine**. Geef de naam en het IP-adres op. Selecteer het besturingssysteem van de computer die u wilt repliceren. Het duurt enkele minuten voordat de servers zijn gedetecteerd en worden weergegeven. 
10. Selecteer in **Eigenschappen** > **Eigenschappen configureren** het account dat door de processerver zal worden gebruikt om automatisch de Mobility-service op de computer te installeren.
11. Controleer of het juiste replicatiebeleid is geselecteerd in **Replicatie-instellingen** > **Replicatie-instellingen configureren**. 
12. Klik op **Replicatie inschakelen**. U kunt de voortgang van de taak **Beveiliging inschakelen** volgen via **Instellingen** > **Taken** > **Site Recovery-taken**. Nadat de taak **Beveiliging voltooien** is uitgevoerd, is de machine klaar voor een mogelijke failover.


Voor het bewaken van servers die u toevoegt, kunt u de laatste detectietijd voor servers controleren in **Configuratieservers** > **Laatst contact om**. Als u machines wilt toevoegen zonder te wachten op de geplande detectietijd, markeert u de configuratieserver (klik er niet op) en klikt u op **Vernieuwen**.

## <a name="next-steps"></a>Volgende stappen

[Voer een nood herstel analyse uit](tutorial-dr-drill-azure.md).
