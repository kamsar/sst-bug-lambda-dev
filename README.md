To reproduce the bug:

* This repo has a super simple HTTP lambda which responds with hello world and any request body that was sent to it
* Run `npx sst dev` to start live lambda. Note that this bug is NOT present if a full deploy is run, only for `sst dev`.
* Run the following CLI command to POST the large (216kb) JSON body (https://pokeapi.co/api/v2/pokemon/pikachu) to the deployed lambda (using jq to parse the outputs.json to get the URL):

```
curl -XPOST -d @pikachu.json -H"Content-Type: application/json" `jq -r '.[].ApiEndpoint' < .sst/outputs.json`
```

* The post will work, and the body be echoed back, **ONCE**
* Run the same command again. You will receive this error message:

```
This function is in live debug mode but did not get a response from your machine. If you do have an `sst dev` session running and this is the first time you have ever run SST in this AWS account, it can take 10 minutes for AWS to provision the underlying infrastructure. Check back shortly.
```

If you wait a while, it seems to work again - once.

The issue seems to appear only if a large body is posted to a HTTP lambda. Smaller bodies are always okay. Suspect it might be a queue ordering issue when receiving chunks in reply or something?
