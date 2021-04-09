---
title: Azure Firewall Premium preview in het Azure Portal
description: Meer informatie over Azure Firewall Premium preview in de Azure Portal.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549699"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure Firewall Premium preview in het Azure Portal

> [!IMPORTANT]
> Azure Firewall Premium is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

 Azure Firewall Premium preview is een firewall van de volgende generatie met mogelijkheden die vereist zijn voor zeer gevoelige en gereglementeerde omgevingen. Het bevat de volgende functies:

- **TLS-inspectie** -Hiermee wordt het uitgaande verkeer gedecodeerd, worden de gegevens verwerkt, worden de gegevens versleuteld en verzonden naar de bestemming.
- **Id** : een netwerk aanval en preventie systeem (id) biedt u de mogelijkheid om netwerk activiteiten te bewaken voor schadelijke activiteiten, informatie over deze activiteit te registreren, deze te rapporteren en optioneel te proberen om deze te blok keren.
- **URL-filtering** : Hiermee wordt de FQDN-filter functie van Azure firewall uitgebreid om een volledige URL te bekijken. Bijvoorbeeld `www.contoso.com/a/c` in plaats van `www.contoso.com` .
- **Webcategorieën** : beheerders kunnen gebruikers toegang tot website categorieën toestaan of weigeren, zoals gokken websites, websites voor sociale media en anderen.

Zie [Azure Firewall Premium-functies](premium-features.md)voor meer informatie.

## <a name="deploy-the-firewall"></a>De firewall implementeren

Het implementeren van een preview van Azure Firewall Premium is vergelijkbaar met het implementeren van een standaard-Azure Firewall:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="Portal-implementatie":::

Voor de **firewall laag** selecteert u **Premium (preview)** en voor het **firewall beleid**, selecteert u een bestaand Premium-beleid of maakt u een nieuwe.

## <a name="configure-the-premium-policy"></a>Het Premium-beleid configureren

Het configureren van een Premium-firewall beleid is vergelijkbaar met het configureren van een standaard firewall beleid. Met een Premium-beleid kunt u de Premium-functies configureren:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Premium-beleids implementatie":::

### <a name="rule-configuration"></a>Regel configuratie

Wanneer u toepassings regels in een Premium-beleid configureert, kunt u extra Premium-functies configureren:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Premium-regel":::

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Firewall Premium preview implementeren en configureren](premium-deploy.md)voor meer informatie over de preview-functies van Azure Firewall Premium in actie.