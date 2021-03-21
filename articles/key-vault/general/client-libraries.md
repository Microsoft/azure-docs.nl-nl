---
title: Clientbibliotheken voor Azure Key Vault | Microsoft Docs
description: Clientbibliotheken voor Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: c5b07c1059e146aaf3c3c2a26038514eb614a3cc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505916"
---
# <a name="client-libraries-for-azure-key-vault"></a>Clientbibliotheken voor Azure Key Vault

De clientbibliotheken voor Azure Key Vault bieden programmatische toegang tot Key Vault-functionaliteit vanuit diverse talen, waaronder .NET, Python, Java en JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Clientbibliotheken per taal en object

Elke SDK heeft afzonderlijke clientbibliotheken voor een sleutelkluis, geheimen, sleutels en certificaten, zoals u kunt zien in de onderstaande tabel.

| Taal | Geheimen | Sleutels | Certificaten | Sleutelkluis (beheerlaag) |
|--|--|--|--|--|
| .NET | - [API-naslaginformatie](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet-pakket](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Quickstart](../secrets/quick-create-net.md) | - [API-naslaginformatie](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet-pakket](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Quickstart](../keys/quick-create-net.md) | - [API-naslaginformatie](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet-pakket](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Quickstart](../certificates/quick-create-net.md) | - [API-naslaginformatie](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API-naslaginformatie](/python/api/overview/azure/keyvault-secrets-readme)<br>- [Pypi-pakket](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-python.md) |- [API-naslaginformatie](/python/api/overview/azure/keyvault-keys-readme)<br>- [Pypi-pakket](https://pypi.org/project/azure-keyvault-keys/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Quickstart](../keys/quick-create-python.md) | - [API-naslaginformatie](/python/api/overview/azure/keyvault-certificates-readme)<br>- [Pypi-pakket](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-python.md) | - [API-naslaginformatie](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [Pypi-pakket](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API-naslaginformatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-java.md) |- [API-naslaginformatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Quickstart](../keys/quick-create-java.md) | - [API-naslaginformatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-java.md) |- [API-naslaginformatie](/java/api/com.microsoft.azure.management.keyvault)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API-naslaginformatie](/javascript/api/@azure/keyvault-secrets/)<br>- [NPM-pakket](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-node.md) |- [API-naslaginformatie](/javascript/api/@azure/keyvault-keys/)<br>- [NPM-pakket](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Quickstart](../keys/quick-create-node.md)| - [API-naslaginformatie](/javascript/api/@azure/keyvault-certificates/)<br>- [NPM-pakket](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-node.md) |  - [API-naslaginformatie](/javascript/api/@azure/arm-keyvault/)<br>- [NPM-pakket](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Volgende stappen

- Zie de [gids voor Azure Key Vault-ontwikkelaars](developers-guide.md) (vooralsnog Engelstalig).
- Meer informatie over het [verifiëren bij een sleutelkluis](authentication.md)
- Meer informatie over het [beveiligen van toegang tot een sleutelkluis](secure-your-key-vault.md)
