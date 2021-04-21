---
title: Maak een virtuele-machineaanbieding op Azure Marketplace.
description: Meer informatie over het maken van een aanbieding voor virtuele machines in de commerciële marketplace van Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 04/08/2021
ms.openlocfilehash: f0c1d9d528ed4fbf61786042fb6fb34f05fec5d5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812589"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Een aanbieding voor virtuele machines maken op Azure Marketplace

In dit artikel wordt beschreven hoe u een azure-aanbieding voor virtuele machines maakt [voor Azure Marketplace](https://azuremarketplace.microsoft.com/). Het heeft betrekking op virtuele windows- en Linux-machines die een besturingssysteem, een virtuele harde schijf (VHD) en maximaal 16 gegevensschijven bevatten.

Voordat u begint, [maakt u een commerciële marketplace-account in Partner Center](create-account.md). Zorg ervoor dat uw account is ingeschreven bij het commerciële marketplace-programma.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit nog niet hebt gedaan, bekijkt u [Aanbieding voor virtuele machines plannen.](marketplace-virtual-machines.md) Hier worden de technische vereisten voor uw virtuele machine uitgelegd en worden de informatie en assets vermeld die u nodig hebt wanneer u uw aanbieding maakt.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan [bij Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het linkerdeelvenster Commerciële  >  **marketplace-overzicht.**
3. Selecteer op **de pagina** Overzicht + **Nieuwe aanbieding** Azure  >  **Virtual Machine.**

    ![Schermopname van de menuopties in het linkerdeelvenster en de knop Nieuwe aanbieding.](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Nadat uw aanbieding is gepubliceerd, worden alle wijzigingen die u in de aanbieding Partner Center pas weergegeven Azure Marketplace u de aanbieding opnieuw hebt gepubliceerd. Zorg ervoor dat u een aanbieding altijd opnieuw publiceren nadat u wijzigingen hebt aangebracht.

Voer een **aanbiedings-id in.** Dit is een unieke id voor elke aanbieding in uw account.

- Deze id is zichtbaar voor klanten in het webadres voor de Azure Marketplace-aanbieding en in Azure PowerShell en de Azure CLI, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. De id kan afbreekstreepingstekens en onderstrepingstekens bevatten, maar geen spaties en mag maximaal 50 tekens bevatten. Als u bijvoorbeeld **test-offer-1** op geeft, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-id kan niet worden gewijzigd nadat u **Maken hebt geselecteerd.**

Voer een **aanbiedingsalias in.** De alias van de aanbieding is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt op Azure Marketplace. Deze verschilt van de naam van de aanbieding en andere waarden die aan klanten worden weergegeven.

Selecteer **Maken om** de aanbieding te genereren en door te gaan. Partner Center wordt de pagina **Aanbieding instellen** geopend.

## <a name="enable-a-test-drive-optional"></a>Een test drive inschakelen (optioneel)

Een test drive is een uitstekende manier om uw aanbieding aan potentiële klanten te presenteren door hen voor een vast aantal uren toegang te geven tot een vooraf geconfigureerde omgeving. Het aanbieden test drive resulteert in een verhoogde conversiesnelheid en genereert zeer gekwalificeerde leads. Zie Voor meer informatie over teststations [Wat is een test drive?](./what-is-test-drive.md).

> [!TIP]
> Een test drive verschilt van een gratis proefversie. U kunt een test drive, gratis proefversie of beide. Beide bieden klanten uw oplossing voor een vaste periode. Maar een test drive bevat ook een praktijkgestuurde rondleiding door de belangrijkste functies en voordelen van uw product, die in een praktijkscenario worden gedemonstreerd.

Als u een test drive wilt inschakelen, selecteert u het selectievakje **Een test drive** inschakelen. U configureert de test drive later. Met test drive is het configureren van een CRM vereist (zie de volgende sectie).

## <a name="configure-customer-leads-management"></a>Leadsbeheer van klanten configureren

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

Selecteer **Concept opslaan** voordat u verdergaat met het volgende tabblad in het menu aan de linkerkant, **Eigenschappen.**

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van de aanbieding voor virtuele machines configureren](azure-vm-create-properties.md)
- [Best practices voor aanbiedingsvermeldingen](gtm-offer-listing-best-practices.md)