---
title: Azure AD Connect in Microsoft Cloud Duitsland
description: Azure AD Connect integreert uw on-premises adreslijsten met Azure Active Directory. Zo kunt u een algemene identiteit bieden voor Microsoft 365-, Azure-en SaaS-toepassingen die zijn geïntegreerd met Azure AD.
keywords: inleiding tot Azure AD Connect, overzicht Azure AD Connect, wat is Azure AD Connect, Active Directory installeren, Duitsland, Zwarte Woud
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246f8326f7112825de98ee824cf4bab7952a7878
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90014584"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect in Microsoft Cloud Duitsland - Openbare preview
## <a name="introduction"></a>Inleiding
Azure AD Connect voorziet in synchronisatie tussen uw on-premises Active Directory en Azure Active Directory.
Op dit moment moeten veel van de scenario's in [Microsoft Cloud Duitsland](https://azure.microsoft.com/global-infrastructure/germany/
) worden uitgevoerd door de operator. Wanneer u Microsoft Cloud Duitsland gebruikt, moet u rekening houden met de volgende informatie:

* De volgende URL's moeten via een proxyserver worden geopend als u wilt dat de synchronisatie succesvol verloopt:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Certificaatintrekkingslijsten
* Wanneer u zich bij uw Azure AD-directory aanmeldt, moet u een account uit het domein onmicrosoft.de gebruiken.

 
## <a name="download"></a>Downloaden
U kunt Azure AD Connect downloaden via de Azure AD Connect-blade in de portal.  Volg de onderstaande instructies om de Azure AD Connect-blade te vinden.

### <a name="the-azure-ad-connect-blade"></a>De Azure AD Connect-blade
Zodra u bent aangemeld bij de Azure-portal:

1. Ga naar Bladeren
2. Selecteer Azure Active Directory
3. Selecteer vervolgens Azure AD Connect

U ziet deze details:

![De Azure AD Connect-blade](./media/reference-connect-germany/germany1.png)

In de volgende tabel staan welke functies worden weergegeven in de blade.

| Titel | Beschrijving |
| --- | --- |
| SYNCHRONISATIESTATUS |Hier ziet u of synchronisatie is in- of uitgeschakeld. |
| LAATSTE SYNCHRONISATIE |De laatste keer dat een synchronisatie is voltooid. |
| FEDERATIEVE DOMEINEN |Hier ziet u hoeveel federatieve domeinen er momenteel zijn geconfigureerd. |

## <a name="installation"></a>Installatie
Als u Azure AD Connect wilt installeren, kunt u [deze](how-to-connect-install-roadmap.md) documentatie gebruiken.

## <a name="advanced-features-and-additional-information"></a>Geavanceerde functies en aanvullende informatie
Ga naar [Uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md) voor aanvullende informatie over aangepaste instellingen of geavanceerde configuraties. Deze pagina bevat informatie over en koppelingen naar aanvullende richtlijnen.

