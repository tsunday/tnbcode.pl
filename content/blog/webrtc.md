+++
categories = ["real-time"]
comments = false
date = "2018-12-26T16:59:13-04:00"
draft = false
showpagemeta = true
showcomments = false
slug = ""
tags = ["real-time", "rtc", "webrtc", "javascript"]
title = "WebRTC - basic steps"
description = "Realtime communications in the browser"

+++

## Realtime communications in the browser

How many of your friends are still using Skype or any kind of native software to have a video chat? In the era of Google Hangouts, Meet or Discord you don't need any other application than the browser of your choice. The reason is that our browsers became powerful platforms that can, besides of showing formatted texts, render graphics, access your devices and stream data using complex network protocols. Today it's better to provide a single native platform, than many specialised applications for different needs.

One of such functionalities built-in the browser is Web Real-Time Communication (WebRTC) module, that allows you to build skype-like web apps. They can use your mic and camera to stream data between other browsers bidirectionally. In this article we're going to get familiar with basics of WebRTC and build simple video chat application

## WebRTC - in detail

WebRTC is an open source C++ framework that allows real-time communications in the web. It's integrated in the most of the web browsers like e.g. Chrome, Opera or Firefox and it's standardized in WebRTC API maintained by W3C. It's ready to use by web developers. WebRTC is built of many smart technologies around network protocols and audio/video processing. The most problematic part of such communication is transferring data directly between the browsers in the web. As the computers are hidden behind network address translation units (NAT), it's not trivial to send data directly to another PC. WebRTC uses Interactive Connectivity Establishment ([ICE](https://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment)) technique that uses [STUN](https://en.wikipedia.org/wiki/STUN) or (more advanced) [TURN](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT)] protocol to determine the optimal path for network packets. Data being send over the network cables should also be properly compressed and optimized. Browsers are also supplied with the best audio/video codecs that allow to prepare such media streams efficiently.

![WebRTC architecture](https://webrtc.org/assets/images/webrtc-public-diagram-for-website.png)
*WebRTC architecture - https://webrtc.org/architecture/*

## Let's build the simple chat application

### Prerequesites

Some parts of WebRTC API are incompatible between different browsers. To provide a generic solution we can use a polyfill package called [adapter](https://github.com/webrtc/adapter).

For simplification, in our examples we will use a small `index.html` file with a few `video` elements in the body:

`<video playsinline autoplay></video>`

and `adapter` script dependency:

`<script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>`

You can also use the package from `npm` or `bower` repository to build your source package with `webpack` or other module bundler.

Javascript calls can be triggered from browser's console (hit F12) or your `.js` source file.

### Accessing peripherals ([ex.1](https://codepen.io/tnbcode/pen/MqJWVd))

First of all, we need access to the peripherals - microphone, speaker and camera. To achieve this, we can use `MediaDevices.getUserMedia()` function. It returnes a `Promise` that resolves to a `MediaStream` object, that includes audio and video tracks. This action requires user permission to access the devices. `getUserMedia()` takes `constraints` object specyfing the media types requested - we can choose audio, video or both.

```
const video = document.querySelector('video');
navigator.mediaDevices.getUserMedia({audio: true, video: true}).then(stream => video.srcObject = stream).catch(err => console.log(err));
``` 

Hello! With this two lines, you should be able to see yourself in the browser window.

>It's also possible to configure audio/video tracks, by passing configuration properties instead of boolean values in the `constraints` object.

Full example can be found [here in the online editor](https://codepen.io/tnbcode/pen/MqJWVd)

### Streaming data ([ex.2](https://codepen.io/tnbcode/pen/ZMoMPO?editors=1010#0))

Having access to media devices is crucial for audio/video communication. Now, it's the time to "send" data between the browsers. In this tutorial, we won't send anything throught the network - for the needs of testing the communication will be placed on the same machine. 

WebRTC API provides `RTCPeerConnection` interface that stands for connection between local and remote computer.

In this example we'll use two `video` elements - for remote and local peers. As both elements are displayed on the same page, we don't have to use signaling server to negotiate the session.

#### Get `MediaStream` from devices
Firstly, we need to access media devices (just like in the previous example):

```
navigator.mediaDevices.getUserMedia({ audio: true, video: true })
.then(stream => {
    local.srcObject = stream;
    localStream = stream;
})
.catch(err => console.log(err));
```

The only difference is that we are storing `MediaStream` object in a local variable.

#### Establish peer connection

Our `call()` function - used for establishing connection between the peers - is a bit elaborated, so let's take a step-by-step analysis. 

> `pc1` and `pc2` variables stand for our local and remote `RTCPeerConnection` objects.

1. Create `RTCPeerConnection` objects (should be called on both sides - remote and local).

```
const pc1 = new RTCPeerConnection();
pc1.onicecandidate = e => pc2.addIceCandidate(e.candidate);

const pc2 = new RTCPeerConnection();
pc2.onicecandidate = e => pc1.addIceCandidate(e.candidate);
```

In a real world, setting ICE candidate should be handled by signalling server. This is our first "Hey! I'm here". Normally it's not possible to set candidate on the other side peer just like that, as it should be send by network. However we can make some shortcuts to keep this example clear.

2. Set remote peer `ontrack` callback.
```
pc2.ontrack = e => {
    remote.srcObject = e.streams[0];
};
```

When the remote peer adds a track, this callback function is executed. So that, we can set our `video` element to display the `MediaStream` - notice that they may be more than one stream in the event.

3. Add local tracks to send them over peer connection.
```
localStream.getTracks().forEach(track => pc1.addTrack(track, localStream));
```
With this line we're adding our local stream tracks (audio and video) to the list of tracks being sent over the network.

4. Create offer - start the magic.

With `createOffer` API method, we can get Session Description Protocol object with all of necessary information about the real-time communication that's going to be invoked. It takes `RTCOfferOptions` object, that can be used to adjust the offer.

```
pc1.createOffer().then(description => {
    pc1.setLocalDescription(description);
    pc2.setRemoteDescription(description);
    pc2.createAnswer().then(description => {
        pc2.setLocalDescription(description);
        pc1.setRemoteDescription(description);
    })
})
```

As a result of this method, we'll get the `Promise` that resolves to SDP object that has to be properly assigned on both local and remote sides. For `pc2` description object resolved with `createOffer` contains remote peer connection details. Finally, to make the communication work, remote peer has to answer with it's own SDP description - again, description object should be properly assigned on both of the sides.

It works! Data stream from our local device is being sent over WebRTC and received properly in our simulated remote peer. 

Full example can be found [here](https://codepen.io/tnbcode/pen/ZMoMPO?editors=1010#0)

## What's next?

This tutorial was meant to show you basic usages and concepts around WebRTC. We've built a simple app that uses whole set of protocols and native browser modules to send audio/video stream over from one peer to another. 

Next step, would be making this work between browser pages (e.g. with a use of [localStorage to simulate socket connection](https://stackoverflow.com/a/35300241)) or even two machines. We could write our own signaling server, to make such basic connection work with session negotiation over the network. For production usage, it's mostly recommended to use one of dedicated media servers that acts as Multipoint Conferencing Unit (MCU). With this we can built multi-endpoint connection (like group video chat) or mix audio and video streams from multiple sources. One of such media servers is [Kurento](http://www.kurento.org/), which provides advanced tools for building rich real-time audio/video communications over the network.

## Reference

- https://webrtc.github.io/samples/

- https://www.html5rocks.com/en/tutorials/webrtc/basics/

- https://stackoverflow.com/questions/35268660/webrtc-between-two-pages-in-the-same-machine

- https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API

- https://webrtc.org/architecture/

