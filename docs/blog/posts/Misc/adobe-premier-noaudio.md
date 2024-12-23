---
draft: false 
date: 2024-07-28
authors: ["vettom"]
categories:
  - Adobe
  - premier
  - audio
---
# Adobe Premier : no audio while editing (Mac OS)
While editing video's on Adobe premier, there could be scenario where audio not working while performing edit. You may also get error stating `The sample rate is not supported by current audio device`. Video itself plays fine on Mac. Changing output hardware does not make difference!

![Adobe premier audio error ](https://vettom-images.s3.eu-west-1.amazonaws.com/others/premier-audio-error.jpg){: style="width:400px" }

It turns out that if Input or output hardware does not support required sample rate, Premier stops the audio completely! In my case my Audio input device was Bose QC35 and Bose Speakers via USB. Since Input device is not supported, audio failed to work despite changing to various output devices include MacBook speaker. Fixed only when input device changed. 

### Solution
Go to Adobe Premier -> settings -> Audio Hardware and update Input/Output device to working device configuration

![Adobe premier audio fix ](https://vettom-images.s3.eu-west-1.amazonaws.com/others/premier-audio-fix.jpg){: style="width:400px" }


### Alternative
Launch `Audio Midi Setup.app` on Mac and try to match sample rate of your device

![Mac midi audio setup ](https://vettom-images.s3.eu-west-1.amazonaws.com/others/midi-audio.png){: style="width:400px" }
