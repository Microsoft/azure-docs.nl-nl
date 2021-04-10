---
title: Open Shift container platform 3,11 Self-Managed Marketplace-aanbieding implementeren in azure
description: Implementeer het Self-Managed Marketplace-aanbod van open Shift container platform 3,11 in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670435"
---
# <a name="configure-prerequisites"></a>Vereisten configureren

Voordat u de Marketplace-aanbieding gebruikt voor het implementeren van een zelfbeheerde open Shift container platform 3,11-cluster in azure, moeten enkele vereiste onderdelen worden geconfigureerd.  Raadpleeg het artikel openstaande [vereisten](./openshift-container-platform-3x-prerequisites.md) voor instructies voor het maken van een SSH-sleutel (zonder een wachtwoordzin), Azure-sleutel kluis, sleutel kluis geheim en een service-principal.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implementeren met behulp van de Marketplace-aanbieding

De eenvoudigste manier om een self-managed open Shift container platform 3,11-cluster in azure te implementeren, is de [Azure Marketplace-aanbieding](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)te gebruiken.

Deze optie is de eenvoudigste, maar heeft ook beperkte aanpassings mogelijkheden. De Marketplace-aanbieding implementeert Open Shift container platform 3.11.82 en bevat de volgende configuratie opties:

- **Hoofd knooppunten**: drie (3) hoofd knooppunten met een configureerbaar exemplaar type.
- **Infra structuur knooppunten**: drie (3) infra structuur knooppunten met een configureerbaar exemplaar type.
- **Knoop punten**: het aantal knoop punten (tussen 1 en 9) en het exemplaar type kunnen worden geconfigureerd.
- **Schijf type**: Managed disks worden gebruikt.
- **Netwerken**: ondersteuning voor nieuw of bestaand netwerk en aangepast CIDR-bereik.
- **CNS**: CNS kan worden ingeschakeld.
- **Metrische gegevens**: Hawkular metrieken kunnen worden ingeschakeld.
- **Logboek registratie**: EFK-logboek registratie kan worden ingeschakeld.
- **Azure-Cloud provider**: standaard ingeschakeld, kan worden uitgeschakeld.

Klik in de linkerbovenhoek van de Azure Portal op **een resource maken**, voer ' open Shift container platform ' in het zoekvak in en druk op ENTER.

   ![Nieuwe resource zoeken](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

De resultaten pagina wordt geopend met het **Red Hat open Shift container Platform 3,11 zelf beheerd** in de lijst. 

   ![Zoek resultaat van nieuwe resource](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Klik op de aanbieding om de details van de aanbieding te bekijken. Klik op **maken** om deze aanbieding te implementeren. De gebruikers interface voor het invoeren van de vereiste para meters wordt weer gegeven. Het eerste scherm is de Blade **basis beginselen** .

   ![Titel pagina aanbieding](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Basisinstellingen**

Als u hulp nodig hebt bij een van de invoer parameters, plaatst u de muis aanwijzer op de ***i*** naast de parameter naam.

Voer waarden in voor de invoer parameters en klik op **OK**.

| Invoer parameter | Parameter beschrijving |
|-----------------------|-----------------|
| Gebruikers naam voor de VM-beheerder | De gebruikers beheerder die moet worden gemaakt op alle VM-exemplaren |
| Open bare SSH-sleutel voor gebruiker met beheerders rechten | Open bare SSH-sleutel die wordt gebruikt voor aanmelding bij de virtuele machine-mag geen wachtwoordzin hebben |
| Abonnement | Azure-abonnement voor het implementeren van het cluster in |
| Resourcegroep | Maak een nieuwe resource groep of selecteer een bestaande lege resource groep voor cluster resources |
| Locatie | Azure-regio voor het implementeren van het cluster in |

   ![Blade basis beginselen aanbieding](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Infrastructuur instellingen**

Voer waarden in voor de invoer parameters en klik op **OK**.

| Invoer parameter | Parameter beschrijving |
|-----------------------|-----------------|
| Voor voegsel van OCP-cluster naam | Het cluster voorvoegsel dat wordt gebruikt voor het configureren van hostnamen voor alle knoop punten. Tussen 1 en 20 tekens |
| Grootte van hoofd knooppunt | Accepteer de standaard grootte van de virtuele machine of klik op **grootte wijzigen** om een andere VM-grootte te selecteren.  Selecteer de juiste VM-grootte voor de werk belasting |
| Grootte van infrastructuur knooppunt | Accepteer de standaard grootte van de virtuele machine of klik op **grootte wijzigen** om een andere VM-grootte te selecteren.  Selecteer de juiste VM-grootte voor de werk belasting |
| Aantal toepassings knooppunten | Accepteer de standaard grootte van de virtuele machine of klik op **grootte wijzigen** om een andere VM-grootte te selecteren.  Selecteer de juiste VM-grootte voor de werk belasting |
| Grootte van toepassings knooppunt | Accepteer de standaard grootte van de virtuele machine of klik op **grootte wijzigen** om een andere VM-grootte te selecteren.  Selecteer de juiste VM-grootte voor de werk belasting |
| Grootte van Bastion-host | Accepteer de standaard grootte van de virtuele machine of klik op **grootte wijzigen** om een andere VM-grootte te selecteren.  Selecteer de juiste VM-grootte voor de werk belasting |
| Nieuwe of bestaande Virtual Network | Een nieuw vNet maken (standaard) of een bestaand vNet gebruiken |
| Standaard CIDR-instellingen kiezen of IP-bereik (CIDR) aanpassen | Accepteer standaard CIDR-bereiken of selecteer **aangepast IP-bereik** en voer aangepaste CIDR-gegevens in.  Met de standaard instellingen wordt vNet gemaakt met CIDR van 10.0.0.0/14, hoofd-subnet met 10.1.0.0/16, infra structuur subnet met 10.2.0.0/16 en Compute en CNS-subnet met 10.3.0.0/16 |
| Naam van de resource groep Key Vault | De naam van de resource groep die de Key Vault bevat |
| Key Vault naam | De naam van de Key Vault die het geheim bevat met de persoonlijke SSH-sleutel.  Alleen alfanumerieke tekens en streepjes zijn toegestaan en moeten tussen de 3 en 24 tekens lang zijn |
| Geheime naam | De naam van het geheim dat de persoonlijke SSH-sleutel bevat.  Alleen alfanumerieke tekens en streepjes zijn toegestaan |

   ![Blade aanbod infrastructuur](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Grootte wijzigen**

Als u een andere VM-grootte wilt selecteren, klikt u op ***grootte wijzigen** _.  Het venster VM-selectie wordt geopend.  Selecteer de gewenste VM-grootte en klik op _ * Selecteer * *.

   ![VM-grootte selecteren](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Bestaande Virtual Network**

| Invoer parameter | Parameter beschrijving |
|-----------------------|-----------------|
| Bestaande Virtual Network naam | De naam van het bestaande vNet |
| Subnetnaam voor hoofd knooppunten | De naam van het bestaande subnet voor hoofd knooppunten.  Moet ten minste 16 IP-adressen bevatten en voldoen aan RFC 1918 |
| Subnetnaam voor infra structuur knooppunten | De naam van het bestaande subnet voor infra structuur knooppunten.  Moet ten minste 32 IP-adressen bevatten en de volgende RFC-1918 |
| De subnetnaam voor Compute-en CNS-knoop punten | De naam van het bestaande subnet voor Compute-en CNS-knoop punten.  Moet ten minste 32 IP-adressen bevatten en de volgende RFC-1918 |
| Resource groep voor de bestaande Virtual Network | De naam van de resource groep die het bestaande vNet bevat |

   ![Infra structuur bestaande vnet aanbod](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Aangepast IP-bereik**

| Invoer parameter | Parameter beschrijving |
|-----------------------|-----------------|
| Adres bereik voor de Virtual Network | Aangepaste CIDR voor het vNet |
| Adres bereik voor het subnet dat de hoofd knooppunten bevat | Aangepaste CIDR voor Master-subnet |
| Adres bereik voor het subnet dat de infrastructuur knooppunten bevat | Aangepaste CIDR voor infra structuur-subnet |
| Adres bereik voor subnet dat de compute-en CNS-knoop punten bevat | Aangepaste CIDR voor de compute-en CNS-knoop punten |

   ![Aangepast IP-bereik aanbod infrastructuur](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Voer waarden in voor de invoer parameters en klik op **OK**

| Invoer parameter | Parameter beschrijving |
|-----------------------|-----------------|
| Gebruikers wachtwoord voor open Shift-beheerder | Wacht woord voor de eerste open Shift-gebruiker.  Deze gebruiker is ook de Cluster beheerder |
| Gebruikers wachtwoord voor open Shift-beheerder bevestigen | Typ het wacht woord voor de gebruikers account voor open Shift-beheerder opnieuw |
| Gebruikers naam voor Red Hat Subscription Manager | De gebruikers naam voor toegang tot uw Red Hat-abonnement of organisatie-ID.  Deze referentie wordt gebruikt om het RHEL-exemplaar te registreren bij uw abonnement en wordt niet opgeslagen door micro soft of Red Hat |
| Gebruikers wachtwoord voor Red Hat Subscription Manager | Wacht woord voor toegang tot uw Red Hat-abonnement of activerings sleutel.  Deze referentie wordt gebruikt om het RHEL-exemplaar te registreren bij uw abonnement en wordt niet opgeslagen door micro soft of Red Hat |
| Open Shift-pool-ID van Red Hat Subscription Manager | De groeps-ID die het recht open Shift container platform bevat. Zorg ervoor dat u voldoende rechten hebt van open Shift container platform voor de installatie van het cluster |
| Groeps-ID van open ploeg voor Red Hat Subscription Manager voor Broker/Master-knoop punten | De groeps-ID die open Shift container platform-rechten bevat voor Broker/Master-knoop punten. Zorg ervoor dat u voldoende rechten hebt van open Shift container platform voor de installatie van het cluster. Als u geen Broker/Master-groeps-ID gebruikt, voert u de groeps-ID in voor toepassings knooppunten |
| Azure-Cloud provider configureren | Open Shift configureren voor het gebruik van de Azure-Cloud provider. Nodig als u Azure Disk attach gebruikt voor permanente volumes.  De standaard waarde is ja |
| GUID van Azure AD-Service-Principal-client-ID | ID-GUID van Azure AD-Service-Principal, ook wel ' AppID ' genoemd. Alleen nodig als Azure-Cloud provider configureren op **Ja** is ingesteld |
| Geheim van Azure AD-Service-Principal client-ID | Azure AD Service Principal client-ID Secret. Alleen nodig als Azure-Cloud provider configureren op **Ja** is ingesteld |
 
   ![Blade open Shift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Aanvullende instellingen**

Op de Blade extra instellingen kan CNS worden geconfigureerd voor glusterfs-opslag, logboek registratie, metrische gegevens en een router subdomein.  Deze opties worden niet standaard geïnstalleerd en er wordt gebruikgemaakt van nip.io als het subdomein van de router voor test doeleinden. Door CNS in te scha kelen, worden drie extra reken knooppunten geïnstalleerd met drie bijkomende gekoppelde schijven die glusterfs van de peul zullen hosten.  

Voer waarden in voor de invoer parameters en klik op **OK**

| Invoer parameter | Parameter beschrijving |
|-----------------------|-----------------|
| Native opslag van de container (CNS) configureren | Hiermee installeert u CNS in het open Shift-cluster en schakelt u dit in als opslag. Is standaard als Azure-provider is uitgeschakeld |
| Cluster logboek registratie configureren | Hiermee wordt de EFK-logboek functionaliteit in het cluster geïnstalleerd.  Infra structuur knooppunten op de juiste wijze te hosten voor EFK. |
| Metrische gegevens voor het cluster configureren | Hiermee worden metrische Hawkular-gegevens in het open Shift-cluster geïnstalleerd.  Infra structuur knooppunten op de juiste wijze te hosten voor Hawkular metrieken van peulen |
| Standaard router subdomein | Selecteer nipio voor testen of aangepast om uw eigen subdomein voor productie in te voeren |
 
   ![Extra Blade bieden](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Aanvullende instellingen-extra para meters**

| Invoer parameter | Parameter beschrijving |
|-----------------------|-----------------|
| CNS Knooppunt grootte | Accepteer de standaard grootte van het knoop punt of selecteer **grootte wijzigen** om een nieuwe VM-grootte te selecteren |
| Voer uw aangepaste subdomein in | Het aangepaste routerings domein dat moet worden gebruikt voor het weer geven van toepassingen via de router in het open Shift-cluster.  Zorg ervoor dat u de juiste DNS-vermelding voor joker tekens maakt] |
 
   ![Extra CNS-installatie aanbieden](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Samenvatting**

Validatie vindt plaats in deze fase om te controleren of het kern quotum voldoende is voor het implementeren van het totale aantal Vm's dat is geselecteerd voor het cluster.  Alle ingevoerde para meters bekijken.  Als de invoer acceptabel is, klikt u op **OK** om door te gaan.

   ![Blade overzicht aanbieding](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Kopen**

Bevestig de contact gegevens op de pagina kopen en klik op **kopen** om akkoord te gaan met de gebruiks voorwaarden en de implementatie van het open Shift container platform-cluster te starten.

   ![Blade aankoop aanbieding](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Verbinding maken met het open Shift-cluster

Wanneer de implementatie is voltooid, haalt u de verbinding op in de sectie uitvoer van de implementatie. Maak verbinding met de open Shift-console met uw browser met behulp van de open **SHIFT console-URL**. u kunt ook SSH-naar de bastion-host. Hieronder volgt een voor beeld waarin de gebruikers naam van de beheerder clusteradmin is en de Bastion open bare IP DNS FQDN is bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [AZ Group delete](/cli/azure/group) om de resource groep, open Shift-cluster en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Volgende stappen

- [Taken na de implementatie](./openshift-container-platform-3x-post-deployment.md)
- [Problemen met openshift-implementatie in azure oplossen](./openshift-container-platform-3x-troubleshooting.md)
- [Aan de slag met open Shift container platform](https://docs.openshift.com)
- 
