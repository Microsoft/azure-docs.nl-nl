---
title: Zelfstudie – Een app voor continue patiëntbewaking maken met Azure IoT Central | Microsoft Docs
description: In deze zelfstudie leert u hoe u een toepassing voor continue patiëntbewaking maakt met een Azure IoT Central-toepassingssjabloon.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: f98482a43516683499e83bebafe4eeadc5b7a8b7
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832501"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Zelfstudie: Een sjabloon voor het controleren van een continue patiëntbewaking implementeren en bewaken

In deze zelfstudie ziet u, als een bouwer van oplossingen, hoe u aan de slag gaat met het implementeren van een IoT Cental-toepassingssjabloon voor continue patiëntbewaking. U leert hoe u de sjabloon implementeert en gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingssjabloon maken
> * De toepassingssjabloon doorlopen

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement wordt aanbevolen. U kunt ook een gratis proefversie voor zeven dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).

## <a name="create-an-application-template"></a>Een toepassingssjabloon maken

Navigeer naar de [​​Toepassingsbeheerwebsite van Azure IoT Central](https://apps.azureiotcentral.com/). Selecteer **Bouwen** in de navigatie balk aan de linkerkant en selecteer vervolgens het tabblad **Gezondheidszorg**.

:::image type="content" source="media/app-manager-health.png" alt-text="Sjabloon voor gezondheidszorgapp":::

Selecteer de knop **App maken** om de toepassing te maken en meld u aan met een persoonlijk, werk- of schoolaccount van Microsoft. Hiermee gaat u naar de pagina **Nieuwe toepassing**.

![Toepassing Gezondheidszorg maken](media/app-manager-health-create.png)

![Toepassing Gezondheidszorg factureringsgegevens maken](media/app-manager-health-create-billinginfo.png)

Om uw toepassing te maken doet u het volgende:

1. In Azure IoT Central wordt automatisch een toepassingsnaam voorgesteld op basis van de sjabloon die u hebt geselecteerd. U kunt deze naam accepteren of uw eigen beschrijvende toepassingsnaam invoeren, zoals **Continue patiëntbewaking**. Azure IoT Central genereert ook een uniek URL-voorvoegsel voor u op basis van de naam van de toepassing. U kunt dit URL-voorvoegsel wijzigen in iets dat gemakkelijker te onthouden is als u dat wilt.

2. U kunt kiezen of u de toepassing wilt maken met behulp van het *gratis* abonnement of een van de *standaard*-abonnementen. Toepassingen die u maakt met het gratis abonnement zijn gratis gedurende zeven dagen voordat ze verlopen en ondersteunen maximaal vijf gratis apparaten. U kunt een toepassing op elk moment van het gratis abonnement naar een standaard-abonnement verplaatsen voordat het verloopt. Als u kiest voor het gratis abonnement, moet u uw contactgegevens invoeren en kiezen of u informatie en tips van Microsoft wilt ontvangen. Toepassingen die u maakt met behulp van een standaard-abonnement ondersteunen maximaal twee gratis apparaten en u moet uw Azure-abonnementsgegevens invoeren voor facturering.

3. Selecteer **Maken** onder aan de pagina om uw toepassing te implementeren.

## <a name="walk-through-the-application-template"></a>De toepassingssjabloon doorlopen

### <a name="dashboards"></a>Dashboards

Nadat u de app-sjabloon hebt geïmplementeerd, gaat u eerst naar het **Lamna-dashboard voor controleren van opgenomen patiënten**. Lamna Gezondheidszorg is een fictief ziekenhuis systeem dat twee ziekenhuizen bevat: Ziekenhuis Woodgrove en Ziekenhuis Burkville. Op het operatordashboard van Woodgrove Hospital kunt u:

* Telemetrie en eigenschappen van apparaten zien, zoals het **accuniveau** van het apparaat of de **connectiviteitsstatus**.

* Bekijk de **plattegrond** en de locatie van het Smart Vitals Patch-apparaat.

* De Smart Vitals Patch **opnieuw inrichten** voor een nieuwe patiënt.

* Bekijk een voorbeeld van een **provider-dashboard** dat een zorgteam in een ziekenhuis kan zien om hun patiënten te volgen.

* Wijzig de **patiëntstatus** van het apparaat om aan te geven of het apparaat wordt gebruikt voor een extern scenario of een scenario met een opgenomen patiënt.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Status van patiënten":::

U kunt ook **Ga naar het dashboard voor externe patiënten** selecteren om het operatordashboard voor het Burkville Hospital weer te geven. Dit dashboard bevat een soortgelijke set acties, telemetrie en informatie. U kunt ook meerdere gebruikte apparaten bekijken en de **firmware bijwerken** voor de afzonderlijke apparaten.

:::image type="content" source="media/lamna-remote.png" alt-text="Extern dashboard voor operators":::

### <a name="device-templates"></a>Apparaatsjablonen

Als u **Apparaatsjablonen** selecteert, ziet u de twee apparaattypen in de sjabloon:

* **Smart Vitals Patch**: Dit apparaat vertegenwoordigt een patch die verschillende soorten vitale functies meet. Het kan worden gebruikt voor het controleren van patiënten in en buiten het ziekenhuis. Als u de sjabloon selecteert, ziet u dat de patch zowel apparaatgegevens, zoals het accuniveau en de temperatuur van het apparaat, als gegevens over de gezondheid van patiënten, zoals het ademhalingstempo en de bloeddruk, verzendt.

* **Smart Knee Brace**: Dit apparaat vertegenwoordigt een kniebrace die patiënten gebruiken bij het herstellen van een knievervangende operatie. Als u deze sjabloon selecteert, worden de mogelijkheden, zoals apparaatgegevens, het bewegingsbereik en de versnelling, weergegeven.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Sjabloon voor intelligente patches":::

### <a name="device-groups"></a>Device groups

Gebruik apparaatgroepen om een set apparaten logisch te groeperen en vervolgens bulksgewijs query's of bewerkingen uit te voeren.

Als u het tabblad Apparaatgroepen selecteert, ziet u een standaard apparaatgroep voor elke apparaatprofiel in de toepassing. Er worden ook twee extra voorbeelden van apparaatgroepen gemaakt met de naam **Apparaten inrichten** en **Apparaten met verouderde firmware**. U kunt deze voorbeeldapparaatgroepen gebruiken als invoer voor het uitvoeren van enkele [Taken](#jobs) in de toepassing.

### <a name="rules"></a>Regels

Als u **Regels** selecteert, ziet u de drie regels in de sjabloon:

* **Hoogste temperatuur brace**: Deze regel wordt geactiveerd wanneer de apparaattemperatuur van de Smart Knee Brace hoger is dan 95&deg;F gedurende een periode van minimaal 5 minuten. Gebruik deze regel om het patiënten- en zorgteam te waarschuwen en het apparaat op afstand af te koelen.

* **Val gedetecteerd**: Deze regel wordt geactiveerd als er wordt gedetecteerd dat een patiënt is gevallen. Gebruik deze regel om een actie te configureren voor het implementeren van een operationeel team om de patiënt te helpen die zijn gevallen.

* **Accu van patch laag**: Deze regel wordt geactiveerd wanneer het accuniveau op het apparaat lager is dan 10%. Gebruik deze regel om een melding naar de patiënt te activeren om het apparaat op te laden.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Regels":::

### <a name="jobs"></a>Taken

Met taken kunt u bulkbewerkingen uitvoeren op een set apparaten, met behulp van [apparaatgroepen](#device-groups) als invoer. De toepassingssjabloon heeft twee voorbeeldtaken die een operator kan uitvoeren:

* **Firmware van kniebrace bijwerken**: Deze taak vindt apparaten in de apparaatgroep **Apparaten met verouderde firmware** en voert een opdracht uit om die apparaten bij te werken naar de meest recente firmwareversie. In deze voorbeeldtaak wordt ervan uitgegaan dat de apparaten een **bijwerkopdracht** kan ontvangen en de firmwarebestanden kan ophalen uit de cloud.  

* **Apparaten opnieuw inrichten**: U hebt een set apparaten die recent zijn teruggestuurd naar het ziekenhuis. Deze taak vindt apparaten in de apparaatgroep **Apparaten inrichten** en voert een opdracht uit om ze opnieuw in te richten voor de volgende groep patiënten.

### <a name="devices"></a>Apparaten

Selecteer het tabblad **Apparaten** en selecteer vervolgens een exemplaar van de **Smart Knee Brace**. Er zijn drie weergaven om informatie over het geselecteerde apparaat dat u hebt geselecteerd, te verkennen. Deze weergaven worden gemaakt en gepubliceerd wanneer u de apparaatsjabloon voor uw apparaat bouwt. Deze weergaven zijn daarom consistent op alle apparaten die u verbindt of simuleert.

De weergave **Dashboard** bevat een overzicht van de telemetrie en eigenschappen van het apparaat voor de operator.

Op het tabblad **Eigenschappen** kunt u eigenschappen van de cloud en de lees-/schrijfeigenschappen van het apparaat bewerken.

Met het tabblad **Opdrachten** kunt u opdrachten uitvoeren op het apparaat.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Dashboard voor kniebrace":::

### <a name="data-export"></a>Gegevensexport

Met gegevensexport kunt u de gegevens continu exporteren naar andere Azure-Services, waaronder de [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassing door **Beheer > Toepassingsinstellingen** te bezoeken en klikt u op **Verwijderen**.

:::image type="content" source="media/admin-delete.png" alt-text="Resources opruimen":::

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het maken van een provider-dashboard dat verbinding maakt met uw IoT Central-toepassing.

> [!div class="nextstepaction"]
> [Een provider-dashboard bouwen](howto-health-data-triage.md)