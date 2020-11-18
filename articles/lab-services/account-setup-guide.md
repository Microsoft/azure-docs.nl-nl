---
title: Gids voor het instellen van een versneld Lab-account voor Azure Lab Services
description: Deze hand leiding helpt beheerders snel een Lab-account in te stellen voor gebruik op hun school.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659859"
---
# <a name="lab-account-setup-guide"></a>Installatie handleiding voor het lab-account
Om uw Azure Lab Services omgeving in te stellen, moeten beheerders eerst een Lab- **account** instellen binnen uw Azure-abonnement. Een Lab-account is een container voor uw Labs en duurt slechts enkele minuten om in te stellen.

Deze hand leiding bevat drie secties:
-  De eerste sectie is gericht op de vereisten die moeten worden voltooid *voordat* u uw Lab-account instelt.
-  De tweede sectie bevat richt lijnen voor het plannen van de instellingen van uw Lab-account.
-  Het derde gedeelte bevat stapsgewijze instructies voor het instellen van een Lab-account.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Vereisten voor het instellen van uw Lab-account
In deze sectie vindt u een overzicht van de vereisten die u moet volt ooien voordat u een Lab-account kunt instellen.

### <a name="obtain-an-azure-subscription"></a>Een Azure-abonnement verkrijgen
Als u een Lab-account wilt maken, moet u toegang hebben tot een Azure-abonnement dat is ingesteld voor uw school. Uw school kan een of meer abonnementen hebben. U gebruikt een abonnement voor het beheren van facturering en beveiliging voor al uw Azure-resources en-services, met inbegrip van Lab-accounts.  Azure-abonnementen worden doorgaans beheerd door uw IT-afdeling.  Lees het volgende onderwerp voor meer informatie:
 - [Beheerders handleiding-abonnement](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Het aantal Vm's en VM-grootten schatten dat u nodig hebt
U moet een schatting maken van het aantal virtuele machines (Vm's) en de [VM-grootten](./administrator-guide.md#vm-sizing) die uw school nodig heeft.  Lees het volgende blog bericht voor meer informatie over het structureren van uw labs\images.  Dit blog bericht helpt u ook bij het bepalen van het aantal Vm's en de VM-grootten die u nodig hebt:
- [Overstappen van een fysiek lab naar Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Zie ook dit artikel voor meer informatie over het structureren van Labs:
- [Beheerders handleiding-Lab](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Meer informatie over de VM-limieten voor abonnementen en regionale VM-capaciteit
Zodra u een schatting hebt van het aantal Vm's en de VM-grootten voor uw Labs, moet u het volgende doen:

- Zorg ervoor dat de capaciteits limiet van uw Azure-abonnement het aantal Vm's en de grootte van de virtuele machine toestaat die u in uw Labs wilt gebruiken.

- Maak een Lab-account in een regio met voldoende VM-capaciteit beschikbaar.

Lees het volgende blog bericht voor meer informatie: [limieten voor VM-abonnementen en regionale capaciteit](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Bepalen hoeveel lab-accounts moeten worden gemaakt

Als u snel aan de slag wilt gaan, maakt u één Lab-account binnen een eigen resource groep.  Later kunt u desgewenst extra Lab-accounts (en resource groepen) maken. Zo kunt u uiteindelijk één Lab-account en resource groep per afdeling hebben als een manier om de kosten duidelijk te scheiden.  Lees de volgende artikelen voor meer informatie over Lab-accounts, resource groepen en het scheiden van de kosten:
- [Beheerders handleiding: resource groep](./administrator-guide.md#resource-group)
- [Beheerders handleiding-Lab-account](./administrator-guide.md#lab-account) 
- [Kosten beheer voor Azure Lab Services](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>De instellingen van uw Lab-account plannen

Als u de instellingen van uw Lab-account wilt plannen, moet u rekening houden met de onderstaande vragen.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Wie moet eigen aren zijn en mede werkers van het lab-account?

   De IT-beheerders van uw school zijn doorgaans de eigen aren en mede werkers voor een Lab-account. Ze zijn verantwoordelijk voor het beheer van de beleids regels die van toepassing zijn op alle Labs in het lab-account. De persoon die het lab-account maakt, wordt automatisch een eigenaar. U kunt extra eigen aren en mede werkers toevoegen vanuit de Azure Active Directory-Tenant (AD) die aan uw abonnement is gekoppeld. Lees voor meer informatie over de eigenaar van het lab-account en de rol Inzender:
   -  [Beheerders handleiding: identiteit beheren](./administrator-guide.md#manage-identity).

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Gebruikers met een Lab zien slechts één lijst van de virtuele machines waartoe ze toegang hebben via tenants in Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Wie kan Labs maken?

   U kunt ervoor kiezen om de leden van uw IT-en faculteit Labs te laten maken. Wanneer een gebruiker een Lab maakt, wordt deze automatisch toegewezen als de eigenaar van het lab.  Als u Labs wilt maken, moeten gebruikers (doorgaans van de Azure AD-Tenant die aan uw abonnement is gekoppeld) worden toegewezen aan de rol Lab Creator binnen het lab-account.  Lees voor meer informatie over de rol Lab Creator:
   -  [Beheerders handleiding-identiteit beheren](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Wie mag de eigenaar zijn van de laboratoria en deze beheren?

   U kunt er ook voor kiezen om IT-en faculteits leden own\manage Labs te laten, *zonder* dat ze de mogelijkheid hebben om Labs te maken.  In dit geval krijgen gebruikers uit de Azure AD-Tenant van uw abonnement de eigenaar of Inzender voor bestaande Labs toegewezen.  Lees voor meer informatie over de eigenaar en Inzender rollen van een Lab:
   - [Beheerders handleiding-identiteit beheren](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Wilt u afbeeldingen opslaan die kunnen worden gedeeld in Labs?
Een galerie met gedeelde installatie kopieën is een opslag plaats die u kunt gebruiken voor het opslaan en delen van installatie kopieën. Voor klassen die dezelfde afbeelding nodig hebben, kunnen Lab-makers de installatie kopie maken en deze vervolgens exporteren naar de galerie met gedeelde afbeeldingen.  Zodra een installatie kopie naar de galerie met gedeelde installatie kopieën is geëxporteerd, kan deze worden gebruikt om nieuwe Labs te maken.

Het is ook mogelijk dat u uw installatie kopieën in uw fysieke omgeving wilt maken en ze vervolgens wilt importeren in de galerie met gedeelde afbeeldingen.  Lees het volgende blog bericht voor meer informatie over dit proces: 
- [Aangepaste afbeelding importeren in galerie met gedeelde afbeeldingen](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Als u besluit een galerie met gedeelde afbeeldingen te gebruiken, moet u een galerie met gedeelde installatie kopieën maken of koppelen aan uw Lab-account. U kunt deze beslissing ook uitstellen tot later, omdat deze op elk gewenst moment kan worden toegevoegd aan een Lab-account.  Lees voor meer informatie over de galerie met gedeelde afbeeldingen:
- [Beheerders handleiding-galerie met gedeelde afbeeldingen](./administrator-guide.md#shared-image-gallery)
- [Beheerders handleiding-prijs informatie voor de galerie met gedeelde afbeeldingen](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Welke installatie kopieën in azure Marketplace worden gebruikt voor uw Labs?
Azure Marketplace biedt honderden installatie kopieën die u kunt inschakelen zodat Lab-makers de installatie kopie kunnen gebruiken om hun Lab te maken. Sommige installatie kopieën bevatten mogelijk alles wat een Lab al nodig heeft. In andere gevallen kunt u een afbeelding gebruiken als uitgangs punt, waarna de maker van het lab deze kan aanpassen door aanvullende toepassingen of hulpprogram ma's te installeren.

Als u niet weet welke installatie kopieën u nodig hebt, kunt u deze later weer inschakelen. Het is ook de beste manier om te zien welke installatie kopieën er beschikbaar zijn om eerst een Lab-account te maken. Hiermee hebt u toegang tot, zodat u de lijst met beschik bare afbeeldingen en de bijbehorende inhoud kunt bekijken.  Lees voor meer informatie over Marketplace-installatie kopieën:
- [Microsoft Azure Marketplace-installatiekopieën opgeven die beschikbaar zijn voor labmakers](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Moeten de virtuele machines van het lab toegang hebben tot andere Azure-of on-premises bronnen?
Wanneer u een Lab-account instelt, kunt u ook uw Lab-account koppelen aan een virtueel netwerk (VNet).  Houd er rekening mee dat uw VNet-en Lab-account zich in dezelfde regio moeten bevinden.  Als u wilt bepalen of u moet worden gekoppeld aan een VNet, moet u rekening houden met de volgende scenario's:

- **Toegang tot een licentie server**
  
   Wanneer u Azure Marketplace-installatie kopieën gebruikt, worden de kosten van de licentie voor het besturings systeem gebundeld in de prijzen voor Lab-Services. U hoeft echter geen licenties voor het besturings systeem zelf op te geven. Voor aanvullende software en toepassingen die zijn geïnstalleerd, moet u echter een licentie opgeven.  Om toegang te krijgen tot een licentie server:
   - U kunt ervoor kiezen om verbinding te maken met een on-premises licentie server.  Voor het maken van een verbinding met een on-premises licentie server is aanvullende installatie vereist.
   - Een andere optie, die sneller is om in te stellen, is het maken van een licentie server die u host op een virtuele Azure-machine.  De virtuele machine van Azure bevindt zich in een virtueel netwerk dat u hebt gekoppeld aan uw Lab-account.

- **Toegang tot andere on-premises resources, zoals een bestands share of data base**

   U maakt een VNet om toegang te bieden tot on-premises resources, meestal met behulp van een site-naar-site-gateway van een virtueel netwerk. Het instellen van dit type omgeving duurt meer tijd.

- **Toegang tot andere Azure-resources die zich buiten een VNet bevinden**

   Als u toegang nodig hebt tot Azure-resources die *niet* zijn beveiligd binnen een VNet, kunt u toegang krijgen tot deze bronnen via het open bare Internet, zonder dat u een peering hoeft te doen.

Lees voor meer informatie over virtuele netwerken:
- [Basis principes van architectuur-Virtual Network](./classroom-labs-fundamentals.md#virtual-network)
- [Verbinding maken met een virtueel netwerk](./how-to-connect-peer-virtual-network.md)
- [Een lab maken met een gedeelde bron in Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Uw Lab-account instellen

Nadat u klaar bent met de planning, kunt u uw Lab-account instellen.  Deze stappen zijn ook van toepassing op het instellen van een Lab- [Azure Lab Services met teams](./lab-services-within-teams-overview.md).

1. **Maak een Lab-account.** Raadpleeg de zelf studie over het [maken van een Lab-account](./tutorial-setup-lab-account.md#create-a-lab-account) voor instructies.
   
    Raadpleeg het volgende artikel voor meer informatie over de naamgeving:

   - [Naamgevings richtlijnen voor bronnen](./administrator-guide.md#naming)

2. **Gebruikers toevoegen aan de rol Lab Creator.** Zie [gebruikers toevoegen aan de rol Lab Creator](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)voor instructies.


3. **Verbinding maken met een virtueel netwerk op hetzelfde niveau.** Zie [verbinding maken tussen het netwerk van uw Lab en een virtueel netwerk van een peer](./how-to-connect-peer-virtual-network.md)voor instructies.

   Mogelijk moet u ook verwijzen naar instructies over [het configureren van het adres bereik van de virtuele machine voor het lab](./how-to-configure-lab-accounts.md).

4. **Afbeeldingen inschakelen en controleren.** Zie [installatie kopieën van Azure Marketplace inschakelen voor Lab-makers](./specify-marketplace-images.md)voor instructies.

   Als u de inhoud van elke Azure Marketplace-installatie kopie wilt bekijken, selecteert u de naam van de installatie kopie. Op de volgende scherm afbeelding ziet u bijvoorbeeld de details van de Ubuntu-Data Science VM:

   ![Scherm opname van Azure Marketplace-installatie kopieën controleren](./media/setup-guide/review-marketplace-images.png)

   Als er een galerie met gedeelde installatie kopieën is gekoppeld aan uw Lab-account en u aangepaste installatie kopieën wilt laten delen door Lab-makers, voert u de volgende stappen uit, zoals wordt weer gegeven in de onderstaande scherm afbeelding:

   ![Scherm opname van het inschakelen van aangepaste installatie kopieën in een galerie met gedeelde afbeeldingen](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Volgende stappen

Volgende stappen die gemeen schappelijk zijn voor het instellen van een test omgeving:

- [Lab-accounts beheren](how-to-manage-lab-accounts.md)
- [Hand leiding voor Lab-installatie](setup-guide.md)