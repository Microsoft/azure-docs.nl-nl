---
title: Inleiding tot Azure Red Hat OpenShift
description: Ontdek de functies en voordelen van Microsoft Azure Red Hat OpenShift om op container gebaseerde toepassingen te implementeren en beheren.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: overview
ms.date: 11/13/2020
ms.custom: mvc
ms.openlocfilehash: ec934a8a9907f0c0c3aff047e003c6725c5ab25f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636217"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Met de Microsoft *Azure Red Hat OpenShift*-service kunt u volledig beheerde [OpenShift-clusters](https://www.openshift.com/) implementeren.

Azure Red Hat OpenShift breidt [Kubernetes](https://kubernetes.io/) uit. Om containers in productie uit te voeren met Kubernetes, zijn er aanvullende hulpprogramma's en resources nodig. Hiervoor is het vaak nodig om tussen afbeeldingsregisters, opslagbeheer, netwerkoplossing, logboekregistratie en bewakingshulpprogramma's te schakelen, waarvoor allemaal samen versiebeheer en tests moeten worden uitgevoerd. Als u op container gebaseerde toepassingen wilt bouwen, hebt u nog meer integratiewerk nodig met middleware, frameworks, databases en CI/CD-tools. Azure Red Hat OpenShift combineert dit allemaal in één platform, waardoor IT-teams rustig kunnen werken en waardoor toepassingsteams hebben wat ze nodig hebben.

Azure Red Hat OpenShift is samen gemaakt en wordt samen uitgevoerd en ondersteund met Red Hat en Microsoft om een geïntegreerde ondersteuningservaring te bieden. Er hoeven geen virtuele machines worden uitgevoerd en patches zijn niet nodig. Hoofd-, infrastructuur- en toepassingsknooppunten worden voor u gepatched, bijgewerkt en bewaakt door Red Hat en Microsoft. Uw Azure Red Hat OpenShift-clusters worden in uw Azure-abonnement geïmplementeerd en zijn opgenomen in uw Azure-factuur.

U kunt uw eigen register-, netwerk-, opslag- of CI/CD-oplossingen kiezen of ingebouwde oplossingen gebruiken voor geautomatiseerd broncodebeheer, container- en appbuilds, implementaties, schalingsmogelijkheden, statusbeheer en meer. Azure Red Hat open Shift biedt een geïntegreerde aanmeldingservaring via Azure Active Directory.

Voltooi de zelfstudie [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md) om aan de slag te gaan.

## <a name="access-security-and-monitoring"></a>Toegang, beveiliging en bewaking

Voor verbeterde beveiliging en verbeterd beheer laat Azure Red Hat OpenShift u integreren met Azure AD (Azure Active Directory) en gebruikmaken van Kubernetes-RBAC (op rollen gebaseerd toegangsbeheer voor Kubernetes). U kunt ook de status van uw cluster en resources bewaken.

## <a name="cluster-and-node"></a>Cluster en knooppunt

Azure Red Hat open Shift-knooppunten worden uitgevoerd op virtuele machines van Azure. U kunt opslag met knooppunten en pods verbinden en clusteronderdelen bijwerken.

## <a name="service-level-agreement"></a>Service Level Agreement

Azure Red Hat OpenShift biedt een Service Level Agreement om een beschikbaarheid van de service te garanderen van 99,95%. Raadpleeg de [Azure Red Hat OpenShift SLA](https://azure.microsoft.com/en-au/support/legal/sla/openshift/v1_0/) voor meer details.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de vereisten voor Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Uw ontwikkelaarsomgeving instellen](tutorial-create-cluster.md)
