# Ktorfit：Kotlin 基于 ktor 的 retrofit：多平台 HTTP 客户端库

Ktorfit 是一个针对 Kotlin 多平台（Android、iOS、Js、Jvm、Linux）的 HTTP 客户端和 Kotlin 符号处理器，使用了 [KSP](https://github.com/google/ksp) 和 [Ktor 客户端](https://ktor.io/docs/getting-started-ktor-client.html)，受 [Retrofit](https://square.github.io/retrofit/) 启发而开发。

## 项目概述

Ktorfit 项目旨在提供一个适用于 Kotlin 多平台的 HTTP 客户端库，其设计灵感源自 Retrofit。该项目使用了 KSP 和 Ktor 客户端，提供了针对不同平台的支持，旨在简化网络请求的处理和管理。

## 项目支持

Ktorfit 支持多平台，包括 Android、iOS、Js、Jvm 和 Linux。该项目的多平台支持使得开发者可以更加便捷地处理网络请求，同时保持代码的一致性和可维护性。

## 如何使用

Ktorfit 提供了详细的[文档](https://foso.github.io/Ktorfit/)，其中包括了项目的使用方法和示例。

## 安装 [官方文档](https://foso.github.io/Ktorfit/installation/#setup)

（点击查看 [示例 ](https://github.com/Foso/Ktorfit/tree/master/example)）

## Android 项目:

#### Gradle 插件 [¶](https://foso.github.io/Ktorfit/installation/#gradle-plugins)

添加 KSP 和 [Ktorfit Gradle 插件](https://plugins.gradle.org/plugin/de.jensklingenberg.ktorfit)

```kotlin
plugins {
    // ...
    id("com.google.devtools.ksp") version "1.9.22-1.0.16" // ksp
    id("org.jetbrains.kotlin.plugin.serialization") version "1.9.22"  // ks序列化库，用于序列化Json
    id("de.jensklingenberg.ktorfit") version "1.12.0" // ktorfit
}
```

接下来，将 Ktorfit KSP 插件添加到依赖。

```kotlin
val ktorfit = "1.12.0"
val ktor = "2.3.6"
val compose_ui_version = "1.5.1"
dependencies {
    // ...
    ksp("de.jensklingenberg.ktorfit:ktorfit-ksp:$ktorfit") // ktorfit ksp 依赖
    implementation("de.jensklingenberg.ktorfit:ktorfit-lib:$ktorfit") // ktorfit 依赖
    
    // 仅当使用 Kotlin 序列化时才需要
    implementation("io.ktor:ktor-client-serialization:$ktor") // ktor 依赖
    implementation("io.ktor:ktor-client-content-negotiation:$ktor") // ktor 依赖
    implementation("io.ktor:ktor-serialization-kotlinx-json:$ktor") // ks 依赖
}
```

## kotlin 多平台项目：

#### Gradle 插件 [¶](https://foso.github.io/Ktorfit/installation/#gradle-plugins)

添加 KSP 和 [Ktorfit Gradle 插件](https://plugins.gradle.org/plugin/de.jensklingenberg.ktorfit)

```kotlin
plugins {
    // ...
    id("com.google.devtools.ksp") version "1.9.22-1.0.16"
    id("kotlinx-serialization")
    id("de.jensklingenberg.ktorfit") version "1.12.0"
}
```

接下来，将 Ktorfit KSP 插件添加到依赖。

```kotlin
val ktorVersion = "2.3.6"
val ktorfitVersion = "1.12.0"

kotlin {
    // ...
    sourceSets {
        // ...
        val commonMain by getting {
            dependencies {
                implementation("de.jensklingenberg.ktorfit:ktorfit-lib:$ktorfitVersion")
                //implementation("de.jensklingenberg.ktorfit:ktorfit-lib-light:$ktorfitVersion")
                implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.3")

                // 仅当使用 Kotlin 序列化时才需要
                implementation("io.ktor:ktor-client-serialization:$ktorVersion")
                implementation("io.ktor:ktor-client-content-negotiation:$ktorVersion")
                implementation("io.ktor:ktor-serialization-kotlinx-json:$ktorVersion")
             }
        }
    }
}

dependencies {
    with("de.jensklingenberg.ktorfit:ktorfit-ksp:$ktorfitVersion") {
        add("kspCommonMainMetadata", this)
        add("kspJvm", this)
        add("kspJvmTest", this)
        add("kspAndroid", this)
        add("kspAndroidTest", this)
        add("kspIosX64", this)
        add("kspIosX64Test", this)
        add("kspIosArm64", this)
        add("kspIosArm64Test", this)
        add("kspIosSimulatorArm64", this)
        add("kspIosSimulatorArm64Test", this)
        add("kspMacosX64", this)
        add("kspMacosX64Test", this)
        add("kspJs", this)
        add("kspJsTest", this)
    }
}
```

## 使用方法

像 Retrofit 一样

```kotlin
package com.wilinz.mapdraw.data.api

import com.wilinz.mapdraw.data.model.OsmSearchResponse
import de.jensklingenberg.ktorfit.http.GET
import de.jensklingenberg.ktorfit.http.Query

interface OsmMapApi {
    @GET("https://nominatim.openstreetmap.org/search")
    suspend fun search(
        @Query("q") q: String,
        @Query("format") format: String = "json",
        @Query("addressdetails") isAddressDetails: Int = 1,
        @Query("limit") limit: Int = 5,
        @Query("polygon_kml") polygonKml: Int = 0
    ): OsmSearchResponse
}
```

```kotlin
package com.wilinz.mapdraw.data

import com.wilinz.mapdraw.data.api.OsmMapApi
import de.jensklingenberg.ktorfit.Ktorfit
import io.ktor.client.HttpClient
import io.ktor.client.plugins.contentnegotiation.ContentNegotiation
import io.ktor.serialization.kotlinx.json.json
import kotlinx.serialization.json.Json

object Network {
    val myClient = HttpClient {
        install(ContentNegotiation) {
            json(Json { isLenient = true; ignoreUnknownKeys = true })
        }
    }
    val ktorfit = Ktorfit.Builder().httpClient(myClient).build()

    val osmMapApi = ktorfit.create<OsmMapApi>()
}
```

```kotlin
val result = osmMapApi.search(q = "桂林")
```

#### Ktor [¶](https://foso.github.io/Ktorfit/installation/#ktor)

Ktorfit 基于 Ktor Client 。 不需要为默认客户端添加额外的依赖项。 仅当你想使用 Ktor 插件进行序列化之类的事情时，需要添加依赖项，并且它们需要兼容对应版本

还可以使用 **“de.jensklingenberg.ktorfit:ktorfit-lib-light”，** 这只会添加 Ktor 客户端核心依赖项，而不是客户端的平台依赖项。 这将使您能够更好地控制所使用的客户端，但您必须自己添加它们。 [https://ktor.io/docs/http-client-engines.html ](https://ktor.io/docs/http-client-engines.html)其他一切与“ktorfit-lib”相同

## 兼容性

Ktorfit 的兼容性信息可在[此处](https://foso.github.io/Ktorfit/#compatibility)查看。

## 项目结构

* <kbd>compiler plugin</kbd> - 编译器插件的源代码模块
* <kbd>ktorfit-annotations</kbd> - Ktorfit 的注解模块
* <kbd>ktorfit-ksp</kbd> - KSP 插件的源代码模块
* <kbd>ktorfit-lib-core</kbd> - Ktorfit 核心库的源代码模块
* <kbd>ktorfit-lib</kbd> - ktorfit-lib-core + 特定平台客户端的依赖
* <kbd>sandbox</kbd> - 用于尝试各种功能的实验性测试模块
* <kbd>example</kbd> - 包含使用 Ktorfit 的示例项目
* <kbd>docs</kbd> - 包含 GitHub 页面的源代码

