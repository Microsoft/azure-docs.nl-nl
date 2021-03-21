---
title: Azure Defender voor Storage configureren
titleSuffix: Azure Storage
description: Configureer Azure Defender voor opslag om afwijkingen in de account activiteit te detecteren en op de hoogte te worden gesteld van mogelijk schadelijke pogingen om toegang te krijgen tot uw account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: cdfc54b1eca3b07202148b7099884a04f35939ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698141"
---
# <a name="configure-azure-defender-for-storage"></a>Azure Defender voor Storage configureren

Azure Defender for Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen tot het openen of exploiteren van opslagaccounts worden gedetecteerd. Dankzij deze beveiligingslaag kunt u bedreigingen aanpakken, ook als u geen beveiligingsexpert bent en zonder dat u beveiligingssystemen hoeft te beheren.

Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. Deze beveiligings waarschuwingen zijn geïntegreerd met [Azure Security Center](https://azure.microsoft.com/services/security-center/)en worden ook via e-mail verzonden naar abonnements beheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen.

De service neemt bron logboeken op met lees-, schrijf-en verwijder aanvragen van Blob-opslag en Azure Files voor detectie van bedreigingen. Als u waarschuwingen van Azure Defender wilt onderzoeken, kunt u gerelateerde opslag activiteiten bekijken met Opslaganalyse logboek registratie. Zie **logboek registratie configureren** in [een opslag account in het Azure Portal bewaken](./manage-storage-analytics-logs.md#configure-logging)voor meer informatie.

## <a name="availability"></a>Beschikbaarheid

Azure Defender for Storage is momenteel beschikbaar voor Blob Storage, Azure Files en Azure Data Lake Storage Gen2. Accounttypen die ondersteuning bieden voor Azure Defender zijn: GPv2, blok-blob en Blob Storage-accounts. Azure Defender for Storage is beschikbaar in alle openbare clouds en Amerikaanse overheidsclouds, maar niet in andere soevereine cloudregio's of Azure Government-cloudregio's.

Accounts met hiërarchische naamruimten die zijn ingeschakeld voor Data Lake Storage-ondersteuningstransacties met behulp van de Azure Blob Storage-API's en de Data Lake Storage-API's. Azure-bestandsshares ondersteunen transacties via SMB.

Voor prijs informatie, inclusief een gratis proef versie van 30 dagen, raadpleegt u de [pagina met Azure Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/).

De volgende lijst bevat een overzicht van de beschik baarheid van Azure Defender voor opslag:

- Releasestatus:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (algemene Beschik baarheid)
  - [Azure files](../files/storage-files-introduction.md) (algemene Beschik baarheid)
  - Azure Data Lake Storage Gen2 (algemene Beschik baarheid)
- Clouds:<br>
    ✔ Commerciële Clouds<br>
    ✔ US Gov<br>
    ✘ China gov, andere gov

## <a name="set-up-azure-defender"></a>Azure Defender instellen

U kunt Azure Defender voor opslag op verschillende manieren configureren, zoals beschreven in de volgende secties.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Wanneer u zich abonneert op de Standard-laag in Azure Security Center, wordt Azure Defender automatisch ingesteld op al uw opslag accounts. U kunt Azure Defender voor uw opslag accounts onder een specifiek abonnement als volgt in-of uitschakelen:

1. Start **Azure Security Center** in het [Azure Portal](https://portal.azure.com).
1. Selecteer in het hoofd menu onder **beheer** de optie **prijzen & instellingen**.
1. Selecteer het abonnement waarvoor u Azure Defender wilt in-of uitschakelen.
1. Selecteer **Azure Defender op** om Azure Defender in te scha kelen voor het abonnement.
1. Ga naar het **resource type Azure Defender-plan selecteren**, zoek de rij **Storage** en selecteer **ingeschakeld** in de kolom **plan** .
1. Sla uw wijzigingen op.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

Azure Defender is nu ingeschakeld voor alle opslag accounts in dit abonnement.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Start de [Azure Portal](https://portal.azure.com/).
1. Ga naar uw opslagaccount. Selecteer onder **Instellingen** de optie **Geavanceerde beveiliging**.
1. Selecteer **Azure Defender for Storage inschakelen**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor een Azure Storage-account":::

Azure Defender is nu ingeschakeld voor dit opslag account.

### <a name="template"></a>[Sjabloon](#tab/template)

Gebruik een Azure Resource Manager sjabloon om een Azure Storage account te implementeren waarop Azure Defender is ingeschakeld. Zie [opslag account met geavanceerde beveiliging tegen bedreigingen](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)voor meer informatie.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Gebruik een Azure Policy om Azure Defender in te scha kelen voor opslag accounts onder een specifiek abonnement of resource groep.

1. Start de pagina Azure **Policy-Definitions** .
1. Zoek het beleid **Azure Defender implementeren op opslag accounts** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Beleid Toep assen om Azure Defender voor opslag accounts in te scha kelen":::

1. Selecteer een Azure-abonnement of resource groep.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Abonnement of resource groep selecteren voor het bereik van beleid ":::

1. Wijs het beleid toe.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Beleid toewijzen om Azure Defender voor opslag in te scha kelen":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Gebruik rest API-opdrachten om de Azure Defender-instelling voor een specifiek opslag account te maken, bij te werken of op te halen.

- [Advanced Threat Protection-maken](/rest/api/securitycenter/advancedthreatprotection/create)
- [Advanced Threat Protection-ophalen](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende Power shell-cmdlets:

- [Advanced Threat Protection inschakelen](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Geavanceerde beveiliging tegen bedreigingen verkrijgen](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Geavanceerde bedreigings beveiliging uitschakelen](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Beveiligingsafwijkingen verkennen

Wanneer er afwijkingen optreden bij opslagactiviteiten, ontvangt u een e-mailmelding met informatie over de verdachte beveiligingsgebeurtenis. Details van de gebeurtenis zijn onder andere:

- De aard van de afwijking
- Naam van het opslagaccount
- Het tijdstip van gebeurtenis
- Het opslagtype
- De mogelijke oorzaken
- De onderzoeksstappen
- De herstelstappen

Het e-mailbericht bevat ook informatie over mogelijke oorzaken en aanbevolen acties voor het onderzoeken en tegengaan van de mogelijke bedreiging.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="E-mail waarschuwings bericht van Azure Defender voor opslag":::

U kunt uw huidige beveiligings waarschuwingen controleren en beheren via de [tegel beveiligings waarschuwingen](../../security-center/security-center-managing-and-responding-alerts.md)van Azure Security Center. Als u op een specifieke waarschuwing klikt, krijgt u details en acties te zien voor het onderzoeken van de huidige dreiging en voor het aanpakken van toekomstige bedreigingen.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Waarschuwing voor Azure Defender voor opslag":::

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om opslag accounts te openen of misbruik te maken. Zie [waarschuwingen voor Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage)voor een lijst met waarschuwingen voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Logboeken in azure Storage-accounts](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Meer informatie over [Azure Security Center](../../security-center/security-center-introduction.md)