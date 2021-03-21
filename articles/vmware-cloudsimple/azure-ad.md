---
title: 'Azure VMware-oplossing door CloudSimple: Azure AD als identiteits bron gebruiken in een Privécloud'
description: Hierin wordt beschreven hoe u Azure AD toevoegt als een id-provider in uw CloudSimple-Privécloud om gebruikers te verifiëren die toegang hebben tot CloudSimple vanuit Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898603"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Azure AD gebruiken als een id-provider voor vCenter op CloudSimple Private Cloud

U kunt uw CloudSimple Private Cloud vCenter instellen om te verifiëren met Azure Active Directory (Azure AD) voor uw VMware-beheerders om toegang te krijgen tot vCenter. Nadat de id-bron voor eenmalige aanmelding is ingesteld, kan de gebruiker van de **cloudowner** gebruikers van de identiteits bron toevoegen aan vCenter.  

U kunt uw Active Directory domein-en domein controllers op een van de volgende manieren instellen:

* Active Directory domein-en domein controllers die on-premises worden uitgevoerd
* Active Directory domein en domein controllers die op Azure worden uitgevoerd als virtuele machines in uw Azure-abonnement
* Nieuwe Active Directory domein-en domein controllers die worden uitgevoerd in uw CloudSimple-Privécloud
* Azure Active Directory-service

In deze hand leiding worden de taken beschreven die nodig zijn om Azure AD in te stellen als een identiteits bron.  Raadpleeg voor meer informatie over het gebruik van on-premises Active Directory of Active Directory die worden uitgevoerd in azure de [vCenter-identiteits bronnen instellen om Active Directory te gebruiken](set-vcenter-identity.md) voor gedetailleerde instructies voor het instellen van de identiteits bron.

## <a name="about-azure-ad"></a>Over Azure AD

Azure AD is de micro soft multi tenant-, Cloud-en Identity Management-service.  Azure AD biedt een schaalbaar, consistent en betrouwbaar verificatie mechanisme om gebruikers te verifiëren en toegang te krijgen tot verschillende services in Azure.  Het biedt ook beveiligde LDAP-services voor services van derden om Azure AD als verificatie/identiteits bron te gebruiken.  Azure AD combineert essentiële Directory Services, Geavanceerd identiteits bestuur en toegangs beheer voor toepassingen, die kunnen worden gebruikt om toegang te verlenen tot uw Privécloud voor gebruikers die de Privécloud beheren.

Als u Azure AD als een identiteits bron met vCenter wilt gebruiken, moet u Azure AD en Azure AD Domain Services instellen. Volg deze instructies:

1. [Azure AD en Azure AD Domain Services instellen](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Een identiteits bron instellen op de vCenter van uw Privécloud](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Azure AD en Azure AD Domain Services instellen

Voordat u aan de slag gaat, moet u toegang hebben tot uw Azure-abonnement met globale beheerders bevoegdheden.  De volgende stappen bevatten algemene richt lijnen. Details zijn opgenomen in de Azure-documentatie.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Als u Azure AD al hebt, kunt u deze sectie overs Laan.

1. Stel Azure AD in op uw abonnement, zoals beschreven in  [Azure AD-documentatie](../active-directory/fundamentals/active-directory-whatis.md).
2. Schakel Azure Active Directory Premium in voor uw abonnement, zoals wordt beschreven in [registreren voor Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Stel een aangepaste domein naam in en controleer de naam van het aangepaste domein, zoals wordt beschreven in [een aangepaste domein naam toevoegen aan Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Stel een DNS-record in voor uw domein registratie service met de informatie die is opgegeven in Azure.
    2. Stel de naam van het aangepaste domein in op het primaire domein.

U kunt desgewenst andere Azure AD-functies configureren.  Deze zijn niet vereist voor het inschakelen van vCenter-verificatie met Azure AD.

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services

> [!NOTE]
> Dit is een belang rijke stap voor het inschakelen van Azure AD als een identiteits bron voor vCenter.  Als u problemen wilt voor komen, moet u ervoor zorgen dat alle stappen correct worden uitgevoerd.

1. Schakel Azure AD Domain Services in, zoals beschreven in [enable Azure Active Directory Domain Services met behulp van de Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
2. Stel het netwerk in dat door Azure AD Domain Services wordt gebruikt, zoals wordt beschreven in [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
3. Configureer de groep Administrators voor het beheren van Azure AD Domain Services, zoals beschreven in [enable Azure Active Directory Domain Services met behulp van de Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
4. Werk de DNS-instellingen voor uw Azure AD Domain Services bij zoals beschreven in [enable Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).  Als u via Internet verbinding wilt maken met AD, stelt u de DNS-record voor het open bare IP-adres van de Azure AD Domain Services in op de domein naam.
5. Wachtwoord hash-synchronisatie inschakelen voor gebruikers.  Met deze stap wordt de synchronisatie van wacht woord-hashes ingeschakeld die vereist zijn voor NT LAN Manager (NTLM) en Kerberos-verificatie om te Azure AD Domain Services. Wanneer u de synchronisatie voor wachtwoordhashes hebt ingesteld, kunnen gebruikers zich bij het beheerde domein aanmelden met hun zakelijke referenties. Zie [wachtwoord-hash-synchronisatie inschakelen voor Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).
    1. Als alleen Cloud gebruikers aanwezig zijn, moeten ze hun wacht woord wijzigen via <a href="https://myapps.microsoft.com/" target="_blank">Azure AD-toegangs venster</a> om ervoor te zorgen dat wacht woord-hashes worden opgeslagen in de vereiste indeling voor NTLM of Kerberos.  Volg de instructies in [Schakel wacht woord-hash synchronisatie naar uw beheerde domein in voor Cloud gebruikers accounts](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Deze stap moet worden uitgevoerd voor afzonderlijke gebruikers en voor elke nieuwe gebruiker die in uw Azure AD-Directory is gemaakt met behulp van de Azure Portal of Azure AD Power shell-cmdlets. Gebruikers die toegang nodig hebben tot Azure AD Domain Services, moeten het <a href="https://myapps.microsoft.com/" target="_blank">Azure AD-toegangs venster</a> gebruiken en toegang krijgen tot hun profiel om het wacht woord te wijzigen.

        > [!NOTE]
        > Als uw organisatie gebruikersaccounts in de cloud heeft, moeten alle gebruikers die Azure Active Directory Domain Services willen gebruiken, hun wachtwoord wijzigen. Een cloudgebruikersaccount is een account dat is gemaakt in uw Azure AD-directory via de Azure portal of Azure AD PowerShell-cmdlets. Deze gebruikersaccounts zijn niet gesynchroniseerd vanuit een on-premises map.

    2. Als u wacht woorden van uw on-premises Active Directory synchroniseert, volgt u de stappen in de [Active Directory documentatie](../active-directory-domain-services/tutorial-configure-password-hash-sync.md).

6.  Configureer beveiligde LDAP op uw Azure Active Directory Domain Services, zoals beschreven in [secure LDAP (LDAPS) configureren voor een Azure AD Domain Services beheerd domein](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Upload een certificaat voor gebruik door secure LDAP zoals beschreven in het Azure-onderwerp [een certificaat voor beveiligde LDAP verkrijgen](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple raadt aan het gebruik van een ondertekend certificaat dat is uitgegeven door een certificerings instantie om ervoor te zorgen dat vCenter het certificaat kan vertrouwen.
    2. Schakel beveiligde LDAP in zoals beschreven [secure LDAP (LDAPS) inschakelen voor een Azure AD Domain Services beheerd domein](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Sla het open bare deel van het certificaat (zonder de persoonlijke sleutel) in de CER-indeling op voor gebruik met vCenter tijdens het configureren van de identiteits bron.
    4. Als Internet toegang tot de Azure AD Domain Services vereist is, schakelt u de optie beveiligde toegang tot LDAP via Internet toestaan in.
    5. Voeg de binnenkomende beveiligings regel voor de Azure AD Domain Services NSG voor TCP-poort 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Een identiteits bron instellen op de vCenter van uw Privécloud

1. [Escalatie bevoegdheden](escalate-private-cloud-privileges.md) voor uw privécloud.
2. Verzamel de configuratie parameters die zijn vereist voor het instellen van de identiteits bron.

    | **Optie** | **Beschrijving** |
    |------------|-----------------|
    | **Naam** | De naam van de identiteits bron. |
    | **Basis-DN voor gebruikers** | Basis-DN-naam voor gebruikers.  Gebruik voor Azure AD: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  voor beeld: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` .|
    | **Domeinnaam** | FQDN-namen van het domein, bijvoorbeeld example.com. Geef geen IP-adres op in dit tekstvak. |
    | **Domein alias** | *(optioneel)* De NetBIOS-naam van het domein. Voeg de NetBIOS-naam van het Active Directory domein als alias van de identiteits bron toe als u SSPI-verificaties gebruikt. |
    | **Basis-DN voor groepen** | De DN-basis naam voor groepen. Gebruik voor Azure AD: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  voor beeld: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL van primaire server** | LDAP-server van de primaire domein controller voor het domein.<br><br>Gebruik de indeling `ldaps://hostname:port`. De poort is doorgaans 636 voor LDAPS-verbindingen. <br><br>Een certificaat dat een vertrouwens relatie voor het LDAPS-eind punt van de Active Directory server tot stand brengt, is vereist wanneer u gebruikt `ldaps://` in de primaire of secundaire LDAP-URL. |
    | **URL van secundaire server** | Adres van de LDAP-server van de secundaire domein controller die wordt gebruikt voor failover. |
    | **Certificaat kiezen** | Als u LDAPS wilt gebruiken met uw Active Directory LDAP-server of OpenLDAP-server identiteits bron, wordt er een knop certificaat kiezen weer gegeven nadat u in het tekstvak URL hebt getypt `ldaps://` . Een secundaire URL is niet vereist. |
    | **Gebruikersnaam** | ID van een gebruiker in het domein met mini maal alleen-lezen toegang tot de basis-DN voor gebruikers en groepen. |
    | **Wachtwoord** | Het wacht woord van de gebruiker die is opgegeven door de gebruikers naam. |

3. Meld u aan bij uw Privécloud-vCenter nadat de bevoegdheden zijn geëscaleerd.
4. Volg de instructies in [een id-bron toevoegen in vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) met de waarden uit de vorige stap om Azure Active Directory als een identiteits bron in te stellen.
5. Gebruikers/groepen toevoegen vanuit Azure AD aan vCenter-groepen, zoals beschreven in het VMware-onderwerp [leden toevoegen aan een vCenter-groep voor één Sign-On](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Nieuwe gebruikers moeten alleen worden toegevoegd aan de *Cloud-eigenaar-groep*, *Cloud-Global-cluster-admin groep*, *Cloud-Global-Storage-admin-Group*, Cloud-Global: *Network-Administrator-* Group of Cloud-Global:- *beheer groep*.  Gebruikers die zijn toegevoegd aan de groep *Administrators* , worden automatisch verwijderd.  Alleen service accounts moeten worden toegevoegd aan de groep *Administrators* .

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over machtigings model voor de Privécloud](learn-private-cloud-permissions.md)
