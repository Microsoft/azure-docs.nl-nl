---
title: Veelgestelde vragen over Windows Virtual Machines in Azure SQL Server | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het uitvoeren van SQL Server op Azure-Vm's.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 014bbe4421bf00f35b2d80505cea288e75f8ca94
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224670"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Veelgestelde vragen over SQL Server op virtuele Azure-machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [SQL Server op Windows Azure virtual machines (vm's)](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Installatie kopieën

1. **Wat SQL Server afbeeldingen van de galerie met virtuele machines zijn beschikbaar?** 

   Azure beheert installatie kopieën van virtuele machines voor alle ondersteunde grote releases van SQL Server op alle edities van Windows en Linux. Zie de volledige lijst met [Windows VM-installatie](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) kopieën en [Linux VM-installatie kopieën](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)voor meer informatie.

1. **Zijn bestaande SQL Server afbeeldingen van de galerie met virtuele machines bijgewerkt?**

   SQL Server installatie kopieën in de galerie met virtuele machines worden elke twee maanden bijgewerkt met de nieuwste updates voor Windows en Linux. Voor Windows-installatie kopieën omvat dit alle updates die zijn gemarkeerd als belang rijk in Windows Update, met inbegrip van belang rijke SQL Server beveiligings updates en-service packs. Voor Linux-installatie kopieën bevat dit de meest recente systeem updates. SQL Server cumulatieve updates worden anders afgehandeld voor Linux en Windows. Voor Linux worden SQL Server cumulatieve updates ook opgenomen in het vernieuwen. Op dit moment worden Windows-Vm's niet bijgewerkt met SQL Server of cumulatieve updates voor Windows Server.

1. **Kunnen SQL Server installatie kopieën van virtuele machines worden verwijderd uit de galerie?**

   Ja. In azure wordt slechts één installatie kopie per primaire versie en editie bewaard. Wanneer bijvoorbeeld een nieuw SQL Server Service Pack wordt uitgebracht, voegt Azure een nieuwe installatie kopie toe aan de galerie voor dat Service Pack. De SQL Server-installatie kopie voor het vorige Service Pack wordt direct verwijderd uit de Azure Portal. Het is echter nog steeds beschikbaar voor het inrichten van Power shell voor de volgende drie maanden. Na drie maanden is de vorige installatie kopie van het Service Pack niet meer beschikbaar. Dit verwijderings beleid is ook van toepassing als een SQL Server-versie niet wordt ondersteund wanneer het einde van de levens cyclus wordt bereikt.


1. **Is het mogelijk om een oudere installatie kopie te implementeren van SQL Server die niet zichtbaar is in de Azure Portal?**

   Ja, met behulp van Power shell. Zie [SQL Server virtuele machines inrichten met Azure PowerShell](create-sql-vm-powershell.md)voor meer informatie over het implementeren van SQL Server-vm's met behulp van Power shell.
   
1. **Is het mogelijk om een gegeneraliseerde Azure Marketplace-SQL Server installatie kopie van mijn SQL Server virtuele machine te maken en deze te gebruiken om Vm's te implementeren?**

   Ja, maar u moet [elke SQL Server VM registreren met de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md) voor het beheren van uw SQL Server virtuele machine in de portal, en het gebruik van functies zoals automatische patching en automatische back-ups. Bij het registreren met de uitbrei ding moet u ook het licentie type opgeven voor elke SQL Server VM.

1. **Hoe kan ik generaliseer SQL Server op Azure VM en gebruiken om nieuwe virtuele machines te implementeren?**

   U kunt een Windows Server-VM implementeren (zonder SQL Server geïnstalleerd) en het [SQL Sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) -proces gebruiken om SQL Server op Azure VM (Windows) met de SQL Server installatie media te generaliseren. Klanten die beschikken over [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) kunnen hun installatiemedia downloaden van het [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klanten die geen Software Assurance hebben, kunnen het installatie medium van een Azure Marketplace gebruiken SQL Server VM-installatie kopie met de gewenste versie.

   U kunt ook een van de SQL Server-installatie kopieën van Azure Marketplace gebruiken om SQL Server op Azure VM te generaliseren. Houd er rekening mee dat u de volgende register sleutel in de bron installatie kopie moet verwijderen voordat u uw eigen installatie kopie maakt. Als u dit niet doet, kan dit ertoe leiden dat de SQL Server Setup Boots trap-map en/of de SQL IaaS-extensie in de status mislukt.

   Pad naar register sleutel:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server op virtuele machines van Azure, met inbegrip van de Vm's die zijn geïmplementeerd vanuit aangepaste gegeneraliseerde installatie kopieën, moeten worden [geregistreerd met de SQL IaaS agent-extensie](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) om te voldoen aan de nalevings vereisten en om optionele functies te gebruiken, zoals automatische patching en automatische back-ups. Met de extensie kunt u ook [het licentie type](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) voor elke SQL Server virtuele machine opgeven.

1. **Kan ik mijn eigen VHD gebruiken om een SQL Server virtuele machine te implementeren?**

   Ja, maar u moet [elke SQL Server VM registreren met de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md) voor het beheren van uw SQL Server virtuele machine in de portal, en het gebruik van functies zoals automatische patching en automatische back-ups.

1. **Is het mogelijk configuraties in te stellen die niet worden weer gegeven in de galerie met virtuele machines (bijvoorbeeld Windows 2008 R2 + SQL Server 2012)?**

   Nee. Voor installatie kopieën in de galerie met virtuele machines die SQL Server bevatten, moet u een van de installatie kopieën selecteren via de Azure Portal of via [Power shell](create-sql-vm-powershell.md). U hebt echter de mogelijkheid om een Windows-VM te implementeren en zelf SQL Server te installeren. U moet [uw SQL Server-VM vervolgens registreren bij de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md) om uw SQL Server-VM in de Azure portal te beheren, evenals functies zoals automatische patches en automatische back-ups. 


## <a name="creation"></a>Maken

1. **Hoe kan ik een virtuele machine van Azure met SQL Server maken?**

   De eenvoudigste methode is het maken van een virtuele machine die SQL Server bevat. Zie [een SQL Server virtuele machine inrichten in de Azure Portal](create-sql-vm-portal.md)voor een zelf studie over het aanmelden voor Azure en het maken van een SQL Server-VM vanuit de portal. U kunt een installatie kopie van een virtuele machine selecteren die gebruikmaakt van betalen per seconde SQL Server-licentie verlening, of u kunt een installatie kopie gebruiken waarmee u uw eigen SQL Server-licentie meebrengt. U hebt ook de mogelijkheid om SQL Server hand matig te installeren op een virtuele machine met een gratis licentie (ontwikkelaar of Express) of door een on-premises licentie opnieuw te gebruiken. Zorg ervoor dat u [uw SQL Server-VM registreert met de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md) voor het beheren van uw SQL Server virtuele machine in de portal, en gebruik functies zoals automatische patching en automatische back-ups. Als u uw eigen licentie meebrengt, moet u beschikken over [License Mobility via Software Assurance op Azure](https://azure.microsoft.com/pricing/license-mobility/). Zie [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Prijsrichtlijnen voor SQL Server Azure VM's) voor meer informatie.

1. **Hoe kan ik mijn on-premises SQL Server Data Base naar de Cloud migreren?**

   Maak eerst een virtuele Azure-machine met een SQL Server-exemplaar. Migreer vervolgens uw on-premises data bases naar dat exemplaar. Zie [een SQL Server-Data Base migreren naar SQL Server in een Azure-VM](migrate-to-vm-from-sql-server.md)voor strategieën voor gegevens migratie.

## <a name="licensing"></a>Licentieverlening

1. **Hoe kan ik mijn gelicentieerd exemplaar van SQL Server installeren op een Azure-VM?**

   Er zijn drie manieren om dit te doen. Als u een klant van Enterprise Overeenkomst (EA) bent, kunt u een van de [installatie kopieën van virtuele machines inrichten die ondersteuning bieden voor licenties](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), ook wel bekend als uw eigen licentie (BYOL). Als u [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)hebt, kunt u de [Azure Hybrid Benefit](licensing-model-azure-hybrid-benefit-ahb-change.md) inschakelen op een bestaande payg-installatie kopie (betalen per gebruik). Of u kunt de SQL Server-installatiemedia kopiëren naar een Windows Server-VM, en vervolgens SQL Server op de VM installeren. Zorg ervoor dat u uw SQL Server-VM registreert met de [uitbrei ding](sql-agent-extension-manually-register-single-vm.md) voor functies zoals portal beheer, geautomatiseerde back-up en automatische patching. 


1. **Heeft een klant SQL Server licenties voor client toegang nodig om verbinding te maken met een SQL Server-abonnement op basis van betalen per gebruik, dat wordt uitgevoerd op Azure Virtual Machines?**

   Nee. Klanten hebben Cal's nodig wanneer ze gebruikmaken van uw eigen licentie en hun SQL Server SA-Server/CAL-VM verplaatsen naar Azure-Vm's. 

1. **Kan ik een VM wijzigen zodat mijn eigen SQL Server-licentie wordt gebruikt, wanneer de VM is gemaakt vanuit een van de Betalen per gebruik-installatiekopieën uit de galerie?**

   Ja. U kunt eenvoudig een PAYG-galerie (betalen naar gebruik) overschakelen naar uw eigen licentie (BYOL) door de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)in te scha kelen.  Raadpleeg [Het licentiemodel voor een SQL Server-VM wijzigen](licensing-model-azure-hybrid-benefit-ahb-change.md) voor meer informatie. Deze faciliteit is momenteel alleen beschikbaar voor open bare en Azure Government Cloud-klanten.


1. **Treedt er downtime op voor SQL Server tijdens het schakelen tussen licentiemodellen?**

   Nee. [Het wijzigen van het licentie model](licensing-model-azure-hybrid-benefit-ahb-change.md) vereist geen uitval tijd voor SQL Server, omdat de wijziging onmiddellijk van kracht is en het opnieuw opstarten van de virtuele machine niet vereist is. Als u uw SQL Server-VM echter wilt registreren met de SQL IaaS agent-extensie, is de [SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) -uitbrei ding een vereiste en installeert u de SQL IaaS-extensie in de _volledige_ modus, wordt de SQL Server-service opnieuw gestart. Als de SQL IaaS-extensie moet worden geïnstalleerd, installeert u deze in de modus _Lightweight_ voor beperkte functionaliteit of installeert u deze in de _volledige_ modus tijdens een onderhouds venster. De SQL IaaS-uitbrei ding die is geïnstalleerd in de _licht_ modus kan op elk gewenst moment worden bijgewerkt naar de _volledige_ modus, maar vereist dat de SQL Server-service opnieuw wordt gestart. 
   
1. **Is het mogelijk om licentie modellen te scha kelen op een SQL Server VM die is geïmplementeerd met het klassieke model?**

   Nee. Het wijzigen van licentie modellen wordt niet ondersteund op een klassieke virtuele machine. U kunt uw VM migreren naar het Azure Resource Manager model en registreren bij de SQL IaaS agent-extensie. Zodra de VM is geregistreerd met de SQL IaaS agent-extensie, zijn wijzigingen in het licentie model beschikbaar op de virtuele machine.

1. **Kan ik de Azure-portal gebruiken om meerdere exemplaren op dezelfde VM te beheren?**

   Nee. Portal beheer is een functie die wordt ondersteund door de SQL IaaS agent-extensie, die afhankelijk is van de uitbrei ding van de SQL Server IaaS-agent. Als zodanig gelden dezelfde beperkingen voor de uitbrei ding als voor de uitbrei ding. In de portal kan slechts één standaardexemplaar of één benoemd exemplaar worden beheerd, zolang het op de juiste wijze is geconfigureerd. Zie [SQL Server IaaS agent extension](sql-server-iaas-agent-extension-automate-management.md)(Engelstalig) voor meer informatie over deze beperkingen. 

1. **Kan Azure Hybrid Benefit worden geactiveerd met een CSP-abonnement?**

   Ja, Azure Hybrid Benefit is beschikbaar voor CSP-abonnementen. CSP-klanten moeten eerst een betalen per gebruik-installatie kopie implementeren en vervolgens [het licentie model wijzigen](licensing-model-azure-hybrid-benefit-ahb-change.md) in uw eigen licentie.
   
 
1. **Moet ik betalen om een SQL-server een licentie te verlenen op een Azure-VM, als deze SQL-server alleen wordt gebruikt als stand-by of om een failover uit te voeren?**

   Als u een gratis passieve licentie wilt voor een secundaire beschikbaarheids groep met een stand-by-of een failover-cluster, moet u voldoen aan de volgende criteria, zoals beschreven in de [product licentie voorwaarden](https://www.microsoft.com/licensing/product-licensing/products):

   1. U hebt [licentie mobiliteit](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) via [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Het passieve SQL Server-exemplaar levert geen SQL Server-gegevens aan clients en voert geen actieve SQL Server-workloads uit. Dit exemplaar wordt alleen gebruikt om te synchroniseren met de primaire server, en behoudt de passieve database verder in een warme stand-by. Als het gaat om gegevens, zoals rapporten naar clients waarop actieve SQL Server werk belastingen worden uitgevoerd, of het uitvoeren van andere werk items dan is opgegeven in de product voorwaarden, moet het een betaalde gelicentieerde SQL Server instantie zijn. De volgende activiteiten zijn toegestaan op het secundaire exemplaar: consistentiecontroles voor databases of CheckDB, volledige back-ups, back-ups van transactielogboeken, en het bewaken van gegevens over resourcegebruik. U kunt het primaire exemplaar en het bijbehorende exemplaar voor herstel na een noodgeval ook één keer in de 90 dagen gedurende korte tijd tegelijk uitvoeren, om herstel na noodgeval te testen. 
   1. De actieve SQL Server-licentie wordt gedekt door Software Assurance en biedt **één** passieve secundaire SQL Server instantie, met Maxi maal dezelfde hoeveelheid reken kracht als de gelicentieerde actieve server. 
   1. De secundaire SQL Server VM maakt gebruik van de licentie voor [herstel na nood gevallen](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) in de Azure Portal.
   
1. **Wat wordt beschouwd als een passief exemplaar?**

   Het passieve SQL Server-exemplaar levert geen SQL Server-gegevens aan clients en voert geen actieve SQL Server-workloads uit. Dit exemplaar wordt alleen gebruikt om te synchroniseren met de primaire server, en behoudt de passieve database verder in een warme stand-by. Als een exemplaar gegevens, zoals rapporten, levert aan clients waarop actieve SQL Server-workloads worden uitgevoerd, of als het andere werkzaamheden uitvoert dan de werkzaamheden die zijn opgegeven in de productvoorwaarden, moet het een betaald gelicentieerd SQL Server-exemplaar zijn. De volgende activiteiten zijn toegestaan op het secundaire exemplaar: consistentiecontroles voor databases of CheckDB, volledige back-ups, back-ups van transactielogboeken, en het bewaken van gegevens over resourcegebruik. U kunt het primaire exemplaar en het bijbehorende exemplaar voor herstel na een noodgeval ook één keer in de 90 dagen gedurende korte tijd tegelijk uitvoeren, om herstel na noodgeval te testen.
   

1. **Welke scenario's kunnen gebruikmaken van Disaster Recovery Benefit ?**

   De [licentie handleiding](https://aka.ms/sql2019licenseguide) bevat scenario's waarin het voor deel van herstel na nood gevallen kan worden gebruikt. Raadpleeg de productvoorwaarden en neem contact op met uw licentiecontactpersoon of accountmanager voor meer informatie.

1. **Welke abonnementen bieden ondersteuning voor Disaster Recovery Benefit?**

   Uitgebreide programma's die abonnementsrechten bieden die vergelijkbaar zijn met die van Software Assurance als een vast voordeel, bieden ondersteuning voor Disaster Recovery Benefit. Dit is inclusief, maar is niet beperkt tot, de Open Value (OV), Open Value Subscription (OVS), Enterprise Overeenkomst (EA), Enterprise Overeenkomst-abonnement (EAS) en de server-en Cloud registratie (SCE). Raadpleeg de [product voorwaarden](https://www.microsoft.com/licensing/product-licensing/products) en neem contact op met uw licentie contactpersonen of account manager voor meer informatie. 

   
 ## <a name="extension"></a>Toestelnummer

1. **Gaat mijn VM registreren met de nieuwe SQL IaaS agent-extensie worden extra kosten in rekening gebracht?**

   Nee. De SQL IaaS agent-extensie biedt alleen extra beheer baarheid voor SQL Server op Azure VM zonder extra kosten. 

1. **Is de SQL IaaS agent-extensie voor alle klanten beschikbaar?**
 
   Ja, zolang de SQL Server virtuele machine is geïmplementeerd in de open bare Cloud met behulp van het Resource Manager-model en niet het klassieke model. Alle andere klanten kunnen zich registreren met de nieuwe SQL IaaS agent-extensie. Maar alleen klanten met het voor deel van [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) kunnen hun eigen licentie gebruiken door de [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) te activeren op een SQL Server VM. 

1. **Wat gebeurt er met de extensie (_micro soft. SqlVirtualMachine_) als de VM-resource wordt verplaatst of verwijderd?** 

   Wanneer de micro soft. Compute/VirtualMachine-resource wordt verwijderd of verplaatst, wordt de bijbehorende micro soft. SqlVirtualMachine-resource op de hoogte gesteld om de bewerking asynchroon te repliceren.

1. **Wat gebeurt er met de virtuele machine als de extensie (_micro soft. SqlVirtualMachine_) wordt verwijderd?**

    De resource micro soft. Compute/VirtualMachine wordt niet beïnvloed wanneer de micro soft. SqlVirtualMachine-resource wordt verwijderd. De licentie wijzigingen worden echter standaard teruggezet naar de oorspronkelijke installatie kopie bron. 

1. **Is het mogelijk om zelf geïmplementeerde SQL Server Vm's te registreren met de SQL IaaS agent-extensie?**

    Ja. Als u SQL Server vanaf uw eigen media hebt geïmplementeerd en de SQL IaaS-extensie hebt geïnstalleerd, kunt u uw SQL Server VM registreren met de extensie om de beheer baarheid voor delen van de SQL IaaS-uitbrei ding te verkrijgen.    


## <a name="administration"></a>Beheer

1. **Kan ik een tweede exemplaar van SQL Server op dezelfde VM installeren? Kan ik de geïnstalleerde functies van het standaard exemplaar wijzigen?**

   Ja. De SQL Server-installatie media bevindt zich in een map op station **C** . Voer **Setup.exe** uit vanaf die locatie om nieuwe SQL Server instanties toe te voegen of om andere geïnstalleerde functies van SQL Server op de computer te wijzigen. Houd er rekening mee dat sommige functies, zoals geautomatiseerde back-ups, automatische patching en Azure Key Vault integratie, alleen kunnen worden uitgevoerd op het standaard exemplaar of een benoemd exemplaar dat correct is geconfigureerd (zie vraag 3). Klanten die [Software Assurance gebruiken via het Azure Hybrid Benefit](licensing-model-azure-hybrid-benefit-ahb-change.md) of het licentie model voor **betalen per gebruik** kunnen meerdere exemplaren van SQL Server op de virtuele machine installeren zonder extra licentie kosten te betalen. Aanvullende SQL Server-exemplaren kunnen systeem bronnen beperken, tenzij correct geconfigureerd. 

1. **Wat is het maximum aantal exemplaren op een VM?**
   SQL Server 2012 tot SQL Server 2019 kan [50 instanties](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) op een zelfstandige server ondersteunen. Dit is dezelfde limiet, ongeacht in azure on-premises. Zie [Aanbevolen procedures](performance-guidelines-best-practices.md#multiple-instances) voor meer informatie over hoe u uw omgeving beter kunt voorbereiden. 

1. **Kan ik het standaardexemplaar van SQL Server verwijderen?**

   Ja, maar er is een aantal overwegingen. SQL Server-gerelateerde facturering kan eerst worden uitgevoerd, afhankelijk van het licentie model voor de virtuele machine. Ten tweede, zoals vermeld in het vorige antwoord, zijn er functies die afhankelijk zijn van de [SQL Server IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md). Als u het standaard exemplaar verwijdert zonder de IaaS-extensie te verwijderen, blijft de uitbrei ding zoeken naar het standaard exemplaar en kunnen fouten in het gebeurtenis logboek worden gegenereerd. Deze fouten zijn afkomstig uit de volgende twee bronnen: **Microsoft SQL Server referentie beheer** en **Microsoft SQL Server IaaS-agent**. Een van de fouten lijkt mogelijk op de volgende:

      Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk.

   Als u besluit om het standaard exemplaar te verwijderen, moet u ook de [uitbrei ding voor de SQL Server IaaS-agent](sql-server-iaas-agent-extension-automate-management.md) verwijderen. 

1. **Kan ik een benoemd exemplaar van SQL Server met de extensie IaaS gebruiken?**
   
   Ja, als het benoemde exemplaar het enige exemplaar op het SQL Server is, en als het oorspronkelijke standaard exemplaar [correct is verwijderd](sql-server-iaas-agent-extension-automate-management.md#named-instance-support). Als er geen standaard exemplaar is en er meerdere benoemde exemplaren op één SQL Server virtuele machine staan, kan de uitbrei ding van de SQL Server IaaS-agent niet worden geïnstalleerd.  

1. **Kan ik SQL Server en de bijbehorende licentie factuur van een SQL Server VM verwijderen?**

   Ja, maar u moet extra stappen ondernemen om te voor komen dat uw SQL Server-exemplaar in rekening wordt gebracht, zoals beschreven in [prijs informatie](pricing-guidance.md). Als u het SQL Server exemplaar volledig wilt verwijderen, kunt u migreren naar een andere Azure-VM zonder SQL Server vooraf geïnstalleerd op de virtuele machine en de huidige SQL Server virtuele machine te verwijderen. Voer de volgende stappen uit als u de virtuele machine wilt blijven gebruiken, maar SQL Server facturering wilt stoppen: 

   1. Maak, indien nodig, back-ups van al uw gegevens, inclusief systeem databases. 
   1. SQL Server volledig verwijderen, met inbegrip van de SQL IaaS-extensie (indien aanwezig).
   1. Installeer de gratis [SQL Express-editie](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Meld u aan bij de SQL IaaS agent-extensie in de [Lightweight-modus](sql-agent-extension-manually-register-single-vm.md).
   1. Beschrijving Schakel de Service Express SQL Server uit door het starten van de service uit te scha kelen. 

1. **Kan ik de Azure-portal gebruiken om meerdere exemplaren op dezelfde VM te beheren?**

   Nee. Portal beheer wordt verzorgd door de SQL IaaS agent-extensie, die afhankelijk is van de uitbrei ding van de SQL Server IaaS-agent. Als zodanig gelden dezelfde beperkingen als voor de uitbrei ding. De portal kan slechts één standaard exemplaar of één benoemd exemplaar beheren, zolang de configuratie op de juiste wijze is geconfigureerd. Zie [SQL Server IaaS agent extension](sql-server-iaas-agent-extension-automate-management.md) (Engelstalig) voor meer informatie. 


## <a name="updating-and-patching"></a>Updates en patches

1. **Hoe kan ik overschakelen naar een andere versie of editie van SQL Server in een Azure VM?**

   Klanten kunnen hun versie of editie van SQL Server wijzigen door gebruik te maken van een installatiemedium met de gewenste versie of editie van SQL Server. Zodra de versie of editie is gewijzigd, gebruikt u de Azure-portal om de versie- of editie-eigenschap van de VM te wijzigen zodat deze nauwkeurig de facturering voor de VM aangeeft. Zie [Change Edition of a SQL Server VM](change-sql-server-edition.md)(Engelstalig) voor meer informatie. Er is geen factureringsverschil voor de verschillende versies van SQL Server, dus wanneer de versie van SQL Server is gewijzigd, is er geen verdere actie nodig.

1. **Waar kan ik de installatiemedia downloaden om de editie of versie van SQL Server te wijzigen?**

   Klanten die beschikken over [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kunnen hun installatiemedia downloaden van het [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klanten die geen Software Assurance hebben, kunnen het installatie medium van een Azure Marketplace gebruiken SQL Server VM-installatie kopie met de gewenste versie.
   
1. **Hoe worden updates en servicepacks toegepast op een VM met SQL Server?**

   virtuele machines geven u controle over de hostmachine, inclusief wanneer en hoe u updates wilt toepassen. U kunt handmatig de Windows-updates toepassen voor het besturingssysteem of u kunt de planningsservice [Automated Patching](automated-patching.md) inschakelen. Automated Patching installeert alle updates die als belangrijk zijn aangeduid, met inbegrip van SQL Server-updates in deze categorie. Overige optionele updates voor SQL Server moeten handmatig worden geïnstalleerd.

1. **Kan ik een upgrade uitvoeren van mijn SQL Server 2008/2008 R2-exemplaar na het registreren van de SQL IaaS agent-extensie?**

   Als het besturings systeem Windows Server 2008 R2 of later is, ja. U kunt elk installatie medium gebruiken om de versie en editie van SQL Server te upgraden, en vervolgens kunt u uw [SQL IaaS-uitbreidings modus](sql-server-iaas-agent-extension-automate-management.md#management-modes)upgraden van _geen enkele agent_ naar een _volle_. Op die manier krijgt u toegang tot alle voor delen van de SQL IaaS-extensie, zoals beheer baarheid van de portal, automatische back-ups en automatische patching. Als de versie van het besturings systeem Windows Server 2008 is, wordt alleen de modus zonder agent ondersteund. 

1. **Hoe krijg ik gratis uitgebreide beveiligingsupdates voor mijn end-of-support exemplaren van SQL Server 2008 en SQL Server 2008 R2?**

   U kunt [gratis uitgebreide beveiligings updates](sql-server-2008-extend-end-of-support.md) verkrijgen door uw SQL Server naar een virtuele machine van Azure te verplaatsen. Zie [de opties voor end-of-support](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) voor meer informatie. 
  
   

## <a name="general"></a>Algemeen

1. **Worden SQL Server-FCI (failover cluster instances) ondersteund op virtuele machines van Azure?**

   Ja. U kunt een failover-cluster exemplaar installeren met behulp van [Premium-bestands shares (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) of [opslag ruimten direct (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) voor het opslag subsysteem. Premium-bestands shares bieden IOPS en doorvoer capaciteit die voldoet aan de behoeften van veel werk belastingen. Voor i/o-intensieve workloads kunt u opslag ruimten direct gebruiken op basis van beheerd Premium of Ultra-disks. U kunt ook clustering-of opslag oplossingen van derden gebruiken, zoals beschreven in [hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure virtual machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Op dit moment wordt de _volledige_ [SQL Server IaaS-agent extensie](sql-server-iaas-agent-extension-automate-management.md) niet ondersteund voor SQL Server FCI op Azure. U wordt aangeraden de _volledige_ uitbrei ding van vm's die deel uitmaken van de FCI te verwijderen en de uitbrei ding in de _licht_ modus te installeren. Deze uitbrei ding ondersteunt functies, zoals automatische back-ups en patches en bepaalde portal functies voor SQL Server. Deze functies werken niet voor SQL Server Vm's nadat de _volledige_ agent is verwijderd.

1. **Wat is het verschil tussen SQL Server Vm's en de SQL Database-Service?**

   In het algemeen is het uitvoeren van SQL Server op een virtuele machine van Azure niet hetzelfde als het uitvoeren van SQL Server in een extern Data Center. [Azure SQL database](../../database/sql-database-paas-overview.md) biedt daarentegen data base-as-a-service. Met SQL Database hebt u geen toegang tot de computers die als host fungeren voor uw data bases. Zie voor een volledige vergelijking [kiezen een cloud SQL Server optie: Azure SQL (PaaS) data base of SQL Server op Azure vm's (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Hoe kan ik SQL data-hulpprogram ma's installeren op mijn Azure VM?**

    Down load en installeer de hulpprogram ma's voor SQL-gegevens van [Microsoft SQL Server Data tools-Business Intelligence voor Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Worden gedistribueerde trans acties met MSDTC ondersteund op SQL Server Vm's?**
   
    Ja. Lokale DTC wordt ondersteund voor SQL Server 2016 SP2 en hoger. Toepassingen moeten echter worden getest wanneer er gebruik wordt gemaakt van AlwaysOn-beschikbaarheids groepen, omdat trans acties in de vlucht tijdens een failover mislukken en opnieuw moeten worden geprobeerd. Geclusterde DTC is beschikbaar vanaf Windows Server 2019. 
    
1. **Worden klant gegevens buiten de regio verplaatst of opgeslagen door Azure SQL-machines?**

   Nee. De virtuele machine van Azure SQL en de SQL IaaS agent-extensie bevatten eigenlijk geen klant gegevens.

## <a name="sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent-extensie

1. **Moet ik mijn SQL Server VM die is ingericht van een SQL Server-installatie kopie registreren in azure Marketplace?**

   Nee. Micro soft registreert automatisch Vm's die zijn ingericht op de SQL Server installatie kopieën in azure Marketplace. Registratie met de extensie is alleen vereist als de virtuele machine *niet* is ingericht op de SQL Server installatie kopieën in azure Marketplace en SQL Server zelf is geïnstalleerd.

1. **Is de SQL IaaS agent-extensie voor alle klanten beschikbaar?** 

   Ja. Klanten moeten hun SQL Server Vm's registreren met de uitbrei ding als ze geen SQL Server installatie kopie van Azure Marketplace gebruiken en in plaats daarvan zelf een geïnstalleerd SQL Server of als ze hun aangepaste VHD hebben. Vm's die eigendom zijn van alle typen abonnementen (direct, Enterprise Overeenkomst en Cloud Solution Provider) kunnen worden geregistreerd met de SQL IaaS agent-extensie.

1. **Wat is de standaard beheer modus bij het registreren met de SQL IaaS agent-extensie?**

   De standaard beheer modus wanneer u zich registreert met de SQL IaaS agent-extensie is *licht gewicht*. Als de eigenschap SQL Server beheer niet is ingesteld als u zich registreert met de uitbrei ding, wordt de modus ingesteld als licht gewicht en wordt de SQL Server-service niet opnieuw opgestart. U kunt het beste eerst registreren bij de SQL IaaS agent-extensie in de Lightweight-modus en vervolgens een upgrade uitvoeren naar Full tijdens een onderhouds venster. Het standaard beheer is ook lichtgewicht tijd wanneer u de [functie voor automatische registratie](sql-agent-extension-automatic-registration-all-vms.md)gebruikt.

1. **Wat zijn de vereisten om u te registreren bij de SQL IaaS agent-extensie?**

   Er zijn geen vereisten om te registreren bij de SQL IaaS agent-extensie, anders dan wanneer SQL Server op de virtuele machine is geïnstalleerd. Als de uitbrei ding van de SQL IaaS-agent is geïnstalleerd in de volledige modus, wordt de SQL Server-service opnieuw gestart, dus als u een onderhouds venster wordt aanbevolen, wordt dit gedaan.

1. **Registreert u bij de SQL IaaS agent-extensie een agent op mijn VM installeren?**

   Ja, registreren met de SQL IaaS agent-extensie in de volledige beheer modus installeert een agent op de virtuele machine. Registratie in Lightweight of de modus voor niet-agent is niet vereist. 

   Als u zich registreert met de SQL IaaS agent-extensie in de Lightweight-modus, worden alleen de *binaire bestanden* van de SQL IaaS-agent uitbreiding naar de virtuele machine gekopieerd. de agent wordt niet geïnstalleerd. Deze binaire bestanden worden vervolgens gebruikt om de agent te installeren wanneer de beheer modus wordt bijgewerkt naar Full.


1. **Wordt de registratie van de SQL IaaS agent-extensie opnieuw gestart SQL Server op mijn VM?**

   Dit is afhankelijk van de modus die tijdens de registratie is opgegeven. Als de modus licht gewicht of niet-agent is opgegeven, wordt de SQL Server-service niet opnieuw opgestart. Als u echter de beheer modus volledig opgeeft, wordt de SQL Server-service opnieuw gestart. De functie voor automatische registratie registreert uw SQL Server Vm's in de Lightweight-modus, tenzij de Windows Server-versie 2008 is, in dat geval de SQL Server virtuele machine wordt geregistreerd in de modus niet-agent. 

1. **Wat is het verschil tussen de Lightweight en agent beheer modi bij het registreren met de SQL IaaS agent-extensie?** 

   Beheer modus voor niet-agent is de enige beschik bare beheer modus voor SQL Server 2008 en SQL Server 2008 R2 op Windows Server 2008. Voor alle latere versies van Windows Server zijn de twee beschik bare beheer baarheids modi licht gewicht en volledig. 

   Voor de modus voor niet-agents zijn SQL Server versie-en editie-eigenschappen vereist om door de klant te worden ingesteld. In de Lightweight-modus wordt een query uitgevoerd op de VM om de versie en editie van het SQL Server-exemplaar te vinden.

1. **Kan ik bij de SQL IaaS agent-extensie registreren zonder het SQL Server licentie type op te geven?**

   Nee. Het SQL Server licentie type is geen optionele eigenschap wanneer u zich registreert met de SQL IaaS agent-extensie. U moet het SQL Server licentie type instellen op betalen naar gebruik of Azure Hybrid Benefit wanneer u zich registreert met de SQL IaaS agent-extensie in alle beheer modi (geen agent, licht gewicht en volledig). Als u een van de gratis versies van SQL Server hebt geïnstalleerd, zoals Developer of Evaluation Edition, moet u zich registreren bij licenties voor betalen naar gebruik. Azure Hybrid Benefit is alleen beschikbaar voor betaalde versies van SQL Server zoals Enter prise-en Standard-edities.

1. **Kan ik een upgrade uitvoeren van de SQL Server IaaS-uitbrei ding van de modus voor de agent naar de volledige modus?**

   Nee. Het bijwerken van de beheer baarheids modus naar volledig of licht is niet beschikbaar voor de modus niet-agent. Dit is een technische beperking van Windows Server 2008. U moet eerst een upgrade uitvoeren van het besturings systeem naar Windows Server 2008 R2 of hoger, en vervolgens kunt u een upgrade uitvoeren naar de volledige beheer modus. 

1. **Kan ik de SQL Server IaaS-uitbrei ding van de Lightweight-modus naar de volledige modus bijwerken?**

   Ja. Het bijwerken van de beheer baarheids modus van Lightweight naar Full wordt ondersteund via Azure PowerShell of de Azure Portal. Hiermee wordt de SQL Server-service opnieuw gestart.

1. **Kan ik de IaaS-uitbrei ding van de SQL Server verlagen van de volledige modus naar geen enkele agent of Lightweight-beheer modus?**

   Nee. Het downgradeen van de SQL Server IaaS-extensie modus wordt niet ondersteund. De beheer baarheids modus kan niet worden gedowngraded van de volledige modus naar de modus licht gewicht of de niet-agent en kan niet worden gedowngraded van de modus Lightweight modus naar geen agent. 

   Als u de beheer baarheids modus wilt wijzigen van de volledige beheer baarheid, verwijdert u de [registratie](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) van de SQL Server VM uit de uitbrei ding SQL IaaS agent door de _resource_ van de virtuele SQL-machine te verwijderen en de SQL Server VM opnieuw te registreren met de SQL IaaS agent-extensie opnieuw in een andere beheer modus.

1. **Kan ik registreren bij de SQL IaaS agent-extensie vanuit het Azure Portal?**

   Nee. Registratie met de SQL IaaS agent-extensie is niet beschikbaar in de Azure Portal. Registratie met de SQL IaaS agent-extensie wordt alleen ondersteund met Azure CLI of Azure PowerShell. 

1. **Kan ik een VM registreren met de SQL IaaS agent-extensie voordat SQL Server is geïnstalleerd?**

   Nee. Een virtuele machine moet ten minste één SQL Server-exemplaar (data base-engine) hebben om u te kunnen registreren bij de SQL IaaS agent-extensie. Als er geen SQL Server-exemplaar op de VM aanwezig is, heeft de nieuwe resource micro soft. SqlVirtualMachine de status mislukt.

1. **Kan ik een virtuele machine met de SQL IaaS agent-extensie registreren als er meerdere exemplaren van SQL Server zijn?**

   Ja, op voor waarde dat er een standaard exemplaar is op de VM. Met de SQL IaaS agent-extensie wordt slechts één exemplaar van de SQL Server (data base-engine) geregistreerd. De SQL IaaS agent-extensie registreert de standaard SQL Server-instantie in het geval van meerdere exemplaren.

1. **Kan ik een SQL Server-failovercluster registreren met de SQL IaaS agent-extensie?**

   Ja. SQL Server failover-cluster exemplaren op een virtuele machine van Azure kunnen worden geregistreerd met de SQL IaaS agent-extensie in de Lightweight-modus. SQL Server failover-cluster exemplaren kunnen echter niet worden bijgewerkt naar de volledige beheer modus.

1. **Kan ik mijn VM registreren met de SQL IaaS agent-extensie als er een AlwaysOn-beschikbaarheids groep is geconfigureerd?**

   Ja. Er zijn geen beperkingen voor het registreren van een SQL Server exemplaar op een Azure-VM met de SQL IaaS agent-extensie als u deelneemt aan de configuratie van een AlwaysOn-beschikbaarheids groep.

1. **Wat zijn de kosten voor registratie bij de SQL IaaS agent-extensie of met een upgrade naar de volledige beheer baarheids modus?**

   Geen. Er zijn geen kosten verbonden aan het registreren met de SQL IaaS agent-extensie of met behulp van een van de drie beheer modi. Het beheer van uw SQL Server-VM met de extensie is volledig gratis. 

1. **Wat is de invloed van de prestaties van het gebruik van de verschillende beheer modi?**

   Er is geen invloed op het gebruik van de modi geen *agent* en *licht gewicht* beheer. Het gebruik van de *volledige* beheer baarheids modus van twee services die op het besturings systeem zijn geïnstalleerd, heeft een minimale invloed. Deze kunnen worden bewaakt via taak beheer en worden weer gegeven in de ingebouwde Windows Services-console. 

   De twee service namen zijn:
   - `SqlIaaSExtensionQuery` (Weergave naam- `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Weergave naam- `Microsoft SQL Server IaaS Agent` )

1. **Wilt u de uitbrei ding Hoe kan ik verwijderen?**

   Verwijder de uitbrei ding door de registratie van de SQL Server VM [ongedaan te maken](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) op basis van de SQL IaaS agent-extensie. 

## <a name="resources"></a>Resources

**Windows-vm's**:

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server inrichten op een Windows-VM](create-sql-vm-portal.md)
* [Een Data Base migreren naar SQL Server op een virtuele Azure-machine](migrate-to-vm-from-sql-server.md)
* [Hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Aanbevolen procedures voor het uitvoeren van SQL Server op Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Toepassings patronen en ontwikkelings strategieën voor het SQL Server op Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuele Linux-machines**:

* [Overzicht van SQL Server op een Linux-VM](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server inrichten op een Linux-VM](../linux/sql-vm-create-portal-quickstart.md)
* [Veelgestelde vragen (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentatie voor SQL Server op Linux](/sql/linux/sql-server-linux-overview)
