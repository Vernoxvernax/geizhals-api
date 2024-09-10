# Geizhals/Skinflint - API Documentation

This is a WIP documentation for the API powering the popular price tracker [Geizhals/Skinflint](https://en.wikipedia.org/wiki/Geizhals).

For information on how to create the bearer token, which is required for almost every request, see [bearer_token.md](bearer_token.md)

___


**Feel free to create issues and or pull requests.**

___

### **How:**

Since the web version of geizhals only provides static text, it was necessary to reverse-engineer the smartphone app.

Because of the use of SSL-Pinning, [mitmproxy](https://mitmproxy.org/) alone, isn't enough to monitor the network traffic.
If you want to help me document the rest of the endpoints or potential future changes in the API, I recommend the following tools (android):
* [mitmproxy](https://mitmproxy.org/) to monitor the network requests
* [iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool) to unpack the apk
* [frida](https://github.com/frida/frida) for ssl-unpinning code-injection
  * [frida-interception-and-unpinning](https://github.com/httptoolkit/frida-interception-and-unpinning). This is one of the very few unpinning scripts that actually worked. [Httptoolkit](https://github.com/httptoolkit/httptoolkit) also works if your phone is rooted and you don't have a problem with light mode GUIs (not-recommended).

#### **Android phones without root**:

+ Follow [this](https://koz.io/using-frida-on-android-without-root/) guide to patch the geizhals apk.
  + Hint: `extractedFolder/smali/at/geizhals/android/MainActivity.smali`
+ I also recommend to skip step 7. and instead use [patrickfav/uber-apk-signer](https://github.com/patrickfav/uber-apk-signer) to sign the apk.
+ Then just follow the instructions for `frida-interception-and-unpinning`.

___

See [**Redocly/openapi-starter**](https://github.com/Redocly/openapi-starter) for more information about redocly.

