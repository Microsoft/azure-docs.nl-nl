---
title: Voer de Deployment Planner voor nood herstel van VMware met Azure Site Recovery uit
description: In dit artikel wordt beschreven hoe u Azure Site Recovery Deployment Planner uitvoert voor VMware-herstel na nood gevallen naar Azure.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: ramamill
ms.openlocfilehash: 05d260de726c62c130a58938c2a2c9fa2440a96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004718"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>De Deployment Planner voor nood herstel van VMware uitvoeren
Dit artikel is de gebruikershandleiding voor de Azure Site Recovery-implementatieplanner voor productie-installaties van het type VMware-naar-Azure.


## <a name="modes-of-running-deployment-planner"></a>Modi waarin de implementatieplanner kan worden uitgevoerd
Het opdrachtregelprogramma (ASRDeploymentPlanner.exe) kunt u uitvoeren in een van de volgende drie modi:

1.  [Profile ring](#profile-vmware-vms)
2.  [Genereren van rapporten](#generate-report)
3.  [Doorvoer bepalen](#get-throughput)

Voer het hulpprogramma eerst uit in de profileringsmodus om gegevensverloop en IOPS te verzamelen voor de virtuele machine. Voer vervolgens het hulpprogramma uit om het rapport te genereren en de netwerkbandbreedte, opslagvereisten en DR-kosten te bepalen.

## <a name="profile-vmware-vms"></a>Virtuele VMware-machines profileren
In de profileringsmodus maakt de implementatieplanner verbinding met de vCenter-server of de vSphere ESXi-host om prestatiegegevens te verzamelen over de virtuele machine.

* Profileren heeft geen invloed op de prestaties van de virtuele machines in de productieomgeving omdat er geen rechtstreekse verbinding met de machines wordt gemaakt. Alle prestatiegegevens worden verzameld van de vCenter-server of vSphere ESXi-host.
* Om ervoor te zorgen dat het profileren een minimaal effect heeft op de server, wordt de vCenter-server of vSphere ESXi-host slechts om de 15 minuten bevraagd. Dit heeft geen gevolgen voor de nauwkeurigheid van de profilering omdat het hulpprogramma alle prestatiemeteritemgegevens per minuut opslaat.

### <a name="create-a-list-of-vms-to-profile"></a>Een lijst maken met de virtuele machines die u wilt profileren
U moet eerst een lijst maken met de virtuele machines die u wilt profileren. U kunt alle namen van virtuele machines op een vCenter-server of vSphere ESXi-host opvragen met behulp van de opdrachten van VMware vSphere PowerCLI in de volgende procedure. Een andere manier is om de beschrijvende namen of IP-adressen van de virtuele machines die u wilt profileren handmatig toe te voegen aan een bestand.

1. Meld u aan bij de virtuele machine waarop VMware vSphere PowerCLI is geïnstalleerd in.
2. Open de console van VMware vSphere PowerCLI.
3. Zorg ervoor dat het uitvoeringsbeleid is ingeschakeld voor het script. Als het beleid is uitgeschakeld, start u de VMware vSphere PowerCLI-console in de beheerdersmodus en voert u de volgende opdracht uit om het beleid in te schakelen:

    ```powershell
    Set-ExecutionPolicy –ExecutionPolicy AllSigned
    ```

4. U kunt eventueel de volgende opdracht uitvoeren als Connect-VIServer niet wordt herkend als de naam van de cmdlet.

    ```powershell
    Add-PSSnapin VMware.VimAutomation.Core
    ```

5. Als u alle namen wilt ophalen van de virtuele machines op een vCenter-server of vSphere ESXi-host en de lijst wilt opslaan in een TXT-bestand, voert u de volgende twee opdrachten uit.
Vervang &lsaquo;servernaam&rsaquo;, &lsaquo;gebruikersnaam&rsaquo;, &lsaquo;wachtwoord&rsaquo;, &lsaquo;outputfile.txt&rsaquo;; door uw invoer.

    ```powershell
    Connect-VIServer -Server <server name> -User <user name> -Password <password>

    Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>
    ```

6. Open het uitvoerbestand in Kladblok en kopieer de namen van alle virtuele machines die u wilt profileren naar een ander bestand (bijvoorbeeld ProfileVMList.txt). Zet elke naam op een afzonderlijke regel. Dit bestand gebruikt u als invoer voor de parameter *-VMListFile* van het opdrachtregelprogramma.

    ![Lijst met namen van virtuele machines in de implementatieplanner
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Profileren starten
Als u de lijst met te profileren virtuele machines hebt opgesteld, kunt u het hulpprogramma uitvoeren in de profileringsmodus. Hier volgt een lijst met verplichte en optionele parameters van het hulpprogramma die u in de profileringsmodus kunt uitvoeren.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parameternaam | Beschrijving |
|---|---|
| -Operation | StartProfiling |
| -Server | De FQDN-naam of het IP-adres van de vCenter-server of vSphere ESXi-host waarvan de virtuele machines moeten worden geprofileerd.|
| -User | De naam van de gebruiker die verbinding maakt met de vCenter-server of vSphere ESXi-host. De gebruiker moet ten minste alleen-lezen-toegang hebben.|
| -VMListFile | Het bestand met de lijst met virtuele machines die u wilt profileren. Het bestandspad kan absoluut of relatief zijn. Het bestand moet per regel één naam of IP-adres van een virtuele machine bevatten. De namen van de virtuele machines in het bestand moeten identiek zijn met de namen van de virtuele machine op de vCenter-server of vSphere ESXi-host.<br>Het bestand ProfileVMList bevat bijvoorbeeld de volgende virtuele machines:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|Het aantal minuten dat profilering moet worden uitgevoerd. Minimaal is 30 minuten.|
|-NoOfHoursToProfile|Het aantal uur dat profilering moet worden uitgevoerd.|
| -NoOfDaysToProfile | Het aantal dagen dat profilering moet worden uitgevoerd. Het is raadzaam om profilering langer dan 7 dagen uit te voeren om ervoor te zorgen dat het workloadpatroon in uw omgeving in de opgegeven periode goed wordt waargenomen, zodat er een nauwkeurige aanbeveling kan worden gedaan. |
|-Virtualization|Geef het type virtualisatie op (VMware of Hyper-V).|
| -Directory | (Optioneel) Het UNC-pad (Universal Naming Convention) of het pad naar de lokale directory voor het opslaan van gegevens die tijdens de profilering worden gegenereerd. Als u geen directorynaam opgeeft, wordt de directory met de naam 'ProfiledData' onder het huidige pad gebruikt als de standaarddirectory. |
| -Password | (Optioneel) Het wachtwoord om verbinding te maken met de vCenter-server of vSphere ESXi-host. Als u nu geen wachtwoord opgeeft, wordt u erom gevraagd wanneer de opdracht wordt uitgevoerd.|
|-Port|(Optioneel) Poortnummer om verbinding te maken met de vCenter-/ESXi-host. De standaardpoort is 443.|
|-Protocol| (Optioneel) Duidt het protocol aan dat 'http' of 'https' is, om verbinding te maken met vCenter. Het standaardprotocol is https.|
| -StorageAccountName | (Optioneel) De naam van het opslagaccount dat wordt gebruikt om de bereikbare doorvoer te vinden voor gegevensreplicatie van on-premises naar Azure. Het hulpprogramma uploadt testgegevens naar dit opslagaccount om de doorvoer te berekenen. Het opslagaccount moet v1 (GPv1) voor algemene doeleinden zijn. |
| -StorageAccountKey | (Optioneel) De sleutel die wordt gebruikt voor toegang tot het opslagaccount. Ga naar Azure Portal > Opslagaccounts > <*naam van opslagaccount*> > Instellingen > Toegangssleutels > Key1. |
| -Environment | (optioneel) Dit is uw doelomgeving voor het Azure Storage-account. Dit kan een van de volgende drie waarden zijn: AzureCloud, AzureUSGovernment, AzureChinaCloud. De standaardwaarde is AzureCloud. Gebruik de para meter wanneer uw Azure-doel regio een Azure-Amerikaanse overheid of een Azure-China 21Vianet is. |


Het is raadzaam om de virtuele machines gedurende meer dan 7 dagen te profileren. Als het verlooppatroon varieert binnen een maand, raden wij u aan de profilering door de week uit te voeren wanneer zich het maximale verloop voordoet. Het beste is het om de profilering gedurende 31 dagen uit te voeren, omdat u dan een betere aanbeveling krijgt. Gedurende de periode voor profilering blijft ASRDeploymentPlanner.exe actief. Het hulpprogramma noteert de invoer van de profileringstijd in dagen. Voor het uitvoeren van een snelle test of concepttest van het hulpprogramma, kunt u een profilering van een paar uur of van een paar minuten uitvoeren. De minimaal toegestane tijd voor profilering is 30 minuten.

Tijdens het profileren kunt u eventueel de naam en sleutel van een opslagaccount doorgeven om vast te stellen wat de doorvoer is die Site Recovery kan behalen op het moment van replicatie vanaf de configuratieserver of processerver naar Azure. Als tijdens de profilering geen accountnaam en -sleutel wordt doorgegeven, wordt er geen bereikbare doorvoer berekend.

U kunt meerdere exemplaren van het hulpprogramma uitvoeren voor verschillende sets virtuele machines. Zorg ervoor dat de namen van de virtuele machines niet worden herhaald in een van de profileringssets. Als u bijvoorbeeld tien virtuele machines hebt geprofileerd (VM1 tot en met VM10) en na een paar dagen vijf andere virtuele machines wilt profileren (VM11 tot en met VM15), kunt u voor de tweede set virtuele machines (VM11 tot en met VM15) het hulpprogramma uitvoeren vanuit een andere opdrachtregelconsole. Zorg er wel voor dat de tweede set virtuele machines geen namen bevat van de eerste reeks geprofileerde virtuele machines of gebruik voor de tweede set een andere uitvoerdirectory. Als er voor het profileren van dezelfde virtuele machines twee exemplaren van het hulpprogramma worden gebruikt en dezelfde uitvoerdirectory wordt gebruikt, is het gegenereerde rapport niet correct.

Het hulp programma is standaard geconfigureerd voor het profiel en genereren van een rapport van Maxi maal 1000 Vm's. U kunt de limiet wijzigen door de sleutelwaarde MaxVMsSupported in het bestand *ASRDeploymentPlanner.exe.config* te wijzigen.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Met de standaardinstellingen worden er twee VMList.txt-bestanden gemaakt om bijvoorbeeld 1500 VM's te profileren. Een met 1000 VM's en een ander met 500 VM's. Voer de twee exemplaren van Azure Site Recovery Deployment Planner uit, een met VMList1.txt en andere met VMList2.txt. U kunt hetzelfde directorypad gebruiken om de geprofileerde gegevens van beide VMList-VM's op te slaan.

Op basis van de hardwareconfiguratie hebben we gezien dat de bewerking kan mislukken vanwege onvoldoende geheugen, met name wat de omvang betreft van het RAM van de server waarop het hulpprogramma wordt uitgevoerd om het rapport te genereren. Als u goede hardware hebt, kunt u de waarde MaxVMsSupported verhogen.  

Als u meerdere vCenter-servers hebt, moet u één exemplaar van ASRDeploymentPlanner uitvoeren voor elke vCenter-profileringsserver.

De configuraties van de virtuele machines worden eenmaal aan het begin van de profilering vastgelegd en opgeslagen in een bestand met de naam VMDetailList.xml. Deze informatie wordt gebruikt bij het genereren van het rapport. Eventuele wijzigingen in de configuratie van een virtuele machine (bijvoorbeeld een groter aantal kerngeheugens, schijven of NIC's) vanaf het begin tot het einde van de profilering worden niet vastgelegd. Als de configuratie van een geprofileerde virtuele machine tijdens het profileren is gewijzigd, kunt u in de openbare preview de volgende oplossing gebruiken om de laatste gegevens van de virtuele machine te achterhalen wanneer u het rapport genereert:

* Maak een back-up van VMdetailList.xml en verwijder het bestand van de huidige locatie.
* Geef argumenten voor -User en -Password door wanneer u het rapport genereert.

Met de profileringsopdracht worden meerdere bestanden gegenereerd in de directory voor profilering. Verwijder deze bestanden niet, aangezien dit gevolgen heeft voor het genereren van de rapporten.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Voorbeeld 1: virtuele machines 30 dagen profileren en de doorvoer van on-premises naar Azure bepalen
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Voorbeeld 2: virtuele machines 15 dagen profileren

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Voorbeeld 3: virtuele machines 60 minuten profileren om het hulpprogramma kort te testen
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Voorbeeld 4: virtuele machines 2 uur profileren om het hulpprogramma kort aan een concepttest te onderwerpen
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Als de server waarop het hulpprogramma wordt uitgevoerd, opnieuw wordt opgestart of is gecrasht, of als u het hulpprogramma afsluit met Ctrl + C, blijven de profileringsgegevens behouden. De kans bestaat echter wel dat de laatste 15 minuten aan geprofileerde gegevens ontbreekt. In dat geval voert u het hulpprogramma opnieuw uit in de profileringsmodus nadat de server opnieuw is opgestart.
>* Wanneer de accountnaam en -sleutel worden doorgegeven, meet het hulpprogramma de doorvoer tijdens de laatste stap van de profilering. Als het hulpprogramma wordt beëindigd voordat de profilering is voltooid, wordt de doorvoer niet berekend. Als u de doorvoer wilt weten voordat het rapport wordt gegenereerd, kunt u de bewerking GetThroughput uitvoeren vanuit de opdrachtregelconsole. Anders wordt bevat het gegenereerde rapport geen informatie over de doorvoer.


## <a name="generate-report"></a>Rapport genereren
Het hulpprogramma genereert een Microsoft Excel-bestand met ingeschakelde macro's (XLSM-bestand) als de rapportuitvoer, met daarin een overzicht van alle aanbevelingen voor de implementatie. Het rapport heeft de naam `DeploymentPlannerReport_<unique numeric identifier>.xlsm` en wordt in de opgegeven directory geplaatst.

>[!NOTE]
>Voor het genereren van het rapport is een Windows-PC of Windows-Server met Excel 2013 of hoger vereist. Het decimaal teken op deze computer moet worden geconfigureerd als '. ' om de kosten ramingen te produceren. Als u ', ' als decimaal teken hebt ingesteld, gaat u naar ' datum-, tijd-of getalnotaties wijzigen ' in het configuratie scherm en gaat u naar ' aanvullende instellingen ' om het decimaal teken te wijzigen in '. '.

Nadat de profilering is voltooid, kunt u het hulpprogramma uitvoeren in de modus voor het genereren van een rapport. Hieronder ziet u een tabel met verplichte en optionele parameters van het hulpprogramma die u kunt uitvoeren in de profileringsmodus.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Parameternaam | Beschrijving |
|-|-|
| -Operation | GenerateReport |
| -Server |  De FQDN-naam of het IP-adres van de vCenter- of vSphere-server (gebruik dezelfde naam of hetzelfde IP-adres als op het moment van profilering) waar de geprofileerde virtuele machines zich bevinden waarvan een rapport wordt gegenereerd. Let op: als u ten tijde van de profilering een vCenter-server hebt gebruikt, kunt u geen vSphere-server gebruiken voor het genereren van rapporten en omgekeerd.|
| -VMListFile | Het bestand met de lijst met geprofileerde virtuele machines waarvoor het rapport moet worden gegenereerd. Het bestandspad kan absoluut of relatief zijn. Het bestand moet per regel één naam of IP-adres van een virtuele machine bevatten. De namen van de virtuele machines die in het bestand zijn opgegeven, moeten gelijk zijn aan de namen van de virtuele machines op de vCenter-server of vSphere ESXi-host en overeenkomen met de namen die tijdens de profilering zijn gebruikt.|
|-Virtualization|Geef het type virtualisatie op (VMware of Hyper-V).|
| -Directory | (Optioneel) De UNC-directory of de lokale directory waar de geprofileerde gegevens (bestanden die tijdens de profilering zijn gegenereerd) worden opgeslagen. Deze gegevens zijn vereist voor het genereren van het rapport. Als u deze parameter niet opgeeft, wordt de directory ProfiledData gebruikt. |
| -GoalToCompleteIR | (Optioneel) Het aantal uren waarin de initiële replicatie van de geprofileerde virtuele machines moet worden voltooid. Het gegenereerde rapport bevat het aantal virtuele machines waarvoor de initiële replicatie in de opgegeven tijd kan worden voltooid. Standaard is dit 72 uur. |
| -User | (Optioneel) De naam van de gebruiker die verbinding maakt met de vCenter-/vSphere-server. De naam wordt gebruikt voor het ophalen van de meest recente configuratiegegevens van de virtuele machines, zoals het aantal schijven, kerngeheugens en NIC's, die in het rapport moeten worden gebruikt. Als u geen naam opgeeft, worden de configuratiegegevens gebruikt die aan het begin van de profilering zijn verzameld. |
| -Password | (Optioneel) Het wachtwoord om verbinding te maken met de vCenter-server of vSphere ESXi-host. Als u hier geen wachtwoord opgeeft, wordt u erom gevraagd wanneer de opdracht wordt uitgevoerd. |
|-Port|(Optioneel) Poortnummer om verbinding te maken met de vCenter-/ESXi-host. De standaardpoort is 443.|
|-Protocol|(Optioneel) Duidt het protocol aan dat 'http' of 'https' is, om verbinding te maken met vCenter. Het standaardprotocol is https.|
| -DesiredRPO | (Optioneel) Het gewenste beoogde herstelpunt (Recovery Point Objective [RPO]) in minuten. Standaard is dit 15 minuten.|
| -Bandwidth | De bandbreedte in Mbps. De parameter voor het berekenen van de RPO die voor de opgegeven bandbreedte kan worden bereikt. |
| -StartDate | (Optioneel) De begindatum en -tijd in de notatie MM-DD-JJJJ:UU:MM (in 24-uursindeling). *Start date* moet worden opgegeven in combi natie met *EndDate*. Het rapport wordt dan gegenereerd voor de geprofileerde gegevens die zijn verzameld tussen StartDate en EndDate. |
| -EndDate | (Optioneel) De einddatum en -tijd in de notatie MM-DD-JJJJ:UU:MM (in 24 uursindeling). *EndDate* moet worden opgegeven, samen met *start date*. Het rapport wordt dan gegenereerd voor de geprofileerde gegevens die zijn verzameld tussen StartDate en EndDate. |
| -GrowthFactor | (Optioneel) De groeifactor, uitgedrukt als een percentage. De standaardwaarde is 30 procent. |
| -UseManagedDisks | (Optioneel) UseManagedDisks - Ja/Nee. Standaard is Ja. Het aantal virtuele machines dat kan worden ondergebracht in een enkel opslagaccount, wordt berekend afgaande op het feit of de Failover/Test-testfailover van virtuele machines is uitgevoerd op een beheerde schijf in plaats van op een niet-beheerde schijf. |
|-SubscriptionId |(Optioneel) De abonnement-GUID. Houd er rekening mee dat deze para meter is vereist wanneer u het rapport met kosten ramingen wilt genereren met de nieuwste prijs op basis van uw abonnement, de aanbieding die aan uw abonnement is gekoppeld en voor uw specifieke Azure-doel regio in de **opgegeven valuta**.|
|-TargetRegion|(Optioneel) De Azure-regio waarop de replicatie is gericht. Omdat er voor Azure verschillende kosten per regio worden berekend, moet u voor het genereren van dit rapport deze parameter met een specifieke Azure-doelregio gebruiken.<br>De standaardwaarde is WestUS2, of de laatst gebruikte doelregio.<br>Raadpleeg de lijst met [ondersteunde doelregio's](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Optioneel) De aanbieding is gekoppeld aan het vermelde abonnement. De standaardwaarde is MS-AZR-0003P (betalen naar gebruik).|
|-Currency|(Optioneel) De valuta waarin de kosten in het gegenereerde rapport worden weergegeven. Standaard is Amerikaanse Dollar ($) of de laatst gebruikte valuta.<br>Raadpleeg de lijst met [ondersteunde valuta's](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Het hulp programma is standaard geconfigureerd voor het profiel en genereren van een rapport van Maxi maal 1000 Vm's. U kunt de limiet wijzigen door de sleutelwaarde MaxVMsSupported in het bestand *ASRDeploymentPlanner.exe.config* te wijzigen.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Voorbeeld 1: een rapport met standaardwaarden genereren wanneer de geprofileerde gegevens zich op de lokale schijf bevinden
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Voorbeeld 2: een rapport genereren wanneer de geprofileerde gegevens zich op een externe server bevinden
De gebruiker moet lees-/schrijftoegang hebben voor de externe directory.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Voorbeeld 3: een rapport genereren met specifieke bandbreedte en het doel IR te voltooien binnen de opgegeven periode
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Voorbeeld 4: een rapport genereren met een groeifactor van 5% in plaats van de standaardwaarde 30%
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Voorbeeld 5: een rapport genereren met een subset geprofileerde gegevens
U hebt gedurende 30 dagen gegevens geprofileerd en u wilt een rapport genereren voor slechts 20 van de 30 dagen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Voorbeeld 6: een rapport genereren voor 5 minuten RPO
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Voorbeeld 7: een rapport genereren voor de Azure-regio India - zuid met de Indiase roepie en een specifieke aanbiedings-ID

Houd er rekening mee dat de abonnements-ID is vereist voor het genereren van een kosten rapport in een specifieke valuta.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Percentielwaarde die voor de berekening wordt gebruikt
**Welke standaardpercentielwaarde met betrekking tot de prestatiegerelateerde metrische gegevens die tijdens de profilering zijn verzameld, wordt tijdens het genereren van het rapport gebruikt?**

Het hulpprogramma wordt standaard ingesteld op de 95e-percentielwaarde van de IOPS voor lezen/schrijven, de IOPS voor schrijven en de gegevensverloop die tijdens het profileren van de virtuele machines zijn verzameld. Deze waarde zorgt ervoor dat een piek (100e-percentielwaarde) die vanwege tijdelijke gebeurtenissen kan ontstaan op virtuele machines, niet wordt gebruikt om de vereisten te bepalen van het doel-opslagaccount en de bron-bandbreedte. Voorbeelden van tijdelijke gebeurtenissen zijn het één keer per dag uitvoeren van een back-up, het periodiek indexeren van een database, het genereren van analyserapporten en andere vergelijkbare activiteiten die op een bepaald moment actief zijn.

Het gebruik van de 95e-percentielwaarde biedt een waarheidsgetrouw beeld van de werkelijke workloadkenmerken en verzekert u van optimale prestaties wanneer deze workloads in Azure worden uitgevoerd. We verwachten niet dat u deze waarde hoeft te wijzigen. Als de waarde toch wilt wijzigen (bijvoorbeeld op het 90e percentiel), kunt u het configuratiebestand *ASRDeploymentPlanner.exe.config* in de standaardmap bijwerken en opslaan om een nieuw rapport te genereren op basis van de bestaande geprofileerde gegevens.
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Overwegingen voor groeifactor
**Waarom moet ik rekening houden met een groeifactor bij het plannen van implementaties?**

Het is essentieel dat u in uw workloadkenmerken rekening houdt met groei (mogelijke toename in gebruik na verloop van tijd). Als de beveiliging eenmaal is ingeschakeld en uw workloadkenmerken vervolgens veranderen, is het namelijk niet mogelijk om over te schakelen naar een ander opslagaccount voor beveiliging zonder eerst de beveiliging uit te schakelen en opnieuw in te schakelen.

Stel dat uw virtuele machine op dit moment voldoende heeft aan een Standard Storage-replicatie-account. De kans is echter groot dat in de volgende drie maanden verschillende wijzigingen optreden:

* Het aantal gebruikers van de toepassing die wordt uitgevoerd op de virtuele machine neemt toe.
* Het toegenomen verloop op de virtuele machine dat hiervan het gevolg is, betekent dat de virtuele machine moet worden overgezet naar een Premium Storage-account om ervoor te zorgen dat Site Recovery de replicatie kan blijven uitvoeren.
* Dit betekent dat u de beveiliging moet uitschakelen en vervolgens opnieuw moet inschakelen met een Premium Storage-account.

We raden het dan ook sterk aan om rekening te houden met groei tijdens het plannen van de implementatie. U kent het gebruikspatroon van uw toepassingen als geen ander en aan de hand daarvan kunt u de verwachte groei inschatten. De standaardwaarde van 30 procent kunt u tijdens het genereren van een rapport aanpassen. U kunt zelfs meerdere rapporten met verschillende groeifactoren genereren met dezelfde geprofileerde gegevens en zo nagaan welke aanbevelingen voor doel-opslagaccount en bron-bandbreedte het beste aansluiten bij uw situatie.

Het gegenereerde Microsoft Excel-rapport bevat de volgende informatie:

* [Samen vatting on-premises](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Aanbevelingen](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM-<->opslag plaatsing](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [Compatibele VM's](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [Niet-compatibele VM's](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Kosten raming](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Doorvoer bepalen

Als u een schatting wilt maken van de doorvoer die Site Recovery tijdens de replicatie van on-premises naar Azure kan behalen, moet u het hulpprogramma uitvoeren in de GetThroughput-modus. Het hulpprogramma berekent de doorvoer vanaf de server waarop het hulpprogramma wordt uitgevoerd. Deze server voldoet bij voorkeur aan de aanbevelingen voor de configuratieserver. Als u al infrastructuuronderdelen van Site Recovery on-premises hebt geïmplementeerd, moet u het hulpprogramma uitvoeren op de configuratieserver.

Open een opdrachtregelconsole en ga naar de map met het hulpprogramma voor de Site Recovery-implementatieplanner. Voer ASRDeploymentPlanner.exe uit met de volgende parameters.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Parameternaam | Beschrijving |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Geef het type virtualisatie op (VMware of Hyper-V).|
| -Directory | (Optioneel) De UNC-directory of de lokale directory waar de geprofileerde gegevens (bestanden die tijdens de profilering zijn gegenereerd) worden opgeslagen. Deze gegevens zijn vereist voor het genereren van het rapport. Als u geen directory opgeeft, wordt de directory 'ProfiledData' gebruikt. |
| -StorageAccountName | De naam van het opslagaccount dat wordt gebruikt om de bandbreedte te bepalen die wordt gebruikt voor gegevensreplicatie van on-premises naar Azure. Het hulpprogramma uploadt testgegevens naar dit opslagaccount om de gebruikte bandbreedte te bepalen. Het opslagaccount moet v1 (GPv1) voor algemene doeleinden zijn.|
| -StorageAccountKey | De sleutel die wordt gebruikt voor toegang tot het opslagaccount. Ga naar Azure Portal > Opslagaccounts > <*naam van het opslagaccount*> > Instellingen > Toegangssleutels > Key1 (of primaire toegangssleutel voor klassieke opslagaccount). |
| -VMListFile | Het bestand met de lijst met virtuele machines die moeten worden geprofileerd voor het berekenen van de gebruikte bandbreedte. Het bestandspad kan absoluut of relatief zijn. Het bestand moet per regel één naam of IP-adres van een virtuele machine bevatten. De namen van de virtuele machines in het bestand moeten identiek zijn met de namen van de virtuele machine op de vCenter-server of vSphere ESXi-host.<br>Het bestand ProfileVMList bevat bijvoorbeeld de volgende virtuele machines:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (optioneel) Dit is uw doelomgeving voor het Azure Storage-account. Dit kan een van de volgende drie waarden zijn: AzureCloud, AzureUSGovernment, AzureChinaCloud. De standaardwaarde is AzureCloud. Gebruik de para meter wanneer uw Azure-doel regio een Azure-Amerikaanse overheid of een Azure-China 21Vianet is. |

Het hulpprogramma maakt in de opgegeven directory verschillende asrvhdfile<#>.vhd-bestanden van 64 MB (# is het volgnummer). Deze bestanden worden geüpload naar het opslagaccount om de doorvoer te bepalen. Nadat de doorvoer is gemeten, worden al deze bestanden verwijderd uit het opslagaccount en van de lokale server. Als het hulpprogramma om wat voor reden dan ook wordt beëindigd tijdens het berekenen van de doorvoer, worden de bestanden niet verwijderd uit het account of van de lokale server. U moet de bestanden dan handmatig verwijderen.

De doorvoer wordt op een gegeven moment gemeten. Dit is de maximale doorvoer die Site Recovery tijdens de replicatie kan behalen, mits alle andere factoren gelijk blijven. Als een bepaalde toepassing bijvoorbeeld op hetzelfde netwerk meer bandbreedte gaat gebruiken, varieert de werkelijke doorvoer tijdens de replicatie. Als u GetThroughput uitvoert vanaf een configuratieserver, is het hulpprogramma niet op de hoogte van beveiligde virtuele machines en actieve replicatie. Het resultaat van de gemeten doorvoer verschilt als de GetThroughput-bewerking wordt uitgevoerd terwijl er sprake is van een groot gegevensverloop op de beveiligde virtuele machines. Het verdient daarom aanbeveling het hulpprogramma tijdens de profilering op verschillende tijdstippen uit te voeren om na te gaan welke doorvoersnelheden op verschillende momenten kunnen worden behaald. Het rapport bevat de laatst gemeten doorvoer in het hulpprogramma.

### <a name="example"></a>Voorbeeld
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Voer het hulpprogramma uit op een server die de dezelfde opslag- en CPU-kenmerken heeft als de configuratieserver.
>
> Voor replicatie moet u de aanbevolen bandbreedte gebruiken om de RPO 100 procent van de tijd te halen. Als u na het instellen van de juiste bandbreedte geen toename ziet in de gerapporteerde behaalde doorvoer, doet u het volgende:
>
>  1. Controleer of er een netwerk-QoS (Quality of Service) is die de doorvoer van Site Recovery beperkt.
>
>  2. Controleer of de kluis van Site Recovery zich in de dichtstbijzijnde fysieke ondersteunde Microsoft Azure-regio bevindt voor minimale netwerklatentie.
>
>  3. Controleer de kenmerken van de lokale opslag om te bepalen of u de hardware kunt verbeteren (bijvoorbeeld SSD in plaats van HDD).
>
>  4. Wijzig de Site Recovery-instellingen op de processerver om [de voor replicatie gebruikte netwerkbandbreedte te vergroten](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Volgende stappen
* [Analyseer het gegenereerde rapport](site-recovery-vmware-deployment-planner-analyze-report.md).
