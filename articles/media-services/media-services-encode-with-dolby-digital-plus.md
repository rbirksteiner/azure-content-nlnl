<properties 
	pageTitle="使用 Dolby Digital Plus 將您的媒體編碼" 
	description="本主題說明如何使用 Dolby Digital Plus 將您的媒體編碼。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>

#使用 Dolby Digital Plus 將您的媒體編碼

Azure Media Encoder 支援 **Dolby® Digital Plus** 編碼。Dolby® Digital Plus (或稱為增強式 AC-3，E-AC-3) 是專為不斷演進的媒體而設計的先進環繞聲音訊轉碼器。從家用劇院、電腦到行動電話和線上串流，Dolby Digital Plus 代表了高逼真度音效。您可以從所有娛樂項目體驗到著名的杜比劇院效果。Dolby Digital Plus 以核心杜比數位技術為基礎，這是電影院、廣播和家庭劇院環繞聲的確立標準。隨著行動裝置日漸普及，Dolby Digital Plus 也逐漸成為行動娛樂的標準。其先進的音訊增強新技術可提供更佳的音效品質，外帶節省頻寬。即使在頻寬受限的情況下，您仍可獲得優質內容，同時減少中斷。


##設定 Azure Media Encoder 以 Dolby Digital Plus 進行編碼

###取得 Azure Media Encoder 處理器 

Azure Media Encoder 支援 Dolby Digital Plus。若想取得 **Azure Media Encoder** 參考，請參閱[取得媒體處理器](media-services-get-media-processor.md)主題。

###<a id="configure_preset"></a>配置 Azure Media Encoder 設定

配置 Azure Media Encoder 使用的編碼設定時，已有許多預先定義的預設以易記字串來呈現。Dolby Digital Plus 編碼器提供豐富的控制項，如需詳細資訊請參閱 [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx)。因此，沒有使用此轉碼器的預先建置字串預設。您必須在 XML 檔案中指定您想要的編碼器設定，然後將本資料與您的工作一起提交，如下列程式碼範例所示：
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

本主題說明配置編碼器設定的數個 XML 預設範例。用來設定 Dolby Digital Plus 編碼的元素是 [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx)，此元素顯示為 Azure Media Encoder XML 預設之 <AudioProfile> 元素的子節點。此 XML 元素包含多個屬性，可以控制編碼的各種元素。

##編碼為 Dolby Digital Plus 5.1 Multichannel

若要編碼為 Dolby Digital Plus 5.1 Multichannel，請設定 Codec 和 EncoderMode 屬性為 "DolbyDigitalPlus"。編碼的頻道數目是使用 <DolbyDigitalPlusAudioProfile> 元素的 AudioCodingMode 屬性來指定。針對 5.1 多聲道編碼，請設定 AudioCodingMode 為 "Mode32"。

下列 XML 預設包含完整的 Azure Media Encoder XML 預設，可產生 H264 頻寬 1080p 視訊並搭配 Dolby Digital Plus 5.1 Multichannel 音訊的 MP4 檔案。此預設也會指定編碼低頻音效 (LFE) 頻道，這由設定 LFEOn 屬性為 true 來指定。未指定的任何屬性都有其預設值。

如同[此主題](media-services-dotnet-encode-asset.md)所述，此 XML 預設應該傳送至 **Azure Media Encoder** 以建立編碼工作 (如同[這裡](#configure_preset)所述，您應該傳送整個 XML 預設，而不是預先定義的預設字串)。


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##編碼為 Dolby Digital Plus Stereo

若要編碼為 Dolby Digital Plus Stereo，請設定 Codec 和 EncoderMode 屬性為 "DolbyDigitalPlus"。編碼的頻道數目是使用 AudioCodingMode 屬性來指定。針對立體聲編碼，請設定 AudioCodingMode 為 "Mode20"。下列 XML 預設範例顯示用來編碼的 <DolbyDigitalPlusAudioProfile> 設定為 5.1 聲道。未指定的任何屬性都有其預設值。

如同[此主題](media-services-dotnet-encode-asset.md)所述，此 XML 預設應該傳送至 **Azure Media Encoder** 以建立編碼工作 (如同[這裡](#configure_preset)所述，您應該傳送整個 XML 預設，而不是預先定義的預設字串)。

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##編碼為多個 MP4 檔案 

您可以在單一 XML 預設中編碼為多個 MP4。針對每個想要產生的 MP4，請在組態中加入 <Preset> 元素。每個 <Preset> 元素可以包含視訊、音訊或兩者的組態資訊。

###組態

下列組態會產生下列輸出：

- 8 個只有視訊的 MP4 檔案
	- 6000 kbps 的 1080p 視訊
	- 4700 kbps 的 1080p 視訊
	- 3400 kbps 的 720p 視訊
	- 2250 kbps 的 960 x 540 視訊
	- 1500 kbps 的 960 x 540 視訊
	- 1000 kbps 的 640 x 380 視訊
	- 650 kbps 的 640 x 380 視訊
	- 400 kbps 的 320 x 180 視訊

- 5 個只有音訊的 MP4 檔案
	- 128 kbp 的 AAC Audio Stereo
	- 512 kbp 的 AAC Audio 5.1
	- 128 kbps 的 Dolby Digital Plus Stereo
	- 512 kbps 的 Dolby Digital Plus 5.1 Multichannel
	- 56 kbps 的 AAC Stereo
- 一個 .ism 資訊清單
- 列出所產生 MP4 檔案之屬性的 XML 檔案。
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##建立商業編碼服務

有些客戶可能會想要利用 Azure 媒體服務來建置商業編碼服務。如果您要建立此類「內建」服務，請務必讓所有 Dolby Digital Plus 編碼參數都可供使用。請確定 <DolbyDigitalPlusAudioProfile> 標記內的所有參數都已公開，並可讓使用者設定。如需讓這些參數可供使用的指引，請連絡 prolicensingsupport@dolby.com。

##使用 Dolby Professional Loudness Metering (DPLM) 支援

Azure Media Encoder 可以使用 DPLM SDK 來測量輸入音訊的對話音量，並設定 DialogNormalization 的正確值。音訊已編碼為 Dolby Digital Plus 時才會啟用這項功能。預設組態檔中使用 <LoudnessMetering> 元素 (<DolbyDigitalPlusAudioProfile> 元素的子項) 來設定 DPLM。下列預設範例顯示如何設定 DPLM：
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

<LoudnessMetering> 元素只能在 <DolbyDigitalPlusAudioProfile> 元素中指定。如果已使用 <LoudnessMetering> 元素，就不能使用 DialogNormalization 屬性。如果同時使用了 <LoudnessMetering> 元素和 DialogNormalization 屬性，編碼器會產生錯誤。LoudnessMetering 的所有屬性都是選擇性，且編碼器會預設為 Dolby Laboratories, Inc. 建議的值。

下列各節說明每個屬性。

###Mode 屬性

此屬性決定音量計量模式。允許的值包括：

 
**ITU_R_BS_1770_2_DI** (預設值) - 表示 ITU-R BS.1770-2 plus Dialogue Intelligence

**ITU_R_BS_1770_1_DI** - 表示 ITU-R BS.1770-1 plus Dialogue Intelligence

**ITU_R_BS_1770_2** - 表示 ITU-R BS.1770-2

**LEQA_DI** - 表示 Leq(A) plus Dialogue Intelligence

**注意：**

** EBU R128** 模式可透過 **ITU_R_BS_1770_2_DI** 達到

納入 **Leq(A)** 純粹為考量舊版，僅應用於特定的舊版工作流程

**ITU** 最近發行了名為 BS.1770-3 的更新，這相當於同時將 TruePeakDCBlock 及 TruePeakEmphasis 設為 false 的 BS.1770-2

###SpeechThreshold 屬性

指定由 DPLM 用來產生整合音量結果的語音閾值 (例如，可選取語音閘控、等級閘控及無閘控)。語音閾值設定範圍從 0%到 100%，每次增加 1%。只有當 DPLM 設定為使用 Dialogue Intelligence 的模式時，此參數才有作用，這表示只有在 Mode 設定為 ITU_R_BS_1770_2_DI 或 ITU_R_BS_1770_1_DI 時，才能指定此參數。Mode 為 ITU_R_BS_1770_2_DI 或 ITU_R_BS_1770_1_DI 時的預設值為 20%。此屬性的值必須設定在 0、1 – 100 的範圍內。

###TruePeakDCBlock 屬性

此輸入參數指定實際峰值計量內的 DC 區塊為啟用 (true) 或停用 (false)。如需 DC 區塊的詳細資料，請參閱 ITU‐R BS.1770‐2。預設值為 false。

###TruePeakEmphasis 屬性

指定實際峰值計量內的加強濾波器為啟用 (true) 或停用 (false)。如需加強濾波器的詳細資料，請參閱 ITU‐R BS.1770‐2。預設值為 false。


###DPLM 結果

當編碼工作指定使用 DPLM 時，輸出資產的中繼資料 XML 會包含音量測量結果。下列是一個範例。
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

每個屬性說明如下。

**DPLMVersionInformation** - 代表所使用 DPLM SDK 版本的字串。

**DialogNormalization** - DialNorm 的值，以分貝為單位，從輸入音訊測量，會嵌入至輸出 DD+ 串流，範圍為 {-31, -30, …, -1} dB。

**IntegratedLoudness** - 由 DPLM 測量的整合音量，範圍從 -70 到 +10 LKFS/dBFS (僅在 Mode 設為 LEQA_DI 時才使用 dBFS)。

**IntegratedLoudnessGatingMethod** - 有效值為：0 – 無/未閘控；1 – 使用語音閘控； 2 – 使用等級閘控。

**IntegratedLoudnessSpeechPercentage** - 此結果包含偵測到語音之輸入媒體時間軸的百分比。值的範圍從 0% 到 100%。

**SamplePeak** - 此結果包含計量重設後任何聲道中的最大絕對樣本值，範圍從 -70 到 +10 dBFS。

**TruePeak** - 此結果包含計量重設後任何聲道中的最大絕對實際峰值。如需實際峰值的說明，請參閱 ITU‐R BS.1770‐2。值的範圍從 -70 到 12.04 dBTP。
 

<!---HONumber=62-->