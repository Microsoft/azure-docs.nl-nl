---
title: Statisch groepslid maatschap wijzigen in dynamisch-Azure AD | Microsoft Docs
description: Meer informatie over het converteren van bestaande groepen van statisch naar dynamisch lidmaatschap met behulp van het Azure AD-beheer centrum of Power shell-cmdlets.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cc9c3e75d92b20bef15e18e0ca6560e9965c61c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95490785"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Statisch groepslid maatschap wijzigen in Azure Active Directory

U kunt het lidmaatschap van een groep wijzigen van statisch in dynamisch (of omgekeerd) in Azure Active Directory (Azure AD). Azure AD houdt dezelfde groeps naam en ID in het systeem, zodat alle bestaande verwijzingen naar de groep nog geldig zijn. Als u in plaats daarvan een nieuwe groep maakt, moet u deze verwijzingen bijwerken. Dynamisch groepslid maatschap elimineert beheer overhead waarmee gebruikers worden toegevoegd en verwijderd. In dit artikel leest u hoe u bestaande groepen van statisch naar dynamisch lidmaatschap kunt converteren met behulp van het Azure AD-beheer centrum of de Power shell-cmdlets.

> [!WARNING]
> Wanneer u een bestaande statische groep wijzigt in een dynamische groep, worden alle bestaande leden uit de groep verwijderd en wordt de lidmaatschaps regel verwerkt om nieuwe leden toe te voegen. Als de groep wordt gebruikt om de toegang tot apps of bronnen te beheren, moet u er rekening mee houden dat de oorspronkelijke leden de toegang kunnen verliezen tot de lidmaatschaps regel volledig is verwerkt.
>
> We raden u aan om de nieuwe lidmaatschaps regel vooraf te testen om ervoor te zorgen dat het nieuwe lidmaatschap van de groep op de verwachte manier wordt verwacht.

## <a name="change-the-membership-type-for-a-group"></a>Het lidmaatschaps type voor een groep wijzigen

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een globale beheerder, gebruikers beheerder of groeps beheerder in uw Azure AD-organisatie is.
2. Selecteer **Groepen**.
3. Open de groep die u wilt wijzigen in de lijst **alle groepen** .
4. Selecteer **Eigenschappen**.
5. Selecteer op de pagina **Eigenschappen** van de groep een **type lidmaatschap** van toegewezen (statisch), dynamische gebruiker of dynamisch apparaat, afhankelijk van het gewenste lidmaatschaps type. Voor dynamisch lidmaatschap kunt u de regel Builder gebruiken om opties voor een eenvoudige regel te selecteren of zelf een lidmaatschaps regel te schrijven. 

De volgende stappen zijn een voor beeld van het wijzigen van een groep van statisch naar dynamisch lidmaatschap voor een groep gebruikers.

1. Selecteer op de pagina **Eigenschappen** voor de geselecteerde groep het **lidmaatschaps type** **dynamisch gebruiker** en selecteer vervolgens Ja in het dialoog venster met uitleg over de wijzigingen in het groepslid maatschap om door te gaan. 
  
   ![lidmaatschaps type van dynamische gebruiker selecteren](./media/groups-change-type/select-group-to-convert.png)
  
2. Selecteer **dynamische query toevoegen** en geef vervolgens de regel op.
  
   ![de regel voor de dynamische groep invoeren](./media/groups-change-type/enter-rule.png)
  
3. Nadat u de regel hebt gemaakt, selecteert u onder aan de pagina **query toevoegen** .
4. Selecteer **Opslaan** op de pagina **Eigenschappen** voor de groep om uw wijzigingen op te slaan. Het **lidmaatschaps type** van de groep wordt onmiddellijk in de groeps lijst bijgewerkt.

> [!TIP]
> Groeps conversie kan mislukken als de ingevoerde lidmaatschaps regel onjuist is. Er wordt in de rechter bovenhoek van de portal een melding weer gegeven waarin wordt uitgelegd waarom de regel niet kan worden geaccepteerd door het systeem. Lees het zorgvuldig om te begrijpen hoe u de regel kunt aanpassen om deze geldig te maken. Zie voor voor beelden van regel syntaxis en een volledige lijst met ondersteunde eigenschappen, Opera tors en waarden voor een lidmaatschaps regel [dynamische lidmaatschaps regels voor groepen in azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Type lidmaatschap voor een groep wijzigen (Power shell)

> [!NOTE]
> Als u de eigenschappen van dynamische groepen wilt wijzigen, moet u cmdlets uit **de preview-versie van** [Azure AD Power shell versie 2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)gebruiken. U kunt de preview-versie van de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview)installeren.

Hier volgt een voor beeld van functies die het lidmaatschaps beheer voor een bestaande groep wijzigen. In dit voor beeld wordt ervoor gezorgd dat de eigenschap GroupTypes correct kan worden gemanipuleerd en alle waarden worden behouden die geen verband houden met het dynamische lidmaatschap.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Een groep statisch maken:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Een groep dynamisch maken:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory-groepen.

* [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een nieuwe groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
