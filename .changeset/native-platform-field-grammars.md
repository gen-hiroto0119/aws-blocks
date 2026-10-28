---
'@aws-blocks/core': patch
---

Spec generation now rejects a malformed Swift or Android native package field, naming the block that declared it, as it already did for the Dart fields. A `swift.identity`, `swift.module`, `android.group`, `android.artifact`, or `android.kotlinPackage` outside its own package manager's grammar previously passed validation and was written into `x-blocks-native-packages`, where the Swift and Kotlin generators rejected it later without naming the declaring block. `swift.product` stays non-blank only, because SwiftPM admits any non-empty product name.
