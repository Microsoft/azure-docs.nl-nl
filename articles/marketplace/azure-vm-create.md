---
title: Maak een aanbieding voor de virtuele machine op Azure Marketplace.
description: Meer informatie over het maken van een aanbieding voor de virtuele machine in micro soft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ffc09daa15e742ca2b5b8a2fa9323e33fe317c60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200394"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Een aanbieding voor een virtuele machine maken op Azure Marketplace

In dit artikel wordt beschreven hoe u een Azure virtual machine-aanbieding maakt voor [Azure Marketplace](https://azuremarketplace.microsoft.com/). Het biedt zowel Windows-als op Linux gebaseerde virtuele machines die een besturings systeem, een virtuele harde schijf (VHD) en Maxi maal 16 gegevens schijven bevatten.

Voordat u begint, moet u [een commercieel Marketplace-account maken in het partner centrum](partner-center-portal/create-account.md). Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit nog niet hebt gedaan, controleert u [de aanbieding van een virtuele machine plannen](marketplace-virtual-machines.md). Hierin worden de technische vereisten voor uw virtuele machine uitgelegd en worden de gegevens en assets vermeld die u nodig hebt bij het maken van uw aanbieding.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het linkerdeel venster de optie **Commercial Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina **overzicht** **+ nieuwe**  >  **Azure virtual machine** aanbieden.

    ![Scherm afbeelding met de opties in het linkerdeel venster en de knop Nieuw aanbod.](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Nadat uw aanbieding is gepubliceerd, worden alle wijzigingen die u in het partner centrum aanbrengt, alleen op Azure Marketplace weer gegeven nadat u de aanbieding opnieuw hebt gepubliceerd. Zorg ervoor dat u een aanbieding altijd opnieuw publiceert nadat u de wijzigingen hebt aangebracht.

Voer een **aanbiedings-id** in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Azure Marketplace-aanbieding en in Azure PowerShell en de Azure CLI, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. De ID kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1** invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Voer een **alias** voor de aanbieding in. De aanbiedings alias is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt op Azure Marketplace. Dit wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.

Selecteer **maken** om de aanbieding te genereren en door te gaan. Het partner centrum opent de pagina voor het instellen van de **aanbieding** .

## <a name="enable-a-test-drive-optional"></a>Een test drive inschakelen (optioneel)

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen gedurende een vast aantal uur toegang te geven tot een vooraf geconfigureerde omgeving. Het bieden van een test drive resulteert in een verhoogde conversie frequentie en genereert uiterst gekwalificeerde leads. Zie [Wat is een test drive?](./what-is-test-drive.md)voor meer informatie over test stations.

> [!TIP]
> Een test drive wijkt af van een gratis proef versie. U kunt een test drive, een gratis proef versie of beide aanbieden. Ze bieden klanten uw oplossing voor een vaste periode. Een test drive bevat echter ook een zelf doorgeleide rond leiding door de belangrijkste functies en voor delen van uw product die worden getoond in een scenario met een praktijk implementatie.

Als u een test drive wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. U gaat de test drive later configureren. Met test drive is het configureren van een CRM vereist (Zie de volgende sectie).

## <a name="configure-customer-leads-management"></a>Beheer van klanten leads configureren

Wanneer u een aanbieding publiceert naar de commerciële Marketplace met het partner centrum, sluit u deze aan op het CRM-systeem (Customer Relationship Management). Zo kunt u contact gegevens van klanten ontvangen zodra iemand interesse in of gebruikt voor uw product. Verbinding maken met een CRM is vereist als u een test drive wilt inschakelen (Zie de voor gaande sectie). Anders is het maken van een verbinding met een CRM optioneel.

1. Selecteer onder **leads van klanten** de koppeling **verbinding maken** .
1. Selecteer in het dialoog venster **verbindings Details** een doel voor de lead.
1. Vul de velden in die worden weer gegeven. Raadpleeg de volgende artikelen voor gedetailleerde stappen:

   - [Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Uw aanbieding configureren voor het verzenden van leads naar Dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (voorheen Dynamics CRM Online)
   - [Uw aanbieding configureren voor het verzenden van leads naar een HTTPS-eind punt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Uw aanbieding configureren voor het verzenden van leads naar Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Uw aanbieding configureren voor het verzenden van leads naar Sales Force](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Als u de configuratie die u hebt ingevoerd, wilt valideren, selecteert u de koppeling **valideren** .
1. Selecteer **Verbinding maken**.

Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad in het menu links in de Navigator, **Eigenschappen**.

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van de aanbieding van virtuele machines configureren](azure-vm-create-properties.md)
- [Best practices voor aanbiedingsvermeldingen](gtm-offer-listing-best-practices.md)