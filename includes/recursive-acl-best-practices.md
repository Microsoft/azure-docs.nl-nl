---
title: bestand opnemen
description: bestand opnemen
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: c5c98d7a067107673b4dafd1897f56804085a297
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654088"
---
## <a name="best-practices"></a>Aanbevolen procedures

In deze sectie vindt u enkele best practice richt lijnen voor het recursief instellen van Acl's. 

#### <a name="handling-runtime-errors"></a>Runtime-fouten afhandelen

Een runtime-fout kan verschillende oorzaken hebben (bijvoorbeeld: een storing of een probleem met de client verbinding). Als er een runtime-fout optreedt, start u het recursieve ACL-proces opnieuw. Acl's kunnen opnieuw worden toegepast op items zonder dat dit een negatieve invloed heeft veroorzaakt. 

#### <a name="handling-permission-errors-403"></a>Afhandeling van machtigings fouten (403)

Als er een uitzonde ring voor Access Control optreedt tijdens het uitvoeren van een recursieve ACL-proces, heeft uw AD- [beveiligings-principal](/azure/role-based-access-control/overview#security-principal) mogelijk onvoldoende machtigingen om een ACL toe te passen op een of meer onderliggende items in de Directory-hiërarchie. Als er een machtigings fout optreedt, wordt het proces gestopt en wordt er een vervolg token gegeven. Los het probleem met de machtiging op en gebruik vervolgens de vervolg token om de resterende gegevensset te verwerken. De mappen en bestanden die al zijn verwerkt, hoeven niet opnieuw te worden verwerkt. U kunt er ook voor kiezen om het recursieve ACL-proces opnieuw te starten. Acl's kunnen opnieuw worden toegepast op items zonder dat dit een negatieve invloed heeft veroorzaakt. 

#### <a name="credentials"></a>Referenties 

We raden u aan een Azure AD-beveiligings-principal in te richten waaraan de rol van [BLOB-gegevens eigenaar voor opslag](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) is toegewezen in het bereik van het doel opslag account of de container. 

#### <a name="performance"></a>Prestaties 

Als u de latentie wilt beperken, raden we u aan het recursieve ACL-proces uit te voeren op een virtuele Azure-machine (VM) die zich in dezelfde regio bevindt als uw opslag account. 

#### <a name="acl-limits"></a>ACL-limieten

Het maximum aantal Acl's dat u kunt Toep assen op een map of bestand is 32 toegangs-Acl's en standaard-32-Acl's. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-access-control) voor meer informatie.