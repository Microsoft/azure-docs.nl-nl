---
title: Ingebouwd containerregister voor Azure Red Hat OpenShift 4 configureren
description: Ingebouwd containerregister voor Azure Red Hat OpenShift 4 configureren
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636375"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Ingebouwd containerregister voor Azure Red Hat OpenShift 4 configureren

Azure Red Hat open Shift biedt een geïntegreerde container installatie kopie met de naam Open [shift container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) waarmee de mogelijkheid om automatisch nieuwe afbeeldings opslagplaatsen op aanvraag in te richten. Dit biedt gebruikers een ingebouwde locatie voor hun toepassings builds om de resulterende installatie kopieën te pushen.

In dit artikel configureert u het ingebouwde REGI ster van de container installatie kopie voor een Azure Red Hat open Shift (ARO) 4-cluster. U leert het volgende:

> [!div class="checklist"]
> * Azure AD instellen
> * OpenID Connect Connect instellen
> * Toegang tot het ingebouwde container installatie kopie register

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand ARO-cluster. Als u een ARO-cluster nodig hebt, raadpleegt u de ARO-zelf studie [een Azure Red Hat open Shift 4-cluster maken](./tutorial-create-cluster.md). Zorg ervoor dat u het cluster met het `--pull-secret` argument maakt naar `az aro create` .  Dit is nodig voor het configureren van Azure Active Directory-verificatie en het ingebouwde container register.

Nadat u uw cluster hebt gemaakt, maakt u verbinding met het cluster door de stappen in [verbinding maken met een Azure Red Hat open Shift 4-cluster](./tutorial-connect-cluster.md)te volgen.
   * Volg de stappen in ' de openhouds-CLI installeren ', omdat de `oc` opdracht later in dit artikel wordt gebruikt.
   * Noteer de URL van de cluster console, die er als volgt uitziet `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . De waarden voor `<random>` en worden `<region>` verderop in dit artikel gebruikt.
   * Noteer de `kubeadmin` referenties. Ze worden ook later in dit artikel gebruikt.

### <a name="configure-azure-active-directory-authentication"></a>Azure Active Directory-verificatie configureren 

Azure Active Directory (Azure AD) implementeert OpenID Connect Connect (OIDC). Met OIDC kunt u Azure AD gebruiken om u aan te melden bij het ARO-cluster. Volg de stappen in [configure Azure Active Directory Authentication](configure-azure-ad-cli.md) om uw cluster in te stellen.

## <a name="access-the-built-in-container-image-registry"></a>Toegang tot het ingebouwde container installatie kopie register

Nu u de verificatie methoden hebt ingesteld op het ARO-cluster, kunt u toegang tot het ingebouwde REGI ster inschakelen.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>De Azure AD-gebruiker definiëren als beheerder

1. Meld u met de referenties van een Azure AD-gebruiker aan bij de open Shift-webconsole vanuit uw browser. We maken gebruik van de open Shift OpenID Connect-verificatie voor Azure Active Directory om de beheerder te definiëren met behulp van OpenID Connect.

   1. Gebruik een InPrivate-, Incognito-of andere vergelijk bare browser venster functie om u aan te melden bij de-console. Het venster ziet er anders uit nadat OIDC is ingeschakeld.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Aanmeld venster voor OpenID connect-verbinding is ingeschakeld.":::
   1. **OpenID Connect** selecteren

   > [!NOTE]
   > Noteer de gebruikers naam en het wacht woord waarmee u zich aanmeldt. Deze gebruikers naam en dit wacht woord functioneren als beheerder voor andere acties in deze en andere artikelen.
2. Meld u aan met de open Shift CLI met behulp van de volgende stappen.  Voor discussies wordt dit proces aangeduid als `oc login` .
   1. Vouw in de rechter bovenhoek van de webconsole het context menu van de aangemelde gebruiker uit en selecteer vervolgens **aanmeldings opdracht kopiëren**.
   2. Meld u indien nodig aan bij een nieuw tabblad venster met dezelfde gebruiker.
   3. Selecteer **weergave token**.
   4. Kopieer de onderstaande waarde voor **aanmelding met dit token** naar het klem bord en voer deze uit in een shell, zoals hier wordt weer gegeven.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Voer `oc whoami` uit in de-console en noteer de uitvoer als **\<aad-user>** .  We gebruiken deze waarde verderop in het artikel.
4. Afmelden bij de open Shift-webconsole. Selecteer de knop in de rechter bovenhoek van het browser venster met de naam **\<aad-user>** en kies **Afmelden**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>De Azure AD-gebruiker de benodigde rollen geven voor de interactie met het REGI ster

1. Meld u met de referenties aan bij de open Shift-webconsole vanuit uw browser `kubeadmin` .
1. Meld u aan bij de open Shift CLI met het token voor `kubeadmin` door de bovenstaande stappen te volgen `oc login` , maar probeer ze na het aanmelden bij de webconsole met `kubeadmin` .
1. Voer de volgende opdrachten uit om de toegang tot het ingebouwde REGI ster voor de **Aad-gebruiker** in te scha kelen.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>De URL van het container register ophalen

Gebruik de `oc get route` opdracht zoals hieronder wordt weer gegeven om de container Registry-URL op te halen.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Let op de console-uitvoer van **container Registry URL**. Deze wordt gebruikt als de volledig gekwalificeerde register naam voor deze hand leiding en volgende.

## <a name="next-steps"></a>Volgende stappen

Nu u het ingebouwde REGI ster voor container installatie kopieën hebt ingesteld, kunt u aan de slag gaan door een toepassing te implementeren op open SHIFT. Voor Java-toepassingen raadpleegt u [een Java-toepassing implementeren met open vrijheid/WebSphere vrijheid op een Azure Red Hat openshift 4-cluster](howto-deploy-java-liberty-app.md).