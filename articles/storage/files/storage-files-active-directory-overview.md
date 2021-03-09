---
title: Overzicht-Azure Files autorisatie op basis van een identiteit
description: Azure Files ondersteunt op identiteit gebaseerde verificatie via SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) en Active Directory. Uw virtuele Windows-machines (Vm's) die lid zijn van een domein, kunnen vervolgens toegang krijgen tot Azure-bestands shares met Azure AD-referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 8887243f953a7bb000033a2e56b4655b93cd7ca8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519789"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Overzicht van Azure Files verificatie opties op basis van een identiteit voor SMB-toegang
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Zie [on-premises Active Directory Domain Services authenticatie inschakelen via SMB voor Azure-bestands shares](storage-files-identity-auth-active-directory-enable.md)voor meer informatie over het inschakelen van on-premises Active Directory Domain Services verificatie voor Azure-bestands shares.

Zie [Azure Active Directory Domain Services-verificatie inschakelen voor Azure files](storage-files-identity-auth-active-directory-domain-service-enable.md)voor meer informatie over het inschakelen van Azure AD DS-verificatie voor Azure-bestands shares.

## <a name="glossary"></a>Woordenlijst 
Het is handig om enkele belang rijke termen te begrijpen met betrekking tot Azure AD domain service-verificatie via SMB voor Azure-bestands shares:

-   **Kerberos-verificatie**

    Kerberos is een authenticatieprotocol dat wordt gebruikt om de identiteit van een gebruiker of host te controleren. Zie [overzicht van Kerberos-verificatie](/windows-server/security/kerberos/kerberos-authentication-overview)voor meer informatie over Kerberos.

-  **SMB-protocol (Server Message Block)**

    SMB is een standaard protocol voor het delen van netwerk bestanden. SMB is ook bekend als common Internet File System of CIFS. Zie het [overzicht van micro soft SMB-protocol en CIFS-protocol](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)voor meer informatie over SMB.

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. Azure AD is een combi natie van basis Directory Services, Toegangs beheer voor toepassingen en identiteits beveiliging in één oplossing. Virtuele Windows-machines (Vm's) die zijn toegevoegd aan Azure AD, hebben geen toegang tot Azure-bestands shares met uw Azure AD-referenties. Zie [Wat is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md) voor meer informatie.

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie. Deze services zijn volledig compatibel met Active Directory Domain Services. Zie [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)voor meer informatie.

- **On-premises Active Directory Domain Services (AD DS)**

    De integratie van on-premises Active Directory Domain Services (AD DS) met Azure Files biedt de methoden voor het opslaan van Directory gegevens wanneer deze beschikbaar zijn voor netwerk gebruikers en beheerders. Beveiliging is geïntegreerd met AD DS via aanmeldings verificatie en toegangs beheer voor objecten in de Directory. Met één netwerk aanmelding kunnen beheerders Directory gegevens en organisatie beheren in hun netwerk, en geautoriseerde netwerk gebruikers kunnen overal toegang krijgen tot bronnen in het netwerk. AD DS wordt doorgaans goedgekeurd door ondernemingen in on-premises omgevingen en AD DS referenties worden gebruikt als de identiteit voor toegangs beheer. Zie [Active Directory Domain Services Overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)voor meer informatie.

-   **Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)**

    Met op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) hebt u verfijnd toegang tot Azure. Met behulp van Azure RBAC kunt u de toegang tot resources beheren door gebruikers de minste machtigingen te verlenen die nodig zijn om hun taken uit te voeren. Zie [Wat is Azure op rollen gebaseerd toegangs beheer (Azure RBAC)?](../../role-based-access-control/overview.md)voor meer informatie over Azure RBAC.

## <a name="common-use-cases"></a>Algemene scenario’s

Verificatie op basis van identiteit en ondersteuning voor Windows-Acl's op Azure Files is het meest geschikt voor de volgende use-cases:

### <a name="replace-on-premises-file-servers"></a>On-premises bestands servers vervangen

Het afmaken en vervangen van verspreide on-premises bestands servers is een veelvoorkomend probleem dat elke onderneming in hun IT-moderniserings traject tegen komt. Azure-bestands shares met on-premises AD DS verificatie is het meest geschikt wanneer u de gegevens naar Azure Files kunt migreren. Met een volledige migratie kunt u profiteren van de hoge Beschik baarheid en schaal baarheid, terwijl ook de wijzigingen aan de client zijde worden geminimaliseerd. Het biedt een naadloze migratie-ervaring voor eind gebruikers, zodat ze hun gegevens met dezelfde referenties kunnen blijven gebruiken met hun bestaande computers die lid zijn van een domein.

### <a name="lift-and-shift-applications-to-azure"></a>Toepassingen naar Azure tillen en verplaatsen

Wanneer u toepassingen naar de Cloud verplaatst en verschuift, wilt u hetzelfde verificatie model blijven gebruiken voor uw gegevens. Als we de op id's gebaseerde ervaring voor toegangs beheer uitbreiden naar Azure-bestands shares, is het niet meer nodig om uw toepassing te wijzigen in moderne authenticatie methoden en de acceptatie van de cloud te versnellen. Azure-bestands shares bieden de mogelijkheid om te integreren met Azure AD DS of een on-premises AD DS voor verificatie. Als uw abonnement een 100% in de Cloud is en de inspanningen voor het beheer van Cloud infrastructuren tot een minimum beperken, is Azure AD DS beter geschikt als een volledig beheerde domein service. Als u volledige compatibiliteit met AD DS mogelijkheden nodig hebt, kunt u overwegen uw AD DS omgeving uit te breiden naar de Cloud door zelf hosting van domein controllers op Vm's. In beide gevallen bieden we de flexibiliteit om de domein services te kiezen die geschikt zijn voor uw bedrijf.

### <a name="backup-and-disaster-recovery-dr"></a>Back-ups en herstel na nood gevallen (DR)

Als u uw primaire bestands opslag on-premises houdt, kunnen Azure-bestands shares fungeren als ideale opslag voor back-ups of DR, om de bedrijfs continuïteit te verbeteren. U kunt Azure-bestands shares gebruiken om een back-up te maken van uw gegevens van bestaande bestands servers, terwijl u Windows-DACL'S behoudt. Voor nood herstel scenario's kunt u een verificatie optie configureren ter ondersteuning van de juiste handhaving van het toegangs beheer bij een failover.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De volgende tabel bevat een overzicht van de ondersteunde Azure file shares-verificatie scenario's voor Azure AD DS en on-premises AD DS. U kunt het beste de domein service selecteren die u voor uw client omgeving hebt gekozen voor de integratie met Azure Files. Als u AD DS al on-premises of in azure hebt geïnstalleerd waarbij uw apparaten zijn gekoppeld aan uw AD, moet u ervoor kiezen om AD DS te gebruiken voor de verificatie van Azure-bestands shares. Als u Azure AD DS al hebt toegepast, moet u dit ook gebruiken voor verificatie bij Azure-bestands shares.


|Azure AD DS-verificatie  | On-premises AD DS verificatie  |
|---------|---------|
|Azure-AD DS die lid zijn van een Windows-computer hebben toegang tot Azure-bestands shares met Azure AD-referenties via SMB.     |On-premises AD DS-of Azure AD DS-gekoppelde Windows-machines hebben toegang tot Azure-bestands shares met on-premises Active Directory referenties die zijn gesynchroniseerd met Azure AD via SMB. Uw-client moet een gezichts lijn hebben voor uw AD DS.        |

### <a name="restrictions"></a>Beperkingen

- Azure AD DS en on-premises AD DS verificatie bieden geen ondersteuning voor verificatie voor computer accounts. U kunt in plaats daarvan een service aanmeldings account gebruiken.
- Geen van de Azure AD DS-verificatie en on-premises AD DS-verificatie wordt ondersteund voor apparaten die zijn toegevoegd aan Azure AD of Azure AD-geregistreerde apparaten.
- Azure-bestands shares bieden alleen ondersteuning voor verificatie op basis van identiteiten met een van de volgende domein Services, [Azure Active Directory Domain Services (Azure AD DS)](#azure-ad-ds) of [on-premises Active Directory Domain Services (AD DS)](#ad-ds).
- Geen van beide verificatie methoden op basis van een identiteit worden ondersteund met Network File System (NFS), een preview-versie.

## <a name="advantages-of-identity-based-authentication"></a>Voor delen van verificatie op basis van identiteit
Verificatie op basis van identiteit voor Azure Files biedt verschillende voor delen ten opzichte van het gebruik van gedeelde sleutel verificatie:

-   **Breid de traditionele toegang tot de bestands share op basis van identiteiten uit naar de Cloud met on-premises AD DS en Azure AD DS**  
    Als u van plan bent om uw toepassing te verplaatsen naar de Cloud, vervangen van traditionele bestands servers met Azure-bestands shares, wilt u mogelijk uw toepassing verifiëren met on-premises AD DS of Azure AD DS referenties voor toegang tot bestands gegevens. Azure Files ondersteunt het gebruik van zowel on-premises AD DS als Azure AD DS referenties om toegang te krijgen tot Azure-bestands shares via SMB vanuit on-premises AD DS of Azure AD DS Vm's die aan een domein zijn gekoppeld.

-   **Gedetailleerd toegangs beheer voor Azure-bestands shares afdwingen**  
    U kunt machtigingen verlenen aan een specifieke identiteit op share-, map-of bestands niveau. Stel bijvoorbeeld dat u verschillende teams hebt die één Azure-bestands share gebruiken voor samen werking tussen projecten. U kunt alle teams toegang verlenen tot niet-gevoelige directory's, terwijl de toegang tot mappen met alleen gevoelige financiële gegevens wordt beperkt tot uw financiële team. 

-   **Back-ups maken van Windows-Acl's (ook wel NTFS genoemd) samen met uw gegevens**  
    U kunt Azure-bestands shares gebruiken om een back-up te maken van uw bestaande on-premises bestands shares. Azure Files behoudt uw Acl's samen met uw gegevens wanneer u een back-up maakt van een bestands share naar Azure-bestands shares via SMB.

## <a name="how-it-works"></a>Uitleg

Azure-bestands shares maken gebruik van het Kerberos-protocol voor verificatie met on-premises AD DS of Azure AD DS. Wanneer een identiteit die is gekoppeld aan een gebruiker of toepassing die wordt uitgevoerd op een client, probeert toegang te krijgen tot gegevens in azure-bestands shares, wordt de aanvraag verzonden naar de domein service, een AD DS of Azure AD DS, om de identiteit te verifiëren. Als de verificatie is geslaagd, wordt een Kerberos-token geretourneerd. De client verzendt een aanvraag met het Kerberos-token en Azure-bestands shares die token gebruiken om de aanvraag te autoriseren. Azure-bestands shares ontvangen alleen het Kerberos-token, geen toegang tot referenties.

Voordat u verificatie op basis van identiteiten op Azure-bestands shares kunt inschakelen, moet u eerst uw domein omgeving instellen.

### <a name="ad-ds"></a>AD DS

Voor on-premises AD DS-verificatie moet u uw AD-domein controllers en domein instellen als lid worden van uw machines of Vm's. U kunt uw domein controllers hosten op virtuele machines van Azure of on-premises. In beide gevallen moet de client die lid is van het domein een regel voor het gezichts vermogen van de domein service hebben, zodat deze zich binnen het bedrijfs netwerk of het virtuele netwerk (VNET) van uw domein service bevinden.

In het volgende diagram ziet u on-premises AD DS verificatie voor Azure-bestands shares via SMB. De on-premises AD DS moeten worden gesynchroniseerd met Azure AD met behulp van Azure AD Connect-synchronisatie. Alleen hybride gebruikers in zowel on-premises AD DS als Azure AD kunnen worden geverifieerd en geautoriseerd voor toegang tot de Azure-bestands share. Dit komt omdat de machtiging op share niveau is geconfigureerd op basis van de identiteit die wordt weer gegeven in azure AD, waarbij de machtiging op het niveau van de map/bestand wordt afgedwongen in AD DS. Zorg ervoor dat u de machtigingen correct configureert voor dezelfde hybride gebruiker.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="Diagram van on-premises AD DS verificatie voor Azure-bestands shares via SMB.":::

### <a name="azure-ad-ds"></a>Azure AD DS

Voor Azure AD DS-verificatie moet u Azure AD Domain Services en domein inschakelen die lid zijn van de Vm's die u wilt gebruiken om toegang tot bestands gegevens te krijgen. De virtuele machine die aan een domein is gekoppeld, moet zich in hetzelfde virtuele netwerk (VNET) bevinden als uw Azure-AD DS. 

Het volgende diagram vertegenwoordigt de werk stroom voor Azure AD DS-verificatie voor Azure-bestands shares via SMB. Het volgt een vergelijkbaar patroon voor on-premises AD DS verificatie voor Azure-bestands shares. Er zijn twee belang rijke verschillen:

- Eerst hoeft u niet de identiteit in azure AD DS te maken om het opslag account aan te duiden. Dit wordt uitgevoerd door het activerings proces op de achtergrond.

- Ten tweede kunnen alle gebruikers die in azure AD bestaan, worden geverifieerd en geautoriseerd. De gebruiker kan alleen Cloud of Hybrid zijn. De synchronisatie van Azure AD naar Azure AD DS wordt beheerd door het platform zonder dat er gebruikers configuratie is vereist. De client moet echter lid zijn van een domein aan Azure AD DS en kan niet worden opgenomen in azure AD. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Diagram":::

### <a name="enable-identity-based-authentication"></a>Verificatie op basis van identiteit inschakelen

U kunt verificatie op basis van een identiteit inschakelen met Azure AD DS of on-premises AD DS voor Azure-bestands shares in uw nieuwe en bestaande opslag accounts. Er kan slechts één domein service worden gebruikt voor verificatie van bestands toegang op het opslag account. Dit is van toepassing op alle bestands shares in het account. Gedetailleerde richt lijnen voor het instellen van uw bestands shares voor verificatie met Azure AD DS in ons artikel [schakel Azure Active Directory Domain Services-verificatie in op Azure files](storage-files-identity-auth-active-directory-domain-service-enable.md) en richt lijnen voor on-premises AD DS in ons andere artikel, om [on-premises Active Directory Domain Services verificatie in te SCHA kelen via SMB voor Azure-bestands shares](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Machtigingen op share niveau voor Azure Files configureren

Als Azure AD DS of een on-premises AD DS-verificatie is ingeschakeld, kunt u de ingebouwde rollen van Azure gebruiken of aangepaste rollen configureren voor Azure AD-identiteiten en toegangs rechten toewijzen aan bestands shares in uw opslag accounts. Met de toegewezen machtiging kan de verleende identiteit alleen toegang krijgen tot de share, niets anders, niet zelfs de hoofdmap. U moet nog steeds machtigingen op Directory-of bestands niveau configureren voor Azure-bestands shares.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Machtigingen op Directory-of bestands niveau configureren voor Azure Files

Azure-bestands shares dwingen standaard Windows-bestands machtigingen af op Directory-en bestands niveau, met inbegrip van de hoofdmap. De configuratie van machtigingen op Directory-of bestands niveau wordt zowel voor SMB als voor de REST ondersteund. Koppel de doel bestands share van uw VM en Configureer machtigingen met behulp van Windows Verkenner, Windows [icacls](/windows-server/administration/windows-commands/icacls)of de [set-ACL-](/powershell/module/microsoft.powershell.security/get-acl) opdracht.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>De sleutel van het opslag account voor super gebruiker-machtigingen gebruiken

Een gebruiker met de sleutel van het opslag account kan toegang krijgen tot Azure-bestands shares met super gebruikers machtigingen. Super gebruiker-machtigingen slaan alle beperkingen voor toegangs beheer over.

> [!IMPORTANT]
> Onze aanbevolen beveiligings best practice is om te voor komen dat u de sleutels van uw opslag account deelt en waar mogelijk op identiteit gebaseerde verificatie gebruikt.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Map-en bestands-Acl's behouden bij het importeren van gegevens in azure-bestands shares

Azure Files ondersteunt het behouden van Acl's op Directory-of bestands niveau bij het kopiëren van gegevens naar Azure-bestands shares. U kunt Acl's op een map of bestand naar Azure-bestands shares kopiëren met behulp van Azure File Sync of gemeen schappelijke sluit voor bestands verplaatsing. U kunt [Robocopy](/windows-server/administration/windows-commands/robocopy) bijvoorbeeld gebruiken met de `/copy:s` vlag voor het kopiëren van gegevens en Acl's naar een Azure-bestands share. Acl's blijven standaard behouden, u bent niet verplicht om verificatie op basis van een identiteit in te scha kelen voor uw opslag account om Acl's te behouden.

## <a name="pricing"></a>Prijzen
Er worden geen extra kosten in rekening gebracht voor het inschakelen van verificatie op basis van identiteit via SMB voor uw opslag account. Zie [Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/) en [Azure AD Domain Services prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/)voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie over Azure Files en verificatie op basis van identiteit via SMB:

- [Een Azure Files-implementatie plannen](storage-files-planning.md)
- [On-premises Active Directory Domain Services authenticatie inschakelen via SMB voor Azure-bestands shares](storage-files-identity-auth-active-directory-enable.md)
- [Azure Active Directory Domain Services authenticatie inschakelen op Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Veelgestelde vragen](storage-files-faq.md)