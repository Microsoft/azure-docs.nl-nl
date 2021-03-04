---
title: 'Zelfstudie: Toegang tot uw privécloud'
description: Meer informatie over het openen van een Azure VMware Solution-privécloud
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: 456767a9edd78a70a0aba45c7b44a2150a2217a1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045000"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Zelfstudie: Toegang tot een privécloud van Azure VMware Solution

U kunt met Azure VMware Solution geen privécloud beheren met uw on-premises vCenter. U moet via een jumpbox aanvullende instellingen en verbindingen met een lokaal vCenter-exemplaar uitvoeren. 

In deze zelfstudie maakt u een jumpbox in de resourcegroep die u hebt gemaakt in de [vorige zelfstudie](tutorial-configure-networking.md) en meldt u zich aan bij vCenter. De jumpbox een virtuele Windows-machine in hetzelfde virtuele netwerk dat u hebt gemaakt.  Het biedt toegang tot vCenter en NSX Manager. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken om verbinding te maken met vCenter
> * Aanmelden bij vCenter vanaf uw virtuele machine

## <a name="create-a-new-windows-virtual-machine"></a>Een nieuwe virtuele Windows-machine maken

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Verbinding maken met het lokale vCenter van uw privécloud

1. Meld u vanuit de jumpbox aan bij de vSphere-client met VMware vCenter SSO met een gebruikersnaam voor de cloudbeheerder en controleer of de gebruikersinterface correct wordt weergegeven.

1. Selecteer in de Azure Portal uw privécloud en **beheer** vervolgens  >  **identiteit**. 

   De URL's en gebruikersreferenties voor vCenter en NSX-T-beheer van de privécloud worden weergegeven.

   >[!TIP]
   >Selecteer **een nieuw wacht woord genereren** om nieuwe vCenter-en NSX-T-wacht woorden te genereren.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="URL's en referenties weergeven van vCenter en NSX-beheer van de privécloud." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. Ga naar de virtuele machine die u in de vorige stap hebt gemaakt en maak verbinding met de virtuele machine. 

   Zie [Verbinding maken met een virtuele machine](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) voor meer informatie als u hulp nodig hebt om verbinding te maken met een virtuele machine.

1. Open een browser in de virtuele Windows-machine en navigeer op twee tabbladen naar de URL's van vCenter en NSX-T-beheer. 

1. Voer op het tabblad vCenter de `cloudadmin@vmcp.local`-gebruikersreferenties in uit de vorige stap.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Aanmelden bij vCenter van de privécloud." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-portal." border="true":::

1. Meld u op het tweede tabblad van de browser aan bij NSX-T-beheer.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Meld u op het tweede tabblad van de browser aan bij NSX-T-beheer." border="true":::



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken om verbinding te maken met vCenter
> * Aanmelden bij vCenter vanaf uw virtuele machine

Ga door naar de volgende zelfstudie voor meer informatie over het instellen van lokaal beheer voor uw particuliere cloudclusters.

> [!div class="nextstepaction"]
> [Een virtueel netwerk maken](tutorial-configure-networking.md)


