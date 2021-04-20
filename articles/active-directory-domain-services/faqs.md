---
title: Veelgestelde vragen over Azure AD Domain Services | Microsoft Docs
description: Lees en begrijp enkele veelgestelde vragen over configuratie, beheer en beschikbaarheid voor Azure Active Directory Domain Services
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
ms.openlocfilehash: 32dcf2b387231d50796de0036388b53cab83bf72
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749326"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Veelgestelde vragen (FAQ) over Azure Active Directory (AD) Domain Services

Op deze pagina vindt u antwoorden op veelgestelde vragen over Azure Active Directory Domain Services.

## <a name="configuration"></a>Configuratie

* [Kan ik meerdere beheerde domeinen maken voor één Azure AD-directory?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Kan ik Azure AD Domain Services inschakelen in een klassiek virtueel netwerk?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Kan ik de Azure AD Domain Services inschakelen in Azure Resource Manager virtueel netwerk?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Kan ik mijn bestaande beheerde domein migreren van een klassiek virtueel netwerk naar Resource Manager virtueel netwerk?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Kan ik Azure AD Domain Services inschakelen in een Azure CSP (Cloud Solution Provider)-abonnement?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Kan ik Azure AD Domain Services inschakelen in een federatief Azure AD-adreslijst? Ik synchroniseer geen wachtwoordhashes naar Azure AD. Kan ik Azure AD Domain Services inschakelen voor deze map?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Kan ik Azure AD Domain Services beschikbaar maken in meerdere virtuele netwerken binnen mijn abonnement?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Kan ik een Azure AD Domain Services powershell gebruiken?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kan ik een Azure AD Domain Services een Resource Manager inschakelen?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Kan ik domeincontrollers toevoegen aan een Azure AD Domain Services beheerd domein?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Kunnen gastgebruikers worden uitgenodigd voor mijn directory met Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Kan ik een bestaand Azure AD Domain Services beheerd domein verplaatsen naar een ander abonnement, resourcegroep, regio of virtueel netwerk?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Kan ik de naam van een bestaand Azure AD Domain Services wijzigen?](#can-i-rename-an-existing-azure-ad-domain-services-domain-name)
* [Bevat Azure AD Domain Services opties voor hoge beschikbaarheid?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan ik meerdere beheerde domeinen maken voor één Azure AD-directory?
Nee. U kunt slechts één beheerd domein maken dat wordt uitgevoerd door Azure AD Domain Services voor één Azure AD-directory.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in een klassiek virtueel netwerk?
Klassieke virtuele netwerken worden niet ondersteund voor nieuwe implementaties. Bestaande beheerde domeinen die zijn geïmplementeerd in klassieke virtuele netwerken, worden nog steeds ondersteund totdat ze op 1 maart 2023 worden gestopt. Voor bestaande implementaties kunt u de Azure AD Domain Services migreren van het klassieke [virtuele netwerkmodel naar Resource Manager](migrate-from-classic-vnet.md).

Zie de officiële kennisgeving over [afschaffing voor meer informatie.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in Azure Resource Manager virtueel netwerk?
Ja. Azure AD Domain Services kunnen worden ingeschakeld in een Azure Resource Manager virtueel netwerk. Klassieke virtuele Azure-netwerken zijn niet meer beschikbaar wanneer u een beheerd domein maakt.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan ik mijn bestaande beheerde domein migreren van een klassiek virtueel netwerk naar Resource Manager virtueel netwerk?
Ja. Zie Migreren van Azure AD Domain Services van het klassieke virtuele netwerkmodel naar Resource Manager [voor meer Resource Manager.](migrate-from-classic-vnet.md)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan ik Azure AD Domain Services inschakelen in Azure CSP (Cloud Solution Provider)-abonnement?
Ja. Zie How [to enable Azure AD Domain Services in Azure CSP subscriptions (Inschakelen van Azure CSP-abonnementen) voor meer informatie.](csp.md)

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan ik Azure AD Domain Services inschakelen in een federatief Azure AD-adreslijst? Ik synchroniseer geen wachtwoordhashes naar Azure AD. Kan ik Azure AD Domain Services inschakelen voor deze directory?
Nee. Als u gebruikers wilt verifiëren via NTLM of Kerberos, moet Azure AD Domain Services toegang hebben tot de wachtwoordhashes van gebruikersaccounts. In een federatief adreslijst worden wachtwoordhashes niet opgeslagen in de Azure AD-directory. Daarom werkt Azure AD Domain Services niet met dergelijke Azure AD-directories.

Als u echter een wachtwoordhashsynchronisatie Azure AD Connect, kunt u Azure AD Domain Services omdat de wachtwoordhashwaarden worden opgeslagen in Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan ik Azure AD Domain Services beschikbaar maken in meerdere virtuele netwerken binnen mijn abonnement?
De service zelf biedt geen directe ondersteuning voor dit scenario. Uw beheerde domein is slechts in één virtueel netwerk tegelijk beschikbaar. U kunt echter connectiviteit tussen meerdere virtuele netwerken configureren om deze Azure AD Domain Services andere virtuele netwerken. Zie virtuele netwerken in Azure verbinden met behulp van [VPN-gateways](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) of [peering voor virtuele netwerken voor meer informatie.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan ik een Azure AD Domain Services powershell gebruiken?
Ja. Zie How [to enable Azure AD Domain Services using PowerShell (PowerShell inschakelen) voor meer informatie.](powershell-create-instance.md)

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan ik de Azure AD Domain Services met behulp van een Resource Manager sjabloon?
Ja, u kunt een beheerd Azure AD Domain Services maken met behulp van een Resource Manager sjabloon. Een service-principal en Azure AD-groep voor beheer moeten worden gemaakt met behulp van de Azure Portal of Azure PowerShell voordat de sjabloon wordt geïmplementeerd. Zie Create an Azure AD DS managed domain using an Azure Resource Manager template (Een beheerd Azure Resource Manager [maken) voor meer informatie.](template-create-instance.md) Wanneer u een Azure AD Domain Services beheerd domein in de Azure Portal, is er ook een optie om de sjabloon te exporteren voor gebruik met extra implementaties.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan ik domeincontrollers toevoegen aan een Azure AD Domain Services beheerd domein?
Nee. Het domein dat door Azure AD Domain Services is een beheerd domein. U hoeft geen domeincontrollers voor dit domein in terichten, te configureren of anderszins te beheren. Deze beheeractiviteiten worden door Microsoft aangeboden als een service. Daarom kunt u geen extra domeincontrollers (lezen-schrijven of alleen-lezen) toevoegen voor het beheerde domein.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Kunnen gastgebruikers worden uitgenodigd voor mijn directory met Azure AD Domain Services?
Nee. Gastgebruikers die zijn uitgenodigd voor uw Azure AD-directory met behulp van [het Azure AD B2B-uitnodigingsproces,](../active-directory/external-identities/what-is-b2b.md) worden gesynchroniseerd met uw Azure AD Domain Services beheerde domein. Wachtwoorden voor deze gebruikers worden echter niet opgeslagen in uw Azure AD-directory. Daarom kan Azure AD Domain Services NTLM- en Kerberos-hashes voor deze gebruikers niet synchroniseren met uw beheerde domein. Dergelijke gebruikers kunnen zich niet aanmelden of computers toevoegen aan het beheerde domein.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Kan ik een bestaand Azure AD Domain Services naar een ander abonnement, resourcegroep, regio of virtueel netwerk?
Nee. Nadat u een Azure AD Domain Services beheerd domein hebt gemaakt, kunt u het beheerde domein niet verplaatsen naar een andere resourcegroep, virtueel netwerk, abonnement, enzovoort. Zorg dat u het meest geschikte abonnement, de resourcegroep, de regio en het virtuele netwerk selecteert wanneer u het beheerde domein implementeert.

### <a name="can-i-rename-an-existing-azure-ad-domain-services-domain-name"></a>Kan ik de naam van een bestaand Azure AD Domain Services domeinnaam wijzigen?
Nee. Nadat u een Azure AD Domain Services beheerd domein hebt gemaakt, kunt u de DNS-domeinnaam niet meer wijzigen. Kies de DNS-domeinnaam zorgvuldig wanneer u het beheerde domein maakt. Voor overwegingen bij het kiezen van de DNS-domeinnaam, zie de zelfstudie voor het maken en [configureren van een Azure AD Domain Services beheerd domein.](tutorial-create-instance.md#create-a-managed-domain)

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Bevat Azure AD Domain Services opties voor hoge beschikbaarheid?

Ja. Elk Azure AD Domain Services beheerd domein bevat twee domeincontrollers. U beheert of maakt geen verbinding met deze domeincontrollers, maar maakt deel uit van de beheerde service. Als u een Azure AD Domain Services implementeert in een regio die ondersteuning biedt Beschikbaarheidszones, worden de domeincontrollers verdeeld over zones. In regio's die geen ondersteuning bieden Beschikbaarheidszones, worden de domeincontrollers verdeeld over beschikbaarheidssets. U hebt geen configuratieopties of beheerbeheer over deze distributie. Zie Beschikbaarheidsopties voor [virtuele machines in Azure voor meer informatie.](../virtual-machines/availability.md)

## <a name="administration-and-operations"></a>Beheer en bewerkingen

* [Kan ik verbinding maken met de domeincontroller voor mijn beheerde domein met Extern bureaublad?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Ik heb de functie Azure AD Domain Services. Welk gebruikersaccount moet ik gebruiken om machines aan dit domein toe te sluiten?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Heb ik domeinbeheerdersbevoegdheden voor het beheerde domein dat door de Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan ik groepslidmaatschap wijzigen met behulp van LDAP of andere AD-beheerprogramma's in beheerde domeinen?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Hoe lang duurt het voordat wijzigingen in mijn Azure AD-directory zichtbaar zijn in mijn beheerde domein?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kan ik het schema van het beheerde domein uitbreiden dat door de Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan ik DNS-records wijzigen of toevoegen in mijn beheerde domein?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Wat is het beleid voor de levensduur van wachtwoorden in een beheerd domein?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Biedt Azure AD Domain Services ad-accountvergrendelingsbeveiliging?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Kan ik Distributed File System (DFS) en replicatie binnen de Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Hoe worden Windows-updates toegepast in Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan ik verbinding maken met de domeincontroller voor mijn beheerde domein met behulp Extern bureaublad?
Nee. U hebt geen machtigingen om verbinding te maken met domeincontrollers voor het beheerde domein met behulp van Extern bureaublad. Leden van de *groep AAD DC-beheerders* kunnen het beheerde domein beheren met AD-beheerprogramma's zoals het Active Directory Administration Center (ADAC) of AD PowerShell. Deze hulpprogramma's worden geïnstalleerd met *behulp Remote Server Administration Tools-functie* op een Windows-server die is toegevoegd aan het beheerde domein. Zie Een beheer-VM maken voor het configureren en beheren van een Azure AD Domain Services [beheerd domein voor meer informatie.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ik heb de Azure AD Domain Services. Welk gebruikersaccount gebruik ik om machines aan dit domein toe te sluiten?
Elk gebruikersaccount dat deel uitmaakt van het beheerde domein, kan lid worden van een VM. Leden van de *groep AAD DC-beheerders* krijgen extern bureaublad-toegang tot computers die zijn lid van het beheerde domein.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Heb ik bevoegdheden voor domeinbeheerders voor het beheerde domein dat door de Azure AD Domain Services?
Nee. U krijgt geen beheerdersbevoegdheden voor het beheerde domein. *De bevoegdheden domeinbeheerder* *en* ondernemingsbeheerder zijn niet beschikbaar voor gebruik binnen het domein. Leden van de domeinbeheerder of ondernemingsbeheerdersgroepen in uw on-premises Active Directory worden ook geen domein-/ondernemingsbeheerdersbevoegdheden verleend voor het beheerde domein.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan ik groepslidmaatschap wijzigen met behulp van LDAP of andere AD-beheerprogramma's in beheerde domeinen?
Gebruikers en groepen die zijn gesynchroniseerd van Azure Active Directory naar Azure AD Domain Services kunnen niet worden gewijzigd omdat hun bron van oorsprong Azure Active Directory. Dit omvat het verplaatsen van gebruikers of groepen van de door AADDC-gebruikers beheerde organisatie-eenheid naar een aangepaste organisatie-eenheid. Elke gebruiker of groep die afkomstig is uit het beheerde domein kan worden gewijzigd.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hoe lang duurt het voordat wijzigingen in mijn Azure AD-directory zichtbaar zijn in mijn beheerde domein?
Wijzigingen die zijn aangebracht in uw Azure AD-directory met behulp van de gebruikersinterface van Azure AD of PowerShell, worden automatisch gesynchroniseerd met uw beheerde domein. Dit synchronisatieproces wordt op de achtergrond uitgevoerd. Er is geen gedefinieerde tijdsperiode voor deze synchronisatie om alle objectwijzigingen te voltooien.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan ik het schema van het beheerde domein uitbreiden dat door de Azure AD Domain Services?
Nee. Het schema wordt beheerd door Microsoft voor het beheerde domein. Schema-extensies worden niet ondersteund door Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan ik DNS-records in mijn beheerde domein wijzigen of toevoegen?
Ja. Leden van de *groep AAD DC Administrators* krijgen dns-administratorbevoegdheden om *DNS-records* in het beheerde domein te wijzigen. Deze gebruikers kunnen de DNS-beheerconsole gebruiken op een computer met Windows Server die is verbonden met het beheerde domein om DNS te beheren. Als u de DNS Manager-console wilt gebruiken, installeert u *DNS Server Tools*, dat deel uitmaakt van de *Remote Server Administration Tools* functie op de server. Zie DNS beheren in een Azure AD Domain Services [beheerd domein voor meer informatie.](manage-dns.md)

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Wat is het beleid voor de levensduur van wachtwoorden in een beheerd domein?
De standaardwachtwoordlevensduur voor een Azure AD Domain Services beheerd domein is 90 dagen. Deze levensduur van het wachtwoord wordt niet gesynchroniseerd met de levensduur van het wachtwoord die is geconfigureerd in Azure AD. Daarom kan het voorkomen dat wachtwoorden van gebruikers verlopen in uw beheerde domein, maar nog steeds geldig zijn in Azure AD. In dergelijke scenario's moeten gebruikers hun wachtwoord wijzigen in Azure AD en wordt het nieuwe wachtwoord gesynchroniseerd met uw beheerde domein. Als u de standaardwachtwoordlevensduur in een beheerd domein wilt wijzigen, kunt u aangepast wachtwoordbeleid [maken en configureren.](password-policy.md).

Daarnaast wordt het Azure AD-wachtwoordbeleid *voor DisablePasswordExpiration* gesynchroniseerd met een beheerd domein. Wanneer *DisablePasswordExpiration* wordt toegepast op een gebruiker in Azure AD, is de *waarde UserAccountControl* voor de gesynchroniseerde gebruiker in het beheerde *domein DONT_EXPIRE_PASSWORD* toegepast.

Wanneer gebruikers hun wachtwoord opnieuw instellen in Azure AD, wordt het kenmerk *forceChangePasswordNextSignIn=True* toegepast. Een beheerd domein synchroniseert dit kenmerk vanuit Azure AD. Wanneer het beheerde domein *forceChangePasswordNextSignIn* detecteert, is ingesteld voor een gesynchroniseerde gebruiker van Azure AD, wordt het *kenmerk pwdLastSet* in het beheerde domein ingesteld op *0,* waardoor het momenteel ingesteld wachtwoord ongeldig wordt.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Biedt Azure AD Domain Services ad-accountvergrendelingsbeveiliging?
Ja. Vijf ongeldige wachtwoordpogingen binnen twee minuten in het beheerde domein zorgen ervoor dat een gebruikersaccount 30 minuten wordt vergrendeld. Na 30 minuten wordt het gebruikersaccount automatisch ontgrendeld. Bij ongeldige wachtwoordpogingen in het beheerde domein wordt het gebruikersaccount in Azure AD niet vergrendeld. Het gebruikersaccount is alleen vergrendeld binnen uw Azure AD Domain Services beheerde domein. Zie Wachtwoord- en [accountvergrendelingsbeleid voor beheerde domeinen voor meer informatie.](password-policy.md)

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Kan ik Distributed File System en replicatie configureren binnen Azure AD Domain Services?
Nee. Distributed File System (DFS) en replicatie zijn niet beschikbaar wanneer u Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Hoe worden Windows-updates toegepast in Azure AD Domain Services?
Domeincontrollers in een beheerd domein passen automatisch vereiste Windows-updates toe. U kunt hier niets configureren of beheren. Zorg ervoor dat u geen regels voor netwerkbeveiligingsgroep maakt die uitgaand verkeer naar Windows Updates blokkeren. Voor uw eigen VM's die aan het beheerde domein zijn verbonden, bent u verantwoordelijk voor het configureren en toepassen van vereiste updates voor het besturingssysteem en toepassingen.

## <a name="billing-and-availability"></a>Facturering en beschikbaarheid

* [Is Azure AD Domain Services een betaalde service?](#is-azure-ad-domain-services-a-paid-service)
* [Is er een gratis proefversie voor de service?](#is-there-a-free-trial-for-the-service)
* [Kan ik een beheerd Azure AD Domain Services onderbreken?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan ik een failover Azure AD Domain Services naar een andere regio voor een dr-gebeurtenis?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan ik Azure AD Domain Services als onderdeel van Enterprise Mobility Suite (EMS)? Heb ik Azure AD Premium nodig om deze te Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [In welke Azure-regio's is de service beschikbaar?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Is Azure AD Domain Services een betaalde service?
Ja. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/) voor meer informatie.

### <a name="is-there-a-free-trial-for-the-service"></a>Is er een gratis proefversie voor de service?
Azure AD Domain Services is opgenomen in de gratis proefversie voor Azure. U kunt zich aanmelden voor een [gratis proefversie van één maand van Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan ik een beheerd Azure AD Domain Services onderbreken?
Nee. Zodra u een beheerd domein Azure AD Domain Services ingeschakeld, is de service beschikbaar in het geselecteerde virtuele netwerk totdat u het beheerde domein verwijdert. Er is geen manier om de service te onderbreken. De facturering wordt elk uur voortgezet totdat u het beheerde domein verwijdert.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan ik een failover Azure AD Domain Services naar een andere regio voor een dr-gebeurtenis?
Ja, om geografische tolerantie te bieden voor een beheerd domein, kunt u een extra [replicaset](tutorial-create-replica-set.md) maken naar een virtueel peernetwerk in elke Azure-regio die ondersteuning biedt voor Azure AD DS. Replicasets delen dezelfde naamruimte en configuratie met het beheerde domein.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan ik Azure AD Domain Services als onderdeel van Enterprise Mobility Suite (EMS)? Heb ik een Azure AD Premium nodig om deze te Azure AD Domain Services?
Nee. Azure AD Domain Services is een Azure-service voor betalen per gebruik en maakt geen deel uit van EMS. Azure AD Domain Services kunnen worden gebruikt met alle edities van Azure AD (gratis en Premium). U wordt gefactureerd op uurbasis, afhankelijk van het gebruik.

### <a name="what-azure-regions-is-the-service-available-in"></a>In welke Azure-regio's is de service beschikbaar?
Raadpleeg de pagina [Azure-services per regio](https://azure.microsoft.com/regions/#services/) voor een overzicht van de Azure-regio's waar Azure AD Domain Services beschikbaar is.

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg de Gids [voor probleemoplossing voor](troubleshoot.md) oplossingen voor veelvoorkomende problemen met het configureren of beheren van Azure AD Domain Services.

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure AD Domain Services? voor [meer informatie over Azure Active Directory Domain Services.](overview.md)

Zie Create and configure an Azure Active Directory Domain Services managed domain (Een beheerd [Azure Active Directory Domain Services maken en configureren) om](tutorial-create-instance.md)aan de slag te gaan.
