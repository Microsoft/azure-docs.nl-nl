---
title: SQL Server over Windows Virtual Machines in Veelgestelde vragen over Azure | Microsoft Docs
description: Dit artikel bevat antwoorden op veelgestelde vragen over het uitvoeren van SQL Server azure-VM's.
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
ms.openlocfilehash: 02bb1f539369cf72a5d5b6503a3584069589b19e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727344"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Veelgestelde vragen over problemen SQL Server azure-VM's
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Dit artikel bevat antwoorden op enkele van de meest voorkomende vragen over het uitvoeren [van SQL Server in Windows Azure Virtual Machines (VM's).](https://azure.microsoft.com/services/virtual-machines/sql-server/)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Afbeeldingen

1. **Welke SQL Server galerie met virtuele machines zijn beschikbaar?** 

   Azure onderhoudt virtuele-machine-afbeeldingen voor alle ondersteunde belangrijke releases van SQL Server op alle edities voor Zowel Windows als Linux. Zie de volledige lijst met windows-VM-afbeeldingen en [Linux-VM-afbeeldingen](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) [voor meer informatie.](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)

1. **Worden bestaande SQL Server galerie met virtuele machines bijgewerkt?**

   Om de twee maanden worden SQL Server in de galerie met virtuele machines bijgewerkt met de nieuwste Windows- en Linux-updates. Voor Windows-afbeeldingen omvat dit alle updates die als belangrijk zijn gemarkeerd in Windows Update, waaronder belangrijke SQL Server beveiligingsupdates en servicepacks. Voor Linux-installatie afbeeldingen omvat dit de nieuwste systeemupdates. SQL Server cumulatieve updates worden anders verwerkt voor Linux en Windows. Voor Linux zijn SQL Server updates ook opgenomen in de vernieuwing. Maar op dit moment worden Windows-VM's niet bijgewerkt met cumulatieve updates SQL Server Windows Server.

1. **Kunnen SQL Server van virtuele machines worden verwijderd uit de galerie?**

   Ja. Azure onderhoudt slechts één afbeelding per hoofdversie en editie. Wanneer er bijvoorbeeld een nieuw SQL Server servicepack wordt uitgebracht, voegt Azure een nieuwe afbeelding toe aan de galerie voor dat servicepack. De SQL Server voor het vorige servicepack wordt onmiddellijk verwijderd uit de Azure Portal. Het is echter nog steeds beschikbaar voor het inrichten vanuit PowerShell voor de komende drie maanden. Na drie maanden is de vorige servicepack-afbeelding niet meer beschikbaar. Dit verwijderingsbeleid is ook van toepassing als een SQL Server niet meer wordt ondersteund wanneer het einde van de levenscyclus bereikt.


1. **Is het mogelijk om een oudere afbeelding van een SQL Server te implementeren die niet zichtbaar is in de Azure Portal?**

   Ja, met behulp van PowerShell. Zie How to provision SQL Server virtual machines with [Azure PowerShell](create-sql-vm-powershell.md)(Virtuele machines inrichten met powershell) voor meer informatie over het implementeren van virtuele machines SQL Server PowerShell.
   
1. **Is het mogelijk om een gealeraliseerde Azure Marketplace SQL Server van mijn virtuele SQL Server te maken en deze te gebruiken om VM's te implementeren?**

   Ja, maar u moet vervolgens elke [SQL Server-VM](sql-agent-extension-manually-register-single-vm.md) registreren met de SQL IaaS Agent-extensie om uw SQL Server-VM te beheren in de portal, en functies zoals automatische patching en automatische back-ups gebruiken. Wanneer u zich bij de extensie registreert, moet u ook het licentietype voor elke virtuele SQL Server opgeven.

1. **Hoe kan ik uw virtuele SQL Server azure-VM generaliseren en gebruiken om nieuwe VM's te implementeren?**

   U kunt een Windows Server-VM implementeren (zonder SQL Server geïnstalleerd) en het [SQL sysprep-proces](/sql/database-engine/install-windows/install-sql-server-using-sysprep) gebruiken om SQL Server op Azure VM (Windows) te generaliseren met de SQL Server-installatiemedia. Klanten die beschikken over [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) kunnen hun installatiemedia downloaden van het [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klanten die geen Software Assurance kunnen de installatiemedia gebruiken van een VM-installatie Azure Marketplace SQL Server met de gewenste editie.

   U kunt ook een van de SQL Server van de Azure Marketplace gebruiken om SQL Server virtuele Azure-VM te generaliseren. Houd er rekening mee dat u de volgende registersleutel in de bronafbeelding moet verwijderen voordat u uw eigen afbeelding maakt. Als u dit niet doet, kan dit leiden tot het mislukken van de SQL Server en/of SQL IaaS-extensie met de status Mislukt.

   Pad naar registersleutel:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server op Azure-VM's, inclusief de VM's die zijn geïmplementeerd op basis van aangepaste gealeraliseerde installatiekopielen, moeten worden geregistreerd bij de [SQL IaaS Agent-extensie](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) om te voldoen aan de nalevingsvereisten en om optionele functies te gebruiken, zoals automatische patching en automatische back-ups. Met de extensie kunt u ook [het licentietype voor](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) elke virtuele SQL Server opgeven.

1. **Kan ik mijn eigen VHD gebruiken om een virtuele SQL Server implementeren?**

   Ja, maar u moet vervolgens elke [SQL Server-VM](sql-agent-extension-manually-register-single-vm.md) registreren bij de SQL IaaS Agent-extensie om uw SQL Server-VM in de portal te beheren, en functies zoals automatische patching en automatische back-ups gebruiken.

1. **Is het mogelijk om configuraties in te stellen die niet worden weergegeven in de galerie met virtuele machines (bijvoorbeeld Windows 2008 R2 + SQL Server 2012)?**

   Nee. Voor galerie-afbeeldingen van virtuele machines die SQL Server bevatten, moet u een van de opgegeven afbeeldingen selecteren via de Azure Portal of via [PowerShell.](create-sql-vm-powershell.md) U hebt echter de mogelijkheid om een Windows-VM te implementeren en er zelf een SQL Server installeren. Vervolgens moet u uw [SQL Server-VM](sql-agent-extension-manually-register-single-vm.md) registreren met de SQL IaaS Agent-extensie om uw SQL Server-VM te beheren in de Azure Portal, en functies zoals automatische patching en automatische back-ups gebruiken. 


## <a name="creation"></a>Maken

1. **Hoe kan ik virtuele Azure-machine maken met SQL Server?**

   De eenvoudigste methode is het maken van een virtuele machine met SQL Server. Zie Provision a SQL Server virtual machine in the Azure Portal (Een virtuele machine inrichten in de portal) voor een zelfstudie over het registreren voor Azure en het maken van een [SQL Server-VM vanuit de portal.](create-sql-vm-portal.md) U kunt een virtuele-machine-afbeelding selecteren die gebruikmaakt van betalen per seconde SQL Server-licentieverlening, of u kunt een afbeelding gebruiken waarmee u uw eigen licentie SQL Server gebruiken. U hebt ook de mogelijkheid om SQL Server handmatig te installeren op een VM met een vrij gelicentieerde editie (Developer of Express) of door een on-premises licentie opnieuw te gebruiken. Zorg ervoor dat u uw [SQL Server-VM](sql-agent-extension-manually-register-single-vm.md) registreert bij de SQL IaaS Agent-extensie voor het beheren van uw SQL Server-VM in de portal, en gebruik functies zoals automatische patching en automatische back-ups. Als u uw eigen licentie gebruikt, moet u een [License Mobility via Software Assurance azure hebben.](https://azure.microsoft.com/pricing/license-mobility/) Zie [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Prijsrichtlijnen voor SQL Server Azure VM's) voor meer informatie.

1. **Hoe kan ik mijn on-premises SQL Server database migreren naar de cloud?**

   Maak eerst een virtuele Azure-machine met een SQL Server-instantie. Migreert vervolgens uw on-premises databases naar dat exemplaar. Zie Een SQL Server [migreren naar een SQL Server in een Azure-VM voor strategieën voor gegevensmigratie.](migrate-to-vm-from-sql-server.md)

## <a name="licensing"></a>Licentieverlening

1. **Hoe kan ik mijn gelicentieerd exemplaar van SQL Server installeren op een Azure-VM?**

   Er zijn drie manieren om dit te doen. Als u een klant van Enterprise Agreement (EA) bent, kunt u een van de [virtuele-machine-afbeeldingen](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)inrichten die licenties ondersteunen. Dit wordt ook wel BYOL (Bring Your Own License) genoemd. Als u een [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)hebt, kunt [](licensing-model-azure-hybrid-benefit-ahb-change.md) u de Azure Hybrid Benefit inschakelen op een bestaande pay-as-you-go-afbeelding (pay-as-you-go). Of u kunt de SQL Server-installatiemedia kopiëren naar een Windows Server-VM, en vervolgens SQL Server op de VM installeren. Zorg ervoor dat u uw virtuele SQL Server [](sql-agent-extension-manually-register-single-vm.md) registreren met de extensie voor functies zoals portalbeheer, automatische back-up en automatische patching. 


1. **Heeft een klant SQL Server Client Access Licenses (CAL's) nodig om verbinding te maken met een SQL Server betalen per gebruik-afbeelding die wordt uitgevoerd op Azure Virtual Machines?**

   Nee. Klanten hebben CAL's nodig wanneer ze bring-your-own-license gebruiken en hun SQL Server SA-server/CAL-VM verplaatsen naar Azure-VM's. 

1. **Kan ik een VM wijzigen zodat mijn eigen SQL Server-licentie wordt gebruikt, wanneer de VM is gemaakt vanuit een van de Betalen per gebruik-installatiekopieën uit de galerie?**

   Ja. U kunt eenvoudig overschakelen van een pay-as-you-go-galerie-afbeelding naar BRING-Your-Own-License (BYOL) door de Azure Hybrid Benefit [.](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)  Raadpleeg [Het licentiemodel voor een SQL Server-VM wijzigen](licensing-model-azure-hybrid-benefit-ahb-change.md) voor meer informatie. Deze faciliteit is momenteel alleen beschikbaar voor openbare en Azure Government cloudklanten.


1. **Treedt er downtime op voor SQL Server tijdens het schakelen tussen licentiemodellen?**

   Nee. [Het wijzigen van het licentiemodel](licensing-model-azure-hybrid-benefit-ahb-change.md) vereist geen downtime voor SQL Server omdat de wijziging onmiddellijk van kracht is en de VM niet opnieuw hoeft te worden opgestart. Als u uw SQL Server-VM echter wilt registreren met de SQL IaaS Agent-extensie, is de [SQL IaaS-extensie](sql-server-iaas-agent-extension-automate-management.md) een vereiste en wordt de SQL Server-service opnieuw opgestart als u de SQL IaaS-extensie in de volledige modus installeert.  Als de SQL IaaS-extensie moet worden geïnstalleerd, installeert u deze _daarom_ in de lichtgewicht modus voor beperkte functionaliteit of installeert u deze _in_ de volledige modus tijdens een onderhoudsvenster. De SQL IaaS-extensie die _is_  geïnstalleerd in de lichtgewicht modus kan op elk moment worden geüpgraded naar de volledige modus, maar hiervoor moet de SQL Server opnieuw worden opgestart. 
   
1. **Is het mogelijk om van licentiemodel te wisselen op een SQL Server VM die is geïmplementeerd met het klassieke model?**

   Nee. Het wijzigen van licentiemodellen wordt niet ondersteund op een klassieke VM. U kunt uw VM migreren naar het Azure Resource Manager model en registreren bij de SQL IaaS Agent-extensie. Zodra de VM is geregistreerd bij de SQL IaaS Agent-extensie, zijn wijzigingen in het licentiemodel beschikbaar op de VM.

1. **Kan ik de Azure-portal gebruiken om meerdere exemplaren op dezelfde VM te beheren?**

   Nee. Portalbeheer is een functie van de SQL IaaS Agent-extensie, die afhankelijk is van de SQL Server IaaS-agentextensie. Als zodanig gelden dezelfde beperkingen voor de extensie als voor de extensie. In de portal kan slechts één standaardexemplaar of één benoemd exemplaar worden beheerd, zolang het op de juiste wijze is geconfigureerd. Zie [IaaS-agentextensie SQL Server meer](sql-server-iaas-agent-extension-automate-management.md)informatie over deze beperkingen. 

1. **Kan Azure Hybrid Benefit worden geactiveerd met een CSP-abonnement?**

   Ja, Azure Hybrid Benefit is beschikbaar voor CSP-abonnementen. CSP-klanten moeten eerst een betalen per gebruik-afbeelding [](licensing-model-azure-hybrid-benefit-ahb-change.md) implementeren en vervolgens het licentiemodel wijzigen in Bring Your Own License.
   
 
1. **Moet ik betalen om een SQL-server een licentie te verlenen op een Azure-VM, als deze SQL-server alleen wordt gebruikt als stand-by of om een failover uit te voeren?**

   Als u een gratis passieve licentie wilt hebben voor een secundaire stand-bybeschikbaarheidsgroep of een failovercluster exemplaar, moet u voldoen aan alle volgende criteria, zoals beschreven in de [productlicentievoorwaarden:](https://www.microsoft.com/licensing/product-licensing/products)

   1. U hebt [licentiemobiliteit](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) via [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Het passieve SQL Server-exemplaar levert geen SQL Server-gegevens aan clients en voert geen actieve SQL Server-workloads uit. Dit exemplaar wordt alleen gebruikt om te synchroniseren met de primaire server, en behoudt de passieve database verder in een warme stand-by. Als er gegevens worden uitgevoerd, zoals rapporten voor clients die actieve SQL Server-workloads uitvoeren of andere werkzaamheden uitvoeren dan wat is opgegeven in de productvoorwaarden, moet het een betaalde gelicentieerde SQL Server zijn. De volgende activiteiten zijn toegestaan op het secundaire exemplaar: consistentiecontroles voor databases of CheckDB, volledige back-ups, back-ups van transactielogboeken, en het bewaken van gegevens over resourcegebruik. U kunt het primaire exemplaar en het bijbehorende exemplaar voor herstel na een noodgeval ook één keer in de 90 dagen gedurende korte tijd tegelijk uitvoeren, om herstel na noodgeval te testen. 
   1. De actieve SQL Server-licentie wordt gedekt door Software Assurance en  maakt één passieve secundaire SQL Server-instantie mogelijk, met maximaal dezelfde hoeveelheid rekenkracht als de gelicentieerde actieve server. 
   1. De secundaire SQL Server-VM maakt gebruik van de [disaster recovery-licentie](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) in de Azure Portal.
   
1. **Wat wordt beschouwd als een passief exemplaar?**

   Het passieve SQL Server-exemplaar levert geen SQL Server-gegevens aan clients en voert geen actieve SQL Server-workloads uit. Dit exemplaar wordt alleen gebruikt om te synchroniseren met de primaire server, en behoudt de passieve database verder in een warme stand-by. Als een exemplaar gegevens, zoals rapporten, levert aan clients waarop actieve SQL Server-workloads worden uitgevoerd, of als het andere werkzaamheden uitvoert dan de werkzaamheden die zijn opgegeven in de productvoorwaarden, moet het een betaald gelicentieerd SQL Server-exemplaar zijn. De volgende activiteiten zijn toegestaan op het secundaire exemplaar: consistentiecontroles voor databases of CheckDB, volledige back-ups, back-ups van transactielogboeken, en het bewaken van gegevens over resourcegebruik. U kunt het primaire exemplaar en het bijbehorende exemplaar voor herstel na een noodgeval ook één keer in de 90 dagen gedurende korte tijd tegelijk uitvoeren, om herstel na noodgeval te testen.
   

1. **Welke scenario's kunnen gebruikmaken van Disaster Recovery Benefit ?**

   De [licentiehandleiding](https://aka.ms/sql2019licenseguide) bevat scenario's waarin Disaster Recovery Benefit kan worden gebruikt. Raadpleeg de productvoorwaarden en neem contact op met uw licentiecontactpersoon of accountmanager voor meer informatie.

1. **Welke abonnementen bieden ondersteuning voor Disaster Recovery Benefit?**

   Uitgebreide programma's die abonnementsrechten bieden die vergelijkbaar zijn met die van Software Assurance als een vast voordeel, bieden ondersteuning voor Disaster Recovery Benefit. Dit is inclusief, maar is niet beperkt tot, de Open Value (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Agreement Subscription (EAS) en de Server and Cloud Enrollment (SCE). Raadpleeg de [productvoorwaarden en](https://www.microsoft.com/licensing/product-licensing/products) raadpleeg uw licentiecontactcontacten of account manager voor meer informatie. 

   
 ## <a name="extension"></a>Toestelnummer

1. **Brengt het registreren van mijn VM bij de nieuwe SQL IaaS Agent-extensie extra kosten met zich mee?**

   Nee. De SQL IaaS Agent-extensie zorgt alleen voor extra beheerbaarheid voor SQL Server azure-VM zonder extra kosten. 

1. **Is de SQL IaaS Agent-extensie beschikbaar voor alle klanten?**
 
   Ja, zolang de virtuele SQL Server is geïmplementeerd in de openbare cloud met behulp van het Resource Manager-model en niet het klassieke model. Alle andere klanten kunnen zich registreren met de nieuwe SQL IaaS Agent-extensie. Alleen klanten met het voordeel [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) kunnen echter hun eigen licentie gebruiken door de Azure Hybrid Benefit [(AHB) op](https://azure.microsoft.com/pricing/hybrid-benefit/) een virtuele SQL Server activeren. 

1. **Wat gebeurt er met de extensiebron (_Microsoft.SqlVirtualMachine)_ als de VM-resource wordt verplaatst of uitgevallen?** 

   Wanneer de resource Microsoft.Compute/VirtualMachine wordt uitgevallen of verplaatst, wordt de bijbehorende Microsoft.SqlVirtualMachine-resource op de hoogte gesteld om de bewerking asynchroon te repliceren.

1. **Wat gebeurt er met de virtuele machine als de extensie (_Microsoft.SqlVirtualMachine_) wordt uitgevallen?**

    De resource Microsoft.Compute/VirtualMachine wordt niet beïnvloed wanneer de resource Microsoft.SqlVirtualMachine wordt uitgevallen. De licentiewijzigingen worden echter standaard terug ingesteld op de oorspronkelijke bron van de afbeelding. 

1. **Is het mogelijk om zelf geïmplementeerde VM'SQL Server registreren met de SQL IaaS Agent-extensie?**

    Ja. Als u SQL Server hebt geïmplementeerd vanaf uw eigen media en de SQL IaaS-extensie hebt geïnstalleerd, kunt u uw SQL Server-VM registreren bij de extensie om de beheerbaarheidsvoordelen van de SQL IaaS-extensie te krijgen.    


## <a name="administration"></a>Beheer

1. **Kan ik een tweede exemplaar van een SQL Server op dezelfde VM installeren? Kan ik geïnstalleerde functies van het standaard exemplaar wijzigen?**

   Ja. De SQL Server-installatiemedia bevindt zich in een map op het **C-station.** Voer **Setup.exe** uit vanaf die locatie om nieuwe SQL Server-exemplaren toe te voegen of om andere geïnstalleerde functies van SQL Server op de computer te wijzigen. Houd er rekening mee dat sommige functies, zoals automatische back-up, automatische patches en Azure Key Vault-integratie, alleen worden uitgevoerd op basis van het standaard exemplaar of een benoemd exemplaar dat juist is geconfigureerd (zie vraag 3). Klanten die Software Assurance via [de Azure Hybrid Benefit](licensing-model-azure-hybrid-benefit-ahb-change.md) of  het licentiemodel voor betalen per gebruik, kunnen meerdere exemplaren van SQL Server installeren op de virtuele machine zonder extra licentiekosten. Extra SQL Server kunnen systeembronnen belasten, tenzij deze correct zijn geconfigureerd. 

1. **Wat is het maximum aantal exemplaren op een VM?**
   SQL Server 2012 tot SQL Server 2019 kunnen [50](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) exemplaren op een stand-alone server ondersteunen. Dit is dezelfde limiet, ongeacht in Azure on-premises. Zie [best practices voor](performance-guidelines-best-practices.md#multiple-instances) meer informatie over het beter voorbereiden van uw omgeving. 

1. **Kan ik het standaardexemplaar van SQL Server verwijderen?**

   Ja, maar er is een aantal overwegingen. Ten eerste SQL Server gekoppelde facturering mogelijk nog steeds plaatsvinden, afhankelijk van het licentiemodel voor de VM. Ten tweede, zoals vermeld in het vorige antwoord, zijn er functies die afhankelijk zijn van de SQL Server [IaaS-agentextensie](sql-server-iaas-agent-extension-automate-management.md). Als u het standaardexe exemplaar verwijdert zonder ook de IaaS-extensie te verwijderen, blijft de extensie zoeken naar het standaardexee exemplaar en kunnen er fouten in het gebeurtenislogboek worden gegenereerd. Deze fouten komen uit de volgende twee bronnen: **Microsoft SQL Server Credential Management** en Microsoft SQL Server **IaaS-agent.** Een van de fouten lijkt mogelijk op de volgende:

      Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk.

   Als u besluit het standaardexee exemplaar te verwijderen, moet u ook de SQL Server [IaaS-agentextensie](sql-server-iaas-agent-extension-automate-management.md) verwijderen. 

1. **Kan ik een benoemd exemplaar van een SQL Server met de IaaS-extensie?**
   
   Ja, als het benoemde exemplaar het enige exemplaar op de SQL Server is en als het oorspronkelijke standaard exemplaar [correct is verwijderd.](sql-server-iaas-agent-extension-automate-management.md#named-instance-support) Als er geen standaardexeem is en er meerdere benoemde exemplaren op één SQL Server-VM staan, kan de SQL Server IaaS-agentextensie niet worden geïnstalleerd.  

1. **Kan ik de SQL Server en de bijbehorende licentiefacturering van een virtuele SQL Server verwijderen?**

   Ja, maar u moet extra stappen ondernemen om te voorkomen dat er kosten in rekening worden gebracht voor uw SQL Server-exemplaar, zoals beschreven in [Prijsinformatie.](pricing-guidance.md) Als u het SQL Server-exemplaar volledig wilt verwijderen, kunt u migreren naar een andere Azure-VM zonder dat SQL Server vooraf is geïnstalleerd op de VM en de huidige virtuele SQL Server verwijderen. Als u de VM wilt behouden maar de facturering SQL Server stoppen, volgt u deze stappen: 

   1. Back-up van al uw gegevens, inclusief systeemdatabases, indien nodig. 
   1. Verwijder SQL Server, inclusief de SQL IaaS-extensie (indien aanwezig).
   1. Installeer de gratis [SQL Express-editie](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Registreer u bij de SQL IaaS Agent-extensie in [de lichtgewicht modus](sql-agent-extension-manually-register-single-vm.md).
   1. [Wijzig de editie van SQL Server](change-sql-server-edition.md#change-edition-in-portal) in de [Azure Portal](https://portal.azure.com) in Express om de facturering te stoppen.  
   1. (optioneel) Schakel de Express SQL Server uit door het starten van de service uit te schakelen. 

1. **Kan ik de Azure-portal gebruiken om meerdere exemplaren op dezelfde VM te beheren?**

   Nee. Portalbeheer wordt verzorgd door de SQL IaaS Agent-extensie, die afhankelijk is van de extensie SQL Server IaaS Agent. Als zodanig gelden dezelfde beperkingen voor de extensie als voor de extensie. De portal kan slechts één standaard exemplaar of één benoemd exemplaar beheren zolang het correct is geconfigureerd. Zie [IaaS SQL Server agentextensie voor meer informatie](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Bijwerken en patchen

1. **Hoe kan ik wijzigen in een andere versie/editie van SQL Server in een Azure-VM?**

   Klanten kunnen hun versie of editie van SQL Server wijzigen door gebruik te maken van een installatiemedium met de gewenste versie of editie van SQL Server. Zodra de versie of editie is gewijzigd, gebruikt u de Azure-portal om de versie- of editie-eigenschap van de VM te wijzigen zodat deze nauwkeurig de facturering voor de VM aangeeft. Zie change edition [of a SQL Server VM (Editie van](change-sql-server-edition.md)een virtuele SQL Server wijzigen) voor meer informatie. Er is geen factureringsverschil voor de verschillende versies van SQL Server, dus wanneer de versie van SQL Server is gewijzigd, is er geen verdere actie nodig.

1. **Waar kan ik de installatiemedia downloaden om de editie of versie van SQL Server te wijzigen?**

   Klanten die beschikken over [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kunnen hun installatiemedia downloaden van het [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klanten die geen VM-Software Assurance kunnen de installatiemedia gebruiken van een Azure Marketplace SQL Server VM-installatie afbeelding met de gewenste editie.
   
1. **Hoe worden updates en servicepacks toegepast op een VM met SQL Server?**

   virtuele machines geven u controle over de hostmachine, inclusief wanneer en hoe u updates wilt toepassen. U kunt handmatig de Windows-updates toepassen voor het besturingssysteem of u kunt de planningsservice [Automated Patching](automated-patching.md) inschakelen. Automated Patching installeert alle updates die als belangrijk zijn aangeduid, met inbegrip van SQL Server-updates in deze categorie. Overige optionele updates voor SQL Server moeten handmatig worden geïnstalleerd.

1. **Kan ik mijn exemplaar SQL Server 2008 /2008 R2 upgraden nadat ik deze heb geregistreerd bij de SQL IaaS Agent-extensie?**

   Als het besturingssysteem Windows Server 2008 R2 of hoger is, ja. U kunt elk installatiemedia gebruiken om de versie en editie van SQL Server bij te werken. Vervolgens kunt u uw [SQL IaaS-extensiemodus](sql-server-iaas-agent-extension-automate-management.md#management-modes)upgraden van geen _agent_ naar _volledige_. Als u dit doet, hebt u toegang tot alle voordelen van de SQL IaaS-extensie, zoals beheerbaarheid van de portal, automatische back-ups en automatische patching. Als de versie van het besturingssysteem Windows Server 2008 is, wordt alleen de NoAgent-modus ondersteund. 

1. **Hoe krijg ik gratis uitgebreide beveiligingsupdates voor mijn end-of-support exemplaren van SQL Server 2008 en SQL Server 2008 R2?**

   U kunt gratis [uitgebreide beveiligingsupdates krijgen](sql-server-2008-extend-end-of-support.md) door uw SQL Server naar een virtuele Azure-machine te verplaatsen. Zie [de opties voor end-of-support](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) voor meer informatie. 
  
   

## <a name="general"></a>Algemeen

1. **Worden SQL Server exemplaren van failoverclusters (FCI) ondersteund op virtuele Azure-VM's?**

   Ja. U kunt een exemplaar van een failovercluster installeren met behulp van [Premium-bestands shares (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) of [Storage Spaces Direct (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) voor het opslagsubsysteem. Premium-bestands shares bieden IOPS- en doorvoercapaciteit die voldoen aan de behoeften van veel workloads. Voor I/O-intensieve workloads kunt u overwegen om Opslagruimten Direct te gebruiken op basis van manged premium- of ultraschijven. U kunt ook clustering- of opslagoplossingen van derden gebruiken, zoals beschreven in Hoge beschikbaarheid en herstel na noodherstel voor SQL Server in [Azure Virtual Machines.](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)

   > [!IMPORTANT]
   > Op dit moment wordt de _volledige_ [IaaS SQL Server agentextensie](sql-server-iaas-agent-extension-automate-management.md) niet ondersteund voor SQL Server FCI in Azure. U wordt aangeraden de volledige _extensie_ te verwijderen van VM's die deel nemen aan de FCI en in plaats daarvan de extensie in de lichtgewicht _modus te_ installeren. Deze extensie ondersteunt functies, zoals automatische back-up en patching en sommige portalfuncties voor SQL Server. Deze functies werken niet voor virtuele SQL Server nadat de _volledige_ agent is verwijderd.

1. **Wat is het verschil tussen SQL Server VM's en de SQL Database service?**

   Conceptueel gezien verschilt het SQL Server uitvoeren op een virtuele Azure-machine niet zo veel van het uitvoeren van SQL Server in een extern datacenter. Daarentegen [Azure SQL Database](../../database/sql-database-paas-overview.md) database-as-a-service. Met SQL Database hebt u geen toegang tot de computers die uw databases hosten. Zie Choose a [cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS) (Een cloud-SQL Server kiezen: Azure SQL (PaaS) Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)of SQL Server op Azure VM's ) voor een volledige vergelijking.

1. **Hoe kan ik SQL Data-hulpprogramma's installeren op mijn Azure-VM?**

    Download en installeer de SQL [Data-hulpprogramma's van Microsoft SQL Server Data Tools - Business Intelligence voor Visual Studio 2013.](https://www.microsoft.com/download/details.aspx?id=42313)

1. **Worden gedistribueerde transacties met MSDTC ondersteund op SQL Server VM's?**
   
    Ja. Lokale DTC wordt ondersteund voor SQL Server 2016 SP2 en hoger. Toepassingen moeten echter worden getest bij het gebruik van Always On-beschikbaarheidsgroepen, omdat transacties tijdens een failover mislukken en opnieuw moeten worden ingediend. Geclusterde DTC is beschikbaar vanaf Windows Server 2019. 
    
1. **Worden Azure SQL virtuele machine verplaatst of worden klantgegevens buiten de regio opgeslagen?**

   Nee. In feite worden Azure SQL machine en de SQL IaaS-agentextensie geen klantgegevens opgeslagen.

## <a name="sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent-extensie

1. **Moet ik mijn virtuele SQL Server registreren die is ingericht vanuit een SQL Server-Azure Marketplace?**

   Nee. Microsoft registreert automatisch VM's die zijn ingericht vanuit de SQL Server-afbeeldingen in Azure Marketplace. Registreren met de extensie is alleen vereist  als de VM niet is ingericht vanuit de SQL Server-installatie Azure Marketplace en SQL Server zelf is geïnstalleerd.

1. **Is de SQL IaaS Agent-extensie beschikbaar voor alle klanten?** 

   Ja. Klanten moeten hun SQL Server-VM's registreren bij de extensie als ze geen SQL Server-installatie afbeelding van Azure Marketplace hebben gebruikt en in plaats daarvan zelf geïnstalleerde SQL Server, of als ze hun aangepaste VHD hebben meegebracht. VM's die eigendom zijn van alle typen abonnementen (Direct, Enterprise Agreement en Cloud Solution Provider) kunnen zich registreren bij de SQL IaaS Agent-extensie.

1. **Wat is de standaardbeheermodus bij het registreren bij de SQL IaaS Agent-extensie?**

   De standaardbeheermodus wanneer u zich registreert bij de SQL IaaS Agent-extensie is *lichtgewicht*. Als de SQL Server management-eigenschap niet is ingesteld wanneer u zich bij de extensie registreert, wordt de modus ingesteld als lichtgewicht en wordt de SQL Server-service niet opnieuw gestart. Het is raadzaam om u eerst te registreren bij de SQL IaaS Agent-extensie in de lichtgewicht modus en vervolgens een upgrade naar volledig uit te voeren tijdens een onderhoudsvenster. Op dezelfde manier is het standaardbeheer ook lichtgewicht wanneer u de functie [voor automatische registratie gebruikt.](sql-agent-extension-automatic-registration-all-vms.md)

1. **Wat zijn de vereisten voor registratie bij de SQL IaaS Agent-extensie?**

   Er zijn geen vereisten voor registratie bij de SQL IaaS Agent-extensie, anders dan dat SQL Server geïnstalleerd op de VM. Houd er rekening mee dat als de SQL IaaS-agentextensie is geïnstalleerd in de volledige modus, de SQL Server-service opnieuw wordt opgestart. Dit wordt daarom aanbevolen tijdens een onderhoudsvenster.

1. **Wordt er een agent op mijn VM geïnstalleerd als ik me registreer met de SQL IaaS Agent-extensie?**

   Ja, als u zich registreert bij de SQL IaaS Agent-extensie in de modus voor volledige beheerbaarheid, wordt er een agent op de VM geïnstalleerd. Registreren in de lightweight- of NoAgent-modus niet. 

   Als u zich registreert bij de SQL IaaS Agent-extensie in  de lichtgewicht modus, worden alleen de binaire bestanden van de SQL IaaS Agent-extensie naar de VM gekopieerd. De agent wordt niet geïnstalleerd. Deze binaire bestanden worden vervolgens gebruikt om de agent te installeren wanneer de beheermodus wordt bijgewerkt naar volledig.


1. **Wordt de registratie bij de SQL IaaS Agent-extensie opnieuw SQL Server mijn VM?**

   Dit is afhankelijk van de modus die tijdens de registratie is opgegeven. Als de modus Lightweight of NoAgent is opgegeven, wordt de SQL Server service niet opnieuw opgestart. Als u de beheermodus echter als volledig opgeeft, wordt SQL Server service opnieuw opgestart. De functie voor automatische registratie registreert uw SQL Server-VM's in de lichtgewicht modus, tenzij de Windows Server-versie 2008 is. In dat geval wordt de SQL Server-VM geregistreerd in de modus NoAgent. 

1. **Wat is het verschil tussen de lightweight- en NoAgent-beheermodi bij het registreren bij de SQL IaaS Agent-extensie?** 

   De beheermodus NoAgent is de enige beschikbare beheermodus voor SQL Server 2008 en SQL Server 2008 R2 in Windows Server 2008. Voor alle latere versies van Windows Server zijn de twee beschikbare beheerbaarheidsmodi licht en vol. 

   NoAgent-modus vereist dat SQL Server versie- en editie-eigenschappen worden ingesteld door de klant. De lightweight-modus vraagt de VM om de versie en editie van de SQL Server vinden.

1. **Kan ik me registreren met de SQL IaaS Agent-extensie zonder het SQL Server op te geven?**

   Nee. Het SQL Server licentietype is geen optionele eigenschap wanneer u zich registreert bij de SQL IaaS Agent-extensie. U moet het SQL Server-licentietype instellen als betalen per gebruik of Azure Hybrid Benefit wanneer u zich registreert bij de SQL IaaS Agent-extensie in alle beheerbaarheidsmodi (NoAgent, lightweight en full). Als u een van de gratis versies van SQL Server geïnstalleerd, zoals Developer of Evaluation Edition, moet u zich registreren met een licentie voor betalen per gebruik. Azure Hybrid Benefit is alleen beschikbaar voor betaalde versies van SQL Server zoals Enterprise- en Standard-edities.

1. **Kan ik de IaaS SQL Server-extensie upgraden van de NoAgent-modus naar de volledige modus?**

   Nee. Het upgraden van de beheerbaarheidsmodus naar volledig of lichtgewicht is niet beschikbaar voor de NoAgent-modus. Dit is een technische beperking van Windows Server 2008. U moet het besturingssysteem eerst upgraden naar Windows Server 2008 R2 of hoger, waarna u kunt upgraden naar de modus volledig beheer. 

1. **Kan ik de IaaS SQL Server-extensie upgraden van de lichtgewicht modus naar de volledige modus?**

   Ja. Het upgraden van de beheerbaarheidsmodus van licht naar volledig wordt ondersteund via Azure PowerShell of Azure Portal. Hierdoor wordt de SQL Server gestart.

1. **Kan ik de IaaS SQL Server extensie downgraden van de volledige modus naar NoAgent of de lightweight-beheermodus?**

   Nee. Het downgraden van SQL Server beheerbaarheidsmodus van de IaaS-extensie wordt niet ondersteund. De beheerbaarheidsmodus kan niet worden gedowngraded van de volledige modus naar de modus Lightweight of NoAgent en kan niet worden gedowngraded van de lichtgewicht modus naar de modus NoAgent. 

   Als u de beheerbaarheidsmodus [](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) wilt wijzigen van volledige beheerbaarheid, maakt u de registratie van de SQL Server-VM bij de SQL IaaS-agentextensie ongedaan door de resource van de virtuele SQL-machine  te laten vallen en de SQL Server-VM opnieuw te registreren bij de SQL IaaS Agent-extensie in een andere beheermodus.

1. **Kan ik me registreren bij de SQL IaaS Agent-extensie van de Azure Portal?**

   Nee. Registreren bij de SQL IaaS Agent-extensie is niet beschikbaar in de Azure Portal. Registreren bij de SQL IaaS Agent-extensie wordt alleen ondersteund met de Azure CLI of Azure PowerShell. 

1. **Kan ik een VM registreren bij de SQL IaaS Agent-extensie voordat SQL Server wordt geïnstalleerd?**

   Nee. Een VM moet ten minste één exemplaar van SQL Server (Database Engine) hebben om te kunnen worden geregistreerd bij de SQL IaaS Agent-extensie. Als er zich geen SQL Server op de virtuele machine, heeft de nieuwe resource Microsoft.SqlVirtualMachine de status Mislukt.

1. **Kan ik een VM registreren bij de SQL IaaS Agent-extensie als er meerdere SQL Server zijn?**

   Ja, mits er een standaard exemplaar op de VM is. Met de SQL IaaS Agent-extensie wordt slechts één SQL Server (Database Engine) geregistreerd. De SQL IaaS Agent-extensie registreert de standaardwaarde SQL Server in het geval van meerdere exemplaren.

1. **Kan ik een exemplaar van SQL Server failovercluster registreren met de SQL IaaS Agent-extensie?**

   Ja. SQL Server exemplaren van een failovercluster op een Azure-VM kunnen worden geregistreerd bij de SQL IaaS Agent-extensie in de lichtgewicht modus. Exemplaren SQL Server failovercluster kunnen echter niet worden geüpgraded naar de modus voor volledige beheerbaarheid.

1. **Kan ik mijn VM registreren bij de SQL IaaS Agent-extensie als er een Always On-beschikbaarheidsgroep is geconfigureerd?**

   Ja. Er zijn geen beperkingen voor het registreren van een SQL Server-exemplaar op een Azure-VM met de SQL IaaS Agent-extensie als u deelneemt aan de configuratie van een Always On-beschikbaarheidsgroep.

1. **Wat zijn de kosten voor het registreren bij de SQL IaaS Agent-extensie of bij het upgraden naar de modus voor volledige beheerbaarheid?**

   Geen. Er zijn geen kosten verbonden aan het registreren bij de SQL IaaS Agent-extensie of voor het gebruik van een van de drie beheerbaarheidsmodi. Het beheren van SQL Server VM met de extensie is volledig gratis. 

1. **Wat is de invloed op de prestaties van het gebruik van de verschillende beheerbaarheidsmodi?**

   Het gebruik van de *noagent* en de lichtgewicht beheerbaarheidsmodi heeft *geen* invloed. Het gebruik van de modus voor *volledige* beheerbaarheid van twee services die op het besturingssysteem zijn geïnstalleerd, heeft minimale gevolgen. Deze kunnen worden bewaakt via taakbeheer en worden gezien in de ingebouwde Windows Services-console. 

   De twee servicenamen zijn:
   - `SqlIaaSExtensionQuery` (Weergavenaam - `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Weergavenaam - `Microsoft SQL Server IaaS Agent` )

1. **Hoe kan ik de extensie verwijderen?**

   Verwijder de extensie door [de registratie van de virtuele](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server van de SQL IaaS Agent-extensie op te maken. 

## <a name="resources"></a>Resources

**Windows-VM's:**

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Een SQL Server inrichten op een Windows-VM](create-sql-vm-portal.md)
* [Een database migreren naar SQL Server op een Azure-VM](migrate-to-vm-from-sql-server.md)
* [Hoge beschikbaarheid en herstel na noodherstel voor SQL Server in Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Best practices voor prestaties voor SQL Server azure-Virtual Machines](performance-guidelines-best-practices.md)
* [Toepassingspatronen en ontwikkelingsstrategieën voor SQL Server in Azure Virtual Machines](application-patterns-development-strategies.md)

**Linux-VM's:**

* [Overzicht van SQL Server op een Linux-VM](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Een SQL Server inrichten op een Linux-VM](../linux/sql-vm-create-portal-quickstart.md)
* [Veelgestelde vragen (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentatie voor SQL Server op Linux](/sql/linux/sql-server-linux-overview)
