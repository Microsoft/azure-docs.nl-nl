---
title: Quickstart - WildFly in CentOS
description: Java-toepassingen implementeren op WildFly in CentOS VM
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: ddd6f277bc71467060aa2279d93f9410a1327dde
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486603"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Quickstart: WildFly in CentOS 8

In deze quickstart ziet u hoe u het zelfstandige knooppunt van WildFly van CentOS 8 VM implementeert. Het is ideaal voor het ontwikkelen en testen van zakelijke Java-toepassingen in Azure. Een toepassingsserverabonnement is niet vereist voor het implementeren van deze quickstart.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. Als u geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) activeren of [gratis een account aanmaken](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Gebruiksvoorbeeld

WildFly is ideaal voor het ontwikkelen en testen van zakelijke Java-toepassingen in Azure. Een lijst met technologieën die beschikbaar zijn in de serverconfiguratieprofielen van WildFly 18 zijn beschikbaar in de [Introductiehandleiding van WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

U kunt WildFly gebruiken in de zelfstandige modus of in de clustermodus van uw use-case. U kunt zorgen voor hoge beschikbaarheid van essentiële Jakarta EE-toepassingen door WildFly op een cluster met knooppunten. Maak een klein aantal wijzigingen in de toepassingsconfiguratie en implementeer de toepassing vervolgens in het cluster. Raadpleeg de [Handleiding voor hoge beschikbaarheid van WildFly](https://docs.wildfly.org/18/High_Availability_Guide.html) voor meer informatie.

## <a name="configuration-choice"></a>Configuratiekeuze

WildFly kan worden opgestart in **Zelfstandige server** modus: een zelfstandig serverexemplaar is een onafhankelijk proces, vergelijkbaar met een JBoss-toepassingsserver (AS) 3-, 4-, 5- of 6-exemplaar. Zelfstandige exemplaren kunnen worden gestart via de startscripts standalone.sh of standalone. bat. Voor meer dan één zelfstandig exemplaar is het de verantwoordelijkheid van de gebruiker om het beheer van meerdere servers op de verschillende servers te coördineren.

U kunt een WildFly-exemplaar ook starten met een alternatieve configuratie met behulp van configuratiebestanden die beschikbaar zijn in de configuratiemap.

Hieronder vindt u de configuratiebestanden voor de zelfstandige server:

- standalone. xml (standaard): deze configuratie is het standaardbestand dat wordt gebruikt voor het starten van het WildFly-exemplaar. Het bevat een gecertificeerde configuratie van het Jakarta-webprofiel met de vereiste technologieën.
   
- Standalone-ha.xml - Jakarta EE Web Profile 8 gecertificeerde configuratie met hoge beschikbaarheid (gericht op webtoepassingen).
   
- Standalone-full.xml - Jakarta EE Platform 8 gecertificeerde configuratie met alle vereiste technologieën voor het hosten van Jakarta EE-toepassingen.

- Standalone-full.xml - Jakarta EE Platform 8 gecertificeerde configuratie met hoge beschikbaarheid voor het hosten van Jakarta EE-toepassingen.

Als u de zelfstandige WildFly-server met een andere configuratie wilt starten, gebruikt u het argument --server-config met het server-config-bestand.

Als u bijvoorbeeld Jakarta EE platform 8 met clusterfuncties wilt gebruiken, gebruikt u de volgende opdracht:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Voor meer informatie over de configuraties raadpleegt u de [Introductiehandleiding van WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Opmerkingen over licentieverlening, ondersteuning en abonnementen

De Azure CentOS 8-installatiekopie is een VM-installatiekopie met betalen per gebruik (PAYG) en vereist niet dat de gebruiker een licentie aanschaft. De eerste keer dat de VM wordt gestart, wordt de licentie van het besturingssysteem van de VM automatisch geactiveerd en wordt een uurtarief in rekening gebracht. Dit is in aanvulling op de VM-uurtarieven van Microsoft voor Linux. Klik op [Prijzen voor Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) voor meer informatie. WildFly is gratis te downloaden en te gebruiken en vereist geen Red Hat-abonnement of -licentie.

## <a name="how-to-consume"></a>Hoe u dit kunt gebruiken

U kunt de sjabloon op de volgende drie manieren implementeren:

- Gebruik PowerShell - implementeer de sjabloon door de volgende opdrachten uit te voeren: (Bekijk [Azure PowerShell](/powershell/azure/) voor meer informatie over het installeren en configureren van Azure PowerShell).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Gebruik Azure CLI - voer de volgende opdrachten uit om de sjabloon te implementeren: (Bekijk [Azure platformoverschrijdende opdrachtregel](/cli/azure/install-azure-cli) voor meer informatie over het installeren en configureren van de interface van de Azure platformoverschrijdende opdrachtregel).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Gebruik Azure Portal - implementeer de sjabloon door <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">hier</a> te klikken en u aan te melden bij uw Azure Portal.

## <a name="arm-template"></a>ARM-sjabloon

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 op CentOS 8 (zelfstandige VM)</a> : dit is een quickstart-sjabloon waarmee een zelfstandig knooppunt wordt gemaakt van WildFly 18.0.1.Final in CentOS 8 VM in uw resourcegroep (RG), die een privé-IP-adres voor de VM, Virtual Network en een opslagaccount voor diagnostische gegevens bevat. Er wordt ook een voorbeeld van een Java-toepassing met de naam JBoss-EAP op Azure geïmplementeerd in WildFly.

## <a name="resource-links"></a>Resource-links

* Meer informatie over [WildFly 18](https://docs.wildfly.org/18/)
* Meer informatie over [Linux-distributies in Azure](../../linux/endorsed-distros.md)
* [Documentatie voor Azure voor Java-ontwikkelaars](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Volgende stappen

Bekijk voor de productieomgeving de Red Hat JBoss EAP Azure Quickstart ARM-sjablonen:

Zelfstandige virtuele machine met Red Hat Enterprise Linux met voorbeeldtoepassing:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP op Red Hat Enterprise Linux (zelfstandige VM)</a>

Geclusterde virtuele machines met Red Hat Enterprise Linux met voorbeeldtoepassing:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP op Red Hat Enterprise Linux (geclusterde VM’s)</a>

Geclusterde virtuele-machineschaalset met Red Hat Enterprise Linux met voorbeeldtoepassing:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP op Red Hat Enterprise Linux (geclusterde virtuele-machineschaalset)</a>