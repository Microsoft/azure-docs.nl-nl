---
title: Op Active Directory gebaseerde services in Azure vergelijken | Microsoft Docs
description: In dit overzicht kunt u de verschillende identiteitsaanbiedingen voor Active Directory Domain Services, Azure Active Directory en Azure Active Directory Domain Services vergelijken.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: justinha
ms.openlocfilehash: 48ce982a6d6d7a3ed12d6e3b4711d111dec4d6f8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174628"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Zelfbeheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken

Er zijn drie gebruikelijke manieren om op Active Directory gebaseerde services in Azure te gebruiken om toepassingen, services of apparaten toegang te geven tot een centrale identiteit. Deze keuze van identiteitsoplossingen biedt u de flexibiliteit om gebruik te maken van de meest geschikte map voor de behoeften van uw organisatie. Als u bijvoorbeeld vooral alleen-cloudgebruikers met mobiele apparaten beheert, is het wellicht niet zinvol om uw eigen AD DS-identiteitsoplossing (Active Directory Domain Services) te bouwen en uit te voeren. In plaats daarvan kunt u gewoon Azure Active Directory gebruiken.

Hoewel de drie op Active Directory gebaseerde identiteitsoplossingen een gemeenschappelijke naam en technologie delen, zijn ze ontworpen om services te bieden die voldoen aan verschillende klanteisen. Op hoog niveau zijn de identiteitsoplossingen en functiesets als volgt:

* **Active Directory Domain Services (AD DS)** - LDAP-server (Lightweight Directory Access Protocol) voor zakelijk gebruik die belangrijke functies biedt, zoals identiteit en verificatie, computerobjectbeheer, groepsbeleid en vertrouwensrelaties.
    * AD DS is een centraal onderdeel in veel organisaties met een on-premises IT-omgeving en biedt kernverificatie van gebruikersaccounts en computerbeheerfuncties.
    * Zie [Overzicht van Active Directory Domain Services in de documentatie van Windows Server][overview-adds] voor meer informatie.
* **Azure Active Directory (Azure AD)** - cloudidentiteit en Mobile Device Management waarmee gebruikersaccount- en verificatieservices worden geboden voor resources zoals Microsoft 365, Azure Portal of SaaS-toepassingen.
    * Azure AD kan worden gesynchroniseerd met een on-premises AD DS-omgeving om één identiteit te bieden aan gebruikers die standaard in de cloud werken.
    * Zie [Wat is Azure Active Directory?][whatis-azuread] voor meer informatie over Azure AD.
* **Azure Active Directory Domain Services (Azure AD DS)** - biedt beheerde domeinservices met een subset van volledig compatibele traditionele AD DS-functies zoals domeindeelname, groepsbeleid, LDAP en Kerberos-/NTLM-verificatie.
    * Azure AD DS kan worden geïntegreerd met Azure AD, en Azure AD kan worden gesynchroniseerd met een on-premises AD DS-omgeving. Met deze mogelijkheid worden de centrale identiteitgebruikscases uitgebreid naar traditionele webtoepassingen die worden uitgevoerd in Azure als onderdeel van een lift-and-shift-strategie.
    * Voor meer informatie over synchronisatie met Azure AD en on-premises raadpleegt u [Hoe objecten en referenties worden gesynchroniseerd in een beheerd domein][synchronization].

In dit overzichtsartikel wordt vergeleken hoe deze identiteitsoplossingen kunnen samenwerken of afzonderlijk kunnen worden gebruikt, afhankelijk van de behoeften van uw organisatie.

> [!div class="nextstepaction"]
> [Maak eerst een beheerd Azure AD DS-domein met behulp van Azure Portal][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure-AD DS en zelfbeheerde AD DS

Als u toepassingen en services hebt die toegang nodig hebben tot traditionele verificatiemechanismen zoals Kerberos of NTLM, zijn er twee manieren om Active Directory Domain Services in de cloud aan te bieden:

* Een *beheerd domein* dat u maakt met behulp van Azure Active Directory Domain Services (Azure AD DS). Microsoft maakt en beheert de vereiste resources.
* Een *zelfbeheerd* domein dat u maakt en configureert met behulp van traditionele resources zoals virtuele machines (VM's), een Windows Server-gastbesturingssysteem en Active Directory Domain Services (AD DS). U blijft deze resources vervolgens beheren.

Met Azure AD DS worden de kernserviceonderdelen door Microsoft geïmplementeerd en onderhouden als een *beheerde* domeinervaring. U hoeft de AD DS-infrastructuur voor onderdelen zoals de VM's, het Windows Server-besturingssysteem of de domeincontrollers (DC's) niet te implementeren, te beheren, te patchen en te beveiligen.

Azure AD DS biedt een kleinere subset met functies voor de traditioneel zelfbeheerde AD DS-omgeving, waardoor een deel van de complexiteit van het ontwerp en beheer wordt beperkt. Zo hoeven er geen AD-forests, domein, sites en replicatiekoppelingen te worden ontworpen en onderhouden. U kunt nog steeds [forestvertrouwensrelaties maken tussen Azure AD DS en on-premises omgevingen][create-forest-trust].

Voor toepassingen en services die worden uitgevoerd in de cloud en toegang moeten hebben tot traditionele verificatiemechanismen zoals Kerberos of NTLM, biedt Azure AD DS een beheerde domeinervaring met de minimale hoeveelheid beheeroverhead. Zie [Beheerconcepten voor gebruikersaccounts, wachtwoorden en beheer in Azure AD DS][administration-concepts] voor meer informatie.

Wanneer u een zelfbeheerde AD DS-omgeving implementeert en uitvoert, moet u alle bijbehorende infrastructuur en maponderdelen onderhouden. Er is extra onderhoudsoverhead bij een zelfbeheerde AD DS-omgeving, maar u kunt vervolgens extra taken uitvoeren, zoals het schema uitbreiden of forestvertrouwensrelaties maken.

Algemene implementatiemodellen voor een zelfbeheerde AD DS-omgeving die identiteit biedt aan toepassingen en services in de cloud, bevatten het volgende:

* **Zelfstandige alleen-cloud-AD DS** - virtuele Azure-machines worden geconfigureerd als domeincontrollers en er wordt een afzonderlijke, alleen-cloud-AD DS-omgeving gemaakt. Deze AD DS-omgeving kan niet worden geïntegreerd met een on-premises AD DS-omgeving. Er wordt een andere set referenties gebruikt voor het aanmelden en het beheren van virtuele machines in de cloud.
* **Implementatie van resourceforest** - virtuele Azure-machines worden geconfigureerd als domeincontrollers en er wordt een AD DS-domein gemaakt dat deel uitmaakt van een bestaande forest. Vervolgens wordt er een vertrouwensrelatie geconfigureerd voor een on-premises AD DS-omgeving. Andere virtuele Azure-machines kunnen deelnemen aan domeinen van deze resourceforest in de cloud. Gebruikersverificatie wordt uitgevoerd via een VPN-/ExpressRoute-verbinding met de on-premises AD DS-omgeving.
* **Een on-premises domein uitbreiden naar Azure** - een virtueel Azure-netwerk maakt verbinding met een on-premises netwerk met behulp van een VPN-/ExpressRoute-verbinding. Virtuele Azure-machines maken verbinding met dit virtuele Azure-netwerk, dat deze machines laat deelnemen aan domeinen van de on-premises AD DS-omgeving.
    * U kunt ook virtuele Azure-machines maken en het niveau ervan verhogen tot replicadomeincontrollers van het on-premises AD DS-domein. Deze domeincontrollers worden gerepliceerd via een VPN-/ExpressRoute-verbinding met de on-premises AD DS-omgeving. Het on-premises AD DS-domein wordt uitgebreid naar Azure.

De volgende tabel bevat een overzicht van enkele van de functies die u nodig hebt voor uw organisatie en van de verschillen tussen een beheerd Azure AD DS-domein en een zelfbeheerd AD DS-domein:

| **Functie** | **Azure AD DS** | **Zelfbeheerd AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Beheerde service**                               | **&#x2713;** | **&#x2715;** |
| **Beveiligde implementaties**                            | **&#x2713;** | De beheerder beveiligt de implementatie |
| **DNS-server**                                    | **&#x2713;** (beheerde service) |**&#x2713;** |
| **Beheerdersbevoegdheden voor domein of onderneming** | **&#x2715;** | **&#x2713;** |
| **Domeindeelname**                                   | **&#x2713;** | **&#x2713;** |
| **Domeinverificatie met behulp van NTLM en Kerberos** | **&#x2713;** | **&#x2713;** |
| **Beperkte Kerberos-delegering**               | Op basis van resource | Op basis van resource en account|
| **Aangepaste OE-structuur**                           | **&#x2713;** | **&#x2713;** |
| **Groepsbeleid**                                  | **&#x2713;** | **&#x2713;** |
| **Schema-uitbreidingen**                             | **&#x2715;** | **&#x2713;** |
| **Vertrouwensrelaties AD-domein/forest**                     | **&#x2713;** (uitgaande forestvertrouwensrelaties in één richting) | **&#x2713;** |
| **Secure LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP-leesbewerkingen**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP-schrijfbewerkingen**                                    | **&#x2713;** (binnen het beheerde domein) | **&#x2713;** |
| **Geografisch gedistribueerde implementaties**                   | **&#x2713;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS en Azure AD

Met Azure AD kunt u de identiteit beheren van apparaten die door de organisatie worden gebruikt en de toegang tot bedrijfsresources beheren vanaf deze apparaten. Gebruikers kunnen ook hun persoonlijke apparaat (een BYO-model (Bring-Your-Own)) registreren bij Azure AD en zo een identiteit ontvangen. Het apparaat wordt vervolgens door Azure AD geverifieerd wanneer een gebruiker zich aanmeldt bij Azure AD en het apparaat gebruikt om toegang te krijgen tot beveiligde resources. Het apparaat kan worden beheerd met MDM-software (Mobile Device Management), bijvoorbeeld Microsoft Intune. Met deze beheermogelijkheid kunt u de toegang tot gevoelige resources beperken tot beheerde en beleidsconforme apparaten.

Traditionele computers en laptops kunnen ook deelnemen aan Azure AD. Dit mechanisme biedt dezelfde voordelen als het registreren van een persoonlijk apparaat bij Azure AD, zoals gebruikers de mogelijkheid te bieden om zich met hun bedrijfsreferenties aan te melden bij het apparaat.

Apparaten die deelnemen aan Azure AD bieden de volgende voordelen:

* Eenmalige aanmelding (SSO) voor toepassingen die worden beveiligd door Azure AD.
* Roaming conform het bedrijfsbeleid van gebruikersinstellingen op alle apparaten.
* Toegang tot de Windows Store voor Bedrijven met behulp van bedrijfsreferenties.
* Windows Hello voor Bedrijven.
* Beperkte toegang tot apps en resources van apparaten die voldoen aan het bedrijfsbeleid.

Apparaten kunnen deelnemen aan Azure AD met of zonder een hybride implementatie die een on-premises AD DS-omgeving bevat. De volgende tabel biedt een overzicht van algemene apparaateigendomsmodellen en hoe deze gewoonlijk deelnemen aan een domein:

| **Type apparaat**                                        | **Apparaatplatformen**             | **Mechanisme**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Persoonlijke apparaten                                          | Windows 10, iOS, Android, macOS | Geregistreerd bij Azure AD    |
| Een apparaat in eigendom van een organisatie dat niet deelneemt aan on-premises AD DS | Windows 10                       | Azure AD-deelname        |
| Een apparaat in eigendom van een organisatie dat deelneemt aan on-premises AD DS  | Windows 10                       | Hybride Azure AD-deelname |

Op een apparaat dat deelneemt aan of is geregistreerd bij Azure AD vindt gebruikersverificatie plaats met moderne op OAuth/OpenID Connect gebaseerde protocollen. Deze protocollen werken via internet en zijn dus handig voor mobiele scenario's waarin gebruikers vanaf elke locatie toegang hebben tot bedrijfsresources.

Met apparaten die deelnemen aan Azure AD DS kunnen toepassingen gebruikmaken van de Kerberos- en NTLM-protocollen voor verificatie, zodat oudere toepassingen worden ondersteund die zijn gemigreerd om te worden uitgevoerd op virtuele Azure-machines als onderdeel van een lift-and-shift-strategie. In de volgende tabel worden de verschillen weergegeven in hoe de apparaten worden gerepresenteerd en deze zichzelf kunnen verifiëren bij de map:

| **Aspect**                      | **Azure AD-deelname**                                 | **Azure AD DS-deelname**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Apparaat beheerd door            | Azure AD                                            | Beheerd Azure AD-domein                                                |
| Representatie in de map | Apparaatobjecten in de Azure AD-map            | Computerobjecten in het beheerde Azure AD DS-domein                        |
| Verificatie                  | Op OAuth/OpenID Connect gebaseerde protocollen              | Kerberos- en NTLM-protocollen                                               |
| Beheer                      | MDM-software (Mobile Device Management), zoals Intune | Groepsbeleid                                                              |
| Netwerken                      | Werkt via internet                             | Moet zijn verbonden met of gekoppeld aan het virtuele netwerk waarop het beheerde domein is geïmplementeerd |
| Ideaal voor...                    | Mobiele of desktopapparaten voor eindgebruikers                  | Server-VM's die zijn geïmplementeerd in Azure                                              |


Als on-premises AD DS en Azure AD zijn geconfigureerd voor federatieve verificatie met ADFS, is er geen (huidige/geldige) wachtwoord-hash beschikbaar in Azure DS. Azure AD-gebruikersaccounts die zijn gemaakt voordat federatieve verificatie werd uitgevoerd, hebben mogelijk een oude wachtwoord-hash, die waarschijnlijk niet overeenkomt met een hash van het on-premises wachtwoord. Om die reden kan Azure AD DS de gebruikersreferenties niet valideren

## <a name="next-steps"></a>Volgende stappen

[Maak een beheerd Azure AD DS-domein met behulp van Azure Portal][tutorial-create] om aan de slag te gaan met Azure AD DS.

U kunt ook meer te weten komen over [beheerconcepten voor gebruikersaccounts, wachtwoorden en beheer in Azure AD DS][administration-concepts] en [hoe objecten en referenties worden gesynchroniseerd in een beheerd domein][synchronization].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
