## 將訊息傳送至事件中心
在本節中，我們會撰寫一個 C 應用程式，以將事件傳送至事件中心。我們將利用 [Apache Qpid 專案](http://qpid.apache.org/)中的 Proton AMQP 程式庫。這與搭配使用 Service Bus Queues and Topics 與透過 C 的 AMQP 類似 (如[這裡](https://code.msdn.microsoft.com/windowsazure/Using-Apache-Qpid-Proton-C-afd76504)所示)。如需詳細資訊，請參閱 [Qpid Proton 文件](http://qpid.apache.org/proton/index.html)。

1. 從 [Qpid AMQP Messenger 頁面](http://qpid.apache.org/components/messenger/index.html)，按一下 [**安裝 Qpid Proton**] 連結，並遵循指示進行 (視您的環境而定)。我們將假設是 Linux 環境 (例如含 Ubuntu 14.04 的 [Azure Linux VM](../articles/virtual-machines/virtual-machines-linux-tutorial.md))。

2. 若要編譯 Proton 程式庫，請安裝下列封裝：

		sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev

3. 下載 [Qpid Proton 程式庫](http://qpid.apache.org/proton/index.html)，並將其解壓縮，例如：

		wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
		tar xvfz qpid-proton-0.7.tar.gz

4. 建立組建目錄、編譯並安裝：

		cd qpid-proton-0.7
		mkdir build
		cd build
		cmake -DCMAKE_INSTALL_PREFIX=/usr ..
		sudo make install

5. 在工作目錄中，使用下列內容建立一個稱為 **sender.c** 的新檔案。請務必替代您事件中樞名稱和命名空間名稱的值 (後者通常是 `{event hub name}-ns`)。您也必須替代先前針對 **SendRule** 建立之金鑰的 URL 編碼版本。您可以在[這裡](http://www.w3schools.com/tags/ref_urlencode.asp)對其進行 URL 編碼。

		#include "proton/message.h"
		#include "proton/messenger.h"

		#include <getopt.h>
		#include <proton/util.h>
		#include <sys/time.h>
		#include <stddef.h>

		#include <stdio.h>
		#include <string.h>
		#include <unistd.h>
		#include <stdlib.h>

		#define check(messenger)                                                     \
		  {                                                                          \
		    if(pn_messenger_errno(messenger))                                        \
		    {                                                                        \
		      printf("check\n");													 \
		      die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
		    }                                                                        \
		  }  

		pn_timestamp_t time_now(void)
		{
		  struct timeval now;
		  if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
		  return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
		}  

		void die(const char *file, int line, const char *message)
		{
		  printf("Dead\n");
		  fprintf(stderr, "%s:%i: %s\n", file, line, message);
		  exit(1);
		}

		int sendMessage(pn_messenger_t * messenger) {
			char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
			char * msgtext = (char *) "Hello from C!";

			pn_message_t * message;
			pn_data_t * body;
			message = pn_message();

			pn_message_set_address(message, address);
			pn_message_set_content_type(message, (char*) "application/octect-stream");
			pn_message_set_inferred(message, true);

			body = pn_message_body(message);
			pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));

			pn_messenger_put(messenger, message);
			check(messenger);
			pn_messenger_send(messenger, 1);
			check(messenger);

			pn_message_free(message);
		}

		int main(int argc, char** argv) {
			printf("Press Ctrl-C to stop the sender process\n");

			pn_messenger_t *messenger = pn_messenger(NULL);
			pn_messenger_set_outgoing_window(messenger, 1);
			pn_messenger_start(messenger);

			while(true) {
				sendMessage(messenger);
				printf("Sent message\n");
				sleep(1);
			}

			// release messenger resources
			pn_messenger_stop(messenger);
			pn_messenger_free(messenger);

			return 0;
		}

6. 編譯檔案，並假設 **gcc**：

		gcc sender.c -o sender -lqpid-proton

> [AZURE.NOTE]在上面的程式碼中，我們使用輸出視窗 1 盡快強制輸出訊息。應用程式一般應該會嘗試批次處理訊息，以增加輸送量。如需如何在此環境和其他環境中以及從提供繫結的平台 (目前是 Perl、PHP、Python 和 Ruby) 中使用 Qpid Proton 程式庫的詳細資訊，請參閱 [Qpid AMQP Messenger 頁面](http://qpid.apache.org/components/messenger/index.html)。

<!---HONumber=July15_HO2-->