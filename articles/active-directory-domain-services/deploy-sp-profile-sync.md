---
title: Share point-gebruikers profiel service inschakelen met Azure AD DS | Microsoft Docs
description: Meer informatie over het configureren van een Azure Active Directory Domain Services beheerd domein voor de ondersteuning van profiel synchronisatie voor share Point server
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8fbde10ccf5a7f083f5fbddaadd6668d880a1fac
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619824"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Azure Active Directory Domain Services configureren voor de ondersteuning van synchronisatie van gebruikers profielen voor share Point server

Share Point server bevat een service voor het synchroniseren van gebruikers profielen. Met deze functie kunnen gebruikers profielen worden opgeslagen op een centrale locatie en toegankelijk via meerdere share point-sites en-farms. Als u de gebruikers profiel service van share Point server wilt configureren, moeten de juiste machtigingen worden verleend in een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein. Zie [gebruikers profiel synchronisatie in share Point server](/SharePoint/administration/user-profile-service-administration)voor meer informatie.

In dit artikel wordt beschreven hoe u Azure AD DS configureert om de synchronisatie service voor gebruikers profielen van share Point server toe te staan.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een Windows Server Management-VM die deel uitmaakt van het door Azure AD DS beheerde domein.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken van een beheer-VM][tutorial-create-management-vm].
* Een gebruikersaccount dat lid is van de *Azure AD DC-beheerdersgroep* in uw Azure AD-tenant.
* Een share point-service account voor de synchronisatie service voor gebruikers profielen.
    * Zie, indien nodig, [plan voor beheer-en service accounts in share Point server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Overzicht van service accounts

In een beheerd domein bestaat een beveiligings groep met de naam *Aad DC-service accounts* als onderdeel van de organisatie-eenheid (OE) van de *gebruiker* . Leden van deze beveiligings groep zijn gemachtigd om de volgende bevoegdheden:

- De bevoegdheid **Directory wijzigingen repliceren** op het hoofd DSE.
- De bevoegdheid **Directory wijzigingen repliceren** in de *configuratie* naamgevings context ( `cn=configuration` container).

De beveiligings groep van de *Aad DC-service accounts* is ook lid van de ingebouwde groep *Pre-Windows 2000-compatibele toegang*.

Wanneer het service account voor de synchronisatie service van de share Point server-gebruikers profielen aan deze beveiligings groep wordt toegevoegd, worden de vereiste bevoegdheden verleend om correct te werken.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Ondersteuning voor synchronisatie van gebruikers profielen van share Point server inschakelen

Het service account voor share Point server heeft voldoende bevoegdheden nodig om wijzigingen in de Directory te repliceren en de synchronisatie van het gebruikers profiel van share Point server correct te laten werken. Als u deze bevoegdheden wilt opgeven, voegt u het service account dat wordt gebruikt voor de synchronisatie van share point-gebruikers profielen toe aan de groep *Aad DC-service accounts* .

Voer de volgende stappen uit op de virtuele machine met Azure AD DS Management:

> [!NOTE]
> Als u groepslid maatschap in een beheerd domein wilt bewerken, moet u zijn aangemeld bij een gebruikers account dat lid is van de groep *Aad DC-Administrators* .

1. Selecteer in het Start scherm de optie **systeem beheer**. Er wordt een lijst met beschik bare beheer hulpprogramma's weer gegeven die in de zelf studie zijn geïnstalleerd om [een beheer-VM te maken][tutorial-create-management-vm].
1. Als u groepslid maatschap wilt beheren, selecteert u **Active Directory-beheercentrum** in de lijst met beheer Programma's.
1. Kies in het linkerdeel venster uw beheerde domein, zoals *aaddscontoso.com*. Er wordt een lijst met bestaande Ou's en resources weer gegeven.
1. Selecteer de organisatie-eenheid **gebruikers** en kies vervolgens de beveiligings groep *Aad DC-service accounts* .
1. Selecteer **leden** en klik vervolgens op **toevoegen...**.
1. Voer de naam van het share point-service account in en selecteer **OK**. In het volgende voor beeld heeft het share point-service account de naam *SPAdmin*:

    ![Het share point-service account toevoegen aan de beveiligings groep van de AAD DC-service accounts](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Volgende stappen

Zie [synchronisatie van gebruikers profielen beheren in share Point server](/SharePoint/administration/manage-profile-synchronization)voor meer informatie.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts