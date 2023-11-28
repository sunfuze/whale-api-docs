---
sidebar_position: 1
slug: /getting-started
title: 快速开始
id: getting-started
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## 前言

[Whale API SDK](https://github.com/longportapp/openapi-sdk) 基于 Rust 底层提供标准实现，目前我们已经发布了 Python、Node.js、Rust、C++、[Golang](https://github.com/longportapp/openapi-go) 的 SDK，其他语言的支持后面会陆续推出。

## 环境需求

<Tabs groupId="programming-language">
  <TabItem value="python" label="Python" default>
    <li><a href="https://www.python.org/">Python 3</a></li>
    <li>Pip</li>
  </TabItem>
  <TabItem value="javascript" label="NodeJS">
    <li><a href="https://nodejs.org/">Node.js</a></li>
    <li>Yarn</li>
  </TabItem>
  <TabItem value="rust" label="Rust">
    <li><a href="https://www.rust-lang.org/">Rust</a></li>
  </TabItem>
  <TabItem value="java" label="Java">
    <li><a href="https://openjdk.org/">JDK</a></li>
    <li><a href="https://maven.apache.org/">Maven</a></li>
  </TabItem>
    <TabItem value="golang" label="Go">
    <li><a href="https://go.dev/">Golang</a></li>
  </TabItem>
</Tabs>

## 安装 SDK

<Tabs groupId="programming-language">
  <TabItem value="python" label="Python" default>

```bash
pip3 install longport
```

  </TabItem>
  <TabItem value="javascript" label="NodeJS">

```bash
yarn install longport
```

  </TabItem>
  <TabItem value="rust" label="Rust">

```toml
[dependencies]
longport = "*"
tokio = { version = "1", features = "rt-multi-thread" }
```

  </TabItem>
  <TabItem value="java" label="Java">

```xml
<dependencies>
    <dependency>
        <groupId>io.github.longportapp</groupId>
        <artifactId>openapi-sdk</artifactId>
        <version>LATEST</version>
    </dependency>
</dependencies>
```

  </TabItem>
  <TabItem value="golang" label="Go">

```shell
go get github.com/longportapp/openapi-go
```

  </TabItem>

</Tabs>

下面我们以获取资产为例，演示一下如何使用 SDK。

## 获取 Access Token

请联系商务来获取 Whale API 的测试 Access Token

配置 Access Token 和请求 Endpoint 有两种方式：

- 代码里面构造配置对象
- 环境变量

### 构造配置对象

SDK 提供了配置的构造方法：

<Tabs groupId="programming-language">
  <TabItem value="python" label="Python" default>

```python
from longport.openapi import TradeContext, Config, HttpClient

# get config fields from config file or env or some secret management system
key = "xxx"
secret = "xxx"
token = "xxx"
http_url = "https://openapi.longport.xyz"
trade_url = "wss://openapi-trade.longport.xyz"

conf = Config(app_key = key, app_secret = secret, access_token = token, http_url = http_url, trade_ws_url = trade_url)
http_cli = HttpClient(app_key = conf.app_key, app_secret = conf.app_secret, http_url = conf.http_url)
ctx = TradeContext(conf)
```

  </TabItem>
  <TabItem value="javascript" label="NodeJS" default>

```javascript
import { Config, HttpClient, TradeContext } from 'longport'

const conf = new Config({
    appKey: 'xxx',
    appSecret: 'xxx',
    accessToken: 'xxx',
    httpUrl: 'https://openapi.longport.xyz',
    tradeWsUrl: 'wss://openapi-trade.longport.xyz'
})

const httpClient = new HttpClient(conf.httpUrl, conf.appKey, conf.appSecret, conf.accessToken)

try {
    const ctx = TradeContext.new(conf)
} catch (e) {
    ...
}
```

</TabItem>
  <TabItem value="rust" label="Rust" default>

```rust
use std::sync::Arc;
use serde_json::json;

use longport::trade::{TradeContext};
use longport::{httpclient::HttpClient, Config};

# [tokio::main]

async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let conf = Config::new("appKey", "appSecret", "accessToken")
        .http_url("https://openapi.longport.xyz")
        .trade_ws_url("wss://openapi-trade.longport-xyz");
    let http_cli = conf.create_http_client();

    let (ctx, receiver) = TradeContext::try_new(Arc::new(conf)).await?;
    // do sth
    Ok(());
}

```

  </TabItem>
  <TabItem value="java" label="Java" default>

```java
import com.longport.*;
import com.longport.trade.*;

class Main {
    public static void main(String[] args) throws Exception {
        try (
            Config config = new ConfigBuilder("appkey", "appsecret", "accessToken")
                .HttpUrl("https://openapi.longport.xyz")
                .tradeWebsocketUrl("wss://openapi-trade.longport.xyz")
                .build();
            TradeContext ctx = TradeContext.create(config).get(); 
            HttpClient cli = new HttpClient("https://openapi.longport.xyz", "appkey", "appsecret", "accesstoken");
        ) {
            ctx.subscribe(new TopicType[] { TopicType.Private});
            ctx.setOnOrderChange((PushOrderChanged orderEvent) -> {
                logger.info("receive order change event: {}", orderEvent);
            });
            // do sth
        }
    }
}
```

  </TabItem>
  <TabItem value="golang" label="Go" default>

```go
package main

import (
    "context"
    "log"
    "time"
    "encoding/json"

    "github.com/longportapp/openapi-go/config"
    "github.com/longportapp/openapi-go/http"
    "github.com/longportapp/openapi-go/trade"
)


func main() {
    
    conf := Config{
        HttpURL: "https://openapi.longport.xyz",
        AppKey: "appkey",
        AppSecret: "appsecret",
        AccessToken: "accesstoken",
        TradeUrl: "wss://openapi-trade.longport.xyz",
    }

    tctx, err := trade.NewFromCfg(conf)

    if err != nil {
        log.Fatal(err)
    }

    httpcli, err := http.NewFromCfg(conf)
    if err != nil {
        log.Fatal(err)
    }
}

```

  </TabItem>

</Tabs>

### 环境变量

环境变量说明

| 环境变量| 说明 | 例子 |
|---------------- | --------------- | :--------------- |
| LONGPORT_APP_KEY    | access key   | access_key |
| LONGPORT_APP_SECRET    | access secret key    |  access_secret_key |
| LONGPORT_ACCESS_TOKEN | access token | tn_xxx |
| LONGPORT_HTTP_URL | API Endpoint | <https://openapi.longport.xyz> |
| LONGPORT_TRADE_WS_URL | Websocket Endpoint | wss://openapi-trade.longport.xyz |

#### macOS / Linux 环境下设置环境变量

打开终端，输入下面的命令即可：

```bash
export LONGPORT_APP_KEY="获取到的 Access Key"
export LONGPORT_APP_SECRET="获取到 的 Access Secret Key"
export LONGPORT_ACCESS_TOKEN="获取到的 Access Token"
export LONGPORT_HTTP_URL="https://openapi.longport.xyz" # 测试 Whale API
export LONGPORT_TRADE_WS_URL="wss://openapi-trade.longport.xyz" # 测试 Whale API Websocket
```

#### Windows 下设置环境变量

Windows 要稍微复杂一些，按下 `Win + R` 快捷键，输入 `cmd` 命令启动命令行（建议使用 [Windows Terminal](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701) 获得更好的开发体验）。

在命令行里面输入下面的命令设置环境变量：

```bash
C:\Users\jason> setx LONGPORT_APP_KEY "获取到的 App Key"
成功：指定的值已得到保存。

C:\Users\jason> setx LONGPORT_APP_SECRET "获取到的 App Secret"
成功：指定的值已得到保存。

C:\Users\jason> setx LONGPORT_ACCESS_TOKEN "获取到的 Access Token"
成功：指定的值已得到保存。

C:\Users\jason> setx LONGPORT_HTTP_URL "https://openapi.longport.xyz"
成功：指定的值已得到保存。

C:\Users\jason> setx LONGPORT_TRADE_WS_URL "https://openapi-trade.longport.xyz"
成功：指定的值已得到保存。

```

:::caution

Windows 环境变量限制，当上面命令都执行成功以后，你需要重新启动 Windows 或者注销后重新登录一次，才可以读取到。

:::

注销或重新启动后，再次打开命令行，输入下面的命令验证一下环境变量是否设置正确：

```bash
C:\Users\jason> set LONGBRIDGE
LONGPORT_APP_KEY=xxxxxxx
LONGPORT_APP_SECRET=xxxxxx
LONGPORT_ACCESS_TOKEN=xxxxxxx
LONGPORT_HTTP_URL=https://openapi.longport.xyz
LONGPORT_TRADE_WS_URL=https://openapi-trade.longport.xyz
```

如果能正确打印你刚才设置的值，那么环境变量就是对了。

:::tip
建议您设置好 `LONGPORT_APP_KEY`, `LONGPORT_APP_SECRET`, `LONGPORT_ACCESS_TOKEN`, `LONGPORT_HTTP_URL`, `LONGPORT_TRADE_WS_URL` 这几个环境变量。我们为了演示方便，后面各章节文档中的示例代码都会使用这几个环境变量。

如您在 Windows 环境不方便使用环境变量，可根据个人需要，修改代码。
:::

## 调用例子

### 下单

我们下单通过 HTTP Client 调用 HTTP API，通过通过 Websocket 订阅订单的状态变更。

<Tabs groupId="programming-language">
  <TabItem value="python" label="Python" default>

创建 `whale.py` 贴入下面的代码：

```python
from longport.openapi import TradeContext, Config, HttpClient, PushOrderChanged

config = Config.from_env()
httpcli = HttpClient.from_env()
ctx = TradeContext(config)

ctx.subscribe(["private"])


def on_order_changed(event: PushOrderChanged):
    print()

ctx.set_on_order_changed(on_order_changed)

resp = httpcli.request("POST", "/v1/whaleapi/trade/order", {
    "symbol": "TSLA",
    "side": "Buy",
    "order_type": "MO",
    "submitted_quantity": "20",
    "time_in_force": "Day",
    "account_no": "L6VQEU00121996"
})

```

运行

```bash
python whale.py
```

  </TabItem>
  <TabItem value="javascript" label="NodeJS">

创建 `whale.js` 贴入下面的代码：

```js
import { Config, TradeContext, HttpClient, TopicType } from 'longport'

const config = Config.fromEnv()
const httpcli = HttpClient.fromEnv()

try {
  const ctx = await TradeContext.new(config)
  ctx.subscribe([TopicType.Private])
  ctx.setOnOrderChanged((event) => {
    // handle order changed event
    console.dir(event)
  })
    
  const resp = await httpcli.request("POST", "/v1/whaleapi/trade/order", {
    "symbol": "TSLA",
    "side": "Buy",
    "order_type": "MO",
    "submitted_quantity": "20",
    "time_in_force": "Day",
    "account_no": "L6VQEU00121996"
  })

} catch(e) {
  // handle error
}

```

运行

```bash
node whale.js
```

  </TabItem>
  <TabItem value="rust" label="Rust">

创建 `main.rs` 贴入下面的代码：

```rust
use std::sync::Arc;
use serde_json::json;

use longport::trade::{TradeContext, TopicType};
use longport::{httpclient::HttpClient, Config};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let config = Arc::new(Config::from_env()?);
    let httpcli = HttpClient::from_env()?;
    let (ctx, receiver) = TradeContext::try_new(config).await?;
    ctx.subscribe([TopicType.Private]).await?;

    let resp = httpcli.request("POST".parse()?, "/v1/whaleapi/trade/order")
        .body(json!{
            "symbol": "TSLA",
            "side": "Buy",
            "order_type": "MO",
            "submitted_quantity": "20",
            "time_in_force": "Day",
            "account_no": "L6VQEU00121996"
        })
        .response::<String>()
        .send()
        .await?;
    println!("{}", resp);

    while let Some(event) = receiver.recv().await {
        println!("{:?}", event);
    }

    Ok(());
}
```

运行

```bash
cargo run
```

  </TabItem>
  <TabItem value="java" label="Java">

创建 `Main.java` 贴入下面的代码：

```java
import com.longport.*;
import com.longport.trade.*;

class Main {
    public static void main(String[] args) throws Exception {
        try (
            Config config = Config.fromEnv(); 
            TradeContext ctx = TradeContext.create(config).get(); 
            HttpClient cli = HttpClient.fromEnv()
        ) {
            ctx.subscribe(new TopicType[] { TopicType.Private});
            ctx.setOnOrderChange((PushOrderChanged orderEvent) -> {
                logger.info("receive order change event: {}", orderEvent);
            });

            HashMap<String, Object> req = new HashMap<String, Object>();
            req.put("symbol", "TSLA.US");
            req.put("order_type", "MO"); // Market Order
            req.put("side", "Buy");
            req.put("submitted_quantity", "20");
            req.put("time_in_force", "Day");
            req.put("account_no", "L6VQEU00121996"); // Test Account Number

            HashMap<String, Object> submitRes = cli.request(HashMap.class, "POST", "/v1/whaleapi/trade/order", req).get();
        }
    }
}
```

运行

```bash
mvn compile exec:exec
```

  </TabItem>
  <TabItem value="golang" label="Go">

创建 whale 目录，执行

```shell
cd whale
go mod init whale
go get github.com/longportapp/openapi-go
```

创建 `main.go`，贴入一下内容：

```go
package main

import (
    "context"
    "log"
    "time"
    "encoding/json"

    "github.com/longportapp/openapi-go/config"
    "github.com/longportapp/openapi-go/http"
    "github.com/longportapp/openapi-go/trade"
)


func main() {
    conf, err := config.NewFromEnv()

    if err != nil {
        log.Fatal(err)
    }

    tctx, err := trade.NewFromCfg(cfg)

    if err != nil {
        log.Fatal(err)
    }

    httpcli, err := http.NewFromCfg(cfg)
    if err != nil {
        log.Fatal(err)
    }

    if _, err = tctx.Subscribe(context.Background(), []string{"private"}); err != nil {
        log.Fatal(err)
    }

    tctx.OnTrade(func(ev *trade.PushEvent) {
        log.Printf("got event: %+v\n", ev.Data)
    })

    var resp json.RawMessage
    if err := httpcli.Call(context.Background(), "POST", "/v1/whaleapi/trade/order", nil, map[string]interface{
        "symbol": "TSLA",
        "side": "Buy",
        "order_type": "MO",
        "submitted_quantity": "20",
        "time_in_force": "Day",
        "account_no": "L6VQEU00121996",
    }, &resp); err != nil {
        log.Fatal(err)
    }

    time.Sleep(time.Second * 5)
}
```

运行

```shell
go run ./

```

  </TabItem>

</Tabs>

上面例子已经完整演示了如何使用 SDK 访问 Whale API 的接口。

## SDK API 文档

SDK 的详细 API 文档请访问：

<https://longportapp.github.io/openapi-sdk/>

## 反馈及沟通
