---
title: 'Azure VMware-oplossing op basis van CloudSimple: vCenter-identiteits bronnen in Privécloud instellen'
description: Hierin wordt beschreven hoe u uw Privécloud kunt instellen om te verifiëren met Active Directory voor VMware-beheerders om toegang te krijgen tot vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a76fecb942c5c6da926e37149245e82dcbc4661b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899147"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>VCenter-identiteits bronnen instellen voor het gebruik van Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Over VMware vCenter-identiteits bronnen

VMware vCenter ondersteunt verschillende identiteits bronnen voor verificatie van gebruikers die toegang hebben tot vCenter.  Uw CloudSimple Private Cloud vCenter kan worden ingesteld om te verifiëren met Active Directory voor uw VMware-beheerders om toegang te krijgen tot vCenter. Wanneer de installatie is voltooid, kan de gebruiker van **cloudowner** gebruikers van de identiteits bron toevoegen aan vCenter.  

U kunt uw Active Directory domein-en domein controllers op een van de volgende manieren instellen:

* Active Directory domein-en domein controllers die on-premises worden uitgevoerd
* Active Directory domein en domein controllers die op Azure worden uitgevoerd als virtuele machines in uw Azure-abonnement
* Nieuwe Active Directory domein-en domein controllers die worden uitgevoerd in uw Privécloud
* Azure Active Directory-service

In deze hand leiding worden de taken beschreven voor het instellen van Active Directory domein en domein controllers die on-premises of als virtuele machines in uw abonnementen worden uitgevoerd.  Als u Azure AD wilt gebruiken als de identiteits bron, raadpleegt u [Azure AD gebruiken als een id-provider voor vCenter op CloudSimple Private Cloud](azure-ad.md) voor gedetailleerde instructies voor het instellen van de identiteits bron.

Voordat u [een identiteits bron toevoegt](#add-an-identity-source-on-vcenter), moet [u uw vCenter-bevoegdheden tijdelijk escaleren](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Nieuwe gebruikers moeten alleen worden toegevoegd aan de *Cloud-eigenaar-groep*, *Cloud-Global-cluster-admin groep*, *Cloud-Global-Storage-admin-Group*, Cloud-Global: *Network-Administrator-* Group of Cloud-Global:- *beheer groep*.  Gebruikers die zijn toegevoegd aan de groep *Administrators* , worden automatisch verwijderd.  Alleen service accounts moeten worden toegevoegd aan de groep *Administrators* en service accounts moeten worden gebruikt om u aan te melden bij de vSphere-webgebruikersinterface.   


## <a name="identity-source-options"></a>Opties voor identiteits bron

* [On-premises Active Directory toevoegen als identiteits bron met eenmalige aanmelding](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Nieuwe Active Directory in een Privécloud instellen](#set-up-new-active-directory-on-a-private-cloud)
* [Active Directory instellen op Azure](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **Active Directory (geïntegreerde Windows-verificatie) wordt niet ondersteund.** Alleen Active Directory over LDAP-optie wordt ondersteund als een identiteits bron.

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>On-premises Active Directory toevoegen als één Sign-On identiteits bron

Als u uw on-premises Active Directory wilt instellen als één Sign-On identiteits bron, hebt u het volgende nodig:

* [Site-naar-site-VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) van uw on-premises Data Center naar uw privécloud.
* IP-adres van on-premises DNS-server is toegevoegd aan vCenter-en platform Services controller (PSC).

Gebruik de informatie in de volgende tabel bij het instellen van uw Active Directory domein.

| **Optie** | **Beschrijving** |
|------------|-----------------|
| **Naam** | De naam van de identiteits bron. |
| **Basis-DN voor gebruikers** | Basis-DN-naam voor gebruikers. |
| **Domeinnaam** | FQDN-namen van het domein, bijvoorbeeld example.com. Geef geen IP-adres op in dit tekstvak. |
| **Domein alias** | De NetBIOS-naam van het domein. Voeg de NetBIOS-naam van het Active Directory domein als alias van de identiteits bron toe als u SSPI-verificaties gebruikt. |
| **Basis-DN voor groepen** | De DN-basis naam voor groepen. |
| **URL van primaire server** | LDAP-server van de primaire domein controller voor het domein.<br><br>Gebruik de notatie `ldap://hostname:port` of `ldaps://hostname:port` . De poort is doorgaans 389 voor LDAP-verbindingen en 636 voor LDAPS-verbindingen. Voor Active Directory implementaties van meerdere domein controllers is de poort doorgaans 3268 voor LDAP en 3269 voor LDAPS.<br><br>Een certificaat dat een vertrouwens relatie voor het LDAPS-eind punt van de Active Directory server tot stand brengt, is vereist wanneer u gebruikt `ldaps://` in de primaire of secundaire LDAP-URL. |
| **URL van secundaire server** | Adres van de LDAP-server van de secundaire domein controller die wordt gebruikt voor failover. |
| **Certificaat kiezen** | Als u LDAPS wilt gebruiken met uw Active Directory LDAP-server of OpenLDAP-server identiteits bron, wordt er een knop certificaat kiezen weer gegeven nadat u in het tekstvak URL hebt getypt `ldaps://` . Een secundaire URL is niet vereist. |
| **Gebruikersnaam** | ID van een gebruiker in het domein met mini maal alleen-lezen toegang tot de basis-DN voor gebruikers en groepen. |
| **Wachtwoord** | Het wacht woord van de gebruiker die is opgegeven door de gebruikers naam. |

Wanneer u de gegevens in de vorige tabel hebt, kunt u uw on-premises Active Directory toevoegen als één Sign-On identiteits bron op vCenter.

> [!TIP]
> Op de [pagina VMware-documentatie](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)vindt u meer informatie over de identiteits bronnen van één Sign-On.

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Nieuwe Active Directory in een Privécloud instellen

U kunt een nieuw Active Directory domein instellen in uw Privécloud en dit als een identiteits bron gebruiken voor eenmalige aanmelding.  Het Active Directory domein kan deel uitmaken van een bestaand Active Directory forest of kunnen worden ingesteld als een onafhankelijk forest.

### <a name="new-active-directory-forest-and-domain"></a>Nieuw Active Directory forest en domein

Als u een nieuw Active Directory-forest en-domein wilt instellen, hebt u het volgende nodig:

* Een of meer virtuele machines met micro soft Windows Server om te gebruiken als domein controllers voor het nieuwe Active Directory-forest en-domein.
* Een of meer virtuele machines waarop de DNS-service wordt uitgevoerd voor naam omzetting.

Zie [een nieuwe Windows Server 2012 Active Directory-forest installeren](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) voor gedetailleerde stappen.

> [!TIP]
> Voor een hoge Beschik baarheid van services raden we u aan om meerdere domein controllers en DNS-servers in te stellen.

Nadat u het Active Directory-forest en-domein hebt ingesteld, kunt u [een id-bron toevoegen aan vCenter](#add-an-identity-source-on-vcenter) voor uw nieuwe Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nieuw Active Directory domein in een bestaand Active Directory-forest

Als u een nieuw Active Directory domein wilt instellen in een bestaand Active Directory forest, hebt u het volgende nodig:

* Site-naar-site-VPN-verbinding met de locatie van uw Active Directory-forest.
* DNS-server om de naam van uw bestaande Active Directory-forest op te lossen.

Zie [een nieuwe Windows Server 2012-Active Directory onderliggende of structuur domein installeren](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) voor gedetailleerde stappen.

Nadat u het Active Directory domein hebt ingesteld, kunt u [een id-bron toevoegen aan vCenter](#add-an-identity-source-on-vcenter) voor uw nieuwe Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Active Directory instellen op Azure

Active Directory op Azure wordt uitgevoerd, is vergelijkbaar met Active Directory die on-premises worden uitgevoerd.  Voor het instellen van Active Directory die op Azure wordt uitgevoerd als een single Sign-On id-bron in vCenter, moet de vCenter-Server en de PSC netwerk verbinding hebben met de Azure-Virtual Network waar Active Directory Services worden uitgevoerd.  U kunt deze verbinding tot stand brengen met behulp van [azure Virtual Network verbinding met ExpressRoute](azure-expressroute-connection.md) van het virtuele Azure-netwerk waar Active Directory Services worden uitgevoerd op CloudSimple-privécloud.

Nadat de netwerk verbinding tot stand is gebracht, volgt u de stappen in [on-premises Active Directory toevoegen als één Sign-On identiteits bron](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) om deze als een identiteits bron toe te voegen.  

## <a name="add-an-identity-source-on-vcenter"></a>Een identiteits bron toevoegen aan vCenter

1. [Escalatie bevoegdheden](escalate-private-cloud-privileges.md) voor uw privécloud.

2. Meld u aan bij de vCenter voor uw Privécloud.

3. Selecteer **Home >-beheer**.

    ![Beheer](media/OnPremAD01.png)

4. Selecteer de **configuratie voor eenmalige aanmelding >**.

    ![Eenmalige aanmelding](media/OnPremAD02.png)

5. Open het tabblad **identiteits bronnen** en klik **+** om een nieuwe identiteits bron toe te voegen.

    ![Identiteits bronnen](media/OnPremAD03.png)

6. Selecteer **Active Directory als een LDAP-server** en klik op **volgende**.

    ![Scherm afbeelding die de Active Directory markeert als een LDAP-server optie.](media/OnPremAD04.png)

7. Geef de identiteits bron parameters voor uw omgeving op en klik op **volgende**.

    ![Active Directory](media/OnPremAD05.png)

8. Controleer de instellingen en klik op **volt ooien**.
