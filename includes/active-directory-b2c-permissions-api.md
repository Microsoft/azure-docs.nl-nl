---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: dd301cb3b18df5d3eb57ac38e9fb6432411d084b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073651"
---
#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/) 

1. Selecteer **App-registraties** en vervolgens de webclienttoepassing die toegang moet hebben tot de API. Bijvoorbeeld *webapp1*.
1. Selecteer onder **Beheren** de optie **API-machtigingen**.
1. Selecteer onder **Geconfigureerde machtigingen** de optie **Een machtiging toevoegen**.
1. Selecteer het tabblad **Mijn API's**.
1. Selecteer de API waarvoor aan de webclienttoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Vouw onder **Machtiging** de optie **demo** uit, en selecteer vervolgens de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld *demo.read* en *demo.write*.
1. Selecteer **Machtigingen toevoegen**.
1. Selecteer **Beheerderstoestemming verlenen voor (naam van uw tenant)** .
1. Als u wordt gevraagd een account te selecteren, selecteert u het momenteel aangemelde beheerdersaccount, of meldt u zich aan met een account in de Azure AD B2C-tenant waaraan minstens de rol *Cloudtoepassingsbeheerder* is toegewezen.
1. Selecteer **Ja**.
1. Selecteer **Vernieuwen** en controleer vervolgens of voor beide bereiken 'Verleend voor...' wordt weergegeven onder **Status**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Selecteer **Toepassingen (verouderd)** en vervolgens de webclienttoepassing die toegang moet hebben tot de API. Bijvoorbeeld *webapp1*.
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. In de vervolgkeuzelijst **API selecteren** selecteert u de API waarvoor aan de webclienttoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Selecteer in de vervolgkeuzelijst **Bereiken selecteren** de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld *demo.read* en *demo.write*.
1. Selecteer **OK**.
