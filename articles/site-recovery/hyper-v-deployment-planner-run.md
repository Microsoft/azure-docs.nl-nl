---
title: De Hyper-V-Deployment Planner uitvoeren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u de Azure Site Recovery Deployment Planner uitvoert voor herstel na nood geval voor Hyper-V naar Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mayg
ms.openlocfilehash: ba1979c940d4a92b3d1a7a52a4f356b2896ece55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74082610"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>De Azure Site Recovery-implementatieplanner uitvoeren voor herstel na noodgevallen van Hyper-V naar Azure

U kunt het opdracht regel programma Site Recovery Deployment planner (ASRDeploymentPlanner.exe) uitvoeren in een van de volgende vier modi: 
-   De lijst met VM’s (virtuele machines) ophalen
-   [Profiel](#profile-hyper-v-vms)
-   Een rapport genereren
-   [Doorvoer bepalen](#get-throughput)

Voer eerst het hulpprogramma uit voor het ophalen van de lijst met virtuele machines van een of meer Hyper-V-hosts. Voer daarna het hulpprogramma uit in de profileringsmodus om gegevensverloop en IOPS te verzamelen voor de virtuele machine. Voer het hulpprogramma daarna uit om het rapport te genereren en de netwerkbandbreedte en opslagvereisten te bepalen.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>De lijst met VM’s ophalen voor het profileren van virtuele Hyper-V-machines
U moet eerst een lijst maken met de virtuele machines die u wilt profileren. Gebruik de GetVMList-modus van de implementatieplanner om met een enkele opdracht de lijst te genereren met VM's die aanwezig zijn op meerdere Hyper-V-hosts. Nadat u de volledige lijst hebt gegenereerd, kunt u de VM’s die u niet wilt profileren, uit het uitvoerbestand verwijderen. Gebruik het uitvoerbestand vervolgens voor alle overige bewerkingen, zoals profileren, rapporten genereren en het bepalen van de doorvoer.

U kunt de lijst met VM’s genereren door het hulpprogramma te laten verwijzen naar een Hyper-V-cluster, naar een zelfstandige Hyper-V-host of naar een combinatie van beide.

### <a name="command-line-parameters"></a>Opdrachtregelparameters
Hieronder ziet u een tabel met verplichte en optionele parameters van het hulpprogramma, die u kunt uitvoeren in de GetVMList-modus. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Parameternaam | Beschrijving |
|---|---|
| -Operation | GetVMList |
| -User | De gebruikersnaam om verbinding te maken met de Hyper-V-host of het Hyper-V-cluster. De gebruiker moet beheerderstoegang hebben.|
| -ServerListFile | Het bestand met de lijst met servers die de VM’s bevatten die moeten worden geprofileerd. Het bestandspad kan absoluut of relatief zijn. Dit bestand moet op elke regel een van de volgende items bevatten:<ul><li>Naam of IP-adres van Hyper-V-host</li><li>Naam of IP-adres van Hyper-V-cluster</li></ul><br>**Voorbeeld:** ServerList.txt bevat de volgende servers:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Optioneel) Het UNC-pad (Universal Naming Convention) of het pad naar de lokale directory voor het opslaan van gegevens die tijdens deze bewerking worden gegenereerd. Als er geen naam is opgegeven, wordt de directory met de naam ProfiledData onder het huidige pad gebruikt als de standaarddirectory.|
|-OutputFile| Beschrijving Het bestand met de lijst met virtuele machines die op de Hyper-V-servers zijn opgehaald, wordt opgeslagen. Als er geen naam wordt vermeld, worden de details opgeslagen in VMList.txt.  Gebruik het bestand om met de profilering te starten nadat u de virtuele machines hebt verwijderd die niet hoeven te worden geprofileerd.|
|-Password|(Optioneel) Het wachtwoord om verbinding te maken met de Hyper-V-host. Als u dit niet als een parameter opgeeft, wordt u om dit wachtwoord gevraagd wanneer u de opdracht uitvoert.|

### <a name="getvmlist-discovery"></a>GetVMList-detectie

- **Hyper-V-cluster**: als de naam van het Hyper-V-cluster wordt vermeld in het lijstbestand van de server, worden met het hulpprogramma alle Hyper-V-knooppunten van het cluster gevonden en worden de VM’s opgehaald die aanwezig zijn op elk van de Hyper-V-hosts.
**Hyper-V-host**: als de naam van de Hyper-V-host is gegeven, wordt met het hulpprogramma eerst gecontroleerd of deze tot een cluster behoort. Als dit het geval is, worden de knooppunten opgehaald die tot het cluster behoren. Vervolgens worden de virtuele machines van elke Hyper-V-host opgehaald. 

U kunt er ook voor kiezen om de beschrijvende namen of IP-adressen van de virtuele machines die u wilt profileren, handmatig toe te voegen aan een bestand.

Open het uitvoerbestand in Kladblok en kopieer vervolgens de namen van alle VM’s die u wilt profileren, naar een ander bestand (bijvoorbeeld ProfileVMList.txt). Zet elke VM-naam op een afzonderlijke regel. Dit bestand wordt gebruikt als invoer voor de parameter -VMListFile van het hulpprogramma voor alle overige bewerkingen, zoals profileren, rapporten genereren en het bepalen van de doorvoer.

### <a name="examples"></a>Voorbeelden

#### <a name="store-the-list-of-vms-in-a-file"></a>De lijst met VM’s opslaan in een bestand
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>De lijst met VM’s opslaan op de standaardlocatie (het pad -Directory)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Virtuele Hyper-V-machines profileren
In de profileringsmodus maakt de implementatieplanner verbinding met elk van de Hyper-V hosts om prestatiegegevens te verzamelen over de virtuele machines. 

Profileren heeft geen invloed op de prestaties van de virtuele machines in de productieomgeving omdat er geen rechtstreekse verbinding met de machines wordt gemaakt. Alle prestatiegegevens worden verzameld vanaf de Hyper-V-host.

Er wordt elke 15 seconden een query uitgevoerd voor de Hyper-V-host om te zorgen voor een nauwkeurige profilering. Elke minuut wordt het gemiddelde van de prestatiemetergegevens opgeslagen.

Het hulpprogramma handelt naadloos de VM-migratie af van het ene knooppunt naar een ander in het cluster, en de opslagmigratie binnen een host.

### <a name="getting-the-vm-list-to-profile"></a>Lijst met te profileren VM’s ophalen
Raadpleeg de GetVMList-bewerking om een lijst met te profileren VM’s te maken.

Als u de lijst met te profileren virtuele machines hebt opgesteld, kunt u het hulpprogramma uitvoeren in de profileringsmodus. 

### <a name="command-line-parameters"></a>Opdrachtregelparameters
De volgende tabel bevat verplichte en optionele parameters van het hulpprogramma die u kunt uitvoeren in de profileringsmodus. Het hulpprogramma is gebruikelijk voor scenario's waarbij u overstapt van VMware naar Azure of van Hyper-V naar Azure. Deze parameters zijn van toepassing op Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parameternaam | Beschrijving |
|---|---|
| -Operation | StartProfiling |
| -User | De gebruikersnaam om verbinding te maken met de Hyper-V-host of het Hyper-V-cluster. De gebruiker moet beheerderstoegang hebben.|
| -VMListFile | Het bestand met de lijst met virtuele machines die moeten worden geprofileerd. Het bestandspad kan absoluut of relatief zijn. Voor Hyper-V is dit het uitvoerbestand voor de bewerking GetVMList. Als u de voorbereiding handmatig uitvoert, moet het bestand één servernaam of IP-adres bevatten, gevolgd door de naam van de VM (gescheiden door een \ per regel). De VM-naam die wordt opgegeven in het bestand, moet gelijk zijn aan de VM-naam op de Hyper-V host.<br><br>**Voorbeeld:** VMList.txt bevat de volgende VM’s:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Het aantal minuten dat profilering wordt uitgevoerd. Het minimum is 30 minuten.|
|-NoOfHoursToProfile|Het aantal uur dat profilering wordt uitgevoerd.|
|-NoOfDaysToProfile |Het aantal dagen dat profilering wordt uitgevoerd. Het is raadzaam om de profilering gedurende meer dan 7 dagen uit te voeren. Deze duur zorgt ervoor dat het workloadpatroon in uw omgeving goed wordt aangehouden in de opgegeven periode, zodat er een nauwkeurige aanbeveling kan worden gedaan.|
|-Virtualization|Het type virtualisatie (VMware of Hyper-V).|
|-Directory|(Optioneel) Het UNC-pad of de lokale directory voor het opslaan van de gegevens die tijdens de profilering zijn gegenereerd. Als er geen naam wordt opgegeven, wordt de directory met de naam ProfiledData onder het huidige pad gebruikt als de standaarddirectory.|
|-Password|(Optioneel) Het wachtwoord om verbinding te maken met de Hyper-V-host. Als u dit niet als een parameter opgeeft, wordt u om dit wachtwoord gevraagd wanneer u de opdracht uitvoert.|
|-StorageAccountName|(Optioneel) De naam van het opslagaccount dat wordt gebruikt om de bereikbare doorvoer te vinden voor gegevensreplicatie van on-premises naar Azure. Het hulpprogramma uploadt testgegevens naar dit opslagaccount om de doorvoer te berekenen. Het opslagaccount moet v1 (GPv1) voor algemene doeleinden zijn.|
|-StorageAccountKey|(Optioneel) De sleutel die wordt gebruikt voor toegang tot het opslagaccount. Ga naar de Azure Portal > **opslag accounts**  >  *naam van opslag account*  >    >  **toegangs sleutels**  >  **key1** (of de primaire toegangs sleutel voor een klassiek opslag account).|
|-Environment|(Optioneel) Uw doelomgeving voor het Azure Storage-account. Dit kan een van de volgende drie waarden zijn: AzureCloud, AzureUSGovernment of AzureChinaCloud. De standaardwaarde is AzureCloud. Gebruik de para meter wanneer uw doel regio Azure US Government of Azure China 21Vianet is.|

Het is raadzaam om de virtuele machines gedurende meer dan 7 dagen te profileren. Als het verlooppatroon binnen een maand varieert, raden wij u aan de profilering door de week uit te voeren wanneer zich het maximale verloop voordoet. Het beste is om de profilering gedurende 31 dagen uit te voeren, zodat u een betere aanbeveling krijgt. 

Gedurende de periode voor profilering blijft ASRDeploymentPlanner.exe actief. Het hulpprogramma noteert de invoer van de profileringstijd in dagen. Voor het uitvoeren van een snelle test van het hulpprogramma of voor het testen van het concept kunt u gedurende een paar uur of een paar minuten profileren. De minimaal toegestane tijd voor profilering is 30 minuten. 

Tijdens het profileren kunt u eventueel de naam en sleutel van een opslagaccount doorgeven om vast te stellen wat de doorvoer is die Azure Site Recovery kan behalen op het moment van replicatie vanaf de Hyper-V server naar Azure. Als tijdens de profilering geen accountnaam en -sleutel wordt doorgegeven, wordt er geen bereikbare doorvoer berekend.

U kunt meerdere exemplaren van het hulpprogramma uitvoeren voor verschillende sets virtuele machines. Zorg ervoor dat de namen van de virtuele machines niet worden herhaald in een van de profileringssets. Stel dat u, bijvoorbeeld, 10 VM’s hebt geprofileerd (VM1 tot en met VM10). Na een paar dagen wilt u nog 5 VM's profileren (VM11 tot en met VM15). Voor de tweede set VM’s (VM11 tot en met VM15) kunt u het hulpprogramma uitvoeren vanuit een andere opdrachtregelconsole. 

Zorg ervoor dat de tweede set VM’s geen namen bevat uit de eerste reeks geprofileerde VM’s of gebruik voor de tweede set een andere uitvoerdirectory. Als er voor het profileren van dezelfde virtuele machines twee exemplaren van het hulpprogramma worden gebruikt en dezelfde uitvoerdirectory wordt gebruikt, is het gegenereerde rapport niet correct. 

Het hulpprogramma is standaard geconfigureerd om voor maximaal 1000 VM’s profilering uit te voeren en rapporten te genereren. U kunt de limiet wijzigen door de sleutelwaarde MaxVMsSupported in het bestand ASRDeploymentPlanner.exe.config te wijzigen.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Met de standaardinstellingen worden er twee VMList.txt-bestanden gemaakt om (bijvoorbeeld) 1500 VM's te profileren. Het ene bestand heeft 1000 VM’s en het andere heeft 500 VM’s. Voer de twee exemplaren van Azure Site Recovery-implementatieplanner uit: de ene met VMList1.txt en de andere met VMList2.txt. U kunt hetzelfde directorypad gebruiken om de geprofileerde gegevens van beide VMList-VM's op te slaan. 

De bewerking kan mislukken vanwege onvoldoende geheugen, op basis van de hardwareconfiguratie (met name de RAM-hoeveelheid) van de server waarop het hulpprogramma wordt uitgevoerd om het rapport te genereren. Als u goede hardware hebt, kunt u de waarde voor MaxVMsSupported wijzigen naar elke hogere waarde.  

De configuraties van de virtuele machines worden eenmaal aan het begin van de profilering vastgelegd en opgeslagen in een bestand met de naam VMDetailList.xml. Deze informatie wordt gebruikt bij het genereren van het rapport. Eventuele wijzigingen in de configuratie van een virtuele machine (bijvoorbeeld een groter aantal kerngeheugens, schijven of NIC's) vanaf het begin tot het einde van de profilering worden niet vastgelegd. Als de configuratie van een geprofileerde VM tijdens het profileren is gewijzigd, kunt u de volgende tijdelijke oplossing gebruiken om de laatste gegevens van de VM te achterhalen wanneer u het rapport genereert:

* Maak een back-up van VMdetailList.xml en verwijder het bestand van de huidige locatie.
* Geef argumenten voor -User en -Password door wanneer u het rapport genereert.

Met de profileringsopdracht worden meerdere bestanden gegenereerd in de directory voor profilering. Verwijder deze bestanden niet, aangezien dit gevolgen heeft voor het genereren van de rapporten.

### <a name="examples"></a>Voorbeelden

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>VM’s gedurende 30 dagen profileren en de doorvoer van on-premises naar Azure bepalen
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>VM’s gedurende 15 dagen profileren
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>VM’s gedurende 60 minuten profileren om het hulpprogramma kort te testen
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>VM’s gedurende 2 uur profileren om het concept te testen
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Overwegingen voor profileren

Als de server waarop het hulpprogramma wordt uitgevoerd, opnieuw wordt opgestart of is gecrasht, of als u het hulpprogramma afsluit met Ctrl + C, blijven de profileringsgegevens behouden. De kans bestaat echter wel dat de laatste 15 minuten aan geprofileerde gegevens ontbreekt. In dergelijke gevallen voert u het hulpprogramma opnieuw uit in de profileringsmodus nadat de server opnieuw is opgestart.

Wanneer de accountnaam en -sleutel worden doorgegeven, meet het hulpprogramma de doorvoer tijdens de laatste stap van de profilering. Als het hulpprogramma wordt beëindigd voordat de profilering is voltooid, wordt de doorvoer niet berekend. Als u de doorvoer wilt weten voordat het rapport wordt gegenereerd, kunt u de bewerking GetThroughput uitvoeren vanuit de opdrachtregelconsole. Anders bevat het gegenereerde rapport geen informatie over de doorvoer.

Azure Site Recovery biedt geen ondersteuning voor virtuele machines met iSCSI-en passthrough-schijven. Het hulp programma kan iSCSI-en passthrough-schijven die zijn gekoppeld aan Vm's niet detecteren en profileren.

## <a name="generate-a-report"></a>Een rapport genereren
Met het hulpprogramma wordt een Microsoft Excel-bestand met ingeschakelde macro's (XLSM-bestand) gegenereerd als de rapportuitvoer. Dit bestand bevat een overzicht van alle aanbevelingen voor implementatie. Het rapport heeft de naam DeploymentPlannerReport_ *unieke numerieke id*.xlsm en wordt in de opgegeven directory geplaatst.

Nadat de profilering is voltooid, kunt u het hulpprogramma uitvoeren in de modus voor het genereren van een rapport. 

### <a name="command-line-parameters"></a>Opdrachtregelparameters
Hieronder ziet u een tabel met verplichte en optionele parameters van het hulpprogramma die u kunt uitvoeren in de profileringsmodus. Het hulpprogramma wordt vaak gebruikt in scenario's voor overstappen van VMware naar Azure of van Hyper-V naar Azure. De volgende parameters zijn van toepassing op Hyper-V.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Parameternaam | Beschrijving |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | Het bestand met de lijst met geprofileerde VM’s waarvoor het rapport moet worden gegenereerd. Het bestandspad kan absoluut of relatief zijn. Voor Hyper-V is dit het uitvoerbestand voor de bewerking GetVMList. Als u de voorbereiding handmatig uitvoert, moet het bestand één servernaam of IP-adres bevatten, gevolgd door de naam van de VM (gescheiden door een \ per regel). De VM-naam die wordt opgegeven in het bestand, moet gelijk zijn aan de VM-naam op de Hyper-V host.<br><br>**Voorbeeld:** VMList.txt bevat de volgende VM’s:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Het type virtualisatie (VMware of Hyper-V).|
|-Directory|(Optioneel) De UNC-directory of de lokale directory waar de geprofileerde gegevens (bestanden die tijdens de profilering zijn gegenereerd) worden opgeslagen. Deze gegevens zijn vereist voor het genereren van het rapport. Als er geen naam wordt opgegeven, wordt de directory met de naam ProfiledData onder het huidige pad gebruikt als de standaarddirectory.|
| -User | (Optioneel) De gebruikersnaam om verbinding te maken met de Hyper-V-host of het Hyper-V-cluster. De gebruiker moet beheerderstoegang hebben. De gebruikersnaam en het wachtwoord worden gebruikt voor het ophalen van de meest recente configuratiegegevens van de VM’s (zoals het aantal schijven, kernen of NIC's) die in het rapport moeten worden gebruikt. Als deze waarde niet is opgegeven, worden de configuratiegegevens gebruikt die tijdens de profilering zijn verzameld.|
|-Password|(Optioneel) Het wachtwoord om verbinding te maken met de Hyper-V-host. Als u dit niet als een parameter opgeeft, wordt u om dit wachtwoord gevraagd wanneer u de opdracht uitvoert.|
| -DesiredRPO | (Optioneel) Het gewenste beoogde herstelpunt (RPO) in minuten. Standaard is dit 15 minuten.|
| -Bandwidth | (Optioneel) De bandbreedte in MB per seconde. Gebruik deze parameter om de RPO te berekenen die kan worden behaald voor de opgegeven bandbreedte. |
| -StartDate | (Optioneel) De begindatum en -tijd in de notatie MM-DD-JJJJ:UU:MM (in 24-uursindeling). Als u StartDate opgeeft, moet u ook EndDate opgeven. Het rapport wordt dan gegenereerd voor de geprofileerde gegevens die zijn verzameld tussen StartDate en EndDate. |
| -EndDate | (Optioneel) De einddatum en -tijd in de notatie MM-DD-JJJJ:UU:MM (in 24 uursindeling). Als u EndDate opgeeft, moet u ook StartDate opgeven. Het rapport wordt dan gegenereerd voor de geprofileerde gegevens die zijn verzameld tussen StartDate en EndDate. |
| -GrowthFactor | (Optioneel) De groeifactor, uitgedrukt als een percentage. De standaardwaarde is 30 procent. |
| -UseManagedDisks | (Optioneel) UseManagedDisks: Ja/Nee. De standaardwaarde is Ja. Het aantal VM’s dat kan worden ondergebracht in een enkel opslagaccount, wordt berekend op basis van het feit of de failover/testfailover van VM’s is uitgevoerd op een beheerde schijf in plaats van op een niet-beheerde schijf. |
|-SubscriptionId |(Optioneel) De abonnement-GUID. Gebruik deze parameter om het kostenramingsrapport te genereren met de meest recente prijzen op basis van uw abonnement, de aanbieding die aan uw abonnement is gekoppeld en de Azure-doelregio in de opgegeven valuta.|
|-TargetRegion|(Optioneel) De Azure-regio waarop de replicatie is gericht. Omdat er voor Azure verschillende kosten per regio worden berekend, moet u deze parameter gebruiken om een rapport te genereren met een specifieke Azure-doelregio. De standaardwaarde is WestUS2 of de laatst gebruikte doelregio. Raadpleeg de lijst met [ondersteunde doelregio's](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Optioneel) De aanbieding is gekoppeld aan het abonnement. De standaardwaarde is MS-AZR-0003P (betalen naar gebruik).|
|-Currency|(Optioneel) De valuta waarin de kosten in het gegenereerde rapport worden weergegeven. De standaardwaarde is Amerikaanse Dollar ($) of de laatst gebruikte valuta. Raadpleeg de lijst met [ondersteunde valuta's](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

Het hulpprogramma is standaard geconfigureerd om voor maximaal 1000 VM’s profilering uit te voeren en rapporten te genereren. U kunt de limiet wijzigen door de sleutelwaarde MaxVMsSupported in het bestand ASRDeploymentPlanner.exe.config te wijzigen.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Voorbeelden
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Een rapport met standaardwaarden genereren wanneer de geprofileerde gegevens zich op de lokale schijf bevinden
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Een rapport genereren wanneer de geprofileerde gegevens zich op een externe server bevinden
De gebruiker moet lees-/schrijftoegang hebben voor de externe directory.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Een rapport genereren met een specifieke bandbreedte die u voor de replicatie gaat inrichten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Een rapport genereren met een groeifactor van 5% in plaats van de standaardwaarde van 30% 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Een rapport genereren met een subset geprofileerde gegevens
U hebt gedurende 30 dagen gegevens geprofileerd en u wilt een rapport genereren voor slechts 20 van de 30 dagen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Een rapport genereren voor een RPO van 5 minuten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Een rapport genereren voor de Azure-regio India - zuid met de Indiase roepie en een specifieke aanbiedings-id
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Percentielwaarde die voor de berekening wordt gebruikt
Wanneer met het hulpprogramma een rapport wordt gegenereerd, wordt de standaardwaarde van 95% gebruikt voor IOPS-lees-/schrijfbewerkingen, IOPS-schrijfbewerkingen en gegevensverloop. Deze waarden worden verzameld tijdens het profileren van alle VM’s. Deze metrische gegevens zorgen ervoor dat de piek van 100 in de percentielwaarde die vanwege tijdelijke gebeurtenissen kan ontstaan op VM’s, niet wordt gebruikt om het doelopslagaccount en de vereisten voor de bronbandbreedte te bepalen. Voorbeelden van tijdelijke gebeurtenissen zijn het één keer per dag uitvoeren van een back-up, het periodiek indexeren van een database, het genereren van analyserapporten, of andere activiteiten die op een bepaald moment actief zijn.

Het gebruik van een percentielwaarde van 95 biedt een waarheidsgetrouw beeld van de werkelijke workloadkenmerken en verzekert u van optimale prestaties wanneer deze workloads worden uitgevoerd in Azure. We verwachten niet dat u deze waarde hoeft te wijzigen. Als u de waarde toch wijzigt (bijvoorbeeld naar een percentiel van 90), kunt u het configuratiebestand ASRDeploymentPlanner.exe.config in de standaardmap bijwerken en opslaan om een nieuw rapport te genereren op basis van de bestaande geprofileerde gegevens.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Overwegingen met betrekking tot de groeifactor
Het is essentieel dat u bij de workloadkenmerken rekening houdt met groei (mogelijke toename in gebruik na verloop van tijd). Als de beveiliging eenmaal is ingeschakeld en uw workloadkenmerken vervolgens veranderen, is het namelijk niet mogelijk om over te schakelen naar een ander opslagaccount voor beveiliging zonder eerst de beveiliging uit te schakelen en opnieuw in te schakelen.

Stel dat uw virtuele machine op dit moment voldoende heeft aan een Standard Storage-replicatie-account. De kans is groot dat in de volgende drie maanden de volgende wijzigingen optreden:

1. Het aantal gebruikers van de toepassing die wordt uitgevoerd op de virtuele machine neemt toe.
2. Het toegenomen verloop op de VM betekent dat deze moet worden overgezet naar een Premium Storage-account om ervoor te zorgen dat de replicatie met Azure Site Recovery kan worden voortgezet.
3. U moet de beveiliging uitschakelen en vervolgens opnieuw inschakelen voor een Premium Storage-account.

We raden u ten zeerste aan om rekening te houden met groei tijdens het plannen van de implementatie. Hoewel de standaardwaarde 30 procent is, bent u zelf het beste bekend met de verwachte groei en het gebruikspatroon van uw toepassing. U kunt dit aantal naar eigen inzicht wijzigen tijdens het genereren van een rapport. Bovendien kunt u meerdere rapporten genereren met verschillende groeifactoren met dezelfde geprofileerde gegevens. Vervolgens kunt u bepalen welke aanbevelingen voor doelopslag en bronbandbreedte het meest geschikt zijn voor u. 

Het gegenereerde Microsoft Excel-rapport bevat de volgende informatie:

* [Samenvatting van on-premises](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Aanbevelingen](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [Plaatsing van VM-opslag](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Compatibele VM's](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [Niet-compatibele VM's](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Opslagvereiste voor on-premises](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR-batchverwerking](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Kostenraming](hyper-v-deployment-planner-cost-estimation.md)

![Rapport van de implementatieplanner](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Doorvoer bepalen
Als u een schatting wilt maken van de doorvoer die Azure Site Recovery tijdens de replicatie van on-premises naar Azure kan behalen, moet u het hulpprogramma uitvoeren in de GetThroughput-modus. Het hulpprogramma berekent de doorvoer vanaf de server waarop het hulpprogramma wordt uitgevoerd. Deze server is in het ideale geval de Hyper-V-server met de VM's die worden beveiligd. 

### <a name="command-line-parameters"></a>Opdrachtregelparameters 
Open een opdrachtregelconsole en ga naar de map voor de Azure Site Recovery-implementatieplanner. Voer ASRDeploymentPlanner.exe uit met de volgende parameters.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Parameternaam | Beschrijving |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|Het type virtualisatie (VMware of Hyper-V).|
|-Directory|(Optioneel) De UNC-directory of de lokale directory waar de geprofileerde gegevens (bestanden die tijdens de profilering zijn gegenereerd) worden opgeslagen. Deze gegevens zijn vereist voor het genereren van het rapport. Als er geen naam wordt opgegeven, wordt de directory met de naam ProfiledData onder het huidige pad gebruikt als de standaarddirectory.|
| -StorageAccountName | De naam van het opslagaccount dat wordt gebruikt om de bandbreedte te bepalen die wordt gebruikt voor gegevensreplicatie van on-premises naar Azure. Het hulpprogramma uploadt testgegevens naar dit opslagaccount om de gebruikte bandbreedte te bepalen. Het opslagaccount moet v1 (GPv1) voor algemene doeleinden zijn.|
| -StorageAccountKey | De sleutel die wordt gebruikt voor toegang tot het opslagaccount. Ga naar de Azure Portal > **opslag accounts**  >  *naam instellingen van het opslag account*  >    >  **key1 toegangs sleutel**  >  .|
| -VMListFile | Het bestand met de lijst met virtuele machines die moeten worden geprofileerd voor het berekenen van de gebruikte bandbreedte. Het bestandspad kan absoluut of relatief zijn. Voor Hyper-V is dit het uitvoerbestand voor de bewerking GetVMList. Als u de voorbereiding handmatig uitvoert, moet het bestand één servernaam of IP-adres bevatten, gevolgd door de naam van de VM (gescheiden door een \ per regel). De VM-naam die wordt opgegeven in het bestand, moet gelijk zijn aan de VM-naam op de Hyper-V host.<br><br>**Voorbeeld:** VMList.txt bevat de volgende VM’s:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(Optioneel) Uw doelomgeving voor het Azure Storage-account. Dit kan een van de volgende drie waarden zijn: AzureCloud, AzureUSGovernment of AzureChinaCloud. De standaardwaarde is AzureCloud. Gebruik de para meter wanneer uw Azure-doel regio een Azure-Amerikaanse overheid of een Azure-China 21Vianet is.|

### <a name="example"></a>Voorbeeld
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Overwegingen over de doorvoer

Met het hulpprogramma worden in de opgegeven directory verschillende asrvhdfile *aantal*.vhd-bestanden van 64 MB gemaakt (waarbij *aantal* het aantal bestanden is). Deze bestanden worden geüpload naar het opslagaccount om de doorvoer te bepalen. Nadat de doorvoer is gemeten, worden al deze bestanden verwijderd uit het opslagaccount en van de lokale server. Als het hulpprogramma om wat voor reden dan ook wordt beëindigd tijdens het berekenen van de doorvoer, worden de bestanden niet verwijderd uit het opslagaccount of van de lokale server. U moet deze bestanden handmatig verwijderen.

De doorvoer wordt gemeten op een opgegeven tijdstip. Dit is de maximale doorvoer die tijdens de replicatie kan worden behaald met Azure Site Recovery, als alle andere factoren ongewijzigd blijven. Als een bepaalde toepassing bijvoorbeeld op hetzelfde netwerk meer bandbreedte gaat gebruiken, varieert de werkelijke doorvoer tijdens de replicatie. Het resultaat van de gemeten doorvoer verschilt als de GetThroughput-bewerking wordt uitgevoerd terwijl er sprake is van een groot gegevensverloop op de beveiligde virtuele machines. 

Het verdient daarom aanbeveling het hulpprogramma tijdens de profilering op verschillende tijdstippen uit te voeren om vast te stellen welke doorvoersnelheden op verschillende momenten kunnen worden behaald. Het rapport bevat de laatst gemeten doorvoer in het hulpprogramma.

> [!NOTE]
> Voer het hulpprogramma uit op een server die dezelfde opslag- en CPU-kenmerken heeft als een Hyper-V-server.

Voor replicatie moet u de aanbevolen bandbreedte gebruiken om de RPO 100 procent van de tijd te halen. Als u na het instellen van de juiste bandbreedte geen toename ziet in de gerapporteerde behaalde doorvoer, doet u het volgende:

1. Controleer of de doorvoer van Azure Site Recovery wordt beperkt vanwege een probleem met de netwerk-QoS (Quality of Service).
2. Controleer of de kluis van Azure Site Recovery zich in de dichtstbijzijnde fysieke ondersteunde Microsoft Azure-regio bevindt voor minimale netwerklatentie.
3. Controleer de kenmerken van de lokale opslag om te bepalen of u de hardware kunt verbeteren (bijvoorbeeld SSD in plaats van HDD).

    
## <a name="next-steps"></a>Volgende stappen
* [Het gegenereerde rapport analyseren](hyper-v-deployment-planner-analyze-report.md)
