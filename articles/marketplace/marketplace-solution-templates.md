---
title: Publicatie handleiding voor Azure Applications Solution-sjabloon aanbiedingen-Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van oplossings sjablonen op Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: c7074981c8491460d6f2a8e7d40d086f261dfeb3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879340"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Publicatie handleiding voor Azure Applications Solution Temp late-aanbiedingen

In dit artikel worden de vereisten voor het publiceren van oplossingen sjablonen beschreven. Dit is een manier om Azure-aanbiedingen in azure Marketplace te publiceren. Voor het type van de oplossings sjabloon is een [Azure Resource Manager sjabloon (arm-sjabloon)](../azure-resource-manager/templates/overview.md) vereist om uw oplossings infrastructuur automatisch te implementeren.

Gebruik het aanbiedings type van de Azure Application *Solution-sjabloon* onder de volgende voor waarden:

- Voor uw oplossing is aanvullende implementatie-en configuratie automatisering nodig tot meer dan één virtuele machine (VM), zoals een combi natie van Vm's, netwerken en opslag resources.
- Uw klanten gaan zelf de oplossing beheren.

De aanbiedings optie die een klant voor dit aanbiedings type ziet, is *nu downloaden*.

## <a name="requirements-for-solution-template-offers"></a>Vereisten voor oplossingen sjabloon bieden

| **Vereisten** | **Details**  |
| ---------------  | -----------  |
|Facturering en meting    |  Oplossingen voor oplossings sjablonen zijn geen transactie aanbiedingen, maar ze kunnen worden gebruikt voor het implementeren van betaalde VM-aanbiedingen die worden gefactureerd via micro soft Commercial Marketplace. De resources die de ARM-sjabloon implementeren van de oplossing zijn ingesteld in het Azure-abonnement van de klant. Voor betalen per gebruik-virtuele machines wordt gehandeld met de klant via micro soft en gefactureerd via het Azure-abonnement van de klant.<br/> Voor een BYOL-facturering (meebrengen van uw eigen licentie), hoewel kosten voor de infra structuur van micro soft billing die in het abonnement van de klant worden gemaakt, kunt u uw software licentie kosten rechtstreeks aan de klant door geven.   |
|Azure-compatibele virtuele harde schijf (VHD)  |   Vm's moeten zijn gebouwd op Windows of Linux. Zie voor meer informatie: <ul> <li>[Maak een Azure-toepassings aanbieding](./create-new-azure-apps-offer.md) (voor Windows-vhd's).</li><li>[Linux-distributies die zijn goedgekeurd op Azure](../virtual-machines/linux/endorsed-distros.md) (voor Linux-vhd's).</li></ul> |
| Gebruikskenmerken van klanten | Het inschakelen van de toewijzing van klant gebruik is vereist voor alle oplossings sjablonen die op Azure Marketplace worden gepubliceerd. Zie voor meer informatie over het gebruik van de klant en hoe u deze functie inschakelt, de [klant gebruiks toewijzing van Azure-partners](./azure-partner-customer-usage-attribution.md).  |
| Beheerde schijven gebruiken | [Managed disks](../virtual-machines/managed-disks-overview.md) is de standaard optie voor persistente schijven van Infrastructure as a Service (IaaS) Vm's in Azure. U moet beheerde schijven gebruiken in oplossings sjablonen. <ul><li>Volg de richt lijnen in [Managed disks in azure Resource Manager Templates](../virtual-machines/using-managed-disks-template-deployments.md)en gebruik de voor [beelden](https://github.com/Azure/azure-quickstart-templates)om uw oplossings sjablonen bij te werken.<br><br> </li><li>Als u de VHD als een installatie kopie wilt publiceren in azure Marketplace, importeert u de onderliggende VHD van de beheerde schijven naar een opslag account met behulp van een van de volgende methoden:<ul><li>[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [De Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, leert u hoe u [uw Cloud activiteiten kunt verg Roten met Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registreren voor en werken in het partner centrum:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- Zie [een Azure-toepassings aanbieding maken](./create-new-azure-apps-offer.md) voor meer informatie.