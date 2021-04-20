---
title: Resource Manager sjabloonvoorbeelden voor SQL-inzichten
description: Voorbeeld van Azure Resource Manager voor het implementeren en configureren van SQL-inzichten.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730523"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Resource Manager sjabloonvoorbeelden voor SQL-inzichten
Dit artikel bevat voorbeeldsjablonen [Azure Resource Manager sql-inzichten](../../azure-resource-manager/templates/template-syntax.md) in te stellen voor het bewaken van SQL die wordt uitgevoerd in Azure.  Zie de [documentatie voor SQL-inzichten](sql-insights-overview.md) voor meer informatie over de aanbieding en versies van SQL die we ondersteunen. Elk voorbeeld bevat een sjabloonbestand en een parameterbestand met voorbeeldwaarden voor het sjabloon.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>Een sql insights-bewakingsprofiel maken
In het volgende voorbeeld wordt een sql insights-bewakingsprofiel gemaakt, waaronder de SQL-bewakingsgegevens die moeten worden verzameld, de frequentie waarmee gegevens worden verzameld en de werkruimte waar de gegevens naar worden verzonden.


### <a name="template-file"></a>Sjabloonbestand

Bekijk het [sjabloonbestand op git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate).

### <a name="parameter-file"></a>Parameterbestand

Bekijk het [parameterbestand op git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json).


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Een bewakings-VM toevoegen aan een SQL Insights-bewakingsprofiel
Zodra u een bewakingsprofiel hebt gemaakt, moet u virtuele Azure-machines toewijzen die worden geconfigureerd om op afstand gegevens te verzamelen van de SQL-resources die u opgeeft in de configuratie voor die VM.  Raadpleeg de documentatie over het inschakelen van SQL-inzichten voor meer informatie.

In het volgende voorbeeld wordt een bewakings-VM geconfigureerd voor het verzamelen van de gegevens van de opgegeven SQL-resources.


### <a name="template-file"></a>Sjabloonbestand

Bekijk het [sjabloonbestand op git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate).

### <a name="parameter-file"></a>Parameterbestand

Bekijk het [parameterbestand op git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json).


## <a name="create-an-alert-rule-for-sql-insights"></a>Een waarschuwingsregel maken voor SQL-inzichten
In het volgende voorbeeld wordt een waarschuwingsregel gemaakt die betrekking heeft op de SQL-resources binnen het bereik van het opgegeven bewakingsprofiel.  Deze waarschuwingsregel wordt weergegeven in de gebruikersinterface van SQL-inzichten in het contextvenster van de gebruikersinterface voor waarschuwingen.  

Het parameterbestand bevat waarden uit een van de waarschuwingssjablonen die we in SQL-inzichten bieden. U kunt dit wijzigen om een waarschuwing te ontvangen over andere gegevens die we voor SQL verzamelen.  De sjabloon geeft geen actiegroep op voor de waarschuwingsregel.


#### <a name="template-file"></a>Sjabloonbestand

Bekijk het [sjabloonbestand op git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate).

### <a name="parameter-file"></a>Parameterbestand

Bekijk het [parameterbestand op git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json).





## <a name="next-steps"></a>Volgende stappen

* [Meer voorbeeldsjablonen voor Azure Monitor](../resource-manager-samples.md).
* [Meer informatie over SQL-inzichten.](sql-insights-overview.md)
