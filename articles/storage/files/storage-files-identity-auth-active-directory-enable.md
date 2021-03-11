---
title: Overzicht-on-premises AD DS verificatie voor Azure-bestands shares
description: Meer informatie over Active Directory Domain Services-verificatie (AD DS) voor Azure-bestands shares. Dit artikel gaat over ondersteunings scenario's, Beschik baarheid en legt uit hoe de machtigingen werken tussen uw AD DS en Azure Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 6098497e89ad66fad7659aa64c9a7849380f8c43
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547497"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Overzicht-on-premises Active Directory Domain Services authenticatie via SMB voor Azure-bestands shares

[Azure files](storage-files-introduction.md)   ondersteunt op identiteit gebaseerde verificatie via Server Message Block (SMB) via twee typen Domain Services: on-premises Active Directory Domain Services (AD DS) en Azure Active Directory Domain Services (Azure AD DS). We raden u ten zeerste aan de [sectie How it Works](./storage-files-active-directory-overview.md#how-it-works) te bekijken om de juiste domein service voor verificatie te selecteren. De installatie verschilt afhankelijk van de domein service die u kiest. Deze reeks artikelen is gericht op het inschakelen en configureren van on-premises AD DS voor verificatie met Azure-bestands shares.

Als u geen ervaring hebt met Azure-bestands shares, raden we u aan onze [plannings handleiding](storage-files-planning.md) te lezen voordat u de volgende serie artikelen leest.

## <a name="supported-scenarios-and-restrictions"></a>Ondersteunde scenario's en beperkingen

- AD DS-identiteiten die worden gebruikt voor Azure Files on-premises AD DS verificatie moeten worden gesynchroniseerd met Azure AD. Synchronisatie van wacht woord-hash is optioneel. 
- Biedt ondersteuning voor Azure-bestands shares die worden beheerd door Azure File Sync.
- Ondersteunt Kerberos-verificatie met AD met RC4-HMAC en [AES 256-versleuteling](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). Ondersteuning voor AES 256-versleuteling is momenteel beperkt tot opslag accounts met namen <= 15 tekens lang zijn. AES 128 Kerberos-versleuteling wordt nog niet ondersteund.
- Biedt ondersteuning voor eenmalige aanmelding.
- Alleen ondersteund op clients met een nieuwere versie van het besturings systeem dan Windows 7 of Windows Server 2008 R2.
- Alleen ondersteund voor het AD-forest waarin het opslag account is geregistreerd. U kunt standaard alleen toegang krijgen tot Azure-bestands shares met de AD DS referenties van één forest. Als u toegang nodig hebt tot uw Azure-bestands share vanuit een ander forest, moet u ervoor zorgen dat u de juiste forestvertrouwensrelatie hebt geconfigureerd. Raadpleeg de [Veelgestelde vragen](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) voor meer informatie.
- Biedt geen ondersteuning voor verificatie tegen computer accounts die zijn gemaakt in AD DS.
- Biedt geen ondersteuning voor verificatie op basis van NFS-bestands shares (Network File System).

Wanneer u AD DS voor Azure-bestands shares via SMB inschakelt, kunnen uw AD DS-gekoppelde computers Azure-bestands shares koppelen met behulp van uw bestaande AD DS referenties. Deze mogelijkheid kan worden ingeschakeld met een AD DS-omgeving die wordt gehost op on-premises computers of die wordt gehost in Azure.

## <a name="videos"></a>Video's

Om u te helpen bij het instellen van Azure Files AD-verificatie voor enkele veelvoorkomende gebruiks gevallen, hebben we twee Video's gepubliceerd met stapsgewijze instructies voor de volgende scenario's:

| Vervangen van on-premises bestands servers met Azure Files (inclusief de installatie van een persoonlijke koppeling voor bestanden en AD-verificatie) | Azure Files gebruiken als de profiel container voor het virtuele Windows-bureau blad (inclusief Setup in AD-verificatie en FsLogix-configuratie)  |
|-|-|
| [![Screen cast van het vervangen van on-premises bestands servers video-klik om af te spelen.](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![Screen cast van het gebruik van Azure Files als de profiel container video-klik om af te spelen.](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>Vereisten 

Voordat u AD DS verificatie voor Azure-bestands shares inschakelt, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid: 

- Selecteer of maak uw [AD DS-omgeving](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) en [Synchroniseer deze met Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) met Azure AD Connect. 

    U kunt de functie inschakelen op een nieuwe of bestaande on-premises AD DS omgeving. Identiteiten die worden gebruikt voor toegang moeten worden gesynchroniseerd met Azure AD. De Azure AD-Tenant en de bestands share die u wilt openen, moeten zijn gekoppeld aan hetzelfde abonnement.

- Domein: lid worden van een on-premises machine of een Azure-VM naar een on-premises AD DS. Zie lid worden van [een computer aan een domein](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)voor meer informatie over het koppelen van een domein.

    Als uw computer geen lid is van een domein dat is gekoppeld aan een AD DS, kunt u mogelijk nog steeds AD-referenties gebruiken voor verificatie als uw computer een regel van het gezichts vermogen van de AD-domein controller heeft.

- Selecteer of maak een Azure-opslag account.  Voor optimale prestaties raden we u aan het opslag account in dezelfde regio te implementeren als de client van waaruit u toegang wilt krijgen tot de share. Koppel vervolgens [de Azure-bestands share](storage-how-to-use-files-windows.md) aan de sleutel van uw opslag account. Bij het koppelen met de sleutel van het opslag account wordt de verbinding gecontroleerd.

    Zorg ervoor dat het opslag account dat uw bestands shares bevat, nog niet is geconfigureerd voor Azure AD DS-verificatie. Als Azure Files Azure AD DS-verificatie is ingeschakeld voor het opslag account, moet het worden uitgeschakeld voordat u de lokale AD DS kunt wijzigen. Dit betekent dat bestaande Acl's die zijn geconfigureerd in azure AD DS-omgeving opnieuw moeten worden geconfigureerd voor de juiste machtigings afdwinging.


    Als u problemen ondervindt bij het maken van verbinding met Azure Files, raadpleegt u [het hulp programma voor probleem oplossing dat is gepubliceerd voor Azure files-montage fouten in Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). We bieden ook [richt lijnen](./storage-files-faq.md#on-premises-access) voor het omzeilen van scenario's wanneer poort 445 wordt geblokkeerd. 


- Maak een relevante netwerk configuratie voordat u AD DS verificatie voor uw Azure-bestands shares inschakelt en configureert. Zie [Azure files netwerk overwegingen](storage-files-networking-overview.md) voor meer informatie.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Files verificatie met AD DS is beschikbaar in [alle open bare en gov regio's van Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Overzicht

Als u van plan bent om netwerk configuraties in te scha kelen op uw bestands share, raden we u aan om het artikel over [netwerk overwegingen](./storage-files-networking-overview.md) te lezen en de gerelateerde configuratie te volt ooien voordat u AD DS verificatie inschakelt.

Door AD DS-verificatie in te scha kelen voor uw Azure-bestands shares kunt u uw Azure-bestands shares verifiëren met uw on-premises AD DS referenties. Verder kunt u hiermee uw machtigingen beter beheren om nauw keurig toegangs beheer toe te staan. Hiervoor moet u identiteiten synchroniseren van on-premises AD DS naar Azure AD met AD Connect. U beheert de toegang op share niveau met identiteiten die zijn gesynchroniseerd met Azure AD, terwijl u toegang tot bestanden/share niveau beheert met on-premises AD DS referenties.

Voer vervolgens de volgende stappen uit om Azure Files voor AD DS-verificatie in te stellen: 

1. [Deel een: AD DS-verificatie inschakelen voor uw opslag account](storage-files-identity-ad-ds-enable.md)

1. [Deel twee: toegangs machtigingen voor een share toewijzen aan de Azure AD-identiteit (een gebruiker, groep of Service-Principal) die is gesynchroniseerd met de doel-AD-identiteit](storage-files-identity-ad-ds-assign-permissions.md)

1. [Deel drie: Windows-Acl's configureren via SMB voor mappen en bestanden](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Deel vier: een Azure-bestands share koppelen aan een virtuele machine die is gekoppeld aan uw AD DS](storage-files-identity-ad-ds-mount-file-share.md)

1. [Het wacht woord van de identiteit van uw opslag account bijwerken in AD DS](storage-files-identity-ad-ds-update-password.md)

In het volgende diagram ziet u de end-to-end werk stroom voor het inschakelen van Azure AD-verificatie via SMB voor Azure-bestands shares. 

![Bestanden AD-werk stroom diagram](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Identiteiten die worden gebruikt voor toegang tot Azure-bestands shares, moeten worden gesynchroniseerd met Azure AD om bestands machtigingen op share niveau af te dwingen via het Azure RBAC-model [(op rollen gebaseerd toegangs beheer)](../../role-based-access-control/overview.md) . [Windows-dacl's](/previous-versions/technet-magazine/cc161041(v=msdn.10)) voor bestanden/mappen die zijn overgedragen van bestaande bestands servers blijven behouden en worden afgedwongen. Dit biedt naadloze integratie met uw bedrijfs AD DS omgeving. Wanneer u on-premises bestands servers vervangt door Azure-bestands shares, hebben bestaande gebruikers toegang tot Azure-bestands shares van hun huidige clients met eenmalige aanmelding, zonder enige wijziging van de referenties die in gebruik zijn.  

## <a name="next-steps"></a>Volgende stappen

Als u on-premises AD DS verificatie voor uw Azure-bestands share wilt inschakelen, gaat u verder met het volgende artikel:

[Deel een: AD DS verificatie inschakelen voor uw account](storage-files-identity-ad-ds-enable.md)
