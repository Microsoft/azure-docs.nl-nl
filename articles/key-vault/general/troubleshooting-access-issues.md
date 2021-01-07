---
title: Problemen met het toegangsbeleid voor Azure Key Vault oplossen
description: Problemen met het toegangsbeleid voor Azure Key Vault oplossen
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 8e4e64cd872794354479c29d4aaa000cee45c5a3
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901212"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Problemen met het toegangsbeleid voor Azure Key Vault oplossen

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Ik kan geheimen/sleutels/certificaat niet vermelden of ophalen. Ik krijg te zien: er is iets misgegaan... Fout.
Als u een probleem hebt met het weergeven/ophalen/maken of openen van een geheim, moet u ervoor zorgen dat er toegangsbeleid is gedefinieerd voor deze bewerking: [Toegangsbeleid van Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Hoe kan ik vaststellen hoe en wanneer sleutelkluizen worden geopend?

Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u wellicht controleren hoe en wanneer uw sleutelkluizen toegankelijk zijn en voor wie. U kunt dit controleren door logboekregistratie in te schakelen voor Azure Key Vault. [Lees meer](./logging.md) voor stapsgewijze instructies om logboekregistratie in te schakelen.

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Hoe kan ik de beschikbaarheid van de kluis, servicelatentieperioden of andere metrische gegevens voor de sleutelkluis bewaken?

Wanneer u begint met het schalen van uw service, neemt het aantal aanvragen toe dat naar uw sleutelkluis wordt verzonden. Dergelijke vraag kan de latentie van uw aanvragen verhogen en er in uitzonderlijke gevallen voor zorgen dat uw aanvragen worden beperkt, waardoor de prestaties van uw service worden beïnvloed. U kunt de metrische gegevens van de prestaties van de sleutelkluis controleren en waarschuwingen ontvangen voor specifieke drempelwaarden. [Lees meer](./alert.md) voor een stapsgewijze handleiding voor het configureren van bewaking.

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Ik kan het toegangsbeleid niet wijzigen, hoe kan dit worden ingeschakeld?
De gebruiker moet voldoende AAD-machtigingen hebben om het toegangsbeleid te wijzigen. In dit geval moet de gebruiker een hogere Inzender-rol hebben.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>Ik zie de fout 'Onbekend beleid'. Wat betekent dat?
Er zijn twee mogelijkheden waardoor u toegangsbeleid in de sectie Onbekend ziet:
* Er is mogelijk een eerdere gebruiker geweest die toegang had en om een of andere reden niet meer bestaat.
* Als het toegangsbeleid is toegevoegd via PowerShell en het toegangsbeleid is toegevoegd voor de object-id van de toepassing in plaats van voor de service-principal.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Hoe kan ik toegangsbeheer per sleutelkluisobject toewijzen? 

De beschikbaarheid van de functie voor toegangsbeheer per geheim/sleutel/certificaat wordt hier vermeld. [Meer informatie](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Hoe kan ik Key Vault-verificatie bieden met een toegangsbeheerbeleid?

De eenvoudigste manier om een cloudtoepassing bij Key Vault te verifiëren, is met een beheerde identiteit. Zie [Verificatie bij Azure Key Vault](authentication.md) voor meer informatie.
Als u een on-premises toepassing maakt, lokale ontwikkeling uitvoert of op een andere manier geen beheerde identiteit kunt gebruiken, kunt u in plaats daarvan handmatig een service-principal registreren en toegang verlenen tot uw sleutelkluis met behulp van een toegangsbeheerbeleid. Zie [Een toegangscontrolebeleid toewijzen](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Hoe kan ik de AD-groep toegang geven tot de sleutelkluis?

Geef de AD-groep toegang tot uw sleutelkluis met behulp van de Azure CLI-opdracht `az keyvault set-policy` of de Azure PowerShell-cmdlet Set-AzKeyVaultAccessPolicy. Zie [Een toegangsbeleid toewijzen - CLI](assign-access-policy-cli.md) en [Een toegangsbeleid toewijzen - PowerShell](assign-access-policy-powershell.md).

De toepassing moet ook ten minste één IAM-rol (Identiteits- en toegangsbeheer) hebben die is toegewezen aan de sleutelkluis. Anders kan de toepassing zich niet aanmelden en mislukt deze door onvoldoende toegangsrechten tot het abonnement. De tokens van Azure AD-groepen met beheerde identiteiten hebben mogelijk acht uur nodig om te vernieuwen en effectief te worden.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Hoe kan ik Key Vault opnieuw implementeren met een ARM-sjabloon zonder bestaand toegangsbeleid te verwijderen?

Op dit moment wordt elk toegangsbeleid in Key Vault verwijderd en vervangen door het toegangsbeleid in de ARM-sjabloon als u Key Vault opnieuw implementeert. Er is geen incrementele optie voor Key Vault-toegangsbeleid. Als u het toegangsbeleid in Key Vault wilt behouden, moet u het bestaande toegangsbeleid lezen in Key Vault en de ARM-sjabloon vullen met dit beleid om eventuele toegangsstoringen te voorkomen.

Een andere optie die u kan helpen bij dit scenario, is het gebruik van Azure RBAC en rollen als alternatief voor toegangsbeleid. Met Azure RBAC kunt u de sleutelkluis opnieuw implementeren zonder het beleid opnieuw op te geven. [Hier](./rbac-guide.md) kunt u meer lezen over deze oplossing.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Aanbevolen stappen voor probleemoplossing voor de volgende fouttypen

* HTTP 401: Niet-geverifieerde aanvraag - [Probleemoplossingsstappen](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Onvoldoende machtigingen - [Probleemoplossingsstappen](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Te veel aanvragen - [Probleemoplossingsstappen](rest-error-codes.md#http-429-too-many-requests)
* Controleer of u toegangsmachtiging voor verwijderen hebt voor de sleutelkluis: Zie [Een toegangsbeleid toewijzen - CLI](assign-access-policy-cli.md), [Een toegangsbeleid toewijzen - PowerShell](assign-access-policy-powershell.md) of [Een toegangsbeleid toewijzen - Portal](assign-access-policy-portal.md).
* Als u een probleem hebt met de verificatie voor de sleutelkluis in code, gebruikt u [Authentication SDK](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html) (Verificatie-SDK)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Wat zijn de aanbevolen procedures die ik moet implementeren als de sleutelkluis wordt beperkt?
Volg de aanbevolen procedures die [hier](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits) worden beschreven

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het oplossen van problemen met sleutelkluisverificatie: [Handleiding voor het oplossen van problemen met Key Vault](rest-error-codes.md).
