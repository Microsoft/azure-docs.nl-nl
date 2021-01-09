---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052737"
---
SMB meerdere kanalen voor Azure-bestands shares heeft momenteel de volgende beperkingen:
- Kan alleen worden gebruikt met lokaal redundante FileStorage-accounts.
- Alleen ondersteund voor Windows-clients. 
- Het maximum aantal kanalen is vier.
- SMB direct wordt niet ondersteund.
- Privé-eind punten voor opslag accounts worden niet ondersteund.
- Voor opslag accounts met on-premises Active Directory Domain Services (AD DS) of Azure AD DS [verificatie op basis van identiteiten](../articles/storage/files/storage-files-active-directory-overview.md) die zijn ingeschakeld voor Azure files, kunnen SMB-clients Windows Verkenner niet gebruiken voor het configureren van NTFS-machtigingen voor mappen en bestanden.
    - Gebruik het Windows [icacls](/windows-server/administration/windows-commands/icacls) -hulp programma of de [set-ACL-](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) opdracht in plaats daarvan om machtigingen te configureren.

