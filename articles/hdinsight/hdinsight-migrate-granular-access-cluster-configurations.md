---
title: Gedetailleerde op rollen gebaseerde toegang Azure HDInsight-cluster configuraties
description: Meer informatie over de wijzigingen die zijn vereist als onderdeel van de migratie naar gedetailleerde op rollen gebaseerde toegang voor HDInsight-cluster configuraties.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 058300dca3e7eae41b7d8010e1ca5ee7d4cdcf3a
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598467"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migreren naar gedetailleerde, op rollen gebaseerde toegang voor clusterconfiguraties

We introduceren enkele belang rijke wijzigingen ter ondersteuning van meer verfijnde op rollen gebaseerde toegang om gevoelige informatie te verkrijgen. Als onderdeel van deze wijzigingen is het mogelijk dat er **op 3 September 2019** een bepaalde actie is vereist als u een van de [betrokken entiteiten/scenario's](#am-i-affected-by-these-changes)gebruikt.

## <a name="what-is-changing"></a>Wat wordt er gewijzigd?

Voorheen kunnen geheimen worden verkregen via de HDInsight API door cluster gebruikers die beschikken over de [rol](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)van eigenaar, bijdrager of lezer, aangezien ze beschikbaar zijn voor iedereen met de `*/read` machtiging. Geheimen worden gedefinieerd als waarden die kunnen worden gebruikt om meer verhoogde toegang te verkrijgen dan de rol van een gebruiker moet toestaan. Dit zijn onder andere waarden zoals de HTTP-referenties van de cluster gateway, sleutels voor opslag accounts en database referenties.

Vanaf 3 september 2019 heeft toegang tot deze geheimen de `Microsoft.HDInsight/clusters/configurations/action` machtiging nodig, wat betekent dat gebruikers niet langer toegang hebben tot de rol van lezer. De functies die deze machtiging hebben, zijn Inzender, eigenaar en de nieuwe rol HDInsight-cluster operator (meer hierover).

We introduceren ook een nieuwe rol van een [HDInsight-cluster operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) die geheimen kan ophalen zonder dat de beheerders machtigingen van Inzender of eigenaar worden toegewezen. Samenvatting:

| Rol                                  | Verwijderd                                                                                       | Gaat verder       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lezer                                | -Lees toegang, inclusief geheimen.                                                                   | -Lees toegang, met **uitzonde ring van** geheimen |           |   |   |
| HDInsight-cluster operator<br>(Nieuwe rol) | N.v.t.                                                                                              | -Lees-en schrijf toegang, inclusief geheimen         |   |   |
| Inzender                           | -Lees-en schrijf toegang, inclusief geheimen.<br>-Alle typen Azure-resources maken en beheren.<br>-Script acties uitvoeren.     | Geen wijziging |
| Eigenaar                                 | -Lees-en schrijf toegang, inclusief geheimen.<br>-Volledige toegang tot alle resources<br>-Toegang tot anderen delegeren.<br>-Script acties uitvoeren. | Geen wijziging |

Voor informatie over het toevoegen van de toewijzing van de HDInsight-cluster operator aan een gebruiker om hen lees-/schrijftoegang te verlenen aan cluster geheimen, raadpleegt u de onderstaande sectie, [voegt u de roltoewijzing van de hdinsight-cluster operator toe aan een gebruiker](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Is dit van invloed op deze wijzigingen?

De volgende entiteiten en scenario's worden beïnvloed:

- [API](#api): gebruikers die de `/configurations` of `/configurations/{configurationName}` -eind punten gebruiken.
- [Azure HDInsight-Hulpprogram ma's voor Visual Studio code](#azure-hdinsight-tools-for-visual-studio-code) versie 1.1.1 of lager.
- [Azure-Toolkit voor IntelliJ](#azure-toolkit-for-intellij) versie 3.20.0 of lager.
- [Azure data Lake-en stream Analytics-Hulpprogram ma's voor Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) onder versie 2.3.9000.1.
- [Azure-Toolkit voor eclipse](#azure-toolkit-for-eclipse) versie 3.15.0 of lager.
- [SDK voor .NET](#sdk-for-net)
    - [versie 1. x of 2. x](#versions-1x-and-2x): gebruikers die de `GetClusterConfigurations`methoden `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` of `DisableHttp` gebruiken vanuit de ConfigurationsOperationsExtensions-klasse.
    - [versie 3. x en hoger](#versions-3x-and-up): gebruikers die gebruikmaken `Get`van `Update`de `EnableHttp`methoden, `DisableHttp` , of van `ConfigurationsOperationsExtensions` de-klasse.
- [SDK voor python](#sdk-for-python): gebruikers die gebruikmaken `get` van `update` de-of `ConfigurationsOperations` -methoden van de-klasse.
- [SDK voor Java](#sdk-for-java): gebruikers die gebruikmaken `update` van `get` de-of `ConfigurationsInner` -methoden van de-klasse.
- [SDK voor Go](#sdk-for-go): gebruikers die gebruikmaken `Get` van `Update` de-of `ConfigurationsClient` -methoden vanuit de struct.
- [AZ. HDInsight Power shell](#azhdinsight-powershell) onder versie 2.0.0.
Zie de onderstaande secties (of gebruik de bovenstaande koppelingen) voor een overzicht van de migratie stappen voor uw scenario.

### <a name="api"></a>API

De volgende Api's worden gewijzigd of afgeschaft:

- [**/Configurations/{configurationName} ophalen**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (gevoelige informatie verwijderd)
    - Eerder gebruikt om afzonderlijke configuratie typen (inclusief geheimen) op te halen.
    - Vanaf 3 september 2019 retourneert deze API-aanroep nu afzonderlijke configuratie typen waarvoor geheimen zijn wegge laten. Gebruik de nieuwe aanroep POST/configurations om alle configuraties, inclusief geheimen, te verkrijgen. Als u alleen gateway-instellingen wilt ophalen, gebruikt u de nieuwe aanroep POST/getGatewaySettings.
- [**/Configurations ophalen**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (afgeschaft)
    - Eerder gebruikt om alle configuraties te verkrijgen (inclusief geheimen)
    - Vanaf 3 september 2019 wordt deze API-aanroep afgeschaft en wordt deze niet meer ondersteund. Gebruik de nieuwe aanroep POST/configurations om alle configuraties te verkrijgen die verder gaan. Voor het verkrijgen van configuraties met gevoelige para meters, gebruikt u de aanroep GET/configurations/{configurationName}.
- [**Post/configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (afgeschaft)
    - Eerder gebruikt om de gateway referenties bij te werken.
    - Vanaf 3 september 2019 wordt deze API-aanroep afgeschaft en wordt deze niet meer ondersteund. Gebruik in plaats daarvan de nieuwe POST-/updateGatewaySettings.

De volgende vervangings-Api's zijn toegevoegd:</span>

- [**/Configurations plaatsen**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Gebruik deze API om alle configuraties, inclusief geheimen, op te halen.
- [**/GetGatewaySettings plaatsen**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Gebruik deze API om gateway instellingen te verkrijgen.
- [**/UpdateGatewaySettings plaatsen**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Gebruik deze API om gateway-instellingen (gebruikers naam en/of wacht woord) bij te werken.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight-Hulpprogram Ma's voor Visual Studio code

Als u versie 1.1.1 of lager gebruikt, werkt u bij met de [nieuwste versie van Azure HDInsight-Hulpprogram ma's voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) om onderbrekingen te voor komen.

### <a name="azure-toolkit-for-intellij"></a>Azure-toolkit voor IntelliJ

Als u versie 3.20.0 of lager gebruikt, werkt u bij naar de [nieuwste versie van de Azure-Toolkit voor IntelliJ-invoeg toepassing](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) om onderbrekingen te voor komen.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake-en Stream Analytics-Hulpprogram Ma's voor Visual Studio

Update naar versie 2.3.9000.1 of hoger van [Azure data Lake en stream Analytics tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) om onderbrekingen te voor komen.  Voor hulp bij het bijwerken raadpleegt u onze documentatie, [Update Data Lake-Hulpprogram ma's voor Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure-toolkit voor Eclipse

Als u versie 3.15.0 of lager gebruikt, werkt u bij naar de [nieuwste versie van de Azure-Toolkit voor eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) om onderbrekingen te voor komen.

### <a name="sdk-for-net"></a>SDK voor .NET

#### <a name="versions-1x-and-2x"></a>Versies 1. x en 2. x

Update naar [versie 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) van de HDINSIGHT-SDK voor .net. U kunt minimale code wijzigingen opgeven als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- `ClusterOperationsExtensions.GetClusterConfigurations`**retourneert niet langer gevoelige para meters** , zoals opslag sleutels (kern site) of http-referenties (gateway).
    - Gebruik `ClusterOperationsExtensions.ListConfigurations` voorwaarts om alle configuraties, inclusief gevoelige para meters, op te halen.  Houd er rekening mee dat gebruikers met de rol ' lezer ' deze methode niet kunnen gebruiken. Dit biedt gedetailleerde controle over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster.
    - Als u alleen referenties van de HTTP- `ClusterOperationsExtensions.GetGatewaySettings`gateway wilt ophalen, gebruikt u.

- `ClusterOperationsExtensions.GetConnectivitySettings`is nu afgeschaft en is vervangen door `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings`is nu afgeschaft en is vervangen door `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp`en `DisableHttp` zijn nu afgeschaft. HTTP is nu altijd ingeschakeld, zodat deze methoden niet meer nodig zijn.

#### <a name="versions-3x-and-up"></a>Versies 3. x en hoger

Update naar [versie 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) of hoger van de HDINSIGHT-SDK voor .net. U kunt minimale code wijzigingen opgeven als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**retourneert niet langer gevoelige para meters** , zoals opslag sleutels (kern site) of http-referenties (gateway).
    - Gebruik [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) voorwaarts om alle configuraties, inclusief gevoelige para meters, op te halen.Houd er rekening mee dat gebruikers met de rol ' lezer ' deze methode niet kunnen gebruiken. Dit biedt gedetailleerde controle over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen referenties van de HTTP- [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)gateway wilt ophalen, gebruikt u. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)is nu afgeschaft en is vervangen door [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)en [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) zijn nu afgeschaft. HTTP is nu altijd ingeschakeld, zodat deze methoden niet meer nodig zijn.

### <a name="sdk-for-python"></a>SDK voor Python

Update naar [versie 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) of hoger van de HDInsight SDK voor python. U kunt minimale code wijzigingen opgeven als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**retourneert niet langer gevoelige para meters** , zoals opslag sleutels (kern site) of http-referenties (gateway).
    - Gebruik [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) voorwaarts om alle configuraties, inclusief gevoelige para meters, op te halen.Houd er rekening mee dat gebruikers met de rol ' lezer ' deze methode niet kunnen gebruiken. Dit biedt gedetailleerde controle over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen referenties van de HTTP- [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)gateway wilt ophalen, gebruikt u.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)is nu afgeschaft en is vervangen door [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK voor Java

Update naar [versie 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) of hoger van de HDINSIGHT-SDK voor Java. U kunt minimale code wijzigingen opgeven als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- `ConfigurationsInner.get`**retourneert niet langer gevoelige para meters** , zoals opslag sleutels (kern site) of http-referenties (gateway).
- `ConfigurationsInner.update`is nu afgeschaft.

### <a name="sdk-for-go"></a>SDK voor Go

Update naar [versie 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) of hoger van de HDInsight SDK voor go. U kunt minimale code wijzigingen opgeven als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**retourneert niet langer gevoelige para meters** , zoals opslag sleutels (kern site) of http-referenties (gateway).
    - Gebruik [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) voorwaarts om alle configuraties, inclusief gevoelige para meters, op te halen.Houd er rekening mee dat gebruikers met de rol ' lezer ' deze methode niet kunnen gebruiken. Dit biedt gedetailleerde controle over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen referenties van de HTTP- [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)gateway wilt ophalen, gebruikt u.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)is nu afgeschaft en is vervangen door [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>AZ. HDInsight Power shell
Update naar [AZ Power shell version 2.0.0](https://www.powershellgallery.com/packages/Az) of hoger om onderbrekingen te voor komen.  U kunt minimale code wijzigingen aanbrengen als u een methode gebruikt die wordt beïnvloed door deze wijzigingen.
- `Grant-AzHDInsightHttpServicesAccess`is nu afgeschaft en is vervangen door de nieuwe `Set-AzHDInsightGatewayCredential` cmdlet.
- `Get-AzHDInsightJobOutput`is bijgewerkt ter ondersteuning van granulaire op rollen gebaseerde toegang tot de opslag sleutel.
    - Gebruikers met de HDInsight Cluster-rollen Operator, Bijdrager of Eigenaar worden niet beïnvloed.
    - Gebruikers met alleen de rol van lezer moeten de `DefaultStorageAccountKey` para meter expliciet opgeven.
- `Revoke-AzHDInsightHttpServicesAccess`is nu afgeschaft. HTTP is nu altijd ingeschakeld, dus deze cmdlet is niet meer nodig.
 Zie de [AZ. Migratie handleiding voor HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) voor meer informatie.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>De roltoewijzing van de HDInsight-cluster operator toevoegen aan een gebruiker

Een gebruiker met de rol [eigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) kan de rol van de [HDInsight-cluster operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) toewijzen aan gebruikers waarvoor u lees-/schrijftoegang wilt hebben tot gevoelige configuratie waarden voor het hdinsight-cluster (zoals de cluster gateway referenties en de sleutels van het opslag account).

### <a name="using-the-azure-cli"></a>Azure CLI gebruiken

De eenvoudigste manier om deze roltoewijzing toe te voegen, is `az role assignment create` met behulp van de opdracht in azure cli.

> [!NOTE]
> Deze opdracht moet worden uitgevoerd door een gebruiker met de rol eigenaar, omdat alleen deze machtigingen kunnen worden verleend. De `--assignee` is de naam van de service-principal of het e-mail adres van de gebruiker aan wie u de rol van de HDInsight-cluster operator wilt toewijzen. Als u een fout melding over onvoldoende machtigingen krijgt, raadpleegt u de veelgestelde vragen hieronder.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Rol toekennen op resource niveau (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Rol toekennen op het niveau van de resource groep

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Rol toekennen op abonnements niveau

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

U kunt ook de Azure Portal gebruiken om de roltoewijzing van de HDInsight-cluster operator toe te voegen aan een gebruiker. Raadpleeg de documentatie voor meer informatie over het [beheren van toegang tot Azure-resources met RBAC en de Azure Portal-een roltoewijzing toevoegen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>Veelgestelde vragen

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Waarom krijg ik een 403 (Verboden) reactie na het bijwerken van mijn API-aanvragen en/of hulpprogramma?

Cluster configuraties bevinden zich nu achter een gedetailleerd toegangs beheer op basis van `Microsoft.HDInsight/clusters/configurations/*` rollen en vereisen de machtiging voor toegang. Als u deze machtiging wilt verkrijgen, wijst u de rol HDInsight-cluster operator, Inzender of eigenaar toe aan de gebruiker of service-principal die toegang probeert te krijgen tot configuraties.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Waarom zie ik ' onvoldoende bevoegdheden om de bewerking te volt ooien ' bij het uitvoeren van de Azure CLI-opdracht om de rol van de HDInsight-cluster operator toe te wijzen aan een andere gebruiker of Service-Principal?

Naast de rol van eigenaar moet de gebruiker of service-principal die de opdracht uitvoert, voldoende Azure AD-machtigingen hebben om de object-Id's van de toegewezen persoon op te zoeken. Dit bericht geeft onvoldoende Azure AD-machtigingen aan. Probeer het `-–assignee` argument te vervangen `–assignee-object-id` door en de object-id van de toegewezen gebruiker op te geven als de para meter in plaats van de naam (of de principal-id in het geval van een beheerde identiteit). Zie de sectie optionele para meters van de [toewijzing AZ Role](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) voor meer informatie.

Als dit nog steeds niet werkt, neemt u contact op met uw Azure AD-beheerder om de juiste machtigingen te verkrijgen.

### <a name="what-will-happen-if-i-take-no-action"></a>Wat gebeurt er als er geen actie wordt ondernomen?

Vanaf 3 september 2019 `GET /configurations` en `POST /configurations/gateway` oproepen worden geen gegevens meer geretourneerd en de `GET /configurations/{configurationName}` aanroep zal niet langer gevoelige para meters retour neren, zoals sleutels van het opslag account of het wacht woord van het cluster. Hetzelfde geldt voor de bijbehorende SDK-methoden en Power shell-cmdlets.

Als u een oudere versie gebruikt van een van de hulpprogram ma's voor Visual Studio, VSCode, IntelliJ of eclips die hierboven worden genoemd, werken ze niet meer totdat u deze bijwerkt.

Zie de bijbehorende sectie in dit document voor uw scenario voor meer gedetailleerde informatie.
