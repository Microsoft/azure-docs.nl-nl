---
title: 'Azure AD Connect: ADSync-service account | Microsoft Docs'
description: In dit onderwerp wordt het ADSync-service account beschreven en worden aanbevolen procedures met betrekking tot het account geboden.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722154"
---
# <a name="adsync-service-account"></a>ADSync-serviceaccount
Azure AD Connect een on-premises service installeert waarmee de synchronisatie tussen Active Directory en Azure Active Directory wordt georchestrator.  De Microsoft Azure AD Sync Synchronization-Service (ADSync) wordt uitgevoerd op een server in uw on-premises omgeving.  De referenties voor de service worden standaard ingesteld in de snelle installaties, maar kunnen worden aangepast om te voldoen aan de beveiligings vereisten van uw organisatie.  Deze referenties worden niet gebruikt om verbinding te maken met uw on-premises forests of Azure Active Directory.

Het kiezen van het ADSync-service account is een belang rijke plannings beslissing die u moet nemen voordat u Azure AD Connect installeert.  Bij een poging om de referenties na de installatie te wijzigen, wordt de service niet gestart, wordt de toegang tot de synchronisatie database verbroken en kan niet worden geverifieerd met uw verbonden directory's (Azure en AD DS).  Er vindt geen synchronisatie plaats totdat de oorspronkelijke referenties zijn hersteld.

De synchronisatie service kan worden uitgevoerd onder verschillende accounts. Het kan worden uitgevoerd onder een virtueel service account (LEVERANCIERSPECIFIEKE naam), een beheerd service account (gMSA/sMSA) of een gewoon gebruikers account. De ondersteunde opties zijn gewijzigd met de release van 2017 april en 2021 maart van Azure AD Connect wanneer u een nieuwe installatie maakt. Als u een upgrade uitvoert van een eerdere versie van Azure AD Connect, zijn deze extra opties niet beschikbaar. 


|Type account|Installatie optie|Beschrijving| 
|-----|------|-----|
|Virtueel service-account|Express en aangepast, 2017 april en hoger| Een virtueel service account wordt gebruikt voor alle snelle installaties, met uitzonde ring van installaties op een domein controller. Wanneer u aangepaste installatie gebruikt, is dit de standaard optie, tenzij een andere optie wordt gebruikt.| 
|Beheerd serviceaccount|Aangepast, 2017 april en hoger|Als u een externe SQL Server gebruikt, raden we u aan een beheerd service account voor een groep te gebruiken. |
|Beheerd serviceaccount|Express en aangepast, 2021 maart en hoger|Een zelfstandig beheerd service account dat wordt voorafgegaan door ADSyncMSA_ wordt gemaakt tijdens de installatie van snelle installaties wanneer dit wordt geïnstalleerd op een domein controller. Wanneer u aangepaste installatie gebruikt, is dit de standaard optie, tenzij een andere optie wordt gebruikt.|
|Gebruikersaccount|Express en Custom, 2017 april tot 2021 maart|Een gebruikers account met AAD_ wordt gemaakt tijdens de installatie van snelle installaties wanneer geïnstalleerd op een domein controller. Wanneer u aangepaste installatie gebruikt, is dit de standaard optie, tenzij een andere optie wordt gebruikt.|
|Gebruikersaccount|Express en aangepast, 2017 maart en ouder|Een gebruikers account met AAD_ wordt gemaakt tijdens de installatie van snelle installaties. Wanneer u een aangepaste installatie gebruikt, kan er een ander account worden opgegeven.| 

>[!IMPORTANT]
> Als u verbinding maken met een build van 2017 maart of eerder gebruikt, moet u het wacht woord niet opnieuw instellen voor het service account, omdat Windows de versleutelings sleutels om veiligheids redenen heeft vernietigd. U kunt het account niet wijzigen in een ander account zonder Azure AD Connect opnieuw te installeren. Als u een upgrade uitvoert naar een build van 2017 april of hoger, wordt het wacht woord voor het service account gewijzigd, maar u kunt het gebruikte account niet wijzigen. 

> [!IMPORTANT]
> U kunt het service account alleen instellen bij de eerste installatie. Het is niet mogelijk om het service account te wijzigen nadat de installatie is voltooid. Als u het wacht woord van het service account moet wijzigen, wordt dit ondersteund en vindt u [hier](how-to-connect-sync-change-serviceacct-pass.md)instructies.

Hier volgt een tabel met de standaard, aanbevolen en ondersteunde opties voor het synchronisatie service account. 

Legenda: 

- **Vet** geeft de standaard optie aan en, in de meeste gevallen, de aanbevolen optie. 
- *Italic* geeft de aanbevolen optie aan wanneer dit niet de standaard optie is. 
- Optie niet-vet: ondersteund 
- Lokaal account: lokale gebruikers account op de server 
- Domein account-domein gebruikers account 
- sMSA- [zelfstandig beheerd service account](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA: door [groep beheerd service account](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB </br> Express**|**LocalDB/LocalSQL </br> aangepast**|**Aangepaste externe SQL- </br>**|
|-----|-----|-----|-----|
|**computer die lid is van een domein**|**KENMERK**|**KENMERK**</br> *sMSA*</br> *gMSA*</br> Lokaal account</br> Domeinaccount| *gMSA* </br>Domeinaccount|
|Domeincontroller| **sMSA**|**sMSA** </br>*gMSA*</br> Domeinaccount|*gMSA*</br>Domeinaccount| 

## <a name="virtual-service-account"></a>Virtueel service-account 

Een virtueel service account is een speciaal type beheerde lokale account dat geen wacht woord heeft en automatisch wordt beheerd door Windows. 

 ![Virtueel service-account](media/concept-adsync-service-account/account-1.png)

Het Virtual service-account is bedoeld om te worden gebruikt met scenario's waarbij de synchronisatie-engine en SQL zich op dezelfde server bevinden. Als u externe SQL gebruikt, raden we u aan om in plaats daarvan een beheerd service account voor een groep te gebruiken. 

Het virtuele-service account kan niet worden gebruikt op een domein controller vanwege [Windows Data Protection API (DPAPI)-](https://msdn.microsoft.com/library/ms995355.aspx) problemen. 

## <a name="managed-service-account"></a>Beheerd serviceaccount 

Als u een externe SQL Server gebruikt, raden we u aan een beheerd service account voor een groep te gebruiken. Zie overzicht van door een groep [beheerde service accounts](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))voor meer informatie over het voorbereiden van uw Active Directory voor door een groep beheerd service account. 

Als u deze optie wilt gebruiken, selecteert u op de pagina [vereiste onderdelen installeren](how-to-connect-install-custom.md#install-required-components) de optie **een bestaand service account gebruiken** en selecteert u een **beheerd service account**. 

 ![beheerd service account](media/concept-adsync-service-account/account-2.png)

Het wordt ook ondersteund voor het gebruik van een zelfstandig beheerd service account. Deze kunnen echter alleen worden gebruikt op de lokale computer en er is geen voor deel van het gebruik van de standaard virtuele-service account. 

### <a name="auto-generated-standalone-managed-service-account"></a>Automatisch gegenereerd zelfstandig beheerd service account 

Als u Azure AD Connect op een domein controller installeert, wordt een zelfstandig beheerd service account gemaakt door de installatie wizard (tenzij u het account opgeeft dat moet worden gebruikt in aangepaste instellingen). Het account wordt voorafgegaan **ADSyncMSA_** en wordt gebruikt om de huidige synchronisatie service uit te voeren als. 

Dit account is een beheerd domein account dat geen wacht woord heeft en automatisch wordt beheerd door Windows. 

Dit account is bedoeld om te worden gebruikt met scenario's waarbij de synchronisatie-engine en SQL zich op de domein controller bevinden. 

## <a name="user-account"></a>Gebruikersaccount 

Er wordt een lokaal service account gemaakt door de installatie wizard (tenzij u het account opgeeft dat moet worden gebruikt in aangepaste instellingen). Het account wordt voorafgegaan AAD_ en wordt gebruikt om de huidige synchronisatie service uit te voeren als. Als u Azure AD Connect op een domein controller installeert, wordt het account in het domein gemaakt. Het AAD_-service account moet zich in het domein bevinden als: 
- u gebruikt een externe server met SQL Server 
- u gebruikt een proxy waarvoor verificatie is vereist 

 ![gebruikers account](media/concept-adsync-service-account/account-3.png)

Het account wordt gemaakt met een lang complex wacht woord dat niet verloopt. 

Dit account wordt gebruikt om wacht woorden op een veilige manier op te slaan voor de andere accounts. Deze andere account wachtwoorden worden in de-data base opgeslagen als versleuteld. De persoonlijke sleutels voor de versleutelings sleutels worden beveiligd met de geheime sleutel versleuteling van cryptografische Services met behulp van Windows Data Protection API (DPAPI). 

Als u een volledige SQL Server gebruikt, is het service account de DBO van de gemaakte Data Base voor de synchronisatie-engine. De service werkt niet zoals bedoeld met een andere machtiging. Er wordt ook een SQL-aanmelding gemaakt. 

Aan het account worden ook machtigingen verleend voor bestanden, register sleutels en andere objecten die betrekking hebben op de synchronisatie-engine. 


## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
