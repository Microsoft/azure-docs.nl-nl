---
title: KeyVaultCertificateSelector UI-element
description: Hierin wordt het element micro soft. KeyVaultCertificateSelector-gebruikers interface voor Azure Portal beschreven.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101218"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Micro soft. KeyVaultCertificateSelector UI-element

Een besturings element voor het selecteren van een sleutel kluis certificaat.

## <a name="ui-sample"></a>UI-voor beeld

De gebruiker krijgt de mogelijkheid om een beschikbaar certificaat te selecteren.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Micro soft. KeyVaultCertificateSelector":::

Nadat u **een certificaat selecteren selecteert**, kan de gebruiker een sleutel kluis en een certificaat van de sleutel kluis opgeven.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Micro soft. KeyVaultCertificateSelector certificaat selecteren":::

Het besturings element wordt bijgewerkt om de geselecteerde sleutel kluis en certificaat naam weer te geven.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Micro soft. KeyVaultCertificateSelector-kluis. het geselecteerde certificaat weer geven":::

## <a name="schema"></a>Schema

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
