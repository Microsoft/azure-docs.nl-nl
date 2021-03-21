---
title: 'Zelfstudie: Virtuele netwerken voor Azure AD Domain Services configureren | Microsoft Docs'
description: In deze zelfstudie leert u hoe u een subnet voor een virtueel Azure-netwerk of netwerkpeering kunt maken en configureren voor een door Azure Active Directory Domain Services beheerd domein met Azure Portal.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618413"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Zelfstudie: Virtuele netwerken configureren voor een door Azure Active Directory Domain Services beheerd domein

Om connectiviteit te bieden aan gebruikers en toepassingen wordt een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein geïmplementeerd in een subnet van een virtueel Azure-netwerk. Dit subnet van een virtueel netwerk mag alleen worden gebruikt voor de beheerde domeinresources die via het Azure-platform beschikbaar zijn.

Wanneer u uw eigen virtuele machines en toepassingen maakt, moeten deze niet worden geïmplementeerd in hetzelfde subnet van een virtueel netwerk. In plaats daarvan moet u uw toepassingen maken en implementeren in een afzonderlijk subnet van een virtueel netwerk, of in een afzonderlijk virtueel netwerk dat via peering met het virtuele netwerk van Azure AD DS is verbonden.

In deze zelfstudie ziet u hoe u een toegewezen subnet van een virtueel netwerk kunt maken en configureren of hoe u een ander netwerk via peering met het virtuele netwerk van het door Azure AD DS beheerde domein kunt verbinden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Informatie over de connectiviteitsopties voor virtuele netwerken voor resources die via een domein aan Azure AD DS zijn toegevoegd
> * Een IP-adresbereik en aanvullend subnet in het virtuele netwerk van Azure AD DS maken
> * Peering configureren van een virtueel netwerk naar een netwerk dat niet aan Azure AD DS is gekoppeld

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* U hebt *bevoegdheden van een globale beheerder* voor de Azure AD-tenant nodig om Azure AD DS te configureren.
* U hebt bevoegdheden van een *Inzender* voor uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Bekijk zo nodig de eerste zelfstudie voor [het maken en configureren van een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie gaat u het beheerde domein maken en configureren met behulp van Azure Portal. Meld u eerst aan bij [Azure Portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="application-workload-connectivity-options"></a>Connectiviteitsopties voor de workload van toepassingen

In de vorige zelfstudie hebt u een beheerd domein gemaakt waarvoor een aantal standaardconfiguratieopties voor het virtuele netwerk is gebruikt. Met behulp van deze standaardopties zijn een virtueel Azure-netwerk en subnet van een virtueel netwerk gemaakt. De Azure AD DS-domeincontrollers die de beheerde Domain Services leveren, zijn verbonden met dit subnet van een virtueel netwerk.

Wanneer u VM's maakt en uitvoert die het beheerde domein moeten gebruiken, moet er netwerkconnectiviteit beschikbaar zijn. Deze netwerkconnectiviteit kan op een van de volgende manieren worden geboden:

* Maak een aanvullend subnet van een virtueel netwerk in het virtuele netwerk van het beheerde domein. Dit aanvullende subnet is de locatie voor het maken en verbinden van uw virtuele machines.
    * Wanneer de virtuele machines deel uitmaken van hetzelfde virtuele netwerk, kunnen ze automatisch een naamomzetting uitvoeren en communiceren met de Azure AD DS-domeincontrollers.
* Configureer virtuele Azure-netwerkpeering via de virtuele netwerk van het beheerde domein naar een of meer afzonderlijke virtuele netwerken. Deze afzonderlijke virtuele netwerken zijn de locatie voor het maken en verbinden van uw virtuele machines.
    * Wanneer u virtuele netwerkpeering configureert, moet u ook DNS-instellingen configureren zodat de naamomzetting weer op de Azure AD DS-domeincontrollers wordt gebruikt.

Doorgaans gebruikt u slechts een van deze netwerkverbindingsopties. De keuze hangt vaak af van de manier waarop u uw afzonderlijke Azure-resources wilt beheren.

* Als u Azure AD DS en verbonden virtuele machines wilt beheren als een groep resources, kunt u een aanvullend subnet van een virtueel netwerk voor virtuele machines maken.
* Als u het beheer van Azure AD DS en vervolgens alle verbonden virtuele machines wilt scheiden, kunt u peering van virtuele netwerken gebruiken.
    * U kunt ook peering van virtuele netwerken gebruiken om een verbinding tot stand te brengen met bestaande virtuele machines in uw Azure-omgeving die met een bestaand virtueel netwerk zijn verbonden.

In deze zelfstudie hoeft u maar een van deze virtuele netwerkverbindingsopties te configureren.

Zie [Netwerkoverwegingen voor Azure Active Directory Domain Services][network-considerations] voor meer informatie over het plannen en configureren van het virtuele netwerk.

## <a name="create-a-virtual-network-subnet"></a>Een subnet voor een virtueel netwerk maken

Standaard bevat het virtuele Azure-netwerk dat met het beheerde domein is gemaakt één subnet van een virtueel netwerk. Dit subnet van een virtueel netwerk moet alleen door het Azure-platform worden gebruikt om beheerde Domain Services te leveren. Voor het maken en gebruiken van uw eigen virtuele machines in dit virtuele Azure-netwerk maakt u een aanvullend subnet.

Als u een subnet van een virtueel netwerk voor virtuele machines en toepassingsworkloads wilt maken, voert u de volgende stappen uit:

1. Selecteer in Azure Portal de resourcegroep van uw beheerde domein, zoals *myResourceGroup*. Kies in de lijst met resources het virtuele standaardnetwerk, zoals *aadds-vnet*.
1. Selecteer in het menu aan de linkerkant van het venster Virtueel netwerk **Adresruimte**. Het virtuele netwerk is gemaakt met één adresruimte van *10.0.2.0/24*, dat wordt gebruikt door het standaardsubnet.

    Voeg een extra IP-adresbereik toe aan het virtuele netwerk. De grootte van dit adresbereik en het daadwerkelijke te gebruiken IP-adresbereik is afhankelijk van andere netwerkresources die al zijn geïmplementeerd. Het IP-adresbereik mag geen overlap vertonen met de bestaande adresbereiken in uw Azure-omgeving of on-premises omgeving. Zorg ervoor dat u het IP-adresbereik voldoende groot hebt gemaakt voor het aantal VM's dat u in het subnet verwacht te implementeren.

    In het volgende voorbeeld wordt een extra IP-adresbereik van *10.0.3.0/24* toegevoegd. Selecteer **Opslaan** wanneer u klaar bent.

    ![Een extra IP-adresbereik toevoegen voor het virtuele netwerk in Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Selecteer vervolgens in het menu aan de linkerkant van het venster Virtueel netwerk **Subnetten** en kies vervolgens **+ Subnet** om een subnet toe te voegen.
1. Voer een naam voor het subnet in, zoals *workloads*. Werk indien nodig het **Adresbereik** bij als u een subset wilt gebruiken van het IP-adresbereik dat in de vorige stappen voor het virtuele netwerk is geconfigureerd. Voor dit moment laten we de standaardinstellingen staan voor opties zoals netwerkbeveiligingsgroep, routeringstabel en service-eindpunten.

    In het volgende voorbeeld wordt een subnet met de naam *workloads* gemaakt waarvoor het IP-adresbereik *10.0.3.0/24* wordt gebruikt:

    ![Een extra subnet toevoegen voor het virtuele netwerk in Azure Portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Selecteer **OK** wanneer u klaar bent. Het duurt even voordat het subnet van het virtuele netwerk is gemaakt.

Wanneer u een virtuele machine maakt waarvoor het beheerde domein moet worden gebruikt, moet u goed controleren of u dit subnet van een virtueel netwerk selecteert. Maak geen virtuele machines in het standaardsubnet *aadds-subnet*. Als u een ander virtueel netwerk selecteert, zijn er geen netwerkverbinding en DNS-omzetting beschikbaar om het beheerde domein te bereiken, tenzij u virtuele netwerkpeering configureert.

## <a name="configure-virtual-network-peering"></a>Peering van virtuele netwerken configureren

U beschikt mogelijk al over een bestaand virtueel Azure-netwerk voor virtuele machines, of u wilt uw beheerde domein en virtuele netwerk van elkaar gescheiden houden. Als u het beheerde domein wilt behouden, moeten virtuele machines in andere virtuele netwerken op een andere manier met de Azure AD DS-domeincontrollers kunnen communiceren. Deze connectiviteit kan worden geleverd met peering van virtuele Azure-netwerken.

Met peering van virtuele Azure-netwerken worden twee virtuele netwerken met elkaar verbonden, zonder dat hiervoor een VPN-apparaat (Virtual Private Network) hoeft te worden gebruikt. Met behulp van netwerkpeering kunt u snel verbinding maken met virtuele netwerken en verkeersstromen in uw Azure-omgeving definiëren.

Zie [Overzicht van peering van virtuele Azure-netwerken][peering-overview] voor meer informatie over peering.

Als u een virtueel netwerk door middel van peering wilt koppelen aan het virtuele netwerk van het beheerde domein, voert u de volgende stappen uit:

1. Kies het virtuele standaardnetwerk dat u hebt gemaakt voor het beheerde domein *aadds-vnet*.
1. Selecteer in het menu aan de linkerkant van het venster Virtueel netwerk **Peerings**.
1. Selecteer **+ Toevoegen** om een peeringverbinding te maken. In het volgende voorbeeld is het standaardnetwerk *aadds-vnet* gekoppeld met het virtuele netwerk *myVnet*. Configureer de volgende instellingen met uw eigen waarden:

    * **Naam van het peeringnetwerk tussen aadds-vnet en het externe virtuele netwerk**: Een beschrijvende id van de twee netwerken, zoals *aadds-vnet-to-myvnet*
    * **Implementatietype voor het virtuele netwerk**: *Resource Manager*
    * **Abonnement**: Het abonnement van het virtuele netwerk waarmee u peering tot stand wilt brengen, zoals *Azure*
    * **Virtueel netwerk**: Het virtuele netwerk waarmee u peering tot stand wilt brengen, zoals *myVnet*
    * **De naam van de peering van myVnet naar aadds-vnet**: Een beschrijvende id van de twee netwerken, zoals *myvnet-to-aadds-vnet*

    ![Virtuele netwerkpeering in Azure Portal configureren](./media/tutorial-configure-networking/create-peering.png)

    Laat andere standaardinstellingen voor virtuele netwerktoegang of doorgestuurd verkeer staan, tenzij u specifieke vereisten voor uw omgeving hebt, en selecteer vervolgens **OK**.

1. Het maken van de peering op zowel het virtuele netwerk van Azure AD DS als het virtuele netwerk dat u hebt geselecteerd, duurt enkele minuten. Wanneer u klaar bent, wordt de **Peeringstatus** veranderd in *Verbonden*, zoals in het volgende voorbeeld:

    ![Verbinding via peering van netwerken in Azure Portal is tot stand gebracht](./media/tutorial-configure-networking/connected-peering.png)

Voordat u het beheerde domein kunt gebruiken op virtuele machines in het gekoppelde virtuele netwerk, moet u de DNS-servers configureren voor de juiste naamomzetting.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>DNS-servers in het gekoppelde virtuele netwerk configureren

Virtuele machines en toepassingen in het gekoppelde virtuele netwerk kunnen pas met het beheerde domein communiceren zodra de DNS-instellingen zijn bijgewerkt. De IP-adressen van de Azure AD DS-domeincontrollers moeten worden geconfigureerd als de DNS-servers in het gekoppelde virtuele netwerk. Er zijn twee manieren om de domeincontrollers als DNS-servers te configureren voor het gekoppelde virtuele netwerk:

* De DNS-servers van het virtuele Azure-netwerk configureren voor het gebruik van de Azure AD DS-domeincontrollers.
* De bestaande DNS-server die in het gekoppelde virtuele netwerk wordt gebruikt, configureren voor het gebruik van voorwaardelijke DNS-forwarding om query's naar het beheerde domein door te sturen. Deze stappen zijn afhankelijk van de bestaande DNS-server die wordt gebruikt.

In deze zelfstudie gaan we de DNS-servers van het virtuele Azure-netwerk configureren om alle query's door te sturen naar de Azure AD DS-domeincontrollers.

1. Selecteer in Azure Portal de resourcegroep van het gekoppelde virtuele netwerk, zoals *myResourceGroup*. Kies in de lijst met resources het gekoppelde virtuele netwerk, zoals *myVnet*.
1. Selecteer in het menu aan de linkerkant van het venster Virtueel netwerk **DNS-servers**.
1. Standaard worden voor een virtueel netwerk de ingebouwde, door Azure meegeleverde DNS-servers gebruikt. Kies ervoor **Aangepaste** DNS-servers te gebruiken. Voer de IP-adressen voor de Azure AD DS-domeincontrollers in. Dit zijn doorgaans *10.0.2.4* en *10.0.2.5*. Bevestig deze IP-adressen in het venster **Overzicht** van uw beheerde domein in de portal.

    ![De DNS-servers van het virtuele netwerk configureren voor het gebruik van de Azure AD DS-domeincontrollers](./media/tutorial-configure-networking/custom-dns.png)

1. Selecteer **Opslaan** wanneer u klaar bent. Het bijwerken van de DNS-servers voor het virtuele netwerk duurt enkele minuten.
1. Als u de bijgewerkte DNS-instellingen wilt toepassen op de virtuele machines, start u de virtuele machines die met het gekoppelde virtuele netwerk zijn verbonden, opnieuw op.

Wanneer u een virtuele machine maakt waarvoor het beheerde domein moet worden gebruikt, moet u goed controleren of u dit gekoppelde virtuele netwerk selecteert. Als u een ander virtueel netwerk selecteert, zijn er geen netwerkverbinding en DNS-omzetting beschikbaar om het beheerde domein te bereiken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Informatie over de connectiviteitsopties voor virtuele netwerken voor resources die via een domein aan Azure AD DS zijn toegevoegd
> * Een IP-adresbereik en aanvullend subnet in het virtuele netwerk van Azure AD DS maken
> * Peering configureren van een virtueel netwerk naar een netwerk dat niet aan Azure AD DS is gekoppeld

Als u dit beheerde domein in actie wilt zien, maakt en koppelt u een virtuele machine aan het domein.

> [!div class="nextstepaction"]
> [Een virtuele Windows Server-machine toevoegen aan uw beheerde domein](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
