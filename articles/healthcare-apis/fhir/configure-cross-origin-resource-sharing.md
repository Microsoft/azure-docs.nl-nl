---
title: Cross-Origin-resource delen configureren in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u cross-Origin-resource deling configureert in azure API voor FHIR.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018139"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Cross-Origin-resource delen configureren in azure API voor FHIR

De Azure-API voor snelle bronnen voor de gezondheids zorg (FHIR) ondersteunt het [gebruik van CORS (cross-Origin Resource Sharing)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). Met CORS kunt u instellingen configureren zodat toepassingen van het ene domein (oorsprong) toegang hebben tot bronnen van een ander domein, ook wel een cross-domain-aanvraag.

CORS wordt vaak gebruikt in een app met één pagina die een REST-API moet aanroepen naar een ander domein.

Als u een CORS-instelling in de Azure API voor FHIR wilt configureren, geeft u de volgende instellingen op:

- **Oorsprong (Access-Control-Allow-Origin)**. Een lijst met domeinen die cross-Origin-aanvragen mogen maken voor de Azure API voor FHIR. Elk domein (oorsprong) moet op een afzonderlijke regel worden ingevoerd. U kunt een asterisk (*) invoeren als u aanroepen vanuit elk domein wilt toestaan, maar dit wordt niet aanbevolen omdat het een beveiligings risico is.

- **Headers (Access-Control-Allow-headers)**. Een lijst met koppen die de oorspronkelijke aanvraag bevat. Als u alle headers wilt toestaan, voert u een asterisk (*) in.

- **Methoden (Access-Control-Allow-methoden)**. De toegestane methoden (PUT, GET, POST, enzovoort) in een API-aanroep. Kies **Alles selecteren** voor alle methoden.

- **Maximale leeftijd (Access-Control-Max-Age)**. De waarde in seconden voor het cachen van Preflight-aanvraag resultaten voor Access-Control-Allow-headers en Access-Control-Allow-methods.

- **Referenties toestaan (Access-Control-Allow-credentials)**. CORS-aanvragen bevatten normaal gesp roken geen cookies om [CSRF-aanvallen (cross-site request vervalsing)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) te voor komen. Als u deze instelling selecteert, kan de aanvraag worden ingediend om referenties op te nemen, zoals cookies. U kunt deze instelling niet configureren als u oorsprong al hebt ingesteld met een asterisk (*).

![CORS-instellingen (cross-Origin Resource Sharing)](media/cors/cors.png)

>[!NOTE]
>U kunt geen andere instellingen opgeven voor verschillende domein oorsprong. Alle instellingen (**headers**, **methoden**, **maximum leeftijd** en **referenties toestaan**) zijn van toepassing op alle oorsprongen die zijn opgegeven in de instelling oorsprong.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het delen van cross-origin in azure API voor FHIR kunt configureren. Implementeer daarna een volledig beheerde Azure-API voor FHIR:
 
>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-portal-quickstart.md)