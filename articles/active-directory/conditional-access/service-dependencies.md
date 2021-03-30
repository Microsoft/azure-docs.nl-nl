---
title: Afhankelijkheden van service voor voorwaardelijke toegang-Azure Active Directory
description: Meer informatie over hoe voor waarden worden gebruikt in Azure Active Directory voorwaardelijke toegang om een beleid te activeren.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91258605"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Wat zijn service afhankelijkheden in Azure Active Directory voorwaardelijke toegang? 

Met beleid voor voorwaardelijke toegang kunt u toegangs vereisten voor websites en services opgeven. Uw toegangs vereisten kunnen bijvoorbeeld bestaan uit het vereisen van multi-factor Authentication (MFA) of [beheerde apparaten](require-managed-devices.md). 

Wanneer u rechtstreeks toegang krijgt tot een site of service, is de impact van een gerelateerd beleid doorgaans gemakkelijk te beoordelen. Als u bijvoorbeeld een beleid hebt waarvoor multi-factor Authentication (MFA) voor share point online is geconfigureerd, wordt MFA afgedwongen voor elke aanmelding bij de share point-webportal. Het is echter niet altijd direct voor het beoordelen van de impact van een beleid, omdat er Cloud-apps zijn met afhankelijkheden van andere Cloud-apps. Micro soft teams kunnen bijvoorbeeld toegang bieden tot resources in share point online. Als u in het huidige scenario toegang hebt tot micro soft-teams, bent u daarom ook gebonden aan het share point MFA-beleid. 

> [!TIP]
> Het gebruik van de [office 365](concept-conditional-access-cloud-apps.md#office-365) -app is gericht op alle Office-apps om problemen met service afhankelijkheden in de Office-stack te voor komen.

## <a name="policy-enforcement"></a>Beleidsafdwinging 

Als u een service afhankelijkheid hebt geconfigureerd, kan het beleid worden toegepast met behulp van vroegtijdige binding of een te late afhankelijk afdwinging. 

- **Early-gebonden beleids afdwinging** betekent dat een gebruiker moet voldoen aan het afhankelijke service beleid voordat de aanroepende app wordt geopend. Een gebruiker moet bijvoorbeeld voldoen aan het share point-beleid voordat u zich aanmeldt bij MS-teams. 
- Na de aanmelding van de gebruiker bij de aanroepende app wordt nagaand **beleid afgedwongen** . Afdwinging wordt uitgesteld bij het aanroepen van app-aanvragen, een token voor de downstream-service. Voor beelden zijn MS-teams die toegang hebben tot de planner en Office.com toegang tot share point. 

In het onderstaande diagram ziet u de service afhankelijkheden van MS teams. Effen pijlen geven een vroegtijdige afdwinging aan met de onderbroken pijl voor planner wordt aangegeven dat het goed gebonden is. 

![Service afhankelijkheden van MS teams](./media/service-dependencies/01.png)

Als best practice moet u waar mogelijk algemene beleids regels instellen voor alle gerelateerde apps en services. Een consistente beveiligings postuur biedt u de beste gebruikers ervaring. Als u bijvoorbeeld een gemeen schappelijk beleid instelt voor Exchange Online, share point online, micro soft teams en Skype voor bedrijven, worden onverwachte prompts die kunnen voortvloeien uit verschillende beleids regels die worden toegepast op downstream-Services, aanzienlijk verminderd. 

Een fantastische manier om dit te doen met toepassingen in de Office-stack is het gebruik van de [office 365-app](concept-conditional-access-cloud-apps.md#office-365) in plaats van afzonderlijke toepassingen te richten.

In de onderstaande tabel vindt u aanvullende service afhankelijkheden waaraan de client-apps moeten voldoen  

| Client-apps         | Downstream-service                          | Maatregelen |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure beheer (Portal en API) | Vroeg gebonden |
| Micro soft leslokaal | Exchange                                    | Vroeg gebonden |
|                     | SharePoint                                  | Vroeg gebonden |
| Microsoft Teams     | Exchange                                    | Vroeg gebonden |
|                     | MS planner                                  | Te laat gebonden  |
|                     | Microsoft Stream                            | Te laat gebonden  |
|                     | SharePoint                                  | Vroeg gebonden |
|                     | Skype voor Bedrijven Online                   | Vroeg gebonden |
| Office-Portal       | Exchange                                    | Te laat gebonden  |
|                     | SharePoint                                  | Te laat gebonden  |
| Outlook-groepen      | Exchange                                    | Vroeg gebonden |
|                     | SharePoint                                  | Vroeg gebonden |
| PowerApps           | Microsoft Azure beheer (Portal en API) | Vroeg gebonden |
|                     | Windows Azure Active Directory              | Vroeg gebonden |
| Project             | Dynamics CRM                                | Vroeg gebonden |
| Skype voor Bedrijven  | Exchange                                    | Vroeg gebonden |
| Visual Studio       | Microsoft Azure beheer (Portal en API) | Vroeg gebonden |
| Microsoft Forms     | Exchange                                    | Vroeg gebonden |
|                     | SharePoint                                  | Vroeg gebonden |
| Microsoft To-Do     | Exchange                                    | Vroeg gebonden |

## <a name="next-steps"></a>Volgende stappen

Zie [de implementatie van voorwaardelijke toegang plannen in azure Active Directory](plan-conditional-access.md)voor meer informatie over het implementeren van voorwaardelijke toegang in uw omgeving.
