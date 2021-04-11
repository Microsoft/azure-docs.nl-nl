---
title: Azure AD Domain Services beveiligen | Microsoft Docs
description: Meer informatie over het uitschakelen van zwakke code ringen, oude protocollen en NTLM-wachtwoord synchronisatie voor een Azure Active Directory Domain Services beheerd domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: 5fa19e23767af0e121d07872970199a2a1705ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951930"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Zwakke versleuteling en wachtwoord-hash-synchronisatie uitschakelen om een Azure Active Directory Domain Services beheerd domein te beveiligen

Azure Active Directory Domain Services (Azure AD DS) maakt standaard gebruik van code ringen, zoals NTLM v1 en TLS v1. Deze code ringen zijn mogelijk vereist voor sommige oudere toepassingen, maar worden beschouwd als zwak en kunnen worden uitgeschakeld als u deze niet nodig hebt. Als u een on-premises hybride verbinding hebt met Azure AD Connect, kunt u de synchronisatie van NTLM-wachtwoord hashes ook uitschakelen.

In dit artikel leest u hoe u NTLM v1-en TLS v1-code ringen kunt uitschakelen en NTLM-wachtwoord synchronisatie kunt uitschakelen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te voltooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * [Maak en configureer zo nodig een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].

## <a name="use-security-settings-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Beveiligings instellingen gebruiken om zwakke versleuteling en NTLM-wachtwoord-hash-synchronisatie uit te scha kelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer **Azure AD Domain Services**.
1. Kies uw beheerde domein, bijvoorbeeld *aaddscontoso.com*.
1. Klik aan de linkerkant op **beveiligings instellingen**.
1. Klik op **uitschakelen** voor de volgende instellingen:
   - **Alleen TLS 1,2-modus**
   - **NTLM-verificatie**
   - **NTLM-wachtwoord synchronisatie van on-premises**

   ![Scherm opname van beveiligings instellingen voor het uitschakelen van zwakke cijfers en NTLM-wachtwoord-hash-synchronisatie](media/secure-your-domain/security-settings.png)

## <a name="use-powershell-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Power shell gebruiken voor het uitschakelen van zwakke cijfers en NTLM-wachtwoord-hash-synchronisatie

Als dat nodig is, [installeert en configureert u Azure PowerShell](/powershell/azure/install-az-ps). Zorg ervoor dat u zich bij uw Azure-abonnement aanmeldt met behulp van de [Connect-AzAccount][Connect-AzAccount]-cmdlet. 

[Installeer en configureer ook Azure AD Power shell](/powershell/azure/active-directory/install-adv2)als dat nodig is. Zorg ervoor dat u zich bij de Azure-tenant aanmeldt met behulp van de [Connect-AzureAD][Connect-AzureAD]-cmdlet.

Als u zwakke coderings suites en hash-synchronisatie van NTLM-referenties wilt uitschakelen, meldt u zich aan bij uw Azure-account en haalt u de Azure AD DS-resource op met behulp van de cmdlet [Get-AzResource][Get-AzResource] :

> [!TIP]
> Als er een fout bericht wordt weer gegeven met de opdracht [Get-AzResource][Get-AzResource] dat de *micro soft. Aad/DomainServices-* resource niet bestaat, [moet u uw toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definieer vervolgens *DomainSecuritySettings* voor het configureren van de volgende beveiligings opties:

1. Schakel NTLM v1-ondersteuning uit.
2. Schakel de synchronisatie van NTLM-wachtwoord-hashes uit vanuit uw on-premises AD.
3. Schakel TLS v1 uit.

> [!IMPORTANT]
> Gebruikers en service accounts kunnen geen LDAP-eenvoudige bindingen uitvoeren als u NTLM-wachtwoord synchronisatie uitschakelen in het beheerde domein van Azure AD DS. Als u eenvoudige LDAP-bindingen wilt uitvoeren, stelt u de optie *"SyncNtlmPasswords" = "disabled";* in de volgende opdracht niet in.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Pas tot slot de gedefinieerde beveiligings instellingen toe op het beheerde domein met behulp van de cmdlet [set-AzResource][Set-AzResource] . Geef de Azure AD DS-resource op uit de eerste stap en de beveiligings instellingen van de vorige stap.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Het duurt enkele ogen blikken voordat de beveiligings instellingen worden toegepast op het beheerde domein.

> [!IMPORTANT]
> Nadat u NTLM hebt uitgeschakeld, voert u een volledige wachtwoord hash-synchronisatie uit in Azure AD Connect om alle wacht woord-hashes te verwijderen uit het beheerde domein. Als u NTLM uitschakelt maar geen wachtwoord hash-synchronisatie afdwingt, worden NTLM-wachtwoord hashes voor een gebruikers account alleen verwijderd bij de volgende wachtwoord wijziging. Dit gedrag zou ertoe kunnen leiden dat een gebruiker zich aanmeldt als de referenties in de cache zijn opgeslagen op een systeem waarop NTLM als verificatie methode wordt gebruikt.
>
> Zodra de NTLM-wachtwoord-hash verschilt van de Kerberos-wachtwoord-hash, werkt terugval naar NTLM niet. Referenties in de cache werken ook niet meer als de virtuele machine verbinding heeft met de beheerde domein controller.  

## <a name="next-steps"></a>Volgende stappen

Zie [hoe objecten en referenties worden gesynchroniseerd in een beheerd domein][synchronization]voor meer informatie over het synchronisatie proces.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
