---
title: Azure Site Recovery Deployment Planner voor nood herstel van VMware
description: Meer informatie over de Azure Site Recovery Deployment Planner voor nood herstel van virtuele VMware-machines naar Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mayg
ms.openlocfilehash: 4291e8438f70e2e7190cd4dc6c890b5b325f2324
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100360904"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Over de Azure Site Recovery Deployment Planner voor VMware naar Azure
Dit artikel is de gebruikershandleiding voor de Azure Site Recovery Deployment Planner voor productie-installaties van het type VMware-naar-Azure.

## <a name="overview"></a>Overzicht

Voordat u een virtuele VMware-machine gaat beveiligen met behulp van Azure Site Recovery, moet u voldoende bandbreedte toewijzen om de gewenste RPO (Recovery Point Objective) te kunnen halen. Bepaal de bandbreedte op basis van de dagelijkse veranderingssnelheid van gegevens. Zorg ervoor dat on-premises het juiste aantal configuratieservers en processervers wordt geïmplementeerd.

Daarnaast is het belangrijk dat u het juiste type en aantal Azure Storage-accounts maakt. U kunt kiezen tussen Standard- of Premium Storage-accounts, waarbij u rekening moet houden met groei van uw productieservers door toegenomen gebruik na verloop van tijd. U bepaalt per virtuele machine het opslagtype, op basis van kenmerken van de werkbelasting (zoals I/O-bewerkingen voor lezen/schrijven per seconde [IOPS] of gegevensverloop) en limieten van Site Recovery.

 De Site Recovery Deployment Planner is een opdrachtregelprogramma voor herstelscenario's van het type Hyper-V-naar-Azure en VMware-naar-Azure. U kunt uw virtuele VMware-machines met dit hulpprogramma op afstand profileren (zonder enige invloed op de productie) om inzicht te krijgen in de vereisten voor bandbreedte en opslag waaraan moet worden voldaan voor een succesvolle replicatie en testfailover. U kunt het hulpprogramma uitvoeren zonder dat u on-premises onderdelen van Site Recovery hoeft te installeren. Voor nauwkeurige doorvoerresultaten voert u de planner uit op een Windows-server die voldoet aan de minimale vereisten van de Site Recovery-configuratieserver die u uiteindelijk gaat implementeren als een van de eerste stappen van de productie-implementatie.

Het hulpprogramma levert de volgende gegevens:

**Evaluatie van de compatibiliteit**

* Beoordeling van geschiktheid van een VM op basis van het aantal schijven, schijfgrootte, IOPS, verloop, opstarttype (EFI/BIOS) en besturingssysteem

**Vereiste netwerkbandbreedte versus RPO-evaluatie**

* De geschatte vereiste bandbreedte in het netwerk voor replicatie van verschillen
* De doorvoer die Site Recovery kan verwerken van on-premises naar Azure
* Het aantal virtuele machines in een batch, op basis van de geschatte bandbreedte die nodig is om initiële replicatie in een bepaalde tijd te voltooien
* RPO die kan worden bereikt voor een bepaalde bandbreedte
* Impact op de gewenste RPO als er een lagere bandbreedte beschikbaar is

**Vereisten voor Azure-infrastructuur**

* Vereiste opslag type (Standard of Premium Storage) voor elke VM
* Het totale aantal Standard-en Premium Storage-accounts dat moet worden ingesteld voor replicatie (inclusief cache-opslag accounts)
* Suggesties voor naamgeving van opslagaccounts op basis van Storage-richtlijnen
* Het aantal Azure-kerngeheugens dat moet worden ingericht voor testfailover of failover van het abonnement
* De aanbevolen grootte voor virtuele machines van Azure voor elke on-premises VM

**On-premises infrastructuurvereisten**
* Het vereiste aantal configuratieservers en processervers dat on-premises moet worden geïmplementeerd

**Geschatte kosten van herstel na noodgevallen naar Azure**
* Geschatte totaalkosten voor herstel na noodgevallen naar Azure: kosten voor rekensnelheid, opslag, netwerk en Site Recovery-licentie
* Analyse detailkosten per VM


>[!IMPORTANT]
>
>Gezien de verwachte toename van het gebruik in de loop van de tijd, wordt bij alle hierboven genoemde berekeningen een groeifactor van 30 procent gehanteerd voor kenmerken van de werkbelasting. Voor de berekeningen wordt ook de 95e-percentielwaarde van alle metrische profileringsgegevens (IOPS voor lezen/schrijven, verloop enzovoort) gebruikt. Zowel de groeifactor als de percentielberekening kunnen worden geconfigureerd. Zie de sectie 'Overwegingen voor groeifactor' voor meer informatie over de groeifactor. Zie het gedeelte 'Percentielwaarde die voor de berekening wordt gebruikt' voor meer informatie over de percentielwaarde.
>

## <a name="support-matrix"></a>Ondersteuningsmatrix

| **Categorie** | **VMware naar Azure** |**Hyper-V naar Azure**|**Azure naar Azure**|**Hyper-V naar secundaire site**|**VMware naar secundaire site**
--|--|--|--|--|--
Ondersteunde scenario's |Ja|Ja|Nee|Ja*|No
Ondersteunde versie | vCenter 7,0, 6,7, 6,5, 6,0 of 5,5| WindowsServer 2016, Windows Server 2012 R2 | NA |WindowsServer 2016, Windows Server 2012 R2|NA
Ondersteunde configuratie|vCenter, ESXi| Hyper-V-cluster, Hyper-V-host|NA|Hyper-V-cluster, Hyper-V-host|NA|
Aantal servers dat kan worden geprofileerd per actief exemplaar van de Site Recovery Deployment Planner |Eén (virtuele machines die horen bij een VMware vCenter Server of een ESXi-server kunnen worden geprofileerd op een tijdstip)|Meerdere (VM's op meerdere hosts of hostclusters kunnen tegelijk worden geprofileerd)| NA |Meerdere (VM's op meerdere hosts of hostclusters kunnen tegelijk worden geprofileerd)| NA

* Het hulpprogramma is voornamelijk bedoeld voor het Hyper-V naar Azure noodherstelscenario. Voor Hyper-V naar secundaire site noodherstel kan deze alleen worden gebruikt voor begrip van aanbevelingen aan de bronzijde, zoals vereiste netwerkbandbreedte, vrije opslagruimte op elk van de Hyper-V-bronservers en de initiële replicatie van batchnummers en batchdefinities. De Azure-aanbevelingen en de kosten van het rapport negeren. De bewerking Ophalen doorvoer is ook niet van toepassing voor het Hyper-V naar secundaire site noodherstelscenario.

## <a name="prerequisites"></a>Vereisten
Het hulpprogramma heeft twee belangrijke fasen: profileren en rapporten genereren. Er is ook een derde optie, waarmee alleen doorvoer wordt berekend. Raadpleeg de volgende tabel voor de vereisten voor de server waarop de profilering en meting van de doorvoersnelheid worden uitgevoerd.

| Serververeiste | Description|
|---|---|
|Profileren en meten van doorvoer| <ul><li>Besturingssysteem: Windows Server 2016 of Windows Server 2012 R2<br>(bij voorkeur met minimaal de [aanbevolen waarden voor de configuratieserver](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server))</li><li>Machineconfiguratie: 8 vCPU's, 16 GB RAM, 300 GB harde schijf</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Visual C++ Redistributable voor Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Internet toegang tot Azure (*. blob.core.windows.net) van deze server, poort 443<br>[Dit is optioneel. U kunt ervoor kiezen om de beschik bare band breedte tijdens het hand matig genereren van rapporten op te geven.]</li><li>Azure Storage-account</li><li>Beheerderstoegang op de server</li><li>Minimaal 100 GB vrije schijfruimte (uitgaande van 1000 virtuele machines met een gemiddelde van elk drie schijven, geprofileerd voor 30 dagen)</li><li>Instellingen voor het niveau van VMware vCenter-statistieken kunnen 1 of hoger niveau zijn</li><li>VCenter-poort toestaan (standaard 443): met Site Recovery Deployment Planner wordt deze poort gebruikt om verbinding te maken met de vCenter-Server/ESXi-host</ul></ul>|
| Rapporten genereren | Een Windows-PC of Windows-Server met Excel 2013 of hoger.<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Visual C++ Redistributable voor Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>[VMware vSphere PowerCLI 6,0 R3](https://aka.ms/download_powercli) is alleen vereist wanneer u de optie-gebruiker door geven in de opdracht rapport genereren voor het ophalen van de meest recente configuratie gegevens van de virtuele machine van de virtuele machines. De Deployment Planner maakt verbinding met de vCenter-Server. Poort voor vCenter-poort (standaard 443) toestaan verbinding te maken met vCenter Server.</li>|
| Gebruikersmachtigingen | Alleen-lezen-machtiging voor het gebruikersaccount dat tijdens het profileren wordt gebruikt voor toegang tot de VMware vCenter-server of VMware vSphere ESXi-host |

> [!NOTE]
>
>Het hulpprogramma kan alleen worden gebruikt voor het profileren van virtuele machines met VMDK- en RDM-schijven. Profilering van virtuele machines met iSCSI- of NFS-schijven is niet mogelijk. Site Recovery biedt wel ondersteuning voor iSCSI- en NFS-schijven voor VMware-servers. Omdat de Deployment Planner zich niet in de gast bevindt en voor de profilering alleen prestatiemeteritems van vCenter gebruikt, heeft het hulpprogramma geen zichtbaarheid op deze schijftypes.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Downloaden en uitpakken van de tool voor implementatieplanning
1. Download de nieuwste versie van de [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
Het hulpprogramma bevindt zich in een gecomprimeerde map. De huidige versie van het hulpprogramma ondersteunt alleen het VMware Azure-scenario.

2. Kopieer de gecomprimeerde map naar de Windows-server waarop u het hulpprogramma wilt uitvoeren.
U kunt het hulpprogramma uitvoeren vanuit Windows Server 2012 R2 als de server netwerktoegang heeft voor verbinding met de vCenter-server of vSphere ESXi-host met de virtuele machines die u wilt profileren. We raden u echter aan het hulpprogramma uit te voeren op een server waarvan de hardwareconfiguratie voldoet aan de [aanbevelingen voor de configuratieserver](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server). Als u al onderdelen van Site Recovery on-premises hebt geïmplementeerd, moet u het hulpprogramma uitvoeren vanaf de configuratieserver.

    Het is raadzaam om voor de server waarop u het hulpprogramma uitvoert, de hardwareconfiguratie te gebruiken die overeenkomt met de configuratie van de configuratieserver (die een ingebouwde processerver heeft). Een dergelijke configuratie zorgt ervoor dat de behaalde doorvoer die het hulpprogramma aangeeft, overeenkomt met de werkelijke doorvoer die Site Recovery kan bereiken tijdens replicatie. De berekening van de doorvoer is afhankelijk van de beschikbare netwerkbandbreedte op de server en de hardwareconfiguratie van de server (zoals CPU en opslag). Als u het hulpprogramma vanaf een andere server uitvoert, wordt de doorvoer berekend vanaf die server naar Azure. Aangezien de hardwareconfiguratie van de server kan verschillen van die van de configuratieserver, is het bovendien mogelijk dat de berekende doorvoer onjuist is.

3. Pak de gecomprimeerde map uit.
De map bevat meerdere bestanden en submappen. Het uitvoerbare bestand is ASRDeploymentPlanner.exe in de map op het hoogste niveau.

    Voorbeeld: kopieer het ZIP-bestand naar station E:\ en pak het uit.
    E:\ASR-implementatie Planner_v2.3.zip

    E:\ASR-implementatie Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Bijwerken naar de nieuwste versie van de Deployment Planner

De meest recente updates worden in de geschiedenis van de Deployment Planner- [versie](site-recovery-deployment-planner-history.md)beschreven.

Als u een eerdere versie van de Deployment Planner hebt, doe dan het volgende:
 * Als de nieuwste versie geen bijgewerkte profileringsvoorziening bevat en er al een profilering wordt uitgevoerd in uw huidige versie van de planner, laat u de profilering gewoon doorgaan.
 * Als de nieuwste versie wel een bijgewerkte profileringsvoorziening bevat, adviseren we om de profilering met de huidige versie te stoppen en opnieuw te starten met de nieuwe versie.


 >[!NOTE]
 >
 >Wanneer u gaat profileren met de nieuwe versie, geef dan dezelfde uitvoerdirectory op zodat het hulpprogramma profielgegevens kan toevoegen aan de bestaande bestanden. Er wordt een volledige set geprofileerde gegevens gebruikt om het rapport te genereren. Als u een andere uitvoerdirectory opgeeft, worden er nieuwe bestanden gemaakt en worden oude profileringsgegevens niet gebruikt bij het genereren van het rapport.
 >
 >Elke nieuwe versie van de Deployment Planner is een cumulatieve update van het ZIP-bestand. U hoeft dus niet de nieuwste bestanden naar de vorige map te kopiëren. U kunt een nieuwe map maken en deze gebruiken.


## <a name="version-history"></a>Versiegeschiedenis
De meest recente versie van het hulp programma Site Recovery Deployment Planner is 2,5.
Raadpleeg de pagina [Site Recovery Deployment Planner version history](./site-recovery-deployment-planner-history.md) (Versiegeschiedenis van de Site Recovery Deployment Planner) voor de oplossingen die zijn toegevoegd in elke update.

## <a name="next-steps"></a>Volgende stappen
[Site Recovery Deployment Planner uitvoeren](site-recovery-vmware-deployment-planner-run.md)
