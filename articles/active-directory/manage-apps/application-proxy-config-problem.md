---
title: Probleem bij het maken van een toepassings proxy toepassing | Microsoft Docs
description: Problemen oplossen met het maken van toepassings proxy toepassingen in de Azure AD-beheer Portal
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0c88b90d0caa2f723921d76b5f9dc036895f14e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258011"
---
# <a name="problem-creating-an-application-proxy-application"></a>Probleem bij het maken van een toepassings proxy toepassing 

Hieronder vindt u enkele veelvoorkomende problemen die het gevolg zijn van het maken van een nieuwe toepassings proxy toepassing.

## <a name="recommended-documents"></a>Aanbevolen documenten 

Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)voor meer informatie over het maken van een toepassings proxy toepassing via de beheer Portal.

Als u de stappen in dit document volgt en er een fout optreedt bij het maken van de toepassing, raadpleegt u de fout Details voor informatie en suggesties voor het oplossen van de toepassing. De meeste fout berichten bevatten een voorgestelde oplossing. 

## <a name="specific-things-to-check"></a>Specifieke dingen om te controleren

Als u veelvoorkomende fouten wilt voor komen, controleert u het volgende:

-   U bent een beheerder met machtigingen voor het maken van een toepassings proxy toepassing

-   De interne URL is uniek

-   De externe URL is uniek

-   De Url's beginnen met http of HTTPS, en eindigen met een "/"

-   De URL moet een domein naam en geen IP-adres zijn

Het fout bericht wordt weer gegeven in de rechter bovenhoek wanneer u de toepassing maakt. U kunt ook het meldings pictogram selecteren om de fout berichten weer te geven.

   ![Meldings prompt](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Volgende stappen
[Toepassingsproxy inschakelen in de Azure-portal](application-proxy-add-on-premises-application.md)
