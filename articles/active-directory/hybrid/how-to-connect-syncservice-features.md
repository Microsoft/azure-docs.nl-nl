---
title: Functies en configuratie van Azure AD Connect-service voor synchronisatie | Microsoft Docs
description: Hierin worden de functies van de service zijde voor Azure AD Connect synchronisatie service beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410535"
---
# <a name="azure-ad-connect-sync-service-features"></a>Functies van de Azure AD Connect-synchronisatieservice

De synchronisatie functie van Azure AD Connect heeft twee onderdelen:

* Het on-premises onderdeel met de naam **Azure AD Connect Sync**, ook wel **Sync Engine** genoemd.
* De service die zich bevindt in azure AD, ook wel bekend als **Azure AD Connect-synchronisatie service**

In dit onderwerp wordt uitgelegd hoe de volgende functies van de **Azure AD Connect Sync-Service** werken en hoe u deze kunt configureren met Windows Power shell.

Deze instellingen worden geconfigureerd door de [Azure Active Directory-module voor Windows Power shell](/previous-versions/azure/jj151815(v=azure.100)). Down load en installeer deze afzonderlijk van Azure AD Connect. De cmdlets die in dit onderwerp worden beschreven, zijn geïntroduceerd in de [release van 2016 maart (build 9031,1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Als u niet beschikt over de cmdlets die in dit onderwerp worden beschreven, of als u niet hetzelfde resultaat hebt, moet u ervoor zorgen dat u de meest recente versie uitvoert.

Voer uit om de configuratie in uw Azure AD-Directory te bekijken `Get-MsolDirSyncFeatures` .  
![Resultaat van Get-MsolDirSyncFeatures](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Veel van deze instellingen kunnen alleen worden gewijzigd door Azure AD Connect.

De volgende instellingen kunnen worden geconfigureerd door `Set-MsolDirSyncFeature` :

| DirSyncFeature | Opmerking |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Toestaan dat objecten naast het primaire SMTP-adres aan userPrincipalName worden toegevoegd. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Hiermee kan de synchronisatie-engine het kenmerk userPrincipalName voor beheerde/gelicentieerde gebruikers (niet-gefedereerde) bijwerken. |

Nadat u een functie hebt ingeschakeld, kan deze niet meer worden uitgeschakeld.

> [!NOTE]
> Vanaf 24 augustus 2016 is de functie *tolerantie voor dubbel kenmerk* voor nieuwe Azure AD-mappen standaard ingeschakeld. Deze functie wordt ook geïmplementeerd en ingeschakeld voor mappen die zijn gemaakt voor deze datum. U ontvangt een e-mail melding wanneer uw adres lijst op het punt staat om deze functie in te scha kelen.
> 
> 

De volgende instellingen worden geconfigureerd door Azure AD Connect en kunnen niet worden gewijzigd door `Set-MsolDirSyncFeature` :

| DirSyncFeature | Opmerking |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: write-back van apparaat inschakelen](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect synchronisatie: Directory-extensies](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency <br/> DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Hiermee kan een kenmerk in quarantaine worden geplaatst wanneer het een duplicaat is van een ander object in plaats van dat het hele object tijdens het exporteren mislukt. |
| Wachtwoordhashsynchronisatie |[Wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](how-to-connect-password-hash-synchronization.md) |
|Pass-through-verificatie|[Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Groep terugschrijven|
| UserWriteback |Momenteel niet ondersteund. |

## <a name="duplicate-attribute-resiliency"></a>Tolerantie voor dubbel kenmerk

In plaats van het inrichten van objecten met dubbele Upn's/proxyAddresses is het dubbele kenmerk ' in quarantaine ' en wordt er een tijdelijke waarde toegewezen. Wanneer het conflict is opgelost, wordt de tijdelijke UPN automatisch gewijzigd in de juiste waarde. Zie [Identiteitssynchronisatie en dubbel kenmerk tolerantie](how-to-connect-syncservice-duplicate-attribute-resiliency.md)voor meer informatie.

## <a name="userprincipalname-soft-match"></a>Dynamische UserPrincipalName-overeenkomst

Als deze functie is ingeschakeld, wordt voor de UPN naast het [primaire SMTP-adres](https://support.microsoft.com/kb/2641663)dat wordt gebruikt altijd de optie soft-overeenkomst ingeschakeld. Soft-overeenkomst wordt gebruikt om bestaande Cloud gebruikers in azure AD te vergelijken met on-premises gebruikers.

Als u on-premises AD-accounts moet overeenkomen met bestaande accounts die in de Cloud zijn gemaakt en u geen gebruikmaakt van Exchange Online, is deze functie nuttig. In dit scenario hebt u over het algemeen geen reden om het SMTP-kenmerk in de cloud in te stellen.

Deze functie is standaard ingeschakeld voor nieuwe Azure AD-mappen. U kunt zien of deze functie is ingeschakeld voor u door het volgende uit te voeren:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Als deze functie niet is ingeschakeld voor uw Azure AD-adres lijst, kunt u deze inschakelen door het volgende uit te voeren:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Updates van userPrincipalName synchroniseren

In het verleden zijn updates van het kenmerk UserPrincipalName met behulp van de synchronisatie service van on-premises geblokkeerd, tenzij beide voor waarden waar zijn:

* De gebruiker wordt beheerd (niet-gefedereerde).
* Er is geen licentie toegewezen aan de gebruiker.

> [!NOTE]
> Van 2019 maart is het synchroniseren van UPN-wijzigingen voor federatieve gebruikers accounts toegestaan.
> 

Als u deze functie inschakelt, kan de synchronisatie-engine de userPrincipalName bijwerken wanneer deze on-premises wordt gewijzigd en u gebruikmaakt van hash-synchronisatie van wacht woord of Pass-Through-verificatie.

Deze functie is standaard ingeschakeld voor nieuwe Azure AD-mappen. U kunt zien of deze functie is ingeschakeld voor u door het volgende uit te voeren:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Als deze functie niet is ingeschakeld voor uw Azure AD-adres lijst, kunt u deze inschakelen door het volgende uit te voeren:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Nadat deze functie is ingeschakeld, blijven de bestaande userPrincipalName-waarden ongewijzigd. Bij de volgende wijziging van het kenmerk userPrincipalName on-premises wordt de UPN door de normale Delta synchronisatie op de gebruikers bijgewerkt.  

## <a name="see-also"></a>Zie ook

* [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md)
* [Uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md).