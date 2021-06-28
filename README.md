## Requirements
- `minSdkVersion 19`
- `AndroidX`

## Getting Started

Add following lines in your root ```build.gradle```
```
allprojects {
    repositories {
        ...
        maven { url "https://gitlab.com/api/v4/projects/24251481/packages/maven" }
    }
}
```

Add following lines in your module level ```build.gradle```
```
dependencies {
    ....
    implementation 'co.invoid.android:headturn:1.0.0rc3'
}
```


## Initialize SDK

```
HeadTurnOptions HeadTurnOptions = new HeadTurnOptions.Builder()
    .setUserId("userId")
    .setNoOfAttempts(5)//Default is infinite
    .build();
HeadTurnHelper.with(context, authKey, headTurnOptions).start();
```
- User id is mandatory. You can pass any random string but make sure you are passing unique userid for you each of your users.
- Use `setNoOfAttempts()` to set allowed no of head turn attempts.

## Response returned from the SDK
- Video file path
- Head turn status<br/>
&nbsp;&nbsp;&nbsp;&nbsp;`HeadTurnHelper.HEAD_TURN_SUCCESS`: Head turn succes <br/>
&nbsp;&nbsp;&nbsp;&nbsp;`HeadTurnHelper.HEAD_TURN_TIMEOUT`: 20seconds exceeded while performing head turn action, head turn failed<br/>
&nbsp;&nbsp;&nbsp;&nbsp;`HeadTurnHelper.HEAD_TURN_COUNT_EXCEEDED` : No of attempts exhausted, head turn failed
- Head turn message 
- Liveness Response<br/>
&nbsp;&nbsp;&nbsp;&nbsp;Use livenessProbabilty `livenessResponse.getProbability()` variable to check the liveness status, if probabilty is null then check for livenessMessage `livenessResponse.getMessage()` to get more information on why probability is null
 - Authorization Result<br/>
&nbsp;&nbsp;&nbsp;&nbsp;You will get authorization related data to check if you are authorised to use the sdk or not

```
@Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        if(requestCode == HeadTurnHelper.HEAD_TURN_HELPER_REQ_CODE) {

            if(resultCode == RESULT_OK) {
                String headTurnstatus = data.getStringExtra(HeadTurnHelper.STATUS);
                String headTurnmessage = data.getStringExtra(HeadTurnHelper.MESSAGE);
                String videoPath = data.getStringExtra(HeadTurnHelper.VIDEO_PATH);
                String photoPath = data.getStringExtra(HeadTurnHelper.PHOTO_PATH);
                LivenessResponse livenessResponse = data.getParcelableExtra(HeadTurnHelper.LIVENESS_REPONSE);

                String livenessProbabilty = livenessResponse.getProbability();
                String livenessMessage = livenessResponse.getMessage();

                if(livenessProbabilty != null) {
                    Log.d("TAG", "probabilty: " + livenessProbabilty);
                }
                if(livenessMessage != null) {
                    Log.d("TAG", "probabilty: " + livenessProbabilty);
                }

            } else if(resultCode == RESULT_CANCELED) {
                Log.d("TAG", "Cancelled by user");

            } else if(resultCode == HeadTurnHelper.AUTHORIZATION_RESULT_CODE) {
                int authorizationResult = data.getIntExtra(HeadTurnHelper.AUTHORIZATION_RESULT, -1);
                if(authorizationResult == HeadTurnHelper.UNAUTHORIZED) {
                    Log.d("TAG", "Not authorised to use the sdk");
                } else {
                    Log.d("TAG", "onActivityResult: authorization error");
                }

            }


        }
        super.onActivityResult(requestCode, resultCode, data);
    }
```

### Notes
- Expected size of video 2-8 mb, max duration 20secs

