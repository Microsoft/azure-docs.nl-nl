---
title: Een inleiding tot Azure Automation
description: In dit artikel wordt uitgelegd wat Azure Automation is en hoe dit moet worden gebruikt om de levenscyclus van infrastructuur en toepassingen te automatiseren.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, statusconfiguratie, updatebeheer, bijhouden van wijzigingen, DSC, voorraad, runbooks, python, grafisch
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e3986b7e8fc70f8662bed40b076897caa6165744
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182815"
---
# <a name="an-introduction-to-azure-automation"></a>Een inleiding tot Azure Automation

Azure Automation biedt een cloud-gebaseerde automatiserings- en configuratieservice die consistent beheer in uw Azure- en niet-Azure-omgevingen ondersteunt. Het omvat procesautomatisering, configuratiebeheer, updatebeheer, gedeelde mogelijkheden en heterogene functies. Automatisering geeft u volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van workloads en bronnen.

![Automatiseringsmogelijkheden](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Procesautomatisering

Met procesautomatisering in Azure Automation kunt u regelmatig terugkerende, tijdrovende en foutgevoelige taken voor cloudbeheer automatiseren. Dankzij deze service kunt u zich concentreren op werk dat bedrijfswaarde toevoegt. Door het aantal fouten te reduceren en de efficiëntie te verbeteren, kunt u uw operationele kosten drukken. De werkomgeving van procesautomatisering wordt beschreven in [Runbook-uitvoering in Azure Automation](automation-runbook-execution.md).

Procesautomatisering ondersteunt de integratie van Azure-services en andere openbare systemen die vereist zijn voor het implementeren, configureren en beheren van uw end-to-end-processen. Met de service kunt u [runbooks in grafische vorm ontwerpen](automation-runbook-types.md) in PowerShell of met behulp van Python. Met behulp van een [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) kunt u het beheer combineren door on-premises omgevingen te organiseren. Met [webhooks](automation-webhooks.md) kunt u voldoen aan aanvragen en zorgen voor doorlopende levering en bewerkingen door automatisering vanuit ITSM, DevOps en controlesystemen te activeren. 

## <a name="configuration-management"></a>Configuration Management

Configuratiebeheer in Azure Automation biedt toegang tot twee functies:

* Wijzigingen bijhouden en Inventaris
* Azure Automation State Configuration

### <a name="change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris

Wijzigingen bijhouden en Inventaris combineren functies voor het bijhouden van wijzigingen en inventaris, zodat u wijzigingen in de infrastructuur van virtuele machines en servers kunt bijhouden. De service biedt ondersteuning voor het bijhouden van wijzigingen in services, daemons, software, registers en bestanden in uw omgeving om u te helpen bij het vaststellen van ongewenste wijzigingen en het activeren van waarschuwingen. Met inventarisondersteuning kunt u in-guest resources opvragen voor zichtbaarheid van de geïnstalleerde toepassingen en andere configuratie-items. Zie [Wijzigingen bijhouden en Inventaris](change-tracking/overview.md) voor meer informatie over deze functie.

### <a name="azure-automation-state-configuration"></a>Azure Automation State Configuration

[Statusconfiguratie van Azure Automation](automation-dsc-overview.md) is een cloudfunctie voor PowerShell DSC (desired state configuration) waarmee services worden geboden voor bedrijfsomgevingen. Met behulp van deze functie kunt u uw DSC-resources beheren in Azure Automation en configuraties toepassen op virtuele of fysieke machines waarmee deze worden opgehaald van een DSC-pull-server in de Azure-cloud. 

## <a name="update-management"></a>Updatebeheer

Azure Automation bevat de functie [Updatebeheer](./update-management/overview.md) voor Windows- en Linux-systemen in hybride omgevingen. Updatebeheer geeft u inzicht in de updatevereisten voor Azure en andere clouds en on-premises. Met de functie kunt u geplande implementaties maken voor het organiseren van de installatie van updates binnen een gedefinieerd onderhoudsvenster. Als een update niet mag worden geïnstalleerd op een computer, kunt u de functie Updatebeheer gebruiken om deze uit te sluiten van een implementatie.

## <a name="shared-capabilities"></a>Gedeelde functionaliteit

Azure Automation biedt een aantal gedeelde mogelijkheden, waaronder gedeelde resources, op rollen gebaseerd toegangsbeheer, flexibele planning, integratie van broncodebeheer, controle en tagging.

### <a name="shared-resources"></a><a name="shared-resources"></a>Gedeelde resources

Azure Automation bestaat uit een set gedeelde bronnen waarmee u uw omgevingen eenvoudiger op schaal kunt automatiseren en configureren.

* **[Planningen](./shared-resources/schedules.md)** : automatiseringsbewerkingen op vooraf gedefinieerde tijdstippen activeren.
* **[Modules](./shared-resources/modules.md)** : Azure en andere systemen beheren. U kunt model importeren in het Automation-account voor cmdlets en DSC-resources van Microsoft, van derden of uit de community, en voor aangepaste gedefinieerde cmdlets en DSC-resources.
* **[Modulegalerie](automation-runbook-gallery.md)** : ondersteunt systeemeigen integratie met de PowerShell Gallery om runbooks weer te geven en deze te importeren in het Automation-account. Met de galerie kunt u snel aan de slag gaan met het integreren en ontwerpen van uw processen vanuit de PowerShell-galerie en Microsoft Script Center.
* **[Python 2-pakketten](python-packages.md)** : ondersteuning voor Python 2-runbooks voor uw Automation-account.
* **[Referenties](./shared-resources/credentials.md)** : hiermee kunt u vertrouwelijke informatie veilig opslaan die vervolgens kan worden gebruikt door runbooks en configuraties tijdens runtime.
* **[Verbindingen](automation-connections.md)** : bewaar naamwaardeparen van algemene informatie voor verbindingen met systemen. De auteur van de module geeft de verbindingen op in runbooks en configuraties voor gebruik tijdens runtime.
* **[Certificaten](./shared-resources/certificates.md)** : definieer informatie die moet worden gebruikt bij de verificatie en het beveiligen van geïmplementeerde resources wanneer deze worden geopend door runbooks of DSC-configuraties tijdens runtime. 
* **[Variabelen](./shared-resources/variables.md)** : bevatten inhoud die kan worden gebruikt in runbooks en configuraties. U kunt variabelewaarden wijzigen zonder een van de runbooks en configuraties die ernaar verwijzen te moeten veranderen.

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Azure Automation ondersteunt op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om toegang tot het Automation-account en de bijbehorende resources te reguleren. Zie [Op rollen gebaseerd toegangsbeheer voor Azure Automation](automation-role-based-access-control.md) voor meer informatie over het configureren van Azure RBAC voor uw Automation-account, runbooks en taken.

### <a name="source-control-integration"></a>Integratie van bronbeheer

Azure Automation ondersteunt [Integratie van broncodebeheer](source-control-integration.md). Deze functie gebruikt configuratie als code waar runbooks of configuraties in een bronbeheersysteem kunnen worden ingecheckt.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogene ondersteuning (Windows en Linux)

Automation is ontworpen voor gebruik in uw hybride cloudomgeving, maar ook voor uw Windows- en Linux-systemen. Het systeem biedt een consistente manier voor het automatiseren en configureren van geïmplementeerde workloads en de besturingssysteem waarop ze worden uitgevoerd.

## <a name="common-scenarios-for-automation"></a>Algemene scenario's voor Automation

Azure Automation ondersteunt beheer gedurende de gehele levenscyclus van uw infrastructuur en toepassingen. Algemene scenario's omvatten de volgende:

* **Runbooks schrijven**: maak PowerShell-, PowerShell Workflow-, grafische, Python 2- en DSC-runbooks in veelvoorkomende talen. 
* **Resources bouwen en implementeren**: implementeer virtuele machines in een hybride omgeving met behulp van runbooks en Azure Resource Manager-sjablonen. Integreer met ontwikkelingsprogramma's zoals Jenkins en Azure DevOps.
* **VM's configureren**: evalueer en configureer Windows- en Linux-machines met de configuratie voor infrastructuur en toepassing.
* **Kennis delen**: voorzie het systeem van informatie over de manier waarop de organisatie workloads levert en onderhoudt. 
* **Voorraad ophalen**: genereer een volledige inventarisatie van geïmplementeerde resources voor doelitems, rapportage en naleving. 
* **Wijzigingen vinden**: stel de wijzigingen vast die kunnen leiden tot een onjuiste configuratie en die kunnen zorgen voor een betere operationele naleving.
* **Controle**: isoleer wijzigingen op machines die problemen veroorzaken en die oplossingen bieden voor of escaleren met beheersystemen.
* **Beveiligen**: zet computers in quarantaine als er beveiligingswaarschuwingen worden gegenereerd. Stel in-guest vereisten in.
* **Beheersen**: stel Azure RBAC in voor teams. Herstel niet-gebruikte resources.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Prijzen voor Azure Automation

U kunt de prijzen gekoppeld aan Azure Automation bekijken op de pagina [Prijzen](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Automation-account maken](automation-quickstart-create-account.md)