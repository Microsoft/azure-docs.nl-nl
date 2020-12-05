---
title: Service-Principal-waarschuwingen in Azure AD Domain Services oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met de configuratie van service-principals voor Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 00ab5c85a477c9c4080acf252cbbde9d4ce816a9
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620236"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Bekende problemen: waarschuwingen voor service-principals in Azure Active Directory Domain Services

[Service-principals](../active-directory/develop/app-objects-and-service-principals.md) zijn toepassingen die door het Azure-platform worden gebruikt voor het beheren, bijwerken en onderhouden van een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein. Als een service-principal wordt verwijderd, heeft dit invloed op de functionaliteit van het beheerde domein.

Dit artikel helpt u bij het oplossen van problemen met Service-Principal-configuratie waarschuwingen.

## <a name="alert-aadds102-service-principal-not-found"></a>Waarschuwing AADDS102: Service-Principal is niet gevonden

### <a name="alert-message"></a>Waarschuwings bericht

*Een service-principal die vereist is voor een goede werking van Azure AD Domain Services, is verwijderd uit uw Azure AD-adres lijst. Deze configuratie is van invloed op de mogelijkheid van micro soft om uw beheerde domein te bewaken, te beheren, te patchen en te synchroniseren.*

Als een vereiste service-principal is verwijderd, kan het Azure-platform geen geautomatiseerde beheer taken uitvoeren. Het beheerde domein past mogelijk geen updates toe of back-ups maken.

### <a name="check-for-missing-service-principals"></a>Controleren op ontbrekende service-principals

Voer de volgende stappen uit om te controleren welke service-principal ontbreekt en opnieuw moet worden gemaakt:

1. Selecteer in de Azure Portal **Azure Active Directory** in het navigatie menu aan de linkerkant.
1. Selecteer **bedrijfs toepassingen**. Kies *alle toepassingen* in de vervolg keuzelijst **toepassings type** en selecteer vervolgens **Toep assen**.
1. Zoek naar elk van de volgende toepassings-Id's. Als er geen bestaande toepassing wordt gevonden, volgt u *de stappen voor het maken* van de service-principal of het opnieuw registreren van de naam ruimte.

    | Toepassings-id | Oplossing |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Een ontbrekende Service-Principal opnieuw maken](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registreer de micro soft. AAD-naam ruimte opnieuw](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Registreer de micro soft. AAD-naam ruimte opnieuw](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registreer de micro soft. AAD-naam ruimte opnieuw](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Een ontbrekende Service-Principal opnieuw maken

Als de *2565bd9d-DA50-47d4-8b85-4c97f669dc36* van de toepassings-id ontbreekt in uw Azure AD-adres lijst, kunt u Azure AD Power shell gebruiken om de volgende stappen uit te voeren. Zie [Azure AD Power shell](/powershell/azure/active-directory/install-adv2)voor meer informatie.

1. Als dat nodig is, installeert u de Azure AD Power shell-module en importeert u deze als volgt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Maak de Service-Principal nu opnieuw met behulp van de cmdlet [New-azureadserviceprincipal namelijk niet][New-AzureAdServicePrincipal] :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

De status van het beheerde domein wordt automatisch bijgewerkt binnen twee uur en de waarschuwing wordt verwijderd.

### <a name="re-register-the-microsoft-aad-namespace"></a>De micro soft AAD-naam ruimte opnieuw registreren

Als de toepassings-ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022* of *D87DCBC6-A371-462E-88E3-28AD15EC4E64* ontbreekt in uw Azure AD-adres lijst, voert u de volgende stappen uit om de *micro soft. Aad* -resource provider opnieuw te registreren:

1. Zoek in het Azure Portal naar en selecteer **abonnementen**.
1. Kies het abonnement dat is gekoppeld aan uw beheerde domein.
1. Kies **resource providers** in de linkernavigatiebalk.
1. Zoek naar *micro soft. Aad* en selecteer **opnieuw registreren**.

De status van het beheerde domein wordt automatisch bijgewerkt binnen twee uur en de waarschuwing wordt verwijderd.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Waarschuwing AADDS105: de toepassing voor wachtwoord synchronisatie is verouderd

### <a name="alert-message"></a>Waarschuwings bericht

*De service-principal met de toepassings-ID ' d87dcbc6-a371-462e-88e3-28ad15ec4e64 ' is verwijderd en opnieuw gemaakt. De recreatie verloopt achter inconsistente machtigingen voor Azure AD Domain Services resources die nodig zijn om uw beheerde domein te onderhouden. De synchronisatie van wacht woorden op uw beheerde domein kan worden beïnvloed.*

Azure AD DS synchroniseert automatisch gebruikers accounts en referenties van Azure AD. Als er een probleem is met de Azure AD-toepassing die wordt gebruikt voor dit proces, mislukt de synchronisatie van referenties tussen Azure AD DS en Azure AD.

### <a name="resolution"></a>Oplossing

Als u de Azure AD-toepassing die wordt gebruikt voor referentie synchronisatie opnieuw wilt maken, gebruikt u Azure AD Power shell om de volgende stappen uit te voeren. Zie voor meer informatie [Azure AD Power Shell installeren](/powershell/azure/active-directory/install-adv2).

1. Als dat nodig is, installeert u de Azure AD Power shell-module en importeert u deze als volgt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Verwijder nu de oude toepassing en het object met behulp van de volgende Power shell-cmdlets:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Wanneer u beide toepassingen verwijdert, maakt het Azure-platform deze automatisch opnieuw en probeert wachtwoord synchronisatie te hervatten. De status van het beheerde domein wordt automatisch bijgewerkt binnen twee uur en de waarschuwing wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
