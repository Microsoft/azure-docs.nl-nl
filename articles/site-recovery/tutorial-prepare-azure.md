---
title: Azure voorbereiden op herstel na noodgevallen van on-premises machines met Azure Site Recovery
description: Informatie over het voorbereiden van Azure op herstel na noodgevallen van on-premises machines met Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0d697f2100360732666c9f5ea8050800dad154b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88653814"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Azure voorbereiden op herstel na noodgevallen van on-premises machines naar Azure

In dit artikel wordt beschreven hoe u Azure-resources en -onderdelen voorbereidt, zodat u herstel na noodgevallen van on-premises VMware-VM’s, Hyper-V-VM’s of fysieke Windows-/Linux-servers naar Azure kunt instellen met behulp van de service [Azure Site Recovery](site-recovery-overview.md).

Dit artikel is de eerste zelfstudie in een reeks waarin u leest hoe u noodherstel kunt instellen voor on-premises VM’s. 


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleren of het Azure-account replicatiemachtigingen heeft.
> * Maak een Recovery Services-kluis. Een kluis bevat metagegevens en configuratiegegevens voor VM’s, en andere replicatieonderdelen.
> * Stel een virtueel Azure-netwerk (VNet) in. Wanneer de Azure-VM's zijn gemaakt na de failover, worden ze gekoppeld aan dit netwerk.

> [!NOTE]
> In zelfstudies ziet u steeds het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Voor gedetailleerde instructies bekijkt u het artikel in de instructiesectie van de Site Recovery-inhoudsopgave.

## <a name="before-you-start"></a>Voordat u begint

- Bekijk de architectuur voor herstel na noodgevallen voor [VMware](vmware-azure-architecture.md)-, [Hyper-V](hyper-v-azure-architecture.md)- en [fysieke servers](physical-azure-architecture.md).
- Lees veelgestelde vragen voor [VMware](vmware-azure-common-questions.md) en [Hyper-V](hyper-v-azure-common-questions.md)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Accountmachtigingen controleren

Als u net pas uw gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement en hebt u de benodigde machtigingen. Als u niet de abonnementsbeheerder bent, neemt u contact op met de beheerder om de machtigingen te krijgen die u nodig hebt. Om replicatie in te kunnen schakelen voor een nieuwe virtuele machine, moet u de volgende machtigingen hebben:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijf naar een Azure Storage-account.
- Schrijf naar een door Azure beheerde schijf.

U kunt deze taken alleen uitvoeren als aan uw account de ingebouwde rol van Inzender voor virtuele machines is toegewezen. En als u Site Recovery-bewerkingen wilt beheren in een kluis, moet aan uw account ook de ingebouwde rol van Site Recovery-inzender zijn toegewezen.


## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Selecteer **Een resource maken** in het menu van de Azure-portal en zoek in de Marketplace naar **Recovery**.
2. Selecteer **Backup and Site Recovery** in de zoekresultaten en klik op de pagina ‘Backup and Site Recovery’ op **Maken**. 
3. Selecteer op de pagina **Een Recovery Services-kluis maken** het **abonnement**. Wij gebruiken het **Contoso-abonnement**.
4. Selecteer bij **Resourcegroep** een bestaande resourcegroep of maak een nieuwe. Voor deze zelfstudie gebruiken we **contosoRG**.
5. Voer in **Kluisnaam** een beschrijvende naam in om de kluis aan te duiden. Voor deze reeks zelfstudies gebruiken we **ContosoVMVault**.
6. Selecteer bij **Regio** de regio waarin de kluis zich moet bevinden. In dit voorbeeld gebruiken we **Europa - west**.
7. Selecteer **Controleren + maken**.

   ![Schermopname van de pagina Een Recovery Services-kluis maken.](./media/tutorial-prepare-azure/new-vault-settings.png)

   De nieuwe kluis wordt nu vermeld in **Dashboard** > **Alle resources** en op de hoofdpagina **Recovery Services-kluizen**.

## <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

On-premises machines worden gerepliceerd naar beheerde Azure-schijven. Wanneer er een failover optreedt, worden er Azure-VM’s gemaakt op basis van deze beheerde schijven en gekoppeld aan het Azure-netwerk dat u in deze procedure opgeeft.

1. Selecteer in [Azure Portal](https://portal.azure.com) achtereenvolgens **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
2. Laat **Resource Manager** geselecteerd als het implementatiemodel.
3. Voer bij **Naam** een netwerknaam in. De naam moet uniek zijn binnen de Azure-resourcegroep. In deze zelfstudie gebruiken we **ContosoASRnet**.
4. Voer bij **Adresruimte** het adresbereik van het virtuele netwerk in CDR-notatie in. Wij gebruiken **10.1.0.0/24**.
5. Selecteer bij **Abonnement** het abonnement waarin u het netwerk wilt maken.
6. Geef de **Resourcegroep** op waarin het netwerk wordt gemaakt. We gebruiken de bestaande resourcegroep **contosoRG**.
7. Selecteer bij **Locatie** dezelfde regio als waarin de Recovery Services-kluis is gemaakt. In onze zelfstudie is dat **Europa - west**. Het netwerk moet zich in dezelfde regio bevinden als de kluis.
8. Voer bij **Adresbereik** het bereik in voor het netwerk. Wij gebruiken **10.1.0.0/24** en we gebruiken geen subnet.
9. We laten de standaardopties staan voor DDoS-basisbescherming zonder service-eindpunt of firewall op het netwerk.
9. Selecteer **Maken**.

   ![Schermopname van de opties voor het maken van een virtueel netwerk.](media/tutorial-prepare-azure/create-network.png)

Het duurt een paar seconden voordat het virtuele netwerk is gemaakt. Nadat dit is gemaakt, wordt het in het dashboard van de Azure-portal weergegeven.




## <a name="next-steps"></a>Volgende stappen

- Voor herstel na noodgevallen voor VMware-servers [bereidt u de on-premises VMware-infrastructuur voor](./vmware-azure-tutorial-prepare-on-premises.md).
- Voor herstel na noodgevallen voor Hyper-V-servers [bereidt u de on-premises Hyper-V-servers voor](hyper-v-prepare-on-premises-tutorial.md).
- Voor herstel na noodgevallen voor fysieke servers [stelt u de configuratieserver en bronomgeving in](physical-azure-disaster-recovery.md).
- [Meer informatie](../virtual-network/virtual-networks-overview.md) over Azure-netwerken.
- [Meer informatie](../virtual-machines/managed-disks-overview.md) over beheerde schijven.
