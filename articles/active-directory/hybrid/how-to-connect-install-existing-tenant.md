---
title: 'Azure AD Connect: wanneer u al beschikt over Azure AD | Microsoft Docs'
description: In dit onderwerp wordt beschreven hoe u verbinding maken gebruikt wanneer u een bestaande Azure AD-Tenant hebt.
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93420526"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: wanneer u een bestaande Tenant hebt
In de meeste onderwerpen over het gebruik van Azure AD Connect wordt ervan uitgegaan dat u begint met een nieuwe Azure AD-Tenant en dat er geen gebruikers of andere objecten zijn. Maar als u bent begonnen met een Azure AD-Tenant, deze hebt gevuld met gebruikers en andere objecten en nu verbinding maken wilt gebruiken, is dit onderwerp voor u.

## <a name="the-basics"></a>De basisbeginselen
Een object in azure AD is Mastered in de Cloud (Azure AD) of on-premises. Voor één enkel object kunt u bepaalde kenmerken niet on-premises en andere kenmerken in azure AD beheren. Elk object heeft een vlag waarmee wordt aangegeven waar het object wordt beheerd.

U kunt on-premises en andere gebruikers in de Cloud beheren. Een veelvoorkomend scenario voor deze configuratie is een organisatie met een combi natie van boekhoud kundige werk nemers en verkoop medewerkers. De werk nemers van het bedrijf hebben een on-premises AD-account, maar de verkoop medewerkers hebben geen account in azure AD. U beheert sommige gebruikers on-premises en sommige in azure AD.

Als u gebruikers in azure AD wilt beheren die zich ook in on-premises AD bevinden en later verbinding willen gebruiken, zijn er enkele extra zaken die u moet overwegen.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchroniseren met bestaande gebruikers in azure AD
Wanneer u Azure AD Connect installeert en u begint met het synchroniseren, voert de Azure AD Sync-Service (in azure AD) een controle uit op elk nieuw object en wordt geprobeerd een bestaand object te vinden dat u wilt vergelijken. Er worden drie kenmerken gebruikt voor dit proces: **userPrincipalName**, **proxyAddresses** en **Source Anchor** / **immutableID**. Een overeenkomst op **userPrincipalName** en **proxyAddresses** wordt aangeduid als een **zachte overeenkomst**. Een overeenkomst op **Source Anchor** wordt aangeduid als **hard match**. Voor het kenmerk **proxyAddresses** wordt alleen de waarde met **SMTP:**, het primaire e-mail adres, gebruikt voor de evaluatie.

De overeenkomst wordt alleen geëvalueerd voor nieuwe objecten die afkomstig zijn van verbinding maken. Als u een bestaand object wijzigt zodat dit overeenkomt met een van deze kenmerken, ziet u in plaats daarvan een fout.

Als in azure AD een object wordt gevonden waarbij de kenmerk waarden hetzelfde zijn voor een object dat afkomstig is van verbinding maken en dat al aanwezig is in azure AD, wordt het object in azure AD overgenomen door verbinding te maken. Het eerder door de Cloud beheerde object wordt gemarkeerd als on-premises beheerd. Alle kenmerken in azure AD met een waarde in on-premises AD worden overschreven door de on-premises waarde.

> [!WARNING]
> Omdat alle kenmerken in azure AD worden overschreven door de on-premises waarde, moet u ervoor zorgen dat u beschikt over goede gegevens on-premises. Als u bijvoorbeeld alleen een beheerd e-mail adres in Microsoft 365 hebt en dit niet in de on-premises AD DS is bijgewerkt, verliest u alle waarden in azure AD/Microsoft 365 die niet aanwezig zijn in AD DS.

> [!IMPORTANT]
> Als u wachtwoord synchronisatie gebruikt, dat altijd wordt gebruikt door snelle instellingen, wordt het wacht woord in azure AD overschreven met het wacht woord in on-premises AD. Als uw gebruikers worden gebruikt voor het beheren van verschillende wacht woorden, moet u hen informeren dat ze het on-premises wacht woord moeten gebruiken wanneer u Connect hebt geïnstalleerd.

In de vorige sectie en waarschuwing moet rekening worden gehouden met de planning. Als u veel wijzigingen in azure AD hebt aangebracht die niet worden weer gegeven in on-premises AD DS, moet u plannen hoe u AD DS kunt vullen met de bijgewerkte waarden voordat u uw objecten synchroniseert met Azure AD Connect.

Als u uw objecten met een zachte overeenkomst hebt gevonden, wordt de **Source Anchor** toegevoegd aan het object in azure AD, zodat er later een vaste overeenkomst kan worden gebruikt.

>[!IMPORTANT]
> Micro soft raadt u ten zeerste aan om on-premises accounts te synchroniseren met vooraf bestaande beheerders accounts in Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Harde overeenkomst versus zachte overeenkomst
Voor een nieuwe installatie van Connect is er geen praktisch verschil tussen een zacht en een harde overeenkomst. Het verschil bevindt zich in een nood herstel situatie. Als uw server met Azure AD Connect verloren is gegaan, kunt u een nieuwe instantie opnieuw installeren zonder dat er gegevens verloren gaan. Een object met een source anchor wordt verzonden om verbinding te maken tijdens de eerste installatie. De overeenkomst kan vervolgens worden geëvalueerd door de client (Azure AD Connect). Dit is veel sneller dan in azure AD. Er wordt een harde overeenkomst geëvalueerd door verbinding te maken en door Azure AD te verbinden. Een zachte overeenkomst wordt alleen geëvalueerd door Azure AD.

### <a name="other-objects-than-users"></a>Andere objecten dan gebruikers
Voor groepen met e-mail functionaliteit en contact personen kunt u een tijdelijke overeenkomst op basis van proxyAddresses. De vaste overeenkomst is niet van toepassing omdat u alleen de source Anchor/immutableID (met behulp van Power shell) alleen voor gebruikers kunt bijwerken. Voor groepen waarvoor geen e-mail functionaliteit is ingeschakeld, is er momenteel geen ondersteuning voor zachte overeenkomsten of vaste overeenkomsten.

### <a name="admin-role-considerations"></a>Overwegingen voor beheerdersrol
Azure AD Connect komt niet overeen met on-premises gebruikers objecten met objecten die een beheerdersrol hebben om niet-vertrouwde on-premises gebruikers te voor komen dat ze overeenkomen met een Cloud gebruiker met een rol van beheerder. Dit is standaard. U kunt dit probleem als volgt oplossen:

1.  Verwijder de Directory functies uit het alleen-Cloud gebruikers object.
2.  Als er een mislukte gebruikers synchronisatie poging is gedaan, verwijdert u het in quarantaine geplaatste object in de Cloud.
3.  Activeer een synchronisatie.
4.  Voeg eventueel de Directory-rollen toe aan het gebruikers object in de Cloud nadat de overeenkomst heeft plaatsgevonden.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Een nieuwe on-premises Active Directory maken op basis van gegevens in azure AD
Sommige klanten beginnen met een Cloud oplossing met Azure AD en ze hebben geen on-premises AD. Later willen ze on-premises resources gebruiken en een on-premises AD maken op basis van Azure AD-gegevens. Azure AD Connect kan u niet helpen bij dit scenario. Er worden geen gebruikers on-premises gemaakt en er is geen mogelijkheid om het wacht woord on-premises in te stellen op hetzelfde als in azure AD.

Als u van plan bent om on-premises AD toe te voegen, is het mogelijk om LOBs (line-of-Business-Apps) te ondersteunen. in plaats daarvan kunt u overwegen om [Azure AD Domain Services](../../active-directory-domain-services/index.yml) te gebruiken.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
