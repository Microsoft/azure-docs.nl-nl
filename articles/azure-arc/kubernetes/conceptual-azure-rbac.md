---
title: Azure RBAC-Azure Arc enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een conceptueel overzicht van de mogelijkheden van Azure RBAC op Azure Arc enabled Kubernetes
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451012"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure RBAC op Azure Arc enabled Kubernetes

Kubernetes [ClusterRoleBinding en RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) object types helpen u om autorisatie in Kubernetes systeem eigen te definiëren. Met Azure RBAC kunt u Azure Active Directory (Azure AD) en roltoewijzingen in azure gebruiken om autorisatie controles op het cluster te beheren.

Met deze functie worden alle voor delen van Azure-roltoewijzingen, zoals activiteiten logboeken met alle Azure RBAC-wijzigingen in een Azure-resource, nu van toepassing voor uw Azure Arc enabled Kubernetes-cluster.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Architectuur-Azure RBAC op Azure Arc enabled Kubernetes

[![Azure RBAC-architectuur ](./media/conceptual-azure-rbac.png)](./media/conceptual-azure-rbac.png#lightbox)

Als u alle verificatie toegangs controles wilt door sturen naar de autorisatie service in azure, wordt er een webhook-server ([Guard](https://github.com/appscode/guard)) op het cluster geïmplementeerd.

De `apiserver` van het cluster is geconfigureerd voor het gebruik van [webhook-token verificatie](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) en [webhook-autorisatie](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) , zodat `TokenAccessReview` en `SubjectAccessReview` aanvragen worden doorgestuurd naar de Guard webhook-server. De- `TokenAccessReview` en- `SubjectAccessReview` aanvragen worden geactiveerd door aanvragen voor Kubernetes-resources die naar worden verzonden `apiserver` .

Met Guard wordt vervolgens een `checkAccess` oproep uitgevoerd op de autorisatie service in azure om te zien of de aanvragende Azure AD-entiteit toegang heeft tot de bron van bezorgdheid. 

Als er een rol in de toewijzing is die deze toegang toestaat, `allowed` wordt een antwoord verzonden vanuit de verificatie service-beveiliging. Guard, op zijn beurt, stuurt een `allowed` reactie naar de `apiserver` , waardoor de aanroepende entiteit toegang kan krijgen tot de aangevraagde Kubernetes-resource.


Als een rol in toewijzing die deze toegang verleent, niet bestaat, wordt er een `denied` antwoord verzonden van de autorisatie service om te beveiligen. Guard stuurt een `denied` reactie naar de `apiserver` , waardoor de aanroepende entiteit een 403 verboden fout heeft voor de aangevraagde bron.

## <a name="next-steps"></a>Volgende stappen

* Gebruik onze Snelstartgids om [een Kubernetes-cluster te verbinden met Azure Arc](./quickstart-connect-cluster.md).
* [Stel Azure RBAC](./azure-rbac.md) in op uw Azure Arc enabled Kubernetes-cluster cluster.