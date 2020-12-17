---
title: Diagnostische gegevens over prestaties voor virtuele machines van Azure | Microsoft Docs
description: Introduceert Azure-prestatie diagnostiek voor Windows.
services: virtual-machines-windows
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 70d1a52ff3326fcd3a628cc7f0f9d91edc9274e7
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655387"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostische gegevens over prestaties voor virtuele Azure-machines

Het hulp programma prestatie diagnostiek helpt u bij het oplossen van prestatie problemen die van invloed kunnen zijn op een virtuele Windows-of Linux-machine (VM). Ondersteunde scenario's voor het oplossen van problemen zijn onder andere snelle controles van bekende problemen en aanbevolen procedures, en complexe problemen die de prestaties van een trage virtuele machine of een hoog gebruik van CPU, schijf ruimte of geheugen vereisen.

U kunt prestatie diagnostiek rechtstreeks vanuit de Azure Portal uitvoeren, waar u ook inzichten en een rapport kunt bekijken over de verschillende logboeken, uitgebreide configuratie en diagnostische gegevens. U wordt aangeraden prestatie diagnostiek uit te voeren en de inzichten en diagnostische gegevens te bekijken voordat u contact opneemt met Microsoft Ondersteuning.

> [!NOTE]
> Voor Windows wordt prestatie diagnostiek op dit moment ondersteund op Vm's waarop .NET SDK versie 4,5 of hoger is geïnstalleerd. Voor de stappen voor het uitvoeren van prestatie diagnostiek op klassieke Vm's raadpleegt u [Azure performance diagnostische VM-extensie](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

### <a name="windows"></a>Windows

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8.1
* Windows 8

### <a name="linux"></a>Linux

- De volgende distributies worden momenteel ondersteund:

    | Distributie               | Versie                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux server        | 6,10 [ `*` ], 7,3, 7,5, 7,6, 7,7, 7,8 |
    | CentOS                     | 6,5 [ `*` ], 7,6, 7,7, 7,8                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [ `*` ], 8,1, 8,2                               |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ], 12 SP5 [ `*` ], 15 [ `*` ], 15 SP1 [ `*` ], 15 SP2 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Raadpleeg [bekende problemen](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Prestatie diagnostiek op uw virtuele machine installeren en uitvoeren

Met diagnostische gegevens over prestaties wordt een VM-extensie geïnstalleerd waarmee een diagnostisch hulp programma wordt uitgevoerd met de naam PerfInsights. PerfInsights is beschikbaar voor [Windows](./how-to-use-perfinsights.md) en [Linux](./how-to-use-perfinsights-linux.md). Voer de volgende stappen uit om prestatie diagnostiek te installeren en uit te voeren:

1. Selecteer **virtuele machines** in de linkerkolom van de opdrachten.
1. Selecteer in de lijst met VM-namen de virtuele machine waarvoor u Diagnostische gegevens wilt uitvoeren.
1. Selecteer in de rechter kolom met opdrachten **prestatie diagnostiek**.

    ![Scherm opname van Azure Portal, met de knop diagnostische gegevens over de installatie gemarkeerd](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > In deze scherm afbeelding is de Blade met de naam van de virtuele machine verborgen.
1. Een opslag account selecteren (optioneel)

    Als u een enkel opslag account wilt gebruiken om de resultaten van de diagnostische gegevens over meerdere Vm's op te slaan, kunt u een opslag account selecteren door te klikken op de knop **instellingen** op de werk balk. Klik op de knop **OK** zodra u het opslag account hebt geselecteerd.

    Als u geen opslag account opgeeft, wordt standaard een nieuw opslag account gemaakt.

    ![Scherm opname van de Blade diagnostische gegevens over prestaties, met de werkbalk knop instellingen gemarkeerd](media/performance-diagnostics/settings-button.png)

    ![Scherm afbeelding van selectie van opslag account uit de Blade instellingen voor diagnostische gegevens](media/performance-diagnostics/select-storage-account.png)

1. Selecteer de knop **prestatie diagnostiek installeren** .
1. Schakel het selectie vakje **Diagnostische gegevens uitvoeren** in als u een diagnose wilt uitvoeren nadat de installatie is voltooid. Als u deze selectie maakt, kunt u het analyse scenario voor prestaties en de bijbehorende opties kiezen.

    ![Scherm afbeelding van de knop installatie van de diagnostische gegevens](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Een analyse scenario selecteren dat moet worden uitgevoerd

De volgende analyse scenario's zijn beschikbaar via de Azure Portal. Selecteer een analyse, afhankelijk van het prestatie probleem dat u hebt. Selecteer de duur en tracerings opties die nodig zijn voor de analyse.

* **Analyse van snelle prestaties**  
    Hiermee wordt gecontroleerd op bekende problemen, worden aanbevolen procedures geanalyseerd en worden diagnostische gegevens verzameld. Het uitvoeren van deze analyse duurt enkele minuten. Meer informatie over [Windows](./how-to-use-perfinsights.md) of [Linux](./how-to-use-perfinsights-linux.md)

* **Analyse van prestaties**  
    Omvat alle controles in de analyse van snelle prestaties en bewaken het gebruik van hoge bronnen. Gebruik deze versie om algemene prestatie problemen op te lossen, zoals hoge CPU, geheugen en schijf gebruik. Deze analyse duurt 30 seconden tot 15 minuten, afhankelijk van de geselecteerde duur. Meer informatie over [Windows](./how-to-use-perfinsights.md) of [Linux](./how-to-use-perfinsights-linux.md)

* **Geavanceerde prestatie analyse**`*`  
    Omvat alle controles in de analyse van prestaties en verzamelt een of meer van de traceringen, zoals vermeld in de volgende secties. Gebruik dit scenario om ingewikkelde problemen op te lossen waarvoor extra traceringen zijn vereist. Als u dit scenario voor langere Peri Oden uitvoert, wordt de totale grootte van de diagnostische uitvoer verg root, afhankelijk van de grootte van de virtuele machine en de geselecteerde tracerings opties. Het uitvoeren van deze analyse duurt 30 seconden tot 15 minuten, afhankelijk van de geselecteerde duur. [Meer informatie](./how-to-use-perfinsights.md)

* **Azure files analyse**`*`  
    Omvat alle controles in de prestatie analyse en legt een netwerk tracering en SMB-teller vast. Gebruik dit scenario om de prestaties van Azure files op te lossen. Het uitvoeren van deze analyse duurt 30 seconden tot 15 minuten, afhankelijk van de geselecteerde duur. [Meer informatie](./how-to-use-perfinsights.md)

>[!Note]
>[ `*` ] Deze analyse scenario's worden alleen ondersteund in Windows.

![Scherm opname van het deel venster diagnostische gegevens uitvoeren in de Blade prestatie diagnostiek](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Symptomen geven (optioneel)

Selecteer een of meer van de geselecteerde symptomen in de lijst of Voeg nieuwe symptomen toe. Dit helpt ons de analyse in de toekomst te verbeteren.

### <a name="provide-support-request-number-if-available-optional"></a>Ondersteunings aanvraag nummer opgeven, indien beschikbaar (optioneel)

Als u werkt met een ondersteunings technicus van micro soft in een bestaand ondersteunings ticket, geeft u het nummer van het ondersteunings ticket op.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Controleer het privacybeleid en de juridische voor waarden en schakel het selectie vakje in om te bevestigen (vereist)

Als u de diagnostische gegevens wilt uitvoeren, moet u akkoord gaan met de juridische voor waarden en het privacybeleid accepteren.

### <a name="select-ok-to-run-the-diagnostics"></a>Selecteer OK om de diagnostische gegevens uit te voeren

Er wordt een melding weer gegeven wanneer prestatie diagnostiek wordt geïnstalleerd. Nadat de installatie is voltooid, ziet u een melding die aangeeft dat de installatie is geslaagd. De geselecteerde analyse wordt vervolgens uitgevoerd voor de opgegeven duur. Dit is een goed moment om het prestatie probleem te reproduceren zodat de diagnostische gegevens op het juiste moment kunnen worden vastgelegd.

Nadat de analyse is voltooid, worden de volgende items geüpload naar Azure-tabellen en een BLOB-container (binary large object) in het opgegeven opslag account:

* Alle inzichten en gerelateerde informatie over de uitvoering
* Een gecomprimeerd zip-bestand (met de naam **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip** ) in Windows en een tar balle-bestand (met de naam **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-SS-FFF. tar. gz** ) op Linux dat logboek bestanden bevat
* Een HTML-rapport

Na het uploaden wordt een nieuw rapport met diagnostische gegevens weer gegeven in de Azure Portal.

![Scherm afbeelding van een lijst met diagnostische gegevens in de Blade diagnostische gegevens over prestaties](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Instellingen voor diagnostische gegevens over prestaties wijzigen

Gebruik de knop **instellingen** werk balk om het opslag account te wijzigen waar de diagnostische gegevens en uitvoer kunnen worden opgeslagen. U kunt hetzelfde opslag account gebruiken voor meerdere Vm's die gebruikmaken van prestatie diagnostiek. Wanneer u het opslag account wijzigt, worden de oude rapporten en inzichten niet verwijderd. Ze worden echter niet meer weer gegeven in de lijst met diagnostische rapporten.

## <a name="review-insights-and-performance-diagnostics-report"></a>Rapport met diagnostische gegevens over inzichten en prestaties bekijken

Elke diagnostische uitvoering bevat een lijst met inzichten en aanbevelingen, betrokken resources, logboek bestanden en andere uitgebreide diagnostische gegevens die worden verzameld, plus een rapport voor offline weer gave. Voor een volledige lijst met alle verzamelde diagnostische gegevens, Zie **wat voor soort informatie wordt verzameld door PerfInsights?** op [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) of [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Een rapport voor prestatie diagnostiek selecteren

U kunt de lijst met diagnostische rapporten gebruiken om alle diagnostische rapporten te vinden die zijn uitgevoerd. De lijst bevat details over de gebruikte analyse, inzichten die zijn gevonden en hun impact niveaus. Selecteer een rij om meer details weer te geven.

![Scherm opname van het selecteren van een diagnose rapport op de Blade diagnostische gegevens over prestaties](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Een rapport over prestatie diagnostiek bekijken

Elk rapport met diagnostische gegevens over prestaties kan verschillende inzichten bevatten en duiden op een impact niveau van hoog, gemiddeld of laag. Elk inzicht bevat ook aanbevelingen waarmee u het probleem kunt voor komen. Inzichten worden gegroepeerd op eenvoudige filters.

Impact niveaus vertegenwoordigen het potentieel voor prestatie problemen, op basis van factoren zoals een onjuiste configuratie, bekende problemen of problemen die door andere gebruikers worden gerapporteerd. Mogelijk hebt u nog niet een of meer van de vermelde problemen ondervonden. U kunt bijvoorbeeld SQL-logboek bestanden en database bestanden op dezelfde gegevens schijf hebben. Deze voor waarde heeft een hoge mate van knel punten en andere prestatie problemen als het database gebruik hoog is, terwijl u mogelijk geen problemen ondervindt als het gebruik laag is.

![Scherm afbeelding van rapport overzicht van prestatie controle](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Inzichten en aanbevelingen voor prestatie diagnostiek controleren

U kunt een inzicht selecteren om meer informatie weer te geven over de betrokken resources, aanbevolen oplossingen en referentie koppelingen.

![Scherm opname van Details over prestatie diagnose](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Het rapport met volledige prestatie diagnostiek downloaden en bekijken

U kunt de knop **rapport downloaden** gebruiken om een HTML-rapport met aanvullende informatie over uitgebreide diagnostische gegevens te downloaden, zoals opslag-en netwerk configuratie, prestatie meter items, traceringen, lijst met processen en Logboeken. De inhoud is afhankelijk van de geselecteerde analyse. Voor geavanceerde probleem oplossing kan het rapport aanvullende informatie en interactieve grafieken bevatten die gerelateerd zijn aan een hoog CPU-gebruik, een hoog schijf gebruik en processen die buitensporig veel geheugen gebruiken. Zie [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) of [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report)voor meer informatie over het rapport prestatie diagnostiek.

## <a name="manage-performance-diagnostics-reports"></a>Rapporten met diagnostische gegevens over prestaties beheren

U kunt een of meer rapporten met diagnostische gegevens over prestaties verwijderen met behulp van de knop **rapport verwijderen** .

## <a name="how-to-uninstall-performance-diagnostics"></a>Prestatie diagnostiek verwijderen

U kunt prestatie diagnostiek verwijderen van een virtuele machine. Met deze actie wordt de VM-extensie verwijderd, maar heeft dit geen invloed op de diagnostische gegevens in het opslag account.

![Scherm afbeelding van de werk balk voor de Blade prestatie diagnostiek met de knop verwijderen gemarkeerd](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Waar worden de diagnostische gegevens van mijn virtuele machine opgeslagen

Alle prestatie diagnostiek en rapporten worden opgeslagen in uw eigen opslag account. Inzichten worden opgeslagen in azure-tabellen. Het gecomprimeerde bestand met rapporten wordt opgeslagen in een BLOB-container (binary large object) met de naam azdiagextnresults.

U kunt de gegevens van het opslag account weer geven met behulp van de knop instellingen op de werk balk.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Hoe kan ik deze gegevens delen met de klanten ondersteuning van micro soft

Er zijn meerdere manieren om het diagnostische rapport te delen met micro soft.

**Optie 1:** Het meest recente rapport automatisch delen  
Wanneer u een ondersteunings ticket met micro soft opent, is het belang rijk om het rapport over prestatie diagnostiek te delen. Als u ervoor hebt gekozen deze informatie met micro soft te delen terwijl u de diagnostische gegevens uitvoert (door het selectie vakje **Ik ga akkoord met de diagnostische informatie te delen met micro** Soft), is micro soft in staat om met een SAS-koppeling naar het uitvoer zip-bestand tot 30 dagen na de uitvoerings datum toegang te krijgen tot het rapport. Alleen het meest recente rapport is beschikbaar voor de ondersteunings technicus.

**Optie 2:** Een Shared Access Signature genereren voor het gecomprimeerde bestand van het diagnostische rapport  
U kunt een koppeling naar het gecomprimeerde bestand in rapporten delen met behulp van hand tekeningen voor gedeelde toegang. Voer de volgende stappen uit om dit te doen:

1. Blader in het Azure Portal naar het opslag account waarin de diagnostische gegevens zijn opgeslagen.
1. Selecteer **blobs** onder het gedeelte **BLOB service** .
1. Selecteer de **azdiagextnresults** -container.
1. Selecteer het gecomprimeerde uitvoer bestand voor prestatie diagnostiek dat u wilt delen.
1. Op het tabblad **SAS genereren** selecteert u de criteria voor delen.
1. Klik op **genereren BLOB SAS-token en URL**.
1. Kopieer de **URL** van de BLOB-SAS en deel deze met de ondersteunings technicus.

**Optie 3:** Het rapport downloaden van het opslag account

U kunt ook het gecomprimeerde bestand voor diagnostische gegevens over prestaties vinden met behulp van stap 1 – 4 in optie 2. Selecteer om het bestand te downloaden en deel het vervolgens via e-mail of vraag de ondersteunings technicus om het bestand te uploaden.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Hoe kan ik de diagnostische gegevens op het juiste moment vastleggen

Elke uitvoering van de prestatie diagnostiek heeft twee fasen:

1. De VM-extensie voor diagnostische gegevens over prestaties installeren of bijwerken.
1. Voer de diagnostische gegevens uit voor de opgegeven duur.

Momenteel is er geen eenvoudige manier om precies te weten wanneer de installatie van de VM-extensie is voltooid. Over het algemeen duurt het ongeveer 45 seconden tot 1 minuut om de VM-extensie te installeren. Nadat de VM-extensie is geïnstalleerd, kunt u uw reproduceren-stappen uitvoeren om de diagnostische gegevens over de prestaties te laten vastleggen voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

Als u de diagnostische gegevens en het rapport over prestaties hebt gecontroleerd en u de oorzaak van het probleem nog steeds niet kunt vaststellen en meer hulp nodig hebt, kunt u een ondersteunings ticket openen met klant ondersteuning van micro soft.

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
