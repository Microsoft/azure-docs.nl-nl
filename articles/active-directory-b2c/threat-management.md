---
title: Bedreigingen voor bronnen en gegevens beheren
titleSuffix: Azure AD B2C
description: Meer informatie over technieken voor detectie en risico beperking voor denial-of-service-aanvallen en wachtwoord aanvallen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85384971"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Bedreigingen voor resources en gegevens in Azure Active Directory B2C beheren

Azure Active Directory B2C (Azure AD B2C) heeft ingebouwde functies waarmee u uw resources en gegevens kunt beveiligen tegen bedreigingen. Deze bedreigingen omvatten denial-of-service-aanvallen en wacht woorden. Denial-of-service-aanvallen maken het mogelijk dat resources niet beschikbaar zijn voor bedoelde gebruikers. Wachtwoord aanvallen leiden tot onbevoegde toegang tot bronnen.

## <a name="denial-of-service-attacks"></a>Denial-of-service-aanvallen

Azure AD B2C beschermt tegen SYN-flood aanvallen met behulp van een SYN-cookie. Azure AD B2C beschermt ook tegen denial-of-service-aanvallen door limieten voor tarieven en verbindingen te gebruiken.

## <a name="password-attacks"></a>Wachtwoord aanvallen

Wacht woorden die door gebruikers zijn ingesteld, moeten redelijkerwijs ingewikkeld zijn. Azure AD B2C heeft beperkende technieken voor wachtwoord aanvallen. Risico beperking omvat het detecteren van aanvals aanvallen en wacht woorden voor Woordenboek aanvallen. Door verschillende signalen te gebruiken, Azure AD B2C analyseert de integriteit van aanvragen. Azure AD B2C is ontworpen om beoogde gebruikers op intelligente wijze te onderscheiden van hackers en botnets.

Azure AD B2C gebruikt een geavanceerde strategie voor het vergren delen van accounts. De accounts zijn vergrendeld op basis van het IP-adres van de aanvraag en de ingevoerde wacht woorden. De duur van de vergren deling neemt ook toe op basis van de kans dat het een aanval is. Nadat een wacht woord tien keer is geprobeerd (de drempel waarde voor de standaard poging), vindt een vergren deling van een minuut plaats. De volgende keer dat een aanmelding wordt mislukt nadat het account is ontgrendeld (dat wil zeggen, nadat het account automatisch is ontgrendeld door de service nadat de vergrendelings periode is verlopen), wordt een vergren deling van één minuut uitgevoerd en wordt voor elke mislukte aanmelding voortgezet. Als u hetzelfde wacht woord herhaaldelijk invoert, telt dit niet op als meerdere mislukte aanmeldingen.

De eerste 10 vergrendelings perioden zijn één minuut lang. De volgende 10 vergrendelings perioden zijn iets langer en duren na elke 10 vergrendelings periode. De vergrendelings teller wordt na een geslaagde aanmelding opnieuw ingesteld op nul wanneer het account niet is vergrendeld. Vergrendelings perioden kunnen Maxi maal vijf uur duren.

## <a name="manage-password-protection-settings"></a>Instellingen voor wachtwoord beveiliging beheren

Instellingen voor wachtwoord beveiliging beheren, met inbegrip van de drempel waarde voor vergren deling:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer onder **beveiliging** **verificatie methoden (preview)** en selecteer vervolgens **wachtwoord beveiliging**.
1. Voer de gewenste instellingen voor wachtwoord beveiliging in en selecteer vervolgens **Opslaan**.

    ![Azure Portal pagina wachtwoord beveiliging in azure AD-instellingen](./media/threat-management/portal-02-password-protection.png)
    <br />*De drempel waarde voor vergren deling in te stellen op 5 in instellingen voor **wachtwoord beveiliging***.

## <a name="view-locked-out-accounts"></a>Vergrendelde accounts weer geven

Als u informatie wilt ophalen over vergrendelde accounts, kunt u het [rapport Active Directory-aanmeld activiteit](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)controleren. Selecteer onder **status** de optie **fout**. Mislukte aanmeldings pogingen met een **fout code voor aanmelding** die `50053` een vergrendeld account aangeven:

![Sectie van het aanmeldings rapport van Azure AD met het account vergrendeld](./media/threat-management/portal-01-locked-account.png)

Zie voor meer informatie over het weer geven van het rapport met aanmeldings activiteiten in Azure Active Directory [fout codes voor aanmeldings activiteiten melden](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
