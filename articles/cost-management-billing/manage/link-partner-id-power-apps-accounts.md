---
title: Een partner-ID koppelen aan uw Power Apps-accounts met uw Azure-referenties
description: In dit artikel kunnen micro soft-partners hun Azure-referenties gebruiken om klanten te helpen bij het gebruik van micro soft power apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: adaff7a6b8559fe9604412a44eced6c490231e3c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603818"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Een partner-ID koppelen aan uw Power Apps-accounts

Dit artikel helpt micro soft-partners, die Power apps-service providers zijn, om hun service aan klanten te koppelen met micro soft power apps. Wanneer u (de micro soft-partner) Power Apps-services voor uw klant beheert, configureert en ondersteunt, hebt u toegang tot de omgeving van uw klant. U kunt uw Azure-referenties en een partner beheer koppeling (PAL) gebruiken om uw partner netwerk-ID te koppelen aan de account referenties die worden gebruikt voor de levering van services.

Met PAL kan micro soft partners met Power apps-klanten identificeren en herkennen. Micro soft maakt gebruik van kenmerken van de organisatie van een partner op basis van de machtigingen van het account (rol van Power-apps) en bereik (Tenant, resource groep, bron).

## <a name="get-access-from-your-customer"></a>Toegang van uw klant krijgen

Voordat u uw partner-ID koppelt, moet uw klant u toegang geven tot hun Power apps-resources met behulp van een van de volgende opties:

- **Gast gebruiker** : uw klant kan u als gast gebruiker toevoegen en alle Power-apps rollen toewijzen. Zie [Gastgebruikers uit een andere directory toevoegen](../../active-directory/external-identities/what-is-b2b.md) voor meer informatie.
- **Directory-account** : uw klant kan een gebruikers account voor u maken in een eigen map en een rol voor Power apps toewijzen.
- **Service-Principal** : uw klant kan een app of script toevoegen vanuit uw organisatie in hun Directory en een rol voor Power apps toewijzen. De identiteit van de app of het script staat bekend als een service-principal.
- **Gedelegeerde beheerder** : uw klant kan een resource groep delegeren zodat uw gebruikers vanuit uw Tenant ermee kunnen werken. Zie [voor partners: de gedelegeerde beheerder](/power-platform/admin/for-partners-delegated-administrator)voor meer informatie.

## <a name="link-customer-to-a-partner-id"></a>Klant aan een partner-ID koppelen

Wanneer u toegang hebt tot de resources van uw klant, gebruikt u de Azure Portal, Power shell of de Azure CLI om uw Microsoft Partner Network-ID (MPN-ID) te koppelen aan uw gebruikers-ID of Service-Principal. Koppel de partner-ID aan elke Tenant van de klant.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure Portal gebruiken om een nieuwe partner-id te koppelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar [Koppelen met een partner-id](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) in Azure Portal.
1. Voer de [Microsoft Partner Network](https://partner.microsoft.com/) -id voor uw organisatie in. Zorg ervoor dat u de  **bijbehorende MPN-id**  gebruikt die wordt weer gegeven in uw partner profiel.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Scherm afbeelding van de koppeling naar een partner-ID-venster." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Als u de partner-ID wilt koppelen aan een andere klant, schakelt u over naar de map. Selecteer de juiste map onder **map switch**.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Scherm opname van het venster van de map en het abonnement waar u kunt overschakelen naar een andere map." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>PowerShell gebruiken om een nieuwe partner-id te koppelen

Installeer de module [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell.

Meld u aan bij de Tenant van de klant met het gebruikers account of de Service-Principal. Zie [Aanmelden met PowerShell](/powershell/azure/authenticate-azureps) voor meer informatie.

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Maak een koppeling met de nieuwe partner-id. De partner-id is de [Microsoft Partner Network-id](https://partner.microsoft.com/) voor uw organisatie. Zorg ervoor dat u de **bijbehorende MPN-id**  gebruikt die wordt weer gegeven in uw partner profiel.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

De gekoppelde partner-id ophalen

```azurepowershell-interactive
get-AzManagementPartner
```

De gekoppelde partner-id bijwerken

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

De gekoppelde partner-id verwijderen

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>De Azure CLI gebruiken om een nieuwe partner-id te koppelen

Installeer eerst de Azure CLI-extensie.

```azurecli-interactive
az extension add --name managementpartner
```

Meld u aan bij de Tenant van de klant met het gebruikers account of de Service-Principal. Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie.

```azurecli-interactive
az login --tenant TenantName
```

Maak een koppeling met de nieuwe partner-id. De partner-id is de [Microsoft Partner Network-id](https://partner.microsoft.com/) voor uw organisatie.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

De gekoppelde partner-id ophalen

```azurecli-interactive
az managementpartner show
```

De gekoppelde partner-id bijwerken

```azurecli-interactive
az managementpartner update --partner-id 12345
```

De gekoppelde partner-id verwijderen

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

In de volgende secties vindt u veelgestelde vragen over het koppelen van een partner-ID aan Power Apps-accounts.

### <a name="who-should-link-the-partner-id"></a>Wie moet de partner-ID koppelen?

Elke gebruiker van de partner organisatie die werkt op de Power apps-resources van de klant kan de partner-ID koppelen aan het account. In het ideale geval moet de koppeling in PAL aan het begin van het project worden uitgevoerd. Het kan echter worden uitgevoerd wanneer u toegang hebt in de map van de klant.

### <a name="can-a-partner-id-be-changed-after-its-linked"></a>Kan een partner-id worden gewijzigd nadat deze is gekoppeld?

Ja. Een gekoppelde partner-id kan worden gewijzigd, toegevoegd of verwijderd. Een voor beeld voor deze situatie is dat wanneer een werk nemer van uw bedrijf uw organisatie verlaat. Een ander voor beeld kan zijn wanneer een project of contract met de klant eindigt.

### <a name="what-if-a-user-has-an-account-in-more-than-one-customer-tenant"></a>Wat gebeurt er als een gebruiker een account in meer dan één klanttenant heeft?

De koppeling tussen de partner-id en het account wordt uitgevoerd voor elke klanttenant. Koppel de partner-id in elke klanttenant.

### <a name="can-other-partners-or-customers-edit-or-remove-the-link-to-the-partner-id"></a>Kunnen andere partners of klanten de koppeling met de partner-id bewerken of verwijderen?

De koppeling is op gebruikersaccountniveau gekoppeld. Alleen u kunt de koppeling met de partner-id bewerken of verwijderen. De klant en andere partners kunnen de koppeling met de partner-id niet wijzigen.

### <a name="which-mpn-id-should-i-use-if-my-company-has-multiple"></a>Welke MPN ID moet ik gebruikers als mijn bedrijf meerdere id's heeft?

Zorg ervoor dat u de **bijbehorende MPN-id** gebruikt die in uw partnerprofiel wordt weergegeven. Doorgaans is dit de koppeling van het lokale account-ID met uw organisatie.

### <a name="how-do-i-explain-pal-to-my-customer"></a>Hoe kan ik u PAL uitleggen voor mijn klant?

PAL stelt micro soft in staat om die partners te identificeren en herkennen die klanten helpen bij het bereiken van zakelijke doel stellingen en het realiseren van de waarde in de Cloud. Klanten moeten eerst een partner toegang bieden tot hun Power apps-resource. Zodra toegang is verleend, wordt de Microsoft Partner Network-ID (MPN-ID) van de partner gekoppeld. Deze koppeling helpt micro soft bij het begrijpen van service providers en het verfijnen van de hulpprogram ma's en Program ma's die nodig zijn voor de beste ondersteuning van klanten.

### <a name="what-data-does-pal-collect"></a>Welke gegevens worden door PAL verzameld?

De PAL-koppeling met bestaande referenties bieden Microsoft geen nieuwe klantgegevens. Het biedt de informatie aan micro soft waar een partner actief betrokken is in de Power apps-omgevingen van een klant. Micro soft kan het kenmerk gebruik en de invloed van de klant omgeving op de organisatie van de partner bepalen op basis van de machtigingen van het account (rol van Power-apps) en het bereik (Tenant, resource groep, resource) dat door de klant aan de partner is gegeven.

### <a name="does-pal-association-affect-the-security-of-a-customers-power-apps-environment"></a>Is PAL Association van invloed op de beveiliging van de Power apps-omgeving van een klant?

PAL-koppeling voegt alleen de MPN-ID van de partner toe aan de referentie die al is ingericht. De machtigingen worden niet gewijzigd (Power apps Role) of bieden extra service gegevens van de Power apps aan de partner of micro soft.

### <a name="next-steps"></a>Volgende stappen

- Neem deel aan de discussie in de [Microsoft Partner Community](https://aka.ms/PALdiscussion) om updates te krijgen of feedback te verzenden.
- Lees de [Veelgestelde vragen over het ontwikkelen van geavanceerde specialisatie voor toepassingen met lage code](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) voor op PAL gebaseerde Power apps-koppeling voor een geavanceerde specialisatie van toepassingen met lage code.
