---
title: Fortanix vertrouwelijk computing manager in een door Azure beheerde toepassing
description: Meer informatie over het implementeren van Fortanix vertrouwelijk computing Manager (CCM) in een beheerde toepassing in de Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563418"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix vertrouwelijk computing manager in een door Azure beheerde toepassing

In dit artikel wordt beschreven hoe u een toepassing implementeert die door Fortanix vertrouwelijk computing Manager wordt beheerd in de Azure Portal.

Fortanix is een software leverancier van derden met producten en services die zijn gebouwd boven op de Azure-infra structuur. Er zijn andere providers van derden die vergelijk bare Services voor vertrouwelijke computing aanbieden op Azure.

> [!NOTE]
>De producten waarnaar in dit document wordt verwezen, vallen niet onder het beheer van micro soft. Micro soft levert deze informatie alleen als een gemak en de verwijzing naar deze niet-micro soft-producten impliceert geen goed keuring door micro soft.

## <a name="prerequisites"></a>Vereisten

- Een persoonlijk docker-REGI ster om geconverteerde installatie kopieën van toepassingen te pushen.
- Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Een vertrouwelijke computing Manager implementeren via een door Azure beheerde toepassing

1. Ga naar de [Azure Portal](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure Portal.":::

2. Zoek in de zoek balk naar ' Fortanix vertrouwelijk computing Manager ' en zoek de Marketplace-vermelding voor Fortanix CCM. Selecteer **Fortanix vertrouwelijk computing manager in azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace-vermelding.":::

3. De pagina waarop u de CCM-beheerde toepassing maakt, wordt geopend. Selecteer **maken**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Toepassing maken.":::

4. Vul alle vereiste velden in.
   1. In de sectie Details van beheerde toepassing heeft het veld **beheerde resource groep** een standaard waarde die de gebruiker kan aanpassen als dat nodig is.
   2. Selecteer in het veld **regio** de optie **Australië-Oost**, **Australië-Zuidoost**, VS- **oost**, **vs-West 2**, **Europa-West**, **Europa-Noord**, **Canada-centraal**, **Canada-Oost** of **VS-Oost 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Vereiste velden":::

   Selecteer **controleren + maken** om de Fortanix CCM-beheerde toepassing te maken.

5. Bekijk de details en schakel het selectie vakje **Ik ga akkoord met de bovenstaande voor waarden** in nadat de validatie is geslaagd en selecteer vervolgens **maken** om de beheerde toepassing te maken.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Bekijk de details.":::

6. De Fortanix CCM-implementatie wordt gestart en er wordt gemeld dat de implementatie wordt uitgevoerd.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Voortgang van de implementatie.":::

7. Wanneer de implementatie is voltooid, selecteert u **naar de resource** knop en gaat u naar de pagina overzicht van geïmplementeerde CCM-beheerde toepassing om het reken knooppunt in te schrijven.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Scherm opname van een geslaagde implementatie in het Azure Portal.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Scherm afbeelding met een overzicht van de vertrouwelijke computing resource in de Azure Portal.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Het reken knooppunt registreren in Fortanix CCM

1. Selecteer **vertrouwelijk computing Manager** in het navigatie menu links. Meld u aan bij Fortanix CCM en maak een account zoals u ziet in **afbeelding 9**.

    Voor meer informatie over hoe u zich kunt aanmelden en hoe u een account maakt in CCM, raadpleegt u [CCM](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in)aan de slag.
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Scherm opname van de Fortanix vertrouwelijk computing Manager-aanmelding.":::
    
2. Als u het token voor samen voegen wilt ophalen uit de CCM-beheer console, selecteert u eerst de knop **knoop punt registreren** . Selecteer vervolgens in het venster knoop punt registreren de knop **kopiëren** om het token voor samen voegen te kopiëren.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Scherm opname van het ophalen van het token voor samen voegen.":::

3. Als u nu een knooppunt agent wilt inschrijven, selecteert u het tabblad **vertrouwelijk computing knoop punt agent** en selecteert u **toevoegen** om een CCM-knooppunt agent toe te voegen.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Scherm opname van het toevoegen van de knooppunt agent.":::

4.  Vul in het formulier CCM-knooppunt agent alle vereiste velden in. Plak het deelname token dat u hebt gekopieerd in stap 2 in het **deel token voor samen voegen**. Selecteer **controleren en verzenden** om dit te bevestigen.

    Zie [compute node registreren](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes)voor meer informatie over het inschrijven van een CCM Compute-knoop punt.
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Scherm opname van het registreren van het reken knooppunt.":::
    
5. Nadat de validatie is geslaagd, selecteert u **verzenden** om het maken van de knooppunt agent te volt ooien.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Scherm opname waarin de knooppunt agent wordt gemaakt.":::

6. Als u de implementatie status wilt controleren, gaat u naar het tabblad **overzicht** en selecteert u de koppeling **beheerde resource groep** .

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Scherm opname van het knoop punt dat wordt geregistreerd.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Scherm afbeelding waarin de implementatie status wordt gecontroleerd.":::

7. U ziet nu dat de implementatie status nog wordt uitgevoerd en duurt enkele minuten voordat de knooppunt agent is inge schreven.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Scherm opname van de implementatie die wordt uitgevoerd.":::

8. Wanneer de registratie van de knooppunt agent is geslaagd, wordt de status gewijzigd in geslaagd.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Scherm opname waarin de implementatie is geslaagd.":::

9. Ga nu in de door CCM beheerde toepassing naar de pagina's met reken knooppunten en u ziet dat het knoop punt zich in een **actieve** status bevindt en is inge schreven.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Scherm opname van het knoop punt dat is geregistreerd.":::

## <a name="clean-up-resources"></a>Resources opschonen

De gebruiker kan ook een CCM-knooppunt agent verwijderen van de pagina met de knoop punt voor vertrouwelijke computing. Als u de knooppunt agent wilt verwijderen, selecteert u de knoop punt agent en selecteert u de knop **verwijderen** op de bovenste balk.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Scherm afbeelding waarin de knooppunt agent wordt verwijderd.":::

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een knoop punt Inge schreven met behulp van een door Azure beheerde app naar de vertrouwelijke computing Manager van Fortanix. Met de knooppunt registratie kunt u de installatie kopie van de toepassing converteren zodat deze wordt uitgevoerd op een computer met een vertrouwelijke computing. Zie [Oplossingen op Virtual Machines](virtual-machine-solutions.md) voor meer informatie over virtuele machines met Confidential Computing in Azure.

Zie [Azure vertrouwelijk computing](overview.md)(Engelstalig) voor meer informatie over de aanbiedingen van de vertrouwelijk Computing van Azure.

Meer informatie over het uitvoeren van soort gelijke taken met andere aanbiedingen van derden op Azure, zoals [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) en [Scone](https://sconedocs.github.io).

