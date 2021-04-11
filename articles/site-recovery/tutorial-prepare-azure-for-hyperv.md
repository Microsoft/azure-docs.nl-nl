---
title: Azure voorbereiden op herstel na noodgevallen voor Hyper-V met Azure Site Recovery
description: Informatie over het voorbereiden van Azure op herstel na noodgevallen van on-premises Hyper-V-VM's met Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.custom: MVC
ms.openlocfilehash: ec1c41935d200ad220716940f898549d835510ba
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579289"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Azure-resources voorbereiden op herstel na noodgevallen

 [Azure Site Recovery](site-recovery-overview.md) draagt bij aan uw zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps actief blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

Deze zelfstudie is de eerste in een reeks waarin wordt beschreven hoe u herstel na noodgevallen kunt instellen voor Hyper-V-VM's.

> [!NOTE]
> In onze zelfstudies geven we altijd het eenvoudigste implementatietraject voor een scenario. Waar mogelijk maken we in deze zelfstudies gebruik van standaardopties en worden niet alle mogelijke instellingen en paden weergegeven. Zie de sectie 'Procedures' voor elk van de bijbehorende scenario's voor meer informatie.

Deze zelfstudie laat zien hoe u Azure-onderdelen voorbereidt wanneer u on-premises virtuele machines (Hyper-V) naar Azure wilt repliceren. U leert het volgende:

> [!div class="checklist"]
> * Controleren of uw Azure-account replicatiemachtigingen heeft.
> * Maak een Azure-opslagaccount waarin installatiekopieën van gerepliceerde machines worden opgeslagen.
> * Maak een Recovery Services-kluis, die metagegevens en configuratiegegevens voor VM's en andere replicatieonderdelen bevat.
> * Stel een Azure-netwerk in. Wanneer de Azure-VM's zijn gemaakt na de failover, worden ze gekoppeld aan dit netwerk.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="sign-in"></a>Aanmelden

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Accountmachtigingen controleren

Als u net een gratis Azure-account hebt gemaakt, bent u de beheerder van het abonnement. Als u niet de beheerder bent, neemt u contact op met de beheerder om de machtigingen te krijgen die u nodig hebt. Om replicatie in te kunnen schakelen voor een nieuwe virtuele machine, moet u de volgende machtigingen hebben:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijven naar het geselecteerde opslagaccount.

U kunt deze taken alleen uitvoeren als aan uw account de ingebouwde rol van Inzender voor virtuele machines is toegewezen. Als u Site Recovery-bewerkingen wilt beheren in een kluis, moet aan uw account ook de ingebouwde rol van Site Recovery-inzender zijn toegewezen.

## <a name="create-a-storage-account"></a>Create a storage account

Installatiekopieën van gerepliceerde machines worden bewaard in Azure Storage. Azure VM's worden gemaakt vanuit de opslag wanneer u een failover van on-premises naar Azure uitvoert. Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.

1. Selecteer in het menu van de [Azure-portal](https://portal.azure.com) achtereenvolgens **Een resource maken** > **Opslag** > **Opslagaccount - blob, bestand, tabel, wachtrij**.
2. Voer in **Opslagaccount maken** een naam voor de account in.  De naam die u kiest, moet uniek zijn binnen Azure, 3 tot 24 tekens lang zijn en mag alleen kleine letters en cijfers bevatten. Gebruik voor deze zelfstudie **contosovmsacct1910171607**.
3. Selecteer bij **implementatiemodel** **Resource Manager**.
4. Selecteer bij **Soort account** de optie **Opslag (algemeen v1)** . Selecteer niet blob-opslag.
5. Selecteer bij **Replicatie** de standaardoptie **Geografisch redundante opslag met leestoegang** voor opslagredundantie.
6. Selecteer bij **Prestaties** **Standard**. Selecteer vervolgens bij **Opslaglaag** de standaardoptie **Dynamisch**.
7. Kies bij **Abonnement** het abonnement waarin u het nieuwe opslagaccount wilt maken.
8. Geef bij **Resourcegroep** een nieuwe resourcegroep op. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Gebruik voor deze zelfstudie **ContosoRG**.
9. Kies bij **Locatie** de geografische locatie van het opslagaccount. Gebruik voor deze zelfstudie **Europa - west**.
10. Selecteer **Maken** om het opslagaccount te maken.

   ![Schermopname van de opties voor het maken van een opslagaccount.](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Selecteer in de Azure-portal **+ Een resource maken** en zoek op de Azure Marketplace naar Recovery services.
2. Selecteer **Backup en Site Recovery (OMS)** . Selecteer vervolgens op de pagina **Backup en Site Recovery** de optie **Maken**.
1. Ga naar **Recovery Services-kluis > Naam** en voer een beschrijvende naam in voor de kluis. Gebruik voor deze zelfstudie **ContosoVMVault**.
2. Selecteer bij **Resourcegroep** een bestaande resourcegroep of maak een nieuwe. Gebruik voor deze zelfstudie **contosoRG**.
3. Selecteer bij **Locatie** de regio waarin de kluis zich moet bevinden. Gebruik voor deze zelfstudie **Europa - west**.
4. Voor snelle toegang tot de kluis vanuit het dashboard, selecteert u **Aan dashboard vastmaken** > **Maken**.

![Schermopname van de pagina Een Recovery Services-kluis maken.](./media/tutorial-prepare-azure/new-vault-settings.png)

De nieuwe kluis wordt weergegeven in **Dashboard** > **Alle resources** en op de hoofdpagina **Recovery Services-kluizen**.

## <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Wanneer de Azure VM's zijn gemaakt vanuit de opslag na de failover, worden ze gekoppeld aan dit netwerk.

1. Selecteer in [Azure Portal](https://portal.azure.com) achtereenvolgens **Een resource maken** > **Netwerken** > **Virtueel netwerk**. Laat Resource Manager geselecteerd als het implementatiemodel.
2. Voer bij **Naam** een netwerknaam in. De naam moet uniek zijn binnen de Azure-resourcegroep. Gebruik voor deze zelfstudie **ContosoASRnet**.
3. Geef de resourcegroep op waarin het netwerk moet worden gemaakt. Gebruik in deze zelfstudie de bestaande resourcegroep **contosoRG**.
4. Voer bij **Adresbereik** het bereik **10.0.0.0/24** in voor het netwerk. Er is geen subnet voor dit netwerk.
5. Selecteer bij **Abonnement** het abonnement waarin u het netwerk wilt maken.
6. Kies bij **Locatie** voor **Europa - west**. Het netwerk moet zich in dezelfde regio bevinden als de Recovery Services-kluis.
7. Laat de standaardopties staan voor DDoS-basisbescherming, zonder service-eindpunt op het netwerk.
8. Selecteer **Maken**.

![Schermopname van de opties voor het maken van een virtueel netwerk.](media/tutorial-prepare-azure/create-network.png)

Het duurt een paar seconden voordat het virtuele netwerk is gemaakt. Nadat dit is gemaakt, wordt het in het dashboard van de Azure-portal weergegeven.

## <a name="useful-links"></a>Handige koppelingen

Meer informatie over:
- [Azure-netwerken](../virtual-network/virtual-networks-overview.md)
- [Azure Managed Disks](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De on-premises Hyper-V-infrastructuur voorbereiden op herstel naar Azure na een noodgeval](hyper-v-prepare-on-premises-tutorial.md)
