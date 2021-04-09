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
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102603236"
---
SMB meerdere kanalen voor Azure-bestands shares heeft momenteel de volgende beperkingen:
- Kan alleen worden gebruikt met lokaal redundante FileStorage-accounts.
- Alleen ondersteund voor Windows-clients. 
- Het maximum aantal kanalen is vier.
- SMB direct wordt niet ondersteund.
- Privé-eind punten voor opslag accounts worden niet ondersteund.
- Voor opslag accounts met on-premises Active Directory Domain Services (AD DS) of Azure AD DS [verificatie op basis van identiteiten](../articles/storage/files/storage-files-active-directory-overview.md) die zijn ingeschakeld voor Azure files, kunnen SMB-clients Windows Verkenner niet gebruiken voor het configureren van NTFS-machtigingen voor mappen en bestanden.
    - Gebruik het Windows [icacls](/windows-server/administration/windows-commands/icacls) -hulp programma of de [set-ACL-](/powershell/module/microsoft.powershell.security/set-acl) opdracht in plaats daarvan om machtigingen te configureren.

