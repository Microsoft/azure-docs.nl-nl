---
title: Azure API Management-back-ends | Microsoft Docs
description: Meer informatie over aangepaste back-ends in API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500615"
---
# <a name="backends-in-api-management"></a>Back-ends in API Management

Een *back* -end (of *API-back-end*) in API Management is een HTTP-service waarmee u de front-end-API en de bewerkingen ervan implementeert.

Bij het importeren van bepaalde Api's configureert API Management de API-back-end automatisch. API Management configureert bijvoorbeeld de back-end bij het importeren van [een OpenAPI-specificatie](import-api-from-oas.md), SOAP- [API](import-soap-api.md)of Azure-resources, zoals een door http geactiveerd [Azure functie-app](import-function-app-as-api.md) of [logische app](import-logic-app-as-api.md).

API Management biedt ook ondersteuning voor het gebruik van andere Azure-resources, zoals een [service Fabric cluster](how-to-configure-service-fabric-backend.md) of een aangepaste service als een API-back-end. Voor het gebruik van deze aangepaste back-ends is extra configuratie vereist, bijvoorbeeld om referenties van aanvragen voor de back-end-service te autoriseren en API-bewerkingen te definiëren. U kunt deze back-endservers configureren en beheren in de Azure Portal of met behulp van Azure-Api's of-hulpprogram ma's.

Nadat u een back-end hebt gemaakt, kunt u naar de back-end-URL in uw Api's verwijzen. Gebruik het [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) beleid om een binnenkomende API-aanvraag om te leiden naar de aangepaste back-end in plaats van de standaard back-end voor die API.

## <a name="benefits-of-backends"></a>Voor delen van back-ends

Een aangepaste back-end heeft verschillende voor delen, waaronder:

* Abstracten informatie over de back-end-service, bevordering van de bruikbaarheid van Api's en verbeterd governance  
* Eenvoudig te gebruiken door een transformatie beleid te configureren voor een bestaande API
* Maakt gebruik van API Management functionaliteit om geheimen in Azure Key Vault te behouden als [benoemde waarden](api-management-howto-properties.md) zijn geconfigureerd voor koptekst-of query parameter verificatie

## <a name="next-steps"></a>Volgende stappen

* Stel een [service Fabric back-end](how-to-configure-service-fabric-backend.md) in met behulp van de Azure Portal.
* Back-ends kunnen ook worden geconfigureerd met behulp van de sjablonen API Management [rest API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)of [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

