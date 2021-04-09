---
title: Service-eindpunt beleid maken en koppelen-Azure Portal
titlesuffix: Azure Virtual Network
description: In dit artikel leert u hoe u en het bijbehorende service-eindpunt beleid kunt instellen met behulp van de Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004956"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Service-eindpunt beleid maken, wijzigen of verwijderen met behulp van de Azure Portal

Met service-eindpunt beleid kunt u virtueel netwerk verkeer filteren op specifieke Azure-resources, via service-eind punten. Als u niet bekend bent met het service-eindpunt beleid, raadpleegt u overzicht van het [service-eindpunt beleid](virtual-network-service-endpoint-policies-overview.md) voor meer informatie.

 In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een service-eindpunt beleid maken
> * Een beleids definitie voor service-eind punten maken
> * Een virtueel netwerk met een subnet maken
> * Een service-eindpunt beleid aan een subnet koppelen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Een service-eindpunt beleid maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Typ ' service-eindpunt beleid ' in het deel venster zoeken en selecteer **service-eindpunt beleid** en selecteer vervolgens **maken**.

![Service-eindpunt beleid maken](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Voer de volgende informatie in de **basis beginselen** in of Selecteer deze. 

   - Abonnement: Selecteer uw abonnement voor beleid
   - Resource groep: Selecteer **nieuwe maken** en voer *myResourceGroup* in
   - Naam: myEndpointPolicy
   - Locatie: VS-Centraal
 
   ![Basis beginselen van het service-eindpunt beleid maken](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Selecteer **+ toevoegen** onder **resources** en voer de volgende informatie in of Selecteer deze in het deel venster **resources toevoegen** .

   - Service: alleen **micro soft. Storage** is beschikbaar met service-eindpunt beleid
   - Bereik: Selecteer één van de **afzonderlijke** accounts, **alle rekeningen in het abonnement** en **alle accounts in de resource groep**
   - Abonnement: Selecteer uw abonnement voor het opslag account. Beleids-en opslag accounts kunnen zich in verschillende abonnementen bevindt.
   - Resource groep: Selecteer de resource groep. Vereist als het bereik is ingesteld als ' alle accounts in de resource groep ' of ' Eén account '.  
   - Resource: Selecteer uw Azure Storage Resource onder het geselecteerde abonnement of de resource groep.
   - Klik onderaan op de knop **toevoegen** om de resource toe te voegen

   ![Definitie van het service-eindpunt beleid-resource](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Voeg meer resources toe door de bovenstaande stappen indien nodig te herhalen

5. Optioneel: Typ of selecteer de volgende gegevens in **Tags**:
   
   - Sleutel: Selecteer uw sleutel voor het beleid. Bijvoorbeeld: afd     
   - Waarde: Voer een waardepaar in voor de sleutel. Ex: Finance

6. Selecteer **Controleren + maken**. Valideer de gegevens en klik op **maken**. Klik op **vorige** om verdere bewerkingen uit te voeren. 

   ![Definitieve validaties van het service-eindpunt beleid maken](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Eindpunt beleid weer geven 

1. Begin met het typen van *service-eindpunt beleid* in het vak *alle services* in de portal. Selecteer **service-eindpunt beleid**.
2. Onder **abonnementen** selecteert u uw abonnement en resource groep, zoals wordt weer gegeven in de volgende afbeelding:

   ![Beleid weer geven](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selecteer het beleid en klik op **beleids definities** om meer beleids definities weer te geven of toe te voegen.

   ![Beleids definities weer geven](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selecteer **gekoppelde subnetten** om de subnetten te bekijken waaraan het beleid is gekoppeld. Als er nog geen subnet is gekoppeld, volgt u de instructies in de volgende stap.

   ![Gekoppelde subnetten](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Een beleid aan een subnet koppelen

>[!WARNING] 
> Zorg ervoor dat alle bronnen die toegankelijk zijn vanaf het subnet, worden toegevoegd aan de beleids definitie voordat u het beleid aan het opgegeven subnet koppelt. Wanneer het beleid is gekoppeld, wordt alleen toegang tot de *weer gegeven resources toestaan* via service-eind punten toegestaan. 
>
> Zorg er ook voor dat er geen beheerde Azure-Services bestaan in het subnet dat is gekoppeld aan het service-eindpunt beleid

- Voordat u een beleid aan een subnet kunt koppelen, moet u een virtueel netwerk en een subnet maken. Raadpleeg het artikel [een Virtual Network maken](./quick-create-portal.md) voor meer informatie.

- Zodra u het virtuele netwerk en het subnet hebt ingesteld, moet u Virtual Network Service-eind punten voor Azure Storage configureren. Selecteer op de Blade Virtual Network **service-eind punten** en selecteer in het volgende deel venster **micro soft. Storage** en selecteer onder **subnetten** het gewenste VNet of subnet

- Nu kunt u het service-eindpunt beleid selecteren in de vervolg keuzelijst van het bovenstaande deel venster als u al een service-eindpunt beleid hebt gemaakt voordat u het service-eind punt voor het subnet configureert, zoals hieronder wordt weer gegeven

    ![Subnet koppelen tijdens het maken van service-eind punt](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- OF als u een service-eindpunt beleid koppelt nadat de service-eind punten al zijn geconfigureerd, kunt u ervoor kiezen om het subnet te koppelen vanuit de Blade van het service-eindpunt beleid door te navigeren naar het deel venster **gekoppelde subnetten** , zoals hieronder wordt weer gegeven

    ![Subnet koppelen via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Toegang tot Azure Storage resources in alle regio's wordt beperkt door het service-eindpunt beleid van dit subnet.

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een service-eindpunt beleid gemaakt en gekoppeld aan een subnet. Zie [overzicht van service-eindpunt beleid](virtual-network-service-endpoint-policies-overview.md) voor meer informatie over beleids regels voor service-eind punten.
