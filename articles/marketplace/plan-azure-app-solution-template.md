---
title: Een oplossings sjabloon plannen voor een Azure-toepassings aanbieding
description: Meer informatie over wat u nodig hebt om een oplossings sjabloon plan te maken voor een nieuwe Azure-toepassings aanbieding met behulp van de portal voor commerciële Marketplace in het micro soft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 3e4d0513808cdc44fc71e182a07fa6b050d182ee
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452511"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Een oplossings sjabloon plannen voor een Azure-toepassings aanbieding

In dit artikel worden de vereisten beschreven voor het publiceren van een oplossings sjabloon plan voor een Azure-toepassing aanbieding. Een oplossings sjabloon plan is een van de twee typen plannen die worden ondersteund door Azure-toepassing-aanbiedingen. Zie [typen plannen](plan-azure-application-offer.md#plans)voor meer informatie over het verschil tussen deze twee typen abonnementen. Als u dit nog niet hebt gedaan, kunt u [een Azure-toepassings aanbieding plannen](plan-azure-application-offer.md).

Voor het type oplossings sjabloon abonnement is een [Azure Resource Manager sjabloon (arm-sjabloon)](../azure-resource-manager/templates/overview.md) vereist om automatisch uw oplossings infrastructuur te implementeren.

## <a name="solution-template-requirements"></a>Vereisten voor oplossings sjablonen

| Vereisten | Details |
| ------------ | ------------- |
| Facturering en meting | Oplossingen voor oplossings sjablonen zijn niet transactable, maar kunnen worden gebruikt voor het implementeren van betaalde VM-aanbiedingen die worden gefactureerd via micro soft Commercial Marketplace. De resources die de ARM-sjabloon implementeren van de oplossing zijn ingesteld in het Azure-abonnement van de klant. Voor betalen per gebruik-virtuele machines wordt gehandeld met de klant via micro soft en gefactureerd via het Azure-abonnement van de klant. <br><br> Voor een BYOL-facturering (meebrengen van uw eigen licentie), hoewel kosten voor de infra structuur van micro soft billing die in het abonnement van de klant worden gemaakt, kunt u uw software licentie kosten rechtstreeks aan de klant door geven. |
| Azure-compatibele virtuele harde schijf (VHD) | Vm's moeten zijn gebouwd op Windows of Linux. Zie voor meer informatie:<ul><li>[Een technische Asset voor Azure VM maken](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (voor Windows-vhd's)</li><li>[Linux-distributies die zijn goedgekeurd op Azure](../virtual-machines/linux/endorsed-distros.md) (voor Linux-vhd's).</li></ul> |
| Gebruikskenmerken van klanten | Het inschakelen van de toewijzing van klant gebruik is vereist voor alle oplossings sjablonen die op Azure Marketplace worden gepubliceerd. Zie voor meer informatie over het gebruik van de klant en hoe u deze functie inschakelt, de [klant gebruiks toewijzing van Azure-partners](azure-partner-customer-usage-attribution.md). |
| Beheerde schijven gebruiken | [Managed disks](../virtual-machines/managed-disks-overview.md) is de standaard optie voor persistente schijven van Infrastructure as a Service (IaaS) Vm's in Azure. U moet beheerde schijven gebruiken in oplossings sjablonen.<ul><li>Volg de richt lijnen in [Managed disks in azure Resource Manager Templates](../virtual-machines/using-managed-disks-template-deployments.md)en gebruik de voor [beelden](https://github.com/Azure/azure-quickstart-templates)om uw oplossings sjablonen bij te werken.</li><li>Als u de VHD wilt publiceren als een installatie kopie in azure Marketplace, importeert u de onderliggende VHD van de beheerde schijven naar een opslag account met behulp van [Azure PowerShell](../virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md) of de [Azure cli](../virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md)</ul> |
| Implementatie pakket | U hebt een implementatie pakket nodig waarmee klanten uw abonnement kunnen implementeren. Als u meerdere plannen maakt waarvoor dezelfde technische configuratie nodig is, kunt u hetzelfde plan pakket gebruiken. Zie de volgende sectie: implementatie pakket voor meer informatie. |
|||

## <a name="deployment-package"></a>Implementatie pakket

Het implementatie pakket bevat alle sjabloon bestanden die nodig zijn voor dit plan en eventuele extra resources, verpakt als zip-bestand.

Alle Azure-toepassingen moeten deze twee bestanden in de hoofdmap van een zip-archief bevatten:

- Een resource manager-sjabloon bestand [ met de naammainTemplate.jsop](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Met deze sjabloon worden de resources gedefinieerd die moeten worden geïmplementeerd in het Azure-abonnement van de klant. Voor voor beelden van Resource Manager-sjablonen raadpleegt u de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/documentation/templates/) of de bijbehorende [github: Azure Resource Manager Quick](https://github.com/azure/azure-quickstart-templates) start-sjablonen opslag plaats.
- Een definitie van de gebruikers interface voor de Azure-toepassing voor het maken van toepassingen met de naam [createUiDefinition.jsop](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). In de gebruikersinterface geeft u elementen op waarmee consumenten parameterwaarden kunnen opgeven.

De maximale bestands grootte die wordt ondersteund zijn:

- Tot 1 GB in totaal gecomprimeerd. zip-archief grootte
- Maxi maal 1 GB voor elk afzonderlijk niet-gecomprimeerd bestand in het zip-archief

Alle nieuwe aanbiedingen van Azure Application moeten ook een GUID voor [klant gebruik van Azure-partners](azure-partner-customer-usage-attribution.md) bevatten.

## <a name="azure-regions"></a>Azure-regio's

U kunt uw abonnement publiceren naar de open bare Azure-regio, de Azure Government regio of beide. Voordat u naar [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)publiceert, moet u het abonnement testen en valideren in de omgeving, omdat bepaalde eind punten kunnen verschillen. Als u uw abonnement wilt instellen en testen, vraagt u een proef account aan bij [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/).

Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten).

Zie [geografische Beschik baarheid en valuta ondersteuning](marketplace-geo-availability-currencies.md)voor een lijst met landen en regio's die door de commerciële Marketplace worden ondersteund.

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS. Als u uw certificeringen voor deze Program ma's wilt controleren, kunt u Maxi maal 100 koppelingen opgeven. Deze kunnen links naar uw vermelding in het programma staan, of koppelingen naar beschrijvingen van uw naleving met hen op uw eigen websites. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

## <a name="choose-who-can-see-your-plan"></a>Kies wie uw abonnement kan zien

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen (openbaar) of alleen voor een specifieke doel groep (privé). U kunt Maxi maal 100 plannen maken en tot 45 kunnen deze privé zijn. Mogelijk wilt u een privé-abonnement maken om verschillende prijs opties of technische configuraties aan specifieke klanten te bieden.

U verleent toegang tot een persoonlijk abonnement met behulp van Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elke abonnements-ID die u toewijst. U kunt Maxi maal 10 abonnements-Id's hand matig of Maxi maal 10.000 abonnement-Id's toevoegen met behulp van een. CSV-bestand. Azure-abonnements-Id's worden weer gegeven als GUID'S en letters moeten kleine letters zijn.

> [!NOTE]
> Als u een privé-abonnement publiceert, kunt u de zicht baarheid later wijzigen in openbaar. Wanneer u echter een openbaar abonnement publiceert, kunt u de zicht baarheid niet wijzigen in privé.

Voor oplossings sjabloon plannen kunt u het abonnement ook verbergen vanuit Azure Marketplace. U kunt dit doen als het plan alleen indirect via een andere oplossings sjabloon of beheerde toepassing wordt geïmplementeerd.

> [!NOTE]
> Privé plannen worden niet ondersteund met Azure-abonnementen die zijn gemaakt via een wederverkoper van het Cloud Solution Provider-programma (CSP).

Zie voor meer informatie [persoonlijke aanbiedingen in micro soft Commercial Marketplace](private-offers.md).

## <a name="next-steps"></a>Volgende stappen

- [Een Azure-toepassings aanbieding maken in de commerciële Marketplace](create-new-azure-apps-offer.md)