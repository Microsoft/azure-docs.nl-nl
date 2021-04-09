---
title: Connectors in de Azure AD Synchronization Service Manager-gebruikers interface | Microsoft Docs '
description: Meer informatie over het tabblad connectors in de Synchronization Service Manager voor Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91319874"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Connectors gebruiken met de Azure AD Connect Sync Service Manager

![Scherm opname van de synchronisatie Service Manager.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Het tabblad connectors wordt gebruikt voor het beheren van alle systemen waarop de synchronisatie-engine is aangesloten.

## <a name="connector-actions"></a>Connector acties
| Bewerking | Opmerking |
| --- | --- |
| Maken |Niet gebruiken. Gebruik de installatie wizard om verbinding te maken met extra AD-forests. |
| Eigenschappen |Wordt gebruikt voor filteren op domein en OE. |
| [Verwijderen](#delete) |Wordt gebruikt om de gegevens in de connector ruimte te verwijderen of om een verbinding met een forest te verwijderen. |
| [Uitvoerings profielen configureren](#configure-run-profiles) |Met uitzonde ring van domein filtering, hoeft niets te worden geconfigureerd. U kunt deze actie gebruiken om al geconfigureerde uitvoerings profielen weer te geven. |
| Uitvoeren |Wordt gebruikt om een eenmalige uitvoering van een profiel te starten. |
| Stoppen |Hiermee stopt u een connector die momenteel een profiel uitvoert. |
| Connector exporteren |Niet gebruiken. |
| Connector importeren |Niet gebruiken. |
| Connector bijwerken |Niet gebruiken. |
| Schema vernieuwen |Hiermee vernieuwt u het schema in de cache. U kunt het beste de optie gebruiken in de installatie wizard, omdat ook de synchronisatie regels worden bijgewerkt. |
| [Zoek connector ruimte](#search-connector-space) |Wordt gebruikt voor het zoeken van objecten en het volgen van een object en de bijbehorende gegevens via het systeem. |

### <a name="delete"></a>Verwijderen
De actie verwijderen wordt gebruikt voor twee verschillende dingen.  
![Scherm opname van het venster ' connector verwijderen ' met ' alleen connector ruimte verwijderen ' geselecteerd.](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Met de optie voor het **verwijderen van connector ruimte worden alleen** alle gegevens verwijderd, maar blijft de configuratie ongewijzigd.

Met de optie **connector verwijderen en connector ruimte** verwijdert u de gegevens en de configuratie. Deze optie wordt gebruikt wanneer u niet meer verbinding wilt maken met een forest.

Met beide opties worden alle objecten gesynchroniseerd en worden de omgekeerde objecten bijgewerkt. Deze actie is een langlopende bewerking.

### <a name="configure-run-profiles"></a>Uitvoerings profielen configureren
Met deze optie kunt u de uitvoerings profielen zien die zijn geconfigureerd voor een connector.

![Scherm opname van het venster ' uitvoerings profielen configureren ' waarvoor ' Delta-import bewerking ' is geselecteerd.](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Zoek connector ruimte
De ruimte van de Zoek connector is handig om objecten te vinden en problemen met gegevens op te lossen.

![Scherm afbeelding met het venster Zoek connector ruimte.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Begin door een **bereik** te selecteren. U kunt zoeken op basis van gegevens (RDN, DN, anker, substructuur) of status van het object (alle andere opties).  
![Scherm afbeelding met de vervolg keuzelijst bereik.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Als u bijvoorbeeld een Sub-Tree zoek actie wilt uitvoeren, krijgt u alle objecten in één organisatie-eenheid.  
![Scherm afbeelding met een voor beeld van een ' substructuur ' zoeken.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Vanuit dit raster kunt u een object selecteren, **Eigenschappen** selecteren en dit in de bron connector ruimte [volgen](tshoot-connect-object-not-syncing.md) , via de tekst en de ruimte van de doel connector.

### <a name="changing-the-ad-ds-account-password"></a>Het wachtwoord voor het AD DS-account wijzigen
Als u het wacht woord van het account wijzigt, kan de synchronisatie service geen wijzigingen meer importeren/exporteren naar on-premises AD.   U kunt het volgende te zien krijgen:

- De stap importeren/exporteren voor de AD-connector mislukt met de fout ' No-start-credentials '.
- Onder Windows Logboeken bevat het gebeurtenis logboek van de toepassing een fout met gebeurtenis-ID 6000 en bericht ' de beheer agent ' contoso.com ' kan niet worden uitgevoerd omdat de referenties ongeldig zijn. '

U kunt dit probleem oplossen door de AD DS-gebruikers account bij te werken met het volgende:


1. Start de Synchronization Service Manager (START → synchronisatie service).
</br>![Service Manager synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Ga naar het tabblad **connectors** .
3. Selecteer de AD-connector die is geconfigureerd voor het gebruik van het AD DS-account.
4. Onder acties, selecteert u **Eigenschappen**.
5. Selecteer in het pop-updialoogvenster verbinding maken met Active Directory forest:
6. De naam van het forest geeft de corresponderende on-premises AD aan.
7. De gebruikers naam geeft het AD DS account aan dat wordt gebruikt voor synchronisatie.
8. Voer het nieuwe wacht woord van het AD DS account in het tekstvak wacht woord ![ Azure AD Connect synchronisatie versleutelings sleutel in](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Klik op OK om het nieuwe wacht woord op te slaan en de synchronisatie service opnieuw te starten om het oude wacht woord uit de geheugen cache te verwijderen.



## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
