# golang支付合集（包括微信支付，支付宝，后期会加入聚合支付）

* golang支付库
想必gopher们都会抱怨go成熟的包太少，特别是go语言支付这块，网上的代码基本没有能用的，要么不全，要么有硬伤，所以结合网上和自己经验，抽出时间写的一部分代码，封装下分享出来，希望能给大家一点借鉴意义。
* 支持的支付方式
目前支持微信app，支付宝网页版，支付宝app。要是谁有新的支付方式也可以合并。

* 项目开源协议：[GPL v3](https://opensource.org/licenses/GPL-3.0)    

* 使用方法

*  #####1. git clone https://github.com/shirdonliao/pays_with_go.git 或者 go get github.com/shirdonliao/pays_with_go

*  #####2. 在自己的项目里调用，示例代码如下，确保填入自己的配置参数，开发中有问题欢迎留言或者关注公众号:codebigdata

```javascript
package main

import (
    "fmt"
    "github.com/shirdonliao/pays_with_go"
	"github.com/shirdonliao/pays_with_go/client"
	"github.com/shirdonliao/pays_with_go/common"
	"github.com/shirdonliao/pays_with_go/constant"
	"net/http"
)
func main() {
	//设置支付宝账号信息
	initClient()
	//设置回调函数
	initHandle()

	//支付
	charge := new(common.Charge)
	charge.PayMethod = constant.WECHAT                              //支付方式
	charge.MoneyFee = 1                                   // 支付钱单位分
	charge.Describe = "测试订单"                                    //支付描述
	charge.TradeNum = "88888888"                                  //交易号
	charge.CallbackURL = "http://127.0.0.1/callback/aliappcallback" //回调地址必须跟下面一样

	fdata, err := gopay.Pay(charge)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(fdata)
}

//
func initClient() {
	client.InitAliAppClient(&client.AliAppClient{
		PartnerID:  "xxx",
		SellerID:   "xxxx",
		AppID:      "xxx",
		PrivateKey: nil,
		PublicKey:  nil,
	})
}


func initHandle() {
	http.HandleFunc("callback/aliappcallback", func(w http.ResponseWriter, r *http.Request) {
		//返回支付结果
		aliResult, err := gopay.AliAppCallback(w, r)
		if err != nil {
			fmt.Println(err)
			//log.xxx
			return
		}
		//接下来处理自己的逻辑
		fmt.Println(aliResult)
	})
}
```
