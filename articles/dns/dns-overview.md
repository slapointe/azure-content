<properties 
   pageTitle="Overview of Azure DNS | Microsoft Azure" 
   description="Overview of Azure DNS hosting services on Microsoft Azure and start hosting your domain on Microsoft Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Azure DNS Overview

Behind any website or service on the Internet, there is an IP address. For example, www.microsoft.com uses the IP address 134.170.185.46. The Domain Name System, or DNS, is responsible for translating (or resolving) the website or service name to its IP address.

Azure DNS is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure. By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools and billing as your other Azure services.

DNS domains in Azure DNS are hosted on Azure’s global network of DNS name servers.  We use Anycast networking, so that each DNS query is answered by the closest available DNS server. This provides both fast performance and high availability for your domain.

The service is based on Azure Resource Manager (ARM). As such, it benefits from ARM features such as role-based access control, audit logs, and resource locking.

Your domains and records can be managed via the Azure portal, Azure PowerShell cmdlets and the cross-platform Azure CLI.  Applications requiring automatic DNS management can integrate with the service via the REST API and SDKs.

Azure DNS does not currently support purchasing of domain names.  To purchase domains you should use a third-party domain name registrar, who will typically charge a small annual fee.  These domains can then be hosted in Azure DNS for management of DNS records—see [Delegate a Domain to Azure DNS](dns-domain-delegation.md) for details.


## Next Steps

[Get started creating DNS zones](dns-getstarted-create-dnszone.md)

[Automate Azure Operations with .NET SDK](dns-sdk.md)

[Azure DNS REST API Reference](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 
