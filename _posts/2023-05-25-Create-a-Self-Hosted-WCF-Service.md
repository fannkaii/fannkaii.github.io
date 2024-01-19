---
layout: post
title: "创建一个自托管（Self-Host）的WCF Service"
author: "Kai"
header-style: text
tags:
  - C#
---


```csharp
// Create WCF service host
Uri baseAddress = new Uri("https://127.0.0.1:8087/MyWcfService");
ServiceHost host = new ServiceHost(typeof(MyWcfService));

host.Credentials.ServiceCertificate.SetCertificate(
    StoreLocation.LocalMachine,
    StoreName.My,
    X509FindType.FindByThumbprint,
    "xxx"
    );

host.Open();
Console.WriteLine("Press any key to stop the service...");
Console.ReadKey();

// Close the service host
host.Close();



public class MyWcfService : IService
{
    public string GetMessage(string name)
    {
        return "Hello, " + name + "!";
    }
}

[ServiceContract]
public interface IService
{
    [OperationContract]
    string GetMessage(string name);
}
```

若确保上述self-host server能运行，需要用管理员权限开一个powershell，运行：

```powershell
# 创建 SSL/TLS 绑定
netsh http add sslcert hostnameport=FK-DNSName:18087 certhash=48c2c6eedda7af27e6febe591827d5eda85e19da appid={03A37246-7000-4090-8057-D4346A7D098B} certstorename=MY

# Add urlacl
netsh http add urlacl https://+:8087/MyWcfService user=运行self-host server的账号
netsh http add urlacl http://+:8088/MyWcfService user=运行self-host server的账号
```

web.config中的配置信息：

```xml
<system.serviceModel>
    <services>
        <service name="MyWindowsService.MyWcfService">
            <endpoint address=""
                        binding="basicHttpBinding"
                        bindingConfiguration="SecureBinding"
                        contract="MyWindowsService.IService" />
            <host>
                <baseAddresses>
                    <add baseAddress="https://127.0.0.1:8087/MyWcfService" />
                </baseAddresses>
            </host>
        </service>
    </services>
    <bindings>
        <basicHttpBinding>
            <binding name="SecureBinding">
                <security mode="Transport">
                    <transport clientCredentialType="None" />
                </security>
            </binding>
        </basicHttpBinding>
    </bindings>
    <behaviors>
        <serviceBehaviors>
            <behavior>
                <serviceMetadata httpsGetEnabled="true" httpGetUrl="" />
                <serviceDebug includeExceptionDetailInFaults="false" />
            </behavior>
        </serviceBehaviors>
    </behaviors>
    <protocolMapping>
        <add scheme="https" binding="basicHttpBinding" />
    </protocolMapping>
    <serviceHostingEnvironment aspNetCompatibilityEnabled="true" />
</system.serviceModel>
```

另一种配置方式：

```xml
<system.serviceModel>
    <services>
        <service name="MyWindowsService.MyWcfService">
            <endpoint address="" binding="basicHttpBinding" bindingConfiguration="SecureBinding" contract="MyWindowsService.IService" />
            <host>
                <baseAddresses>
                    <add baseAddress="https://127.0.0.1:8087/MyWcfService" />
                </baseAddresses>
            </host>
        </service>
    </services>
    <bindings>
        <basicHttpBinding>
            <binding name="SecureBinding" closeTimeout="00:10:00" openTimeout="00:10:00" receiveTimeout="00:10:00" sendTimeout="00:10:00" allowCookies="false" bypassProxyOnLocal="false" hostNameComparisonMode="StrongWildcard" maxBufferSize="2147483647" maxBufferPoolSize="2147483647" maxReceivedMessageSize="2147483647" messageEncoding="Text" textEncoding="utf-8" transferMode="Buffered" useDefaultWebProxy="true">
                <readerQuotas maxDepth="32" maxStringContentLength="2147483647" maxArrayLength="2147483647" maxBytesPerRead="2147483647" maxNameTableCharCount="2147483647" />
                <security mode="Transport">
                    <transport clientCredentialType="None" proxyCredentialType="None" realm="" />
                    <message clientCredentialType="UserName" algorithmSuite="Default" />
                </security>
            </binding>
        </basicHttpBinding>
    </bindings>
    <behaviors>
        <serviceBehaviors>
            <behavior>
                <serviceMetadata httpGetEnabled="True" httpGetUrl="http://127.0.0.1:8086/MyWcfService" />
                <serviceDebug includeExceptionDetailInFaults="True" />
            </behavior>
        </serviceBehaviors>
    </behaviors>
    <protocolMapping>
        <add scheme="https" binding="basicHttpBinding" />
    </protocolMapping>
    <serviceHostingEnvironment aspNetCompatibilityEnabled="true" />
</system.serviceModel>
```

查找证书的方式1：

```csharp
// 定义要查找的 CN、E、OU 值
string commonName = "FSAG-Central-CA_01";

// 打开本地计算机上的“个人”证书存储
X509Store store = new X509Store(StoreName.CertificateAuthority, StoreLocation.LocalMachine);
store.Open(OpenFlags.ReadOnly);

// 在证书存储中查找符合条件的证书
X509Certificate2Collection certCollection = store.Certificates.Find(
    X509FindType.FindBySubjectName, commonName, true);


// 如果找到符合条件的证书，则输出证书信息
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    Console.WriteLine(cert.Subject);
    Console.WriteLine(cert.Thumbprint);
}
else
{
    Console.WriteLine("未找到符合条件的证书");
}

// 关闭证书存储
store.Close();
```

查找证书的方式2：

```csharp
string dnsName = "FK-DNSName "; // 指定要查找的 DNS 名称
string commonName = "FK2023.CN"; //CN：通用名称（Common Name），通常为域名或主机名

// 打开本地计算机的证书存储，并获取存储中包含指定 DNS 名称的证书
using (var store = new X509Store(StoreLocation.LocalMachine))
{
    store.Open(OpenFlags.ReadOnly);

    foreach (var cert in store.Certificates)
    {
        foreach (var extension in cert.Extensions)
        {
            //2.5.29.17 是 X.509 证书中 Subject Alternative Name (SAN) 扩展的固定 OID（Object Identifier），它在 X.509 标准中被定义为 SAN 扩展的唯一标识符。当您需要查找或获取证书的 SAN 信息时，可以使用 2.5.29.17 作为 OID 参数来访问该扩展。
            if (extension.Oid.Value.Equals("2.5.29.17", StringComparison.OrdinalIgnoreCase)) // Subject Alternative Name(SAN) 扩展的 OID
            {
                string sanValues = extension.Format(true); // 获取 Subject Alternative Name(SAN) 扩展中包含的所有值

                if (sanValues.Contains($"DNS Name={dnsName}") && cert.Subject.Contains($"CN={commonName}"))
                {
                    Console.WriteLine($"证书 Subject: {cert.Subject}");
                    Console.WriteLine($"证书 SAN: {sanValues}");
                }
            }
        }
    }
}
```

C# Client端调用Self-Host的WCF Service

```csharp
string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];

// 创建服务终结点地址
EndpointAddress address = new EndpointAddress(serviceUrl);
var eab = new EndpointAddressBuilder(address);
string wcfKey = ConfigurationManager.AppSettings["WcfKey"];
eab.Headers.Add(AddressHeader.CreateAddressHeader("WcfKey", string.Empty, wcfKey));

// 创建 Binding
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.Transport);
binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Certificate;

// 从证书存储区加载客户端证书
X509Certificate2 clientCert = Utility.LoadCertificateFromStore(
    (StoreName)Enum.Parse(typeof(StoreName), ConfigurationManager.AppSettings["Cert_StoreName"]),
    (StoreLocation)Enum.Parse(typeof(StoreLocation), ConfigurationManager.AppSettings["Cert_LocalMachine"]),
    ConfigurationManager.AppSettings["Cert_Thumbprint"]);

// 创建 ChannelFactory
ChannelFactory<IFiloService> factory = new ChannelFactory<IFiloService>(binding, eab.ToEndpointAddress());
factory.Credentials.ClientCertificate.Certificate = clientCert;
factory.Endpoint.EndpointBehaviors.Add(new AddUserAgentEndpointBehavior());

// 创建客户端代理
IService client = factory.CreateChannel();

#if DEBUG
//本Client的证书使用“IIS Express Development Certificate”，为避免报错，需要如下配置。仅本地调试时使用
//如不配置将报错：Could not establish trust relationship for the SSL/TLS secure channel with authority '127.0.0.1:8087'.
if (Utility.FqiGeneratorEnv == EnumFqiGeneratorEnv.Online && System.Net.ServicePointManager.ServerCertificateValidationCallback == null)
{
    System.Net.ServicePointManager.ServerCertificateValidationCallback +=
    (se, cert, chain, sslerror) =>
    {
        return true;
    };
}
#endif

// 调用服务方法
client.GetMessage("Kai");
```

上述代码中用到的Class：

```csharp
    /// <summary>
    /// Message inspector used to add the User-Agent HTTP Header to the WCF calls for Server
    /// </summary>
    public class AddUserAgentClientMessageInspector : IClientMessageInspector
    {
        public object BeforeSendRequest(ref System.ServiceModel.Channels.Message request, IClientChannel channel)
        {
            var userAgent = "MyUserAgent/1.0.0.0";

            if (request.Properties.Count == 0 || request.Properties[HttpRequestMessageProperty.Name] == null)
            {
                var property = new HttpRequestMessageProperty();
                property.Headers["User-Agent"] = userAgent;
                request.Properties.Add(HttpRequestMessageProperty.Name, property);
            }
            else
            {
                ((HttpRequestMessageProperty)request.Properties[HttpRequestMessageProperty.Name]).Headers["User-Agent"] = userAgent;
            }
            return null;
        }

        public void AfterReceiveReply(ref System.ServiceModel.Channels.Message reply, object correlationState)
        {
        }
    }

    /// <summary>
    /// Endpoint behavior used to add the User-Agent HTTP Header to WCF calls for Server
    /// </summary>
    public class AddUserAgentEndpointBehavior : IEndpointBehavior
    {
        public void ApplyClientBehavior(ServiceEndpoint endpoint, ClientRuntime clientRuntime)
        {
            clientRuntime.MessageInspectors.Add(new AddUserAgentClientMessageInspector());
        }

        public void AddBindingParameters(ServiceEndpoint endpoint, BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceEndpoint endpoint, EndpointDispatcher endpointDispatcher)
        {
        }

        public void Validate(ServiceEndpoint endpoint)
        {
        }
    }
```