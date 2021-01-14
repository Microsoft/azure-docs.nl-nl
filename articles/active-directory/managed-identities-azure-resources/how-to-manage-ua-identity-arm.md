---
title: Een door de gebruiker toegewezen beheerde identiteit maken & verwijderen met behulp van Azure Resource Manager
description: Stapsgewijze instructies voor het maken en verwijderen van door de gebruiker toegewezen beheerde identiteiten met behulp van Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbeb8e0fc638955995cc5aaf00f58d40adc69a8
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184872"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven en verwijderen met behulp van Azure Resource Manager


Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel maakt u een door de gebruiker toegewezen beheerde identiteit met behulp van een Azure Resource Manager.

Het is niet mogelijk om een door de gebruiker toegewezen beheerde identiteit weer te geven en te verwijderen met behulp van een Azure Resource Manager sjabloon.  Raadpleeg de volgende artikelen voor het maken en vermelden van een door de gebruiker toegewezen beheerde identiteit:

- [Door de gebruiker toegewezen beheerde identiteit weer geven](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Door de gebruiker toegewezen beheerde identiteit verwijderen](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="template-creation-and-editing"></a>Sjabloon maken en bewerken

Net als Azure Portal en het uitvoeren van scripts, bieden Azure Resource Manager-sjablonen de mogelijkheid om nieuwe of gewijzigde resources gedefinieerd door een Azure-resourcegroep te implementeren. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op basis van een portal, waaronder:

- Door een [aangepaste sjabloon van de Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) te gebruiken, waarmee u een volledig nieuwe sjabloon kunt maken, of door het te baseren op een bestaande algemene sjabloon of een [quickstart-sjabloon](https://azure.microsoft.com/documentation/templates/).
- Door af te leiden van een bestaande resourcegroep, door een sjabloon te exporteren vanaf ofwel [de oorspronkelijke implementatie](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) ofwel van de [huidige status van de implementatie](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Door een lokale [JSON-editor (zoals VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) te gebruiken en deze vervolgens te uploaden en implementeren met behulp van PowerShell of CLI.
- Door het [Azure Resource Group-project](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) van Visual Studio te gebruiken om een sjabloon te maken en te implementeren. 

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, gebruikt u de volgende sjabloon. Vervang de `<USER ASSIGNED IDENTITY NAME>` waarde door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Volgende stappen

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure-VM met behulp van een Azure Resource Manager-sjabloon raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een Azure-VM met behulp van een](qs-configure-template-windows-vm.md)sjabloon.


