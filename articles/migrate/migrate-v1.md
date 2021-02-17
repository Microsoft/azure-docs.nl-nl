---
title: Werken met de vorige versie van Azure Migrate
description: Hierin wordt beschreven hoe u kunt werken met de vorige versie van Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 00e44185c938c94903e9b85a4748906721dac27f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571685"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Werken met de vorige versie van Azure Migrate

Dit artikel bevat informatie over het werken met de vorige versie van Azure Migrate. 


Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: Gebruik deze versie om Azure Migrate-projecten te maken, on-premises machines te detecteren, en evaluaties en migraties te organiseren. [Lees meer](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: Als u de vorige versie van Azure Migrate gebruikt (alleen evaluatie van on-premises VMware-VM's werd ondersteund), kunt u nu beter de huidige versie gaan gebruiken. De projecten uit de vorige versie worden in dit artikel Klassieke projecten genoemd. Klassieke Azure Migrate buiten gebruik worden gesteld in februari 2024. Na februari 2024 wordt de klassieke versie van Azure Migrate niet meer ondersteund en worden de meta gegevens van de inventaris in klassieke projecten verwijderd. Als u nog steeds klassieke Azure Migrate projecten moet gebruiken, is dit wat u wel en niet kunt doen:
    - U kunt geen migratieprojecten meer maken.
    - We raden u aan om ook geen nieuwe detecties meer uit te voeren.
    - U houdt toegang tot bestaande projecten.
    - U kunt nog steeds evaluaties uitvoeren.
    

## <a name="upgrade-between-versions"></a>Upgrade tussen versies

U kunt projecten of onderdelen in de vorige versie niet upgraden naar de nieuwe versie. U moet [een nieuw Azure Migrate-project maken](create-manage-projects.md) en daar [hulpprogramma's voor evaluatie en migratie aan toevoegen](./create-manage-projects.md). Gebruik de zelfstudies om te begrijpen hoe u de beschikbare hulpprogramma’s voor evaluatie en migratie kunt gebruiken. Als er een Log Analytics-werkruimte aan een Klassiek project was gekoppeld, kunt u deze aan een project in de huidige versie koppelen nadat u het Klassieke project hebt verwijderd.

## <a name="find-projects-from-previous-version"></a>Projecten uit de vorige versie zoeken

Ga als volgt te werk om projecten uit de vorige versie te zoeken:

1. Ga in de Azure-portal naar **Alle services**, zoek naar **Azure Migrate** en selecteer het. 
2. Op het dashboard van Azure Migrate bevindt zich een melding met een koppeling voor toegang tot oude Azure Migrate-projecten.
3. Klik op de koppeling om Klassieke projecten te openen.

## <a name="delete-projects-from-previous-version"></a>Projecten uit de vorige versie verwijderen

Ga als volgt te werk om projecten uit de vorige versie te vinden en verwijderen:

1. Ga in de Azure-portal naar **Alle services**, zoek naar **Azure Migrate** en selecteer het. 
2. Op het dashboard van Azure Migrate bevindt zich een melding met een koppeling voor toegang tot oude Azure Migrate-projecten.
3. Klik op de koppeling om Klassieke projecten te openen.
4. Selecteer het project dat u wilt verwijderen, en verwijder het. 


## <a name="create-an-assessment"></a>Een evaluatie maken

Nadat virtuele machines in de portal zijn gedetecteerd, kunt u ze groeperen en een evaluatie maken.

- U kunt onmiddellijk 'als on-premises'-evaluaties maken zodra er VM's in de portal zijn gedetecteerd.
- Voor prestatie-evaluaties raden we aan ten minste een dag te wachten met het maken van een prestatie-evaluatie, om betrouwbare aanbevelingen voor de grootte te krijgen.

Maak als volgt een evaluatie:

1. Klik op de **overzichtspagina** van het project op **+Evaluatie maken**.
2. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.
3. Maak de groep en geef een groepsnaam op.
4. Selecteer de machines die u aan de groep wilt toevoegen.
5. Klik op **Evaluatie maken** om de groep en de evaluatie te maken.
6. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Overzicht** > **Dashboard**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.

Als u een bestaande evaluatie wilt bijwerken met de meest recente prestatiegegevens, kunt u de opdracht **Opnieuw berekenen** voor de evaluatie gebruiken om deze bij te werken.

## <a name="review-an-assessment"></a>Een evaluatie beoordelen 

Een evaluatie heeft drie fasen:

- Een evaluatie begint met een geschiktheidsanalyse om erachter te komen of machines compatibel zijn in Azure.
- Schattingen van de grootte.
- Schatting van de maandelijkse kosten.

Een machine gaat alleen door naar een latere fase als deze door de voorgaande fase is gekomen. Als een machine bijvoorbeeld niet door de geschiktheidscontrole komt, wordt deze gemarkeerd als niet geschikt voor Azure en worden de grootte en de kosten niet bepaald.


### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

De weergave van de Azure-gereedheid in de evaluatie toont de gereedheidsstatus van alle VM's.

**Gereedheid** | **Status** | **Details**
--- | --- | ---
Gereed voor Azure | Geen compatibiliteitsproblemen. De machine kan in de huidige toestand naar Azure worden gemigreerd, en kan in Azure worden opgestart met volledige Azure-ondersteuning. | Voor virtuele machines die gereed zijn, wordt door Azure Migrate een VM-grootte in Azure aanbevolen.
Voorwaardelijk gereed voor Azure | De machine kan worden opgestart in Azure, maar heeft mogelijk geen volledige ondersteuning voor Azure. Bijvoorbeeld een machine met een oudere versie van Windows Server die niet wordt ondersteund in Azure. | In Azure Migrate worden de gereedheidsproblemen beschreven en worden herstelstappen voorgesteld.
Niet gereed voor Azure |  De VM kan niet worden opgestart in Azure. Als een VM bijvoorbeeld een schijf van meer dan 4 TB heeft, kan deze niet worden gehost op Azure. | In Azure Migrate worden de gereedheidsproblemen beschreven en worden herstelstappen voorgesteld.
Gereedheid onbekend | Azure Migrate kan de gereedheid voor Azure niet vaststellen, meestal omdat er geen gegevens beschikbaar zijn.


#### <a name="azure-vm-properties"></a>Azure VM-eigenschappen
Gereedheid houdt rekening met een aantal VM-eigenschappen om te bepalen of de VM kan worden uitgevoerd in Azure.


**Eigenschap** | **Details** | **Gereedheid**
--- | --- | ---
**Opstarttype** | BIOS ondersteund. UEFI niet ondersteund. | Voorwaardelijk gereed als het opstarttype UEFI is.
**Kernen** | Kernen van machine <= het maximum aantal kernen (128) dat wordt ondersteund voor een Azure-VM.<br/><br/> Als de prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met de gebruikte kernen.<br/>Als er een comfortfactor is opgegeven in de evaluatie-instellingen, wordt het aantal gebruikte kernen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen prestatiegeschiedenis is, gebruikt Azure Migrate de toegewezen kernen, zonder de comfortfactor toe te passen. | Gereed indien kleiner dan of gelijk aan de limieten.
**Geheugen** | De grootte van het geheugen van de machine <= het maximale geheugen (3892 GB op Azure M series Standard_M128m&nbsp;<sup>2</sup>) voor een Azure-VM. [Meer informatie](../virtual-machines/sizes.md).<br/><br/> Als de prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met het gebruikte geheugen.<br/><br/>Als er een comfortfactor is opgegeven, wordt het gebruikte geheugen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen geheugen gebruikt, zonder dat de comfortfactor wordt toegepast.<br/><br/> | Gereed indien binnen de limieten.
**Opslagschijf** | De toegewezen grootte van een schijf moet 4 TB (4096 GB) of minder zijn.<br/><br/> Het aantal schijven dat is gekoppeld aan de machine moet 65 of minder zijn, inclusief de besturingssysteemschijf. | Gereed indien binnen de limieten.
**Netwerken** | Aan een machine moet 32 of minder NIC's zijn gekoppeld. | Gereed indien binnen de limieten.

#### <a name="guest-operating-system"></a>Gastbesturingssysteem

Behalve naar de VM-eigenschappen kijkt Azure Migrate ook naar het gastbesturingssysteem van de on-premises VM om te bepalen of de VM kan worden uitgevoerd in Azure.

- Azure Migrate kijkt naar het besturingssysteem dat is opgegeven in vCenter Server.
- Omdat de detectie door Azure Migrate is gebaseerd op het apparaat, is er geen manier om te controleren of het besturingssysteem dat in de VM wordt uitgevoerd, hetzelfde is als wat is opgegeven in vCenter Server.

De volgende logica wordt gebruikt.

**Besturingssysteem** | **Details** | **Gereedheid**
--- | --- | ---
Windows Server 2016 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 R2 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2008 R2 en alle SP's | Azure biedt volledige ondersteuning.| Gereed voor Azure
Windows Server 2008 (32-bits en 64-bits) | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2003, 2003 R2 | Niet meer ondersteund, en vereist een [aangepaste ondersteuningsovereenkomst (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) voor ondersteuning in Azure. | Voorwaardelijk gereed voor Azure. Overweeg het besturingssysteem te upgraden voordat u naar Azure migreert.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Niet meer ondersteund. De machine kan mogelijk worden opgestart in Azure, maar Azure biedt geen ondersteuning voor het besturingssysteem. | Voorwaardelijk gereed voor Azure. Wij raden aan het besturingssysteem te upgraden voordat u naar Azure migreert.
Windows-client 7, 8 en 10 | Azure biedt alleen ondersteuning met een [Visual Studio-abonnement.](../virtual-machines/windows/client-images.md) | Voorwaardelijk gereed voor Azure
Windows 10 Pro Desktop | Azure biedt ondersteuning met [multitenant-hostingrechten.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Voorwaardelijk gereed voor Azure
Windows Vista, XP Professional | Niet meer ondersteund. De machine kan mogelijk worden opgestart in Azure, maar Azure biedt geen ondersteuning voor het besturingssysteem. | Voorwaardelijk gereed voor Azure. Wij raden aan het besturingssysteem te upgraden voordat u naar Azure migreert.
Linux | Azure keurt deze [Linux-besturingssystemen](../virtual-machines/linux/endorsed-distros.md) goed. Andere Linux-besturingssystemen kunnen mogelijk worden opgestart in Azure, maar wij raden aan het besturingssysteem te upgraden naar een goedgekeurde versie voordat u naar Azure migreert. | Gereed voor Azure als de versie goedgekeurd is.<br/><br/>Voorwaardelijk gereed als de versie niet goedgekeurd is.
Andere besturingssystemen<br/><br/> Bijvoorbeeld Oracle Solaris, Apple macOS, FreeBSD, enz. | Deze besturingssystemen worden niet door Azure goedgekeurd. De machine kan mogelijk worden opgestart in Azure, maar Azure biedt geen ondersteuning voor het besturingssysteem. | Voorwaardelijk gereed voor Azure. Wij raden aan een ondersteund besturingssysteem te installeren voordat u naar Azure migreert.  
Besturingssysteem dat is opgegeven als **Anders** in vCenter Server | In dit geval kan Azure Migrate het besturingssysteem niet identificeren. | Gereedheid onbekend. Zorg ervoor dat het besturingssysteem dat wordt uitgevoerd in de VM wordt ondersteund in Azure.
32-bits besturingssystemen | De computer kan mogelijk worden opgestart in Azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Voorwaardelijk gereed voor Azure. Overweeg het besturingssysteem van de machine te upgraden van een 32-bits naar een 64-bits besturingssysteem voordat u naar Azure migreert.


### <a name="review-sizing"></a>Grootte bepalen

 De grootte die Azure Migrate aanbeveelt, is afhankelijk van het criterium voor het instellen van de grootte dat is opgegeven in de evaluatie-eigenschappen.

- Als de grootte wordt bepaald op basis van de prestaties, wordt de aanbeveling gedaan op basis van de prestatiegeschiedenis (CPU en geheugen) en schijven (IOPS en doorvoer) van de VM.
- Als het criterium voor het bepalen van de grootte 'als on-premises' is, wordt de aanbevolen grootte in Azure gebaseerd op de grootte van de on-premises VM. De schijfgrootte wordt gebaseerd op het opslagtype dat is opgegeven in de evaluatie-eigenschappen (standaard is Premium-schijven). Azure Migrate houdt geen rekening met de prestatiegegevens voor de VM en schijven.

### <a name="review-cost-estimates"></a>Geschatte kosten

Kostenschattingen tonen de totale compute- en opslagkosten voor het uitvoeren van de VM's in Azure, evenals de details per machine.

- De geschatte kosten worden berekend op basis van de aanbevolen grootte voor een VM-machine, de schijven ervan, en de evaluatie-eigenschappen.
- Geschatte maandelijkse kosten voor computing en opslag worden samengevoegd voor alle virtuele machines in de groep.
- De kostenschatting geldt voor het uitvoeren van de on-premises VM als IaaS-VM's (Infrastructure as a Service). Azure Migrate houdt geen rekening met PaaS- of SaaS-kosten (Platform as a service of Software as a service).

### <a name="review-confidence-rating-performance-based-assessment"></a>Betrouwbaarheidsclassificatie (beoordeling op basis van prestaties)

Elke evaluatie op basis van prestaties is gekoppeld aan een betrouwbaarheidsclassificatie.

- Een betrouwbaarheidsclassificatie varieert van één ster tot vijf sterren (één ster is de laagste en vijf sterren de hoogste).
- De betrouwbaarheidsclassificatie wordt aan een evaluatie toegewezen op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.
- De betrouwbaarheidsclassificatie van een evaluatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die Azure Migrate geeft.
- Er is geen betrouwbaarheidsclassificatie beschikbaar voor 'as-is' on-premises evaluaties.

Azure Migrate heeft de volgende informatie nodig voor groottebepaling op basis van prestaties:
- Gebruiksgegevens voor CPU.
- VM-geheugengegevens.
- Voor elke schijf die aan de VM is gekoppeld, zijn de IOPS en de doorvoergegevens van de schijf vereist.
- Voor elke aan een VM gekoppelde netwerkadapter heeft Azure Migrate de netwerk-invoer/uitvoer nodig.
- Als een van de bovenstaande gegevens niet beschikbaar is, zijn de aanbevelingen voor de grootte minder betrouwbaar (en is de betrouwbaarheidsclassificatie dus lager).


Afhankelijk van het beschikbare percentage gegevenspunten, worden de mogelijke betrouwbaarheidsclassificaties in de tabel samengevat.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Evaluatieproblemen die de betrouwbaarheidsclassificatie beïnvloeden

Mogelijk zijn niet alle gegevenspunten beschikbaar voor een evaluatie, vanwege een aantal redenen:

- U hebt uw omgeving niet voor de duur van de evaluatie geprofileerd. Als u de evaluatie bijvoorbeeld maakt met de duur van de prestaties ingesteld op één dag, moet u na het starten van de detectie minimaal een dag wachten, totdat alle gegevenspunten zijn verzameld.
- Er zijn enkele VM's uitgeschakeld in de periode waarover de evaluatie werd berekend. Als er VM's een gedeelte van de tijd uitgeschakeld zijn geweest, kan Azure Migrate voor die periode geen prestatiegegevens verzamelen.
- Er zijn VM's gemaakt in de berekeningsperiode van de evaluatie. Als u bijvoorbeeld een evaluatie maakt op basis van de prestatiegeschiedenis van de afgelopen maand, maar u hebt een week geleden een aantal VM's gemaakt in de omgeving, geldt de prestatiegeschiedenis van de nieuwe VM's niet voor de gehele duur.

> [!NOTE]
> Als de betrouwbaarheidsclassificatie van een evaluatie lager is dan vijf sterren, wacht dan minimaal een dag tot het apparaat de omgeving heeft geprofileerd en bereken de evaluatie dan opnieuw. Als u dit niet doet, is de groottebepaling op basis van prestaties mogelijk niet betrouwbaar. Als u niet opnieuw wilt berekenen, raden wij aan over te schakelen op groottebepaling 'als on-premises', door de evaluatie-eigenschappen te wijzigen.



## <a name="create-groups-using-dependency-visualization"></a>Groepen maken met behulp van afhankelijkheidsvisualisatie

Naast het handmatig maken van groepen, kunt u groepen maken met behulp van afhankelijkheidsvisualisatie.
- Normaal gesproken gebruikt u deze methode als u groepen met een grotere mate van vertrouwen wilt beoordelen door afhankelijkheden van machines kruiselings te controleren voordat u een evaluatie uitvoert.
- Afhankelijkheidsvisualisatie helpt u uw migratie naar Azure effectief te plannen. Het helpt u ervoor te zorgen dat er niets achterblijft, en dat er geen onverwachte uitval optreedt wanneer u naar Azure migreert.
- U kunt alle onderling afhankelijke systemen detecteren die samen moeten worden gemigreerd, en bepalen of een actief systeem nog wel gebruikers bedient of dat het een kandidaat is voor buitengebruikstelling in plaats van migratie.
- Azure Migrate maakt gebruik van de Servicetoewijzing-oplossing in Azure Monitor om afhankelijkheidsvisualisatie mogelijk te maken.

> [!NOTE]
> Afhankelijkheidsvisualisatie is niet beschikbaar in Azure Government.

Als u afhankelijkheidsvisualisatie wilt instellen, koppelt u een Log Analytics-werkruimte aan een Azure Migrate-project, installeert u agents op machines waarvoor u afhankelijkheden wilt visualiseren en maakt u vervolgens groepen met behulp van afhankelijkheidsinformatie. 



### <a name="associate-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte koppelen

Als u afhankelijkheidsvisualisatie wilt gebruiken, koppelt u een Log Analytics-werkruimte aan een migratieproject. U kunt alleen een werkruimte maken of koppelen in hetzelfde abonnement als waar het migratieproject wordt gemaakt.

1. Als u een Log Analytics-werkruimte aan een project wilt koppelen, klikt u in **Overzicht**> **Essentials** op **Vereist configuratie**.
2. U kunt een nieuwe werkruimte maken of een bestaande koppelen:
  - Geef een naam op om een nieuwe werkruimte te maken. De werkruimte wordt gemaakt in een regio in dezelfde [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) als het migratieproject.
  - Wanneer u een bestaande werkruimte koppelt, kunt u kiezen uit alle beschikbare werkruimten in hetzelfde abonnement als het migratieproject. Er worden alleen werkruimten weergegeven die zijn gemaakt in een [door Servicetoewijzing ondersteunde regio](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions). Als u een werkruimte wilt koppelen, moet u ervoor zorgen dat u 'Lezer'-toegang hebt tot de werkruimte.

> [!NOTE]
> U kunt de werkruimte die aan een migratieproject is gekoppeld, niet wijzigen.

### <a name="download-and-install-vm-agents"></a>VM-agents downloaden en installeren

Nadat u een werkruimte hebt geconfigureerd, downloadt en installeert u agents op elke on-premises machine die u wilt evalueren. Als u machines zonder internetverbinding hebt, moet u bovendien [Log Analytics-gateway](../azure-monitor/agents/gateway.md) downloaden en installeren op deze machines.

1. Klik in **Overzicht** op **Beheer** > **Machines**, en selecteer de gewenste machine.
2. Klik in de kolom **Afhankelijkheden** op **Agents installeren**.
3. Download en installeer op de pagina **Afhankelijkheden** de MMA (Microsoft Monitoring Agent) en de afhankelijkheidsagent op elke VM die u wilt evalueren.
4. Kopieer de werkruimte-id en -sleutel. U hebt deze nodig wanneer u de MMA installeert op de on-premises machine.

> [!NOTE]
> Als u de installatie van agents wilt automatiseren, kunt u een implementatiehulpprogramma zoals Configuration Manager of een partnerprogramma zoals [Intigua](https://www.intigua.com/intigua-for-azure-migration) gebruiken, dat een oplossing voor het implementeren van agents biedt voor Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>De MMA-agent op een Windows-computer installeren

Installeer de agent als volgt op een Windows-computer:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Akkoord** om de licentie te accepteren.
3. Behoud of wijzig in **Doelmap** de standaardinstallatiemap > **Volgende**.
4. Selecteer in **Installatieopties voor agent** de optie **Azure Log Analytics** > **Volgende**.
5. Klik op **Toevoegen** om een nieuwe Log Analytics-werkruimte toe te voegen. Plak de werkruimte-id en -sleutel die u in de portal hebt gekopieerd. Klik op **Volgende**.

U kunt de agent installeren vanaf de opdrachtregel of met behulp van een geautomatiseerde methode zoals Configuration Manager. [Meer informatie](../azure-monitor/agents/log-analytics-agent.md#installation-options) over het gebruiken van deze methoden om de MMA-agent te installeren.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>De MMA-agent op een Linux-computer installeren

Installeer de agent als volgt op een Linux-computer:

1. Breng de juiste bundel (x86 of x64) met behulp van scp/ftp over naar uw Linux-computer.
2. Installeer de bundel met behulp van het argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Meer informatie](../azure-monitor/agents/agents-overview.md#supported-operating-systems) over de lijst met door MMA ondersteunde Linux-besturingssystemen.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>De MMA-agent installeren op een computer die wordt bewaakt door Operations Manager

Voor computers die worden bewaakt met System Center Operations Manager 2012 R2 of hoger is het niet nodig om de MMA-agent te installeren. Servicetoewijzing wordt geïntegreerd met de Operations Manager MMA om de benodigde afhankelijkheidsgegevens te verzamelen. [Meer informatie](../azure-monitor/vm/service-map-scom.md#prerequisites). De afhankelijkheidsagent moet wel worden geïnstalleerd.

### <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren

1. Als u de afhankelijkheidsagent op een Windows-computer wilt installeren, dubbelklikt u op het installatiebestand en volgt u de wizard.
2. Als u de afhankelijkheidsagent op een Linux-computer wilt installeren, installeert u als root met de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

- Meer informatie over de [ondersteuning voor de afhankelijkheidsagent](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) voor de Windows- en Linux-besturingssystemen.
- [Meer informatie](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) over hoe u scripts kunt gebruiken om de afhankelijkheidsagent te installeren.

>[!NOTE]
> Het artikel Azure Monitor voor VM's waarnaar wordt verwezen om een overzicht te geven van de systeemvereisten, en de methoden voor het implementeren van de afhankelijkheidsagent, zijn ook van toepassing op de Servicetoewijzing-oplossing.

### <a name="create-a-group-with-dependency-mapping"></a>Een groep met afhankelijkheidstoewijzing maken

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **Beheren** > **Machines**.
2. Zoek naar de machine waarop u de agents hebt geïnstalleerd.
3. De kolom **Afhankelijkheden** voor de machine zou nu moeten worden weergegeven als **Afhankelijkheden weergeven**. Klik op de kolom om de afhankelijkheden van de machine weer te geven.
4. De afhankelijkheidstoewijzing voor de machine bevat de volgende details:
    - Inkomende (clients) en uitgaande (servers) TCP-verbindingen van/naar de machine
        - De afhankelijke machines waarop geen MMA en afhankelijkheidsagent is geïnstalleerd, worden gegroepeerd op poortnummer.
        - De afhankelijke machines waarop de MMA en afhankelijkheidsagent wel is geïnstalleerd, worden weergegeven als aparte vakken.
    - Processen die worden uitgevoerd op de machine; u kunt elk machinevak uitvouwen om de processen weer te geven
    - Machine-eigenschappen, inclusief de FQDN, besturingssysteem, MAC-adres worden weergegeven. U kunt op elk machinevak klikken om details weer te geven.

4. U kunt afhankelijkheden voor verschillende tijdsduren weergeven door te klikken op de tijdsduur in het tijdsbereiklabel. Het bereik is standaard een uur. U kunt het tijdsbereik wijzigen of begin- en einddatums en duur opgeven.

   > [!NOTE]
   >    Er wordt een tijdsbereik van maximaal een uur ondersteund. Gebruik Azure Monitor-logboeken voor het [opvragen van afhankelijkheidsgegevens](./how-to-create-group-machine-dependencies.md) over een langere periode.

5. Nadat u de afhankelijke machines die u wilt groeperen, hebt geïdentificeerd, gebruikt u Ctrl+klikken om meerdere computers in de toewijzing te selecteren en klikt u op **Machines groeperen**.
6. Geef een groepsnaam op. Controleer of de afhankelijke machines worden gedetecteerd door Azure Migrate.

    > [!NOTE]
    > Als een afhankelijke machine niet wordt gedetecteerd door Azure Migrate, kunt u deze niet toevoegen aan de groep. Als u dergelijke machines aan de groep wilt toevoegen, moet u het detectieproces opnieuw uitvoeren met het juiste bereik in vCenter Server en ervoor zorgen dat de computer wordt gedetecteerd door Azure Migrate.  

7. Als u een evaluatie voor deze groep wilt maken, schakelt u het selectievakje in om een nieuwe evaluatie voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Nadat de groep is gemaakt, wordt aanbevolen agents op alle machines van de groep te installeren en de groep te verfijnen door de afhankelijkheid van de hele groep te visualiseren.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Afhankelijkheidsgegevens uit Azure Monitor-logboeken opvragen

Afhankelijkheidsgegevens die door Servicetoewijzing zijn vastgelegd, zijn beschikbaar om te worden opgevraagd in de Log Analytics-werkruimte die is gekoppeld aan uw Azure Migrate-project. [Lees meer](../azure-monitor/vm/service-map.md#log-analytics-records) over de Servicetoewijzing-gegevenstabellen die u kunt opvragen in Azure Monitor-logboeken. 

De Kusto-query's uitvoeren:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **Overzicht**.
2. Ga in **Overzicht** naar de sectie **Essentials** van het project en klik op de werkruimtenaam naast **OMS-werkruimte**.
3. Klik op de werkruimtepagina van Log Analytics op **Algemeen** > **Logboeken**.
4. Schrijf uw query om afhankelijkheidsgegevens te verzamelen met behulp van Azure Monitor-logboeken. In de volgende sectie vindt u enkele voorbeeldquery's.
5. Voer de query uit door op Uitvoeren te klikken. 

[Meer informatie](../azure-monitor/logs/log-analytics-tutorial.md) over het schrijven van Kusto-query's. 

### <a name="sample-azure-monitor-logs-queries"></a>Voorbeeldquery's voor Azure Monitor-logboeken

Hieronder vindt u voorbeeldquery's die u kunt gebruiken om afhankelijkheidsgegevens te extraheren. U kunt de query's aanpassen om de gewenste gegevenspunten te extraheren. [Hier](../azure-monitor/vm/service-map.md#log-analytics-records) is een volledige lijst met velden in afhankelijkheidsgegevensrecords beschikbaar. [Hier](../azure-monitor/vm/service-map.md#sample-log-searches) kunt u meer voorbeeldquery's vinden.

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Inkomende verbindingen op een set machines samenvatten

De records in de tabel voor metrische gegevens over verbindingen, VMConnection, vertegenwoordigen geen afzonderlijke fysieke netwerkverbindingen. Er worden meerdere fysieke netwerkverbindingen gegroepeerd in een logische verbinding. [Meer informatie](../azure-monitor/vm/service-map.md#connections) over hoe fysieke netwerkverbindingsgegevens worden geaggregeerd in één logische record in VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Verzonden en ontvangen gegevensvolume op inkomende verbindingen tussen een set machines samenvatten

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Volgende stappen
[Lees meer over](migrate-services-overview.md) de nieuwste versie van Azure Migrate.