# Kaltura Webcasting

The [Kaltura Webcasting suite](https://corp.kaltura.com/video-collaboration-communication/townhalls-live-webcasting/) provides a set of tools to broadcast live events on the web, including interactive video and slides-sync, in-stream audience polls, moderated Q&A chat and live announcements. Kaltura Webcasting currently supports the following live source ingest protocols: RTMP, RTSP, SIP or WebRTC.

The Kaltura suite of Webcasting tools can be integrated as a native component of any web application, and enhance your applications with video live-streaming and webcasting capabilities.
This guide will walk you through the steps needed to set up the Webcast Launcher and embed the Kaltura livestream video player in your webpage.

## Getting Started with Webcasting

To get started, you'll need a Kaltura account, which you can sign up for [here](https://vpaas.kaltura.com/reg/index.php). You'll also need the webcasting module enabled on your account. Speak to your account manager or email us at [vpaas@kaltura.com](mailto:vpaas@kaltura.com) to get your account configured.

Start with the [Python sample app](https://github.com/kaltura-vpaas/webcasting-app-python) it is a turn key integration that works out of the box and can have you up and running in minutes. 

An in depth guide of the webcasting framework is covered [here](https://github.com/kaltura-vpaas/webcasting-integration)

## Webcasting Analytics

See the https://github.com/kaltura-vpaas/embeddable_analytics project which has an entry for webcasting under the "livestream" tab

Kaltura also has a rich Analytics API that can allow you to generate your own reports for your webcasts. You can get started with https://developer.kaltura.com/console/service/report/action/getTable and selecting LIVE in the reportType dropdown. 

## Connecting to a Webcasting Stream via SIP

Kaltura offers [SIP integration](https://knowledge.kaltura.com/help/kaltura-video-conference-integration-v1#activatevcikmsusers) for services like Zoom and Skype. As mentioned previously, webcasting and SIP integration aka VCI must be enabled for your account. Here are API calls to work with SIP and the Kaltura API:

### Check if SIP permission is enabled on this account

Using the [permission.list](https://developer.kaltura.com/console/service/permission/action/list) API Call here are the parameters needed:

```bash
curl -X POST https://www.kaltura.com/api_v3/service/permission/action/list \

  -d "ks=$KALTURA_SESSION" \
  -d "filter[statusEqual]=1" \
  -d "filter[nameEqual]=FEATURE_SIP" \
  -d "filter[objectType]=KalturaPermissionFilter"
```

In the response - Check if "totalCount": 1,--> if it’s 1, than SIP is enabled on this account. If not, go to admin console, configure this account, and add “Kaltura Live - Self Serve enabled”

### Check if the liveStream Entry doesn’t already have a SIP token

[liveStream.get](https://developer.kaltura.com/console/service/liveStream/action/get) 

```bash
curl -X POST https://www.kaltura.com/api_v3/service/livestream/action/get \
    -d "ks=$KALTURA_SESSION" \
    -d "entryId=[YOUR_ENTRY_ID]" \
    -d "version=-1"
```

In the response - Check if "sipToken": null or empty, --> that means you don’t yet have a token

### Generate the SIP token

[pexip.generateSipUrl](https://developer.kaltura.com/console/service/pexip/action/generateSipUrl)  

Based on the result of previous step, if the entry already has a sipToken, and you wish to regenerate a new one – make sure to pass true in the regenerate param below. If the entry does not yet have a sipToken, pass false in regenerate below.

```bash
curl -X POST https://www.kaltura.com/api_v3/service/sip_pexip/action/generateSipUrl \
    -d "ks=$KALTURA_SESSION" \
    -d "entryId=[YOUR_ENTRY_ID]" \
    -d "regenerate=true"
```