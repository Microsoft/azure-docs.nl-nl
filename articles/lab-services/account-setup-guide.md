---
title: Gids voor het instellen van een versneld Lab-account voor Azure Lab Services
description: Deze hand leiding helpt beheerders snel een Lab-account in te stellen voor gebruik op hun school.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669386"
---
# <a name="lab-account-setup-guide"></a>Installatie handleiding voor het lab-account
Als u een beheerder bent, moet u voordat u uw Azure Lab Services omgeving instelt eerst een *Lab-account* maken in uw Azure-abonnement. Een Lab-account is een container voor een of meer Labs en het duurt slechts enkele minuten om in te stellen.

Deze hand leiding bevat drie secties:
-  Vereisten
-  De instellingen van uw Lab-account plannen
-  Uw Lab-account instellen

## <a name="prerequisites"></a>Vereisten
In de volgende secties wordt uitgelegd wat u moet doen voordat u een Lab-account kunt instellen.


### <a name="access-your-azure-subscription"></a>Toegang tot uw Azure-abonnement
Als u een Lab-account wilt maken, moet u toegang hebben tot een Azure-abonnement dat al is ingesteld voor uw school. Uw school heeft mogelijk een of meer abonnementen. U gebruikt een abonnement voor het beheren van facturering en beveiliging voor al uw Azure-resources en-services, met inbegrip van Lab-accounts.  Azure-abonnementen worden meestal beheerd door uw IT-afdeling.  Zie de sectie ' abonnement ' van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#subscription)voor meer informatie.

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Schatting maken van het aantal Vm's en VM-grootten dat u nodig hebt
Het is belang rijk om te weten hoeveel [virtuele machines (vm's) en VM-grootten](./administrator-guide.md#vm-sizing) uw school lab nodig heeft. 

Voor hulp bij het structureren van uw Labs en installatie kopieën raadpleegt u het blog bericht [dat van een fysiek Lab wordt verplaatst naar Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Zie de sectie Lab van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#lab)voor meer informatie over het structureren van Labs.

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Meer informatie over de VM-limieten voor abonnementen en regionale VM-capaciteit
Nadat u het aantal Vm's en de VM-grootten voor uw Labs hebt geschat, moet u het volgende doen:

- Zorg ervoor dat de capaciteits limiet van uw Azure-abonnement het aantal Vm's en de grootte van de virtuele machine toestaat die u in uw Labs wilt gebruiken.
- Maak uw Lab-account in een regio met voldoende beschik bare VM-capaciteit.

Zie [limieten voor VM-abonnementen en regionale capaciteit](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)voor meer informatie.

### <a name="decide-how-many-lab-accounts-to-create"></a>Bepalen hoeveel lab-accounts moeten worden gemaakt

Als u snel aan de slag wilt gaan, maakt u één Lab-account binnen een eigen resource groep.  U kunt later aanvullende Lab-accounts en-resource groepen maken, indien nodig. Zo kunt u uiteindelijk één Lab-account en resource groep per afdeling hebben als een manier om de kosten duidelijk te scheiden. 

Zie voor meer informatie over Lab-accounts, resource groepen en het scheiden van de kosten:
- De sectie ' resource groep ' van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#resource-group)
- De sectie ' Lab account ' van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#lab-account) 
- [Kosten beheer voor Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>De instellingen van uw Lab-account plannen

Houd rekening met de volgende vragen om de instellingen voor uw Lab-account te plannen.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Wie moet de eigen aren en mede werkers zijn van het lab-account?

De IT-beheerders van uw school nemen normaal gesp roken de rol van eigenaar en Inzender voor een Lab-account. Deze rollen zijn verantwoordelijk voor het beheer van de beleids regels die van toepassing zijn op alle Labs in het lab-account. De persoon die het lab-account maakt, wordt automatisch een eigenaar. U kunt extra eigen aren en mede werkers toevoegen vanuit de Azure Active Directory Azure AD-Tenant die is gekoppeld aan uw abonnement. 

Voor meer informatie over de eigenaar van het lab-account en Inzender rollen, zie de sectie identiteit beheren van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Gebruikers met een Lab zien slechts één lijst met de virtuele machines waartoe ze toegang hebben via Azure AD-tenants in Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Wie kan Labs maken?

U kunt ervoor kiezen om uw IT-team of faculteits leden te laten maken van Labs. Als u Labs wilt maken, wijst u deze personen vervolgens toe aan de rol Lab Creator binnen het lab-account. U wijst deze rol normaal gesp roken toe vanuit de Azure AD-Tenant die is gekoppeld aan uw school abonnement. Iedereen die een Lab maakt, wordt automatisch toegewezen als de eigenaar van het lab.  

Zie de sectie identiteit beheren van de [Azure Lab Services-beheerders handleiding](./administrator-guide.md#manage-identity)voor meer informatie over de rol Lab Creator.

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Wie mag de eigenaar zijn van de laboratoria en deze beheren?

U kunt er ook voor kiezen om IT-en faculteits leden own\manage Labs te laten, *zonder* dat ze de mogelijkheid hebben om Labs te maken.  In dit geval krijgen gebruikers uit de Azure AD-Tenant van uw abonnement de eigenaar of Inzender voor bestaande Labs toegewezen.  

Voor meer informatie over de rollen Lab-eigenaar en Inzender, zie de sectie identiteit beheren van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Wilt u afbeeldingen opslaan en deze delen in Labs?

Galerie met gedeelde afbeeldingen is een service die u kunt gebruiken voor het opslaan en delen van installatie kopieën. Voor klassen die dezelfde installatie kopie moeten gebruiken, kunnen Lab-makers de installatie kopie maken en deze vervolgens exporteren naar een galerie met gedeelde installatie kopieën.  Nadat een installatie kopie naar de galerie met gedeelde afbeeldingen is geëxporteerd, kan deze worden gebruikt om nieuwe Labs te maken.

Mogelijk wilt u uw installatie kopieën in uw fysieke omgeving maken en deze vervolgens importeren in een galerie met gedeelde afbeeldingen. Zie het blog bericht [een aangepaste installatie kopie importeren naar een galerie met gedeelde afbeeldingen](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)voor meer informatie.

Als u de service voor de gedeelde installatie kopie galerie wilt gebruiken, moet u een galerie met gedeelde installatie kopieën maken of koppelen aan uw Lab-account. U kunt deze beslissing nu uitstellen, omdat een galerie met gedeelde afbeeldingen op elk gewenst moment kan worden gekoppeld aan een Lab-account.  

Zie voor meer informatie:
- De sectie ' galerie met gedeelde afbeeldingen ' van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#shared-image-gallery)
- De sectie ' prijzen ' van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Welke installatie kopieën in azure Marketplace worden gebruikt voor uw Labs?

Azure Marketplace biedt honderden installatie kopieën die u kunt inschakelen zodat Lab-makers ze kunnen gebruiken om hun Labs te maken. Sommige installatie kopieën bevatten mogelijk alles wat een Lab al nodig heeft. In andere gevallen kunt u een afbeelding gebruiken als uitgangs punt, waarna de maker van het lab deze kan aanpassen door aanvullende toepassingen of hulpprogram ma's te installeren.

Als u niet weet welke installatie kopieën u nodig hebt, kunt u deze later weer inschakelen. De beste manier om te zien welke installatie kopieën beschikbaar zijn, is om eerst een Lab-account te maken. Hiermee hebt u toegang tot, zodat u de lijst met beschik bare afbeeldingen en de bijbehorende inhoud kunt bekijken.  

Zie voor meer informatie [de Azure Marketplace-installatie kopieën opgeven die beschikbaar zijn voor Lab-makers](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Moeten de Lab-Vm's toegang hebben tot andere Azure-of on-premises bronnen?

Wanneer u een Lab-account instelt, kunt u ook uw Lab-account koppelen aan een virtueel netwerk.  Houd er rekening mee dat het virtuele netwerk en het lab-account zich in dezelfde regio moeten bevinden.  Als u wilt bepalen of u moet worden gekoppeld aan een virtueel netwerk, moet u rekening houden met de volgende scenario's:

- **Toegang tot een licentie server**
  
   Wanneer u Azure Marketplace-installatie kopieën gebruikt, worden de kosten van de licentie voor het besturings systeem gebundeld in de prijzen voor Lab-Services. U hoeft echter geen licenties voor het besturings systeem zelf op te geven. Voor aanvullende software en toepassingen die zijn geïnstalleerd, moet u een licentie opgeven, indien van toepassing.  Om toegang te krijgen tot een licentie server:
   - U kunt ervoor kiezen om verbinding te maken met een on-premises licentie server.  Voor het maken van een verbinding met een on-premises licentie server is aanvullende installatie vereist.
   - Een andere optie, die sneller is om in te stellen, is het maken van een licentie server die u host op een virtuele Azure-machine.  De virtuele machine van Azure bevindt zich in een virtueel netwerk dat u met uw Lab-account hebt.

- **Toegang tot andere on-premises resources, zoals een bestands share of data base**

   Normaal gesp roken maakt u een virtueel netwerk om toegang te bieden tot on-premises resources met behulp van een site-naar-site-gateway van het virtuele netwerk. Het instellen van dit type omgeving duurt meer tijd.

- **Toegang tot andere Azure-resources die zich buiten een virtueel netwerk bevinden**

   Als u toegang nodig hebt tot Azure-resources die *niet* zijn beveiligd binnen een virtueel netwerk, hebt u toegang via het open bare Internet, zonder dat u een peering hoeft te doen.

   Zie voor meer informatie over virtuele netwerken:
   - De sectie ' virtueel netwerk ' van [architectuur beginselen in Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Verbind uw Lab-netwerk met een virtueel peer netwerk in Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Een lab maken met een gedeelde resource in Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Uw Lab-account instellen

Nadat u klaar bent met de planning, kunt u uw Lab-account instellen. U kunt dezelfde stappen Toep assen om [Azure Lab Services in teams in](./lab-services-within-teams-overview.md)te stellen.

1. **Maak een Lab-account**. Zie [een Lab-account maken](./tutorial-setup-lab-account.md#create-a-lab-account)voor instructies.
   
    Zie de sectie ' naamgeving ' van [Azure Lab Services-beheerders handleiding](./administrator-guide.md#naming)voor meer informatie over naamgevings regels.

1. **Gebruikers toevoegen aan de rol Lab Creator**. Zie [gebruikers toevoegen aan de rol Lab Creator](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)voor instructies.

1. **Verbinding maken met een virtueel netwerk op hetzelfde niveau**. Zie [uw Lab-netwerk verbinden met een virtueel netwerk van een peer](./how-to-connect-peer-virtual-network.md)voor instructies.

   Mogelijk moet u ook verwijzen naar instructies voor [het configureren van het adres bereik van de virtuele machine voor het lab](./how-to-configure-lab-accounts.md).

1. **Afbeeldingen inschakelen en controleren**. Zie [opgeven welke Azure Marketplace-installatie kopieën beschikbaar zijn voor Lab-makers](./specify-marketplace-images.md)voor instructies.

   Als u de inhoud van elke Azure Marketplace-installatie kopie wilt bekijken, selecteert u de naam van de installatie kopie. Op de volgende scherm afbeelding ziet u bijvoorbeeld de details van de Ubuntu-Data Science VM:

   ![Scherm afbeelding van een lijst met installatie kopieën die beschikbaar zijn voor beoordeling in azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Als er een galerie met gedeelde installatie kopieën is gekoppeld aan uw Lab-account en u aangepaste installatie kopieën wilt laten delen door Lab-makers, voert u de volgende stappen uit, zoals wordt weer gegeven in de onderstaande scherm afbeelding:

   ![Scherm opname van een lijst met ingeschakelde aangepaste installatie kopieën in een galerie met gedeelde afbeeldingen.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het instellen en beheren van Labs:

- [Lab-accounts beheren](how-to-manage-lab-accounts.md)  
- [Hand leiding voor Lab-installatie](setup-guide.md)
