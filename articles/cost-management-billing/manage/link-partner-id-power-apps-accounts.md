---
title: Een partner-id koppelen aan uw Power Apps accounts met uw Azure-referenties
description: Dit artikel helpt Microsoft-partners hun Azure-referenties te gebruiken om klanten te helpen microsoft-Power Apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: acb22cc4b2a461e476131a83972db3e782425a39
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727704"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Een partner-id koppelen aan uw Power Apps accounts

Dit artikel helpt Microsoft-partners, die Power Apps zijn, om hun service te koppelen aan klanten op Microsoft Power Apps. Wanneer u (de Microsoft-partner) services voor uw klant Power Apps, configureert en ondersteunt, hebt u toegang tot de omgeving van uw klant. U kunt uw Azure-referenties en een PAL (Partner Admin Link) gebruiken om uw partnernetwerk-id te koppelen aan de accountreferenties die worden gebruikt voor servicelevering.

Met de PAL kan Microsoft partners identificeren en herkennen die Power Apps hebben. Microsoft schrijft het gebruik toe aan de organisatie van een partner op basis van de machtigingen (Power Apps rol) en het bereik (tenant, resourcegroep, resource).

## <a name="get-access-from-your-customer"></a>Toegang van uw klant krijgen

Voordat u uw partner-id koppelt, moet uw klant u toegang geven tot hun Power Apps resources met behulp van een van de volgende opties:

- **Gastgebruiker:** uw klant kan u toevoegen als gastgebruiker en alle Power Apps toewijzen. Zie [Gastgebruikers uit een andere directory toevoegen](../../active-directory/external-identities/what-is-b2b.md) voor meer informatie.
- **Directory-account:** uw klant kan een gebruikersaccount voor u maken in zijn eigen directory en elke Power Apps toewijzen.
- **Service-principal:** uw klant kan een app of script uit uw organisatie toevoegen aan de directory en elke Power Apps toewijzen. De identiteit van de app of het script staat bekend als een service-principal.
- **Gedelegeerde beheerder:** uw klant kan een resourcegroep delegeren, zodat uw gebruikers er vanuit uw tenant aan kunnen werken. Zie Voor partners: de [gedelegeerde beheerder voor meer informatie.](/power-platform/admin/for-partners-delegated-administrator)

## <a name="link-customer-to-a-partner-id"></a>Klant koppelen aan een partner-id

Wanneer u toegang hebt tot de resources van uw klant, gebruikt u de Azure Portal, PowerShell of de Azure CLI om uw Microsoft Partner Network-id (MPN-id) te koppelen aan uw gebruikers-id of service-principal. Koppel de partner-id aan elke klant-tenant.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure Portal gebruiken om een nieuwe partner-id te koppelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar [Koppelen met een partner-id](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) in Azure Portal.
1. Voer de [Microsoft Partner Network-id](https://partner.microsoft.com/) voor uw organisatie in. Zorg ervoor dat u de  **bijbehorende MPN-id gebruikt die**  wordt weergegeven in uw partnerprofiel.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Schermopname van het venster Koppeling naar een partner-id." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Als u uw partner-id aan een andere klant wilt koppelen, schakelt u over naar de directory. Selecteer **onder Schakelen tussen** mappen de juiste map.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Schermopname van het venster Map en abonnement waar u van map kunt wisselen." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>PowerShell gebruiken om een nieuwe partner-id te koppelen

Installeer de [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell module.

Meld u aan bij de tenant van de klant met het gebruikersaccount of de service-principal. Zie [Aanmelden met PowerShell](/powershell/azure/authenticate-azureps) voor meer informatie.

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Maak een koppeling met de nieuwe partner-id. De partner-id is de [Microsoft Partner Network-id](https://partner.microsoft.com/) voor uw organisatie. Zorg ervoor dat u de **bijbehorende MPN-id gebruikt die**  wordt weergegeven in uw partnerprofiel.

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

Meld u aan bij de tenant van de klant met het gebruikersaccount of de service-principal. Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie.

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

### <a name="next-steps"></a>Volgende stappen

- Lees de [veelgestelde Cost Management + Billing voor](../cost-management-billing-faq.yml) vragen en antwoorden over het koppelen van een partner-id aan Power Apps accounts.
- Neem deel aan de discussie in de [Microsoft Partner Community](https://aka.ms/PALdiscussion) om updates te krijgen of feedback te verzenden.
- Lees de [veelgestelde](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) vragen over geavanceerde specialisaties voor toepassingsontwikkeling met lage code voor een op PAL gebaseerde Power Apps voor geavanceerde specialisatie voor toepassingsontwikkeling met lage code.
