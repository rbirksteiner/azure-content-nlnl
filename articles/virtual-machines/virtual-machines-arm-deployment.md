<properties 
	pageTitle="使用 Compute、Network 和 Storage .NET Library 部署 Azure 資源" 
	description="了解如何使用 Compute、Storage 和 Network .NET Library 中的一些可用用戶端建立和刪除 Microsoft Azure 中的資源" 
	services="virtual-machines,virtual-network,storage" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="davidmu"/>

# 使用 Compute、Network 和 Storage .NET Library 部署 Azure 資源

本教學課程會為您示範如何使用 Compute、Storage 和 Network .NET Library 中的一些可用用戶端建立和刪除 Microsoft Azure 中的資源。它也會為您示範如何使用 Azure Active Directory 驗證對 Azure 資源管理員的要求。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

若要完成本教學課程，您也需要：

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure 儲存體帳戶](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/zh-tw/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/zh-tw/download/details.aspx?id=40855)
- [Azure PowerShell](../install-configure-powershell.md)

執行這些步驟需要 30 分鐘左右。

## 步驟 1：將應用程式新增至 Azure AD，並設定權限

若要使用 Azure AD 驗證對 Azure 資源管理員的要求，必須將應用程式新增至預設目錄中。執行下列操作來新增應用程式：

1. 開啟 Azure PowerShell 提示字元，然後執行此命令：

        Switch-AzureMode –Name AzureResourceManager

2. 設定您想要用於本教學課程的 Azure 帳戶。執行此命令，並在出現提示時，輸入您的訂用帳戶的認證：

	    Add-AzureAccount

3. 將下列命令中的 {password} 取代成您想要使用的密碼，然後執行該命令以建立應用程式：

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. 記錄上一個步驟中回應的 ApplicationId 值。稍後在本教學課程中將會需要這項資訊：

	![建立 AD 應用程式](./media/virtual-machines-arm-deployment/azureapplicationid.png)

	>[AZURE.NOTE]您也可以在「管理入口網站」中應用程式的 [用戶端識別碼] 欄位內尋找應用程式識別碼。

5. 將 {application-id} 取代成您剛才記錄的識別碼，然後建立應用程式的服務主體：

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. 設定使用應用程式的權限：

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 步驟 2：建立 Visual Studio 專案，並安裝程式庫

NuGet 封裝是安裝完成本教學課程所需程式庫最簡單的方式。您必須安裝 Azure Resource Management Library、Azure Active Directory Authentication Library，以及 Computer Resource Provider Library。若要在 Visual Studio 中取得這些程式庫，請執行下列操作：

1. 按一下 [檔案] > [新增] > [專案]。

2. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式]，輸入專案的名稱和位置，然後按一下 [確定]。

3. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [管理 NuGet 封裝]。

4. 在搜尋方塊中輸入 *Active Directory*，對 Active Directory Authentication Library 封裝按一下 [**安裝**]，然後依照指示，安裝封裝。

5. 在頁面的頂端，選取 [包含發行前版本]。在搜尋方塊中輸入 *Azure Compute*，對 Compute .NET Library 按一下 [安裝]，然後依照指示，安裝封裝。

6. 在搜尋方塊中輸入 *Azure Network*，對 Network .NET Library 按一下 [安裝]，然後依照指示，安裝封裝。

7. 在搜尋方塊中輸入 *Azure Storage*，對 Network .NET Library 按一下 [安裝]，然後依照指示，安裝封裝。

8. 在搜尋方塊中輸入 *Azure Resource Management*，對 Resource Management Library 按一下 [安裝]。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

## 步驟 3：建立用來驗證要求的認證

既然已經建立 Azure Active Directory 應用程式並安裝驗證程式庫，您要將應用程式資訊格式化成用來驗證對 Azure 資源管理員的要求的認證。執行下列動作：

1.	開啟您建立之專案的 Program.cs 檔案，然後將下列 using 陳述式加入至檔案的頂端：

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure.Management.Resources;
		using Microsoft.Azure.Management.Resources.Models;
		using Microsoft.Azure.Management.Storage;
		using Microsoft.Azure.Management.Storage.Models;
		using Microsoft.Azure.Management.Network;
		using Microsoft.Azure.Management.Network.Models;
		using Microsoft.Azure.Management.Compute;
		using Microsoft.Azure.Management.Compute.Models;


2. 將下列方法加入至 Program 類別，以取得建立認證所需的權杖：

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	將 {application-id} 取代成您先前記錄的應用程式識別碼、將 {password} 取代成您為 AD 應用程式選擇的密碼，並將 {tenant-id} 取代成您的訂用帳戶的租用戶識別碼。您可以透過執行 Get-AzureSubscription 來尋找租用戶識別碼。

3.	將下列程式碼加入至 Program.cs 檔案的 Main 方法以建立認證：

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	將 {subscription-id} 取代成您的訂用帳戶識別碼。

4.	儲存 Program.cs 檔案。

## 步驟 4：加入程式碼以註冊提供者，並建立資源

### 註冊提供者，並建立資源群組

資源一律會部署到資源群組。您可以使用 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 和 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 類別建立要在其中部署資源的資源群組。

1.	將下列方法加入至 Program 類別以建立資源群組：

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  
          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：

		CreateResourceGroup(credential);
		Console.ReadLine();

###建立儲存體帳戶

儲存針對虛擬機器建立的虛擬硬碟檔案時，需要儲存體帳戶。

1.	將下列方法加入至 Program 類別以建立儲存體帳戶：

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");
          
          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：
		
		CreateStorageAccount(credential);
		Console.ReadLine();

###建立虛擬網路

將虛擬機器加入至虛擬網路之後，虛擬機器最具生產力。

1.	將下列方法加入至 Program 類別以建立子網路、公用 IP 位址和虛擬網路：

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：

		CreateNetwork(credential);
		Console.ReadLine();

###建立虛擬機器

既然您已經建立所有支援的資源，您可以建立虛擬機器。

1.	將下列方法加入至 Program 類別以建立虛擬機器：

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);
                
            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk 
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]映像庫中的映像 VHD 名稱會定期變更，因此您必須取得目前的映像名稱，才能部署虛擬機器。若要這樣做，請參閱[使用 Windows PowerShell 管理映像 Windows](https://msdn.microsoft.com/library/azure/dn790330.aspx)，然後將 {source-image-name} 取代成您想要使用的 VHD 檔案的名稱。例如，"a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd"。

	將 {subscription-id} 取代成您的訂用帳戶的識別碼。

2.	將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：

		CreateVirtualMachine(credential);
        Console.ReadLine();

##步驟 5：加入程式碼以刪除資源

您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。如果您想要刪除虛擬機器及所有支援的資源，您只需要刪除資源群組。

1.	將下列方法加入至 Program 類別以刪除資源群組：

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：

		DeleteResourceGroup(credential);
        Console.ReadLine();

##步驟 6：執行主控台應用程式

1.	若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]，然後使用搭配您的訂用帳戶使用的相同使用者名稱和密碼，登入 Azure AD。

2.	傳回每個狀態碼之後，按下 **Enter** 以建立每個資源。建立虛擬機器之後，請執行下一個步驟，再按 Enter 以刪除所有資源。

	此主控台應用程式從開始到完成的完整執行應該需要 5 分鐘左右。在您按下 Enter 以開始刪除資源之前，可能需要幾分鐘時間，先確認 Azure Preview 入口網站中建立的資源，然後再刪除。

3. 瀏覽至 Azure Preview 入口網站中的稽核記錄，以查看資源的狀態：

	![建立 AD 應用程式](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=July15_HO2-->