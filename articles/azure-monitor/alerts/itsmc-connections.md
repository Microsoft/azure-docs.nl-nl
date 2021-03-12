---
title: IT Service Management-connector in Azure Monitor
description: Dit artikel bevat informatie over het verbinden van uw ITSM-producten/-services met de IT Service Management-connector (ITSMC) in Azure Monitor om de ITSM-werk items centraal te controleren en te beheren.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009314"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-producten/-services verbinden met IT-servicebeheerconnector
Dit artikel bevat informatie over het configureren van de verbinding tussen uw ITSM-product/-service en de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren. Zie [overzicht](./itsmc-overview.md)voor meer informatie over ITSMC.

De volgende ITSM-producten/-services worden ondersteund. Selecteer het product om gedetailleerde informatie weer te geven over hoe u het product verbindt met ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> We stellen onze klanten van Cher well en Provance voor om de [actie webhook](./action-groups.md#webhook) te gebruiken voor Cher well en Provance eind punt als een andere oplossing voor de integratie.

## <a name="ip-ranges-for-itsm-partners-connections"></a>IP-adresbereiken voor ITSM partners-verbindingen
Als u de IP-adressen van ITSM wilt weer geven om ITSM-verbindingen van partners ITSM-hulpprogram ma's toe te staan, raden we u aan om het volledige open bare IP-bereik van de Azure-regio weer te geven waarin hun LogAnalytics-werk ruimte deel uitmaakt. [meer informatie](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Voor regio's EUS/WEU/EUS2/WUS2/VS Zuid-Centraal kan de klant alleen de ActionGroup-netwerk code vermelden.

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)
