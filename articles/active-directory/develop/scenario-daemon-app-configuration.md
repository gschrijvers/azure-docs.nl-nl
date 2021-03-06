---
title: Daemon-apps configureren die web-Api's aanroepen-micro soft Identity-platform | Azure
description: Meer informatie over het configureren van de code voor uw daemon-toepassing die web-Api's aanroept (app-configuratie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ead39343cca9943ba55d66509bd9917402efb8cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868975"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app die web-Api's aanroept-code configuratie

Meer informatie over het configureren van de code voor uw daemon-toepassing die web-Api's aanroept.

## <a name="msal-libraries-that-support-daemon-apps"></a>MSAL-bibliotheken die ondersteuning bieden voor daemon-apps

Deze micro soft-bibliotheken ondersteunen daemon-apps:

  MSAL-bibliotheek | Beschrijving
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | De .NET Framework-en .NET Core-platformen worden ondersteund voor het bouwen van daemon-toepassingen. (UWP, Xamarin. iOS en Xamarin. Android worden niet ondersteund omdat deze platformen worden gebruikt voor het bouwen van open bare client toepassingen.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL python | Ondersteuning voor daemon-toepassingen in python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Ondersteuning voor daemon-toepassingen in Java.

## <a name="configure-the-authority"></a>De instantie configureren

Voor daemon-toepassingen worden toepassings machtigingen gebruikt in plaats van gedelegeerde machtigingen. Het ondersteunde account type kan dus geen account zijn in een organisatorische map of persoonlijke Microsoft-account (bijvoorbeeld Skype, Xbox, Outlook.com). Er is geen Tenant beheerder om toestemming te verlenen voor een daemon-toepassing voor een persoonlijk micro soft-account. U moet *accounts in mijn organisatie* of *accounts in een organisatie*kiezen.

De in de toepassings configuratie opgegeven instantie moet dus worden getenantd (een Tenant-ID of een domein naam opgeven die is gekoppeld aan uw organisatie).

Als u een ISV bent en een multi tenant hulp programma wilt bieden, kunt u gebruiken `organizations`. Maar houd er rekening mee dat u ook moet uitleggen wat uw klanten zijn om toestemming van de beheerder te verlenen. Zie [toestemming vragen voor een hele Tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)voor meer informatie. Er is momenteel een beperking in MSAL: `organizations` is alleen toegestaan wanneer de referenties van de client een toepassings geheim zijn (niet een certificaat).

## <a name="configure-and-instantiate-the-application"></a>De toepassing configureren en instantiëren

In MSAL-bibliotheken worden de client referenties (geheim of certificaat) door gegeven als para meter van de client toepassings constructie.

> [!IMPORTANT]
> Zelfs als uw toepassing een-console toepassing is die als een service wordt uitgevoerd, moet deze een een vertrouwelijke client toepassing zijn.

### <a name="configuration-file"></a>Configuratiebestand

Het configuratie bestand definieert het volgende:

- De instantie of het Cloud exemplaar en de Tenant-ID.
- De client-ID die u hebt ontvangen van de registratie van de toepassing.
- Een client geheim of een certificaat.

# <a name="net"></a>[.NET](#tab/dotnet)

[appSettings. json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) van het voor beeld van de [.net Core-Console-daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

U geeft een `ClientSecret` of een `CertificateName`op. Deze instellingen zijn exclusief.

# <a name="python"></a>[Python](#tab/python)

Wanneer u een vertrouwelijke client met client geheimen bouwt, is het configuratie bestand van de [para meters. json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) in het voor beeld van [python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) als volgt:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Wanneer u een vertrouwelijke client met certificaten bouwt, zijn de [para meters. json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) -configuratie bestand in het [python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) -voor beeld als volgt:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>De MSAL-toepassing instantiëren

Als u de MSAL-toepassing wilt instantiëren, moet u het MSAL-pakket (afhankelijk van de taal) toevoegen, raadplegen of importeren.

De bouw is anders, afhankelijk van of u client geheimen of certificaten gebruikt (of, als een geavanceerd scenario, ondertekende beweringen).

#### <a name="reference-the-package"></a>Verwijzen naar het pakket

Raadpleeg het MSAL-pakket in de code van uw toepassing.

# <a name="net"></a>[.NET](#tab/dotnet)

Voeg het [micro soft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-pakket toe aan uw toepassing.
In MSAL.NET wordt de vertrouwelijke client toepassing vertegenwoordigd door de `IConfidentialClientApplication` interface.
Gebruik de naam ruimte MSAL.NET in de bron code.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>De vertrouwelijke client toepassing instantiëren met een client geheim

Dit is de code voor het instantiëren van de vertrouwelijke client toepassing met een client geheim:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>De vertrouwelijke client toepassing instantiëren met een client certificaat

Hier volgt de code voor het bouwen van een toepassing met een certificaat:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

In MSAL Java zijn er twee bouwers voor het instantiëren van de vertrouwelijke client toepassing met certificaten:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

of

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Geavanceerd scenario: de vertrouwelijke client toepassing instantiëren met client verklaringen

# <a name="net"></a>[.NET](#tab/dotnet)

In plaats van een client geheim of een certificaat kan de vertrouwelijke client toepassing ook de identiteit bewijzen door gebruik te maken van client verklaringen.

MSAL.NET heeft twee methoden om ondertekende bevestigingen te bieden aan de vertrouwelijke client-app:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Wanneer u gebruikt `WithClientAssertion`, moet u een ondertekende JWT opgeven. Dit geavanceerde scenario wordt beschreven in [client verklaringen](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Wanneer u gebruikt `WithClientClaims`, produceert MSAL.net een ondertekende bevestiging die de claims bevat die worden verwacht door Azure AD, plus aanvullende client claims die u wilt verzenden.
Deze code laat zien hoe u dit doet:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Zie voor meer informatie [client verklaringen](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

In MSAL python kunt u client claims opgeven door gebruik te maken van de claims die worden ondertekend door `ConfidentialClientApplication`de persoonlijke sleutel van deze persoon.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Zie de naslag documentatie voor MSAL python voor [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)voor meer informatie.

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Volgende stappen

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-app-tokens ophalen voor de app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-app-tokens ophalen voor de app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-app-tokens ophalen voor de app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
