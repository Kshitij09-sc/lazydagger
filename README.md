# LazyDagger

> Kotlin Symbolic Processor for Dagger Lazy Delegates

<img src="art/lazydagger-stable-diffusion.jpeg" width="100%"></img>
<p align="center"><i>Generated by Stable Diffusion for "Sleeping face on a dagger"</i></p>


Dagger supports `Lazy` Provider wrapper to delay expensive object creation. When used wisely, it can greatly improve the
startup time of your app. However, this invites a lot of boilerplate similar to backing properties in Kotlin. For
instance,

```kotlin
// non-lazy implementation
class CommentViewModel(
    val analyticsManager: AnalyticsManager,
    val authRepository: AuthRepository,
    val deleteUseCase: DeleteUseCase
) {
    ...
}

// lazy setup
interface CommentViewModelLazyDelegate {
    val analyticsManager: AnalyticsManager
    val authRepository: AuthRepository
    val deleteUseCase: DeleteUseCase
}

class CommentViewModelLazyDelegateImpl @Inject constructor(
    val analyticsManagerLazy: Lazy<AnalyticsManager>,
    val authRepositoryLazy: Lazy<AuthRepository>,
    val deleteUseCaseLazy: Lazy<DeleteUseCase>
) : CommentViewModelLazyDelegate {
    val analyticsManager: AnalyticsManager by lazy { analyticsManagerLazy.get() }
    val authRepository: AuthRepository by lazy { authRepositoryLazy.get() }
    val deleteUseCase: DeleteUseCase by lazy { deleteUseCaseLazy.get() }
}

@HiltViewModel
class CommentViewModel @Inject constructor(
    lazyDelegate: CommentViewModelLazyDelegate
) : CommentViewModelLazyDelegate by lazyDelegate {
    ...
}
```

While we could have kept the backing properties in the original ViewModel itself, we can't get away with the amount of
boilerplate involved in this process (2 x No. of Lazy Injections). LazyDagger helps you get away from this situation by
generating this code for you.

# Usage

```kotlin
@LazyDagger(ActivityRetainedComponent::class)
interface CommentViewModelLazyDelegate {
    val analyticsManager: AnalyticsManager
    val authRepository: AuthRepository
    val deleteUseCase: DeleteUseCase
}
```

Simply add `@LazyDagger` annotation to your interface and the implementation will be generated alongside the Hilt
binding modules for specified component(s) or `SingletonComponent::class` if not specified. Check the `sample/` project
to learn more

# Download

[ ![Maven Central](https://badgen.net/maven/v/maven-central/com.kshitijpatil.lazydagger/lazydagger-core) ](https://search.maven.org/artifact/com.kshitijpatil.lazydagger/lazydagger-core)

Add KSP plugin, gradle dependency and include generated ksp directory to your sourceSets

```kotlin
plugins {
    id("com.google.devtools.ksp") version "<version>"
}

dependencies {
    implementation("com.kshitijpatil.lazydagger:lazydagger-core:$lazydaggerVersion")
    ksp("com.kshitijpatil.lazydagger:lazydagger-codegen:$lazydaggerVersion")
}

sourceSets.main {
    java.srcDirs("build/generated/ksp/main/kotlin")
}
```

License
---

    Copyright 2022 Kshitij Patil
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
    http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
