---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83673761"
---
### <a name="identity-tier"></a>Identiteits categorie 

Met het Microsoft-Oracle partnerschap kunt u een uniforme identiteit instellen in azure, OCI en uw Oracle-toepassing. Voor JD Edwards EnterpriseOne of People Soft Application Suite is een exemplaar van de Oracle HTTP-server (OHS) nodig om eenmalige aanmelding in te stellen tussen Azure AD en Oracle IDCS.

OHS fungeert als een omgekeerde proxy voor de toepassingslaag, wat betekent dat alle aanvragen voor de eind toepassingen deze door lopen. Oracle Access Manager webgate is een OHS web server-invoeg toepassing die elke aanvraag onderschept die naar de eind toepassing gaat. Als een bron waartoe toegang wordt verkregen, is beveiligd (hiervoor is een geauthenticeerde sessie vereist), start de webgate de OIDC-verificatie stroom met de identiteits-Cloud service via de browser van de gebruiker. Zie de [documentatie van Oracle Access Manager](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327)voor meer informatie over de stromen die worden ondersteund door de OpenID Connect Connect-webgate.

Met deze instelling kan een gebruiker die zich al bij Azure AD heeft aangemeld, naar de JD Edwards EnterpriseOne of People Soft-toepassing navigeren zonder zich opnieuw aan te melden via de Oracle Identity Cloud service. Klanten die deze oplossing implementeren, profiteren van de voor delen van eenmalige aanmelding, met inbegrip van één set referenties, een verbeterde aanmeldings ervaring, verbeterde beveiliging en gereduceerde kosten voor Help Desk.

Zie het bijbehorende [Oracle-technisch](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)document voor meer informatie over het instellen van eenmalige aanmelding voor JD Edwards EnterpriseOne of People Soft met Azure AD.