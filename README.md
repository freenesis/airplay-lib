# java-airplay-lib

[![build](https://github.com/freenesis/airplay-lib/actions/workflows/build.yaml/badge.svg)](https://github.com/freenesis/airplay-lib/actions/workflows/build.yaml)


This library is intended to easily create AirPlay2 servers acting like Apple TV. Tested with iPhone X (iOS 14.0.1)

## How to use?

* Add airplay-lib [dependency](https://github.com/freenesis/airplay-lib/packages/1990857) to your project

* Make your server discoverable by [Bonjour](https://ru.wikipedia.org/wiki/Bonjour)

```java
  String serverName = "AirplayServer";
  int airPlayPort = 5001;
  int airTunesPort = 7001;
  AirPlayBonjour airPlayBonjour = new AirPlayBonjour(serverName);
  airPlayBonjour.start(airPlayPort, airTunesPort);
  ...
  airPlayBonjour.stop();
```

* Listen airTunesPort and handle RTSP requests. Pass request content bytes to the library and respond with provided content bytes.

```java

  RTSP GET | POST

  String uri = ...
  byte[] requestContent = ...
  switch (uri) {
    case "/info": {
      airPlay.info(.. byte output stream ..);
      // RTSP OK + provided bytes 
    }
    case "/pair-setup": {
      airPlay.pairSetup(.. byte output stream ..);
      // RTSP OK + provided bytes 
    }
    case "/pair-verify": {
      airPlay.pairVerify(.. requestContent input stream ..,
        .. byte output stream ..);
      // RTSP OK + provided bytes 
    }
    case "/fp-setup": {
      airPlay.fairPlaySetup(.. requestContent input stream ..,
        .. byte output stream ..);
      // RTSP OK + provided bytes
    }
    case "/feedback": {
      // RTSP OK
    }
  }
  
  RTSP SETUP
  
    airPlay.rtspSetup(.. requestContent input stream ..,
      .. byte output stream .., int videoDataPort, int videoEventPort,
      int videoTimingPort, int audioDataPort, int audioControlPort); 
    // RTSP OK + provided bytes
      
    if (airPlay.isFairPlayVideoDecryptorReady()) {
      // start listening video data on videoDataPort 
    }
    
    if (airPlay.isFairPlayAudioDecryptorReady()) {
      // start listening audio data on audioDataPort 
    }
  
  RTSP GET_PARAMETER, RECORD, SET_PARAMETER, TEARDOWN
  
  ...
  
  DECRYPT MIRROR DATA
    
    airPlay.decryptVideo(byte[] video);
    
    airPlay.decryptAudio(byte[] audio, int audioLength);
```

## Example server

[airplay-server](https://github.com/freenesis/airplay-server) with Netty


## Links

[Analysis of AirPlay2 Technology](http://www.programmersought.com/article/2084789418/)