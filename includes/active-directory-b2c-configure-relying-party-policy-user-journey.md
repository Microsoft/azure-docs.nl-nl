---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674224"
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

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin`
1. Selecteer voor **toepassing** een webtoepassing die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

