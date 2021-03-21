---
title: Replica sets-concepten voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over de replica sets in Azure Active Directory Domain Services en hoe ze redundantie bieden voor toepassingen waarvoor identiteits Services zijn vereist.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: 8eb1560887c08c3f64fa599c39e5577242d2a1e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689059"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services"></a>Replica sets van concepten en functies voor Azure Active Directory Domain Services

Wanneer u een beheerd domein van Azure Active Directory Domain Services (Azure AD DS) maakt, definieert u een unieke naam ruimte. Deze naamruimte bestaat uit de domeinnaam, zoals *aaddscontoso.com*, en er worden twee domeincontrollers (DC's) geïmplementeerd in uw geselecteerde Azure-regio. Deze implementatie van DC's wordt een replicaset genoemd.

U kunt een beheerd domein uitbreiden als u meer dan één replicaset per Azure AD-tenant wilt hebben. Replicasets kunnen worden toegevoegd aan elk gekoppeld virtueel netwerk in een Azure-regio die Azure AD DS ondersteunt. Extra replica sets in verschillende Azure-regio's bieden geografisch herstel na noodgeval voor oudere toepassingen als een Azure-regio offline gaat.

> [!NOTE]
> Met replica sets kunt u geen meerdere unieke beheerde domeinen implementeren in één Azure-Tenant. Elke replicaset bevat dezelfde gegevens.

## <a name="how-replica-sets-work"></a>Hoe replica sets werken

Wanneer u een beheerd domein maakt, zoals *aaddscontoso.com*, wordt er een eerste replicaset gemaakt. Extra replicasets hebben dezelfde naamruimte en configuratie. Wijzigingen in Azure AD DS, waaronder voor configuratie, gebruikers-id en -referenties, groepen, groepsbeleidsobjecten en computerobjecten, worden toegepast op alle replicasets in het beheerde domein met behulp van AD DS-replicatie.

U maakt elke replicaset in een virtueel netwerk. Elk virtueel netwerk moet worden gekoppeld aan elk ander virtueel netwerk dat als host fungeert voor de replicaset van een beheerd domein. Deze configuratie maakt een netwerk topologie die Directory replicatie ondersteunt. Een virtueel netwerk kan meerdere replica sets ondersteunen, op voor waarde dat elke replicaset zich in een ander virtueel subnet bevindt.

Alle replica sets worden geplaatst op dezelfde Active Directory-site. Als gevolg hiervan worden alle wijzigingen door gegeven met intra site replicatie voor snelle convergentie.

> [!NOTE]
> Het is niet mogelijk om afzonderlijke sites te definiëren en replicatie-instellingen te definiëren tussen replica sets.

In het volgende diagram ziet u een beheerd domein met twee replica sets. De eerste replicaset wordt gemaakt met de naam ruimte van het domein. Er wordt een tweede replicaset gemaakt:

![Diagram van een voor beeld van een beheerd domein met twee replica sets](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> Replica sets garanderen de beschik baarheid van verificatie services in regio's waarvoor een replicaset is geconfigureerd. Als een toepassing geografische redundantie heeft wanneer er sprake is van een regionale storing, moet het toepassings platform dat afhankelijk is van het beheerde domein zich ook in de andere regio bevinden.
>
> Tolerantie van andere services die nodig zijn voor de werking van de toepassing, zoals Azure Vm's of Azure-app Services, wordt niet verstrekt door replica sets. Het beschikbaarheids ontwerp van andere toepassings onderdelen moet rekening houden met de tolerantie functies voor services die de toepassing vormen.

In het volgende voor beeld ziet u een beheerd domein met drie replica sets om verdere tolerantie te bieden en de beschik baarheid van verificatie services te garanderen. In beide voor beelden bestaan toepassings werkbelastingen in dezelfde regio als de replicaset van het beheerde domein:

![Diagram van een voor beeld van een beheerd domein met drie replica sets](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

De standaard-SKU voor een beheerd domein is de *Enter prise* -SKU, die ondersteuning biedt voor meerdere replica sets. Als u aanvullende replica sets wilt maken als u de *standaard* -SKU hebt gewijzigd, moet u [het beheerde domein upgraden](change-sku.md) naar *Enter prise* of *Premium*.

Het ondersteunde maximum aantal replica sets is vier, met inbegrip van de eerste replica die is gemaakt tijdens het maken van het beheerde domein.

Facturering voor elke replicaset is gebaseerd op de domein configuratie-SKU. Als u bijvoorbeeld een beheerd domein hebt dat gebruikmaakt van de *Enter prise* -SKU en u drie replica sets hebt, wordt uw abonnement per uur gefactureerd voor elk van de drie replica sets.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>Kan ik een replicaset maken in een ander abonnement dan mijn beheerde domein?

Nee. Replica sets moeten zich in hetzelfde abonnement bevallen als het beheerde domein.

### <a name="how-many-replica-sets-can-i-create"></a>Hoeveel replica sets kan ik maken?

U kunt Maxi maal vier replica sets maken: de eerste replicaset voor het beheerde domein, plus drie extra replica sets.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>Hoe worden de gebruikers-en groeps gegevens gesynchroniseerd met mijn replica sets?

Alle replica sets zijn met elkaar verbonden met behulp van een net-peering voor het virtuele netwerk. Eén replicaset ontvangt updates van gebruikers en groepen van Azure AD. Deze wijzigingen worden vervolgens gerepliceerd naar de andere replica sets met intra site-AD DS replicatie via het peered netwerk.

Net als bij on-premises AD DS, kan de replicatie een uitgebreide status voor verbroken verbindingen veroorzaken. Aangezien gekoppelde virtuele netwerken niet transitief zijn, is voor de ontwerp vereisten voor replica sets een volledig gecomputernetde netwerk topologie vereist.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>Hoe kan ik wijzigingen aanbrengen in mijn beheerde domein nadat ik replica sets heb?

Wijzigingen in het beheerde domein werken net als voorheen. U [maakt en gebruikt een beheer-VM met de RSAT-hulpprogram ma's die zijn gekoppeld aan het beheerde domein](tutorial-create-management-vm.md). U kunt net zoveel Management-Vm's toevoegen aan het beheerde domein als u wilt.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met replica sets, moet u [een door Azure AD DS beheerd domein maken en configureren][tutorial-create-advanced]. Bij implementatie kunt [u extra replica sets maken en gebruiken][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
