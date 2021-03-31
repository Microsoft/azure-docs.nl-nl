---
title: Schaduw kenmerken van Azure AD Connect Sync-Service | Microsoft Docs
description: Hierin wordt beschreven hoe schaduw kenmerken werken in Azure AD Connect-synchronisatie service.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 128303cb51b39db8442fdda71f949db17923bfa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90088967"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Schaduw kenmerken van Azure AD Connect Sync-Service
De meeste kenmerken worden in azure AD op dezelfde manier weer gegeven als in uw on-premises Active Directory. Maar sommige kenmerken hebben een speciale verwerking en de kenmerk waarde in azure AD kan afwijken van wat Azure AD Connect synchroniseert.

## <a name="introducing-shadow-attributes"></a>Inleiding tot schaduw kenmerken
Sommige kenmerken hebben twee weer gaven in azure AD. Zowel de on-premises waarde als een berekende waarde worden opgeslagen. Deze extra kenmerken worden schaduw kenmerken genoemd. De twee meest voorkomende kenmerken waar u dit gedrag ziet, zijn **userPrincipalName** en **proxyAddress attribuut**. De wijziging in kenmerk waarden treedt op wanneer er waarden zijn in deze kenmerken die niet-geverifieerde domeinen vertegenwoordigen. Maar de synchronisatie-engine in Connect leest de waarde in het kenmerk Shadow dus uit het perspectief, het kenmerk is bevestigd door Azure AD.

U kunt de schaduw kenmerken niet zien met behulp van de Azure Portal of met Power shell. Maar het begrip van het concept helpt u bij het oplossen van bepaalde scenario's waarbij het kenmerk verschillende waarden op locatie en in de Cloud heeft.

Bekijk dit voor beeld van Fabrikam voor meer informatie over het gedrag.  
![Scherm afbeelding toont het Active Directory UPN-achtervoegsel voor verschillende voor beelden met een bijbehorende Azure AD-domein waarde die niet is toegevoegd, niet is geverifieerd en geverifieerd.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Ze hebben meerdere UPN-achtervoegsels in hun on-premises Active Directory, maar ze hebben er slechts één geverifieerd.

### <a name="userprincipalname"></a>userPrincipalName
Een gebruiker heeft de volgende kenmerk waarden in een niet-geverifieerd domein:

| Kenmerk | Waarde |
| --- | --- |
| on-premises userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Het kenmerk userPrincipalName is de waarde die u ziet wanneer u Power shell gebruikt.

Omdat de feitelijke on-premises kenmerk waarde wordt opgeslagen in azure AD, wordt bij het verifiëren van het fabrikam.com-domein het kenmerk userPrincipalName door Azure AD bijgewerkt met de waarde van de shadowUserPrincipalName. U hoeft geen wijzigingen te synchroniseren van Azure AD Connect om deze waarden te kunnen bijwerken.

### <a name="proxyaddresses"></a>proxyAddresses
Hetzelfde proces geldt ook alleen voor proxyAddresses, met inbegrip van geverifieerde domeinen, maar met een aantal aanvullende logica. De controle op geverifieerde domeinen gebeurt alleen voor gebruikers van een postvak. Een gebruiker met e-mail functionaliteit of contact persoon vertegenwoordigt een gebruiker in een andere Exchange-organisatie en u kunt waarden in proxyAddresses toevoegen aan deze objecten.

Voor een postvak gebruiker, on-premises of in Exchange Online, worden alleen waarden voor geverifieerde domeinen weer gegeven. Dit kan er als volgt uitzien:

| Kenmerk | Waarde |
| --- | --- |
| on-premises proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

In dit geval is **SMTP: Abbie. spencer \@ fabrikam.com** verwijderd omdat dat domein niet is geverifieerd. Maar Exchange heeft ook **SIP toegevoegd: Abbie. spencer \@ fabrikamonline.com**. Fabrikam heeft geen on-premises Lync/Skype gebruikt, maar Azure AD en Exchange Online bereid het voor.

Deze logica voor proxyAddresses wordt aangeduid als **ProxyCalc**. ProxyCalc wordt bij elke wijziging van een gebruiker aangeroepen wanneer:

- De gebruiker is toegewezen aan een service abonnement dat Exchange Online bevat, zelfs als de gebruiker geen licentie voor Exchange heeft. Bijvoorbeeld als aan de gebruiker de Office E3-SKU is toegewezen, maar aan share point online is toegewezen. Dit geldt ook als uw postvak nog steeds on-premises is.
- Het kenmerk msExchRecipientTypeDetails heeft een waarde.
- U brengt een wijziging aan in proxyAddresses of userPrincipalName.

ProxyCalc kan enige tijd duren om een wijziging van een gebruiker te verwerken en is niet synchroon met de Azure AD Connect export proces.

> [!NOTE]
> De ProxyCalc Logic heeft een aantal extra gedrag voor geavanceerde scenario's die niet in dit onderwerp worden beschreven. In dit onderwerp vindt u meer informatie over het gedrag en het documenteren van de interne logica niet.

### <a name="quarantined-attribute-values"></a>Waarden voor in quarantaine geplaatste kenmerken
Schaduw kenmerken worden ook gebruikt wanneer er dubbele kenmerk waarden zijn. Zie [dubbel kenmerk tolerantie](how-to-connect-syncservice-duplicate-attribute-resiliency.md)voor meer informatie.

## <a name="see-also"></a>Zie ook
* [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md)
* [Uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md).
