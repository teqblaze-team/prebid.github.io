#### Server-side Rewarded Ad Unit Configuration

The Rewarded Ad Unit assumes special behavior that should be configurable by the platform or publisher according to the application or ad experience guides.  

Configuration of rewarded ad unit can be done using stored impression-level stored request and the [passthrough](https://docs.prebid.org/prebid-server/endpoints/openrtb2/pbs-endpoint-auction.html#request-passthrough) feature of Prebid Server. 

Prebid SDK will search for a particular `rwdd` object in `ext.prebid.passthrough` of bid response to configure the behavior of the Rewarded Ad Unit. The following table describes the structure and usage purpose of `rwdd` configuration parameters.

{: .table .table-bordered .table-striped }

| Attribute | Type | Description | Example |  
|-----------|------|-------------|---------|
| `reward`             | object <br> (optional)   | Metadata provided by the publisher to describe the reward.                                                             |<pre>{<br>&nbsp;"type": "SuperDollars", <br>&nbsp;"count": 10<br>}</pre> | 
| `reward.type`        | string   | Type of the reward in the app's coins. | `"SuperDollars"` | 
| `reward.count`       | integer  | Amount of coins. | `10` | 
| `reward.ext`         | object   | For future extensions. | `{"ext":{}}` | 
| `completion`         | object <br> (optional)   | Describes the condition when the SDK should send a signal to the app that the user has earned the reward. | `{ "video": { "endcard": { "time": 5 } } }` | 
| `completion.banner`  | object   | Details for banner ad completion. | `{ "time": 5, "event": "custom_event_url" }` | 
| `completion.banner.time`   | integer  | Period of time the banner ad is on screen. | `5` | 
| `completion.banner.event`  | string   | URL with custom schema sent by the creative to indicate that the user did earn a reward. | `"rwdd://userDidEarnReward"` | 
| `completion.video`   | object   | Details for video ad completion. | `{ "endcard": { "time": 5 } }` | 
| `completion.video.time`    | integer  | Period of time the video ad is on screen. | `10` | 
| `completion.video.playbackevent` | string   | The playback event stage in the video. | `"start"`, `"firstquartile"`, `"midpoint"`, `"thirdquartile"`, `"complete"` | 
| `completion.video.endcard` | object   | Properties for the end card. | `{ "time": 5 }` | 
| `completion.video.endcard.time` | integer  | Period of time the end card is on screen. | `5` | 
| `completion.video.endcard.event` | string   | URL with custom schema sent by the creative for end card. | `"rwdd://userDidEarnReward"` | 
| `close`              | object <br> (optional)   | Describes the ad close behavior after the reward is earned. | `{ "postrewardtime": 3, "action": "autoclose" }` | 
| `close.postrewardtime` | integer  | Time interval (seconds) after reward event when SDK should close interstitial. | `3` | 
| `close.action`       | string   | Action SDK should make: `"autoclose"` (close interstitial) or `"closebutton"` (show close button) | `"autoclose"` | 


An example of an impression-level stored request:

```json
{
  "ext": {
    "prebid": {
      "storedauctionresponse": {
        "id": "prebid-response-video-rewarded-endcard"
      },
      "passthrough": [
        {
          "type": "prebidmobilesdk",
          "rwdd": {
            "reward": {
              "type": "SuperDollars",
              "count": 10
            },
            "completion": {
              "video": {
                "endcard": {
                  "time": 5
                }
              }
            },
            "close": {
              "postrewardtime": 3,
              "action": "autoclose"
            }
          }
        }
      ]
    }
  }
}
```

More details about the SDK behavior according to the `rwdd` configuration you can find in the [GitHub Proposal](https://github.com/prebid/prebid-mobile-ios/pull/1058).