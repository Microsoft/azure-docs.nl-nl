---
title: Azure App Configuration integreren met Kubernetes-implementatie met Helm
description: Ontdek hoe u dynamische configuraties in Kubernetes-implementatie gebruikt met Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 4e38366ddcee07f38ca390acf9d580b8764c1c00
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979824"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integreren met Kubernetes-implementatie met Helm

Helm biedt een manier om toepassingen die in Kubernetes worden uitgevoerd te definiëren, installeren en upgraden. Een Helm-grafiek bevat de informatie die nodig is om een exemplaar van een Kubernetes-toepassing te maken. De configuratie wordt buiten de grafiek zelf opgeslagen, in een bestand genaamd *values.yaml*. 

Tijdens het releaseproces voegt Helm de grafiek samen met de juiste configuratie om de toepassing uit te voeren. Er kan bijvoorbeeld naar variabelen die in *values.yaml* zijn gedefinieerd, worden verwezen met omgevingsvariabelen in de actieve containers. Helm ondersteunt ook het maken van Kubernetes-geheimen, die als gegevensvolumes kunnen worden gekoppeld of als omgevingsvariabelen kunnen worden weergegeven.

U kunt de waarden die in *values.yaml* zijn opgeslagen, overschrijven door aanvullende YAML-gebaseerde configuratiebestanden op de opdrachtregel op te geven wanneer Helm wordt uitgevoerd. Azure App Configuration ondersteunt het exporteren van configuratiewaarden naar YAML-bestanden. Door deze exportmogelijkheid in uw implementatie te integreren, kunnen uw Kubernetes-toepassingen configuratiewaarden benutten die in App Configuration zijn opgeslagen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Gebruik waarden uit App Configuration wanneer u een toepassing in Kubernetes implementeert met Helm.
> * Maak een Kubernetes-geheim op basis van een sleutelkluisverwijzing in App Configuration.

In deze zelfstudie wordt ervan uitgegaan dat u basisbegrip hebt van het beheren van Kubernetes met Helm. Meer informatie over het installeren van toepassingen met Helm in [Azure Kubernetes Service](../aks/kubernetes-helm.md).

## <a name="prerequisites"></a>Vereisten

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli) (versie 2.4.0 of hoger) installeren
- [Helm](https://helm.sh/docs/intro/install/) (versie 2.14.0 of hoger) installeren
- Een Kubernetes-cluster.

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Configuratieverkenner** > **Maken** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | settings.color | Wit |
    | settings.message | Gegevens van Azure App Configuration |

    Laat **Label** en **Inhoudstype** nog even leeg.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een sleutelkluisverwijzing toevoegen aan App Configuration
1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en voeg een geheim aan [Sleutelkluis](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) toe met de naam **Wachtwoord** en de waarde **mijnwachtwoord**. 
2. Selecteer het App Configuration-archiefexemplaar dat u in de vorige sectie hebt gemaakt.

3. Selecteer **Configuratieverkenner**.

4. Selecteer **+ Maken** > **Sleutelkluisverwijzing** en geef de volgende waarden op:
    - **Sleutel**: Selecteer **secrets.password**.
    - **Label**: Laat deze waarde leeg.
    - **Abonnement**, **Resourcegroep** en **Sleutelkluis**: Voer de waarden in die overeenkomen met de waarden in de sleutelkluis die u in de vorige stap hebt gemaakt.
    - **Geheim**: Selecteer het geheim genaamd **Wachtwoord** dat u in de vorige sectie hebt gemaakt.

## <a name="create-helm-chart"></a>Helm-grafiek maken ##
Maak eerst een Helm-voorbeeldgrafiek met de volgende opdracht
```console
helm create mychart
```

Helm maakt een nieuwe directory genaamd ‘mychart’ met de structuur die hieronder wordt weergegeven. 

> [!TIP]
> Volg deze [grafiekhandleiding](https://helm.sh/docs/chart_template_guide/getting_started/) voor meer informatie.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Werk vervolgens de sectie **spec:template:spec:containers** van het bestand *deployment.yaml* bij. Het volgende fragment voegt twee omgevingsvariabelen aan de container toe. U zult de waarden ervan dynamisch instellen tijdens de implementatie.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Na de update moet het volledige *deployment.yaml*-bestand eruitzien zoals hieronder.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Als u gevoelige gegevens als Kubernetes-geheimen wilt opslaan, voegt u een *secrets.yaml*-bestand toe onder de map met sjablonen.

> [!TIP]
> Meer informatie over het gebruiken van [Kubernetes-geheimen](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Tot slot werkt u het bestand *values.yaml* met de volgende inhoud bij om desgewenst standaardwaarden van de configuratie-instellingen en geheimen te bieden waarnaar wordt verwezen in de bestanden *deployment.yaml* en *secrets.yaml*. De daadwerkelijke waarden ervan worden overschreven door configuratie die uit App Configuration wordt opgehaald.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Configuratie uit App Configuration doorgeven aan Helm-installatie ##
Download de configuratie uit App Configuration eerst naar een *myConfig.yaml*-bestand. Gebruik een sleutelfilter om alleen de sleutels te downloaden die met **settings.** beginnen. Als het sleutelfilter in uw geval niet voldoende is om sleutels uit te sluiten van sleutelkluisverwijzingen, kunt u het argument **--skip-keyvault** gebruiken om ze uit te sluiten. 

> [!TIP]
> Meer informatie over de [exportopdracht](/cli/azure/appconfig/kv#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Download vervolgens geheimen naar een bestand genaamd *mySecrets.yaml*. Het opdrachtregelargument **--resolve-keyvault** lost de sleutelkluisverwijzingen op door de daadwerkelijke waarden op te halen in Sleutelkluis. U moet deze opdracht uitvoeren met aanmeldingsgegevens die toegangsmachtigingen voor de bijbehorende sleutelkluis hebben.

> [!WARNING]
> Aangezien dit bestand gevoelige gegevens bevat, moet u het veilig opslaan en moet u het verwijderen wanneer u het niet meer nodig hebt.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Gebruik het argument **-f** van de Helm-upgrade om de twee configuratiebestanden door te geven die u hebt gemaakt. Ze overschrijven de configuratiewaarden die in *values.yaml* zijn gedefinieerd met de waarden die uit App Configuration zijn geëxporteerd.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

U kunt ook het argument **--set** voor de Helm-upgrade gebruiken om letterlijke sleutelwaarden door te geven. Het gebruik van het argument **--set** is een goede manier om permanente gevoelige gegevens op schijf te voorkomen. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Verifieer dat configuraties en geheimen succesvol zijn ingesteld door het [Kubernetes-dashboard](../aks/kubernetes-dashboard.md) te openen. U ziet dat de waarden voor **color** en **message** uit App Configuration zijn ingevuld in de omgevingsvariabelen van de container.

![Quickstart voor het lokaal starten van een app](./media/kubernetes-dashboard-env-variables.png)

Eén geheim, **password**, dat als sleutelkluisverwijzing in App Configuration is opgeslagen, is ook toegevoegd aan Kubernetes-geheimen. 

![Schermopname met het wachtwoord gemarkeerd in de sectie Data.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure App Configuration-gegevens geëxporteerd die moeten worden gebruikt in een Kubernetes-implementatie met Helm. Voor meer informatie over het gebruik van App Configuration gaat u verder naar de Azure CLI-voorbeelden.

> [!div class="nextstepaction"]
> [Azure-CLI](/cli/azure/appconfig)
