---
title: Azure AD Connect Health instructies voor het ophalen van gegevens | Microsoft Docs
description: Op deze pagina wordt beschreven hoe u gegevens ophaalt uit Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463520"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Azure AD Connect Health instructies voor het ophalen van gegevens

In dit document wordt beschreven hoe u Azure AD Connect kunt gebruiken om gegevens op te halen uit Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Alle e-mail adressen ophalen voor gebruikers die zijn geconfigureerd voor status waarschuwingen.

Gebruik de volgende stappen om de e-mail adressen op te halen voor alle gebruikers die zijn geconfigureerd in Azure AD Connect Health om waarschuwingen te ontvangen.

1.  Begin op de Blade Azure Active Directory Connect status en selecteer **synchronisatie Services** in de linkernavigatiebalk.
 ![Synchronisatie Services](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Klik op de tegel **waarschuwingen** .</br>
 ![Waarschuwing](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Klik op **instellingen voor meldingen**.
 ![Melding](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  Op de Blade **meldings instelling** ziet u de lijst met e-mail adressen die zijn ingeschakeld als ontvangers voor status waarschuwings meldingen.
 ![E-mails](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Accounts ophalen die zijn gemarkeerd met AD FS mislukte wachtwoord pogingen

Gebruik de volgende stappen om accounts op te halen die zijn gemarkeerd met AD FS mislukte wachtwoord pogingen.

1.  Selecteer op de Blade Azure Active Directory status de optie **synchronisatie fouten**.
 ![Synchronisatie fouten](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  Klik op de Blade **synchronisatie fouten** op **exporteren**. Hiermee wordt een lijst met geregistreerde synchronisatie fouten geëxporteerd.
 ![Exporteren](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [De Azure AD Connect Health-agent installeren](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health Operations (Engelstalig)](how-to-connect-health-operations.md)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
* [Geschiedenis van Azure AD Connect Health-versie](reference-connect-health-version-history.md)