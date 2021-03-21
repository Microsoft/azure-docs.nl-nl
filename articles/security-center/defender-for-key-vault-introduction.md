---
title: Azure Defender voor Key Vault - de voordelen en functies
description: Meer informatie over de voordelen en functies van Azure Defender voor Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: f127a24bec5567a3868ae77cb8a52f0af2a19603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100675"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Inleiding tot Azure Defender voor Key Vault

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. 

Schakel **Azure Defender voor Key Vault** in voor geavanceerde Azure-beveiliging tegen Azure Key Vault-bedreigingen, zodat u een extra laag beveiligingsinformatie kunt leveren. 

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|**Azure Defender voor Key Vault** wordt gefactureerd zoals wordt weer gegeven op [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Wat zijn de voordelen van Azure Defender voor Key Vault?

Met Azure Defender worden ongebruikelijke en mogelijk schadelijke pogingen om Key Vault-accounts te openen of misbruiken, gedetecteerd. Deze beschermingslaag stelt u in staat om bedreigingen te verhelpen zonder dat u een beveiligingsexpert hoeft te zijn en zonder dat u externe beveiligingsbewakingssystemen hoeft te beheren.  

Als er afwijkende activiteiten optreden, toont Azure Defender waarschuwingen en mailt Azure Defender deze waarschuwingen optioneel naar de relevante leden van uw organisatie. Deze waarschuwingen bevatten de details over het verdachte incident evenals aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure Defender voor Key Vault-waarschuwingen
Wanneer u een waarschuwing ontvangt van Azure Defender voor Key Vault, raden we u aan de waarschuwing te onderzoeken en erop te reageren, zoals wordt beschreven in [Reageren op Azure Defender voor Key Vault](defender-for-key-vault-usage.md). Azure Defender voor Key Vault beveiligt toepassingen en referenties, dus zelfs als u vertrouwd bent met de toepassing of gebruiker die de waarschuwing heeft geactiveerd, is het belangrijk dat u de situatie rondom elke waarschuwing controleert.

De waarschuwingen worden weergegeven op de pagina **Beveiliging** van Key Vault, op het dashboard van Azure Defender en op de waarschuwingspagina van Security Center.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Beveiligingspagina van Azure Key Vault":::


> [!TIP]
> U kunt Azure Defender voor Key Vault-waarschuwingen simuleren door de instructies te volgen in [Validating Azure Key Vault threat detection in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336) (Azure Key Vault-bedreigingsdetectie valideren in Azure Security Center).


## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender voor Key Vault.

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- [Key Vault-beveiligingswaarschuwingen](alerts-reference.md#alerts-azurekv)--Het Key Vault-gedeelte van de referentietabel voor alle Azure Security Center-waarschuwingen
- [Security Center-gegevens continu exporteren](continuous-export.md)
- [Waarschuwingen van Azure Defender onderdrukken](alerts-suppression-rules.md)