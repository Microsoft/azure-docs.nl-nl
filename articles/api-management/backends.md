---
title: Back-API Management van Azure | Microsoft Docs
description: Meer informatie over aangepaste back-API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813016"
---
# <a name="backends-in-api-management"></a>Back-API Management

Een *back-end* (of *API-back-end)* in API Management is een HTTP-service die uw front-end-API en de bewerkingen ervan implementeert.

Wanneer u bepaalde API's importeert, API Management de API-back-end automatisch geconfigureerd. De back-API Management bijvoorbeeld geconfigureerd bij het importeren van een [OpenAPI-specificatie,](import-api-from-oas.md) [SOAP API](import-soap-api.md)of Azure-resources, zoals een door HTTP geactiveerde [Azure-functie-app](import-function-app-as-api.md) of [logische app](import-logic-app-as-api.md).

API Management ondersteunt ook het gebruik van andere Azure-resources, zoals een [Service Fabric-cluster](how-to-configure-service-fabric-backend.md) of een aangepaste service als api-back-end. Voor het gebruik van deze aangepaste back-ends is extra configuratie vereist, bijvoorbeeld om referenties van aanvragen aan de back-endservice te autoreren en API-bewerkingen te definiëren. U configureert en beheert deze back-Azure Portal in de Azure Portal of met behulp van Azure API's of hulpprogramma's.

Nadat u een back-end hebt aanmaken, kunt u verwijzen naar de back-end-URL in uw API's. Gebruik het beleid om een binnenkomende API-aanvraag om te leiden naar de aangepaste back-end in plaats [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) van de standaardback-end voor die API.

## <a name="benefits-of-backends"></a>Voordelen van back-enden

Een aangepaste back-end heeft verschillende voordelen, waaronder:

* Abstractie van informatie over de back-endservice, het bevorderen van herbruikbaarheid tussen API's en verbeterde governance  
* Eenvoudig te gebruiken door een transformatiebeleid voor een bestaande API te configureren
* Maakt gebruik van API Management functionaliteit voor het onderhouden van [](api-management-howto-properties.md) geheimen in Azure Key Vault als benoemde waarden zijn geconfigureerd voor verificatie van header- of queryparameters

## <a name="next-steps"></a>Volgende stappen

* Stel een [back-Service Fabric in met](how-to-configure-service-fabric-backend.md) behulp van de Azure Portal.
* Back-Azure PowerShell kunnen ook worden geconfigureerd met behulp van de API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)of [Azure Resource Manager sjablonen.](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

