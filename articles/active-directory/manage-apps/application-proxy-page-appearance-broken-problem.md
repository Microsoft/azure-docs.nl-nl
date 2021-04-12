---
title: App-pagina wordt niet correct weer gegeven voor de app Application proxy | Microsoft Docs
description: Richt lijnen wanneer de pagina niet correct wordt weer gegeven in een toepassings proxy toepassing die u hebt geïntegreerd met Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
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
ms.openlocfilehash: 99b09e7b15427eb33e1e85edd89f4c8a37c4a3eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254651"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Toepassings pagina wordt niet correct weer gegeven voor een toepassings proxy toepassing

Dit artikel helpt u bij het oplossen van problemen met Azure Active Directory-toepassingsproxy-toepassingen wanneer u naar de pagina navigeert, maar iets op de pagina wordt niet correct weer geven.

## <a name="overview"></a>Overzicht
Wanneer u een app voor een toepassings proxy publiceert, zijn alleen pagina's onder uw root toegankelijk bij het openen van de toepassing. Als de pagina niet correct wordt weer gegeven, ontbreekt er mogelijk een aantal pagina bronnen in de interne basis-URL die voor de toepassing wordt gebruikt. Zorg ervoor dat u *alle* resources voor de pagina hebt gepubliceerd als onderdeel van uw toepassing om dit probleem op te lossen.

U kunt controleren of ontbrekende resources het probleem zijn door uw netwerk vastleggen (zoals Fiddler of F12-hulpprogram ma's in Internet Explorer/micro soft Edge) te openen, de pagina te laden en te zoeken naar 404-fouten. Hiermee wordt aangegeven dat de pagina's die momenteel niet kunnen worden gevonden en dat u ze moet publiceren.

Als voor beeld van dit geval wordt ervan uitgegaan dat u een onkosten toepassing hebt gepubliceerd met behulp van de interne URL `http://myapps/expenses` , maar de app maakt gebruik van het opmaak model `http://myapps/style.css` . In dit geval wordt het opmaak model niet gepubliceerd in uw toepassing, waardoor het laden van de app onkosten een 404-fout genereert tijdens het laden van style. CSS. In dit voor beeld wordt het probleem opgelost door de toepassing te publiceren met een interne URL `http://myapp/` .

## <a name="problems-with-publishing-as-one-application"></a>Problemen met publiceren als één toepassing

Als het niet mogelijk is om alle resources binnen dezelfde toepassing te publiceren, moet u meerdere toepassingen publiceren en koppelingen ertussen inschakelen.

Hiervoor raden wij u aan de oplossing voor [aangepaste domeinen](application-proxy-configure-custom-domain.md) te gebruiken. Deze oplossing vereist echter dat u eigenaar bent van het certificaat voor uw domein en dat uw toepassingen volledig gekwalificeerde domein namen (FQDN) gebruiken. Zie de [documentatie problemen oplossen met verbroken koppelingen](application-proxy-page-links-broken-problem.md)voor andere opties.

## <a name="next-steps"></a>Volgende stappen
[Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)
