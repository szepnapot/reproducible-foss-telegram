# reproducible-foss-telegram

Reproducible FOSS-Telegram build from source, signed with own key 

To add to rattlesnake config include the following:

```yaml
[[custom-prebuilts]]
  modules = ["telegram"]
  repo = "https://github.com/szepnapot/reproducible-foss-telegram"
```

# Build FOSS-Telegram from source

```bash
# Clone the Signal Android source repository
git clone --recursive https://github.com/Telegram-FOSS-Team/Telegram-FOSS.git
cd Telegram-FOSS

# Check out the release tag for the version you'd like to build
git checkout v[the version number]

# Build using a Docker build-box
docker run -v `pwd`:/project -it 60ph3r/android-build-box bash

# First build the native dependencies
cd project/TMessagesProj/jni
# make sure required environment variables in place
# they should NOT be empty
echo $NDK
echo $NINJA_PATH
./build_ffmpeg_clang.sh
./patch_boringssl.sh
./build_boringssl.sh

# You should get your own API key here: https://core.telegram.org/api/obtaining_api_id 
# and create a file called API_KEYS in the source root directory
# the contents should look like this:
# APP_ID = 12345
# APP_HASH = aaaaaaaabbbbbbccccccfffffff001122

# build the apk 
# the final apk can be found at 
# /project/TMessagesProj/build/outputs/apk/afat/release/
cd /project
./gradlew assembleAfatRelease
```

# Sign APK(s)

Download [uber-apk-signer](https://github.com/patrickfav/uber-apk-signer)

```bash
java -jar uber-apk-signer-1.0.0.jar --apks [path to apks] -o [out folder etc.] --ks [keystore] --ksAlias [alias] --ksKeyPass [pass] --ksPass [pass]
```
