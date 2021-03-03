---
title: 'Quickstart: JBoss Enterprise Application (EAP) op Red Hat Enterprise Linux (RHEL) implementeren naar virtuele Azure-machines en virtuele-machineschaalsets'
description: Zakelijke Java-toepassingen implementeren met behulp van Red Hat JBoss EAP op virtuele Azure RHEL-machines en virtuele-machineschaalsets.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: 4027e9c336d938c3877e56b0eb59edea6ac1d992
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671973"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Zakelijke Java-toepassingen implementeren in Azure met JBoss EAP op Red Hat Enterprise Linux

In de Azure-quickstart-sjablonen in dit artikel leest u hoe u [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) met [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) implementeert naar virtuele Azure-machines en virtuele-machineschaalsets. U gebruikt een voorbeeld van een Java-app om de implementatie te valideren. 

JBoss EAP is een opensource-toepassingsserverplatform. Het biedt beveiliging, schaalbaarheid en prestaties op bedrijfsniveau voor uw Java-toepassingen. RHEL is een opensource-platform voor het besturingssysteem. Het biedt de mogelijkheid om bestaande apps te schalen en nieuwe technologieën in alle omgevingen uit te rollen. 

JBoss EAP en RHEL bevatten alles wat u nodig hebt om zakelijke Java-toepassingen te bouwen, uit te voeren, te implementeren en te beheren in elke omgeving. De combinatie is een opensource-oplossing voor on-premises, virtuele omgevingen en in privé-, openbare of hybride clouds.

## <a name="prerequisites"></a>Vereisten 

* Een Azure-account met een actief abonnement. Als u een Azure-abonnement wilt ontvangen, activeert u uw [Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [maakt u gratis een account](https://azure.microsoft.com/pricing/free-trial).

* JBoss EAP-installatie. U moet een Red Hat-account hebben met rechten voor Red Hat Subscription Management (RHSM) voor JBoss EAP. Met deze rechten kunt u de door Red Hat geteste en gecertificeerde JBoss EAP-versie downloaden.  

  Als u geen EAP-rechten hebt, moet u een [JBoss EAP-evaluatieabonnement verkrijgen](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) voordat u aan de slag gaat. Als u een nieuw Red Hat-abonnement wilt maken, gaat u naar [Red Hat Customer Portal](https://access.redhat.com/) en stelt u een account in.

* De [Azure CLI](/cli/azure/overview).

* RHEL-opties. Kies tussen betalen per gebruik (PAYG) of uw eigen abonnement (BYOS). Met BYOS moet u uw [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-installatiekopie activeren voordat u de quickstart-sjabloon implementeert.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE- en Jakarata EE-toepassingsmigratie

### <a name="migrate-to-jboss-eap"></a>Migreren naar JBoss EAP
JBoss EAP 7.2 en 7.3 zijn gecertificeerde implementaties van de specificaties voor Java Enterprise Edition (Java EE) 8 en Jakarta EE 8. JBoss EAP biedt vooraf geconfigureerde opties voor functies, zoals clustering met hoge beschikbaarheid (HA), berichten en gedistribueerd opslaan in cache. Ook kunnen gebruikers toepassingen schrijven, implementeren en uitvoeren met behulp van de verschillende API's en services die JBoss EAP biedt.  

Ga naar [Kennismaking met JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) of [Kennismaking met JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index) voor meer informatie over JBoss EAP.

 #### <a name="applications-on-jboss-eap"></a>Toepassingen op JBoss EAP

* **Webservices-toepassingen**. Webservices bieden een standaardmanier om tussen softwaretoepassingen onderling te werken. Elke toepassing kan worden uitgevoerd op verschillende platforms en frameworks. Deze webservices vergemakkelijken de communicatie van interne en heterogene subsystemen. 

  Ga naar [Ontwikkelen van webservices-toepassingen op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) of [Ontwikkelen van webservices-toepassingen op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index) voor meer informatie.

* **Enterprise Java Beans-toepassingen (EJB)** . EJB 3.2 is een API voor het ontwikkelen van gedistribueerde, transactionele, veilige en draagbare Java EE- en Jakarta EE-toepassingen. EJB maakt gebruik van onderdelen aan de serverzijde met de naam Enterprise Beans voor het op een losgekoppelde manier implementeren van de bedrijfslogica van een toepassing, die hergebruik stimuleert. 

  Ga naar [Ontwikkelen van EJB-toepassingen op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) of [Ontwikkelen van EJB-toepassingen op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index) voor meer informatie.

* **Toepassingen in de sluimerstand zetten**. Ontwikkelaars en beheerders kunnen Java Persistence API- (JPA) en Hibernate-toepassingen ontwikkelen en implementeren met JBoss EAP. Hibernate Core is een object-relationeel toewijzingsframework voor de Java-taal. Het biedt een framework voor het toewijzen van een objectgeoriënteerd domeinmodel aan een relationele database, zodat directe interactie van toepassingen met de database kan worden vermeden. 

  Hibernate Entity Manager implementeert de programmeerinterfaces en levenscyclusregels die zijn gedefinieerd door de [JPA 2.1-specificatie](https://www.jcp.org/en/jsr/overview). In combinatie met Hibernate Annotations, implementeert deze wrapper een volledige (en zelfstandige) JPA-oplossing bovenop de goed functionerende Hibernate Core. 
  
  Ga naar [JPA op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) of  [Jakarta Persistence op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api) voor meer informatie over Hibernate.

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) is een migratiehulpprogramma voor Java-toepassingsservers. Gebruik dit hulpprogramma om te migreren van een andere app-server naar JBoss EAP. Het werkt met IDE-invoegtoepassingen voor [Eclipse IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) en [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) voor Java. 

MTA is een gratis en opensource-hulpprogramma dat:
* Toepassingsanalyse automatiseert.
* Schatting van inspanning ondersteunt.
* Codemigratie versnelt.
* Containerisatie ondersteunt.
* Kan worden geïntegreerd met Azure Workload Builder.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>JBoss EAP migreren van on-premises naar Azure
Met de Azure Marketplace-aanbieding van JBoss EAP op RHEL worden Azure-VM's in minder dan 20 minuten geïnstalleerd en ingericht. U hebt toegang tot deze aanbiedingen via [Azure Marketplace](https://azuremarketplace.microsoft.com/).

Deze Azure Marketplace-aanbieding omvat diverse combinaties van EAP- en RHEL-versies ter ondersteuning van uw vereisten. Voor JBoss EAP geldt altijd dat u uw eigen abonnement moet gebruiken (BYOS), maar voor een RHEL-besturingssysteem kunt u kiezen tussen BYOS of betalen per gebruik (PAYG). De Azure Marketplace-aanbieding bevat abonnementsopties voor JBoss EAP op RHEL als zelfstandige of geclusterde virtuele machines:

* JBoss EAP 7.2 op RHEL 7.7 VM (PAYG)
* JBoss EAP 7.2 op RHEL 8.0 VM (PAYG)
* JBoss EAP 7.3 op RHEL 8.0 VM (PAYG)
* JBoss EAP 7.2 op RHEL 7.7 VM (BYOS)
* JBoss EAP 7.2 op RHEL 8.0 VM (BYOS)
* JBoss EAP 7.3 op RHEL 8.0 VM (BYOS)

Naast Azure Marketplace-aanbiedingen kunt u quickstart-sjablonen gebruiken om met uw Azure-migratietraject aan de slag te gaan. Deze quickstarts bevatten vooraf gemaakte Azure Resource Manager-sjablonen (ARM) en -scripts voor het implementeren van JBoss EAP op RHEL in verschillende configuraties en versiecombinaties. U krijgt het volgende:

* Een load balancer.
* Een Privé-IP-adres voor taakverdeling en virtuele machines.
* Een virtueel netwerk met één subnet.
* VM-configuratie (cluster of zelfstandig).
* Een voorbeeld van een Java-toepassing.

De oplossingsarchitectuur voor deze sjablonen omvat:

* JBoss EAP op een zelfstandige RHEL VM.
* JBoss EAP geclusterd op meerdere RHEL VM's.
* JBoss EAP geclusterd met behulp van Azure virtuele-machineschaalsets.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migratie van de Linux-werkbelasting voor JBoss EAP
Azure Workload Builder vereenvoudigt het testen van het concept, de evaluatie en het migratieproces voor on-premises Java-apps naar Azure. Workload Builder wordt geïntegreerd met de Azure Migrate Discovery Tool om JBoss EAP-servers te identificeren. Vervolgens wordt een Ansible-playbook voor JBoss EAP-serverimplementatie dynamisch gegenereerd. Het Red Hat MTA-hulpprogramma wordt gebruikt voor het migreren van servers van andere app-servers naar JBoss EAP. 

Stappen voor het vereenvoudigen van de migratie zijn onder andere:
1. **Evaluatie**. Evalueer JBoss EAP-clusters met behulp van een Azure-VM of een virtuele-machineschaalset.
1. **Beoordeling**. Scan toepassingen en de infrastructuur.
1. **Configuratie van de infrastructuur**. Maak een werkbelastingsprofiel.
1. **Implementeren en testen**. Implementeer, migreer en test de werkbelasting.
1. **Configuratie na de implementatie**. Integreer met gegevens, bewakingsfuncties, beveiliging, back-up en meer.

## <a name="server-configuration-choice"></a>Keuze voor serverconfiguratie

Voor de implementatie van de RHEL VM kunt u kiezen tussen PAYG en BYOS. Installatiekopieën van de [Azure Marketplace](https://azuremarketplace.microsoft.com) staan standaard ingesteld op PAYG. Implementeer een RHEL VM van het type BYOS als u uw eigen RHEL-installatiekopie van het besturingssysteem hebt. Zorg ervoor dat uw RHSM-account BYOS-rechten heeft via Cloud Access voordat u de virtuele machine of virtuele-machineschaalset implementeert.

JBoss EAP heeft krachtige beheermogelijkheden en biedt functionaliteit en API's voor de toepassingen. Deze beheermogelijkheden verschillen, afhankelijk van welke werkingsmodus u gebruikt om JBoss EAP te starten. Het wordt ondersteund op RHEL en Windows Server. JBoss EAP biedt een zelfstandige serverwerkingsmodus voor het beheren van afzonderlijke exemplaren. Het biedt ook een beheerd domeinmodus voor het beheren van groepen exemplaren vanuit één besturingspunt. 

> [!NOTE]
> JBoss EAP-beheerde domeinen worden niet ondersteund in Microsoft Azure omdat de functie voor hoge beschikbaarheid (HA) wordt beheerd door Azure-infrastructuurservices. 

De omgevingsvariabele `EAP_HOME` wordt gebruikt voor het aanduiden van het pad naar de JBoss EAP-installatie. Gebruik de volgende opdracht om de JBoss EAP-service in de zelfstandige modus te starten:

```
$EAP_HOME/bin/standalone.sh
```
    
Dit opstartscript maakt gebruik van het EAP_HOME/bin/standalone.conf-bestand om enkele standaardvoorkeuren in te stellen, zoals JVM-opties. U kunt instellingen in dit bestand aanpassen. JBoss EAP maakt gebruik van het configuratiebestand standalone.xml om standaard in de zelfstandige modus te starten, maar u kunt ook een andere modus gebruiken om het te starten. 

Raadpleeg [Configuratiebestanden voor zelfstandige server voor EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) of [Configuratiebestanden voor zelfstandige server voor EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) voor meer informatie over de beschikbare zelfstandige configuratiebestanden en hoe u deze kunt gebruiken. 

Gebruik het argument `--server-config` om JBoss EAP te starten met een andere configuratie. Bijvoorbeeld:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Gebruik het argument `--help` voor een volledige lijst met alle beschikbare opstartscriptargumenten en de doeleinden daarvan. Zie [Serverruntime-argumenten op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) of [Serverruntime-argumenten op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) voor meer informatie.
    
JBoss EAP kan ook in de clustermodus worden gebruikt. JBoss EAP-clusterberichten maakt groepering van JBoss EAP-berichtenservers mogelijk voor het delen van de verwerkingsbelasting van berichten. Elk actief knooppunt in het cluster is een actieve JBoss EAP-berichtenserver, die zijn eigen berichten beheert en zijn eigen verbindingen afhandelt. Zie [Overzicht van clusters op EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) of [Overzicht van clusters op EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) voor meer informatie. 

## <a name="support-and-subscription-notes"></a>Opmerkingen over ondersteuning en abonnementen
Deze quickstart-sjablonen worden als volgt aangeboden: 

- Het RHEL-besturingssysteem wordt aangeboden als PAYG of BYOS via het Red Hat Gold-installatiekopiemodel.
- JBoss EAP wordt alleen aangeboden als BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Het RHEL-besturingssysteem gebruiken met het PAYG-model

Deze quickstart-sjablonen gebruiken standaard de RHEL 7.7 of 8.0 PAYG-installatiekopie op aanvraag uit Azure Marketplace. Voor PAYG-installatiekopieën gelden extra kosten per uur voor RHEL-abonnementen boven op de normale berekenings-, netwerk- en opslagkosten. Tevens wordt het exemplaar geregistreerd bij uw Red Hat-abonnement. Dit betekent dat u een van uw rechten gebruikt. 

Deze PAYG-installatiekopie leidt tot 'dubbele facturering'. U kunt dit probleem voorkomen door uw eigen RHEL-installatiekopie te bouwen. Lees het Red Hat Knowledge Base-artikel (KB) [Hoe u een RHEL VM kunt inrichten voor Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure) voor meer informatie. Of activeer de [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-installatiekopie.

Zie [Red Hat Enterprise Linux-prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) voor meer informatie over de prijzen voor PAYG virtuele machines. Als u RHEL in het PAYG-model wilt gebruiken, hebt u een Azure-abonnement met de opgegeven betalingswijze nodig voor [RHEL 7.7 op Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) of [RHEL 8.0 op Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Voor deze aanbiedingen moet een betalingswijze worden opgegeven in het Azure-abonnement.

#### <a name="using-rhel-os-with-the-byos-model"></a>Het RHEL-besturingssysteem gebruiken met het BYOS-model

Als u BYOS wilt gebruiken voor het RHEL-besturingssysteem, moet u een geldig Red Hat-abonnement hebben met rechten om het RHEL-besturingssysteem in Azure te gebruiken. Zorg dat u aan de volgende vereisten voldoet voordat u het RHEL-besturingssysteem met het BYOS-model implementeert:

1. Zorg ervoor dat er rechten voor het RHEL-besturingssysteem en JBoss EAP zijn gekoppeld aan uw Red Hat-abonnement.
2. Geef uw Azure-abonnements-id toestemming voor het gebruik van RHEL BYOS-installatiekopieën. Volg de [Documentatie voor Red Hat Subscription Management](https://access.redhat.com/documentation/red_hat_subscription_management/1/) om het proces te voltooien. Dit omvat de volgende stappen:

   1. Schakel Microsoft Azure in als provider in het Red Hat Cloud Access Dashboard.

   1. Voeg uw Azure-abonnements-id's toe.

   1. Schakel nieuwe producten in voor Cloud Access op Microsoft Azure.
    
   1. Activeer Red Hat Gold-installatiekopieën voor uw Azure-abonnement. Zie [Red Hat Gold-installatiekopieën op Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access) voor meer informatie.

   1. Wacht tot Red Hat Gold-installatiekopieën beschikbaar zijn in uw Azure-abonnement. Deze installatiekopieën zijn doorgaans binnen drie uur na verzending beschikbaar.
    
3. Accepteer de Azure Marketplace-voorwaarden voor RHEL BYOS-installatiekopieën. U kunt dit proces voltooien door de volgende Azure CLI-opdrachten uit te voeren. Raadpleeg de documentatie over [RHEL BYOS Gold-installatiekopieën in Azure](./byos.md) voor meer informatie. Het is belangrijk dat u de nieuwste versie van Azure CLI uitvoert.

   1. Open een Azure CLI-sessie en verifieer met uw Azure-account. Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli) voor hulp.

   1. Controleer of de RHEL BYOS-installatiekopieën beschikbaar zijn in uw abonnement door de volgende CLI-opdracht uit te voeren. Als u hier geen resultaten krijgt, zorgt u ervoor dat uw Azure-abonnement is geactiveerd voor RHEL BYOS-installatiekopieën.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Voer de volgende opdracht uit om de Azure Marketplace-voorwaarden voor respectievelijk RHEL 7.7 BYOS en RHEL 8.0 BYOS te accepteren:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Uw abonnement is nu klaar om RHEL 7.7 of 8.0 BYOS te implementeren op virtuele machines van Azure.

#### <a name="using-jboss-eap-with-the-byos-model"></a>JBoss EAP gebruiken met het BYOS-model

JBoss EAP is alleen beschikbaar in Azure via het BYOS-model. Wanneer u deze sjabloon implementeert, moet u uw RHSM-referenties opgeven samen met de RHSM-pool-id met geldige EAP-rechten. Als u geen EAP-rechten hebt, moet u een [JBoss EAP-evaluatieabonnement verkrijgen](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) voordat u aan de slag gaat.

## <a name="deployment-options"></a>Implementatieopties

U kunt de sjabloon op de volgende manieren implementeren:

- **PowerShell**. Voer de volgende opdrachten uit om de sjabloon te implementeren: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Zie de [documentatie over PowerShell](/powershell/azure/) voor meer informatie over het installeren en configureren van Azure PowerShell.  

- **Azure CLI**. Voer de volgende opdrachten uit om de sjabloon te implementeren:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Bekijk [De CLI installeren](/cli/azure/install-azure-cli) voor meer informatie over het installeren en configureren van de Azure CLI.

- **Azure-portal**. U kunt implementeren op Azure Portal door naar de Azure-quickstart-sjablonen te gaan zoals vermeld in de volgende sectie. Zodra u in de quickstart bent, selecteert u de knop **Implementeren naar Azure** of **Bladeren op GitHub**.

## <a name="azure-quickstart-templates"></a>Azure-quickstartsjablonen

U kunt beginnen met een van de volgende quickstart-sjablonen voor JBoss EAP op RHEL die voldoet aan uw implementatiedoel:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP op RHEL (zelfstandige virtuele machine)</a>. Hiermee wordt een webtoepassing met de naam JBoss-EAP op Azure geïmplementeerd naar JBoss EAP 7.2 of 7.3 dat wordt uitgevoerd op RHEL 7.7 of 8.0 VM.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP op RHEL (meerdere geclusterde virtuele machines)</a>. Hiermee wordt een webtoepassing geïmplementeerd met de naam eap-session-replication naar een JBoss EAP 7.2- of 7.3-cluster dat wordt uitgevoerd op *n* RHEL 7.7 of 8.0 virtuele machines. De gebruiker bepaalt zelf de waarde van *n*. Alle virtuele machines worden toegevoegd aan de back-endpool van een load balancer.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP op Red Hat Enterprise Linux (geclusterde virtuele-machineschaalset)</a>. Hiermee wordt een webtoepassing geïmplementeerd met de naam eap-session-replication naar een JBoss EAP 7.2- of 7.3-cluster dat wordt uitgevoerd op RHEL 7.7 of 8.0 virtuele-machineschaalsets.

## <a name="resource-links"></a>Resource-links

* [Azure Hybrid Benefit](../../windows/hybrid-use-benefit-licensing.md)
* [Een Java-app voor Azure App Service configureren](../../../app-service/configure-language-java.md)
* [JBoss EAP op Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP op Azure App Service Linux](../../../app-service/quickstart-java.md)
* [JBoss EAP op Azure App Service implementeren](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Meer informatie over [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Meer informatie over [Red Hat-abonnementsbeheer](https://access.redhat.com/products/red-hat-subscription-management).
* Meer informatie over [Red Hat-werkbelastingen op Azure](./overview.md).
* [JBoss EAP implementeren op een RHEL VM of virtuele-machineschaalset van Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* [JBoss EAP implementeren op een RHEL VM of virtuele-machineschaalset op basis van Azure-quickstart-sjablonen](https://aka.ms/Quickstart-JBoss-EAP).