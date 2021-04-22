---
title: Een inhoudsbibliotheek maken voor het implementeren van VM's in Azure VMware Solution
description: Maak een inhoudsbibliotheek voor het implementeren van een VM in Azure VMware Solution privécloud.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: b27d2682d8799bec6b09a08e5063359113b20a88
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873879"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Een inhoudsbibliotheek maken voor het implementeren van VM's in Azure VMware Solution

In een inhoudsbibliotheek wordt inhoud in de vorm van bibliotheekitems opgeslagen en beheerd. Eén bibliotheekitem bestaat uit een of meer bestanden die u gebruikt voor het implementeren van virtuele machines (VM's). 

In dit artikel wordt de procedure voor het maken van een inhoudsbibliotheek doorlopen.  Vervolgens gaan we een VM implementeren met behulp van een ISO-afbeelding uit de inhoudsbibliotheek.

## <a name="prerequisites"></a>Vereisten

Een NSX-T-segment (logische switch) en een beheerde DHCP-service zijn vereist om deze zelfstudie te voltooien.  Zie voor meer informatie het [artikel DHCP beheren in Azure VMware Solution.](manage-dhcp.md)

## <a name="create-a-content-library"></a>Maak een inhoudsbibliotheek

1. Selecteer op de on-premises vSphere-client **Menu > Inhoudsbibliotheken**.

   ![Selecteer Menu-> Inhoudsbibliotheken](./media/content-library/vsphere-menu-content-libraries.png)

1. Selecteer de **knop Toevoegen** om een nieuwe inhoudsbibliotheek te maken.

   ![Selecteer de knop Toevoegen om een nieuwe inhoudsbibliotheek te maken.](./media/content-library/create-new-content-library.png)

1. Geef een naam op en bevestig het IP-adres van de vCenter-server en selecteer **Volgende**.

   ![Geef een naam en opmerkingen van uw keuze op en selecteer Volgende.](./media/content-library/new-content-library-step1.png)

1. Selecteer de **Lokale inhoudsbibliotheek** en selecteer **Volgende**.

   ![In dit voorbeeld gaan we een lokale inhoudsbibliotheek maken. Selecteer volgende.](./media/content-library/new-content-library-step2.png)

1. Selecteer het gegevensarchief waarin uw inhoudsbibliotheek wordt opgeslagen en selecteer vervolgens **Volgende**.

   ![Selecteer het gegevensarchief dat u als host voor uw inhoudsbibliotheek wilt laten fungeren en selecteer volgende.](./media/content-library/new-content-library-step3.png)

1. Controleer en verifieer de instellingen voor de inhoudsbibliotheek en selecteer **Voltooien**.

   ![Controleer uw instellingen en selecteer Voltooien.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Upload een ISO-installatiekopie naar de inhoudsbibliotheek

Nu de inhoudsbibliotheek is gemaakt, kunt u een ISO-installatiekopie toevoegen om een virtuele machine te implementeren in een privécloudcluster. 

1. Selecteer op de vSphere-client **Menu > Inhoudsbibliotheken**.

1. Klik met de rechtermuisknop op de inhoudsbibliotheek die u wilt gebruiken voor de nieuwe ISO en selecteer **Item importeren**.

1. Voer een van de volgende handelingen uit om een bibliotheekitem voor de Bron te importeren en selecteer vervolgens **Importeren**:
   1. Selecteer URL en geef een URL op om een ISO te downloaden.

   1. Selecteer **Lokaal bestand** dat u van het lokale systeem wilt uploaden.

   > [!TIP]
   > Optioneel, u kunt een aangepaste itemnaam en opmerkingen voor het Doel definiëren.

1. Open de bibliotheek en selecteer het tabblad **Andere typen** om te controleren of de ISO succesvol is geüpload.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Een virtuele machine implementeren in een privécloudcluster

1. Selecteer op de vSphere-client **Menu > Hosts en clusters**.

1. Vouw in het linkerdeelvenster de structuur uit en selecteer een cluster.

1. Selecteer **Acties > Nieuwe virtuele machine**.

1. Ga door met de wizard en wijzig de gewenste instellingen.

1. Selecteer **Nieuw CD/DVD-station > Clientapparaat > Inhoudsbibliotheek ISO-bestand**.

1. Selecteer de in de vorige sectie geladen ISO en selecteer **OK**.

1. Schakel het selectievakje **Verbinding maken** in zodat de ISO wordt gekoppeld op het moment van inschakeling.

1. Selecteer **Nieuw netwerk > Selecteer vervolgkeuze > Bladeren**.

1. Selecteer de **logische switch (segment)** en selecteer **OK**.

1. Wijzig andere hardware-instellingen en selecteer **Volgende**.

1. Controleer de instellingen en selecteer **Voltooien**.


## <a name="next-steps"></a>Volgende stappen

Nu u het maken van een inhoudsbibliotheek voor het implementeren van VM's in Azure VMware Solution hebt behandeld, kunt u het volgende leren:

- [VM-workloads migreren naar uw privécloud](tutorial-deploy-vmware-hcx.md)
- [Levenscyclusbeheer van Azure VMware Solution-VM's](lifecycle-management-of-azure-vmware-solution-vms.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
