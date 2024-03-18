# Self hosted Turborepo Remote Cache Lambda
Using Turborepo preflight requests combined with Lambda and S3 to generate presigned URLs which allows files larger than 6MB.

## Installation:
First create an private S3 Bucket then add some "Lifecycle rules" to delete old files after awhile to save storage.

```bash
git clone git@github.com:EloB/turborepo-remote-cache-lambda.git
cd turborepo-remote-cache-lambda/
npm --prefix=function install
sam build
zip -j app.zip .aws-sam/build/TurboRepoRemoteCacheFunction/*
```
Then create a Lambda function in AWS Console and upload this zip file. Give it permissions to the S3 bucket. Add a function url to it. Add `JWT_SECRET` and `S3_BUCKET` as environment variables.

## Usage:

### Create `.turbo/config.json` in your monorepo to enable remote caching.
```json
{
  "teamid": "team_yourteamhere",
  "apiurl": "https://YOUR-LAMBDA-URL.lambda-url.eu-central-1.on.aws"
}
```

### Generate a JWT token.
Simpliest way is going to https://jwt.io/ and create one. Enter you `JWT_SECRET` in the `your-256-bit-secret` field and set that payload from the image and define a name.
![image](https://github.com/EloB/turborepo-remote-cache-lambda/assets/476567/109f84c2-7dbd-4aed-a74b-c50279b6aced)

### Executing turbo tasks
Don't forget to set the environment `TURBO_TOKEN` when running a turbo task. Important also that you apply the `--preflight` to the commands when executing for instance `turbo build --preflight` else it won't work. There should be an dedicated environment variable for that but hasn't able to work (see `TURBO_PREFLIGHT` at https://turbo.build/repo/docs/reference/system-variables#system-environment-variables).

## Todo
- Simplify the deployment using `sam deploy` and parameter overrides. Accepting PRs for this!
