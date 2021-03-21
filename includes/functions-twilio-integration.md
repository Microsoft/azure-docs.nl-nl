---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026662"
---
In dit voor beeld moet de [Twilio](https://www.twilio.com/) -service worden gebruikt om SMS-berichten te verzenden naar een mobiele telefoon. Azure Functions biedt al ondersteuning voor Twilio via de [Twilio-binding](../articles/azure-functions/functions-bindings-twilio.md). het voor beeld gebruikt deze functie.

Het eerste wat u nodig hebt, is een Twilio-account. U kunt er gratis op maken https://www.twilio.com/try-twilio . Nadat u een account hebt, voegt u de volgende drie **app-instellingen** toe aan de functie-app.

| Naam van app-instelling | Waarde beschrijving |
| - | - |
| **TwilioAccountSid**  | De SID voor uw Twilio-account |
| **TwilioAuthToken**   | Het verificatie token voor uw Twilio-account |
| **TwilioPhoneNumber** | Het telefoon nummer dat is gekoppeld aan uw Twilio-account. Dit wordt gebruikt om SMS-berichten te verzenden. |