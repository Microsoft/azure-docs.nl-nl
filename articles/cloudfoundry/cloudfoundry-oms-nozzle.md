---
title: Azure Log Analytics-spuit voor Cloud Foundry bewaking implementeren
description: Stapsgewijze richt lijnen voor het implementeren van de Cloud Foundry loggregator-spuit voor Azure Log Analytics. Gebruik de spuit stuk om de gegevens over het Cloud Foundry systeem status en prestaties te bewaken.
services: virtual-machines
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 54001c47d03b686a8e7c1f59f1e53d405e3bc506
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557383"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Azure Log Analytics-spuit voor Cloud Foundry systeem bewaking implementeren

[Azure monitor](https://azure.microsoft.com/services/log-analytics/) is een service in Azure. U kunt hiermee gegevens verzamelen en analyseren die worden gegenereerd op basis van uw Cloud-en on-premises omgevingen.

Het Log Analytics spuit stuk (de spuit stuk) is een Cloud Foundry (CF)-onderdeel dat metrische gegevens van de [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) -firehose doorstuurt naar Azure monitor-Logboeken. Met de spuit stuk kunt u uw CF-systeem status-en prestatie gegevens verzamelen, bekijken en analyseren, in meerdere implementaties.

In dit document leert u hoe u de spuit kunt implementeren in uw CF-omgeving en vervolgens toegang hebt tot de gegevens via de console Azure Monitor logs.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

De volgende stappen zijn vereisten voor het implementeren van de spuit stuk.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. een CF-of Pivotal Cloud Foundry-omgeving implementeren in azure

U kunt de spuit stuk gebruiken met een open-source-CF-implementatie of een PCF-implementatie (Pivotal Cloud Foundry).

* [Cloud Foundry op Azure implementeren](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Draai Cloud Foundry op Azure implementeren](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installeer de CF opdracht regel Programma's voor het implementeren van de spuit stuk

De spuit stuk wordt uitgevoerd als een toepassing in de CF-omgeving. U hebt CF CLI nodig om de toepassing te implementeren.

De spuit stuk heeft ook toegangs machtigingen nodig voor de loggregator firehose en de Cloud controller. Als u de gebruiker wilt maken en configureren, hebt u de service gebruikers account en verificatie (UAA) nodig.

* [Cloud Foundry CLI installeren](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA-opdracht regel client installeren](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Voordat u de UAA opdracht regel-client instelt, moet u ervoor zorgen dat RubyGems is geïnstalleerd.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. een Log Analytics-werk ruimte maken in azure

U kunt de Log Analytics-werk ruimte hand matig of met behulp van een sjabloon maken. Met de sjabloon wordt een installatie van vooraf geconfigureerde KPI-weer gaven en waarschuwingen voor de Azure Monitor Logboeken-console geïmplementeerd. 

#### <a name="to-create-the-workspace-manually"></a>De werk ruimte hand matig maken:

1. Zoek in de Azure Portal de lijst met Services in de Azure Marketplace en selecteer vervolgens Log Analytics werk ruimten.
2. Selecteer **Maken** en geef uw keuze aan voor de volgende items:

   * **Log Analytics werk ruimte**: Typ een naam voor uw werk ruimte.
   * **Abonnement**: als u meerdere abonnementen hebt, kiest u de optie die gelijk is aan uw CF-implementatie.
   * **Resource groep**: u kunt een nieuwe resource groep maken of dezelfde gebruiken met uw CF-implementatie.
   * **Locatie**: Voer de locatie in.
   * **Prijs categorie**: Selecteer **OK** om te volt ooien.

Zie [aan de slag met Azure monitor-logboeken](../azure-monitor/overview.md)voor meer informatie.

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>De Log Analytics-werk ruimte maken via de bewakings sjabloon van Azure Market Place:

1. Open Azure Portal.
1. Klik op het plus teken (' + ') of ' een resource maken ' in de linkerbovenhoek.
1. Typ ' Cloud Foundry ' in het venster zoeken, selecteer Cloud Foundry bewakings oplossing.
1. De voor pagina met de Cloud Foundry monitoring-oplossings sjabloon is geladen, klikt u op maken om de Blade sjabloon te starten.
1. Voer de vereiste para meters in:
    * **Abonnement**: Selecteer een Azure-abonnement voor de log Analytics-werk ruimte, meestal hetzelfde als bij Cloud Foundry-implementatie.
    * **Resource groep**: Selecteer een bestaande resource groep of maak een nieuwe voor de log Analytics-werk ruimte.
    * **Locatie van de resource groep**: Selecteer de locatie van de resource groep.
    * **OMS_Workspace_Name**: Voer de naam van een werk ruimte in als de werk ruimte niet bestaat, wordt er een nieuwe gemaakt.
    * **OMS_Workspace_Region**: Selecteer de locatie voor de werk ruimte.
    * **OMS_Workspace_Pricing_Tier**: selecteer de log Analytics WORKSPACE-SKU. Zie de [richt lijnen voor prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) voor naslag informatie.
    * **Juridische voor waarden**: Klik op juridische voor waarden en klik vervolgens op maken om de juridische termijn te accepteren.
1. Nadat alle para meters zijn opgegeven, klikt u op maken om de sjabloon te implementeren. Wanneer de implementatie is voltooid, wordt de status weer gegeven op het tabblad melding.


## <a name="deploy-the-nozzle"></a>De spuit stuk implementeren

Er zijn verschillende manieren om de spuit stuk te implementeren: als een PCF-tegel of als een CF-toepassing.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>De spuit stuk als een PCF OPS Manager-tegel implementeren

Volg de stappen voor [het installeren en configureren van de Azure log Analytics-spuit stuk voor PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Dit is de vereenvoudigde benadering van de PCF OPS Manager-tegel, waarna de spuit stuk automatisch wordt geconfigureerd en gepusht. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>De spuit hand matig implementeren als een CF-toepassing

Als u geen gebruik maakt van PCF OPS Manager, implementeert u de spuit stuk als een toepassing. In de volgende secties wordt dit proces beschreven.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Meld u aan bij uw CF-implementatie als een beheerder via CF CLI

Voer de volgende opdracht uit:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" is uw CF-domein naam. U kunt deze ophalen door in het manifest bestand van de CF-implementatie op SYSTEM_DOMAIN te zoeken. 

"CF_User" is de CF-beheerders naam. U kunt de naam en het wacht woord ophalen door in de sectie ' scim ' te zoeken naar de naam en het ' cf_admin_password ' in het manifest bestand van de CF-implementatie.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Een CF-gebruiker maken en vereiste bevoegdheden verlenen

Voer de volgende opdrachten uit:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" is uw CF-domein naam. U kunt deze ophalen door in het manifest bestand van de CF-implementatie op SYSTEM_DOMAIN te zoeken.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Down load de nieuwste versie van Log Analytics spuit stukken

Voer de volgende opdracht uit:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

Nu kunt u omgevings variabelen instellen in het bestand manifest. yml in uw huidige map. Hieronder ziet u het app-manifest voor de spuit stuk. Vervang waarden door uw specifieke Log Analytics werkruimte gegevens.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>De toepassing pushen vanaf uw ontwikkel computer

Zorg ervoor dat u zich in de map OMS-log-Analytics-firehose-spuit stuk bevindt. Voer de volgende opdracht uit:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>De spuit installatie valideren

### <a name="from-apps-manager-for-pcf"></a>Van apps Manager (voor PCF)

1. Meld u aan bij OPS Manager en zorg ervoor dat de tegel wordt weer gegeven op het installatie dashboard.
2. Meld u aan bij het beheer van apps, zorg ervoor dat de ruimte die u hebt gemaakt voor de spuit stuk wordt weer gegeven in het gebruiks rapport en controleer of de status normaal is.

### <a name="from-your-development-computer"></a>Van uw ontwikkel computer

In het venster CF CLI typt u:
```
cf apps
```
Controleer of de toepassing OMS-spuit programma wordt uitgevoerd.

## <a name="view-the-data-in-the-azure-portal"></a>De gegevens in de Azure Portal weer geven

Als u de bewakings oplossing via de Market Place-sjabloon hebt geïmplementeerd, gaat u naar Azure Portal en zoekt u de oplossing. U vindt de oplossing in de resource groep die u in de sjabloon hebt opgegeven. Klik op de oplossing, blader naar de log Analytics-console, de vooraf geconfigureerde weer gaven worden weer gegeven met de belangrijkste Cloud Foundry systeem-Kpi's, toepassings gegevens, waarschuwingen en metrische gegevens over de status van de virtuele machine. 

Als u de werk ruimte Log Analytics hand matig hebt gemaakt, volgt u de onderstaande stappen om de weer gaven en waarschuwingen te maken:

### <a name="1-import-the-oms-view"></a>1. de OMS-weer gave importeren

Blader vanuit de OMS-Portal naar **weer gave Designer**  >  **import**  >  **Browse** en selecteer een van de omsview-bestanden. Selecteer bijvoorbeeld *Cloud Foundry. omsview* en sla de weer gave op. Er wordt nu een tegel op de pagina **overzicht** weer gegeven. Selecteer deze optie om gevisualiseerde metrische gegevens weer te geven.

U kunt deze weer gaven aanpassen of nieuwe weer gaven maken met behulp van de **weer gave Designer**.

De *' Cloud Foundry. omsview '* is een preview-versie van de Cloud Foundry OMS-weergave sjabloon. Dit is een volledig geconfigureerde standaard sjabloon. Als u suggesties of feedback over de sjabloon hebt, verzendt u deze naar de [sectie probleem](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. waarschuwings regels maken

U kunt [de waarschuwingen maken](../azure-monitor/alerts/alerts-overview.md)en zo nodig de query's en drempel waarden aanpassen. De volgende waarschuwingen worden aanbevolen:

| Zoekquery                                                                  | Waarschuwing genereren op basis van | Beschrijving                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type = CF_ValueMetric_CL Origin_s = BBS Name_s = "domein. CF-apps"                   | Aantal resultaten < 1   | **BBS. Domain.cf: apps** geeft aan of het CF-Apps-domein up-to-date is. Dit betekent dat CF-app-aanvragen van de Cloud controller zijn gesynchroniseerd met BBS. LRPsDesired (Diego-gewenste AIs) voor uitvoering. Geen gegevens ontvangen betekent dat het CF-Apps-domein niet up-to-date is in het opgegeven tijd venster. |
| Type = CF_ValueMetric_CL Origin_s = rep Name_s = UnhealthyCell Value_d>1            | Aantal resultaten > 0   | Voor Diego-cellen betekent 0 dat ze in orde zijn, en 1 betekent een slechte status. Stel de waarschuwing in als er meerdere beschadigde Diego-cellen worden gedetecteerd in het opgegeven tijd venster. |
| Type = CF_ValueMetric_CL Origin_s = "Bosh-HM-doorstuur server" Name_s = "System. in orde" Value_d = 0 | Aantal resultaten > 0 | 1 betekent dat het systeem in orde is en 0 betekent dat het systeem niet in orde is. |
| Type = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d>0 | Aantal resultaten > 0   | Consul verzendt regel matig de integriteits status. 0 betekent dat het systeem in orde is en 1 betekent dat de route zender detecteert dat consul niet actief is. |
| Type = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s = "TruncatingBuffer. DroppedMessages" of Name_s = "Doppler. shedEnvelopes") Delta_d>0 | Aantal resultaten > 0 | Het Delta aantal berichten dat opzettelijk is verwijderd door Doppler vanwege een back-updruk. |
| Type = CF_LogMessage_CL SourceType_s = LGR MessageType_s = ERR                      | Aantal resultaten > 0   | Loggregator verzendt **LGR** om problemen met het logboek registratie proces aan te geven. Een voor beeld van een dergelijk probleem is wanneer de uitvoer van het logboek bericht te hoog is. |
| Type = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Aantal resultaten > 0   | Wanneer de spuit stuk een trage consumenten waarschuwing van loggregator ontvangt, wordt de **slowConsumerAlert** -ValueMetric naar Azure monitor-logboeken verzonden. |
| Type = CF_CounterEvent_CL Job_s = spuit Name_s = eventsLost Delta_d>0              | Aantal resultaten > 0   | Als het Delta aantal verloren gebeurtenissen een drempel waarde bereikt, betekent dit dat de spuit stuk mogelijk een probleem heeft dat wordt uitgevoerd. |

## <a name="scale"></a>Schalen

U kunt de spuit stuk en de loggregator schalen.

### <a name="scale-the-nozzle"></a>Spuit stuk schalen

U moet beginnen met ten minste twee exemplaren van de spuit stuk. De firehose distribueert de werk belasting over alle exemplaren van de spuit stuk.
Om ervoor te zorgen dat de spuit stuk het gegevens verkeer van de firehose kan blijven, stelt u de **slowConsumerAlert** -waarschuwing in (vermeld in de voor gaande sectie ' waarschuwings regels maken '). Nadat u een waarschuwing hebt ontvangen, volgt u de [instructies voor Slow spuit](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) om te bepalen of schalen nodig is.
Als u de spuit stuk wilt uitbreiden, gebruikt u apps beheren of de CF CLI om de instantie nummers of het geheugen of de schijf bronnen voor de spuit te verhogen.

### <a name="scale-the-loggregator"></a>De loggregator schalen

Loggregator verzendt een **LGR** -logboek bericht om problemen met het logboek registratie proces aan te geven. U kunt de waarschuwing bewaken om te bepalen of de loggregator omhoog moet worden geschaald.
U kunt de loggregator omhoog schalen door de buffer grootte van Doppler te verhogen of extra Doppler server-instanties toe te voegen in het CF-manifest. Zie [de richt lijnen voor het schalen van de loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)voor meer informatie.

## <a name="update"></a>Bijwerken

Als u de spuit stuk wilt bijwerken met een nieuwere versie, downloadt u de nieuwe spuit versie, volgt u de stappen in de sectie ' spuit stuk implementeren ' en pusht u de toepassing opnieuw.

### <a name="remove-the-nozzle-from-ops-manager"></a>Spuit stuk uit OPS Manager verwijderen

1. Meld u aan bij de OPS-Manager.
2. Zoek het **Microsoft Azure log Analytics spuit voor** de tegel pcf.
3. Selecteer het pictogram voor garbage en bevestig het verwijderen.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Spuit stuk verwijderen van uw ontwikkel computer

Typ in uw CF CLI-venster:
```
cf delete <App Name> -r
```

Als u de spuit stuk verwijdert, worden de gegevens in de OMS-Portal niet automatisch verwijderd. Het verloopt op basis van de instelling voor het bewaren van Azure Monitor Logboeken.

## <a name="support-and-feedback"></a>Ondersteuning en feedback

De Azure Log Analytics spuit is open sourced. Stuur uw vragen en feedback naar de [sectie github](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Als u een ondersteunings aanvraag voor Azure wilt openen, kiest u virtuele machine met Cloud Foundry als de service categorie. 

## <a name="next-step"></a>Volgende stap

Vanuit PCF 2.0 worden metrische gegevens over de VM-prestaties overgebracht naar Azure Log Analytics spuit stuk door de doorstuur server van systeem metrieken en geïntegreerd in de Log Analytics-werk ruimte. U hebt de Log Analytics-agent niet langer nodig voor de metrische gegevens van de VM-prestaties. U kunt echter nog steeds de Log Analytics agent gebruiken om syslog-gegevens te verzamelen. De Log Analytics-agent wordt geïnstalleerd als een Bosh-invoeg toepassing voor uw CF-Vm's. 

Zie [log Analytics agent implementeren voor uw Cloud Foundry-implementatie](https://github.com/Azure/oms-agent-for-linux-boshrelease)voor meer informatie.