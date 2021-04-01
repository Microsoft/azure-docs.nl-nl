---
title: Problemen met controle gegevens van geverifieerde domein wijziging oplossen | Microsoft Docs
description: Bevat informatie die wordt weer gegeven in de activiteiten logboeken van Azure Active Directory wanneer u een domein van geverifieerde gebruikers wijzigt.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87117428"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Problemen oplossen: gegevens over geverifieerde domein wijzigingen controleren 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Ik heb veel wijzigingen aan mijn gebruikers en ik weet niet wat de oorzaak van het is.

### <a name="symptoms"></a>Symptomen

Ik controleer de audit logboeken van Azure AD en Bekijk meerdere gebruikers updates in mijn Azure AD-Tenant. Met deze **Update-gebruikers** gebeurtenissen worden geen **actor** gegevens weer gegeven, waardoor onzekerheid wordt veroorzaakt door wat/wie de massale wijzigingen voor gebruikers heeft geactiveerd. 

### <a name="cause"></a>Oorzaak

 Een veelvoorkomende reden achter wijzigingen in massa objecten is een niet-synchrone back-end-bewerking met de naam **ProxyCalc**.  **ProxyCalc** is de logica waarmee de juiste **userPrincipalName** -en **proxy adressen** worden bepaald, die worden bijgewerkt in azure AD-gebruikers,-groepen of-contact personen. Het ontwerp achter **ProxyCalc** is om ervoor te zorgen dat alle **userPrincipalName** -en **proxy adressen** op elk gewenst moment consistent zijn in azure AD. **ProxyCalc** moet worden geactiveerd door een expliciete wijziging zoals een geverifieerde domein wijziging en wordt niet op de achtergrond uitgevoerd als een taak. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>Wat betekent de consistentie van UserPrincipalName? 

Voor Cloud gebruikers betekent consistentie dat de **userPrincipalName** is ingesteld op een geverifieerd domein achtervoegsel. Wanneer een inconsistente **userPrincipalName** wordt verwerkt, wordt deze door **ProxyCalc** geconverteerd naar het standaard onmicrosoft.com-achtervoegsel, bijvoorbeeld: username@Contoso.onmicrosoft.com 

Voor gesynchroniseerde gebruikers betekent consistentie dat de **userPrincipalName** is ingesteld op een geverifieerd domein achtervoegsel en overeenkomt met de on-premises **userPrincipalName** -waarde (ShadowUserPrincipalName). Wanneer een inconsistente **userPrincipalName** wordt verwerkt, wordt **ProxyCalc** teruggezet naar dezelfde waarde als de **ShadowUserPrincipalName** , of als het domein achtervoegsel is verwijderd uit de Tenant, wordt dit geconverteerd naar het standaard domein achtervoegsel *. onmicrosoft.com. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Wat betekenen de consistentie van proxy adressen? 

Voor Cloud gebruikers betekent consistentie dat de proxy adressen overeenkomen met een geverifieerd domein achtervoegsel. Wanneer een inconsistent proxy-adres wordt verwerkt, wordt het door **ProxyCalc** geconverteerd naar het standaard domein achtervoegsel *. onmicrosoft.com, bijvoorbeeld: SMTP:username@Contoso.onmicrosoft.com 

Voor gesynchroniseerde gebruikers betekent consistentie dat de proxy adressen overeenkomen met de on-premises proxy adres (sen)-waarde (n) (dat wil zeggen ShadowProxyAddresses). **ProxyAddresses** worden naar verwachting gesynchroniseerd met **ShadowProxyAddresses**. Als aan de gesynchroniseerde gebruiker een Exchange-licentie is toegewezen, moeten de proxy adressen overeenkomen met de waarde (n) van de on-premises proxy en moeten ze ook overeenkomen met een gecontroleerd domein achtervoegsel. In dit scenario wordt het inconsistente proxy adres met een niet-geverifieerd domein achtervoegsel opgeheven met **ProxyCalc** en verwijderd uit het object in azure AD. Als dat niet-geverifieerde domein later wordt geverifieerd, wordt de **ProxyCalc** opnieuw berekend en wordt het proxy adres van **ShadowProxyAddresses** weer toegevoegd aan het object in azure AD.  

> [!NOTE]
> Voor gesynchroniseerde objecten om te voor komen dat **ProxyCalc** Logic onverwachte resultaten berekent, is het het beste om proxy adressen in te stellen op een geverifieerd Azure AD-domein op het on-premises object.  

  
Een van de beheer taken die **ProxyCalc** kunnen activeren, is telkens wanneer er een geverifieerd domein wordt gewijzigd. Deze taak vindt plaats wanneer een geverifieerd domein wordt toegevoegd aan of verwijderd uit een Azure AD-Tenant, waarmee **ProxyCalc** intern wordt geactiveerd.  

Als u bijvoorbeeld een geverifieerd domein Fabrikam.com toevoegt aan uw Contoso.onmicrosoft.com-Tenant, wordt met deze actie een ProxyCalc-bewerking geactiveerd op alle objecten in de Tenant. Deze gebeurtenis wordt vastgelegd in de audit logboeken van Azure AD als **Update gebruikers** gebeurtenissen, voorafgegaan door een gebeurtenis voor het toevoegen van een **geverifieerd domein** . Aan de andere kant, als Fabrikam.com is verwijderd uit de Contoso.onmicrosoft.com-Tenant, worden alle **gebruikers** gebeurtenissen van de update voorafgegaan door een gebeurtenis voor het verwijderen van een **gecontroleerd domein** .   

#### <a name="additional-notes"></a>Aanvullende opmerkingen:

ProxyCalc veroorzaakt geen wijzigingen in bepaalde objecten: 

- geen actieve Exchange-licentie 
- **MSExchRemoteRecipientType** hebben ingesteld op null 
- worden niet beschouwd als een gedeelde bron. Gedeelde bron is wanneer **CloudMSExchRecipientDisplayType** een van de volgende waarden bevat: **MailboxUser (gedeeld)**, **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **groeps postvak**, **Postvak plannen**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** 
  
 Micro soft werkt samen met het bijwerken van de **actor** gegevens in de audit Logboeken om deze wijzigingen te identificeren, zoals geactiveerd door een geverifieerde domein wijziging, om meer correlatie tussen deze twee verschillende gebeurtenissen te maken. Met deze actie wordt gecontroleerd of de wijzigings gebeurtenis van het geverifieerde domein heeft plaatsgevonden en is begonnen met het bijwerken van de objecten in de Tenant. 

Daarnaast zijn er in de meeste gevallen geen wijzigingen aan gebruikers, omdat hun **userPrincipalName** en **proxy adressen** consistent zijn. in de audit logboeken worden alleen de updates vastgelegd die een daad werkelijke wijziging in het object hebben veroorzaakt. Met deze actie wordt de ruis in de audit logboeken voor komen en helpen beheerders de resterende gebruikers wijzigingen aan de wijzigings gebeurtenis van het geverifieerde domein, zoals hierboven beschreven. 

## <a name="next-steps"></a>Volgende stappen

[Schaduw kenmerken van Azure AD Connect Sync-Service](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
