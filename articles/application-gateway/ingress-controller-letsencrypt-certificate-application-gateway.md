---
title: LetsEncrypt.org-certificaten gebruiken met Application Gateway
description: In dit artikel vindt u informatie over het verkrijgen van een certificaat van LetsEncrypt.org en het op uw Application Gateway gebruiken voor AKS-clusters.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84807034"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Certificaten gebruiken met LetsEncrypt.org in Application Gateway voor AKS-clusters

In deze sectie configureert u uw AKS om [LetsEncrypt.org](https://letsencrypt.org/) te gebruiken en automatisch een TLS/SSL-certificaat voor uw domein te verkrijgen. Het certificaat wordt geïnstalleerd op Application Gateway, waarmee SSL/TLS-beëindiging voor uw AKS-cluster wordt uitgevoerd. De installatie die hier wordt beschreven, maakt gebruik van de Kubernetes-invoeg toepassing voor [certificaat beheer](https://github.com/jetstack/cert-manager) , waarmee het maken en beheren van certificaten wordt geautomatiseerd.

Volg de onderstaande stappen om [CERT-Manager](https://docs.cert-manager.io) op uw bestaande AKS-cluster te installeren.

1. Helm grafiek

    Voer het volgende script uit om het `cert-manager` helm-diagram te installeren. Hiermee wordt:

    - een nieuwe `cert-manager` naam ruimte maken op uw AKS
    - Maak de volgende CRDs: Certificate, Challenge, ClusterIssuer, Issuer, order
    - CERT-beheer grafiek (van [docs.cert-Manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps) installeren

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer-resource

    Een `ClusterIssuer` resource maken. Dit is vereist `cert-manager` om de `Lets Encrypt` certificerings instantie te vertegenwoordigen waar de ondertekende certificaten worden verkregen.

    Met behulp van de niet-naam ruimte `ClusterIssuer` bron, geeft CERT-Manager certificaten uit die kunnen worden gebruikt vanuit meerdere naam ruimten. `Let’s Encrypt` gebruikt het ACME-protocol om te controleren of u een bepaalde domein naam beheert en u een certificaat uitgeeft. Meer informatie over het configureren van `ClusterIssuer` eigenschappen [vindt u hier](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` geeft `cert-manager` een instructie om certificaten uit te geven met behulp `Lets Encrypt` van de faserings omgeving die wordt gebruikt voor het testen (het basis certificaat is niet aanwezig in de browsers/client vertrouwensrelatie archieven).

    Het standaard type Challenge in de onderstaande YAML is `http01` . Andere uitdagingen worden beschreven in [letsencrypt.org-vraag typen](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Update `<YOUR.EMAIL@ADDRESS>` in de onderstaande YAML

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. App implementeren

    Maak een ingangs bron om de toepassing beschikbaar te maken met `guestbook` behulp van de Application Gateway met het certificaat voor het versleutelen van certificaten.

    Zorg ervoor dat u Application Gateway een open bare frontend-IP-configuratie met een DNS-naam hebt (met behulp van het standaard `azure.com` domein of een service inricht `Azure DNS Zone` en uw eigen aangepaste domein toewijst).
    Let op de aantekening `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` , waarbij CERT-beheer aangeeft dat de gelabelde ingangs bron moet worden verwerkt.

    > [!IMPORTANT] 
    > Update `<PLACEHOLDERS.COM>` in het onderstaande YAML met uw eigen domein (of de Application Gateway, bijvoorbeeld ' KH-AKS-ingress.westeurope.cloudapp.Azure.com ')

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Na een paar seconden kunt u de service openen `guestbook` via de HTTPS-URL van Application Gateway met behulp van het automatisch verleende **staging** - `Lets Encrypt` certificaat.
    U ontvangt mogelijk een waarschuwing van een ongeldige certificerings instantie in uw browser. Het faserings certificaat wordt uitgegeven door `CN=Fake LE Intermediate X1` . Dit geeft aan dat het systeem werkt zoals verwacht en dat u klaar bent voor uw productie certificaat.

4. Productie certificaat

    Zodra het faserings certificaat is geïnstalleerd, kunt u overschakelen naar een productie server:
    1. Vervang de tijdelijke aantekening van uw ingangs resource door: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Verwijder de bestaande staging die `ClusterIssuer` u in de vorige stap hebt gemaakt en maak een nieuwe door de Acme-server te vervangen door de CLUSTERISSUER yaml hierboven met `https://acme-v02.api.letsencrypt.org/directory`

5. Verlopen en verlengen van certificaten

    Voordat het `Lets Encrypt` certificaat verloopt, `cert-manager` wordt het certificaat in het Kubernetes-geheim archief automatisch bijgewerkt. Op dat moment wordt Application Gateway ingangs controller het bijgewerkte geheim toegepast dat wordt vermeld in de binnenkomende resources die worden gebruikt om de Application Gateway te configureren.
