<properties
    pageTitle="檢視存取控制服務 (Java) 所傳回的 SAML"
    description="了解如何在裝載於 Azure 上的 Java 應用程式中，檢視存取控制服務所傳回的 SAML。"
	services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="06/03/2015"
    ms.author="robmcm" />

# 如何檢視 Azure 存取控制服務傳回的 SAML

本指南說明如何檢視 Azure 存取控制服務 (ACS) 傳回給應用程式的基本安全性聲明標記語言 (SAML)。本指南以＜[如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者][]＞主題為基礎，提供程式碼來顯示 SAML 資訊。完成後的應用程式如下所示：

![Example SAML output][saml_output]

如需 ACS 的詳細資訊，請參閱[後續步驟](#next_steps)一節。

> [AZURE.NOTE]Azure Access Services Control Filter (由 Microsoft Open Technologies 提供) 是社群技術預覽。作為發行前軟體，它尚未得到 Microsoft Open Technologies, Inc. 及 Microsoft 的正式支援。

## 必要條件

若要完成本指南中的工作，請完成＜[如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者][]＞中的範例，並將它當做本教學課程的起點。

## 將 JspWriter 程式庫加入至組建路徑和部署組件

將含有 **javax.servlet.jsp.JspWriter** 類別的程式庫加入至組建路徑和部署組件。如果使用 Tomcat，則程式庫是位於 Apache **lib** 資料夾中的 **jsp-api.jar**。

1. 在 Eclipse 的 [專案瀏覽器]，以滑鼠右鍵按一下 [MyACSHelloWorld]，然後依序按一下 [建置路徑]、[配置建置路徑]、[程式庫] 索引標籤及 [新增外部 JAR]。
2. 在 [選取 JAR] 對話方塊中，瀏覽至所需的 JAR 並選取，然後按一下 [開啟]。
3. 在 [MyACSHelloWorld 的內容] 對話方塊仍開啟的情況下，按一下 [部署組件]。
4. 在 [Web 部署組件] 對話方塊中，按一下 [新增]。
5. 在 [New Assembly Directive] 對話方塊中，按一下 [Java Build Path Entries]，再按 [下一步]。
6. 選取適當的程式庫，然後按一下 [完成]。
7. 按一下 [確定] 以關閉 [Properties for MyACSHelloWorld] 對話方塊。

## 修改 JSP 檔案來顯示 SAML

修改 **index.jsp** 來使用下列程式碼。

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	    <%@ page import="javax.xml.parsers.*"
	             import="javax.xml.transform.*"
	             import="org.w3c.dom.*"
	             import="java.io.*"
	             import="javax.xml.transform.stream.*"
	             import="javax.xml.transform.dom.*"
	             import="javax.xml.xpath.*"
	             import="javax.servlet.jsp.JspWriter" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Sample ACS Filter</title>
	</head>
	<body>
		<h3>SAML information from sample ACS program</h3>
		<%!
	    void displaySAMLInfo(Node node, String parent, JspWriter out)
	    {
	    
		    try
		    {
				String nodeName;
			    int nChild, i;
			    
			    nodeName = node.getNodeName();
			    out.println("<br>");
			    out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
			       
			       // Attributes.
			       NamedNodeMap attribsMap = node.getAttributes();
			       if (null != attribsMap)
			       {
	                     for (i=0; i < attribsMap.getLength(); i++)
	                     {
	                            Node attrib = attribsMap.item(i);
	                            out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
	                     }
			       }
			       
			       // Child nodes.
			       NodeList list = node.getChildNodes();
			       if (null != list)
	 		       {
			              nChild = list.getLength();
			              if (nChild > 0)
			              {                    
	
				                 // If it is a text node, just print the text.
				                 if (list.item(0).getNodeName() == "#text")
				                 {
	                                 out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
				                 }
				                 else
				                 {
				                	 // Print out the child node names.
				                	 out.print("Contains " + nChild + " child node(s): ");   
		   		                     for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    
					                    out.print("<b>" + temp.getNodeName() + "</b>");
					                    if (i < nChild - 1)
					                    {
					                    	// Separate the names.
					                    	out.print(", ");
					                    }
					                    else
					                    {
					                    	// Finish the sentence.
					                    	out.print(".");
					                    }
					                    	
				                     }
					                 out.println("<br>");
					                 
					                 // Process the child nodes.
					                 for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    displaySAMLInfo(temp, parent + nodeName + "", out);
				                     }
				               }
			              }
			          }
			      }
			    catch (Exception e)
			    {
			    	System.out.println("Exception encountered.");
			    	e.printStackTrace();	    	
			    }
		    }
	    %>
	
	    <%
	    try 
	    {
		    String data  = (String) request.getAttribute("ACSSAML");
		    
		    DocumentBuilder docBuilder;
			Document doc = null;
			DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
			docBuilderFactory.setIgnoringElementContentWhitespace(true);
			docBuilder = docBuilderFactory.newDocumentBuilder();
			byte[] xmlDATA = data.getBytes();
			
			ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
			doc = docBuilder.parse(in);
			doc.getDocumentElement().normalize();
			
			// Iterate the child nodes of the doc.
	        NodeList list = doc.getChildNodes();
	
	        for (int i=0; i < list.getLength(); i++)
	        {
	        	displaySAMLInfo(list.item(i), "", out);
	        }
		}
	    catch (Exception e) 
	    {
	    	out.println("Exception encountered.");
	    	e.printStackTrace();
		}
	    
	    %>
	</body>
	</html>

## 執行應用程式

1. 使用＜[如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者][]＞中說明的步驟，在電腦模擬器中執行應用程式，或部署至 Azure。
2. 啟動瀏覽器並開啟您的 Web 應用程式。登入應用程式之後，您會看到 SAML 資訊，包括身分識別提供者所提供的安全性聲明。

## 後續步驟

若要進一步探索 ACS 功能及試試其他更精緻的案例，請參閱[存取控制服務 2.0][] (英文)。

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[存取控制服務 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 

<!---HONumber=62-->