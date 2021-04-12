---
title: Azure Policy-beleid exporteren
description: Meer informatie over het exporteren van Azure Policy-resources naar GitHub, zoals beleids definities en beleids toewijzingen.
ms.date: 03/31/2021
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fd2a24d2c01b4214eda88054b45a409cd865a32
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096543"
---
# <a name="export-azure-policy-resources"></a>Azure Policy-beleid exporteren

Dit artikel bevat informatie over het exporteren van uw bestaande Azure Policy-resources. Het exporteren van uw resources is nuttig en aanbevolen voor back-ups, maar is ook een belang rijke stap in uw reis met Cloud governance en het behandelen van uw [beleid als code](../concepts/policy-as-code.md). Azure Policy resources kunnen worden geëxporteerd via [Azure Portal](#export-with-azure-portal), [Azure cli](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)en elk van de ondersteunde sdk's.

## <a name="export-with-azure-portal"></a>Exporteren met Azure Portal

Voer de volgende stappen uit om een beleidsdefinitie te exporteren uit Azure Portal:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **Definities** aan de linkerkant van de Azure Policy-pagina.

1. Gebruik de knop **Definities exporteren** of selecteer het beletselteken in de rij van een beleidsdefinitie en selecteer vervolgens **Definitie exporteren**.

1. Selecteer de knop **Aanmelden bij GitHub**. Als u nog niet hebt geverifieerd met GitHub om Azure Policy te autoriseren voor het exporteren van de resource, bekijkt u de toegang tot de [GitHub-actie](https://github.com/features/actions) in het nieuwe venster dat wordt geopend en selecteert u **AzureGitHubActions autoriseren** om door te gaan met het exportproces. Zodra dit is voltooid, wordt het nieuwe venster automatisch gesloten.

1. Stel op het tabblad **Basisprincipes** de volgende opties in en selecteer vervolgens het tabblad **Beleid** of de knop **Volgende: Beleid** onderaan de pagina.

   - **Filter voor opslagplaats**: Ingesteld op _Mijn opslagplaatsen_ om alleen opslagplaatsen weer te geven waarvan u de eigenaar bent, of _Alle opslagplaatsen_ om alles weer te geven waaraan u de GitHub-actie toegang hebt verleend.
   - **Opslagplaats**: Stel in op de opslagplaats waarnaar u de Azure Policy-resources wilt exporteren.
   - **Vertakking**: Stel de vertakking in de opslagplaats in. Het gebruik van een andere vertakking dan de standaard is een goede manier om uw updates te valideren voordat u de broncode verder samenvoegt.
   - **Map**: De _map op het hoofdniveau_ om de Azure Policy-resources naar te exporteren. Submappen onder deze map worden gemaakt op basis van de resources die worden geëxporteerd.

1. Stel op het tabblad **Beleid** het bereik in op zoeken door het beletselteken te selecteren en een combinatie van beheergroepen, abonnementen of resourcegroepen te kiezen.
   
1. Gebruik de knop **Beleidsdefinitie(s) toevoegen** om het bereik te doorzoeken op zoek naar welke objecten moeten worden geëxporteerd. Selecteer in het venster aan de zijkant dat wordt geopend elk object dat u wilt exporteren. Filter de selectie door middel van het zoekvak of het type. Wanneer u alle objecten hebt geselecteerd die u wilt exporteren, gebruikt u de knop **Toevoegen** onderaan de pagina.

1. Selecteer voor elk geselecteerd object de gewenste exportopties, zoals _Alleen definitie_ of _Definitie en toewijzing(en)_ voor een beleidsdefinitie. Selecteer vervolgens het tabblad **Beoordelen + exporteren** of de knop **Volgende: Beoordelen + exporteren** onderaan de pagina.

   > [!NOTE]
   > Als de optie _Definitie en toewijzing(en)_ wordt gekozen, worden alleen beleidstoewijzingen geëxporteerd binnen het bereik dat is ingesteld door het filter wanneer de beleidsdefinitie wordt toegevoegd.

1. Controleer op het tabblad **Beoordelen + exporteren** of de informatie overeenkomt en gebruik vervolgens de knop **Exporteren** aan de onderkant van de pagina.

1. Controleer uw GitHub-opslagplaats, vertakking en _map op het hoofdniveau_ om te zien of de geselecteerde resources nu zijn geëxporteerd naar uw broncodebeheer.

De Azure Policy-resources worden geëxporteerd naar de volgende structuur in de geselecteerde GitHub-opslagplaats en _map op het hoofdniveau_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exporteren met Azure CLI

Azure Policy definities, initiatieven en toewijzingen kunnen elk worden geëxporteerd als JSON met [Azure cli](/cli/azure/install-azure-cli). In elk van deze opdrachten wordt een para meter **name** gebruikt om op te geven voor welk object de JSON moet worden opgehaald. De eigenschap **name** is vaak een _GUID_ en is niet de **DisplayName** van het object.

- Definitie- [AZ Policy Definition show](/cli/azure/policy/definition#az_policy_definition_show)
- Initiative- [AZ policy set-definition show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Toewijzing- [AZ-beleids toewijzing weer geven](/cli/azure/policy/assignment#az_policy_assignment_show)

Hier volgt een voor beeld van het ophalen van de JSON voor een beleids definitie met de **naam** _VirtualMachineStorage_:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exporteren met Azure PowerShell

Azure Policy definities, initiatieven en toewijzingen kunnen worden geëxporteerd als JSON met [Azure PowerShell](/powershell/azure/). Elk van deze cmdlets maakt gebruik van een para meter **name** om op te geven voor welk object de JSON moet worden opgehaald. De eigenschap **name** is vaak een _GUID_ en is niet de **DisplayName** van het object.

- Definitie- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Initiative- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Toewijzing- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Hier volgt een voor beeld van het ophalen van de JSON voor een beleids definitie met de **naam** _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
