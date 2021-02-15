---
title: Naslag informatie over het Azure Sentinel data source-schema
description: Dit artikel bevat informatie over Azure en gegevens bron schema's van derden die worden ondersteund door Azure Sentinel, met koppelingen naar de bijbehorende referentie documentatie.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: b5d53ec6c6a8002c72a53d6928d56e55d520ef38
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390824"
---
# <a name="data-source-schema-reference"></a>Verwijzing naar gegevens bron schema

Dit artikel bevat een lijst met ondersteunde Azure-en gegevens bron schema's van derden, met koppelingen naar de bijbehorende referentie documentatie.

## <a name="azure-data-sources"></a>Azure-gegevensbronnen

| Type                             | Gegevensbron             | Tabel naam Log Analytics | Schema verwijzing |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Aanmeld eigenschappen van Azure AD-activiteiten rapporten](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | Audit logs bevat              | [Naslag informatie over Azure Monitor audit logs bevat](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Naslag informatie over Azure Monitor AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Office 365 Management Activity API-schema's: <br>- [Algemeen schema ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange-beheer schema ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange-postvak schema](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Share point-basis schema](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [Share point-Bestands bewerkingen](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Naslag informatie over Azure Monitor AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | Syslog                 | [Azure Monitor syslog-referentie](/azure/azure-monitor/reference/tables/syslog) |
| **Netwerk**                          | IIS-logboeken               | W3CIISLog              | [Naslag informatie over Azure Monitor W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Netwerk**                          | VMinsights             | VMConnection           | [Naslag informatie over Azure Monitor VMConnection](/azure/azure-monitor/reference/tables/vmconnection) |
| **Netwerk**                          | Wire data-oplossing     | WireData               | [Naslag informatie over Azure Monitor WireData](/azure/azure-monitor/reference/tables/wiredata) |
| **Netwerk**                          | NSG-stroom logboeken          | AzureNetworkAnalytics  | [Schema's en gegevens aggregatie in Traffic Analytics](/azure/network-watcher/traffic-analytics-schema) |
| | | | |

> [!NOTE]
> Zie voor meer informatie de volledige [Azure monitor gegevens referentie](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>gegevens bronnen van derden

De volgende tabel bevat een lijst met ondersteunde externe leveranciers en hun syslog-of common Event Format (CEF)-toewijzings documentatie voor verschillende ondersteunde logboek typen, die CEF veld Toewijzingen en voorbeeld logboeken voor elk categorie type bevatten.

| Type |    Leverancier |    Product | Tabel naam Log Analytics | CEF veld-toewijzings verwijzing  |
| ----- | ----- | ----- | ----- |----- |
| **Netwerk** | Palo Alto   | BESTURINGS SYSTEEM PANNEN    | CommonSecurityLog |   [Pan-OS 9,0 algemene hand leiding](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) voor de integratie van de gebeurtenis indeling (zoek naar *CEF-stijl logboek indelingen*) |
| **Netwerk** | Check Point  |ALL   | CommonSecurityLog | [Beschrijving van logboek velden](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Netwerk** | FortiGate   | ALL   | CommonSecurityLog | [Schema structuur van logboek](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Netwerk** | Barracuda | Web Application Firewall |  CommonSecurityLog   | [Syslog en andere logboeken configureren](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Netwerk** | Cisco | ASA | CommonSecurityLog | [Berichten van de Cisco ASA-serie syslog](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Netwerk** | Cisco | Firepower   | CommonSecurityLog | [Cisco Firepower Threat verdediging syslog-berichten](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Netwerk** | Cisco   | Paraplu  | Tabel met aangepaste logboeken  | [Logboek indelingen en versie beheer](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Netwerk**   | Cisco | Meraki    | CommonSecurityLog |   [Gebeurtenis typen en logboek voorbeelden van syslog](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Netwerk**   | Zscaler | Nano streaming-service (NSS)|   CommonSecurityLog | [Nss-feeds Format teren](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (alleen web-, firewall-, DNS-en tunnel Logboeken) |
| **Netwerk**   |F5 | BigIP LTM|    CommonSecurityLog|  [Gebeurtenis berichten en typen aanvallen](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Netwerk** | F5  | BigIP ASM|    CommonSecurityLog|  [Logboek registratie van toepassings beveiligings gebeurtenissen](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Netwerk** | Citrix  |Web App-Firewall   | CommonSecurityLog|    [Ondersteuning voor logboek registratie van algemene gebeurtenis indelingen (CEF) in de toepassings firewall](https://support.citrix.com/article/CTX136146) <br>  [Verwijzing naar syslog-bericht voor NetScaler 12,0](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | Symantec Endpoint Protection-beheerder (SEPM) | CommonSecurityLog|[Instellingen voor externe logboek registratie en ernst van logboek gebeurtenissen voor Endpoint Protection-beheerder](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Trend Micro |Alles |CommonSecurityLog | [Syslog-inhouds toewijzing-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ondersteunde Azure Sentinel-connectors, zoals CEF, syslog, direct, agent en aangepaste connectors:

- [Verbinding maken met gegevensbronnen](connect-data-sources.md)

- [Azure Sentinel syslog, CEF en andere connectors van derden](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)