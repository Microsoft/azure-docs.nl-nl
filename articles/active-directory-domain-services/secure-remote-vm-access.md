---
title: Toegang tot externe VM'S beveiligen in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het beveiligen van externe toegang tot virtuele machines met behulp van Network Policy Server (NPS) en Azure AD Multi-Factor Authentication met een Extern bureaublad-services-implementatie in een Azure Active Directory Domain Services beheerd domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618923"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Externe toegang tot virtuele machines in Azure Active Directory Domain Services beveiligen

Als u externe toegang tot virtuele machines (Vm's) wilt beveiligen die worden uitgevoerd in een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein, kunt u Extern bureaublad-services (RDS) en Network Policy Server (NPS) gebruiken. Azure AD DS verifieert gebruikers wanneer ze toegang aanvragen via de RDS-omgeving. Voor een betere beveiliging kunt u Azure AD-Multi-Factor Authentication integreren om een extra verificatie prompt te bieden tijdens aanmeldings gebeurtenissen. Azure AD Multi-Factor Authentication maakt gebruik van een uitbrei ding voor NPS om deze functie te bieden.

> [!IMPORTANT]
> De aanbevolen manier om veilig verbinding te maken met uw virtuele machines in een Azure AD DS beheerde domein is Azure Bastion, een volledig door het platform beheerde PaaS-service die u in uw virtuele netwerk hebt ingericht. Een bastion-host voorziet in een veilige en Remote Desktop Protocol naadloze RDP-verbinding met uw Vm's rechtstreeks in de Azure Portal via SSL. Wanneer u verbinding maakt via een bastion-host, hebben uw Vm's geen openbaar IP-adres nodig en hoeft u geen netwerk beveiligings groepen te gebruiken om de toegang tot RDP op TCP-poort 3389 beschikbaar te maken.
>
> We raden u ten zeerste aan Azure Bastion te gebruiken in alle regio's waar dit wordt ondersteund. In regio's zonder Azure Bastion-Beschik baarheid voert u de stappen uit die in dit artikel worden beschreven totdat Azure Bastion beschikbaar is. Wees voorzichtig met het toewijzen van open bare IP-adressen aan Vm's die zijn toegevoegd aan Azure AD DS waarbij al het binnenkomende RDP-verkeer is toegestaan.
>
> Zie [Wat is Azure Bastion?][bastion-overview]voor meer informatie.

In dit artikel wordt beschreven hoe u RDS configureert in azure AD DS en optioneel de Azure AD Multi-Factor Authentication NPS-extensie kunt gebruiken.

![Overzicht van Extern bureaublad-services (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te voltooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * [Maak en configureer zo nodig een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een subnet met *werk belastingen* dat is gemaakt in uw Azure Active Directory Domain Services virtuele netwerk.
    * Configureer zo nodig [virtuele netwerken voor een beheerde Azure Active Directory Domain Services domein][configure-azureadds-vnet].
* Een gebruikersaccount dat lid is van de *Azure AD DC-beheerdersgroep* in uw Azure AD-tenant.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>De Extern bureaublad omgeving implementeren en configureren

Om aan de slag te gaan, moet u Mini maal twee virtuele Azure-machines maken waarop Windows Server 2016 of Windows Server 2019 wordt uitgevoerd. Voor redundantie en hoge Beschik baarheid van uw Extern bureaublad-omgeving (RD) kunt u later extra hosts toevoegen en verdelen.

Een voorgestelde RDS-implementatie omvat de volgende twee Vm's:

* *RDGVM01* : voert de RD Connection Broker-server, RD Web Access-server en RD-gateway-server uit.
* *RDSHVM01* : de RD Session Host-server wordt uitgevoerd.

Zorg ervoor dat Vm's zijn geïmplementeerd in een subnet met *werk belastingen* van uw Azure AD DS virtuele netwerk en voeg vervolgens de vm's toe aan het beheerde domein. Zie [een Windows Server-VM maken en toevoegen aan een beheerd domein][tutorial-create-join-vm]voor meer informatie.

De implementatie van de extern bureau blad-omgeving bevat een aantal stappen. De bestaande extern bureau blad-implementatie handleiding kan worden gebruikt zonder dat er specifieke wijzigingen in een beheerd domein kunnen worden gebruikt:

1. Meld u aan bij Vm's die zijn gemaakt voor de RD-omgeving met een account dat deel uitmaakt van de groep *Administrators van Azure AD DC* , zoals *contosoadmin*.
1. Als u RDS wilt maken en configureren, gebruikt u de bestaande [implementatie handleiding][deploy-remote-desktop]voor de Extern bureaublad-omgeving. Distribueer de onderdelen van de extern bureau blad-server op uw virtuele Azure-machines naar wens.
    * Specifiek voor Azure AD DS: als u extern bureau blad-licentie verlening configureert, stelt u deze in op de modus **per apparaat** , niet **per gebruiker** , zoals vermeld in de implementatie handleiding.
1. Als u toegang wilt verlenen via een webbrowser, stelt u [de Extern bureaublad-webclient in voor uw gebruikers][rd-web-client].

Met RD geïmplementeerd in het beheerde domein, kunt u de service beheren en gebruiken, net zoals u zou doen met een on-premises AD DS domein.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>NPS en de Azure AD MFA NPS-extensie implementeren en configureren

Als u de beveiliging van de gebruikers ervaring wilt verbeteren, kunt u de RD-omgeving eventueel integreren met Azure AD Multi-Factor Authentication. Met deze configuratie ontvangen gebruikers een extra prompt tijdens het aanmelden om hun identiteit te bevestigen.

Als u deze mogelijkheid wilt bieden, wordt er een extra Network Policy Server (NPS) geïnstalleerd in uw omgeving, samen met de Azure AD Multi-Factor Authentication NPS-extensie. Deze uitbrei ding kan worden geïntegreerd met Azure AD om de status van de multi-factor Authentication-prompts aan te vragen en te retour neren.

Gebruikers moeten zijn [geregistreerd voor het gebruik van Azure ad multi-factor Authentication][user-mfa-registration], waarvoor mogelijk extra Azure AD-licenties nodig zijn.

Als u Azure AD-Multi-Factor Authentication wilt integreren in uw Azure AD DS Extern bureaublad omgeving, maakt u een NPS-server en installeert u de extensie:

1. Maak een extra Windows Server 2016-of 2019-VM, zoals *NPSVM01*, die is verbonden met een subnet met *werk belastingen* in uw Azure AD DS virtuele netwerk. Voeg de virtuele machine toe aan het beheerde domein.
1. Meld u aan bij de NPS-VM als een account dat deel uitmaakt van de groep *Azure AD DC-Administrators* , zoals *contosoadmin*.
1. In **Serverbeheer** selecteert u **functies en onderdelen toevoegen** en installeert u vervolgens de functie *Services voor netwerk beleid en-toegang* .
1. Gebruik het bestaande artikel over de procedure voor [het installeren en configureren van de Azure AD MFA NPS-extensie][nps-extension].

Als de NPS-server en de Azure AD Multi-Factor Authentication NPS-extensie zijn geïnstalleerd, voltooit u de volgende sectie om deze te configureren voor gebruik met de RD-omgeving.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Extern bureaublad-gateway-en Azure AD-Multi-Factor Authentication integreren

Als u de Azure AD Multi-Factor Authentication NPS-extensie wilt integreren, gebruikt u het bestaande procedure-artikel om [uw extern bureaublad-gateway-infra structuur te integreren met behulp van de Network Policy Server (NPS)-extensie en Azure AD][azure-mfa-nps-integration].

De volgende aanvullende configuratie opties zijn nodig voor de integratie met een beheerd domein:

1. [Registreer de NPS-server niet in Active Directory][register-nps-ad]. Deze stap mislukt in een beheerd domein.
1. In [stap 4 om netwerk beleid te configureren][create-nps-policy], schakelt u ook het selectie vakje in om de **inbel eigenschappen van het gebruikers account te negeren**.
1. Als u Windows Server 2019 gebruikt voor de NPS-server en de NPS-extensie van Azure AD Multi-Factor Authentication, voert u de volgende opdracht uit om het beveiligde kanaal bij te werken zodat de NPS-server op de juiste wijze kan communiceren:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Gebruikers wordt nu gevraagd om een extra verificatie factor wanneer ze zich aanmelden, zoals een tekst bericht of prompt in de Microsoft Authenticator-app.

## <a name="next-steps"></a>Volgende stappen

Zie [extern bureaublad-services-hoge Beschik baarheid][rds-high-availability]voor meer informatie over het verbeteren van de tolerantie van uw implementatie.

Zie [hoe het werkt: Azure AD multi-factor Authentication][concepts-mfa]voor meer informatie over het beveiligen van gebruikers aanmelding.

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability