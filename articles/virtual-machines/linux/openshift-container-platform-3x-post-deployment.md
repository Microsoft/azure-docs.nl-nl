---
title: Open Shift container platform 3,11 in azure na implementatie taken
description: Aanvullende taken voor nadat een open Shift container platform 3,11-cluster is geïmplementeerd.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: dd967ad08b628f9073edfe548033f7e97845d047
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016060"
---
# <a name="post-deployment-tasks"></a>Taken na de implementatie

Nadat u een open Shift-cluster hebt geïmplementeerd, kunt u aanvullende items configureren. In dit artikel komen de volgende onderwerpen aan bod:

- Eenmalige aanmelding configureren met behulp van Azure Active Directory (Azure AD)
- Azure Monitor logboeken configureren voor het bewaken van open Shift
- Metrische gegevens en logboek registratie configureren
- Open Service Broker voor Azure installeren (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Eenmalige aanmelding configureren met behulp van Azure Active Directory

Als u Azure Active Directory voor verificatie wilt gebruiken, moet u eerst een Azure AD-App-registratie maken. Dit proces bestaat uit twee stappen: het maken van de app-registratie en het configureren van machtigingen.

### <a name="create-an-app-registration"></a>Een app-registratie maken

In deze stappen wordt gebruikgemaakt van de Azure CLI om de app-registratie te maken en de gebruikers interface (Portal) om de machtigingen in te stellen. Als u de app-registratie wilt maken, hebt u de volgende vijf stukjes informatie nodig:

- Weergave naam: naam van de app-registratie (bijvoorbeeld OCPAzureAD)
- Start pagina: URL van open Shift-console (bijvoorbeeld `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Id-URI: open Shift-console-URL (bijvoorbeeld `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Antwoord-URL: open bare URL voor Master en de naam van de app-registratie (bijvoorbeeld `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Wacht woord: beveiligd wacht woord (gebruik een sterk wacht woord)

In het volgende voor beeld wordt een app-registratie gemaakt met behulp van de voor gaande informatie:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Als de opdracht is voltooid, krijgt u een JSON-uitvoer vergelijkbaar met:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Noteer de eigenschap appId die wordt geretourneerd door de opdracht voor een latere stap.

In Azure Portal:

1. Selecteer **Azure Active Directory**  >  **app-registratie**.
2. Zoek naar de registratie van uw app (bijvoorbeeld OCPAzureAD).
3. Klik in de resultaten op de app-registratie.
4. Selecteer onder **instellingen** de optie **vereiste machtigingen**.
5. Selecteer **toevoegen** onder **vereiste machtigingen**.

   ![App-registratie](media/openshift-post-deployment/app-registration.png)

6. Klik op stap 1: Selecteer API en klik vervolgens op **Windows Azure Active Directory (Microsoft. Azure. ActiveDirectory)**. Klik onderaan op **selecteren** .

   ![App-registratie selecteren-API](media/openshift-post-deployment/app-registration-select-api.png)

7. Selecteer bij stap 2: machtigingen selecteren, **Aanmelden en gebruikers profiel lezen** onder **gedelegeerde machtigingen** en klik vervolgens op **selecteren**.

   ![App-registratie toegang](media/openshift-post-deployment/app-registration-access.png)

8. Selecteer **Gereed**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Open Shift configureren voor Azure AD-verificatie

Als u openshift wilt configureren voor het gebruik van Azure AD als een verificatie provider, moet het/etc/origin/master/Master-config.yaml-bestand worden bewerkt op alle hoofd knooppunten.

Zoek de Tenant-ID met behulp van de volgende CLI-opdracht:

```azurecli
az account show
```

Zoek in het bestand yaml de volgende regels:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Voeg de volgende regels toe direct na de voor gaande regels:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Zorg ervoor dat de tekst correct wordt uitgelijnd onder identityProviders. Zoek de Tenant-ID met behulp van de volgende CLI-opdracht: ```az account show```

Start de open Shift-Master services opnieuw op alle hoofd knooppunten:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

In de open Shift-console ziet u nu twee opties voor verificatie: htpasswd_auth en [app-registratie].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Open SHIFT met Azure Monitor-logboeken bewaken

Er zijn drie manieren om de Log Analytics-agent toe te voegen aan open SHIFT.
- De Log Analytics-agent voor Linux rechtstreeks op elk open Shift-knoop punt installeren
- Azure Monitor VM-extensie inschakelen voor elk open Shift-knoop punt
- De Log Analytics-agent installeren als een open Shift-daemon-set

Lees de volledige [instructies](../../azure-monitor/insights/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) voor meer informatie.

## <a name="configure-metrics-and-logging"></a>Metrische gegevens en logboek registratie configureren

Op basis van de vertakking kunnen de Azure Resource Manager sjablonen voor open Shift container platform en OKD invoer parameters bieden voor het inschakelen van metrische gegevens en logboek registratie als onderdeel van de installatie.

De aanbieding open Shift container platform Marketplace biedt ook een optie voor het inschakelen van metrische gegevens en logboek registratie tijdens de Cluster installatie.

Als metrieken/logboek registratie niet is ingeschakeld tijdens de installatie van het cluster, kunnen ze na het feit eenvoudig worden ingeschakeld.

### <a name="azure-cloud-provider-in-use"></a>Azure-Cloud provider in gebruik

SSH naar het Bastion-knoop punt of het eerste hoofd knooppunt (op basis van de sjabloon en vertakking in gebruik) met behulp van de referenties die zijn verschaft tijdens de implementatie. Geef de volgende opdracht:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>De Azure-Cloud provider wordt niet gebruikt

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Open Service Broker voor Azure installeren (OSBA)

Met Service Broker openen voor Azure of OSBA kunt u Azure Cloud Services rechtstreeks inrichten vanuit open SHIFT. OSBA in een open Service Broker API-implementatie voor Azure. De open Service Broker-API is een specificatie die een gemeen schappelijke taal definieert voor cloud providers die native toepassingen in de Cloud kunnen gebruiken om Cloud Services zonder vergren deling te beheren.

Volg de instructies die u hier vindt om OSBA te installeren op open Shift: https://github.com/Azure/open-service-broker-azure#openshift-project-template . 
> [!NOTE]
> Voer de stappen in het gedeelte open Shift-project sjabloon alleen uit en niet de volledige installatie sectie.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met open Shift container platform](https://docs.openshift.com)
