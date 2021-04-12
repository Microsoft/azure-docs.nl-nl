---
title: Een aanbieding voor een virtuele machine plannen-micro soft Commercial Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een aanbieding van een virtuele machine naar Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 6e391ca79c9dcf9ab533a7e67961c654b5f8630b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103365"
---
# <a name="plan-a-virtual-machine-offer"></a>Een aanbieding voor een virtuele machine plannen

In dit artikel worden de verschillende opties en vereisten beschreven voor het publiceren van een virtuele machine (VM)-aanbieding naar de commerciële Marketplace. VM-aanbiedingen zijn aanbiedingen die via Azure Marketplace kunnen worden geïmplementeerd en gefactureerd.

Voordat u begint, [maakt u een commercieel Marketplace-account in partner centrum](create-account.md) en zorgt u ervoor dat uw account is inge schreven in het commerciële Marketplace-programma.

### <a name="technical-fundamentals"></a>Technische basis principes

Het proces voor het ontwerpen, bouwen en testen van aanbiedingen vergt tijd en vereist expertise in zowel het Azure-platform als de technologieën die worden gebruikt om uw aanbieding te bouwen. Uw technische team moet een werk ervaring hebben met [Azure-virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking#networking), en met het [ontwerp en de architectuur van Azure-toepassingen](https://azure.microsoft.com/solutions/architecture/). Raadpleeg de volgende aanvullende technische bronnen: 

- Zelfstudies
  - [Virtuele Linux-machines](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Virtuele Windows-machines](../virtual-machines/windows/tutorial-manage-vm.md)

- Voorbeelden
  - [Azure CLI-voor beelden voor Linux Vm's](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell voor Linux-Vm's](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Azure CLI-voor beelden voor Windows-Vm's](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell voor Windows-Vm's](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Technische vereisten

VM-aanbiedingen hebben de volgende technische vereisten:

- U moet één virtuele harde schijf (VHD) van het besturings systeem voorbereiden. Vhd's met gegevens schijf zijn optioneel. Dit wordt hieronder nader beschreven.
- De klant kan uw aanbieding op elk gewenst moment annuleren.
- U moet ten minste één abonnement maken voor uw aanbieding. De prijs van uw abonnement is gebaseerd op de [licentie optie](#licensing-options) die u selecteert.
   > [!IMPORTANT]
   > Elke VM-installatie kopie in een plan moet hetzelfde aantal gegevens schijven hebben.

Een VM bevat twee onderdelen:

- **VHD** – bevat het besturings systeem en de oplossing die met uw aanbieding wordt geïmplementeerd. Het proces van het voorbereiden van de VHD verschilt, afhankelijk van het feit of het een Linux-, Windows-of op basis gebaseerde VM betreft.
- **Vhd's met gegevens schijf** (optioneel): toegewezen, permanente opslag voor een virtuele machine. Gebruik niet de VHD met het besturings systeem (bijvoorbeeld station C:) om permanente gegevens op te slaan. 
    - U kunt Maxi maal 16 gegevens schijven toevoegen.
    - Gebruik één VHD per gegevens schijf, zelfs als de schijf leeg is.

    > [!NOTE]
    > Ongeacht welk besturings systeem u gebruikt, voegt u alleen het minimum aantal gegevens schijven toe dat nodig is voor de oplossing. Klanten kunnen geen schijven verwijderen die deel uitmaken van een installatie kopie op het moment van de implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na de implementatie.

Zie [een virtuele machine maken met behulp van een goedgekeurde basis](azure-vm-create-using-approved-base.md) of [een virtuele machine maken met uw eigen installatie kopie](azure-vm-create-using-own-image.md)voor gedetailleerde instructies voor het voorbereiden van uw technische activa.

## <a name="preview-audience"></a>Voor beeld van doel groep

Een preview-doel groep heeft toegang tot uw VM-aanbieding voordat u Live in azure Marketplace hebt gepubliceerd, zodat u de end-to-end-functionaliteit kunt testen voordat u deze live publiceert. Op de pagina **voor beeld van doel groep** kunt u een beperkte preview-doel groep definiëren. 

> [!NOTE]
> Een preview-doel groep wijkt af van een privé-abonnement. Een privé-abonnement is één die u alleen beschikbaar maakt voor een specifieke doel groep. Zo kunt u een aangepast plan met specifieke klanten onderhandelen. Zie de volgende sectie: plannen voor meer informatie.

U kunt uitnodigingen verzenden naar e-mail adressen van micro soft-accounts (MSA) of Azure Active Directory (Azure AD). Voeg Maxi maal 10 e-mail adressen hand matig toe of importeer Maxi maal 20 met een CSV-bestand. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel groep definiëren om eventuele wijzigingen of updates naar uw aanbieding te testen.

## <a name="plans-and-pricing"></a>Abonnementen en prijzen

Voor VM-aanbiedingen is ten minste één abonnement vereist. Een plan definieert het bereik en de limieten van de oplossing en de bijbehorende prijzen. U kunt meerdere plannen maken voor uw aanbieding om uw klanten verschillende technische en licentie opties te geven, evenals gratis proef versies. Bekijk [plannen en prijzen voor commerciële Marketplace-aanbiedingen](plans-pricing.md) voor algemene richt lijnen voor abonnementen, waaronder prijs modellen, gratis proef versies en privé abonnementen. 

Vm's zijn volledig geschikt voor Commerce, met behulp van betalen per gebruik of uw eigen licentie modellen (BYOL). Micro soft host de commerce trans actie en factureert uw klant namens u. U krijgt het voor deel van het gebruik van de voorkeurs betalings relatie tussen uw klant en micro soft, inclusief alle Enter prise-overeenkomsten. Zie voor meer informatie [commerciële Marketplace Transact-mogelijkheden](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> De Azure-voor uitbetaling (voorheen monetaire toezeg ging) die is gekoppeld aan een Enterprise Overeenkomst, kan worden gebruikt voor het Azure-gebruik van uw VM, maar niet op basis van uw software licentie kosten.

### <a name="licensing-options"></a>Licentie opties

Tijdens de voor bereiding voor het publiceren van een nieuwe VM-aanbieding, moet u beslissen welke licentie optie u moet kiezen. Hiermee wordt bepaald welke aanvullende informatie u nodig hebt om later te voorzien in het maken van uw aanbieding in Partner Center.

Dit zijn de beschik bare licentie opties voor VM-aanbiedingen:

| Optie voor licentieverlening | Transactie proces |
| --- | --- |
| Gratis proefversie | Bied uw klanten een gratis proef versie van drie of zes maanden aan. |
| Station testen | Met deze optie kunnen uw klanten Vm's evalueren zonder extra kosten. Ze hoeven geen bestaande Azure-klant te zijn om te kunnen samen werken met de proef ervaring. Zie [Wat is een test drive?](./what-is-test-drive.md) voor meer informatie. |
| BYOL (Bring Your Own License) | Met de optie uw eigen licentie nemen kunnen uw klanten bestaande software licenties aan Azure bieden.\* |
| Op basis van gebruik | Met deze optie kunt u uw klanten per uur betalen, ook wel betalen naar gebruik. |
| Interactieve demo  | Geef uw klanten een begeleide ervaring met uw oplossing met behulp van een interactieve demonstratie. Het voor deel is dat u een proef ervaring kunt bieden zonder dat u een ingewikkelde configuratie van uw complexe oplossing hoeft te bieden. |
|

\* Als uitgever ondersteunt u alle aspecten van de software licentie transactie, inclusief (maar niet beperkt tot) order, uitvoering, meting, facturering, facturering, betaling en verzameling.

In het volgende voor beeld ziet u een VM-aanbieding in azure Marketplace met prijzen op basis van gebruik.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Voor beeld van een VM-aanbod scherm.":::

### <a name="private-plans"></a>Privé plannen

U kunt de detectie en implementatie van uw virtuele machine beperken tot een specifieke set klanten door de installatie kopie en prijzen als een persoonlijk abonnement te publiceren. Met persoonlijke abonnementen kunt u exclusieve aanbiedingen voor uw dichtstbijzijnde klanten maken en aangepaste software en voor waarden aanbieden. Met de aangepaste voor waarden kunt u een groot aantal scenario's markeren, met inbegrip van veld LED-deals met gespecialiseerde prijzen en voor waarden, en vroegtijdige toegang tot beperkte release software. Met persoonlijke abonnementen kunt u specifieke prijzen of producten aan een beperkt aantal klanten geven.

Zie voor meer informatie [Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen](plans-pricing.md) en [persoonlijke aanbiedingen in micro soft Commercial Marketplace](private-offers.md).

## <a name="test-drive"></a>Station testen

U kunt ervoor kiezen om een test drive in te scha kelen voor uw VM. Test stations geven klanten een vast aantal uur toegang tot een vooraf geconfigureerde omgeving. U kunt test stations voor elke publicatie optie inschakelen, maar deze functie heeft aanvullende vereisten. Zie [Wat is een test drive?](what-is-test-drive.md)voor meer informatie over test stations. Zie [technische configuratie testen](test-drive-technical-configuration.md)voor meer informatie over het configureren van verschillende soorten test stations.

> [!TIP]
> Een test drive wijkt af van een [gratis proef versie](plans-pricing.md#free-trials). U kunt een test drive, een gratis proef versie of beide aanbieden. Ze bieden uw klanten uw oplossing voor een vaste periode. Een test drive bevat echter ook een zelf doorgeleide rond leiding door de belangrijkste functies en voor delen van uw product die worden getoond in een scenario met een praktijk implementatie.

## <a name="customer-leads"></a>Leads van klanten

U moet uw aanbieding verbinden met het CRM-systeem (Customer Relationship Management) om klant gegevens te verzamelen. De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, de ID en de online winkel waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. De commerciële Marketplace ondersteunt een groot aantal CRM-systemen, samen met de optie voor het gebruik van een Azure-tabel of het configureren van een HTTPS-eind punt met behulp van energie automatisering.

U kunt op elk gewenst moment een CRM-verbinding toevoegen of wijzigen tijdens of na het maken van de aanbieding. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

## <a name="legal-contracts"></a>Juridische contracten

Micro soft biedt een standaard contract dat u voor uw aanbiedingen kunt gebruiken in de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het Standard-contract aanbiedt, hoeven klanten slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voor waarden te maken.

Als u kiest voor het gebruik van het standaard contract, hebt u de mogelijkheid om universele wijzigings voorwaarden toe te voegen en Maxi maal 10 aangepaste wijzigingen aan te brengen in het standaard contract. U kunt ook uw eigen voor waarden gebruiken in plaats van het standaard contract. U gaat deze details beheren op de pagina **Eigenschappen** . Zie voor gedetailleerde informatie het [standaard contract voor micro soft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor de commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. Het is een ' or '-scenario. U kunt uw oplossing aanbieden onder het standaard contract of uw eigen voor waarden. Als u de voor waarden van het standaard contract wilt wijzigen, kunt u dit doen via de standaard wijzigingen in contracten.

## <a name="cloud-solution-providers"></a>Providers van cloudoplossingen

Wanneer u uw aanbieding in Partner Center maakt, wordt het tabblad **verkopen via csp's** weer geven. Met deze optie kunnen partners die deel uitmaken van het CSP-programma (Microsoft Cloud Solution Providers), de virtuele machine door verkopen als onderdeel van een gebundelde aanbieding. Alle BYOL-abonnementen (voor het maken van uw eigen licentie) worden automatisch aangemeld bij het programma. U kunt er ook voor kiezen om uw keuze te maken in uw niet-BYOL plannen. Zie het [Cloud Solution Provider-programma](cloud-solution-providers.md) voor meer informatie. 

> [!NOTE]
> De opt-in voor de Cloud Solution Provider (CSP)-partner kanaal is nu beschikbaar. Zie [**Cloud Solution Providers**](./cloud-solution-providers.md)(Engelstalig) voor meer informatie over het marketing uw aanbod via micro soft CSP-partner kanalen.

## <a name="next-steps"></a>Volgende stappen

- [Een aanbieding voor een virtuele machine maken op Azure Marketplace](azure-vm-create.md)
- [Maak een virtuele machine met behulp van een goedgekeurde basis](azure-vm-create-using-approved-base.md) of [Maak een virtuele machine met behulp van uw eigen installatie kopie](azure-vm-create-using-own-image.md).
- [Best practices voor aanbiedingsvermeldingen](gtm-offer-listing-best-practices.md)
