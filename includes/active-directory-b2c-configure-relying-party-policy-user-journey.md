---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98951517"
---
## <a name="configure-the-relying-party-policy"></a>Het Relying Party-beleid configureren

Het Relying Party beleid, bijvoorbeeld [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), geeft de gebruikers traject op die Azure AD B2C gaat worden uitgevoerd. Zoek het element **DefaultUserJourney** in [Relying Party](../articles/active-directory-b2c/relyingparty.md). Werk de  **ReferenceId** bij zodat deze overeenkomt met de reis-id van de gebruiker, waarbij u de ID-provider hebt toegevoegd. 

In het volgende voor beeld is voor de `CustomSignUpOrSignIn` gebruikers traject de **ReferenceId** ingesteld op `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Het aangepaste beleid uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.
1. Selecteer **aangepast beleid uploaden** en upload de twee beleids bestanden die u hebt gewijzigd, in de volgende volg orde: het uitbrei ding beleid, bijvoorbeeld `TrustFrameworkExtensions.xml` het Relying Party beleid, zoals `SignUpSignIn.xml` .



