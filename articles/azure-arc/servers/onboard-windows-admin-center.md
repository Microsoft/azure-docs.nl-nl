---
title: Hybride computers verbinden met Azure vanuit het Windows-beheer centrum
description: In dit artikel leert u hoe u de agent kunt installeren en computers kunt verbinden met Azure met behulp van Azure Arc-servers in het Windows-beheer centrum.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 8fa118a254689feade2897f083bb3aa2dc9db02c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183410"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Hybride computers verbinden met Azure vanuit het Windows-beheer centrum

U kunt Azure Arc-servers inschakelen voor een of meer Windows-computers in uw omgeving door een reeks stappen hand matig uit te voeren. U kunt ook [Windows-beheer centrum](/windows-server/manage/windows-admin-center/understand/what-is) gebruiken om de verbonden machine-agent te implementeren en uw on-premises servers te registreren zonder dat u de stappen buiten dit hulp programma hoeft uit te voeren.

## <a name="prerequisites"></a>Vereisten

* Arc ingeschakelde servers: Bekijk de vereisten en controleer of uw abonnement, uw Azure- [account en resources](agent-overview.md#prerequisites) voldoen aan de vereisten.

* Windows-beheer centrum: Bekijk de vereisten voor [het voorbereiden van uw omgeving voor het](/windows-server/manage/windows-admin-center/deploy/prepare-environment) implementeren en [configureren van Azure-integratie ](/windows-server/manage/windows-admin-center/azure/azure-integration).

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* De doel-Windows-servers die u wilt beheren, moeten verbinding hebben met internet om toegang te krijgen tot Azure.

### <a name="security"></a>Beveiliging

Deze implementatie methode vereist dat u beschikt over beheerders rechten op de doel-Windows-computer of-server om de agent te installeren en configureren. U moet ook lid zijn van de rol [**Gateway gebruikers**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) .

## <a name="deploy"></a>Implementeren

Voer de volgende stappen uit om de Windows-Server te configureren met servers waarop Arc is ingeschakeld.

1. Meld u aan bij het Windows-beheer centrum.

1. Selecteer in de lijst met verbindingen op de pagina **overzicht** in de lijst met verbonden Windows-servers een server in de lijst om er verbinding mee te maken.

1. Selecteer **Azure Hybrid Services** in het linkerdeel venster.

1. Selecteer op de pagina **Azure Hybrid Services** de optie **Azure-Services detecteren**.

1. Selecteer op de pagina **Azure-Services detecteren** onder **Azure-beleids regels en oplossingen voor het beheren van uw servers met Azure Arc de** optie **instellen**.

1. Als u wordt gevraagd om te verifiëren bij Azure en vervolgens aan de **slag** gaat, selecteert u op de pagina **Settings\Azure Arc voor servers** .

1. Op de pagina **verbinding maken met Azure kunt u** het volgende opgeven:

    1. Selecteer in de vervolg keuzelijst **Azure-abonnement** het Azure-abonnement.
    1. Voor **resource groep** selecteert u **Nieuw** om een nieuwe resource groep te maken of selecteert u in de vervolg keuzelijst **resource groep** een bestaande resource groep om de machine te registreren en te beheren.
    1. Selecteer in de vervolg keuzelijst **Regio**, de Azure-regio om de metagegevens van de server op te slaan.
    1. Als de computer of server communiceert via een proxy server om verbinding te maken met internet, selecteert u de optie **proxy server gebruiken**. Geef het IP-adres van de proxy server of de naam en het poort nummer op dat door de computer wordt gebruikt om te communiceren met de proxy server.

1. Selecteer **instellen** om door te gaan met het configureren van de Windows Server met servers die geschikt zijn voor Azure Arc.

De Windows-Server maakt verbinding met Azure, downloadt de agent van de verbonden machine, installeert deze en registreert bij Azure Arc-servers. Selecteer **meldingen** in het menu om de voortgang bij te houden.

Om de installatie van de verbonden machine agent te bevestigen, selecteert u in het Windows-beheer centrum [**gebeurtenissen**](/windows-server/manage/windows-admin-center/use/manage-servers#events) in het linkerdeel venster om de *MsiInstaller* -gebeurtenissen in het gebeurtenis logboek van de toepassing te controleren.

## <a name="verify-the-connection-with-azure-arc"></a>De verbinding met Azure Arc controleren

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc-servers, gaat u naar Azure Portal om te controleren of de server verbinding heeft gemaakt. Bekijk uw machine in [Azure Portal](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Een geslaagde machineverbinding" border="false":::

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), moet u controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking inschakelen met [Azure monitor met vm's](../../azure-monitor/insights/vminsights-enable-policy.md)en nog veel meer.

* Meer informatie over de [log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u bewakings gegevens van het besturings systeem en werk belasting wilt verzamelen, deze wilt beheren met Automation-runbooks of-functies zoals Updatebeheer, of om andere Azure-Services zoals [Azure Security Center](../../security-center/security-center-introduction.md)te gebruiken.