---
title: 'Zelfstudie: verbinding maken met een Azure Red Hat OpenShift 4-cluster'
description: Meer informatie over verbinding maken met een Microsoft Azure Red Hat OpenShift-cluster
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 31abf5008c082fbb54e873b0502876c2fce5d440
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635962"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Zelfstudie: Verbinding maken met een Azure Red Hat OpenShift 4-cluster

In deze zelfstudie, deel twee van drie, maakt u verbinding met een Azure Red Hat OpenShift-cluster met OpenShift 4 als kubeadmin-gebruiker via de OpenShift-webconsole. In deze zelfstudie leert u procedures om het volgende te doen:
> [!div class="checklist"]
> * `kubeadmin`Referenties voor uw cluster verkrijgen
> * De OpenShift CLI installeren
> * Verbinding maken met een Azure Red Hat OpenShift-cluster met de OpenShift CLI

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een Azure Red Hat OpenShift-cluster gemaakt. Als u deze stappen niet hebt uitgevoerd en u deze zelfstudie toch wilt volgen, begint u met [Zelfstudie 1: Een Azure Red Hat OpenShift 4-cluster maken](tutorial-create-cluster.md).

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

U kunt zich aanmelden bij het cluster met behulp van de `kubeadmin`-gebruiker.  Voer de volgende opdracht uit om het wachtwoord voor de `kubeadmin`-gebruiker te vinden.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

In de volgende voorbeeld ziet u wat het wachtwoord in `kubeadminPassword` is.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

U kunt de URL van de clusterconsole vinden door de volgende opdracht uit te voeren, die eruit ziet als `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`.

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Start de console-URL in een browser en meld u aan met de referenties van `kubeadmin`.

![Aanmeldingsscherm voor Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>De OpenShift CLI installeren

Zodra u bent aangemeld bij de OpenShift-webconsole, klikt u op **?** in de rechterbovenhoek en klikt u vervolgens op **Opdrachtregel hulpprogrammarogramma's**. Download de versie die geschikt is voor uw computer.

![Schermopname van de optie Opdrachtregelprogramma's in de lijst wanneer u het ? selecteert pictogram.](media/aro4-download-cli.png)

U kunt ook de meest recente versie van de CLI die geschikt is voor uw computer downloaden van <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>.

Als u de opdrachten uitvoert op de Azure Cloud Shell, downloadt u de nieuwste OpenShift 4 CLI voor Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Verbinding maken met behulp van OpenShift CLI

Haal het adres van de API-server op.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Meld u aan bij de API-server van het OpenShift-cluster met de volgende opdracht. Vervang **\<kubeadmin password>** door het wachtwoord dat u zojuist hebt opgehaald.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * `kubeadmin`Referenties voor uw cluster verkrijgen
> * De OpenShift CLI installeren
> * Verbinding maken met een Azure Red Hat OpenShift-cluster met de OpenShift CLI

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)