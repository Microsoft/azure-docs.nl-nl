---
title: Wat is er nieuw in Azure Automation
description: Belangrijke updates voor Azure Automation elke maand worden bijgewerkt.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531085"
---
# <a name="whats-new-in-azure-automation"></a>Wat is er nieuw in Azure Automation?

Azure Automation ontvangt voortdurend verbeteringen. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten

Deze pagina wordt maandelijks bijgewerkt, dus bezoek deze regelmatig opnieuw.

## <a name="march-2021"></a>Maart 2021

### <a name="new-azure-automation-built-in-policies"></a>Nieuw Azure Automation ingebouwd beleid

**Type:** Nieuwe functie

Azure Automation 5 nieuwe ingebouwde beleidsregels toegevoegd:

- Automation-accounts moeten openbare netwerktoegang uitschakelen,
- Azure Automation-accounts moeten door de klant beheerde sleutels gebruiken om data-at-rest te versleutelen
- Toegangsaccounts Azure Automation om openbare netwerktoegang uit te schakelen
- Privé-eindpuntverbindingen configureren op Azure Automation accounts
- Privé-eindpuntverbindingen in Automation-accounts moeten zijn ingeschakeld.

Zie het [naslagartikel over](./policy-reference.md) beleid voor meer informatie.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Ondersteuning voor Automation en State Configuration ga gedeclareerd in India - zuid

**Type:** Nieuwe functie

Gebruik de mogelijkheden voor procesautomatisering en statusconfiguratie in India - zuid. Lees de [aankondiging](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) voor meer informatie.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Ondersteuning voor Automation en State Configuration ga gedeclareerd in VK - west

**Type:** Nieuwe functie

Gebruik procesautomatisering en statusconfiguratiemogelijkheden in VK - west. Lees aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/)

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Ondersteuning voor Automation en State Configuration ga gedeclareerd in VAE - centraal

**Type:** Nieuwe functie

Gebruik procesautomatisering en statusconfiguratiemogelijkheden in VAE - centraal. Lees de [aankondiging](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) voor meer informatie.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Ondersteuning voor Automation en State Configuration beschikbaar in Australië - centraal 2 , Noorwegen - west en Frankrijk - zuid

**Type:** Nieuwe functie

Zie voor meer informatie de pagina [Gegevens in de locatie](https://azure.microsoft.com/global-infrastructure/data-residency/) door het geografische gebied voor elke regio te selecteren.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Er zijn nieuwe scripts toegevoegd voor het installeren van Hybrid Worker in Windows en Linux

**Type:** Nieuwe functie

Er zijn twee nieuwe scripts toegevoegd aan de [GitHub-opslagplaats](https://github.com/azureautomation) Azure Automation voor een van de belangrijkste scenario's van Azure Automation voor het instellen van een Hybrid Runbook Worker op een Windows- of Linux-computer. Het script maakt een nieuwe VM of gebruikt een bestaande VM, maakt indien nodig een Log Analytics-werkruimte, installeert de Log Analytics-agent voor Windows of Log Analytics-agent voor Linux en registreert de machine bij de Log Analytics-werkruimte. Het Windows-script heet **Create Automation Windows HybridWorker** en het Linux-script is **Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Runbook aanroepen via een webhook Azure Resource Manager sjabloon

**Type:** Nieuwe functie

Zie Use a webhook from an ARM template (Een [webhook gebruiken vanuit een ARM-sjabloon)](./automation-webhooks.md#use-a-webhook-from-an-arm-template) voor meer informatie.

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Updatebeheer ondersteunt nu Centos 8.x, Red Hat Enterprise Linux Server 8.x en SUSE Linux Enterprise Server 15

**Type:** Nieuwe functie

Zie de [volledige lijst met](./update-management/overview.md#supported-operating-systems) ondersteunde Linux-besturingssystemen voor meer informatie.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Ondersteuning voor gegevens in regio's voor Brazilië - zuid en Zuid-Azië - oost 

**Type:** Nieuwe functie

In alle regio's, met uitzondering van Brazilië - zuid en Azië - zuidoost, worden Azure Automation-gegevens opgeslagen in een andere regio (gekoppelde Azure-regio) voor bedrijfscontinuïteit en herstel na noodherstel (BCDR). Alleen voor de regio's Brazilië en Azië - zuidoost slaan we Azure Automation gegevens op in dezelfde regio om te voldoen aan de vereisten voor gegevensopslag voor deze regio's. Zie [Geo-replicatie in Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation) voor meer informatie.

## <a name="february-2021"></a>Februari 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Ondersteuning voor Automation en State Configuration ga gedeclareerd in Japan - west

**Type:** Nieuwe functie

Automation-account en State Configuration beschikbaarheid in de regio Japan - west. Lees de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Aangepaste naleving van Azure Policy voor het afdwingen van runbookuitvoering op Hybrid Worker

**Typ :** Nieuwe functie

U kunt de nieuwe Azure Policy-nalevingsregel gebruiken om het maken van taken, webhooks en taakschema's alleen uit te voeren op Hybrid Worker groepen.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Updatebeheer in VS - oost, Frankrijk - centraal en Europa - noord regio's

**Type:** Nieuwe functie

De Updatebeheer automation-functie is beschikbaar in VS - oost, Frankrijk - centraal en Europa - noord regio's. Zie [Ondersteunde regiotoewijzing](how-to/region-mappings.md) voor updates van de documentatie die deze wijziging weerspiegelt.

## <a name="january-2021"></a>Januari 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Ondersteuning voor Automation en State Configuration ga gedeclareerd in Zwitserland - west

**Type:** Nieuwe functie

Automation-account en State Configuration beschikbaarheid in de Zwitserland - west regio. Lees de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/)

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Python 3-script toegevoegd om module met meerdere afhankelijkheden te importeren

**Type:** Nieuwe functie

Het script kan worden gedownload uit onze [GitHub-opslagplaats](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py). 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Hybrid Runbook Worker voor Centos 8.x/RHEL 8.x/SLES 15

**Type.** Nieuwe functie

De Hybrid Runbook Worker ondersteunt distributies van CentOS 8.x, REHL 8.x en SLES 15 voor alleen procesautomatisering op Hybrid Runbook Workers. Zie [Ondersteunde besturingssystemen voor](automation-linux-hrw-install.md#supported-linux-operating-systems) updates van de documentatie om deze wijzigingen weer te geven.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Updatebeheer en Wijzigingen bijhouden beschikbaarheid in de regio's Australië - oost, Azië - oost, VS - west en VS - centraal

**Type:** Nieuwe functie

Automation-account, Wijzigingen bijhouden en inventaris en Updatebeheer zijn beschikbaar in de regio'Australië - oost, Azië - oost, VS - west en VS - centraal. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Openbare preview van Python 3-runbooks geïntroduceerd in de cloud van de Amerikaanse overheid

**Type:** Nieuwe functie Azure Automation openbare preview-ondersteuning voor het uitvoeren van Python 3-cloud en hybride runbook in cloudregio's van de Amerikaanse overheid. Zie de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation-runbooks verplaatst van TechNet Script Center naar GitHub

**Type:** Plannen voor wijziging

Het TechNet Script Center wordt niet meer gebruikt en alle runbooks die worden gehost in de Runbook Gallery, zijn verplaatst naar onze [Automation GitHub-organisatie.](https://github.com/azureautomation) Lees runbooks die naar [GitHub worden verplaatst Azure Automation meer informatie.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="december-2020"></a>December 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation en Updatebeheer Private Link GA

**Type:** Nieuwe functie

Azure Automation en Updatebeheer aangekondigd als algemene algemene Azure-clouds en overheids clouds. Azure Automation ondersteuning voor Private Link om de uitvoering van een runbook op een Hybrid Worker-rol te beveiligen, met behulp van Updatebeheer voor het patchen van computers, het aanroepen van een runbook via een webhook en het gebruik van State Configuration-service om uw computers te blijven gebruiken. Lees voor meer informatie Azure Automation Private Link [ondersteuning](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation geclassificeerd als Grade-C gecertificeerd voor toegankelijkheid

**Type:** Nieuwe functie

Toegankelijkheidsfuncties van Microsoft-producten helpen instanties om te voldoen aan algemene toegankelijkheidsvereisten. Zoek op de blogaankondigingspagina **naar Azure Automation** het rapport Toegankelijkheidsconformatie voor de Automation-service te lezen. [](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Ondersteuning voor Automation en State Configuration GA in VAE - noord

**Type:** Nieuwe functie

Automation-account en State Configuration beschikbaarheid in de VAE - noord regio. Lees de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Ondersteuning voor Automation en State Configuration GA in Duitsland - west-centraal

**Type:** Nieuwe functie

Automation-account en State Configuration beschikbaarheid in de regio Duitsland - west. Lees de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)

### <a name="dsc-support-for-oracle-6-and-7"></a>DSC-ondersteuning voor Oracle 6 en 7

**Type:** Nieuwe functie

Beheer Oracle Linux 6 en 7 machines met Automation State Configuration. Zie [Ondersteunde Linux-distributies](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) voor updates van de documentatie om deze wijzigingen weer te geven.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Openbare preview voor Python3-runbooks in Automation

**Type:** Nieuwe functie

Azure Automation ondersteunt nu uitvoering van Python 3-cloud en hybride runbook in openbare preview in alle regio's in de wereldwijde Azure-cloud. Zie [announcement](( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) voor meer informatie.

## <a name="november-2020"></a>November 2020

### <a name="dsc-support-for-ubuntu-1804"></a>DSC-ondersteuning voor Ubuntu 18.04

**Type:** Nieuwe functie

Zie [Ondersteunde Linux-distributies](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) voor updates van de documentatie die deze wijzigingen weerspiegelt.

## <a name="october-2020"></a>Oktober 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Ondersteuning voor Automation en State Configuration GA in Zwitserland - noord

**Type:** Nieuwe functie

Automation-account en State Configuration beschikbaarheid in Zwitserland - noord. Lees aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Ondersteuning voor Automation en State Configuration GA in Brazilië - zuid - oost

**Type:** Nieuwe functie

Automation-account en State Configuration beschikbaarheid in Brazilië - zuid - oost. Lees aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)

### <a name="update-management-availability-in-south-central-us"></a>Updatebeheer beschikbaarheid in VS - zuid-centraal

**Type:** Nieuwe functie

Azure Automation regiotoewijzing bijgewerkt om ondersteuning te Updatebeheer functie in de regio VS - zuid-centraal. Zie [Ondersteunde regiotoewijzing](how-to/region-mappings.md#supported-mappings) voor updates van de documentatie om deze wijziging weer te geven.

## <a name="september-2020"></a>September 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>VM's buiten bedrijfsuren starten/stoppen-runbooks bijgewerkt voor het gebruik van Azure Az-modules

**Type:** Nieuwe functie

Runbooks voor VM's starten/stoppen zijn bijgewerkt voor het gebruik van Az-modules in plaats van Azure Resource Manager modules. Zie [VM's buiten bedrijfsuren starten/stoppen](automation-solution-vm-management.md) overzicht voor updates van de documentatie om deze wijzigingen weer te geven.

## <a name="august-2020"></a>Augustus 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>De DSC-extensie is gepubliceerd ter ondersteuning van Azure Arc

**Type:** Nieuwe functie

Gebruik Azure Automation State Configuration om configuraties centraal op te slaan en de gewenste status te behouden van hybride verbonden machines die zijn ingeschakeld via de DSC VM-extensie Azure Arc DSC-servers. Lees Overzicht van [VM-extensies voor Arc-servers voor meer informatie.](../azure-arc/servers/manage-vm-extensions.md)

### <a name="july-2020"></a>Juli 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Openbare preview van ondersteuning voor Private Link in Automation geïntroduceerd

**Type:** Nieuwe functie

Gebruik Azure Private Link virtuele netwerken veilig te verbinden met Azure Automation met behulp van privé-eindpunten. Lees de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Hybrid Runbook Worker voor Windows Server 2008 R2

**Type:** Nieuwe functie

Automation Hybrid Runbook Worker het besturingssysteem Windows Server 2008 R2. Zie [Ondersteunde besturingssystemen](automation-windows-hrw-install.md#supported-windows-operating-system) voor updates van de documentatie om deze wijzigingen weer te geven.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Updatebeheer voor Windows Server 2008 R2

**Type:** Nieuwe functie

Updatebeheer ondersteunt het beoordelen en patchen van het besturingssysteem Windows Server 2008 R2. Zie [Ondersteunde besturingssystemen](update-management/overview.md#clients) voor updates van de documentatie om deze wijzigingen weer te geven.

### <a name="automation-diagnostic-logs-schema-update"></a>Schema-update voor diagnostische logboeken voor Automation

**Type:** Nieuwe functie

Het schema van Azure Automation logboekgegevens in de Log Analytics-service is gewijzigd. Zie Taakgegevens doorsturen [naar Azure Automation logboeken voor Azure Monitor meer informatie.](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse biedt ondersteuning voor Automation Updatebeheer

**Type:** Nieuwe functie

Azure Lighthouse gedelegeerd resourcebeheer mogelijk met Updatebeheer voor serviceproviders en klanten. Meer informatie is [hier](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/) beschikbaar.

## <a name="june-2020"></a>Juni 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatisering en Updatebeheer beschikbaarheid in de US Gov - Arizona regio

**Type:** Nieuwe functie

Automation-account en Updatebeheer zijn beschikbaar in US Gov - Arizona. Zie aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Hybrid Runbook Worker-onboardingscript bijgewerkt voor het gebruik van Az-modules

**Type:** Nieuwe functie

Het New-OnPremiseHybridWorker runbook is bijgewerkt om Az-modules te ondersteunen. Zie het pakket in de [PowerShell Gallery.](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)

### <a name="update-management-availability-in-china-east-2"></a>Updatebeheer beschikbaarheid in China - oost 2

**Type:** Nieuwe functie

Azure Automation regiotoewijzing bijgewerkt ter ondersteuning van Updatebeheer functie in China - oost 2 regio. Zie [Ondersteunde regiotoewijzing](how-to/region-mappings.md#supported-mappings) voor updates van de documentatie om deze wijziging weer te geven.

## <a name="may-2020"></a>Mei 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>DNS-records van automation-service bijgewerkt van regiosspecifieke url's naar accountspecifieke URL's van Automation

**Type:** Nieuwe functie

Azure Automation DNS-records zijn bijgewerkt om privékoppelingen te ondersteunen. Lees de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Mogelijkheid toegevoegd om Automation-runbooks en DSC-scripts standaard versleuteld te houden

**Type:** Nieuwe functie

Naast het verbeteren van de beveiliging van assets, worden runbooks en DSC-scripts ook versleuteld om de beveiliging Azure Automation verbeteren.

## <a name="april-2020"></a>April 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Het uitvoeren van de Automation Watcher-taak

**Type:** Plannen voor wijziging

Azure Logic Apps is nu de aanbevolen en ondersteunde manier om te controleren op gebeurtenissen, terugkerende taken te plannen en acties te activeren. Er worden geen verdere investeringen gedaan in de functionaliteit van Watcher-taken. Zie Terugkerende geautomatiseerde taken plannen en uitvoeren met Logic Apps [voor meer Logic Apps.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

## <a name="march-2020"></a>Maart 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Ondersteuning voor impactniveau 5 (IL5)-rekenisolatie in de commerciële azure- en overheidscloud

**Type:**

Azure Automation Hybrid Runbook Worker kunnen worden gebruikt in Azure Government ondersteuning van Impact Level 5-workloads. Zie onze documentatie voor meer [informatie.](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)

## <a name="february-2020"></a>Februari 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Ondersteuning geïntroduceerd voor servicetags voor virtuele Azure-netwerken

**Type:** Nieuwe functie

Automatiseringsondersteuning van servicetags staat het verkeer voor de Automation-service toe of wordt dit voor een subset van scenario's. Zie de documentatie voor meer [informatie.](automation-hybrid-runbook-worker.md#service-tags)

### <a name="enable-tls-12-support-for-azure-automation-service"></a>TLS 1.2-ondersteuning inschakelen voor Azure Automation service

**Type:** Plannen voor wijziging

Azure Automation biedt volledige ondersteuning voor TLS 1.2 en alle client-aanroepen (via webhooks, DSC-knooppunten en Hybrid Worker). TLS 1.1 en TLS 1.0 worden nog steeds ondersteund voor achterwaartse compatibiliteit met oudere clients totdat klanten standaardiseren en volledig migreren naar TLS 1.2.

## <a name="january-2020"></a>Januari 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Openbare preview van door de klant beheerde sleutels voor Azure Automation

**Type:** Nieuwe functie

Klanten kunnen versleuteling van uw Azure Automation beheren en beveiligen met behulp van hun eigen beheerde sleutels. Zie Use [of customer-managed keys (Door de klant beheerde sleutels gebruiken) voor meer informatie.](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Rest API's van Azure Service Management (ASM) voor Azure Automation

**Type:** Pensioen

REST API's voor Azure Service Management (ASM) voor Azure Automation worden na 30 januari 2020 niet meer ondersteund. Zie de aankondiging voor [meer informatie.](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)

## <a name="next-steps"></a>Volgende stappen

Als u een bijdrage wilt leveren aan Azure Automation documentatie, bekijkt u de Gids voor [docs-inzenders.](/contribute/)
