---
title: Problemen oplossen met een kenmerk dat niet wordt gesynchroniseerd in Azure AD Connect | Microsoft Docs '
description: Dit onderwerp bevat stappen voor het oplossen van problemen met kenmerk synchronisatie met de taak voor het oplossen van problemen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317484"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Problemen oplossen met een kenmerk dat niet wordt gesynchroniseerd in Azure AD Connect

## <a name="recommended-steps"></a>**Aanbevolen stappen**

Voordat u problemen met het synchroniseren van kenmerken kunt onderzoeken, moet u het **Azure AD Connect** synchronisatie proces begrijpen:

  ![Azure AD Connect synchronisatie proces](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Connector ruimte, een tabel in de data base.
* **Mv:** Een omgekeerde, een tabel in de data base.
* **AD:** Active Directory
* **Aad:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synchronisatie stappen**

* Importeren uit AD: Active Directory objecten worden in AD CS gezet.

* Importeren uit AAD: Azure Active Directory objecten worden in AAD CS binnengebracht.

* Synchronisatie: **regels voor binnenkomende synchronisatie** en **uitgaande synchronisatie regels** worden in volg orde van prioriteits nummer van lager naar hoger uitgevoerd. Als u de synchronisatie regels wilt weer geven, kunt u naar de **Editor voor synchronisatie regels** gaan vanuit de bureaublad toepassingen. De **binnenkomende synchronisatie regels** brengen gegevens over van CS naar mv. Met de **regels voor uitgaande synchronisatie** worden gegevens verplaatst van MV naar CS.

* Exporteren naar AD: wanneer de synchronisatie is uitgevoerd, worden objecten van AD CS geëxporteerd naar **Active Directory**.

* Exporteren naar AAD: als u de synchronisatie uitvoert, worden objecten van AAD CS geëxporteerd naar **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Stap voor stap onderzoek**

* We starten onze zoek opdracht vanuit de **tekst** en bekijken de kenmerk toewijzing van bron naar doel.

* Start **Synchronization Service Manager** van de bureaublad toepassingen, zoals hieronder wordt weer gegeven:

  ![Synchronization Service Manager starten](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Selecteer op de **Synchronization Service Manager** de **omgekeerde zoek opdracht**, selecteer **bereik per object type**, selecteer het object met een kenmerk en klik op de knop **zoeken** .

  ![Omgekeerde zoek opdracht](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Dubbel klik op het object in de **omgekeerde** zoek opdracht om alle kenmerken ervan weer te geven. U kunt op het tabblad **connectors** klikken om het overeenkomende object in alle **connector ruimten** te bekijken.

  ![Omgekeerde object connectors](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Dubbel klik op de **Active Directory-Connector** om de kenmerken van de **connector ruimte** weer te geven. Klik op de knop **Preview** in het volgende dialoog venster op de knop **Preview genereren** .

  ![Scherm opname van het scherm Eigenschappen van connector ruimte-object met de knop Preview gemarkeerd.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Klik nu op de **kenmerk stroom importeren**. Hiermee wordt de stroom van kenmerken van **Active Directory-Connector ruimte** naar de **omgekeerde tekst** weer gegeven. Kolom **synchronisatie regel** toont welke **synchronisatie regel** aan dat kenmerk is bijgedragen. In de kolom **gegevens bron** worden de kenmerken van de **connector ruimte** weer gegeven. De eigenschaps kolom van het **kenmerk autoverse** toont u de kenmerken in de **tekst**. U kunt zoeken naar het kenmerk dat hier niet wordt gesynchroniseerd. Als u het kenmerk hier niet kunt vinden, wordt dit niet toegewezen en moet u nieuwe aangepaste **synchronisatie regel** maken om het kenmerk toe te wijzen.

  ![Kenmerken van connector ruimte](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Klik op de **stroom kenmerk exporteren** in het linkerdeel venster om de kenmerk stroom van een **terugkerend** terug naar **Active Directory-Connector ruimte** weer te geven met behulp van **uitgaande synchronisatie regels**.

  ![Scherm opname van de kenmerk stroom van een omgekeerde teruggave naar Active Directory-Connector ruimte met behulp van uitgaande synchronisatie regels.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Op dezelfde manier kunt u het **Azure Active Directory Connector Space** -object weer geven en de **Preview-versie** voor het weer geven van de kenmerk stroom van een **omgekeerde** naar de **connector ruimte** en vice versa. zo kunt u onderzoeken waarom een kenmerk niet is gesynchroniseerd.

## <a name="recommended-documents"></a>**Aanbevolen documenten**
* [Azure AD Connect synchronisatie: technische concepten](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect synchronisatie: uitleg van de architectuur](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect Sync: uitleg over declaratieve inrichting](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect Sync: uitleg over declaratieve inrichtings expressies](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect-synchronisatie: inzicht in de standaardconfiguratie](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect Sync: informatie over gebruikers, groepen en contact personen](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect Sync: schaduw kenmerken](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md).
- [Wat is een Hybrid Identity?](whatis-hybrid-identity.md).