---
title: Door groep beheerde service accounts voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het maken van een beheerd service account voor een groep (gMSA) voor gebruik met Azure Active Directory Domain Services beheerde domeinen
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f975d3e0e605b7c24b9fd31dc8fc78f0f37bb6b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619981"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Een door een groep beheerd service account (gMSA) maken in Azure Active Directory Domain Services

Toepassingen en services hebben vaak een identiteit nodig om zichzelf te verifiëren met andere resources. Een webservice moet bijvoorbeeld mogelijk worden geverifieerd met een database service. Als een toepassing of service meerdere instanties heeft, zoals een webserver Farm, kan het hand matig maken en configureren van de identiteiten voor deze resources tijdrovend zijn.

In plaats daarvan kunt u een beheerd service account (gMSA) van een groep maken in het beheerde domein van Azure Active Directory Domain Services (Azure AD DS). Het Windows-besturings systeem beheert automatisch de referenties voor een gMSA, waardoor het beheer van grote groepen resources wordt vereenvoudigd.

In dit artikel wordt beschreven hoe u met Azure PowerShell een gMSA maakt in een beheerd domein.

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

## <a name="managed-service-accounts-overview"></a>Overzicht van beheerde service accounts

Een zelfstandig beheerd service account (sMSA) is een domein account waarvan het wacht woord automatisch wordt beheerd. Deze aanpak vereenvoudigt het beheer van Service Principal Name (SPN) en maakt gedelegeerd beheer mogelijk voor andere beheerders. U hoeft geen referenties voor het account hand matig te maken en te draaien.

Een door een groep beheerd service account (gMSA) biedt dezelfde beheer vereenvoudiging, maar voor meerdere servers in het domein. Met een gMSA kunnen alle exemplaren van een service die wordt gehost op een server farm dezelfde service-principal gebruiken voor wederzijdse verificatie protocollen. Wanneer een gMSA wordt gebruikt als Service-Principal, beheert het Windows-besturings systeem opnieuw het wacht woord van het account in plaats van te vertrouwen op de beheerder.

Zie voor meer informatie [groep managed service accounts (gMSA) Overview (Engelstalig)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Service accounts in azure AD DS gebruiken

Als beheerde domeinen worden vergrendeld en beheerd door micro soft, zijn er enkele aandachtspunten bij het gebruik van service accounts:

* Maak service accounts in aangepaste organisatie-eenheden (OE) op het beheerde domein.
    * U kunt geen service account maken in de ingebouwde AADDC- *gebruikers* of *AADDC-computers* .
    * Maak in plaats daarvan [een aangepaste OE][create-custom-ou] in het beheerde domein en maak vervolgens service accounts in die aangepaste organisatie-eenheid.
* De basis sleutel voor Key Distribution Services (KDS) wordt vooraf gemaakt.
    * De basis sleutel KDS wordt gebruikt om wacht woorden voor Gmsa's te genereren en op te halen. In azure AD DS wordt de hoofdmap KDS voor u gemaakt.
    * U hebt geen rechten om een andere te maken of om de standaard KDS-basis sleutel te bekijken.

## <a name="create-a-gmsa"></a>Een gMSA maken

Maak eerst een aangepaste OE met behulp van de cmdlet [New-ADOrganizationalUnit][New-AdOrganizationalUnit] . Zie [aangepaste organisatie-eenheden in Azure AD DS][create-custom-ou]voor meer informatie over het maken en beheren van aangepaste organisatie-eenheden.

> [!TIP]
> [Gebruik uw beheer-VM][tutorial-create-management-vm]om deze stappen uit te voeren om een gMSA te maken. Deze beheer-VM moet al de vereiste AD Power shell-cmdlets en de verbinding met het beheerde domein hebben.

In het volgende voor beeld wordt een aangepaste OE gemaakt met de naam *myNewOU* in het beheerde domein met de naam *aaddscontoso.com*. Gebruik uw eigen OE en beheerde domein naam:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Maak nu een gMSA met de cmdlet [New-ADServiceAccount][New-ADServiceAccount] . De volgende voorbeeld parameters worden gedefinieerd:

* **-Naam** is ingesteld op *WebFarmSvc*
* **-Path** para meter geeft u de aangepaste OE op voor de gMSA die u in de vorige stap hebt gemaakt.
* DNS-vermeldingen en spn's (Service Principal Names) worden ingesteld voor *WebFarmSvc.aaddscontoso.com*
* Principals in *AADDSCONTOSO-server $* mogen het wacht woord ophalen en de identiteit gebruiken.

Geef uw eigen namen en domein namen op.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Toepassingen en services kunnen nu zo worden geconfigureerd dat ze de gMSA gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie aan de slag [met door groep beheerde service accounts][gmsa-start]voor meer informatie over gmsa's.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
