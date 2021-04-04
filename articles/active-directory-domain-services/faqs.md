---
title: Veelgestelde vragen over Azure AD Domain Services | Microsoft Docs
description: Enkele veelgestelde vragen over configuratie, beheer en beschik baarheid voor Azure Active Directory Domain Services lezen en begrijpen
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/09/2021
ms.author: justinha
ms.openlocfilehash: 3d0f2b44f37cb318be2117b5dc5d8b42b418ff19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090986"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Veelgestelde vragen over Azure Active Directory (AD) Domain Services

Op deze pagina vindt u antwoorden op veelgestelde vragen over Azure Active Directory Domain Services.

## <a name="configuration"></a>Configuratie

* [Kan ik meerdere beheerde domeinen maken voor één Azure AD-adres lijst?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Kan ik Azure AD Domain Services inschakelen in een klassiek virtueel netwerk?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Kan ik Azure AD Domain Services inschakelen in een Azure Resource Manager virtueel netwerk?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Kan ik mijn bestaande beheerde domein migreren van een klassiek virtueel netwerk naar een virtueel netwerk van Resource Manager?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Kan ik Azure AD Domain Services in een Azure CSP-abonnement (Cloud Solution Provider) inschakelen?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Kan ik Azure AD Domain Services inschakelen in een federatieve Azure AD-adres lijst? Ik Synchroniseer geen wacht woord-hashes naar Azure AD. Kan ik Azure AD Domain Services voor deze map inschakelen?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Kan ik Azure AD Domain Services beschikbaar maken in meerdere virtuele netwerken binnen mijn abonnement?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Kan ik Azure AD Domain Services inschakelen met behulp van Power shell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kan ik Azure AD Domain Services inschakelen met een resource manager-sjabloon?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Kan ik domein controllers toevoegen aan een Azure AD Domain Services beheerd domein?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Kunnen gast gebruikers worden uitgenodigd voor mijn Directory Azure AD Domain Services gebruiken?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Kan ik een bestaand Azure AD Domain Services beheerd domein verplaatsen naar een ander abonnement, een andere resource groep, een andere regio of een virtueel netwerk?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Bevat Azure AD Domain Services opties voor hoge Beschik baarheid?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan ik meerdere beheerde domeinen maken voor één Azure AD-adres lijst?
Nee. U kunt slechts één beheerd domein maken dat door Azure AD Domain Services wordt onderhouden voor één Azure AD-adres lijst.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in een klassiek virtueel netwerk?
Klassieke virtuele netwerken worden niet ondersteund voor nieuwe implementaties. Bestaande beheerde domeinen die in klassieke virtuele netwerken zijn geïmplementeerd, worden nog steeds ondersteund totdat ze zijn ingetrokken op 1 maart 2023. Voor bestaande implementaties kunt u [Azure AD Domain Services migreren van het klassieke virtuele netwerk model naar Resource Manager](migrate-from-classic-vnet.md).

Zie de [officiële afschaffing](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)voor meer informatie.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in een Azure Resource Manager virtueel netwerk?
Ja. Azure AD Domain Services kan worden ingeschakeld in een Azure Resource Manager virtueel netwerk. Klassieke virtuele netwerken van Azure zijn niet meer beschikbaar wanneer u een beheerd domein maakt.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan ik mijn bestaande beheerde domein migreren van een klassiek virtueel netwerk naar een virtueel netwerk van Resource Manager?
Ja. Zie [Azure AD Domain Services migreren van het klassieke virtuele netwerk model naar Resource Manager](migrate-from-classic-vnet.md)voor meer informatie.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan ik Azure AD Domain Services in een Azure CSP-abonnement (Cloud Solution Provider) inschakelen?
Ja. Zie [Azure AD Domain Services inschakelen in azure CSP-abonnementen](csp.md)voor meer informatie.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan ik Azure AD Domain Services inschakelen in een federatieve Azure AD-adres lijst? Ik Synchroniseer geen wacht woord-hashes naar Azure AD. Kan ik Azure AD Domain Services voor deze map inschakelen?
Nee. Azure AD Domain Services moet toegang hebben tot de wacht woord-hashes van gebruikers accounts om gebruikers te verifiëren via NTLM of Kerberos. In een federatieve map worden wacht woord-hashes niet opgeslagen in de Azure AD-adres lijst. Azure AD Domain Services werkt daarom niet met deze Azure AD-mappen.

Als u echter Azure AD Connect gebruikt voor het synchroniseren van wacht woord-hashes, kunt u Azure AD Domain Services gebruiken omdat de wacht woord-hash-waarden zijn opgeslagen in azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan ik Azure AD Domain Services beschikbaar maken in meerdere virtuele netwerken binnen mijn abonnement?
De service zelf biedt geen rechtstreekse ondersteuning voor dit scenario. Uw beheerde domein is slechts in één virtueel netwerk tegelijk beschikbaar. U kunt echter de verbinding tussen meerdere virtuele netwerken configureren om Azure AD Domain Services naar andere virtuele netwerken beschikbaar te maken. Zie [verbinding maken met virtuele netwerken in azure met behulp van VPN-gateways](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) of [peering op het virtuele netwerk](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan ik Azure AD Domain Services inschakelen met behulp van Power shell?
Ja. Zie [Azure AD Domain Services inschakelen met behulp van Power shell](powershell-create-instance.md)voor meer informatie.

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan ik Azure AD Domain Services inschakelen met een resource manager-sjabloon?
Ja, u kunt een Azure AD Domain Services beheerd domein maken met behulp van een resource manager-sjabloon. Een Service-Principal en een Azure AD-groep voor beheer moeten worden gemaakt met behulp van de Azure Portal of Azure PowerShell voordat de sjabloon wordt geïmplementeerd. Zie [een door Azure AD DS beheerd domein maken met behulp van een Azure Resource Manager sjabloon](template-create-instance.md)voor meer informatie. Wanneer u een Azure AD Domain Services beheerd domein maakt in de Azure Portal, is er ook een optie om de sjabloon te exporteren voor gebruik met aanvullende implementaties.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan ik domein controllers toevoegen aan een Azure AD Domain Services beheerd domein?
Nee. Het door Azure AD Domain Services verschafte domein is een beheerd domein. U hoeft geen domein controllers in te richten, configureren of anderszins te beheren voor dit domein. Deze beheer activiteiten worden door micro soft als service verschaft. Daarom kunt u geen extra domein controllers (lezen/schrijven of alleen-lezen) toevoegen voor het beheerde domein.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Kunnen gast gebruikers worden uitgenodigd voor mijn Directory Azure AD Domain Services gebruiken?
Nee. Gast gebruikers die zijn uitgenodigd voor uw Azure AD-adres lijst met het [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) -invite-proces, worden gesynchroniseerd met uw Azure AD Domain Services beheerde domein. Wacht woorden voor deze gebruikers worden echter niet opgeslagen in uw Azure AD-adres lijst. Daarom heeft Azure AD Domain Services geen manier om NTLM-en Kerberos-hashes te synchroniseren voor deze gebruikers in uw beheerde domein. Dergelijke gebruikers kunnen zich niet aanmelden of computers toevoegen aan het beheerde domein.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Kan ik een bestaand Azure AD Domain Services beheerd domein verplaatsen naar een ander abonnement, een andere resource groep, een andere regio of een virtueel netwerk?
Nee. Nadat u een Azure AD Domain Services beheerd domein hebt gemaakt, kunt u het beheerde domein niet verplaatsen naar een andere resource groep, een virtueel netwerk, een abonnement, enzovoort. Zorg ervoor dat u het meest geschikte abonnement, de resource groep, de regio en het virtuele netwerk selecteert wanneer u het beheerde domein implementeert.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Bevat Azure AD Domain Services opties voor hoge Beschik baarheid?

Ja. Elk Azure AD Domain Services beheerd domein bevat twee domein controllers. U kunt deze domein controllers niet beheren of er geen verbinding mee maken, die deel uitmaken van de beheerde service. Als u Azure AD Domain Services implementeert in een regio die Beschikbaarheidszones ondersteunt, worden de domein controllers gedistribueerd over meerdere zones. In regio's die geen ondersteuning bieden voor Beschikbaarheidszones, worden de domein controllers verdeeld over beschikbaarheids sets. U hebt geen configuratie opties of beheer controle over deze distributie. Zie [beschikbaarheids opties voor virtuele machines in azure](../virtual-machines/availability.md)voor meer informatie.

## <a name="administration-and-operations"></a>Beheer en bewerkingen

* [Kan ik verbinding maken met de domein controller voor mijn beheerde domein met behulp van Extern bureaublad?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Ik heb Azure AD Domain Services ingeschakeld. Welk gebruikers account moet ik gebruiken om computers toe te voegen aan dit domein?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Heb ik domein beheerders rechten voor het beheerde domein dat wordt meegeleverd door Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan ik groepslid maatschappen wijzigen met behulp van LDAP of andere AD-beheer Programma's in beheerde domeinen?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Hoe lang duurt het voor de wijzigingen die ik in mijn Azure AD-adres lijst aanbrengt, worden weer gegeven in mijn beheerde domein?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kan ik het schema uitbreiden van het beheerde domein dat wordt meegeleverd met Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan ik DNS-records in mijn beheerde domein wijzigen of toevoegen?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Wat is het beleid voor de levens duur van wacht woorden op een beheerd domein?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Biedt Azure AD Domain Services AD-account vergrendelings beveiliging?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Kan ik Distributed File System (DFS) en replicatie binnen Azure AD Domain Services configureren?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Hoe worden Windows-updates toegepast in Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan ik verbinding maken met de domein controller voor mijn beheerde domein met behulp van Extern bureaublad?
Nee. U bent niet gemachtigd om verbinding te maken met domein controllers voor het beheerde domein met behulp van Extern bureaublad. Leden van de groep *Aad DC-Administrators* kunnen het beheerde domein beheren met AD-beheer Programma's zoals het Active Directory Administration Center (ADAC) of AD Power shell. Deze hulpprogram ma's worden geïnstalleerd met behulp van de functie *Remote Server Administration Tools* op een Windows-Server die is gekoppeld aan het beheerde domein. Zie [een beheer-VM maken voor het configureren en beheren van een Azure AD Domain Services beheerd domein](tutorial-create-management-vm.md)voor meer informatie.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ik heb Azure AD Domain Services ingeschakeld. Welk gebruikers account moet ik gebruiken om computers toe te voegen aan dit domein?
Elk gebruikers account dat deel uitmaakt van het beheerde domein, kan deel nemen aan een virtuele machine. Leden van de groep *Aad DC-Administrators* krijgen toegang tot extern bureau blad voor computers die zijn toegevoegd aan het beheerde domein.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Heb ik domein beheerders rechten voor het beheerde domein dat wordt meegeleverd door Azure AD Domain Services?
Nee. U hebt geen beheerders bevoegdheden verleend voor het beheerde domein. De bevoegdheden *domein beheerder* en *ondernemings Administrator* zijn niet beschikbaar voor gebruik binnen het domein. Leden van de groep domein Administrators of ondernemings Administrators in uw on-premises Active Directory zijn ook geen machtigingen voor domein/ondernemings Administrator verleend voor het beheerde domein.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan ik groepslid maatschappen wijzigen met behulp van LDAP of andere AD-beheer Programma's in beheerde domeinen?
Gebruikers en groepen die zijn gesynchroniseerd van Azure Active Directory naar Azure AD Domain Services kunnen niet worden gewijzigd, omdat de bron van oorsprong is Azure Active Directory. Dit geldt ook voor het verplaatsen van gebruikers of groepen van de AADDC gebruikers beheerde organisatie-eenheid naar een aangepaste organisatie-eenheid. Elke gebruiker of groep die afkomstig is van het beheerde domein, kan worden gewijzigd.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hoe lang duurt het voor de wijzigingen die ik in mijn Azure AD-adres lijst aanbrengt, worden weer gegeven in mijn beheerde domein?
Wijzigingen die zijn aangebracht in uw Azure AD-adres lijst met behulp van de Azure AD-gebruikers interface of Power shell, worden automatisch gesynchroniseerd met uw beheerde domein. Dit synchronisatie proces wordt op de achtergrond uitgevoerd. Er is geen gedefinieerde tijds periode voor deze synchronisatie om alle object wijzigingen te volt ooien.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan ik het schema uitbreiden van het beheerde domein dat wordt meegeleverd met Azure AD Domain Services?
Nee. Het schema wordt door micro soft beheerd voor het beheerde domein. Schema-uitbrei dingen worden niet ondersteund door Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan ik DNS-records in mijn beheerde domein wijzigen of toevoegen?
Ja. Leden van de groep *Aad DC-Administrators* krijgen *DNS-administrator* bevoegdheden voor het wijzigen van DNS-records in het beheerde domein. Deze gebruikers kunnen de DNS-beheer console gebruiken op een computer met Windows Server die is toegevoegd aan het beheerde domein voor het beheren van DNS. Als u de DNS-beheer console wilt gebruiken, installeert u de *Hulpprogram ma's voor DNS-server*, die deel uitmaken van de optionele functie *Remote Server Administration Tools* op de-server. Zie [DNS beheren in een Azure AD Domain Services beheerd domein](manage-dns.md)voor meer informatie.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Wat is het beleid voor de levens duur van wacht woorden op een beheerd domein?
De standaard levensduur van het wacht woord voor een Azure AD Domain Services beheerd domein is 90 dagen. De levens duur van het wacht woord is niet gesynchroniseerd met de levens duur van het wacht woord dat is geconfigureerd in azure AD. Daarom hebt u mogelijk een situatie waarin gebruikers wachtwoorden verlopen in uw beheerde domein, maar nog steeds geldig zijn in azure AD. In dergelijke scenario's moeten gebruikers hun wacht woord wijzigen in azure AD en wordt het nieuwe wacht woord gesynchroniseerd met uw beheerde domein. Als u de standaard levensduur van het wacht woord in een beheerd domein wilt wijzigen, kunt u [aangepaste wachtwoord beleidsregels maken en configureren.](password-policy.md)

Daarnaast wordt het Azure AD-wachtwoord beleid voor *DisablePasswordExpiration* gesynchroniseerd met een beheerd domein. Wanneer *DisablePasswordExpiration* wordt toegepast op een gebruiker in azure AD, is *DONT_EXPIRE_PASSWORD* toegepast op de waarde *userAccountControl* voor de gesynchroniseerde gebruiker in het beheerde domein.

Wanneer gebruikers hun wacht woord opnieuw instellen in azure AD, wordt het kenmerk *forceChangePasswordNextSignIn = True* toegepast. Een beheerd domein synchroniseert dit kenmerk vanuit Azure AD. Wanneer het beheerde domein detecteert dat *forceChangePasswordNextSignIn* is ingesteld voor een gesynchroniseerde gebruiker vanuit Azure AD, wordt het kenmerk *pwdLastSet* in het beheerde domein ingesteld op *0*, waardoor het wacht woord dat momenteel is ingesteld, ongeldig is.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Biedt Azure AD Domain Services AD-account vergrendelings beveiliging?
Ja. Vijf ongeldige wachtwoord pogingen binnen twee minuten op het beheerde domein zorgen ervoor dat een gebruikers account 30 minuten wordt vergrendeld. Na 30 minuten wordt het gebruikers account automatisch ontgrendeld. Bij ongeldige wachtwoord pogingen op het beheerde domein wordt het gebruikers account in azure AD niet vergrendeld. Het gebruikers account is alleen vergrendeld binnen uw Azure AD Domain Services beheerde domein. Zie voor meer informatie [wacht woord-en account vergrendelings beleid in beheerde domeinen](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Kan ik Distributed File System en replicatie binnen Azure AD Domain Services configureren?
Nee. Distributed File System (DFS) en replicatie zijn niet beschikbaar wanneer u Azure AD Domain Services gebruikt.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Hoe worden Windows-updates toegepast in Azure AD Domain Services?
Op domein controllers in een beheerd domein worden automatisch de vereiste Windows-updates toegepast. U kunt hier niets configureren of beheren. Zorg ervoor dat u geen regels voor netwerk beveiligings groepen maakt waarmee uitgaand verkeer naar Windows-updates wordt geblokkeerd. Voor uw eigen Vm's die zijn gekoppeld aan het beheerde domein, bent u verantwoordelijk voor het configureren en Toep assen van vereiste updates voor het besturings systeem en de toepassing.

## <a name="billing-and-availability"></a>Facturering en beschik baarheid

* [Is er Azure AD Domain Services een betaalde service?](#is-azure-ad-domain-services-a-paid-service)
* [Is er een gratis proef versie voor de service?](#is-there-a-free-trial-for-the-service)
* [Kan ik een Azure AD Domain Services beheerd domein onderbreken?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan ik een failover uitvoeren van Azure AD Domain Services naar een andere regio voor een DR-gebeurtenis?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan ik Azure AD Domain Services krijgen als onderdeel van de Enter prise Mobility Suite (EMS)? Heb ik Azure AD Premium nodig om Azure AD Domain Services te gebruiken?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [In welke Azure-regio's is de service beschikbaar?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Is er Azure AD Domain Services een betaalde service?
Ja. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/) voor meer informatie.

### <a name="is-there-a-free-trial-for-the-service"></a>Is er een gratis proef versie voor de service?
Azure AD Domain Services is opgenomen in de gratis proef versie voor Azure. U kunt zich registreren voor een [gratis proef versie van Azure voor één maand](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan ik een Azure AD Domain Services beheerd domein onderbreken?
Nee. Zodra u een Azure AD Domain Services beheerd domein hebt ingeschakeld, is de service beschikbaar in het geselecteerde virtuele netwerk totdat u het beheerde domein verwijdert. Er is geen manier om de service te onderbreken. De facturering blijft elk uur in beslag totdat u het beheerde domein verwijdert.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan ik een failover uitvoeren van Azure AD Domain Services naar een andere regio voor een DR-gebeurtenis?
Ja, om geografische tolerantie voor een beheerd domein te bieden, kunt u een extra [replicaset](tutorial-create-replica-set.md) maken in een gekoppeld virtueel netwerk in een Azure-regio die ondersteuning biedt voor Azure AD DS. Replica sets delen dezelfde naam ruimte en configuratie met het beheerde domein.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan ik Azure AD Domain Services krijgen als onderdeel van de Enter prise Mobility Suite (EMS)? Heb ik Azure AD Premium nodig om Azure AD Domain Services te gebruiken?
Nee. Azure AD Domain Services is een Azure-service voor betalen naar gebruik en maakt geen deel uit van EMS. Azure AD Domain Services kunnen worden gebruikt in combi natie met alle versies van Azure AD (gratis en Premium). U wordt gefactureerd per uur, afhankelijk van het gebruik.

### <a name="what-azure-regions-is-the-service-available-in"></a>In welke Azure-regio's is de service beschikbaar?
Raadpleeg de pagina [Azure-Services per regio](https://azure.microsoft.com/regions/#services/) voor een overzicht van de Azure-regio's waar Azure AD Domain Services beschikbaar is.

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg de [hand leiding](troubleshoot.md) voor het oplossen van problemen met oplossingen voor veelvoorkomende problemen met het configureren of beheren van Azure AD Domain Services.

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Active Directory Domain Services?](overview.md)voor meer informatie over Azure AD Domain Services.

Zie [een door Azure Active Directory Domain Services beheerd domein maken en configureren](tutorial-create-instance.md)om aan de slag te gaan.
