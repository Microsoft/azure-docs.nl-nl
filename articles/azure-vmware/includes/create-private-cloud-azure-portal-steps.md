---
title: Een Azure VMware Solution-privécloud maken
description: Stappen voor het maken van een privécloud van Azure VMware-oplossing met behulp van de Azure Portal.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 983dccfaa9ea43955bfecc68bbbe432c579d51d1
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653127"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Een nieuwe resource maken**. Typ in het tekstvak **Marketplace doorzoeken** `Azure VMware Solution` en selecteer **Azure VMware Solution** uit de lijst. Selecteer **Maken** in het venster **Azure VMware Solution**.

1. Voer op het tabblad **Basisinstellingen** waarden in voor de velden. De volgende tabel geeft de eigenschappen weer voor de velden.

   | Veld   | Waarde  |
   | ---| --- |
   | **Abonnement** | Het abonnement dat u wilt gebruiken voor de implementatie.|
   | **Resourcegroep** | De resourcegroep voor uw privécloud-resources. |
   | **Locatie** | Selecteer een locatie, zoals **VS-Oost**.|
   | **Resourcenaam** | De naam van uw Azure VMware Solution-privécloud. |
   | **SKU** | Selecteer de volgende SKU-waarde: AV36 |
   | **Hosts** | Dit is het aantal hosts dat aan het privécloudcluster moet worden toegevoegd. De standaardwaarde is 3, maar kan na de implementatie worden verhoogd of verlaagd.  |
   | **Beheerderswachtwoord van vCenter** | Voer een wachtwoord voor de cloudbeheerder in. |
   | **Manager-wachtwoord van NSX-T** | Voer een NSX-T-beheerderswachtwoord in. |
   | **Adres blokkeren** | Voer een IP-adresblok in voor het CIDR-netwerk voor de privécloud, zoals 10.175.0.0/22. |
   | **Virtual Network** | Selecteer een virtueel netwerk of maak een nieuw exemplaar voor de Azure VMware Solution-privécloud.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Voer op het tabblad Basisinstellingen waarden in voor de velden." border="true":::

1. Als dit gereed is, selecteert u **Controleren + maken**. Controleer de ingevoerde gegevens in het volgende scherm. Als de gegevens juist zijn, selecteert u **Maken**.

   > [!NOTE]
   > Deze stap duurt ongeveer twee uur. 

1. Controleer of de implementatie is gelukt. Ga naar de resourcegroep die u hebt gemaakt en selecteer uw privécloud.  U ziet de status **Geslaagd** wanneer de implementatie is voltooid. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Controleer of de implementatie is gelukt." border="true":::