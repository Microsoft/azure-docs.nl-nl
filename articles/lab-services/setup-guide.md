---
title: Gids voor de installatie van een versneld Lab voor Azure Lab Services
description: Als u een Lab maakt, kunt u deze hand leiding gebruiken om snel een Lab-account in te stellen op uw school.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021725"
---
# <a name="lab-setup-guide"></a>Hand leiding voor Lab-installatie

In deze hand leiding leert u hoe u een Lab voor studenten maakt op uw school.

Het proces voor het publiceren van een lab naar uw studenten kan tot enkele uren duren. De hoeveelheid instel tijd is afhankelijk van het aantal virtuele machines (Vm's) dat u in uw Lab wilt maken. Sta ten minste een dag toe om ervoor te zorgen dat het lab goed werkt en om voldoende tijd te bieden voor het publiceren van de virtuele machines van uw studenten.

## <a name="understand-the-lab-requirements-of-your-class"></a>Meer informatie over de Lab-vereisten van uw klasse

Voordat u een nieuw Lab instelt, moet u rekening houden met de volgende vragen.

### <a name="what-software-requirements-does-the-class-have"></a>Welke software vereisten heeft de klasse?

Raadpleeg de leer doelen van uw klasse als u besluit welk besturings systeem, welke toepassingen en hulpprogram ma's u nodig hebt om te installeren op de virtuele machines van het lab. Als u virtuele lab-Vm's wilt instellen, hebt u drie opties:

- **Een Azure Marketplace-installatie kopie gebruiken**: Azure Marketplace biedt honderden installatie kopieën die u kunt gebruiken bij het maken van een lab. Voor sommige klassen bevat een van deze installatie kopieën mogelijk al alles wat u nodig hebt voor uw klasse.

- **Een nieuwe aangepaste installatie kopie maken**: u kunt uw eigen aangepaste installatie kopie maken met behulp van een Azure Marketplace-installatie kopie als uitgangs punt. U kunt het vervolgens aanpassen door extra software te installeren en wijzigingen aan te brengen in de configuratie.

- **Een bestaande aangepaste installatie kopie gebruiken**: u kunt aangepaste installatie kopieën die u eerder hebt gemaakt, of afbeeldingen die zijn gemaakt door andere beheerders of docenten op uw school, opnieuw gebruiken. Als u aangepaste installatie kopieën wilt gebruiken, moeten uw beheerders een galerie met gedeelde installatie kopieën instellen.  Een galerie met gedeelde installatie kopieën is een opslag plaats die wordt gebruikt voor het opslaan van aangepaste installatie kopieën.

> [!NOTE]
> Uw beheerders zijn verantwoordelijk voor het inschakelen van Azure Marketplace-installatie kopieën en aangepaste installatie kopieën, zodat u ze kunt gebruiken. Coördineer met uw IT-afdeling om ervoor te zorgen dat de installatie kopieën die u nodig hebt, zijn ingeschakeld. Aangepaste installatie kopieën die u maakt, worden automatisch ingeschakeld voor gebruik in Labs waarvan u de eigenaar bent.

### <a name="what-hardware-requirements-does-the-class-have"></a>Welke hardwarevereisten heeft de klasse?

U kunt kiezen uit verschillende reken grootten:

- **Geneste virtualisatie-grootten**: Hiermee kunt u studenten toegang geven tot een virtuele machine die meerdere geneste virtuele machines kan hosten. U kunt deze reken grootte bijvoorbeeld gebruiken voor netwerken of ethische hacking-klassen.

- **GPU-grootten**: Hiermee kunnen uw studenten computer-intensieve typen toepassingen gebruiken. Deze keuze wordt bijvoorbeeld vaak gebruikt met kunst matige intelligentie en machine learning.

Zie voor hulp bij het selecteren van de juiste VM-grootte:
- [VM-grootte](./administrator-guide.md#vm-sizing)
- [Van een fysiek Lab overschakelen naar Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Omdat de beschik baarheid van de berekenings grootte varieert per regio, zijn er mogelijk minder grootten beschikbaar voor uw Lab. Over het algemeen moet u de kleinste reken grootte selecteren die aansluit bij uw behoeften. Met Azure Lab Services kunt u later een nieuwe Lab met een grotere reken capaciteit instellen, als dat nodig is.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Welke afhankelijkheden heeft de klasse op externe Azure-of netwerk bronnen?
Uw Lab-Vm's hebben mogelijk toegang nodig tot externe bronnen, zoals een Data Base, een bestands share of een licentie server.  Als u wilt dat uw Lab-Vm's externe bronnen kunnen gebruiken, moet u met uw IT-beheerders instemmen.

> [!NOTE]
> U moet overwegen of u de omgevings afhankelijkheid van uw Lab op externe bronnen kunt verlagen door netwerk bronnen rechtstreeks op de virtuele machine op te geven. Als u bijvoorbeeld wilt voor komen dat gegevens uit een externe data base hoeven te worden gelezen, kunt u de data base rechtstreeks op de virtuele machine installeren.  

### <a name="how-will-you-control-costs"></a>Hoe kunt u de kosten beheren?
Lab Services maakt gebruik van een prijs model voor betalen per gebruik, wat betekent dat u alleen betaalt voor de tijd dat een Lab-VM wordt uitgevoerd. Gebruik voor het beheren van de kosten een of meer van de volgende opties:

- **Planning**: gebruik schema's om automatisch te bepalen wanneer uw Lab-vm's worden gestart en afgesloten.
- **Quota**: gebruik quota's om het aantal uren te bepalen dat studenten buiten de geplande uren toegang hebben tot een virtuele machine.  Wanneer een student een VM gebruikt en een quotum bereikt, wordt de VM automatisch afgesloten.  De student kan de virtuele machine niet opnieuw opstarten, tenzij u het quotum verhoogt.
- **Automatisch afsluiten**: wanneer u de instelling voor automatisch afsluiten inschakelt, worden Windows-vm's automatisch afgesloten nadat een student de verbinding met een Remote Desktop Protocol-sessie (RDP) heeft verbroken. Deze instelling is standaard uitgeschakeld.

Zie voor meer informatie over het beheren van kosten:
- [Kosten schatten](./cost-management-guide.md#estimate-the-lab-costs)
- [Kosten beheren](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Hoe kunnen studenten hun werk opslaan?
Elke afzonderlijke student krijgt een VM voor de levens duur van het lab. Studenten kunnen hun werk opslaan:

- Naar de virtuele machine.
- Naar een externe locatie, zoals OneDrive of GitHub. Het is mogelijk om OneDrive automatisch te configureren voor studenten op hun Lab-Vm's.

> [!NOTE]
> Om ervoor te zorgen dat uw studenten geen toegang meer hebben tot hun opgeslagen werk buiten het lab en nadat de klasse is geëindigd, raden we u aan hun werk op te slaan in een externe opslag plaats.

### <a name="how-will-students-connect-to-their-vms"></a>Hoe maken studenten verbinding met hun Vm's?
Voor RDP-verbindingen met virtuele Windows-machines raden studenten de [Microsoft extern bureaublad-client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)te gebruiken. De Extern bureaublad-client ondersteunt Mac-, Chromebook-en Windows-apparaten.

Voor virtuele Linux-machines kunnen studenten gebruikmaken van het SSH-protocol (Secure Shell) of RDP. Als u wilt dat studenten verbinding maken met behulp van RDP, moet u de benodigde RDP-en Graphical User Interface-pakketten (GUI) installeren en configureren.

### <a name="will-students-also-use-microsoft-teams"></a>Zullen studenten ook micro soft-teams gebruiken?
Azure Lab Services integreert met micro soft-teams zodat faculteits leden hun Labs in teams kunnen maken en beheren.  Op dezelfde manier hebben studenten toegang tot hun Labs in teams.

Zie [Azure Lab Services in micro soft teams](./lab-services-within-teams-overview.md)voor meer informatie.

## <a name="set-up-your-lab"></a>Uw lab instellen

Nadat u de vereisten voor het lab van uw klasse hebt begrepen, bent u klaar om deze in te stellen. Volg de koppelingen in deze sectie voor meer informatie. Er worden ook instructies gegeven voor het instellen van Labs in teams.

1. **Maak een Lab**. Zie de volgende zelfstudies:
    - [Een leslokaallab maken](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Een lab maken in teams](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Zie [geneste virtualisatie inschakelen](./how-to-enable-nested-virtualization-template-vm.md)als uw klasse geneste virtualisatie vereist.

1. **Afbeeldingen aanpassen en Lab-vm's publiceren**. Als u verbinding wilt maken met een speciale virtuele machine die de sjabloon-VM wordt genoemd, raadpleegt u:
    - [Een sjabloon-VM maken en beheren](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Een gedeelde installatiekopiegalerie gebruiken](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Als u Windows gebruikt, raadpleegt u ook [een Windows-sjabloon instellen VM](./how-to-prepare-windows-template.md). Deze instructies omvatten stappen voor het instellen van OneDrive en Microsoft Office voor uw studenten.

1. **Groep en capaciteit van de virtuele machine beheren**. U kunt de capaciteit van de virtuele machine eenvoudig omhoog of omlaag schalen, afhankelijk van uw klasse. Houd er rekening mee dat het verg Roten van de VM-capaciteit enkele uren kan duren, omdat er nieuwe Vm's worden ingesteld. Zie de volgende artikelen:
    - [Een VM-groep instellen en beheren](./how-to-set-virtual-machine-passwords.md)
    - [Een VM-groep beheren in Lab-Services in teams](./how-to-manage-vm-pool-within-teams.md)

1. **Test gebruikers toevoegen en beheren**. Zie voor informatie over het toevoegen van gebruikers aan uw Lab:
   - [Gebruikers toevoegen aan het lab](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Uitnodigingen voor gebruikers verzenden](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Gebruikers lijsten van Lab-Services beheren in teams](./how-to-manage-user-lists-within-teams.md)

    Zie [studenten accounts](./how-to-configure-student-usage.md#student-accounts)voor meer informatie over de typen accounts die studenten kunnen gebruiken.
  
1. **Stel kosten besturings elementen** in. Als u een planning wilt instellen, quota's wilt maken en automatisch afsluiten wilt inschakelen, raadpleegt u de volgende zelf studies:

   - [Een planning instellen](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > Afhankelijk van het besturings systeem dat u hebt geïnstalleerd, kan het enkele minuten duren voordat de virtuele machine wordt gestart. Om ervoor te zorgen dat een Lab-VM gereed is voor gebruik tijdens de geplande uren, raden we u aan om deze 30 minuten vooraf te starten.

   - [Quota instellen voor gebruikers](./how-to-configure-student-usage.md#set-quotas-for-users) en [aanvullende quota's instellen voor specifieke gebruikers](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [De functie Automatisch afsluiten wanneer de verbinding is verbroken inschakelen](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Schema's en quota zijn niet van toepassing op de sjabloon-VM, maar de instellingen voor automatisch afsluiten zijn van toepassing. 
        > 
        > Wanneer u een Lab maakt, wordt de VM van de sjabloon gemaakt, maar niet gestart. U kunt de sjabloon-VM starten, er verbinding mee maken, vereiste software voor het lab installeren en deze vervolgens publiceren. Wanneer u de sjabloon-VM publiceert, wordt deze automatisch afgesloten als u deze nog niet hand matig hebt gedaan. 
        > 
        > Bij het uitvoeren van sjabloon-Vm's worden *kosten* in rekening gebracht wanneer ze worden uitgevoerd. Zorg er dus voor dat de sjabloon-VM wordt afgesloten wanneer deze niet hoeft te worden uitgevoerd.

    - [Schema's voor Lab-Services maken en beheren in teams](./how-to-create-schedules-within-teams.md) 

1. **Gebruik het dash board**. Zie [het leslokaal Lab-dash board gebruiken](./use-dashboard.md)voor instructies.

    > [!NOTE]
    > De geschatte kosten die worden weer gegeven op het dash board zijn de maximale kosten die u kunt verwachten voor het gebruik van het lab. Er worden bijvoorbeeld *geen* kosten in rekening gebracht voor ongebruikte quotum uren door uw studenten. De geschatte kosten zijn *niet* van toepassing op de kosten voor het gebruik van de sjabloon-VM, de galerie met gedeelde afbeeldingen of wanneer de test Maker een gebruikers computer start.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen als onderdeel van het beheer van uw Labs:
- [Gebruik van leslokaal Lab bijhouden](tutorial-track-usage.md)  
- [Een leslokaallab openen](tutorial-connect-virtual-machine-classroom-lab.md)
