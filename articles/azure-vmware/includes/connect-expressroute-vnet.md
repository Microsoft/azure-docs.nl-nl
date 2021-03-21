---
title: ExpressRoute verbinden met de gateway voor het virtuele netwerk
description: Stappen om ExpressRoute te verbinden met de gateway voor het virtuele netwerk.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494347"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Ga naar de privécloud die u hebt gemaakt in de zelfstudie [vSphere-cluster implementeren in Azure](../tutorial-create-private-cloud.md). Selecteer bij **Beheren** de optie **Connectiviteit** en selecteer het tabblad **ExpressRoute** .

1. Kopieer de autorisatiesleutel. Als er geen autorisatiesleutel is, moet u er een maken door **+ Een autorisatiesleutel aanvragen** te selecteren.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Kopieer de autorisatiesleutel. Als er geen autorisatiesleutel is, moet u er een maken door + Een autorisatiesleutel aanvragen" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png"::: te selecteren.

1. Ga naar de gateway voor het virtuele netwerk die u in de vorige stap hebt gemaakt en selecteer onder **Instellingen** de optie **Verbindingen**. Selecteer op de pagina **Verbindingen** de optie **+ Toevoegen**.

1. Geef op de pagina **Verbinding toevoegen** waarden op voor de velden en selecteer **OK**. 

   | Veld | Waarde |
   | --- | --- |
   | **Naam**  | Geef een naam voor de verbinding op.  |
   | **Verbindingstype**  | Selecteer **ExpressRoute**.  |
   | **Autorisatie inwisselen**  | Zorg ervoor dat dit selectievakje is ingeschakeld.  |
   | **Gateway voor een virtueel netwerk** | De gateway voor het virtuele netwerk die u eerder hebt gemaakt.  |
   | **Autorisatiesleutel**  | Kopieer en plak de autorisatiesleutel vanuit het tabblad ExpressRoute voor de resourcegroep. |
   | **URI van peercircuit**  | Kopieer en plak de ExpressRoute-id vanuit het tabblad ExpressRoute voor uw resourcegroep.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Scherm opname van de pagina verbinding toevoegen om ExpressRoute te verbinden met de virtuele netwerk gateway.":::

De verbinding tussen het ExpressRoute-circuit en uw virtuele netwerk wordt gemaakt.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Scherm opname van de gateway verbindingen van het virtuele netwerk.":::