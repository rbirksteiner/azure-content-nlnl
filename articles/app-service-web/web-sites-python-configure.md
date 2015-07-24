<properties 
	pageTitle="使用 Azure App Service Web Apps 設定 Python" 
	description="本教學課程描述在 Azure App Service Web Apps 上編寫與設定基本的 Web 伺服器閘道介面 (WSGI) 相容之 Python 應用程式的選項。" 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>




# 使用 Azure App Service Web Apps 設定 Python

本教學課程描述在 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 上編寫與設定基本的 Web 伺服器閘道介面 (WSGI) 相容之 Python 應用程式的選項。

它會說明 Git 部署的其他功能，例如虛擬環境和使用 requirements.txt 進行封裝安裝。


## Bottle、Django 或 Flask？

Azure Marketplace 包含 Bottle、Django 和 Flask 架構的範本。如果您在 Azure App Service 中開發您的第一個 Web 應用程式，或是您不熟悉 Git，我們建議您遵循這些教學課程，其中包括了在 Windows 或 Mac 使用 Git 部署，從資源庫建置工作應用程式的逐步指示：

- [利用 Bottle 建立 Web 應用程式](web-sites-python-create-deploy-bottle-app.md)
- [利用 Django 建立 Web 應用程式](web-sites-python-create-deploy-django-app.md)
- [利用 Flask 建立 Web 應用程式](web-sites-python-create-deploy-flask-app.md)


## 在 Azure Preview 入口網站中建立 Web 應用程式

本教學課程假設定有現有的 Azure 訂用帳戶，而且能夠存取 Azure Preview 入口網站。

如果您還沒有 Web 應用程式，則可以從 [Azure Preview 入口網站](https://portal.azure.com)建立。按一下左下角的 [新增] 按鈕，然後按一下 [Web + 行動] > [Web 應用程式]。

## Git 發行

遵循[在 Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)的以下指示，為您新建立的 Web 應用程式設定 Git 發行功能。本教學課程將使用 Git 來建立、管理並將您的 Python Web 應用程式發佈至 Azure App Service。

Git 發佈設定完畢後，會建立一個 Git 儲存機制並與您的 Web 應用程式產生關聯。該儲存機制的 URL 會加以顯示，方便您將資料從本機開發環境推送到雲端。若要透過 Git 發佈應用程式，請確保同時安裝了 Git 用戶端，並遵守提供的指示將您的 Web 應用程式內容推送到 Azure App Service。


## 應用程式概觀

在後續章節中，會建立下列檔案。它們應該放在 Git 儲存機制的根目錄中。

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## WSGI 處理常式

WSGI 是由 [PEP 3333](http://www.python.org/dev/peps/pep-3333/) 描述的一項 Python 標準，此標準定義了 Web 伺服器與 Python 之間的介面。此標準為您提供標準化介面，方便您使用 Python 撰寫各種 Web 應用程式與架構。今日熱門的 Python Web 架構都採用 WSGI。Azure App Service Web Apps 針對此類任何架構提供支援，而進階使用者甚至可以撰寫自己的架構，但前提是自訂處理常式必須遵守 WSGI 規範指示。

以下是用於定義自訂處理常式的 `app.py` 範例：

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

您可以搭配 `python app.py` 在本機執行此應用程式，然後在您的網頁瀏覽器中瀏覽到 `http://localhost:5555`。


## 虛擬環境

雖然上述範例應用程式不需要任何外部的封裝，但您的應用程式可能需要。

為了協助管理外部的封裝相依性，Azure Git 部署支援虛擬環境的建立。

當 Azure 偵測到儲存機制根目錄中的 requirements.txt 時，會自動建立名為 `env` 的虛擬環境。這只會發生在第一次部署，或是選取之 Python 執行階段變更之後的任何部署期間。

您可能想要在本機建立虛擬環境以進行開發，但是請勿將它包含在 Git 儲存機制中。


## 封裝管理

Requirements.txt 中所列封裝，將會使用 pip 自動安裝於虛擬環境中。這種情況會發生在每個部署，但是如果已安裝封裝，pip 會跳過安裝。

`requirements.txt` 範例：

    azure==0.8.4


## Python 版本

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

`runtime.txt` 範例：

    python-2.7


## Web.config

您需要建立 web.config 檔來指定伺服器應該如何處理要求。

請注意，如果您在儲存機制中有 web.x.y. 組態檔，其中 x.y 符合所選的 Python 執行階段，則 Azure 會自動複製適當的檔案，做為 web.config。

下列 web.config 範例仰賴虛擬環境 Proxy 指令碼，於下一節中說明。其會使用上述範例 `app.py` 中所使用之 WSGI 處理常式。

Python 2.7 的 `web.config` 範例：

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Python 3.4 的 `web.config` 範例：

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


靜態檔案交由 Web 伺服器直接處理，而不會通過 Python 程式碼，以改善效能。

在上述範例中，磁碟上靜態檔案的位置應該符合在 URL 中的位置。這表示 `http://pythonapp.azurewebsites.net/static/site.css` 要求將於 `\static\site.css` 提供磁碟上的檔案。

有可能將規則 `Static Files` 設定為從不同於 URL 位置的磁碟位置上提供檔案。在下列規則定義中，`http://pythonapp.azurewebsites.net/static/site.css` 將於 `\FlaskWebProject\static\site.css` 提供磁碟上的檔案，而不是 `\static\site.css`。

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` 是您指定 WSGI 處理常式的地方。在上述範例中，為 `app.wsgi_app` 因為處理常式是根資料夾中 `app.py` 內名為 `wsgi_app` 的函式。

`PYTHONPATH` 可自訂，但是如果您藉由在 requirements.txt 中指定相依性，將其全部安裝於虛擬環境中，您應該不需要變更。


## 虛擬環境 Proxy

下列指令碼用來擷取 WSGI 處理常式，會啟動虛擬環境並記錄錯誤。它已設計為 Generic，不需修改就可使用。

`ptvs_virtualenv_proxy.py` 內容：

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
        
        site.main()
        
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## 自訂 Git 部署

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## 疑難排解 - 封裝安裝

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## 疑難排解 - 虛擬環境

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)





 

<!---HONumber=62-->