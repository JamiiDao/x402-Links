# x402-links
> Universal links for x402 resources

The x402-links is a specification for improving the user-experience of discovering and interacting with x402 resources in the real-world. It is inspired by `Solana pay` QR codes and implements a URI specification for opening x402 resources using links in HTML website and mobile app deeplinks.


x402 protocol defines a protocol for discovering and interacting with services that implement the HTTP 402 status code for payments. Beyond that the x402 protocol is agnostic and dosen't define a unified way of users interacting with the x402 resources.

## Some example context

Imagine a scenario where Alice (a Solana DeFi user) walks past a billboard and sees a new book `Conqueror of Blockhains; Taker Of Markets.` by her favourite author `Toly The Great`. The publisher's website supports x402 payments where Alice dosen't need to create an account, she can pay for the book using x402 microtransactions. So does she need to search online for the book, lookup which publishers sell the eBook version and create an account? Or is there a better way to do it?

Using the x402-links, the pubisher of the billboard ad can attach a x402-link as part of the billboard graphic next to the book. Alice can simply scan it and her phone will prompt her to open the x402-link with an app she has installed on her phone that supports the x402-links specification (similar to how Solana Pay links can be opened by an app installed on her phone that supports open Solana pay QR codes). The app the opens the QR code or deeplink and shows her the resource and a button to buy the book. When Alice clicks the button the app prepares a transaction with the correct token (example USDC) and sends a `signTransaction` request to her mobile wallet where she signs the transaction which the wallet returns to the x402-link app that requested the transaction to be signed. The app then sends the transaction to the website or app that will fulfill the purchase of the book.

This user experience ensures Alice can get access to the book in a few seconds (assuming a fast blockchain like Solana) by just viewing and tapping away. The app that supports x402-links does all the heavy lifting from fetching the information about the x402 resource, any graphics (like the book's cover), the price, the address she is paying to, preparing the transaction and returning the payment to the merchant's x402 server. Alice just had some funds in a wallet, no need to know what protocol was used to give her access to her book. Now she has something to do this weekend :)

Notice that the merchant didn't need to ship a mobile app for Alice to download. All the merchant did was display a QR Code on the billboard or a deeplink on their website and an installed app handled the rest. This is similar to how Solana mobile apps create a consistent user experience despite hundreds of services offering Solana payments. Simply pull, `one app for all experiences`.

## The x402-links specification

```sh
# Example of a x402-URI that discovers x402 resources at the URL
x402://discover/example.com/x402
```

### The x402:// scheme
An x402-link is identified by the scheme `x402://`. While just using the `x402:` as the scheme is sufficient, for maximum compatibility with URL parsers the `//` is added to `x402:` to reduce friction of adopting this URI protocol.

This URI is a deeplink that opens in an app that supports handling `x402-links`.

### The domain
The domain is the second part after the `x402://` scheme. It is used to decide how to handle a link. This can be useful in handling deeplinks using a navigation mechanism.  ***Note that all characters after the domain need to be percent encoded to avoid any conflicts with URI parsers.***

The domain is of two types:
1. `discover/`

    The `discover/` domain is used to discover x402 resources on a server. What follows the `discover/` domain is a URI used to fetch the resources as JSON as described in the x402 specification. The URI does not need to be in the route, query parameters can be defined by the server meaning that the URL is agnostic as long as it is a HTTPS url
    ```sh
    # example; note the URL after `pay/` domain ins percent encoded
    x402://discover/https%3A%2F%2Fexample.com%2Fx402%2Fdiscover
    ```

2. `pay/`

    This is used to perform a payment action on a x402 resource. In the case of Alice above, like buying the book, no subscriptions, no notifications, no signups. `once/` is followed by the x402 endpoint that will handle her request using the x402 headers defined in the x402 specification.
    ```sh
    # example; note the URL after `pay/` domain ins percent encoded
    x402://pay/https%3A%2F%2Fexample.com%2Fx402%2Fpublisher%3FToly-The-Great%26name%3DConqueror-of-Blockhains-Taker-Of-Markets%26version%3Dlatest-version
    ```

## Usage Examples

### HTML Usage
A HTML a tag is used to add the x402-URI link:
```html
<a href="x402://discover/https%3A%2F%2Fexample.com%2Fx402%2Fdiscover">Explore agents</a>
``` 

When clicked from a smartphone, most browsers like Chrome and Firefox will prompt the user to open in an app that parses `x402-URI`s if the user agrees then the mobile plaftorm open the URI in the app.

### Dapp store
The dapp store for mobile app is agnostic based on the platform. For example the Solana dapp store (Android apps).
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
       ...
        <activity
            ...>

            <intent-filter>
                <action android:name="android.intent.action.VIEW" />

                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />

                <data
                    android:host="discover"
                    android:scheme="x402" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

Here the `<intent-filter>` has the `<data android:host="discover" android:scheme="x402" />` entry which handles the `x402://discover/...` x402-URI. This tells android that the app can open `x402://` URIs.

Other mobile platforms handle this differently so check how `iOS`, `KaiOS`, `HarmonyOS`, etc, handle opening URIs.

### QR-CODES
QR-Codes can be used in the same way.
```
x402://<domain>/<uri>
```
Example QR:

![x402-URI QR](./x402-URI.png)

> Scan this QR and share the link to Firefox or Chrome. These browsers will prompt you to open the x402-URI with an installed app that supports handling x402-URIs.
>

## x402-URI Live Updates
Platforms like `Android`, `iOS` and `HarmonyOS` implement live updates to give live feedback of ongoing events via notifications.

URLs that support server-side events like agent-to-agent communication can simply define return JSON as part of the message data in the EventStream of an EventSource in server-sent events.

```json
{
    "contentTitle": "<String>",
    "contentText": "<String>",
    "shortCriticalText": "<String>",
    "progress": {
        "point": <Int (u32::MAX)>,
        "color": "<String>"
    },
    "isProgressIndeterminate": <Boolean>,
    "actions": ["<String>"],
    "style": {
        "points": [
            {
                "point": <Int (u32::MAX)>,
                "color": "<String>"
            }
        ],
        "segments":[
            {
                "segment": <Int (u32::MAX)>,
                "color": "<String>"
            }
        ]
    }
}

```


## Summary
The x402-URI improve the user experience for ordinary users opening up x402 payments anywhere on any website, app or QR-Code in the physical world. The services with the x402 resources no longer need to spend resources making users understand x402 payments. The user just taps on a button or scans a QR code.