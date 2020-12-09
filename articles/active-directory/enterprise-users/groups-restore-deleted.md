---
title: Een verwijderde Microsoft 365-groep herstellen - Azure AD | Microsoft Docs
description: Een verwijderde groep herstellen, groepen weergeven die kunnen worden hersteld en een groep definitief verwijderen in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfdc96df548a4a3df2b789759889b85835e1382a
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860725"
---
# <a name="restore-a-deleted-microsoft-365-group-in-azure-active-directory"></a>Een verwijderde Microsoft 365-groep herstellen in Azure Active Directory

Wanneer u een Microsoft 365-groep verwijdert in Azure Active Directory (Azure AD), blijft de verwijderde groep behouden maar is deze gedurende 30 dagen na de verwijderingsdatum niet meer zichtbaar. Zo kunnen de groep en bijbehorende inhoud indien nodig nog worden hersteld. Deze functionaliteit is exclusief beperkt tot Microsoft 365-groepen in Azure AD. Het is niet beschikbaar voor beveiligingsgroepen en distributiegroepen. Houd er rekening mee dat de herstelperiode van 30 dagen voor de groep niet kan worden aangepast.

> [!NOTE]
> Gebruik `Remove-MsolGroup` niet omdat de groep dan definitief wordt leeggemaakt. Gebruik altijd `Remove-AzureADMSGroup` om een Microsoft 365-groep te verwijderen.

De machtigingen die zijn vereist om een groep te herstellen, kunnen zijn:

Rol | Machtigingen
--------- | ---------
Globale beheerder, groepsbeheerder, Laag2-ondersteuning voor partner en Intune-beheerder | Kan elke willekeurige Microsoft 365-groep herstellen
Gebruikersbeheerder en Laag1-ondersteuning voor partner | Kan elke verwijderde Microsoft 365-groep herstellen, behalve de groepen die zijn toegewezen aan de rol Bedrijfsbeheerder
Gebruiker | Kan elke verwijderde Microsoft 365-groep herstellen waarvan ze eigenaar zijn

## <a name="view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore"></a>De verwijderde Microsoft 365-groepen weergeven en beheren die beschikbaar zijn om te herstellen

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een gebruikersbeheerdersaccount.

2. Selecteer **Groepen** en vervolgens **Verwijderde groepen** om de verwijderde groepen weer te geven die beschikbaar zijn om te herstellen.

    ![groepen weergeven die kunnen worden hersteld](./media/groups-restore-deleted/deleted-groups3.png)

3. Op de blade **Verwijderde groepen** kunt u:

   - De verwijderde groep en de inhoud ervan herstellen door **Groep herstellen** te selecteren.
   - De verwijderde groep definitief verwijderen door **Definitief verwijderen** te selecteren. U kunt een groep alleen definitief verwijderen als u een beheerder bent.

## <a name="view-the-deleted-microsoft-365-groups-that-are-available-to-restore-using-powershell"></a>De verwijderde Microsoft 365-groepen weergeven die beschikbaar zijn om te herstellen met behulp van Powershell

De volgende cmdlets kunnen worden gebruikt om de verwijderde groepen weer te geven. Zo kunt u controleren of de groep of groepen waarin u geïnteresseerd bent, nog niet definitief zijn leeggemaakt. Deze cmdlets vormen onderdeel van de [Azure AD PowerShell-module](https://www.powershellgallery.com/packages/AzureAD/). In het artikel [Azure Active Directory PowerShell-versie 2](/powershell/azure/active-directory/install-adv2) vindt u meer informatie over deze module.

1.  Voer de volgende cmdlet uit om alle verwijderde Microsoft 365-groepen in de Azure AD-organisatie weer te geven die nog kunnen worden hersteld.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Als u de object-id van een specifieke groep weet (u kunt deze ophalen uit de cmdlet in stap 1) , kunt u de ook volgende cmdlet uitvoeren om te controleren dat de specifieke verwijderde groep nog niet definitief is leeggemaakt.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-microsoft-365-group-using-powershell"></a>Uw verwijderde Microsoft 365-groep herstellen met behulp van Powershell

Zodra u hebt gecontroleerd of de groep nog steeds beschikbaar is voor herstellen, herstelt u de verwijderde groep met een van de volgende stappen. Als de groep documenten, SP-sites of andere permanente objecten bevat, duurt het maximaal 24 uur om deze groep en de bijbehorende inhoud te herstellen.

1. Voer de volgende cmdlet uit om de groep en de bijbehorende inhoud te herstellen.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. U kunt ook de volgende cmdlet uitvoeren om de verwijderde groep definitief te verwijderen.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Hoe weet u of dit heeft gewerkt?

Voer de cmdlet `Get-AzureADGroup –ObjectId <objectId>` uit om informatie over de groep weer te geven als u wilt controleren of het herstellen van een Microsoft 365-groep is geslaagd. Nadat de herstelaanvraag is voltooid, ziet u het volgende:

- De groep wordt in de linkernavigatiebalk in Exchange weergegeven
- Het plan voor de groep wordt weergegeven in Planner
- Alle SharePoint-sites en alle bijbehorende inhoud zijn beschikbaar
- Toegang tot de groep kan worden verkregen via elk Exchange-eindpunt en andere Microsoft 365-workload die ondersteuning biedt voor Microsoft 365-groepen

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory-groepen.

* [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Leden van een groep beheren](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
