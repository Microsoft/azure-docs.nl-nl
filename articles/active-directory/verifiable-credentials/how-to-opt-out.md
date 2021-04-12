---
title: Afmelden bij de Azure Active Directory verifieer bare referenties (preview-versie)
description: Meer informatie over het afmelden van de voor beeld van verifieer bare referenties
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: d6e72b6d6f566fcf3f52e1c48ab6824c0e9a968e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222789"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Opt-out van de verifieer bare referenties (preview-versie)

In dit artikel:

- De reden waarom u zich moet afmelden.
- De stappen zijn vereist.
- Wat gebeurt er met uw gegevens?
- Effect op bestaande verifieer bare referenties.

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Controleer de onboarding van verifieer bare referenties.

## <a name="potential-reasons-for-opting-out"></a>Mogelijke redenen voor het afmelden

Op dit moment kunnen we geen wijzigingen aanbrengen in de domein gegevens. Als u een fout maakt of besluit dat u een wijziging wilt aanbrengen, is er dus geen andere optie beschikbaar naast inbellen en opnieuw starten.

## <a name="the-steps-required"></a>De stappen die vereist zijn

1. Zoek in het Azure Portal naar verifieer bare referenties.
2. Kies **instellingen** in het menu aan de linkerkant.
3. Klik onder de sectie **uw organisatie opnieuw instellen** op **alle referenties verwijderen en opt-in Preview**.

   ![instellingen org opnieuw instellen](media/how-to-opt-out/settings-reset.png)

4. Lees het waarschuwings bericht en selecteer **verwijderen en opt-out**.

   ![instellingen verwijderen en opt-out](media/how-to-opt-out/delete-and-opt-out.png)

U hebt nu uit de preview-versie van de geverifieerde referenties gekozen. Blijf lezen om te begrijpen wat er gebeurt met de schermen.

## <a name="what-happens-to-your-data"></a>Wat gebeurt er met uw gegevens?

Wanneer u de functie voor het afmelden van de Azure Active Directory Controleer bare referentie service hebt voltooid, worden de volgende acties uitgevoerd:

- De gewiste sleutels in Key Vault worden [zacht verwijderd](../../key-vault/general/soft-delete-overview.md).
- Het object voor de certificaat verlener wordt uit de data base verwijderd.
- De Tenant-id wordt uit de data base verwijderd. 
- Alle contracten-objecten worden uit onze data base verwijderd.

Zodra er een opt-out plaatsvindt, kunt u uw werk niet meer herstellen of geen enkele bewerking uitvoeren. Deze stap is een eenrichtings bewerking en u moet zich opnieuw aanmelden, wat resulteert in een nieuw exemplaar dat wordt gemaakt.  

## <a name="effect-on-existing-verifiable-credentials"></a>Effect op bestaande verifieer bare referenties

Alle Controleer bare referenties die al zijn uitgegeven, blijven bestaan. Ze worden niet cryptografisch ongeldig, omdat uw was omgezet in een ION-omzet.
Als relying party's echter de status-API aanroepen, ontvangen ze altijd een fout bericht.

## <a name="next-steps"></a>Volgende stappen

- Verifieer bare Referenties instellen voor uw [Azure-Tenant](get-started-verifiable-credentials.md)