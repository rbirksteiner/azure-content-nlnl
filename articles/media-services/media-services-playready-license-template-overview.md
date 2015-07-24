<properties 
	pageTitle="媒體服務 PlayReady 授權範本概觀" 
	description="本主題提供了用來設定 PlayReady 授權之 PlayReady 授權範本的概觀。" 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#媒體服務 PlayReady 授權範本概觀

Azure 媒體服務現在提供一種服務，來傳遞 Microsoft PlayReady 授權。使用者播放程式 (例如 Silverlight) 嘗試播放 PlayReady 保護內容時，會將要求傳送到授權傳遞服務來取得授權。如果授權服務核准要求，就會發出傳送給用戶端並可用來解密和播放所指定內容的授權。

媒體服務也會提供可讓您設定 PlayReady 授權的 API。授權包含您要 PlayReady DRM 執行階段在使用者嘗試播放受保護內容時強制執行的權限和限制。以下是您可以指定之 PlayReady 授權限制的一些範例：

- 授權從此時間期間起有效的 DateTime。
- 授權過期的 DateTime 值。 
- 針對要儲存在用戶端上永續性儲存體的授權。永續性授權通常會用來允許離線播放內容。
- 播放器播放您的內容必須具有的最低安全性層級。 
- audio\\video 內容的輸出控制輸出保護層級。 
- 如需詳細資訊，請參閱 [PlayReady 法規規則](https://www.microsoft.com/playready/licensing/compliance/)文件中的「輸出控制」區段 (3.5)。

>[AZURE.NOTE]目前，您只能設定 PlayReady 授權的 PlayRight (這是必要權限)。PlayRight 可讓用戶端播放內容。PlayRight 也可讓設定限制專屬於播放。如需詳細資訊，請參閱 [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight)。

若要使用媒體服務設定 PlayReady 授權，您必須設定媒體服務 PlayReady 授權範本。範本會在 XML 中定義。

下列範例顯示設定基本串流授權的最簡單 (也是最常見) 範本。使用此授權，您的用戶端就可以播放您的 PlayReady 受保護內容。
	
	<?xml version="1.0" encoding="utf-8"?>
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" 
	                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <PlayRight />
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

XML 符合 PlayReady 授權範本 XML 結構描述，該結構描述是在 PlayReady 授權範本 XML 結構描述區段中定義。

媒體服務也會定義一組可以用來在 XML 中序列化和還原序列化的 .NET 類別。對於主要類別的描述，請參閱 [媒體服務 .NET 類別]((media-services-playready-license-template-overview.md#classes)。用於設定授權範本。

如需使用 .NET 類別來設定 PlayReady 授權範本的端對端範例，請參閱[使用 PlayReady 動態加密和授權傳遞服務](https://msdn.microsoft.com/library/azure/dn783467.aspx)。

##<a id="classes"></a>用來設定授權範本的媒體服務 .NET 類別

以下是主要的 .NET 類別，可用於設定媒體服務 PlayReady 授權範本。這些類別對應至 [PlayReady 授權範本 XML 結構描述](media-services-playready-license-template-overview.md#schema)中定義的類型。

[MediaServicesLicenseTemplateSerializer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer.aspx) 類別是用來序列化和還原序列化媒體服務授權範本 XML。

###PlayReadyLicenseResponseTemplate

[PlayReadyLicenseResponseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate.aspx) - 此類別代表傳回給使用者之回應的範本。它包含授權伺服器與應用程式之間的自訂資料字串的欄位 (對於自訂應用程式邏輯很有用)，以及一或多個授權範本的清單。

這是範本階層中的「最上層」類別。表示回應範本包含授權範本的清單，而授權範本包含 (直接或間接) 所有其他類別，這些類別組成要序列化的範本資料。


###PlayReadyLicenseTemplate

[PlayReadyLicenseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate.aspx) - 類別代表用於建立 PlayReady 授權 (傳回給使用者) 的授權範本。它包含授權中內容金鑰的資料，和使用內容金鑰時，由 PlayReady DRM 執行階段強制執行的任何權限和限制。


###<a id="PlayReadyPlayRight"></a>PlayReadyPlayRight

[PlayReadyPlayRight](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright.aspx) - 此類別代表 PlayReady 授權的 PlayRight。它授與使用者能力可以播放內容，該內容受限於授權中設定及 PlayRight 本身 (適用於播放特定原則) 的零或多個限制。大部分的 PlayRight 原則與輸出限制相關，控制內容可以播放的輸出類型，和使用指定輸出時必須套用的任何限制。例如，如果 DigitalVideoOnlyContentRestriction 已啟用，則 DRM 執行階段只會允許透過數位輸出 (不允許類比視訊輸出傳遞內容) 顯示視訊。

>[AZURE.IMPORTANT]這些類型的限制非常強大，但也可能會影響客戶體驗。如果輸出保護設定限制太多，內容可能無法在某些用戶端上播放。如需詳細資訊，請參閱「PlayReady 法規規則」文件。

如需 Silverlight 支援的保護層級的範例，請參閱：[Silverlight 支援輸出保護](http://go.microsoft.com/fwlink/?LinkId=617318)。

##<a id="schema"></a>PlayReady 授權範本 XML 結構描述
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
	  <xs:complexType name="AgcAndColorStripeRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
	  <xs:simpleType name="ContentType">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="Unspecified" />
	      <xs:enumeration value="UltravioletDownload" />
	      <xs:enumeration value="UltravioletStreaming" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
	  <xs:complexType name="ExplicitAnalogTelevisionRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
	  <xs:complexType name="PlayReadyContentKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
	  <xs:complexType name="ContentEncryptionKeyFromHeader">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:PlayReadyContentKey">
	        <xs:sequence />
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
	  <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:PlayReadyContentKey">
	        <xs:sequence>
	          <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
	  <xs:complexType name="PlayReadyLicenseResponseTemplate">
	    <xs:sequence>
	      <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
	      <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
	  <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
	  <xs:complexType name="PlayReadyLicenseTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
	      <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
	      <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
	      <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
	  <xs:simpleType name="PlayReadyLicenseType">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="Nonpersistent" />
	      <xs:enumeration value="Persistent" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
	  <xs:complexType name="PlayReadyPlayRight">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
	  <xs:simpleType name="UnknownOutputPassingOption">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="NotAllowed" />
	      <xs:enumeration value="Allowed" />
	      <xs:enumeration value="AllowedWithVideoConstriction" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
	  <xs:complexType name="ScmsRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
	</xs:schema>

<!---HONumber=July15_HO2-->