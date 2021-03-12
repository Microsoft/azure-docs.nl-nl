---
title: 'Quickstart: Azure-API for FHIR implementeren met PowerShell'
description: In deze quickstart wordt uitgelegd hoe u Azure API for FHIR implementeert met behulp van PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.openlocfilehash: ca20346f4c2968eac5e7308cb05b59f968a1c63e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018021"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Quickstart: Azure-API for FHIR implementeren met PowerShell

In deze quickstart wordt uitgelegd hoe u Azure API for FHIR implementeert met behulp van PowerShell.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>De Azure-API for FHIR-resourceprovider registreren

Als de `Microsoft.HealthcareApis`-resourceprovider nog niet voor uw abonnement is geregistreerd, kunt u deze registreren bij:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Azure-resourcegroep maken

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Azure API for FHIR implementeren

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> Afhankelijk van de versie van de `Az`PowerShell-module die u hebt geïnstalleerd, kan de ingerichte FHIR-server worden geconfigureerd om [lokale RBAC](configure-local-rbac.md) te gebruiken, waarbij de dan aangemelde PowerShell-gebruiker op de lijst met toegestane identiteitsobject-id's voor de geïmplementeerde FHIR-service staat. Het wordt aanbevolen om daarna [Azure RBAC te gebruiken](configure-azure-rbac.md) voor het toewijzen van gegevenslaagrollen. Mogelijk moet u deze gebruikersobject-id na de implementatie verwijderen om de modus Azure RBAC in te schakelen.


## <a name="fetch-capability-statement"></a>Mogelijkheidsinstructie ophalen

U kunt controleren of de Azure-API voor het FHIR-account wordt uitgevoerd door een FHIR-mogelijkheidsinstructie op te halen:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan de resourcegroep met de volgende stappen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Azure API for FHIR geïmplementeerd in uw abonnement. Als u aanvullende instellingen wilt instellen in uw Azure API for FHIR, gaat u verder met de handleiding bij aanvullende instellingen. Als u klaar bent om te beginnen met het gebruik van Azure API for FHIR, leest u de informatie over het registreren van toepassingen.

>[!div class="nextstepaction"]
>[Aanvullende instellingen in de Azure-API voor FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Overzicht toepassingen registreren](fhir-app-registration.md)
