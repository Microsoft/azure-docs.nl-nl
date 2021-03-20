---
title: Problemen met automatisch schalen oplossen met Virtual Machine Scale Sets
description: Problemen met automatisch schalen oplossen met Virtual Machine Scale Sets. Krijg inzicht in veelvoorkomende problemen en hoe u deze kunt oplossen.
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 11302c301bee466f678d544d0c4838c39cec9c8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91818545"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Problemen met automatisch schalen met schaalsets voor virtuele machines oplossen
**Probleem** : u hebt een infra structuur voor automatisch schalen gemaakt in azure Resource Manager met schaal sets voor virtuele machines, bijvoorbeeld door het implementeren van een sjabloon zoals deze:: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale  u hebt uw schaal regels gedefinieerd en werkt goed, behalve de hoeveelheid belasting die u op de virtuele machines plaatst, wordt niet automatisch geschaald.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
Hieronder vindt u enkele dingen die u moet overwegen:

* Hoeveel Vcpu's heeft elke VM en wordt u elke vCPU geladen?
  Het voor gaande voor beeld van Azure Quick Start-sjabloon bevat een do_work. php-script, waarmee één vCPU wordt geladen. Als u een virtuele machine gebruikt die groter is dan een VM-grootte van één vCPU, zoals Standard_A1 of D1, moet u deze belasting meerdere keren uitvoeren. Het aantal Vcpu's voor uw Vm's controleren door grootten te controleren op [virtuele Windows-machines in azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Hoeveel Vm's in de schaalset voor virtuele machines voert u uit op elk gewenst werk schema?
  
    Een uitzonderings gebeurtenis treedt alleen op wanneer de gemiddelde CPU voor **alle** virtuele machines in een schaalset de drempel waarde overschrijdt, in de tijd die is gedefinieerd in de regels voor automatisch schalen.
* Hebt u een schaal gebeurtenis gemist?
  
    Controleer de controle Logboeken in de Azure Portal op Scale-gebeurtenissen. Misschien is er een omhoog geschaald en wordt er een geschaald die is gemist. U kunt filteren op schaal.
  
    ![Auditlogboeken][audit]
* Zijn de drempel waarden voor inzoomen en uitschalen voldoende voor u?
  
    Stel dat u een regel hebt ingesteld die moet worden uitgeschaald wanneer de gemiddelde CPU groter is dan 50% gedurende vijf minuten, en moet worden geschaald wanneer de gemiddelde CPU kleiner is dan 50%. Met deze instelling wordt het probleem ' gaat en neer ' veroorzaakt wanneer het CPU-gebruik zich in de buurt van de drempel bevindt, waarbij schaal acties voortdurend toenemen en de grootte van de set verlagen. Als gevolg van deze instelling probeert de service voor automatisch schalen ' gaat en neer ' te voor komen, wat kan worden manifesteert als niet geschaald. Zorg er daarom voor dat uw drempel waarden voor uitschalen en schalen voldoende verschillen om ruimte te maken tussen schalen.
* Hebt u uw eigen JSON-sjabloon geschreven?
  
    Het is eenvoudig om fouten te maken, dus begin met een sjabloon zoals de hierboven beschreven die u kunt gebruiken, en breng kleine incrementele wijzigingen aan. 
* Kunt u hand matig in-of uitschalen?
  
    Probeer de resource van de virtuele-machine schaalset opnieuw te implementeren met een andere capaciteits instelling om het aantal Vm's hand matig te wijzigen. Hier volgt een voorbeeld sjabloon: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – u moet mogelijk de sjabloon bewerken om er zeker van te zijn dat deze dezelfde computer grootte heeft als uw schaalset gebruikt. Als u het aantal Vm's hand matig kunt wijzigen, weet u dan dat het probleem is geïsoleerd voor automatisch schalen.
* Controleer uw micro soft. Compute/virtualMachineScaleSet-en micro soft. Insights-resources in de [Azure resource Explorer](https://resources.azure.com/)
  
    De Azure Resource Explorer is een onmisbaar hulp programma voor probleem oplossing waarmee u de status van uw Azure Resource Manager resources kunt zien. Klik op uw abonnement en Bekijk de resource groep die u wilt oplossen. Bekijk de virtuele-machine schaalset die u hebt gemaakt in de compute-resource provider en controleer de weer gave van de instantie. hier ziet u de status van een implementatie. Controleer ook de weer gave van de instantie van Vm's in de schaalset voor virtuele machines. Ga vervolgens naar de resource provider micro soft. Insights en controleer of de regels voor automatisch schalen naar rechts zoeken.
* Werkt de diagnostische uitbrei ding en worden prestatie gegevens verzonden?
  
    **Update:** Automatisch schalen van Azure is verbeterd voor het gebruik van een op een host gebaseerde metrische pijp lijn, waarvoor geen diagnostische uitbrei dingen meer nodig zijn om te worden geïnstalleerd. De volgende alinea's zijn niet meer van toepassing als u een toepassing voor automatisch schalen maakt met behulp van de nieuwe pijp lijn. Hier vindt u een voor beeld van Azure-sjablonen die zijn geconverteerd om de host-pijp lijn te gebruiken: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale . 
  
    Het gebruik van metrische gegevens op basis van een host voor automatisch schalen is beter om de volgende redenen:
  
  * U hoeft minder onderdelen te verplaatsen omdat er geen diagnostische uitbrei dingen moeten worden geïnstalleerd.
  * Eenvoudigere sjablonen. Voeg regels voor het automatisch schalen van inzichten toe aan een bestaande sjabloon voor schaal sets.
  * Betrouwbaardere rapportage en snellere Lance ring van nieuwe Vm's.
    
    Het is raadzaam om alleen een diagnostische uitbrei ding te gebruiken als u wilt rapporteren over geheugen diagnoses of schalen. Bij metrische gegevens van een host wordt geen geheugen gerapporteerd.
    
    In dat geval volgt u alleen de rest van dit artikel als u diagnostische uitbrei dingen gebruikt voor automatisch schalen.
    
    Automatisch schalen in Azure Resource Manager kan werken (maar niet meer nodig) met behulp van een VM-extensie die de uitbrei ding voor diagnostische gegevens wordt genoemd. Het verzendt prestatie gegevens naar een opslag account dat u in de sjabloon definieert. Deze gegevens worden vervolgens geaggregeerd door de Azure Monitor-service.
    
    Als de Insights-service geen gegevens kan lezen van de virtuele machines, moet u een e-mail verzenden. U ontvangt bijvoorbeeld een e-mail bericht als de virtuele machines niet beschikbaar zijn. Controleer het e-mail adres dat u hebt opgegeven bij het maken van uw Azure-account.
    
    U kunt de gegevens ook zelf bekijken. Bekijk het Azure-opslag account met behulp van een Cloud Explorer. Als u bijvoorbeeld [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)gebruikt, meldt u zich aan en kiest u het Azure-abonnement dat u gebruikt. Bekijk vervolgens de naam van het opslag account voor diagnostische gegevens waarnaar wordt verwezen in de definitie van de diagnostische gegevens in uw implementatie sjabloon.
    
    ![Cloud Explorer][explorer]
    
    U ziet een aantal tabellen waarin de gegevens van elke virtuele machine worden opgeslagen. Als u Linux en de CPU-metric als voor beeld neemt, bekijkt u de meest recente rijen. Visual Studio Cloud Explorer ondersteunt een query taal, zodat u een query kunt uitvoeren. U kunt bijvoorbeeld een query uitvoeren voor ' time stamp gt datetime ' 2016-02-02T21:20:00Z ' ' om er zeker van te zijn dat u de meest recente gebeurtenissen krijgt. De tijd zone komt overeen met UTC. Worden de gegevens die u ziet in de weer gegeven, overeenkomen met de schaal regels die u instelt? In het volgende voor beeld is de CPU voor machine 20 in de afgelopen vijf minuten verhoogd naar 100%.
    
    ![Opslag tabellen][tables]
    
    Als er geen gegevens zijn, wordt het probleem veroorzaakt door de diagnostische uitbrei ding die in de Vm's wordt uitgevoerd. Als de gegevens daar zich bevinden, is er een probleem met uw schaal regels of met de Insights-service. Controleer de [status van Azure](https://azure.microsoft.com/status/).
    
    Als u deze stappen hebt uitgevoerd, kunt u, als u nog steeds problemen hebt met automatisch schalen, de volgende resources proberen: 
    * Lees de forums op [micro soft Q&een vraag pagina](/answers/topics/azure-virtual-machines.html)of [stack overflow](https://stackoverflow.com/questions/tagged/azure) 
    * Een ondersteunings oproep registreren. Bereid u voor op het delen van de sjabloon en een weer gave van uw prestatie gegevens.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
