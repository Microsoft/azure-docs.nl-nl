---
title: Gegevens controller maken in Azure Data Studio
description: Gegevens controller maken in Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: f2d44cc769e9673eeb75828126f806d2b2308a17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573877"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Gegevens controller maken in Azure Data Studio

U kunt een gegevens controller maken met behulp van Azure Data Studio via de implementatie wizard en notebooks.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

- U hebt toegang tot een Kubernetes-cluster nodig en het kubeconfig-bestand moet zijn geconfigureerd om te verwijzen naar het Kubernetes-cluster waarnaar u wilt implementeren.
- U moet [de client hulpprogramma's installeren](install-client-tools.md) , met inbegrip van **Azure Data Studio** de uitbrei dingen van de Azure Data Studio **Azure-Arc** en **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- U moet zich aanmelden bij Azure in Azure Data Studio.  Ga hiervoor als volgt te werk: Typ CTRL/Command + SHIFT + P om het opdracht tekst venster te openen en typ **Azure**.  Kies **Azure: Meld u aan**.   In het deel venster klikt u op het pictogram + in de rechter bovenhoek om een Azure-account toe te voegen.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Gebruik de implementatie wizard om een Azure Arc-gegevens controller te maken

Volg deze stappen om een Azure-Arc-gegevens controller te maken met behulp van de implementatie wizard.

1. Klik in Azure Data Studio op het tabblad verbindingen in de linkernavigatiebalk.
2. Klik op de knop **...** boven aan het deel venster verbindingen en kies **nieuwe implementatie...**
3. Kies in de wizard nieuwe implementatie de optie **Azure Arc data controller** en klik vervolgens op de knop **selecteren** onder aan de pagina.
4. Zorg ervoor dat de vereiste hulpprogram ma's beschikbaar zijn en voldoen aan de benodigde versies. **Klik op volgende**.
5. Gebruik het standaard kubeconfig-bestand of selecteer een andere.  Klik op **Volgende**.
6. Kies een Kubernetes-cluster context. Klik op **Volgende**.
7. Kies een implementatie configuratie profiel, afhankelijk van uw doel-Kubernetes-cluster. **Klik op volgende**.
8. Als u Azure Red Hat open Shift of Red Hat open Shift container platform gebruikt, past u beveiligings context beperkingen toe. Volg de instructies in [een beveiligings context beperking Toep assen voor Azure Arc enabled Data Services op open Shift](how-to-apply-security-context-constraint.md).

   >[!IMPORTANT]
   >Op Azure Red Hat open Shift of Red Hat open Shift container platform moet u de beveiligings context beperking Toep assen voordat u de gegevens controller maakt.

1. Kies het gewenste abonnement en de resource groep.
1. Selecteer een Azure-locatie.
   
   De Azure-locatie die u hier selecteert, is de locatie in azure waar de *meta gegevens* van de gegevens controller en de data base-exemplaren die worden beheerd, worden opgeslagen. De gegevens controller-en data base-instanties worden op elk gewenst moment in uw Kubernetes-cluster gemaakt.

10. Selecteer de juiste connectiviteits modus. Meer informatie over [connectiviteits modi](./connectivity.md). **Klik op volgende**.

    Als u de Service-Principal-referenties voor de directe connectiviteits modus selecteert zoals beschreven in [Create Service Principal](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).

11. Voer een naam in voor de gegevens controller en voor de naam ruimte waarin de gegevens controller wordt gemaakt.

    De gegevens controller en naam van de naam ruimte worden gebruikt voor het maken van een aangepaste resource in het Kubernetes-cluster, zodat deze voldoen aan de [naamgevings conventies van Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).
    
    Als de naam ruimte al bestaat, wordt deze gebruikt als de naam ruimte nog geen andere Kubernetes-objecten bevat: Peul, enzovoort.  Als de naam ruimte niet bestaat, wordt er een poging gedaan om de naam ruimte te maken.  Voor het maken van een naam ruimte in een Kubernetes-cluster zijn Kubernetes-cluster beheerder bevoegdheden vereist.  Als u geen Kubernetes hebt, vraagt u uw Kubernetes-cluster beheerder de eerste paar stappen uit te voeren in het artikel [een gegevens controller maken met Kubernetes-systeem eigen hulpprogram ma's](./create-data-controller-using-kubernetes-native-tools.md) die moeten worden uitgevoerd door een Kubernetes-beheerder voordat u deze wizard voltooit.


12. Selecteer de opslag klasse waar de gegevens controller wordt geïmplementeerd. 
13.  Voer een gebruikers naam en wacht woord in en bevestig het wacht woord voor het gebruikers account van de gegevens controller beheerder. Klik op **Volgende**.

14. Controleer de implementatie configuratie.
15. Klik op **implementeren** om de gewenste configuratie of het **script naar de notebook** te implementeren om de implementatie-instructies te controleren of wijzigingen aan te brengen die nodig zijn, zoals namen van opslag klassen of service typen. Klik boven aan het notitie blok op **alles uitvoeren** .

## <a name="monitoring-the-creation-status"></a>De aanmaak status bewaken

Het duurt enkele minuten voordat de controller is gemaakt. U kunt de voortgang in een ander Terminal venster bewaken met de volgende opdrachten:

> [!NOTE]
>  In de onderstaande voor beelden wordt ervan uitgegaan dat u een gegevens controller en Kubernetes-naam ruimte hebt gemaakt met de naam ' Arc '.  Als u de naam van een andere naam ruimte/gegevens controller hebt gebruikt, kunt u ' Arc ' vervangen door uw naam.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

U kunt ook de status van de aanmaak van een bepaalde pod controleren door een opdracht als hieronder uit te voeren.  Dit is met name nuttig voor het oplossen van problemen.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemen bij het maken oplossen

Raadpleeg de [hand leiding](troubleshoot-guide.md)voor het oplossen van problemen als u problemen ondervindt bij het maken.
