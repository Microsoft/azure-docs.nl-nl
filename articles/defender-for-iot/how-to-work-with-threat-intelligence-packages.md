---
title: Threat Intelligence-gegevens bijwerken
description: Het gegevens pakket voor bedreigings informatie wordt geleverd met elke nieuwe versie van Defender voor IoT, of, indien nodig, tussen releases.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: a210771d99d28a0e9c15d7952d491a5e5f94e704
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521404"
---
# <a name="threat-intelligence-research-and-packages"></a>Onderzoek en pakketten voor bedreigings informatie

Beveiligings teams in micro soft hebben eigen ICS-bedreigings informatie en onderzoek naar beveiligings lekken uitgevoerd. Deze teams omvatten MSTIC (micro soft Threat Intelligence Center), DART (micro soft Detection-en Response-Team), DCU (Digital misdrijven Unit), en sectie 52 (IoT/OT/ICS Domain experts die de ICS-specifieke nul-dagen, reverse-engineering malware, campagnes en aanvallers) bijhouden.

De teams bieden beveiligings detectie,-analyse en-reactie op de website van micro soft:

- Cloud infrastructuur en-services.
- Traditionele producten en apparaten.
- Interne bedrijfs resources.

Beveiligings teams profiteren van het volgende:

- Bescherming tegen bekende en relevante bedreigingen.
- Inzichten die u helpen sorteren en prioriteiten te geven.
- Een goed idee van de volledige context van bedreigingen voordat ze worden beïnvloed.
- Meer relevante, nauw keurige en bruikbare gegevens.

Deze intelligentie voegt contextuele informatie toe om micro soft platform Analytics te verrijken en ondersteunt de beheerde services van het bedrijf voor reactie op incidenten en inbreuk onderzoek. Threat Intelligence-pakketten bevatten hand tekeningen (inclusief malware-hand tekeningen), CVEs en andere beveiligings inhoud.

U kunt pakketten downloaden vanaf de pagina **updates** in de Azure Defender voor IOT-Portal.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Down load updates via de Azure Defender voor IoT-Portal.":::

## <a name="update-threat-intelligence-data"></a>Threat Intelligence-gegevens bijwerken

Er worden bedreigings informatie pakketten geleverd bij elke nieuwe versie-update voor Defender voor IoT, of, indien nodig, tussen releases.

Pakketten die u van de Defender voor IoT-Portal downloadt, kunnen hand matig worden geüpload naar afzonderlijke Sens oren. Als de on-premises beheer console uw Sens oren beheert, kunt u bedreigings informatie pakketten downloaden naar de beheer console en deze naar meerdere Sens oren tegelijk pushen.

Een pakket bijwerken op één sensor:

1. Ga naar de pagina Azure Defender voor IoT- **updates** .

2. Down load en sla het pakket met de **bedreigings informatie** op.

3. Meld u aan bij de sensor console.

4. Selecteer **systeem instellingen** in het menu aan de zijkant.

5. Selecteer **Threat Intelligence-gegevens** en selecteer vervolgens **bijwerken**.

6. Upload het nieuwe pakket.

Een pakket op meerdere Sens oren tegelijk bijwerken:

1. Ga naar de pagina Azure Defender voor IoT- **updates** .

2. Down load en sla het pakket met de **bedreigings informatie** op.

3. Meld u aan bij de beheer console.

4. Selecteer **systeem instellingen** in het menu aan de zijkant.

5. Selecteer in de sectie **configuratie sensor engine** de Sens oren die de bijgewerkte pakketten moeten ontvangen.  

6. Selecteer in de sectie **Threat Intelligence-gegevens selecteren** het plus teken ( **+** ).

7. Upload het pakket.

## <a name="next-steps"></a>Volgende stappen

[Update versies](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
