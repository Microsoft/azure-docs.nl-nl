---
title: Claims met besturings elementen voor weer gave controleren
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van Azure AD B2C besturings elementen voor weer gave om de claims te controleren in de door uw aangepaste beleids regels verschafte gebruikers ritten.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd90be6d93dc5ca399ac87daba0ca44fa7e88ff8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94532491"
---
# <a name="verification-display-control"></a>Besturings element voor verificatie weergave

Gebruik een [besturings element](display-controls.md) voor controle weergave om een claim te verifiëren, bijvoorbeeld een e-mail adres of telefoon nummer, waarbij een verificatie code wordt verzonden naar de gebruiker.

## <a name="verificationcontrol-actions"></a>VerificationControl-acties

Het besturings element voor controle weergave bestaat uit twee stappen (acties):

1. Vraag een bestemming aan bij de gebruiker, zoals een e-mail adres of telefoon nummer waarnaar de verificatie code moet worden verzonden. Wanneer de gebruiker de knop **code verzenden** selecteert, wordt de **actie SendCode** van het besturings element controle weer gegeven. Met de **actie SendCode** wordt een code gegenereerd, wordt de inhoud gemaakt die moet worden verzonden en verzonden naar de gebruiker. De waarde van het adres kan vooraf worden ingevuld en fungeert als een tweede factor verificatie.

    ![Voorbeeld pagina voor de actie code verzenden](media/display-control-verification/display-control-verification-email-action-01.png)

1. Nadat de code is verzonden, leest de gebruiker het bericht, voert de verificatie code in het besturings element van het weergave besturings element in en selecteert **code controleren**. Als u **code controleren** selecteert, wordt de **actie VerifyCode** uitgevoerd om de code te controleren die aan het adres is gekoppeld. Als de gebruiker **nieuwe code verzenden** selecteert, wordt de eerste actie opnieuw uitgevoerd.

    ![Voorbeeld pagina voor het controleren van code actie](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>Vereiste elementen VerificationControl

De **VerificationControl** moet de volgende elementen bevatten:

- Het type `DisplayControl` is `VerificationControl` .
- `DisplayClaims`
  - **Verzenden naar** -een of meer claims waarmee wordt aangegeven waarnaar de verificatie code moet worden verzonden. Bijvoorbeeld *e-mail adres* of *land code* en *telefoon nummer*.
  - **Verificatie code** -de claim van de verificatie code die door de gebruiker wordt verstrekt nadat de code is verzonden. Deze claim moet worden ingesteld als vereist en `ControlClaimType` moet worden ingesteld op `VerificationCode` .
- Uitvoer claim (optioneel) die moet worden geretourneerd naar de zelfbevestigende pagina nadat het verificatie proces door de gebruiker is voltooid. Bijvoorbeeld *e-mail adres* of *land code* en *telefoon nummer*. Het zelfondertekende technische profiel gebruikt de claims om de gegevens op te slaan of de uitvoer claims samen te stellen aan de volgende Orchestration-stap.
- Twee `Action` s met de volgende namen:
  - **SendCode** : stuurt een code naar de gebruiker. Deze actie bevat doorgaans twee technische validatie profielen, waarmee een code kan worden gegenereerd en verzonden.
  - **VerifyCode** : verifieert de code. Deze actie bevat meestal een technische profiel voor validatie.

In het onderstaande voor beeld wordt een tekstvak voor **e-mail** op de pagina weer gegeven. Wanneer de gebruiker zijn of haar e-mail adres invoert en **SendCode** selecteert, wordt de actie **SendCode** geactiveerd in de back-end van Azure AD B2C.

Vervolgens voert de gebruiker de **verificationCode** in en selecteert **VerifyCode** om de **VerifyCode** -actie in de back-end te activeren. Als alle validaties worden door gegeven, wordt de **VerificationControl** beschouwd als voltooid en kan de gebruiker door gaan met de volgende stap.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
