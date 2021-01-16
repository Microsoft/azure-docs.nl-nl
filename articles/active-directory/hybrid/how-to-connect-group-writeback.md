---
title: 'Azure AD Connect: terugschrijven van groep'
description: In dit artikel wordt beschreven hoe u de groep terugschrijven in Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246907"
---
# <a name="azure-ad-connect-group-writeback"></a>Terugschrijven van groep Azure AD Connect

Met groeps terugschrijven kunnen klanten Cloud groepen gebruiken voor hun hybride behoeften. Als u de functie groepen Microsoft 365 gebruikt, kunt u deze groepen weer gegeven in uw on-premises Active Directory. Deze optie is **alleen** beschikbaar als u Exchange aanwezig hebt in uw on-premises Active Directory.

## <a name="pre-requisites"></a>Vereisten
Aan de volgende vereisten moet worden voldaan om de groeps terugschrijven in te scha kelen.
- Azure Active Directory Premium licenties voor uw Tenant.
- Een geconfigureerde hybride implementatie tussen uw on-premises Exchange-organisatie en Microsoft 365 en heeft gecontroleerd of deze goed werkt.
- Er is een ondersteunde versie van Exchange on-premises geïnstalleerd
- Eenmalige aanmelding met Azure Active Directory Connect geconfigureerd 

## <a name="enable-group-writeback"></a>Groep terugschrijven inschakelen
Voer de volgende stappen uit om groeps terugschrijven in te scha kelen:

1. Open de wizard Azure AD Connect, selecteer **configureren** en klik vervolgens op **volgende**.
2. Selecteer **synchronisatie opties aanpassen** en klik vervolgens op **volgende**.
3. Voer uw referenties in op de pagina **verbinding maken met Azure AD** . Klik op **Volgende**.
4. Controleer op de pagina **optionele functies** of de opties die u eerder hebt geconfigureerd, nog steeds zijn geselecteerd.
5. Selecteer **groep terugschrijven** en klik vervolgens op **volgende**.
6. Selecteer op de **pagina terugschrijven** een Active Directory organisatie-eenheid (OE) om objecten op te slaan die zijn gesynchroniseerd van Microsoft 365 naar uw on-premises organisatie en klik vervolgens op **volgende**.
7. Klik op de pagina **gereed** voor configuratie op **configureren**.
8. Wanneer de wizard is voltooid, klikt u op **Afsluiten** op de pagina configuratie voltooid.
9. Open de Windows Power shell als beheerder op de Azure Active Directory Connect-server en voer de volgende opdrachten uit.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

Zie [Microsoft 365 groepen met on-premises Exchange hybride configureren](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect)voor meer informatie over het configureren van de Microsoft 365 groepen.

## <a name="disabling-group-writeback"></a>Terugschrijven van groep uitschakelen
Voer de volgende stappen uit om het terugschrijven van de groep uit te scha kelen: 


1. Start de wizard Azure Active Directory Connect en navigeer naar de pagina Additional Tasks. Selecteer de taak **synchronisatie opties aanpassen** en klik op **volgende**.
2. Schakel op de pagina **optionele functies** het selectie vakje terugschrijven van groep uit.  U ontvangt een waarschuwing waarin wordt aangegeven dat groepen worden verwijderd.  Klik op **Ja**.
   >[!IMPORTANT]
   > Als u het terugschrijven van de groep uitschakelt, worden de groepen die eerder zijn gemaakt door deze functie, verwijderd uit de lokale Active Directory tijdens de volgende synchronisatie cyclus. 

   ![Selectie vakje uitschakelen](media/how-to-connect-group-writeback/group2.png)
  
3. Klik op **Volgende**.
4. Klik op **Configureren**

 >[!NOTE]
 > Als u het terugschrijven van de groep uitschakelt, worden de volledige import-en volledige synchronisatie vlaggen ingesteld op ' True ' op de Azure Active Directory-connector, waardoor de regel wordt gewijzigd in de volgende synchronisatie cyclus en de groepen die eerder werden teruggeschreven naar uw Active Directory worden verwijderd.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
