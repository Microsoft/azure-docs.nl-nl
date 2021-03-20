---
title: De configuratie server in Azure Site Recovery implementeren
description: In dit artikel wordt beschreven hoe u een configuratie server implementeert voor VMware-nood herstel met Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359812"
---
# <a name="deploy-a-configuration-server"></a>Een configuratieserver implementeren

U implementeert een on-premises configuratie server wanneer u [Azure site Recovery](site-recovery-overview.md) gebruikt voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure. De configuratie server coördineert de communicatie tussen on-premises VMware en Azure. Het beheert ook de gegevens replicatie. Dit artikel begeleidt u stapsgewijs door de stappen die nodig zijn voor het implementeren van de configuratie server wanneer u virtuele VMware-machines naar Azure repliceert. Als u een configuratie server wilt instellen voor de replicatie van de fysieke server, raadpleegt u [de configuratie server instellen voor herstel na nood gevallen van fysieke servers naar Azure](physical-azure-set-up-source.md).

> [!TIP]
> Zie [architectuur voor nood herstel van VMware naar Azure](vmware-azure-architecture.md)voor meer informatie over de rol van een configuratie server als onderdeel van Azure site Recovery architectuur.

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Een configuratie server implementeren met een eicellen-sjabloon

De configuratie server moet worden ingesteld als een Maxi maal beschik bare VMware-VM met bepaalde minimale vereisten voor hardware en grootte. Voor een handige en eenvoudige implementatie biedt Site Recovery een download bare eicellen-sjabloon (open Virtualization Application) voor het instellen van de configuratie server die voldoet aan alle verplichte vereisten die hier worden vermeld.

## <a name="prerequisites"></a>Vereisten

De minimale hardwarevereisten voor een configuratie server worden in de volgende secties samenvatten.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Machtigings vereisten voor Azure Active Directory

U moet een gebruiker hebben met een van de volgende machtigingen die zijn ingesteld in Azure Active Directory (Azure AD) om de configuratie server te registreren bij Azure Site Recovery Services.

1. De gebruiker moet beschikken over een ontwikkelaar van de toepassing om een toepassing te kunnen maken.
    - Meld u aan bij de Azure Portal om te verifiëren.</br>
    - Ga naar **Azure Active Directory**-  >  **rollen en-beheerders**.</br>
    - Controleer of de Application Developer-rol is toegewezen aan de gebruiker. Als dat niet het geval is, gebruik dan een gebruiker met deze machtiging of neem contact op met een [beheerder om de machtiging in te scha kelen](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles).
    
2. Als de ontwikkelaar van de toepassing niet kan worden toegewezen, moet u ervoor zorgen dat de gebruikers de vlag **toepassingen kunnen registreren** zijn ingesteld op **True** als de gebruiker een identiteit wil maken. Deze machtigingen inschakelen:
    - Meld u aan bij Azure Portal.
    - Ga naar **Azure Active Directory**  >  **gebruikers instellingen**.
    - Onder **app-registraties** **kunnen gebruikers toepassingen registreren**, **Ja** selecteren.

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services *wordt niet ondersteund*. Gebruik een account dat wordt beheerd via [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="download-the-template"></a>De sjabloon downloaden

1. Ga in de kluis naar **Infrastructuur voorbereiden** > **Bron**.
2. Selecteer **+Configuratieserver** in **Bron voorbereiden**.
3. Controleer in **Server toevoegen** of **Configuratieserver voor VMware** wordt weergegeven in **Servertype**.
4. Download de OVA-sjabloon voor de configuratieserver.

   > [!TIP]
   >U kunt de nieuwste versie van de configuratie Server sjabloon ook rechtstreeks downloaden vanuit het [micro soft Download centrum](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> De licentie die is opgenomen in een Scott-sjabloon is een evaluatie licentie die 180 dagen geldig is. Na deze periode moet u een licentie aanschaffen.

## <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren

1. Meld u aan bij de VMware vCenter-Server of vSphere ESXi-host met behulp van de VMware vSphere-client.
2. Selecteer in het menu **bestand** de optie **OVF-sjabloon implementeren** om de wizard **OVF-sjabloon implementeren** te starten.

     ![OVF-sjabloon implementeren](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Voer in **Bron selecteren** de locatie van de gedownloade OVF in.
4. Selecteer **Volgende** in **Beoordelingsdetails**.
5. Accepteer de standaardinstellingen in **Naam en map selecteren** en **Configuratie selecteren**.
6. Selecteer in **Opslag selecteren** **Thick Provision Eager Zeroed** in **Indeling virtuele schijf selecteren**. Het gebruik van de optie Thin Provisioning kan van invloed zijn op de prestaties van de configuratie server.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina's.
8. In **Klaar om te voltooien** doet u het volgende:

    * Als u de virtuele machine met de standaard instellingen wilt instellen, selecteert u **inschakelen nadat de implementatie**  >  **is voltooid**.
    * Als u een extra netwerk interface wilt toevoegen, schakelt u het selectie vakje **na de implementatie** uit en selecteert u vervolgens **volt ooien**. Standaard wordt de configuratieserversjabloon met één NIC geïmplementeerd. Na de implementatie kunt u meer NIC’s toevoegen.

> [!IMPORTANT]
> Wijzig geen resource configuraties, zoals geheugen, kernen en CPU-beperkingen, of wijzig of verwijder geïnstalleerde services of bestanden op de configuratie server na de implementatie. Deze typen wijzigingen zijn van invloed op de registratie van de configuratie server met Azure-Services en de prestaties van de configuratie server.

## <a name="add-an-additional-adapter"></a>Een extra adapter toevoegen

> [!NOTE]
> Er zijn twee Nic's vereist als u van plan bent om de IP-adressen van de bron machines in de failover te bewaren en later terug te vallen op on-premises. Er is één NIC verbonden met de bron machines en de andere NIC wordt gebruikt voor Azure-connectiviteit.

Als u een extra NIC aan de configuratieserver wilt toevoegen, moet u dit doen voordat u de server in de kluis registreert. Het toevoegen van extra adapters wordt niet ondersteund na registratie.

1. Klik in de vSphere Client-inventaris met de rechtermuisknop op de VM en selecteer **Instellingen bewerken**.
2. Selecteer **Toevoegen** > **Ethernet-adapter** bij **Hardware**. Selecteer vervolgens **Volgende**.
3. Selecteer een adaptertype en een netwerk.
4. Als u de virtuele NIC wilt verbinden wanneer de VM is ingeschakeld, selecteert u **verbinden bij inschakelen**. Selecteer vervolgens **volgende**  >  **volt ooien**  >  **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>De configuratie server registreren bij Azure Site Recovery Services

1. Schakel de VM in vanuit de VMware vSphere Client-console.
2. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Meld u nadat de installatie is voltooid bij de virtuele machine aan als de administrator.
4. De eerste keer dat u zich aanmeldt, wordt het Azure Site Recovery-configuratie hulpprogramma binnen een paar seconden gestart.
5. Voer een naam in die wordt gebruikt voor het registreren van de configuratieserver bij Site Recovery. Selecteer vervolgens **Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement.</br>
    a. De referenties moeten toegang hebben tot de kluis waarin u de configuratieserver wilt registreren.</br>
    b. Zorg ervoor dat het gekozen gebruikers account gemachtigd is om een toepassing te maken in Azure. Als u de vereiste machtigingen wilt inschakelen, volgt u de richt lijnen in de sectie [Azure Active Directory machtigings vereisten](#azure-active-directory-permission-requirements).
7. Het hulpprogramma voert enkele configuratietaken uit en start opnieuw op.
8. Meld u opnieuw aan bij de machine. De wizard Configuratie Server beheer wordt in een paar seconden automatisch gestart.

### <a name="configure-settings"></a>Instellingen configureren

1. Selecteer in de wizard voor het beheer van de configuratieserver **Connectiviteit instellen**. Selecteer in de vervolg keuzelijsten eerst de NIC die de ingebouwde proces server gebruikt voor detectie en push installatie van Mobility service op bron machines. Selecteer vervolgens de NIC die door de configuratie server wordt gebruikt voor de connectiviteit met Azure. Selecteer **Opslaan**. U kunt deze instelling niet wijzigen nadat deze is geconfigureerd. Wijzig het IP-adres van een configuratie server niet. Zorg ervoor dat het IP-adres dat is toegewezen aan de configuratie server een statisch IP-adres is en niet een IP-adres.
2. Meld u bij **Recovery Services kluis selecteren** aan Microsoft Azure met de referenties die u in stap 6 van [de configuratie server bij Azure site Recovery Services registreert](#register-the-configuration-server-with-azure-site-recovery-services).
3. Nadat u zich hebt aangemeld, selecteert u uw Azure-abonnement en de relevante resource groep en kluis.

    > [!NOTE]
    > Na de registratie is er geen flexibiliteit om de Recovery Services-kluis te wijzigen.
    > Het wijzigen van een Recovery Services-kluis vereist de ontkoppeling van de configuratie server van de huidige kluis en de replicatie van alle beveiligde virtuele machines onder de configuratie server is gestopt. Zie voor meer informatie [beheer van de configuratie server voor nood herstel voor VMware VM](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Bij **Software van derden installeren**:

    |Scenario   |Te volgen stappen  |
    |---------|---------|
    |Kan ik MySQL hand matig downloaden en installeren?     |  Ja. Down load de MySQL-toepassing, plaats deze in de map **C:\Temp\ASRSetup** en installeer hand matig. Nadat u de voor waarden hebt geaccepteerd en **downloaden en installeren** selecteert, is de portal *al geïnstalleerd*. U kunt door gaan met de volgende stap.       |
    |Kan ik voor komen dat MySQL online worden gedownload?     |   Ja. Plaats uw MySQL-installatie toepassing in de map **C:\Temp\ASRSetup**. Accepteer de voor waarden, selecteer **downloaden en installeren** en de portal gebruikt het installatie programma dat u hebt toegevoegd om de toepassing te installeren. Nadat de installatie is voltooid, gaat u verder met de volgende stap.    |
    |Ik wil MySQL downloaden en installeren via Azure Site Recovery.    |  Accepteer de gebruiksrecht overeenkomst en selecteer **downloaden en installeren**. Nadat de installatie is voltooid, gaat u verder met de volgende stap.       |

5. Bij het valideren van de configuratie van het **toestel** worden de vereisten gecontroleerd voordat u doorgaat.
6. Voer op de **vCenter Server/VSphere ESXi-server configureren** de FQDN of het IP-adres van de vCenter-Server of vSphere-host in, waarbij de vm's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
7. Voer referenties in die door de configuratieserver moeten worden gebruikt voor verbinding met de VMware-server. Site Recovery gebruikt deze referenties voor het automatisch detecteren van VMware-VM’s die beschikbaar zijn voor replicatie. Selecteer **Toevoegen** > **Doorgaan**. De referenties die u hier opgeeft, worden lokaal opgeslagen.
8. Voer bij **referenties voor virtuele machine configureren** de gebruikers naam en het wacht woord van de virtuele machines in om de Mobility-service automatisch te installeren tijdens de replicatie. Voor **Windows** -computers heeft het account lokale beheerders rechten nodig voor de computers die u wilt repliceren. Voor **Linux** geeft u Details voor het hoofd account op.
9. Selecteer **Configuratie voltooien** om de registratie te voltooien.
10. Nadat de registratie is voltooid, opent u Azure Portal en controleert u of de configuratieserver en de VMware-server worden weergegeven op **Recovery Services-kluis** > **Beheer** > **Site Recovery-infrastructuur** > **Configuratieservers**.

## <a name="upgrade-the-configuration-server"></a>De configuratie Server upgraden

Als u de configuratie server wilt bijwerken naar de nieuwste versie, raadpleegt u [de configuratie server voor nood herstel voor VMware-Vm's beheren](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Zie [service-updates in site Recovery](service-updates-how-to.md)voor instructies over het upgraden van alle site Recovery onderdelen.

## <a name="manage-the-configuration-server"></a>De configuratieserver beheren

Om onderbrekingen te voor komen, moet u ervoor zorgen dat het IP-adres van de configuratie server niet verandert nadat de configuratie server is geregistreerd bij een kluis. Zie [de configuratie server voor nood herstel voor VMware-Vm's beheren](vmware-azure-manage-configuration-server.md)voor meer informatie over algemene beheer taken voor configuratie servers.

## <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

Raadpleeg het [artikel over probleem oplossing](vmware-azure-troubleshoot-configuration-server.md) voor het oplossen van problemen met de implementatie & verbinding.

## <a name="faqs"></a>Veelgestelde vragen

* Hoe lang is de licentie op een configuratie server die is geïmplementeerd via OVF geldig? Wat gebeurt er als ik de licentie niet opnieuw Activeer?

    De licentie voor een eicellen-sjabloon is een evaluatie licentie die gedurende 180 dagen geldig is. Voordat het verloopt, moet u de licentie activeren. Als dat niet het geval is, kan de configuratie server veelvuldig worden afgesloten en kan dit leiden tot replicatie activiteiten. Zie voor meer informatie [beheer van de configuratie server voor nood herstel voor VMware VM](vmware-azure-manage-configuration-server.md#update-windows-license).

* Kan ik de virtuele machine waarop de configuratie server is geïnstalleerd voor verschillende doel einden gebruiken?

    Nee. Gebruik de virtuele machine uitsluitend voor het doel van de configuratie server. Zorg ervoor dat u alle specificaties volgt die worden vermeld in [vereisten](#prerequisites) voor efficiënt beheer van herstel na nood gevallen.
* Kan ik de kluis die al is geregistreerd op de configuratie server wijzigen met een nieuwe kluis?

    Nee. Nadat een kluis is geregistreerd bij de configuratie server, kan deze niet meer worden gewijzigd.
* Kan ik dezelfde configuratie server gebruiken voor het beveiligen van fysieke en virtuele machines?

    Ja. Dezelfde configuratie server kan worden gebruikt voor het repliceren van fysieke en virtuele machines. De fysieke machine kan echter alleen worden teruggezet naar een virtuele VMware-machine.
* Wat is het doel van een configuratie server en waar wordt deze gebruikt?

    Zie voor meer informatie over de configuratie server en de functies van [VMware naar Azure-replicatie architectuur](vmware-azure-architecture.md).
* Waar vind ik de nieuwste versie van de configuratie server?

    Zie [de configuratie Server upgraden](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)voor stappen om de configuratie server via de portal bij te werken. Zie [service-updates in site Recovery](./service-updates-how-to.md)voor instructies over het upgraden van alle site Recovery onderdelen.
* Waar kan ik de wachtwoordzin voor de configuratie server downloaden?

    Als u de wachtwoordzin wilt downloaden, raadpleegt u [de configuratie server voor nood herstel voor VMware-Vm's beheren](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Kan ik de wachtwoordzin wijzigen?

    Nee. Wijzig de wachtwoordzin van de configuratie server niet. Een wijziging in de wachtwoordzin verbreekt de replicatie van beveiligde machines en leidt tot een kritieke status.
* Waar kan ik een kluis registratie sleutel downloaden?

    Selecteer    >  **site Recovery infrastructuur**  >  **configuratie servers** beheren in Recovery Services kluis. In **servers** selecteert u **registratie sleutel downloaden** om het bestand met kluis referenties te downloaden.
* Kan ik een bestaande configuratie server klonen en gebruiken voor replicatie-indeling?

    Nee. Het gebruik van een gekloond configuratie Server onderdeel wordt niet ondersteund. Het klonen van een scale-out proces server is ook een niet-ondersteund scenario. Het klonen van Site Recovery onderdelen is van invloed op voortdurende replicaties.

* Kan ik het IP-adres van een configuratie server wijzigen?

    Nee. Wijzig het IP-adres van een configuratie server niet. Zorg ervoor dat alle IP-adressen die zijn toegewezen aan de configuratie server statische IP-adressen zijn en geen DHCP Ip's.
* Kan ik een configuratie server instellen in azure?

    Stel in een on-premises omgeving een configuratie server in met een directe detectie lijn met behulp van het v-centrum en om latentie van gegevens overdracht te minimaliseren. U kunt geplande back-ups maken van configuratie server voor [failback-doel einden](vmware-azure-manage-configuration-server.md#failback-requirements).

* Kan ik het cache stuur programma wijzigen op een configuratie server of scale-out proces server?

    Nee, het cache stuur programma kan niet worden gewijzigd nadat het instellen is voltooid.

Zie [Veelgestelde vragen over Configuration Server](vmware-azure-common-questions.md#configuration-server)voor meer informatie over configuratie servers.

## <a name="next-steps"></a>Volgende stappen

Stel herstel na nood gevallen van [virtuele VMware-machines](vmware-azure-tutorial.md) in op Azure.