---
title: E-mailmeldingen voor waarschuwingen van Azure Security Center configureren
description: Meer informatie over het afstemmen van de typen e-mailberichten voor beveiligingswaarschuwingen die worden verzonden door Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/09/2021
ms.author: memildin
ms.openlocfilehash: 342904a3ae996fe8c2eeddf2edfbc4283a3d03eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439439"
---
# <a name="configure-email-notifications-for-security-alerts"></a>E-mailmeldingen voor beveiligingswaarschuwingen configureren 

Beveiligingswaarschuwingen moeten de juiste personen in uw organisatie bereiken. Standaard worden e-mailberichten door Security Center verzonden naar de eigenaren als er een waarschuwing met een hoge urgentie is geactiveerd voor hun abonnement. Op deze pagina wordt uitgelegd hoe u deze meldingen kunt aanpassen.

Als u uw eigen voorkeuren voor e-mailmeldingen wilt definiëren, kunt u die kiezen op de instellingspagina **E-mailmeldingen** van Azure Security Center:

- ***Wie* moet worden gewaarschuwd**: e-mailberichten kunnen worden verzonden naar geselecteerde personen of naar iedereen met een specifieke Azure-rol voor een abonnement. 
- ***Waarover* moeten ze worden gewaarschuwd**: wijzig de ernstniveaus van meldingen die door Security Center moeten worden verzonden.

Security Center beperkt het aantal uitgaande e-mails om te voorkomen dat waarschuwingen niet meer serieus worden genomen. Security Center verzendt voor elk abonnement:

- Maximaal één e-mail per **6 uur** (4 e-mails per dag) voor meldingen met een **hoog ernstniveau**
- Maximaal één e-mail per **12 uur** (2 e-mails per dag) voor meldingen met een **gemiddeld ernstniveau**
- Maximaal één e-mail per **24 uur** voor waarschuwingen met een **laag ernstniveau**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="De details van de contactpersoon configureren die e-mails over beveiligingswaarschuwingen ontvangt." :::
 
## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|Gratis|
|Vereiste rollen en machtigingen:|**Beveiligingsbeheerder**<br>**Abonnementeigenaar** |
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>De e-mail meldingen over beveiligings waarschuwingen aanpassen via de portal<a name="email"></a>
U kunt e-mailmeldingen verzenden naar individuele personen of naar alle gebruikers met specifieke Azure-rollen.

1. Selecteer in het gebied **Prijzen en instellingen** van Security Center het relevante abonnement en selecteer **E-mailmailmeldingen**.

1. Definieer de ontvangers voor uw meldingen met een van de volgende opties, of beide:

    - Selecteer in de vervolgkeuzelijst een van de beschikbare rollen.
    - Voer specifieke e-mailadressen in, gescheiden door komma's. U kunt een onbeperkt aantal e-mailadressen invoeren.

1. Als u de contactgegevens voor beveiliging wilt toepassen op uw abonnement, selecteert u **Opslaan**.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>De e-mail meldingen voor waarschuwingen aanpassen via de API
U kunt ook uw e-mail meldingen beheren via de geleverde REST API. Zie de [SECURITYCONTACTS API-documentatie](/rest/api/securitycenter/securitycontacts)voor meer informatie.

Dit is een voor beeld van een aanvraag tekst voor de PUT-aanvraag bij het maken van een beveiligings contact configuratie:

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "High"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>Zie ook
Zie de volgende pagina's voor meer informatie over beveiligingswaarschuwingen:

- [Beveiligingswaarschuwingen: een referentiegids](alerts-reference.md): meer informatie over de beveiligingswaarschuwingen die u kunt zien in de module Threat Protection van Azure Security Center
- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): meer informatie over hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren
- [Werkstroomautomatisering](workflow-automation.md): automatiseren van reacties op waarschuwingen met aangepaste meldingslogica