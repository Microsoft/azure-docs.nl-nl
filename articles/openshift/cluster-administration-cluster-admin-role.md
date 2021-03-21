---
title: Azure Red Hat open Shift cluster beheerdersrol | Microsoft Docs
description: Toewijzing en gebruik van de rol Azure Red Hat open Shift Cluster Administrator
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636235"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat open Shift-klant beheerdersrol

> [!IMPORTANT]
> Azure Red Hat open Shift 3,11 wordt 30 juni 2022 buiten gebruik gesteld. Ondersteuning voor het maken van nieuwe Azure Red Hat open Shift 3,11-clusters loopt voort tot en met 30 november 2020. Na de buiten gebruiks telling worden de resterende Azure Red Hat open Shift 3,11-clusters afgesloten om beveiligings problemen te voor komen.
> 
> Volg deze hand leiding voor het [maken van een Azure Red Hat open Shift 4-cluster](tutorial-create-cluster.md).
> Als u specifieke vragen hebt, [kunt u contact met ons](mailto:arofeedback@microsoft.com)opnemen.

U bent de Cluster beheerder van een Azure Red Hat open Shift-cluster. Uw account heeft verhoogde machtigingen en toegang tot alle door de gebruiker gemaakte projecten.

Als uw account is gekoppeld aan de autorisatie rol klant-beheerder-cluster, kan hiermee automatisch een project worden beheerd.

> [!Note] 
> De rol klant-beheerder-cluster cluster is niet hetzelfde als de cluster functie cluster beheerder.

U kunt bijvoorbeeld acties uitvoeren die zijn gekoppeld aan een set termen ( `create` ) om te worden uitgevoerd op een set resource namen ( `templates` ). Voer de volgende opdracht uit om de details van deze rollen en hun sets met werk woorden en resources weer te geven:

`$ oc get clusterroles customer-admin-cluster -o yaml`

De namen van woorden kunnen niet per se rechtstreeks aan opdrachten worden toegewezen `oc` . Ze zijn in het algemeen gelijk aan de typen CLI-bewerkingen die u kunt uitvoeren. 

Als u bijvoorbeeld de `list` opdracht geeft, kunt u een lijst met alle objecten van een resource naam () weer geven `oc get` . De `get` opdracht geeft aan dat u de details van een specifiek object kunt weer geven als u de naam ervan kent ( `oc describe` ).

## <a name="configure-the-customer-administrator-role"></a>De rol klant beheerder configureren

U kunt de cluster functie klant-beheerder-cluster alleen configureren tijdens het maken van een cluster door de vlag op te geven `--customer-admin-group-id` . Dit veld kan momenteel niet worden geconfigureerd in de Azure Portal. Zie [Azure Active Directory-integratie voor Azure Red Hat open Shift voor](howto-aad-app-configuration.md)meer informatie over het configureren van Azure Active Directory en de groep Administrators.

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Lidmaatschap van de rol klant beheerder bevestigen

Als u het lidmaatschap van de groep klanten beheerder wilt bevestigen, probeert u de open Shift CLI-opdrachten `oc get nodes` of `oc projects` . `oc get nodes` Er wordt een lijst met knoop punten weer gegeven als u de rol klant-admin-cluster hebt en een machtigings fout als u alleen de rol klant-beheerder-project hebt. `oc projects` alle projecten in het cluster worden weer gegeven in plaats van alleen de projecten waarin u werkt.

Als u de rollen en machtigingen in uw cluster verder wilt verkennen, kunt u de [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) opdracht gebruiken.

## <a name="next-steps"></a>Volgende stappen

Configureer de rol klant-beheerder-cluster cluster:
> [!div class="nextstepaction"]
> [Azure Active Directory integratie voor Azure Red Hat open Shift](howto-aad-app-configuration.md)
