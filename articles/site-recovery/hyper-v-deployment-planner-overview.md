---
title: Deployment Planner voor nood herstel via Hyper-V met Azure Site Recovery
description: Meer informatie over de Azure Site Recovery Deployment Planner herstel na nood gevallen voor Hyper-V naar Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: mayg
ms.openlocfilehash: e4f1931aab056306ac5e9f9e9ef402ca26ec2d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86528941"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Over de Azure Site Recovery Deployment Planner voor herstel na nood geval voor Hyper-V naar Azure

Dit artikel is de gebruikershandleiding voor de Azure Site Recovery Deployment Planner voor productie-installaties van het type Hyper-V-naar-Azure.

Voordat u een virtuele Hyper-V-machine gaat beveiligen met behulp van Site Recovery, moet u voldoende bandbreedte toewijzen om de gewenste RPO (Recovery Point Objective) te kunnen halen. Wijs voldoende vrije opslagruimte toe aan elk volume van on-premises Hyper-V-opslag.

Daarnaast is het belangrijk dat u het juiste type en aantal opslagaccounts maakt in Azure. U kunt kiezen tussen Standard- of Premium Storage-accounts, waarbij u rekening moet houden met groei van uw productieservers door toegenomen gebruik na verloop van tijd. U bepaalt per virtuele machine het opslagtype, op basis van kenmerken van de werkbelasting (zoals I/O-bewerkingen voor lezen/schrijven per seconde [IOPS] of gegevensverloop) en limieten van Azure Site Recovery. 

De Azure Site Recovery-implementatieplanner is een opdrachtregelprogramma voor zowel Hyper-V naar Azure en VMware naar Azure herstel na noodgevallen. U kunt uw virtuele Hyper-V-machines op meerdere Hyper-V-hosts met dit hulpprogramma op afstand profileren (zonder enige productie-invloed) om inzicht te krijgen in de bandbreedte en in de Azure-opslagvereisten voor succesvolle replicatie en testfailover / failover. U kunt het hulpprogramma uitvoeren zonder dat u on-premises onderdelen van Azure Site Recovery hoeft te installeren. We raden echter aan, als u nauwkeurige bereikte doorvoerresultaten wilt, de planner uit te voeren op een Windows Server met dezelfde hardwareconfiguratie als die van een van de Hyper-V-servers die u gebruikt voor het inschakelen van de noordherstelbeveiliging naar Azure. 

Het hulpprogramma levert de volgende gegevens:

**Evaluatie van de compatibiliteit**

* Beoordeling van geschiktheid van een VM op basis van het aantal schijven, schijfgrootte, IOPS, verloop en een aantal VM-eigenschappen.

**Vereiste netwerkbandbreedte versus RPO-evaluatie**

* De geschatte vereiste bandbreedte in het netwerk voor replicatie van verschillen
* De doorvoer die Azure Site Recovery kan verwerken van on-premises naar Azure
* RPO die kan worden bereikt voor een bepaalde bandbreedte
* Impact op de gewenste RPO als er een lagere bandbreedte beschikbaar is.

    
**Vereisten voor Azure-infrastructuur**

* Het vereiste type opslag (Standard- of Premium Storage-account) voor elke virtuele machine
* Het totale aantal Standard- en Premium Storage-accounts dat moet worden ingericht voor replicatie
* Suggesties voor naamgeving van opslagaccounts op basis van Azure Storage-richtlijnen
* De plaatsing van het opslagaccount voor alle virtuele machines
* Het aantal Azure-kerngeheugens dat moet worden ingericht voor testfailover of failover van het abonnement
* De aanbevolen grootte voor virtuele machines van Azure voor elke on-premises VM

**On-premises infrastructuurvereisten**
* De totale vrije opslagruimte op elk volume van Hyper-V-opslag die is vereist voor de initiële replicatie en replicatie van verschillen om ervoor te zorgen dat de VM-replicatie niet leidt tot ongewenste downtime voor uw productietoepassingen
* Maximale kopieerfrequentie die moet worden ingesteld voor Hyper-V-replicatie

**Hulp bij eerste replicatie via batch** 
* Het aantal batches van VM dat moet worden gebruikt voor beveiliging
* Lijst met VM's in elke batch
* Volgorde waarin elke batch zal worden beveiligd
* Geschatte tijd voor het voltooien van de initiële replicatie van elke batch

**Geschatte kosten DR naar Azure**
* Geschatte totaalkosten DR naar Azure: rekensnelheid, opslag, netwerk en Azure Site Recovery-licentiekosten
* Analyse detailkosten per VM



>[!IMPORTANT]
>
>Gezien de verwachte toename van het gebruik in de loop van de tijd, wordt bij alle hierboven genoemde berekeningen een groeifactor van 30 procent gehanteerd voor kenmerken van de werkbelasting en wordt de 95e-percentielwaarde van alle metrische profileringsgegevens (IOPS voor lezen/schrijven, verloop, enzovoort) gebruikt. Beide elementen (groeifactor en percentielberekening) kunnen worden geconfigureerd. Zie de sectie 'Overwegingen voor groeifactor' voor meer informatie over de groeifactor. Zie de sectie 'Percentielwaarde gebruikt voor de berekening' voor meer informatie over de percentielwaarde.
>

## <a name="support-matrix"></a>Ondersteuningsmatrix

|**Categorieën** | **VMware naar Azure** |**Hyper-V naar Azure**|**Azure naar Azure**|**Hyper-V naar secundaire site**|**VMware naar secundaire site**
--|--|--|--|--|--
Ondersteunde scenario's |Ja|Ja|Nee|Ja*|Nee
Ondersteunde versie | vCenter 6,7, 6,5, 6,0 of 5,5| WindowsServer 2016, Windows Server 2012 R2 | NA |WindowsServer 2016, Windows Server 2012 R2|NA
Ondersteunde configuratie|vCenter, ESXi| Hyper-V-cluster, Hyper-V-host|NA|Hyper-V-cluster, Hyper-V-host|NA|
Aantal servers dat kan worden geprofileerd per exemplaar van de Azure Site Recovery Deployment Planner |Eén (virtuele machines die horen bij een VMware vCenter Server of een ESXi-server kunnen worden geprofileerd op een tijdstip)|Meerdere (VM's op meerdere hosts of hostclusters kunnen tegelijk profiel zijn)| NA |Meerdere (VM's op meerdere hosts of hostclusters kunnen tegelijk profiel zijn)| NA

* Het hulpprogramma is voornamelijk bedoeld voor het Hyper-V naar Azure noodherstelscenario. Voor Hyper-V naar secundaire site noodherstel kan deze alleen worden gebruikt voor begrip van aanbevelingen aan de bronzijde, zoals vereiste netwerkbandbreedte, vrije opslagruimte op elk van de Hyper-V-bronservers en de initiële replicatie van batchnummers en batchdefinities.  De Azure-aanbevelingen en de kosten van het rapport negeren. De bewerking Ophalen doorvoer is ook niet van toepassing voor het Hyper-V naar secundaire site noodherstelscenario.

## <a name="prerequisites"></a>Vereisten
Het hulpprogramma heeft drie belangrijke fasen voor Hyper-V: lijst ophalen met de VM, profilering en generatie rapportage. Er is ook een voerde optie, waarmee alleen doorvoer wordt berekend. De vereisten voor de server waarop de verschillende fasen moeten worden uitgevoerd worden in de volgende tabel weergegeven:

| Serververeiste | Beschrijving |
|---|---|
|VM-lijst ophalen, profileren en meten van doorvoer |<ul><li>Besturingssysteem: Microsoft Windows Server 2016 of Microsoft Windows Server 2012 R2 </li><li>Machineconfiguratie: 8 vCPU's, 16 GB RAM, 300 GB harde schijf</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ Redistributable voor Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Internet toegang tot Azure (*. blob.core.windows.net) van deze server, poort 443<br>[Dit is optioneel. U kunt ervoor kiezen om de beschik bare band breedte tijdens het hand matig genereren van rapporten op te geven.]</li><li>Azure Storage-account</li><li>Beheerderstoegang op de server</li><li>Minimaal 100 GB vrije schijfruimte (uitgaande van 1000 virtuele machines met een gemiddelde van elk drie schijven, geprofileerd voor 30 dagen)</li><li>De virtuele machine waarop u de Azure Site Recovery ontwikkelingsplannertool uitvoert moet worden toegevoegd aan de TrustedHosts-lijst van alle Hyper-V-servers.</li><li>Alle Hyper-V-servers die moeten worden profileeerd, moeten worden toegevoegd aan de TrustedHosts-lijst van de client-VM vanaf waar het hulp programma wordt uitgevoerd. [Meer informatie voor het toevoegen van servers in de lijst TrustedHosts](#steps-to-add-servers-into-trustedhosts-list). </li><li> Het hulpprogramma moet met Beheerdersrechten worden uitgevoerd vanuit PowerShell of de opdrachtregelconsole op de client</ul></ul>|
| Rapporten genereren | Een Windows-pc of Windows-server met Microsoft Excel 2013 of hoger |
| Gebruikersmachtigingen | Administrator-account voor toegang tot het Hyper-V cluster/de Hyper-V-host tijdens weergave van de VM's en profileringsbewerkingen.<br>Alle hosts die moeten worden geprofileerd dienen een administrator-account te hebben voor het account met dezelfde referenties, dat wil zeggen gebruikersnaam en wachtwoord
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Meer informatie voor het toevoegen van servers in de lijst TrustedHosts
1. De virtuele machine vanaf waar het hulpprogramma moet worden geïmplementeerd, dient alle hosts te bevatten in de lijst met TrustedHosts. Om de client toe te voegen aan de lijst met Trustedhosts voert u de volgende opdracht uit vanaf een hogergelegen PowerShell op de virtuele machine. De virtuele machine is een Windows Server 2012 R2 of Windows Server 2016. 

   ```powershell
   set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
   ```
1. Elke Hyper-V-Host die moet worden geprofileerd dient te beschikken over:

    a. De virtuele machine waarop het hulpprogramma moet worden uitgevoerd in de lijst met TrustedHosts. Voer de volgende opdracht uit vanuit een PowerShell-sessie met verhoogde bevoegdheden op de Hyper-V-host.

      ```powershell
      set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
      ```

    b. Externe communicatie van PowerShell is ingeschakeld.

      ```powershell
      Enable-PSRemoting -Force
      ```

## <a name="download-and-extract-the-deployment-planner-tool"></a>Downloaden en uitpakken van de tool voor implementatieplanning

1.  Down load de nieuwste versie van de [Azure site Recovery Deployment planner](https://aka.ms/asr-deployment-planner).
Het hulpprogramma bevindt zich in een gecomprimeerde map. Hetzelfde hulpprogramma ondersteunt zowel VMware naar Azure als Hyper-V naar Azure herstel na noodgevallen. U kunt dit hulpprogramma ook gebruiken voor een Hyper-V-naar secundaire site noodherstelscenario maar negeer dan de infrastructuuradviezen voor Azure uit het rapport.

1.  Kopieer de ZIP-map naar de Windows-server waarop u het hulpprogramma wilt uitvoeren. U kunt het hulpprogramma uitvoeren op een Windows Server 2012 R2 of Windows Server 2016. De server moet toegang tot het netwerk hebben om verbinding te kunnen maken met het Hyper-V-cluster of de Hyper-V-host met de virtuele machines die moeten worden geprofileerd. We raden dezelfde hardwareconfiguratie op de virtuele machine waar het hulpprogramma wordt uitgevoerd, als op die van de Hyper-V-server, die u wilt beveiligen. Een dergelijke configuratie zorgt ervoor dat de behaalde doorvoer die het hulpprogramma aangeeft, overeenkomt met de werkelijke doorvoer die Azure Site Recovery kan bereiken tijdens replicatie. De berekening van de doorvoer is afhankelijk van de beschikbare netwerkbandbreedte op de server en de hardwareconfiguratie van de server (CPU, opslag, enzovoort). De doorvoer wordt berekend door de server waarop het hulpprogramma wordt uitgevoerd naar Azure. Aangezien de hardwareconfiguratie van de server kan verschillen van die van de configuratieserver, is het bovendien mogelijk dat de berekende doorvoer onjuist is.
De aanbevolen configuratie van de virtuele machine: 8 Vcpu, 16 GB RAM-geheugen, 300 GB harde schijf.

1.  Pak de gecomprimeerde map uit.
De map bevat meerdere bestanden en submappen. Het uitvoerbare bestand is ASRDeploymentPlanner.exe in de map op het hoogste niveau.

Voorbeeld: kopieer het ZIP-bestand naar station E:\ en pak het uit. E:\ASR-implementatie Planner_v2.3.zip

E:\ASR-implementatie Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Bijwerken naar de nieuwste versie van de implementatieplanner

De meest recente updates worden in de geschiedenis van de Deployment Planner- [versie](site-recovery-deployment-planner-history.md)beschreven.

Als u een eerdere versie van de implementatieplanner hebt, doe dan het volgende:
 * Als de nieuwste versie geen bijgewerkte profileringsvoorziening bevat en er al een profilering wordt uitgevoerd in uw huidige versie van de planner, laat u de profilering gewoon doorgaan.
 * Als de nieuwste versie wel een bijgewerkte profileringsvoorziening bevat, adviseren we om de profilering met de huidige versie te stoppen en opnieuw te starten met de nieuwe versie.


  >[!NOTE]
  >
  >Wanneer u gaat profileren met de nieuwe versie, geef dan dezelfde uitvoerdirectory op zodat het hulpprogramma profielgegevens kan toevoegen aan de bestaande bestanden. Er wordt een volledige set geprofileerde gegevens gebruikt om het rapport te genereren. Als u een andere uitvoerdirectory opgeeft, worden er nieuwe bestanden gemaakt en worden oude profileringsgegevens niet gebruikt bij het genereren van het rapport.
  >
  >Elke nieuwe implementatieplanner is een cumulatieve update van het ZIP-bestand. U hoeft dus niet de nieuwste bestanden naar de vorige map te kopiëren. U kunt een nieuwe map maken en deze gebruiken.

## <a name="version-history"></a>Versiegeschiedenis
De nieuwste versie van Azure Site Recovery Deployment Planner tool is 2,5.
Raadpleeg de pagina met [Azure site Recovery Deployment planner versie geschiedenis](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) voor de correcties die in elke update worden toegevoegd.


## <a name="next-steps"></a>Volgende stappen
* [Voer de implementatie planner uit](./hyper-v-deployment-planner-run.md).
