---
title: Aan de slag met Azure Lab Services
description: In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165039"
---
# <a name="get-started-with-lab-services"></a>Aan de slag met Lab-Services 

Azure Lab Services biedt studenten en docenten rechtstreeks vanaf hun eigen computers toegang tot virtual computer labs.

Docenten moeten weten hoe ze leerlingen/ouders kunnen leren om Lab-services te gebruiken in hun instructie door middel van een-op-een door hardware uitgegeven student. Als gevolg hiervan hebben studenten mogelijk toegang tot de industrie standaard software die vereist is voor hun Program ma's door middel van Virtual Machines (VM). 

Een VM is een virtuele omgeving die fungeert als een virtuele computer. Vm's hebben hun eigen processor, geheugen en opslag. Vm's bieden een vervanging voor een echte machine en kunnen gebruikers toegang geven tot besturings systemen en software zonder dat ze op hun eigen apparaat hoeven te zijn. Azure Lab Services biedt studenten een hulp programma voor het openen en navigeren van Vm's en voor mede werkers om hun virtuele computer Labs te beheren. 

In dit artikel vindt u informatie over het maken van toegang tot, het beheren en leren van studenten/ouders om Azure Lab Services te gebruiken.

## <a name="key-concepts"></a>Belangrijkste concepten

### <a name="quota-hours"></a>Quotum uren

Studenten hebben toegang tot hun Vm's op elk gewenst moment tijdens de geplande periode, zonder dat dit van invloed is op hun quotum uren. Quota-uren worden ingesteld voor het hele semester en bepalen het aantal uren dat een student de virtuele machine buiten de regel matig geplande tijd kan gebruiken.

8 uur per week, ingesteld op zondag-niet cumulatief.

Zie [quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)voor meer informatie.

### <a name="automatic-shut-down"></a>Automatisch afsluiten

Automatisch afsluiten is ingeschakeld voor de Labs om kosten te besparen en de quota-uren van studenten op te slaan. Als u automatisch afsluit, worden de Vm's na een periode van inactiviteit (geen muis-of toetsenbord invoer) ingeschakeld. Automatisch afsluiten in twee fasen: de verbinding van een student met de virtuele machine na een periode van inactiviteit wordt verbroken. Op dit moment wordt de virtuele machine nog steeds **uitgevoerd** en kunnen de studenten verbinding maken. Na een andere periode van inactiviteit nadat de verbinding is verbroken, wordt de VM vanzelf afgesloten.

Automatisch afsluiten is een belang rijk hulp programma voor het besparen van de kosten, maar biedt wel een uitdaging voor studenten om hun werk op te slaan en grote project bestanden te renderen. Als uw studenten vaak niet worden verbonden of als de Vm's te snel worden uitgeschakeld, neemt u contact op met uw CTE-beheerder. 

Zie [Configure Automatic Shutdown of vm's voor een Lab-account](how-to-configure-lab-accounts.md)voor meer informatie.

### <a name="managing-virtual-machines"></a>Virtuele machines beheren

Door het lab te beheren, kunnen docenten dingen beheersen, zoals Lab-capaciteit (het aantal Vm's dat beschikbaar is voor studenten) en het hand matig starten, stoppen of opnieuw instellen van Vm's. docenten kunnen ook verbinding maken met Vm's om studenten interface te ervaren, bestanden te openen en problemen met software of de virtuele machine zelf op te lossen.

Het belangrijkste dat u moet onthouden bij het beheren van de virtuele machines is dat telkens wanneer er een machine wordt **uitgevoerd**, er kosten in rekening worden gebracht, zelfs als er geen verbinding is met de VM.

## <a name="lab-dashboards"></a>Lab-Dash boards

### <a name="overview"></a>Overzicht

Dash boards voor Labs in Azure Lab Services bieden een moment opname van verschillende aspecten van een bepaald Lab, zoals VM-informatie, het aantal toegewezen en niet-toegewezen Vm's, het aantal geregistreerde en niet-geregistreerde gebruikers en informatie over Lab-schema's. 

> [!NOTE]
> Hoewel de meeste beheer aspecten van het dash board en de [Azure Lab Services website](https://labs.azure.com/) zichtbaar zijn voor docenten, zijn de machtigingen die specifiek zijn voor uw rol mogelijk van invloed op de mogelijkheid om bepaalde criteria in het dash board te wijzigen. Als u een probleem ondervindt met uw specifieke Lab-installatie, neem dan contact op met uw CTE-beheerder.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="de Azure Lab Services portal":::

### <a name="examine-a-dashboard"></a>Een dash board onderzoeken

1. Ga naar de [Azure Lab Services-website](https://labs.azure.com/)en meld u aan.
1. Selecteer uw Lab.
1. Er wordt een **dash board** weer gegeven aan de linkerkant van het venster. Klik op het **dash board** en er worden een aantal tegels in het dash board weer geven.
1. **&** hieronder vindt u ook tegels voor sjablonen, groepen van virtuele machines, gebruikers en schema's, waarmee u aspecten kunt aanpassen en meer details over het leslokaal Lab kunnen bekijken.

    * Sjabloon: beschrijft de datum waarop de sjabloon is gemaakt en de laatste publicatie. 
    * Virtuele-machine groep: aantal toegewezen en niet-toegewezen Vm's.
    * Gebruikers-aantal geregistreerde gebruikers en gebruikers die zijn toegevoegd aan het lab, maar die niet zijn geregistreerd.
    * Planningen: geeft toekomstige geplande gebeurtenissen weer voor het lab en een koppeling om meer gebeurtenissen weer te geven.

Zie [dash board gebruiken](use-dashboard.md)voor meer informatie.

### <a name="manually-starting-vms"></a>Vm's hand matig starten

1. Op de pagina **virtuele-machine groep** kunt u alle vm's in een Lab starten door te klikken op de knop **Alles starten** boven aan de pagina.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Start uw Vm's":::
1. U kunt afzonderlijke Vm's starten door te klikken op de status in-en uitschakelen. 

    De wissel knop wordt **gelezen als** de VM wordt gestart, en vervolgens **uitgevoerd** zodra de VM is gestart.
1. U kunt ook een aantal virtuele machines selecteren met behulp van de controles links van de kolom **naam** . 

    Wanneer u de gewenste Vm's hebt geselecteerd, klikt u op de knop **Start** boven aan het scherm.
1. Eenmaal gestart, kunt u op de knop **Alles stoppen** klikken om alle virtuele machines te stoppen.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Uw Vm's stoppen":::

### <a name="stopping-and-resetting-vms"></a>Vm's stoppen en opnieuw instellen

* U kunt afzonderlijke Vm's stoppen door te klikken op de status in-en uitschakelen.
* U kunt ook meerdere Vm's stoppen met behulp van de controles en klikken op de knop stoppen boven aan het scherm.

Als een student problemen ondervindt bij het maken van verbinding met de virtuele machine, of als de VM om een andere reden opnieuw moet worden ingesteld, kunt u de functie reset gebruiken.
1. Als u een of meer Vm's opnieuw wilt instellen, selecteert u deze met behulp van de controles en klikt u op de knop **opnieuw instellen** boven aan de pagina.
1. Klik in het pop-upvenster op **opnieuw instellen**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Uw VM opnieuw instellen":::

    > [!NOTE]
    > Het inschakelen van een student-VM heeft geen invloed op het quotum voor de student. Quota voor gebruikers Hiermee geeft u het aantal Lab-uren op dat voor de gebruiker buiten de geplande tijd beschikbaar moet zijn.

### <a name="connect-to-vms"></a>Verbinding maken met VM's

Docenten kunnen verbinding maken met een student-VM zolang deze is ingeschakeld en de student is niet verbonden met de virtuele machine. Door verbinding te maken met de virtuele machine, kunt u toegang krijgen tot lokale bestanden op de VM en kunnen studenten problemen oplossen.

1. Als u verbinding wilt maken met de student-VM, houdt u de muis aanwijzer op de virtuele machine in de lijst en klikt u op de knop **verbinding maken** . 
1. Volg vervolgens de aan de slag-hand leiding voor studenten voor Chromebooks, Macs of Pc's

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Knop verbinding maken met de VM van student":::

## <a name="manage-users-in-a-lab"></a>Gebruikers beheren in een Lab

Docenten kunnen student gebruikers toevoegen aan een lab en hun uren quota's bewaken. Zie [Lab-gebruikers toevoegen en beheren](how-to-configure-student-usage.md)voor meer informatie over het toevoegen van gebruikers op e-mail adres of het gebruik van een spreadsheet lijst en het registreren van gebruikers.

Nadat u gebruikers hebt uitgenodigd of de koppeling hebt gedeeld, kunt u controleren welke gebruikers zijn geregistreerd op de pagina **gebruikers** in de kolom **status** . 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u in deze Quick Start hebt gemaakt, niet meer gaat gebruiken, verwijdert u de resources.

## <a name="next-steps"></a>Volgende stappen

[Een lab-account maken](tutorial-setup-lab-account.md)
