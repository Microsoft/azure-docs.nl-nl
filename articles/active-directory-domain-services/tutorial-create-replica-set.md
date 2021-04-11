---
title: 'Zelfstudie: Een replicaset maken in Azure AD Domain Services | Microsoft Docs'
description: Meer informatie over het maken en gebruiken van replicasets in de Azure-portal voor servicetolerantie met Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: justinha
ms.openlocfilehash: a016287fedbd303a5571100130769ce4299828bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798553"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>Zelf studie: replica sets maken en gebruiken voor tolerantie of geolocatie in Azure Active Directory Domain Services

Als u de tolerantie van een beheerd domein in Azure Active Directory Domain Services (Azure AD DS) wilt verbeteren of als u wilt implementeren op aanvullende geografische locaties die dicht bij uw toepassingen staan, kunt u *replicasets* gebruiken. Elke naamruimte van een beheerd domein in Azure AD DS, zoals *aaddscontoso.com*, bevat één initiële replicaset. De mogelijkheid om extra replicasets te maken in andere Azure-regio's leidt tot geografische tolerantie voor een beheerd domein.

U kunt een replicaset toevoegen aan elk gekoppeld virtueel netwerk in een Azure-regio die Azure AD DS ondersteunt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inzicht in de vereisten voor het virtuele netwerk
> * Een replicaset maken
> * Een replicaset verwijderen

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een met Azure Active Directory Domain Services beheerd domein dat is gemaakt met behulp van het Azure Resource Manager-implementatiemodel en is geconfigureerd in uw Azure AD-tenant.
    * [Maak en configureer zo nodig een door Azure Active Directory Domain Services beheerd domein][tutorial-create-instance].

    > [!IMPORTANT]
    > Beheerde domeinen die zijn gemaakt met het klassieke implementatiemodel kunnen geen replicasets gebruiken. U moet ook minstens een *Enterprise* SKU gebruiken voor het beheerde domein. Indien nodig, kunt u [de SKU voor een beheerd domein wijzigen][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt en beheert u replicasets met behulp van de Azure-portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="networking-considerations"></a>Aandachtspunten voor netwerken

De virtuele netwerken die replicasets hosten, moeten met elkaar kunnen communiceren. Toepassingen en services die afhankelijk zijn van Azure AD DS, hebben ook een netwerkverbinding nodig met de virtuele netwerken die de replicasets hosten. Peerings van virtuele Azure-netwerken moeten worden geconfigureerd tussen alle virtuele netwerken om een volledig gemeshed netwerk te maken. Deze peerings bieden efficiënte replicatie tussen sites voor replicasets.

Voordat u replicasets kunt gebruiken in Azure AD DS, raadpleegt u de volgende vereisten voor virtuele Azure-netwerken:

* Vermijd overlappende IP-adresruimten om peering en routering van virtuele netwerken mogelijk te maken.
* Maak subnetten met voldoende IP-adressen ter ondersteuning van uw scenario.
* Zorg ervoor dat Azure AD DS een eigen subnet heeft. Deel dit subnet van het virtuele netwerk niet met toepassings-VM's en -services.
* Gekoppelde virtuele netwerken zijn NIET transitief.

> [!TIP]
> Wanneer u een replicaset maakt in de Azure-portal, worden de netwerkpeerings van virtuele netwerken voor u gemaakt.
>
> Indien nodig kunt u een virtueel netwerk en subnet maken wanneer u een replicaset toevoegt in de Azure-portal. U kunt ook bestaande virtueel-netwerkbronnen in de doelregio voor een replicaset kiezen en de peerings automatisch laten maken, als ze nog niet bestaan.

## <a name="create-a-replica-set"></a>Een replicaset maken

Wanneer u een beheerd domein maakt, zoals *aaddscontoso.com*, wordt er een eerste replicaset gemaakt. Extra replicasets hebben dezelfde naamruimte en configuratie. Wijzigingen in Azure AD DS, waaronder voor configuratie, gebruikers-id en -referenties, groepen, groepsbeleidsobjecten en computerobjecten, worden toegepast op alle replicasets in het beheerde domein met behulp van AD DS-replicatie.

In deze zelfstudie maakt u een extra replicaset in een Azure-regio die verschilt van die van de eerste Azure AD DS-replicaset.

Als u een extra replicaset wilt maken, voert u de volgende stappen uit:

1. Zoek en selecteer **Azure AD Domain Services** in de Azure-portal.
1. Kies uw beheerde domein, bijvoorbeeld *aaddscontoso.com*.
1. Selecteer aan de linkerkant de optie **replica sets**. Elk beheerd domein bevat één initiële replicaset in de geselecteerde regio, zoals wordt weergegeven in de volgende schermopname:

    ![Schermopname van het weergeven en toevoegen van een replicaset in de Azure-portal](./media/tutorial-create-replica-set/replica-set-list.png)

    Als u een extra replicaset wilt maken, selecteert u **+ Toevoegen**.

1. Selecteer in het venster *Een replicaset toevoegen* de doelregio, bijvoorbeeld *US - oost*.

    Selecteer een virtueel netwerk bij de doelregio, zoals *vnet-eastus*, en kies vervolgens een subnet, zoals *aadds-subnet*. Kies, indien nodig, **Nieuwe maken** om een virtueel netwerk in de doelregio toe te voegen en kies vervolgens **Beheren** om een subnet voor Azure AD DS te maken.

    Als ze nog niet bestaan, worden de peerings van het virtuele Azure-netwerk automatisch gemaakt tussen het virtuele netwerk van uw bestaande beheerde domein en het virtuele doelnetwerk.

    In de volgende schermopname wordt het proces voor het maken van een nieuwe replicaset in *US - oost* weergegeven:

    ![Schermopname van het maken van een replicaset in de Azure-portal](./media/tutorial-create-replica-set/create-replica-set.png)

1. Selecteer **Opslaan** wanneer u klaar bent.

Het proces voor het maken van de replicaset neemt enige tijd in beslag omdat de resources in de doelregio worden gemaakt. Het beheerde domein zelf wordt vervolgens gerepliceerd met AD DS-replicatie.

De replicaset wordt weergegeven als *Inrichten* terwijl de implementatie wordt uitgevoerd, zoals de volgende schermopname laat zien. Wanneer de replicaset is voltooid, wordt deze weergegeven als *In uitvoering*.

![Schermopname van de implementatiestatus van een replicaset in de Azure-portal](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Een replicaset verwijderen

Een beheerd domein kan momenteel maximaal vier replica's hebben: de initiële replicaset en drie extra replicasets. Als u een replicaset niet meer nodig hebt, of als u een replicaset in een andere regio wilt maken, kunt u de overbodige replicasets verwijderen.

> [!IMPORTANT]
> U kunt de laatste replicaset in een beheerd domein niet verwijderen.

Als u een replicaset wilt verwijderen, voert u de volgende stappen uit:

1. Zoek en selecteer **Azure AD Domain Services** in de Azure-portal.
1. Kies uw beheerde domein, bijvoorbeeld *aaddscontoso.com*.
1. Selecteer aan de linkerkant de optie **replica sets**. Selecteer in de lijst met replicasets het contextmenu **...** naast de replicaset die u wilt verwijderen.
1. Selecteer **Verwijderen** in het contextmenu en bevestig dat u de replicaset wilt verwijderen.

> [!NOTE]
> Het verwijderen van een replicaset kan tijdrovend zijn.

Als u het virtuele netwerk dat of de peering die door de replicaset wordt gebruikt, niet meer nodig hebt, kunt u deze resources ook verwijderen. Zorg ervoor dat er geen andere toepassingsbronnen in de andere regio zijn die de netwerkverbindingen nodig hebben voordat u ze verwijdert.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Peering van virtuele netwerken configureren
> * Een replicaset maken in een andere geografische regio
> * Een replicaset verwijderen

Voor meer conceptuele informatie kunt u bekijken hoe replicasets werken in Azure AD DS.

> [!div class="nextstepaction"]
> [Concepten en functies van replicasets][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
