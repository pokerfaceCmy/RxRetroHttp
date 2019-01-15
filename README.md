# RxRetroHttp
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
[![](https://jitpack.io/v/BakerJQ/RxRetroHttp.svg)](https://jitpack.io/#BakerJQ/RxRetroHttp)

Android http request lib,  supports multiple api result data structures and multiple urls

Http请求库，支持同时存在多种返回格式和多个base url（[中文文档](https://github.com/BakerJQ/RxRetroHttp/blob/master/README_cn.md)）
## Why RxRetroHttp
In some case, we have to include more than one http request style -- eg. multiple result structure, multiple host address, multiple http settings, etc-- in one app.

This usually happens when we need to use some third party api or combine multiple system in one project.

RxRetroHttp suppose to simplify your http request realization in this situation.

## Gradle via JitPack
Add it in your root build.gradle at the end of repositories:
``` groovy
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}
```
Add the dependency
``` groovy
dependencies {
    implementation 'com.github.BakerJQ:RxRetroHttp:0.1.0'
}

```

## How to use
### Initialize
#### Main Api Request
Initialize the sdk in Application, the code below shows the initialization of the main url request settings
```java
RxRetroHttp.getInstance()
           .setBaseUrl("https://host/main/")//your main url
           .setApiResultClass(MainApiResult.class)//your main api result structure, if not, will use default gson converter
           .init(this);
```
#### Other Api Request
If your app includes other api request, try the code below

Mention:
- You need to do this after you called init()
- DON'T forget to add the 'Tag' in addClient() function
- If you don't set an ApiResultClass, the lib will use GsonConverterFactory as default, this means that the lib will not deal with response logic for you, or you can add your own ResponseConverter
```java
RxRetroHttp.getInstance()
           .setApiResultClass(YourApiResult.class)//other result
           .setBaseUrl("http://host/api/data/")//other url
           .addClient(new SimpleRetroClient(), "YourTag");//other request tag
```

### Api Request
#### Step 1. Define Api Result Structure by Implement IApiResult
Code below is just a simple sample
```java
public class YourApiResult<T> implements IApiResult<T> {
    private int code;//result code
    private String msg;//result message
    private T result;//result data
    //define what means a successful result(eg. code == 1)
    @Override
    public boolean isSuccess();
    //return the structured data
    @Override
    public T getData();
    //return the message
    @Override
    public String getResultMsg();
    //return the code
    @Override
    public String getResultCode();
    //return the key name of "result" in the response json
    @Override
    public String getDataField();
}
```
#### Step 2. Define Retrofit Api Service
Define the ApiService, to be mentioned, you DO NOT need to use wrapped api result like "Observable<YourApiResult<TestInfo>>"
```java
public interface YourApiService {
    @GET("test/info")
    Observable<TestInfo> getTestInfo();
    @GET("test/list")
    Observable<List<TestInfo>> getTestInfo();
}
```
#### Step 3. Call Request
Just define a call like Retrofit, if your request is not the main url request, DON'T forget the 'Tag'
```java
RxRetroHttp.create(YourApiService.class, "YourTag").getTestInfo()
```
## Proguard
- Add Retrofit proguard
- Add OkHttp proguard
- Add Gson proguard
- Deal with your data entity

## Thanks
Thanks To （[RxEasyHttp](https://github.com/zhou-you/RxEasyHttp)）

## Mention
This is just a beta version, there's still a lot of work to be done.

The introduction for now is still a simple version.

## *License*
RxRetroHttp is released under the Apache 2.0 license.

```
Copyright 2019 BakerJ.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at following link.

     http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```