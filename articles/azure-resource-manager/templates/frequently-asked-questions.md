---
title: Veelgestelde vragen over ARM-sjablonen
description: Veelgestelde vragen over Azure Resource Manager sjablonen (ARM-sjablonen).
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e64253aa79cfaeb2655bb091d038dc7c98b198bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419402"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Veelgestelde vragen over ARM-sjablonen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Resource Manager sjablonen (ARM-sjablonen).

## <a name="getting-started"></a>Aan de slag

* **Wat zijn ARM-sjablonen en waarom moet ik deze gebruiken?**

  ARM-sjablonen zijn JSON-bestanden waarin u definieert wat u wilt implementeren in Azure. Met sjablonen kunt u een infra structuur-as-code-oplossing voor Azure implementeren. Uw organisatie kan de vereiste infra structuur herhaaldelijk en betrouwbaar implementeren voor verschillende omgevingen.

  Zie [Wat zijn arm-sjablonen?](overview.md) voor meer informatie over hoe arm-sjablonen u helpen bij het beheren van uw Azure-infra structuur.

* **Hoe kan ik aan de slag met sjablonen?**

  Om het ontwerpen van ARM-sjablonen te vereenvoudigen, hebt u de juiste hulpprogram ma's nodig. U kunt het beste [Visual Studio code](https://code.visualstudio.com/) en de [uitbrei ding voor de Azure Resource Manager-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)installeren. Voor een korte inleiding tot deze hulpprogram ma's raadpleegt u [Quick Start: arm-sjablonen maken met Visual Studio code](quickstart-create-templates-use-visual-studio-code.md).

  Als u klaar bent voor het maken van ARM-sjablonen, start u de [zelf studie reeks voor beginners op arm-sjablonen](template-tutorial-create-first-template.md). In deze zelf studies gaat u stapsgewijs door de procedure voor het maken van een ARM-sjabloon. Meer informatie over de verschillende onderdelen van de sjabloon en hoe deze samen werken. Deze inhoud is ook beschikbaar als een [Microsoft Learn module](/learn/modules/authoring-arm-templates/).

* **Moet ik ARM-sjablonen of-terraform gebruiken om te implementeren in azure?**

  Gebruik de optie die u het beste vindt. Beide services helpen u bij het automatiseren van implementaties naar Azure.

  We geloven dat er voor delen zijn voor het gebruik van ARM-sjablonen over andere infrastructuur services. Zie voor meer informatie over deze voor delen [Waarom arm-sjablonen kiezen?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Build 2020

* **Ik heb uw presentatie gemist bij micro soft build 2020. Is de presentatie beschikbaar voor weer gave?**

  Ja, [Bekijk het op elk gewenst moment](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Waar kan ik meer informatie krijgen over de nieuwe functies die u tijdens de build hebt aangekondigd?**

  Voor algemene informatie over de functies die we werken, kunt u zich aanmelden bij onze [groep Azure Advisors Deployments Yammer](https://aka.ms/ARMMeet).

  Meld u aan [voor meldingen voor](https://aka.ms/armLangUpdates)meer informatie over de taal van de nieuwe sjabloon.

  Zie [Azure Resource Manager-sjabloon specificaties (preview-versie)](template-specs.md)voor meer informatie over sjabloon specificaties.

## <a name="creating-and-testing-templates"></a>Sjablonen maken en testen

* **Waar vind ik meer informatie over aanbevolen procedures voor ARM-sjablonen?**

  Zie [Best practices voor arm](template-best-practices.md)-sjablonen voor aanbevelingen voor het implementeren van uw sjablonen. Nadat u een sjabloon hebt gemaakt, voert u de [arm test Toolkit](https://github.com/azure/arm-ttk)uit. Hiermee wordt gecontroleerd of uw sjabloon overeenkomt met aanbevolen procedures.

* **Ik heb mijn omgeving ingesteld via de portal. Is er een manier om de sjabloon op te halen uit een bestaande resource groep?**

  Ja, u kunt [de sjabloon exporteren](export-template-portal.md) vanuit een resource groep. De geëxporteerde sjabloon is een goed uitgangs punt voor het leren over sjablonen, maar u wilt deze waarschijnlijk herzien voordat u het in een productie omgeving gebruikt.

  Wanneer u de sjabloon exporteert, kunt u selecteren welke resources u wilt toevoegen in de sjabloon.

* **Kan ik een resource groep maken in een ARM-sjabloon en daar resources op implementeren?**

  Ja, u kunt een resource groep maken in een sjabloon wanneer u de sjabloon implementeert op het niveau van uw Azure-abonnement. Zie [resource groep en resources](deploy-to-subscription.md#resource-groups)voor een voor beeld van het maken van een resource groep en het implementeren van resources.

* **Kan ik een abonnement in een ARM-sjabloon maken?**

  Ja, Zie [programmatisch Azure-abonnementen maken met de nieuwste api's](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)voor meer informatie.

* **Hoe kan ik mijn sjabloon testen voordat ik deze implementatie?**

  We raden u aan om de [arm test Toolkit](https://github.com/azure/arm-ttk) en de [What-if-bewerking](template-deploy-what-if.md) uit te voeren op uw sjablonen voordat u ze implementeert. De test Toolkit controleert of uw sjabloon aanbevolen procedures gebruikt. Er worden waarschuwingen weer gegeven wanneer er wijzigingen worden geïdentificeerd die kunnen worden verbeterd hoe u uw sjabloon hebt geïmplementeerd.

  De bewerking What-if toont de wijzigingen die uw sjabloon in uw omgeving maakt. U kunt onbedoelde wijzigingen zien voordat ze worden geïmplementeerd. Wat-als resulteert ook in fouten die tijdens de Preflight-validatie kunnen worden gedetecteerd. Als uw sjabloon bijvoorbeeld een syntactische fout bevat, wordt deze fout geretourneerd. Het retourneert ook eventuele fouten die kunnen bepalen wat de uiteindelijke status van de geïmplementeerde resources zijn. Als uw sjabloon bijvoorbeeld een opslag account implementeert met een naam die al in gebruik is, wat-als deze fout retourneert.

* **Waar vind ik informatie over de eigenschappen die beschikbaar zijn voor elk resource type?**

  VS code biedt IntelliSense voor het werken met de resource-eigenschappen. U kunt ook de [sjabloon verwijzing](/azure/templates/) voor eigenschappen en beschrijvingen weer geven.

* **Ik moet meerdere exemplaren van een resource type maken. Hoe kan ik een iterator in mijn sjabloon maken?**

  Gebruik het copy-element om meer dan één exemplaar op te geven. U kunt kopiëren op [resources](copy-resources.md), [Eigenschappen](copy-properties.md), [variabelen](copy-variables.md)en [uitvoer](copy-outputs.md)gebruiken.

## <a name="template-language"></a>Sjabloon taal

* **Ik heb gehoord dat je aan de taal van een nieuwe sjabloon werkt. Waar kan ik meer informatie vinden?**

  Zie [Wat is Bicep (preview)?](bicep-overview.md)voor meer informatie over de nieuwe taal.

* **Is er een plan voor de ondersteuning van het maken van sjablonen in YAML?**

  Er is momenteel geen abonnement om YAML te ondersteunen. We zijn ervan overtuigd dat de taal van de nieuwe sjabloon een oplossing biedt die eenvoudiger te gebruiken is dan YAML of JSON.

* **Kan ik nog steeds sjablonen in JSON schrijven nadat de taal van de nieuwe sjabloon is vrijgegeven?**

  Ja, u kunt door gaan met het gebruik van JSON-sjablonen.

* **Kunt u een hulp programma voor het converteren van mijn JSON-sjablonen naar de taal van de nieuwe sjabloon bieden?**

  Ja. Zie [arm-sjablonen converteren tussen JSON en Bicep](bicep-decompile.md).

## <a name="template-specs"></a>Sjabloonspecificaties

* **Hoe kan ik aan de slag met de preview-versie van sjabloon specificaties?**

  Installeer de meest recente versie van Power shell of Azure CLI. Gebruik voor Azure PowerShell de [versie 5.0.0 of hoger](/powershell/azure/install-az-ps). Gebruik voor Azure CLI de [versie 2.14.2 of hoger](/cli/azure/install-azure-cli).

* **Hoe zijn sjabloon specificaties en Azure-blauw drukken?**

  Azure-blauw drukken maakt gebruik van sjabloon specificaties in de implementatie door de resource te vervangen door `blueprint definition` een `template spec` resource. We bieden een migratie-pad om de blauw druk definitie te converteren naar een sjabloon specificatie, maar de definitie-Api's van de blauw druk worden nog steeds ondersteund. Er zijn geen wijzigingen in de `blueprint assignment` resource. Blauw drukken blijft een gebruikers ervaring voor het opstellen van een omgeving in Azure.

* **Vervangen de sjabloon specificaties gekoppelde sjablonen?**

  Nee, maar sjabloon specificaties zijn ontworpen om goed te werken met gekoppelde sjablonen. U hoeft de gekoppelde sjabloon niet te verplaatsen naar een openbaar toegankelijk eind punt voordat u de bovenliggende sjabloon implementeert. In plaats daarvan verpakt u de bovenliggende sjabloon en de bijbehorende artefacten samen bij het maken van de sjabloon specificatie.

* **Kunnen sjabloon specificaties worden gedeeld via abonnementen?**

  Ja, ze kunnen worden gebruikt voor alle abonnementen zolang de gebruiker lees toegang heeft tot de sjabloon specificatie. Sjabloon specificaties kunnen niet worden gebruikt voor meerdere tenants.

## <a name="scripts-in-templates"></a>Scripts in sjablonen

* **Kan ik een script in mijn sjabloon toevoegen om taken uit te voeren die niet mogelijk zijn in een sjabloon?**

  Ja, [implementatie scripts](deployment-script-template.md)gebruiken. U kunt Azure PowerShell of Azure CLI-scripts in uw sjablonen toevoegen. De functie is beschikbaar als preview-versie.

* **Kan ik nog steeds aangepaste script extensies en desired state Configuration (DSC) gebruiken?**

  Deze opties zijn nog steeds beschikbaar en zijn niet gewijzigd. Implementatie scripts zijn ontworpen om acties uit te voeren die niet zijn gerelateerd aan de VM-gast. Als u een script moet uitvoeren op een hostbesturingssysteem in een VM, is de aangepaste script extensie en/of DSC een betere keuze. Implementatie scripts hebben echter voor delen, zoals het instellen van de time-outperiode.

* **Worden implementatie scripts ondersteund in Azure Government?**

  Ja, u kunt implementatie scripts gebruiken in US Gov-Arizona en US Gov-Virginia.

## <a name="preview-changes-before-deployment"></a>Preview-wijzigingen voorafgaand aan implementatie

* **Kan ik een voor beeld bekijken van de wijzigingen die plaatsvinden voordat een sjabloon wordt geïmplementeerd?**

  Ja, gebruik de [functie What-if](template-deploy-what-if.md). Het evalueert de huidige status van uw omgeving en vergelijkt deze met de status die na de implementatie aanwezig zal zijn. U kunt de overzichts wijzigingen controleren om te controleren of de sjabloon geen onverwachte resultaten heeft.

* **Kan ik wat-als gebruiken met zowel een incrementele als een volledige modus?**

  Ja, beide [implementatie modi](deployment-modes.md) worden ondersteund. Zie voor een voor beeld van het gebruik van een incrementele modus ' [wat als'-bewerking uitvoeren](template-deploy-what-if.md#run-what-if-operation). Zie het [verwijderen bevestigen](template-deploy-what-if.md#confirm-deletion)voor een voor beeld van het gebruik van de volledige modus.

* **Is wat-als werkt met gekoppelde sjablonen?**

  Ja, wat-als de status van de bovenliggende sjabloon en de gekoppelde sjablonen evalueert.

* **Kan ik wat-als gebruiken in een Azure-pijp lijn?**

  Ja, u kunt wat-als gebruiken om te controleren of de pijp lijn moet worden voortgezet.

* **Wanneer ik What-if gebruik, zie ik wijzigingen in eigenschappen die niet in mijn sjabloon staan. Wordt dit ' ruis ' verwacht?**

  We werken aan het verminderen van de ruis. U kunt ons verbeteren door problemen op te lossen in onze GitHub-opslag plaats: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Sjabloon visualiseren

* **Is er een manier om mijn ARM-sjabloon en de bijbehorende resources te visualiseren?**

  We hebben een [Community-bijdrage VS code-uitbrei ding](https://aka.ms/ARMVisualizer) die een uitstekende taak is voor het visualiseren van uw arm-sjabloon. Hier ziet u de resources die u implementeert en de relaties daartussen.

* **Kan ik de sjabloon visualer buiten VS code gebruiken?**

  Er wordt een voor beeld weer gegeven van de sjabloon visualiseren in de portal. Bekijk deze [korte sessie van build](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)voor meer informatie.

## <a name="deployment-limits"></a>Implementatie limieten

* **Hoeveel resource groepen kan ik implementeren in een enkele implementatie bewerking?**

  In het verleden was deze limiet vijf resource groepen. Het is onlangs verhoogd tot 800-resource groepen. Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie.

* **Ik heb een fout melding ontvangen over een limiet van 800 implementaties in de implementatie geschiedenis. Wat moet ik doen?**

  We wijzigen de manier waarop de implementatie geschiedenis voor een resource groep wordt onderhouden. In het verleden moest u implementaties hand matig verwijderen uit deze geschiedenis om deze fout te voor komen. Vanaf 2020 juni worden implementaties van de geschiedenis automatisch verwijderd wanneer u bijna de limiet krijgt. Raadpleeg [Automatic deletions from deployment history](deployment-history-deletions.md) (Automatische verwijderingen uit de implementatiegeschiedenis) voor meer informatie.

  Het verwijderen van een implementatie uit de geschiedenis heeft geen invloed op de geïmplementeerde resources.

## <a name="templates-and-devops"></a>Sjablonen en DevOps

* **Kan ik ARM-sjablonen integreren in azure-pijp lijnen?**

  Ja. Zie [zelf studie: doorlopende integratie van arm-sjablonen met Azure-pijp lijnen](deployment-tutorial-pipeline.md) en [arm-sjablonen integreren met Azure-pijp](add-template-to-azure-pipelines.md)lijnen voor een uitleg over het gebruik van sjablonen en pijp lijnen.

* **Kan ik GitHub-acties gebruiken om een sjabloon te implementeren?**

  Ja, Zie [arm-sjablonen implementeren met behulp van github-acties](deploy-github-actions.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Wat zijn arm-sjablonen?](overview.md)voor een inleiding tot arm-sjablonen.
