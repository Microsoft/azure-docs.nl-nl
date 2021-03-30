---
title: API's exporteren van Azure API Management naar Microsoft Power Platform | Microsoft Docs
description: Meer informatie over het exporteren van API's van API Management naar Microsoft Power Platform.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146651"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>API's exporteren van Azure API Management naar Microsoft Power Platform 

Vrijwillige ontwikkelaars die Microsoft [Power](https://powerplatform.microsoft.com) Platform gebruiken, moeten vaak beschikken hebben over de zakelijke mogelijkheden die door professionele ontwikkelaars zijn ontwikkeld en in Azure geïmplementeerd. [Azure API Management](https://aka.ms/apimrocks) stelt professionele ontwikkelaars in staat hun back-endservice als API's te publiceren en deze API's eenvoudig als aangepaste connectors voor gebruik door vrijwillige ontwikkelaars naar Power Platform (Power Apps en Power Automate) te exporteren. 

In dit artikel worden de stappen beschreven voor het exporteren van API's van API Management naar Power Platform. 

## <a name="prerequisites"></a>Vereisten

+ Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
+ Zorg ervoor dat er zich een API in uw API Management-exemplaar bevindt die u naar Power Platform wilt exporteren
+ Zorg ervoor dat u beschikt over een Power Apps- of Power Automate-[omgeving](/powerapps/powerapps-overview#power-apps-for-admins) 

## <a name="export-an-api"></a>Een API exporteren

1. Navigeer naar uw API Management-service in de Azure-portal en selecteer **API's** in het menu.
2. Klik op de drie puntjes naast de API die u wilt exporteren. 
3. Selecteer **Exporteren**.
4. Selecteer **Power Apps en Power Automate**.
5. Kies een omgeving waarnaar u de API wilt exporteren. 
6. Geef een weergavenaam op. Deze wordt gebruikt als de naam van de aangepaste connector.  
7. Als de API wordt beveiligd door een OAuth 2.0-server, kunt u desgewenst aanvullende informatie opgeven, bijvoorbeeld `Client ID`, `Client secret`, `Authorization URL`, `Token URL` en `Refresh URL`.  
8. Selecteer **Exporteren**. 

Wanneer het exporteren is voltooid, gaat u naar uw Power Apps- of Power Automate-omgeving. De API wordt weergegeven als een aangepaste connector.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Microsoft Power Platform](https://powerplatform.microsoft.com/)
* [U krijgt meer informatie over veelvoorkomende taken in API Management door de zelfstudies te volgen](./import-and-publish.md)
