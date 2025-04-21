+++
title = "Maybe, set LayoutParams of view to be inflated in Compose UI's AndroidView"
date = "2025-02-07T23:51:03-08:00"
draft = false
author = "Gurpreet"
tags = ["android", "compose-ui"]
+++

I spent half of my day today figuring out why a `WebView` does not properly load menu on a web page properly while all the other functionality seems to be working as expected. The screen was written in Compose UI and employed `AndroidView` to compose an Android `WebView`.

Turns out, I missed that `LayoutParams` need to be set for the `WebView` provided via the factory block; just modifying `AndroidView` to fill max available size won’t work as expected.

Writing this so I do not waste more time in the future again, and so this can help any other poor soul pulling his/her hair, trying to figure out why their `WebView` isn’t working as expected.

```kotlin
    AndroidView(
        factory = { context ->
            WebView(context).apply {
                layoutParams = ViewGroup.LayoutParams(
                    ViewGroup.LayoutParams.MATCH_PARENT,
                    ViewGroup.LayoutParams.MATCH_PARENT
                )

                with(settings) {
                    // Your webview settings.
                }

                loadUrl(url)
            }
        },
        modifier = Modifier
            .fillMaxSize()
            .padding(padding)
    )
```
