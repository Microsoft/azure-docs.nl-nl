---
title: Azure HANA-besturings elementen voor grote instanties via Azure Portal | Microsoft Docs
description: Hierin wordt beschreven hoe u met Azure HANA grote instanties kunt identificeren en gebruiken via de portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36f6638614d5c0b55b2adc202e8f8d6f9450aaf3
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674409"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Beheer van grote Azure HANA-exemplaren via de Azure-portal

>[!NOTE]
>Volg de instructies in de [BareMetal-instanties beheren via het onderwerp Azure Portal](baremetal-infrastructure-portal.md) voor Rev 4,2.

In dit document wordt beschreven hoe [Hana grote instanties](./hana-overview-architecture.md) worden weer gegeven in [Azure Portal](https://portal.azure.com) en welke activiteiten kunnen worden uitgevoerd via Azure Portal met Hana grote instantie-eenheden die voor u zijn geïmplementeerd. Zicht baarheid van HANA grote instanties in Azure Portal wordt gegeven via een Azure-resource provider voor HANA grote instanties, die momenteel beschikbaar zijn in de open bare preview

## <a name="register-hana-large-instance-resource-provider"></a>Bron provider van HANA-grote instanties registreren
Normaal gesp roken is uw Azure-abonnement dat u gebruikt voor HANA-implementaties met grote instanties geregistreerd voor de bron provider van de HANA-grote instantie. Als u echter niet ziet dat u de geïmplementeerde omvang rijke HANA-exemplaar-eenheden kunt zien, moet u de resource provider registreren in uw Azure-abonnement. Er zijn twee manieren om de bron provider voor de HANA grote instanties te registreren

### <a name="register-through-cli-interface"></a>Registreren via CLI-interface
U moet zijn aangemeld bij uw Azure-abonnement, dat wordt gebruikt voor de implementatie van HANA grote instanties via de Azure CLI-interface. U kunt (opnieuw) de HANA-provider voor grote instanties registreren met deze opdracht:
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

Zie het artikel [Azure-resource providers en-typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) voor meer informatie.


### <a name="register-through-azure-portal"></a>Registreren via Azure Portal
U kunt (opnieuw) de bron van de HANA-resource voor grote instanties registreren via Azure Portal. U moet uw abonnement in Azure Portal vermelden en dubbel klikken op het abonnement dat is gebruikt voor het implementeren van uw HANA-grote exemplaar-eenheid ('s). U bevindt zich op de pagina overzicht van uw abonnement, selecteer ' resource providers ' zoals hieronder wordt weer gegeven en typ ' HANA ' in het venster zoeken. 

![HLI RP registreren via Azure Portal](./media/hana-li-portal/portal-register-hli-rp.png)

De resource provider is al geregistreerd in de scherm opname die wordt weer gegeven. Als de resource provider nog niet is geregistreerd, klikt u op "opnieuw registreren" of "registreren".

Zie het artikel [Azure-resource providers en-typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) voor meer informatie.


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Weer gave van HANA grote instantie-eenheden in de Azure Portal
Wanneer u een HANA-implementatie aanvraag voor een grote instantie verzendt, wordt u gevraagd om het Azure-abonnement op te geven dat u wilt verbinden met de grote omvang van de HANA-instanties. Het wordt aanbevolen om hetzelfde abonnement te gebruiken dat u gebruikt voor het implementeren van de SAP-toepassingslaag die werkt op basis van de HANA grote instantie-eenheden.
Als uw eerste HANA grote instanties worden geïmplementeerd, wordt een nieuwe [Azure-resource groep](../../../azure-resource-manager/management/manage-resources-portal.md) gemaakt in het Azure-abonnement dat u hebt verzonden in de implementatie aanvraag voor uw Hana grote instanties.  De nieuwe resource groep bevat alle HANA grote instantie-eenheden die u hebt geïmplementeerd in het specifieke abonnement.

Als u de nieuwe Azure-resource groep wilt zoeken, vermeldt u de resource groep in uw abonnement door te navigeren via het navigatie deel venster links van de Azure Portal

![Scherm opname van de optie Resource groepen.](./media/hana-li-portal/portal-resource-group.png)

In de lijst met resource groepen wordt weer gegeven, moet u mogelijk filteren op het abonnement dat u hebt gebruikt om HANA grote instanties te implementeren

![Resource groepen filteren in Azure Portal](./media/hana-li-portal/portal-filtering-subscription.png)

Nadat u het juiste abonnement hebt gefilterd, kunt u nog steeds een lange lijst met resource groepen hebben. Zoek er een met een **TXXX** waarbij ' xxx ' drie cijfers is, zoals **-T050**. 

Als u de resource groep hebt gevonden, vermeldt u de details ervan. De lijst die u hebt ontvangen, kan er als volgt uitzien:

![De lijst van HLI in Azure Portal](./media/hana-li-portal/portal-hli-units-list.png)

Alle vermelde eenheden vertegenwoordigen een enkele HANA grote instantie-eenheid die is geïmplementeerd in uw abonnement. In dit geval kijkt u naar acht verschillende HANA grote instantie-eenheden die zijn geïmplementeerd in uw abonnement.

Als u verschillende HANA-tenants met grote instanties hebt geïmplementeerd onder hetzelfde Azure-abonnement, vindt u meerdere Azure-resource groepen 


## <a name="look-at-attributes-of-single-hli-unit"></a>De kenmerken van één exemplaar van de HLI bekijken
In de lijst met de HANA-eenheden voor grote instanties kunt u op één eenheid klikken en de details van de single HANA-eenheid voor grote instanties ophalen. 

Nadat u op weer geven hebt geklikt, krijgt u in het scherm overzicht een presentatie van de eenheid, die er als volgt uitziet:

![Overzicht van een HLI-eenheid weer geven](./media/hana-li-portal/portal-show-overview.png)

Op de verschillende kenmerken die worden weer gegeven, zien deze kenmerken er nauwelijks anders uit dan Azure VM-kenmerken. Op de kop van de linkerkant ziet u de resource groep, de Azure-regio, de abonnements naam en de ID, evenals een aantal tags die u hebt toegevoegd. Standaard heeft de HANA-eenheden voor grote instanties geen tag toegewezen. Aan de rechter kant van de kop wordt de naam van de eenheid vermeld als toegewezen wanneer de implementatie is uitgevoerd. Het besturings systeem wordt weer gegeven, evenals het IP-adres. Net als bij virtuele machines wordt het type HANA-eenheid voor grote instanties met het aantal CPU-threads en het geheugen ook weer gegeven. Meer details over de verschillende HANA-eenheden voor grote instanties worden hier weer gegeven:

- [Beschikbare SKU's voor HLI](./hana-available-skus.md)
- [Opslag architectuur van SAP HANA (grote exemplaren)](./hana-storage-architecture.md) 

Extra gegevens aan de rechter kant is de revisie van de HANA-stempel met grote instanties. Mogelijke waarden zijn:

- Revisie 3
- Revisie 4

Revisie 4 is de nieuwste architectuur van HANA grote instanties met grote verbeteringen in de netwerk latentie tussen virtuele Azure-machines en HANA grote instantie-eenheden die zijn geïmplementeerd in revisie 4 stem pels of rijen.
In de rechter benedenhoek van het overzicht wordt een andere zeer belang rijke informatie gevonden met de naam van de Azure proximity-plaatsings groep die automatisch wordt gemaakt voor elke geïmplementeerde waarde van de grote instantie van HANA. Er moet naar deze plaatsings groep worden verwezen bij het implementeren van de Azure-Vm's die als host fungeren voor de SAP-toepassingslaag. Door gebruik te maken van de [Azure proximity-plaatsings groep](../../linux/co-location.md) die is gekoppeld aan de Hana-eenheid voor grote instanties, zorgt u ervoor dat de virtuele Azure-machines dicht bij elkaar liggen op de Hana grote instantie-eenheid. Hoe proximity-plaatsings groepen kunnen worden gebruikt om de SAP-toepassingslaag te vinden in hetzelfde Azure-Data Center als revisie 4 gehoste HANA grote instantie-eenheden, worden beschreven in [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).

Een aanvullend veld in de rechter kolom van de koptekst geeft informatie over de energie status van de HANA grote instantie-eenheid.

> [!NOTE]
> De energie status geeft aan of de hardware-eenheid is ingeschakeld of uitgeschakeld. Het geeft geen informatie over het besturings systeem dat actief is. Wanneer u een HANA grote instantie-eenheid opnieuw opstart, ondervindt u een korte periode waarin de status van de eenheid wordt **gewijzigd in de** status **gestart**. Als de status **gestart** is, betekent dat het besturings systeem wordt gestart of dat het besturings systeem volledig is gestart. Als gevolg hiervan kunt u na het opnieuw opstarten van de eenheid niet verwachten dat u zich onmiddellijk aanmeldt bij de eenheid zodra de status overschakelt naar **gestart**.
> 

Als u op ' meer weer geven ' drukt, wordt extra informatie weer gegeven. Een extra informatie geeft de revisie van de HANA-stempel van de grote instantie weer, de eenheid die is geïmplementeerd in. Zie het artikel [Wat is SAP Hana op Azure (grote exemplaren)](./hana-overview-architecture.md) voor de verschillende revisies van Hana grote instantie tempels

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Activiteiten van één HANA grote instantie-eenheid controleren 
Meer dan een overzicht van de HANA-eenheden voor grote instanties kunt u de activiteiten van de desbetreffende eenheid controleren. Een activiteiten logboek kan er als volgt uitzien:

![Navigatie deel venster in Azure Portal](./media/hana-li-portal/portal-activity-list.png)

Een van de geregistreerde hoofd activiteiten wordt opnieuw gestart van een eenheid. De weer gegeven gegevens zijn inclusief de status van de activiteit, de tijds tempel die de activiteit heeft geactiveerd, de abonnements-ID waaruit de activiteit is geactiveerd en de Azure-gebruiker die de activiteit heeft geactiveerd. 

Een andere activiteit die wordt opgenomen, is gewijzigd in de eenheid in de meta gegevens van Azure. Naast het opnieuw opstarten, ziet u de activiteit van **Write HANAInstances**. Met dit type activiteit worden geen wijzigingen aangebracht in de HANA-grote exemplaar-eenheid zelf, maar worden wijzigingen in de meta gegevens van de eenheid in azure gedocumenteerd. In de lijst wordt een tag toegevoegd en verwijderd (Zie de volgende sectie).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Een Azure-tag toevoegen en verwijderen in een HANA-eenheid voor grote instanties
Een andere mogelijkheid is dat u een [tag](../../../azure-resource-manager/management/tag-resources.md) toevoegt aan een Hana-eenheid voor grote instanties. De manier waarop Tags worden toegewezen, verschilt niet van het toewijzen van tags aan Vm's. Net als bij Vm's zijn de Tags aanwezig in de meta gegevens van Azure en, voor HANA grote instanties, dezelfde beperkingen als labels voor Vm's.

Het verwijderen van Tags werkt op dezelfde manier als met virtuele machines. Beide activiteiten, Toep assen en verwijderen van een tag worden vermeld in het activiteiten logboek van de bepaalde HANA grote instantie-eenheid.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Eigenschappen van een HANA-eenheid voor grote instanties controleren
De sectie- **Eigenschappen** bevatten belang rijke informatie die u krijgt wanneer de instanties aan u worden door gegeven. Het is een sectie waar u alle informatie krijgt die u nodig hebt in ondersteunings gevallen of die u nodig hebt bij het instellen van de configuratie van de opslag momentopname. Dit gedeelte is een verzameling gegevens rond uw exemplaar, de connectiviteit van het exemplaar naar Azure en de back-end van de opslag. De bovenkant van de sectie ziet er als volgt uit:


![bovenste deel van de HLI-eigenschappen in Azure Portal](./media/hana-li-portal/portal-properties-top.png)

De eerste paar gegevens items, u hebt al in het overzichts scherm gezien. Maar een belang rijk deel van de gegevens is de ExpressRoute circuit-ID, die u hebt gekregen toen de eerste geïmplementeerde eenheden werden ontvangen. In sommige ondersteunings gevallen wordt u mogelijk gevraagd om die gegevens. Onder aan de scherm opname wordt een belang rijke gegevens invoer weer gegeven. De weer gegeven gegevens zijn het IP-adres van de NFS-opslaglaag waarmee uw opslag wordt geïsoleerd in uw **Tenant** in de Hana-verzameling met grote exemplaren. Dit IP-adres is ook nodig wanneer u het [configuratie bestand bewerkt voor back-ups van opslag momentopnamen](./hana-backup-restore.md#set-up-storage-snapshots). 

Terwijl u omlaag schuift in het eigenschappen venster, ontvangt u aanvullende gegevens, zoals een unieke Resource-ID voor uw HANA-grote exemplaar-eenheid of de abonnements-ID die is toegewezen aan de implementatie.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Een HANA-grote exemplaar-eenheid opnieuw opstarten via Azure Portal
Het Linux-besturings systeem moet opnieuw worden opgestart. er zijn verschillende situaties waarin het niet lukt om de computer opnieuw op te starten. Als u een herstart wilt forceren, moet u een service aanvraag openen zodat micro soft-bewerkingen een energie herstart van de HANA-grote exemplaar-eenheid uitvoeren. De functionaliteit van een energie herstart van een HANA-grote exemplaar-eenheid is nu geïntegreerd in de Azure Portal. Zoals u in het overzichts deel van de HANA-eenheid voor grote instanties bevindt, ziet u de knop voor opnieuw opstarten boven op de sectie gegevens

![Stap #1 opnieuw starten in Azure Portal](./media/hana-li-portal/portal-restart-first-step.png)

Wanneer u op de knop opnieuw opstarten drukt, wordt u gevraagd of u de eenheid echt opnieuw wilt opstarten. Als u bevestigt door op de knop Ja te drukken, wordt de eenheid opnieuw opgestart.

> [!NOTE]
> Tijdens het opnieuw opstarten van de eenheid duurt het even voordat de status van de unit wordt **gewijzigd in de** status **gestart**. Als de status **gestart** is, betekent dat het besturings systeem wordt gestart of dat het besturings systeem volledig is gestart. Als gevolg hiervan kunt u na het opnieuw opstarten van de eenheid niet verwachten dat u zich onmiddellijk aanmeldt bij de eenheid zodra de status overschakelt naar **gestart**.

> [!IMPORTANT]
> Afhankelijk van de hoeveelheid geheugen in uw HANA-eenheid voor grote instanties, kan het opnieuw opstarten en opnieuw opstarten van de hardware en het besturings systeem een uur duren


## <a name="open-a-support-request-for-hana-large-instances"></a>Open een ondersteunings aanvraag voor HANA grote instanties
Uit de Azure Portal weer gave van HANA grote instantie-eenheden, kunt u ook specifieke ondersteunings aanvragen voor een HANA-grote exemplaar-eenheid maken. Bij het volgen van de koppeling **nieuwe ondersteunings aanvraag** 

![stap #1 van de service aanvraag initiëren in Azure Portal](./media/hana-li-portal/portal-initiate-support-request.png)

Als u de service van SAP HANA Large Instances wilt ophalen die in het volgende scherm wordt weer gegeven, moet u mogelijk alle services selecteren, zoals hieronder wordt weer gegeven

![Alle services in Azure Portal selecteren](./media/hana-li-portal/portal-create-service-request.png)

In de lijst met Services kunt u de service **SAP Hana grote instantie** vinden. Bij het kiezen van die service kunt u specifieke probleem typen selecteren, zoals wordt weer gegeven:


![Probleem klasse in Azure Portal selecteren](./media/hana-li-portal/portal-select-problem-class.png)

Onder elk van de verschillende probleem typen krijgt u een selectie van de subtypen van het probleem dat u moet selecteren om het probleem verder te kenmerken. Nadat u het subtype hebt geselecteerd, kunt u nu een naam voor het onderwerp opgeven. Wanneer u klaar bent met het selectie proces, kunt u naar de volgende stap van het maken gaan. In de sectie **oplossingen** gaat u naar documentatie over Hana grote instanties, die een verwijzing naar een oplossing van het probleem kunnen geven. Als u een oplossing voor het probleem niet kunt vinden in de voorgestelde documentatie, gaat u naar de volgende stap. In de volgende stap wordt u gevraagd of het probleem wordt veroorzaakt door Vm's of met HANA grote instantie-eenheden. Met deze informatie kunt u de ondersteunings aanvraag naar de juiste specialisten sturen. 

![Details van de ondersteunings aanvraag in Azure Portal](./media/hana-li-portal/portal-support-request-details.png)

Wanneer u de vragen hebt beantwoord en aanvullende details hebt gegeven, kunt u de volgende stap door lopen om de ondersteunings aanvraag en de verzenden te bekijken.

## <a name="next-steps"></a>Volgende stappen

- [SAP HANA (grote exemplaren) in azure controleren](./troubleshooting-monitoring.md)
- [Bewaken en problemen oplossen vanaf de HANA-zijde](./hana-monitor-troubleshoot.md)
