---
title: 'Azure AD Connect Sync: de standaard configuratie wijzigen | Microsoft Docs'
description: Biedt aanbevolen procedures voor het wijzigen van de standaard configuratie van Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e91ff8fa3666a2dfc5aaad07be7927852b08bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357695"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect synchronisatie: Aanbevolen procedures voor het wijzigen van de standaard configuratie
Het doel van dit onderwerp is het beschrijven van ondersteunde en niet-ondersteunde wijzigingen in Azure AD Connect synchronisatie.

De configuratie die door Azure AD Connect is gemaakt, werkt in de meeste omgevingen die on-premises Active Directory met Azure AD synchroniseren. In sommige gevallen is het echter nood zakelijk om enkele wijzigingen toe te passen op een configuratie om te voldoen aan een bepaalde behoefte of vereiste.

## <a name="changes-to-the-service-account"></a>Wijzigingen in het service account
Azure AD Connect synchronisatie wordt uitgevoerd onder een service account dat is gemaakt door de installatie wizard. Dit service account bevat de versleutelings sleutels voor de data base die wordt gebruikt door de synchronisatie. Het is gemaakt met een lang wacht woord van 127 tekens en het wacht woord is ingesteld op niet verlopen.

* Het wordt **niet ondersteund** om het wacht woord van het service account te wijzigen of opnieuw in te stellen. Hierdoor worden de versleutelings sleutels vernietigd en kan de service geen toegang krijgen tot de data base en kan niet worden gestart.

## <a name="changes-to-the-scheduler"></a>Wijzigingen in de scheduler
Vanaf de release van build 1,1 (februari 2016) kunt u instellen dat de [scheduler](how-to-connect-sync-feature-scheduler.md) een andere synchronisatie cyclus heeft dan de standaard 30 minuten.

## <a name="changes-to-synchronization-rules"></a>Wijzigingen in synchronisatie regels
De installatie wizard bevat een configuratie die moet worden gebruikt voor de meest voorkomende scenario's. Als u wijzigingen moet aanbrengen in de configuratie, moet u deze regels volgen om nog steeds een ondersteunde configuratie te hebben.

> [!WARNING]
> Als u wijzigingen aanbrengt in de standaard regels voor synchronisatie, worden deze wijzigingen overschreven wanneer de volgende keer Azure AD Connect wordt bijgewerkt, wat resulteert in onverwachte en waarschijnlijk ongewenste synchronisatie resultaten.

* U kunt [kenmerk stromen wijzigen](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) als de standaard directe kenmerk stromen niet geschikt zijn voor uw organisatie.
* Als u [geen kenmerk](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) wilt door lopen en bestaande kenmerk waarden in azure ad wilt verwijderen, moet u een regel voor dit scenario maken.
* [Schakel een ongewenste synchronisatie regel uit](#disable-an-unwanted-sync-rule) in plaats van deze te verwijderen. Een verwijderde regel wordt opnieuw gemaakt tijdens een upgrade.
* Als u [een out-of-Box-regel wilt wijzigen](#change-an-out-of-box-rule), moet u een kopie van de oorspronkelijke regel maken en de out-of-Box-regel uitschakelen. De editor voor synchronisatie regels vraagt en helpt u.
* Exporteer uw aangepaste synchronisatie regels met de editor voor synchronisatie regels. De editor biedt u een Power shell-script dat u kunt gebruiken om ze eenvoudig opnieuw te maken in een nood herstel scenario.

> [!WARNING]
> De out-of-Box Sync-regels hebben een vinger afdruk. Als u deze regels wijzigt, komt de vinger afdruk niet meer overeen. Het kan voor komen dat u in de toekomst problemen ondervindt wanneer u een nieuwe versie van Azure AD Connect probeert toe te passen. Breng alleen wijzigingen aan zoals beschreven in dit artikel.

### <a name="disable-an-unwanted-sync-rule"></a>Een ongewenste synchronisatie regel uitschakelen
Verwijder geen out-of-Box Sync-regel. Het wordt opnieuw gemaakt tijdens de volgende upgrade.

In sommige gevallen heeft de installatie wizard een configuratie gemaakt die niet werkt voor uw topologie. Als u bijvoorbeeld een topologie van een account-resource forest hebt, maar u het schema in het account forest hebt uitgebreid met het Exchange-schema, worden de regels voor Exchange gemaakt voor het account forest en de bron-forest. In dit geval moet u de synchronisatie regel voor Exchange uitschakelen.

![Uitgeschakelde synchronisatie regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

In de bovenstaande afbeelding heeft de installatie wizard een oud Exchange 2003-schema gevonden in het account-forest. Deze schema-uitbrei ding is toegevoegd voordat de bron-forest werd geïntroduceerd in de omgeving van fabrikam. Om ervoor te zorgen dat er geen kenmerken van de oude Exchange-implementatie worden gesynchroniseerd, moet de synchronisatie regel worden uitgeschakeld, zoals wordt weer gegeven.

### <a name="change-an-out-of-box-rule"></a>Een out-of-Box-regel wijzigen
De enige keer dat u een out-of-Box-regel moet wijzigen, is wanneer u de regel voor samen voegen wilt wijzigen. Als u een kenmerk stroom wilt wijzigen, moet u een synchronisatie regel met een hogere prioriteit dan de out-of-Box-regels maken. De enige regel die u nagenoeg nodig hebt om te klonen, is de regel **in van de deelname aan AD-gebruikers**. U kunt alle andere regels vervangen door een regel met een hogere prioriteit.

Als u wijzigingen moet aanbrengen in een out-of-Box-regel, moet u een kopie maken van de out-of-Box-regel en de oorspronkelijke regel uitschakelen. Breng vervolgens de wijzigingen aan in de gekloonde regel. De editor voor synchronisatie regels helpt u bij deze stappen. Wanneer u een out-of-Box-regel opent, wordt het volgende dialoog venster weer gegeven:  
![Waarschuwing voor een niet-geregelde box](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecteer **Ja** als u een kopie van de regel wilt maken. Vervolgens wordt de gekloonde regel geopend.  
![Gekloonde regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Breng op deze gekloonde regel de gewenste wijzigingen aan in het bereik, de koppeling en de trans formaties.

## <a name="next-steps"></a>Volgende stappen
**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
