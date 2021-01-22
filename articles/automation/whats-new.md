---
title: Wat is er nieuw in Azure Automation
description: Belang rijke updates voor Azure Automation elke maand bijgewerkt.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b412fffdda1ce6a2023a8b9ae459a52986cd93d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690861"
---
# <a name="whats-new-in-azure-automation"></a>Wat is er nieuw in Azure Automation?

Azure Automation worden doorlopend verbeterd. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten

Deze pagina wordt maandelijks bijgewerkt. Ga daarom regel matig opnieuw te werk.

## <a name="january-2021"></a>Januari 2021

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation runbooks verplaatst van het TechNet-Script centrum naar GitHub

**Type:** Plan voor wijziging

Het TechNet-Script centrum wordt buiten gebruik gesteld en alle runbooks die in de Runbook Gallery worden gehost, zijn verplaatst naar onze [Automation github-organisatie](https://github.com/azureautomation). Lees [Azure Automation Runbooks verplaatsen naar github](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)voor meer informatie.

## <a name="december-2020"></a>December 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation en Updatebeheer persoonlijke koppeling GA

**Type:** Nieuwe functie

Azure Automation en Updatebeheer ondersteuning aangekondigd als GA voor Azure Global-en Government-Clouds. Azure Automation ingeschakelde persoonlijke koppelings ondersteuning om de uitvoering van een runbook op een Hybrid worker-rol te beveiligen, met behulp van Updatebeheer om computers te patchen, een runbook aan te roepen via een webhook en een status configuratie service te gebruiken om uw computers te laten overkomen. Lees [Azure Automation ondersteuning voor persoonlijke koppelingen](https://azure.microsoft.com/updates/azure-automation-private-link) voor meer informatie

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation geclassificeerd als Grade-C gecertificeerd voor toegankelijkheid

**Type:** Nieuwe functie

Toegankelijkheids functies van micro soft-producten helpen instanties voor algemene toegankelijkheids vereisten. Zoek op de pagina [blog aankondiging](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) naar **Azure Automation** om het rapport met de toegankelijkheids conformiteit voor de Automation-Service te lezen.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Ondersteuning voor Automation en status configuratie GA in UAE-noord

**Type:** Nieuwe functie

Het Automation-account en de status configuratie Beschik baarheid in de UAE-noord regio. Lees de [aankondiging](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)voor meer informatie.

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Ondersteuning voor Automation en status configuratie GA in Duitsland-west-centraal

**Type:** Nieuwe functie

Het Automation-account en de status configuratie Beschik baarheid in de regio Duitsland West. Lees de [aankondiging](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)voor meer informatie.

### <a name="dsc-support-for-oracle-6-and-7"></a>DSC-ondersteuning voor Oracle 6 en 7

**Type:** Nieuwe functie

Oracle Linux 6-en 7-computers beheren met de configuratie van de automatiserings status. Zie [ondersteunde Linux-distributies](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) voor updates in de documentatie om deze wijzigingen weer te geven.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Open bare Preview voor Python3-runbooks in Automation

**Type:** Nieuwe functie

Azure Automation ondersteunt nu python 3 Cloud & hybride runbook-uitvoering in open bare preview in alle regio's in de globale cloud van Azure. Zie [aankondiging] (() https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) voor meer informatie.

## <a name="november-2020"></a>November 2020

### <a name="dsc-support-for-ubuntu-1804"></a>DSC-ondersteuning voor Ubuntu 18,04

**Type:** Nieuwe functie

Zie [ondersteunde Linux distributies](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) voor updates in de documentatie over deze wijzigingen.

## <a name="october-2020"></a>Oktober 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Ondersteuning voor Automation en status configuratie GA in Zwitserland-noord

**Type:** Nieuwe functie

Het Automation-account en de status configuratie Beschik baarheid in Zwitserland-noord. Lees de [aankondiging](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)voor meer informatie.

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Ondersteuning voor Automation en status configuratie GA in Brazilië-zuid Oost

**Type:** Nieuwe functie

Het Automation-account en de status configuratie Beschik baarheid in Brazilië-zuid Oost. Lees de [aankondiging](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)voor meer informatie.

### <a name="update-management-availability-in-south-central-us"></a>Beschik baarheid van Updatebeheer in Zuid-Centraal VS

**Type:** Nieuwe functie

Azure Automation regio toewijzing is bijgewerkt om Updatebeheer functie te ondersteunen in de regio Zuid-Centraal vs. Zie de [ondersteunde regio toewijzing](how-to/region-mappings.md#supported-mappings) voor updates in de documentatie om deze wijziging weer te geven.

## <a name="september-2020"></a>September 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>VM's buiten bedrijfsuren starten/stoppen runbooks bijgewerkt voor het gebruik van Azure AZ-modules

**Type:** Nieuwe functie

De runbooks voor het starten/stoppen van VM'S zijn bijgewerkt voor het gebruik van AZ-modules in plaats van Azure Resource Manager modules. Zie [VM's buiten bedrijfsuren starten/stoppen](automation-solution-vm-management.md) overzicht voor updates in de documentatie om deze wijzigingen weer te geven.

## <a name="august-2020"></a>Augustus 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>De DSC-extensie is gepubliceerd ter ondersteuning van Azure Arc

**Type:** Nieuwe functie

Gebruik Azure Automation status configuratie om configuraties centraal op te slaan en de gewenste status van hybride verbonden computers te onderhouden die zijn ingeschakeld via de Azure-extensie voor de DSC-VM van de server. Lees voor meer informatie het [overzicht van VM-extensies voor Arc-ingeschakelde servers](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Juli 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Geïntroduceerde open bare preview van persoonlijke koppelings ondersteuning in Automation

**Type:** Nieuwe functie

Gebruik persoonlijke Azure-koppeling om virtuele netwerken veilig te verbinden met Azure Automation met behulp van privé-eind punten. Lees de [aankondiging](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)voor meer informatie.

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Ondersteuning voor Hybrid Runbook Worker voor Windows Server 2008 R2

**Type:** Nieuwe functie

Automation-Hybrid Runbook Worker ondersteunt het besturings systeem Windows Server 2008 R2. Zie [ondersteunde besturings systemen](automation-windows-hrw-install.md#supported-windows-operating-system) voor updates in de documentatie om deze wijzigingen weer te geven.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Ondersteuning voor Updatebeheer voor Windows Server 2008 R2

**Type:** Nieuwe functie

Updatebeheer ondersteunt het evalueren en repareren van het besturings systeem Windows Server 2008 R2. Zie [ondersteunde besturings systemen](update-management/overview.md#clients) voor updates in de documentatie om deze wijzigingen weer te geven.

### <a name="automation-diagnostic-logs-schema-update"></a>Schema-update voor diagnostische automatiserings logboeken

**Type:** Nieuwe functie

Het schema van Azure Automation logboek gegevens in de Log Analytics-service is gewijzigd. Zie [Azure Automation-taak gegevens door sturen naar Azure monitor-logboeken](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)voor meer informatie.

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse ondersteunt Automation-Updatebeheer

**Type:** Nieuwe functie

Met Azure Lighthouse kunt u gedelegeerd resource beheer met Updatebeheer voor service providers en klanten. Meer informatie is [hier](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/) beschikbaar.

## <a name="june-2020"></a>Juni 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Beschik baarheid automatisering en Updatebeheer in de US Gov-Arizona regio

**Type:** Nieuwe functie

Automation-account en Updatebeheer zijn beschikbaar in US Gov-Arizona. Zie [aankondiging](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)voor meer informatie.

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Hybrid Runbook Worker onboarding-script bijgewerkt voor gebruik van AZ-modules

**Type:** Nieuwe functie

Het New-OnPremiseHybridWorker runbook is bijgewerkt met ondersteuning voor AZ-modules. Zie het pakket in de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)voor meer informatie.

### <a name="update-management-availability-in-china-east-2"></a>Beschik baarheid Updatebeheer in China-oost 2

**Type:** Nieuwe functie

Azure Automation regio toewijzing is bijgewerkt ter ondersteuning van Updatebeheer functie in China-oost 2-regio. Zie de [ondersteunde regio toewijzing](how-to/region-mappings.md#supported-mappings) voor updates in de documentatie om deze wijziging weer te geven.

## <a name="may-2020"></a>Mei 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>De DNS-records van de Automation-Service zijn bijgewerkt vanuit een regio-specifiek voor Automation-account-specifieke Url's

**Type:** Nieuwe functie

Azure Automation DNS-records zijn bijgewerkt ter ondersteuning van persoonlijke koppelingen. Lees de [aankondiging](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)voor meer informatie.

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>Mogelijkheid om Automation-runbooks & DSC-scripts die standaard zijn versleuteld, te blijven gebruiken

**Type:** Nieuwe functie

Naast het verbeteren van de beveiliging van assets, worden runbooks & DSC-scripts ook versleuteld om Azure Automation beveiliging te verbeteren.

## <a name="april-2020"></a>April 2020

### <a name="retirement-of-the-automation-watcher-task"></a>De taak Automation Watcher buiten gebruik stellen

**Type:** Plan voor wijziging

Azure Logic Apps is nu de aanbevolen en ondersteunde manier om te controleren op gebeurtenissen, terugkerende taken te plannen en acties te activeren. Er zijn geen verdere investeringen in de Watcher-taak functionaliteit. Zie [terugkerende geautomatiseerde taken plannen en uitvoeren met Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor meer informatie.

## <a name="march-2020"></a>Maart 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Ondersteuning voor IL5-berekeningen (impact Level 5) in azure commerciële en overheids Cloud

**Voert**

Azure Automation Hybrid Runbook Worker kan in Azure Government worden gebruikt ter ondersteuning van de workloads van impact niveau 5. Zie onze [documentatie](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)voor meer informatie.

## <a name="february-2020"></a>Februari 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Ondersteuning geïntroduceerd voor Azure Virtual Network-Service Tags

**Type:** Nieuwe functie

Met Automation-ondersteuning van service tags wordt het verkeer voor de Automation-Service toegestaan of geweigerd, voor een subset van scenario's. Raadpleeg de [documentatie](automation-hybrid-runbook-worker.md#service-tags)voor meer informatie.

### <a name="enable-tls-12-support-for-azure-automation-service"></a>TLS 1,2-ondersteuning voor de Azure Automation-service inschakelen

**Type:** Plan voor wijziging

Azure Automation volledig ondersteunt TLS 1,2 en alle client aanroepen (via webhooks, DSC-knoop punten en Hybrid worker). TLS 1,1 en TLS 1,0 worden nog steeds ondersteund voor achterwaartse compatibiliteit met oudere clients totdat klanten de standaardiseren en volledig migreren naar TLS 1,2.

## <a name="january-2020"></a>Januari 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Geïntroduceerde open bare preview van door de klant beheerde sleutels voor Azure Automation

**Type:** Nieuwe functie

Klanten kunnen de versleuteling van Azure Automation-assets beheren en beveiligen met behulp van hun eigen beheerde sleutels. Zie [het gebruik van door de klant beheerde sleutels](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)voor meer informatie.

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Buiten gebruik stellen van Azure Service Management (ASM) REST-Api's voor Azure Automation

**Type:** Buiten gebruik stellen

REST-Api's van Azure Service Management (ASM) voor Azure Automation worden buiten gebruik gesteld en worden na 30 januari 2020 niet meer ondersteund. Zie de [aankondiging](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u een bijdrage wilt leveren aan Azure Automation documentatie, raadpleegt u de [hand leiding voor docs-inzenders](/contribute/).
