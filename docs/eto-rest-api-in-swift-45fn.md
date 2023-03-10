# Swift 中的 ETO REST API

> 原文：<https://dev.to/ladvien/eto-rest-api-in-swift-45fn>

## 登录 ETO

这个代码库已经变成了一个宠物项目。它使用 [Alamofire](https://github.com/Alamofire/Alamofire) 创建 GET 和 POST 请求来对抗[Outcomes REST API。](https://services.etosoftware.com/)这使得本地应用程序能够与我们的无家可归者管理信息系统( [HMIS](https://www.hudexchange.info/programs/hmis/) )数据库进行交互。我一直在编写代码来练习这门手艺。此外，在我们的[护理连续体](https://www.hudexchange.info/programs/coc/)中有一些操作可以通过创建本地应用程序来改进，而不是使用 web 应用程序。例如，街道外展团队经常没有他们的电脑，因为大部分人都没有电脑。但是很少有街头推广专家没有移动设备。这将使外联专家能够访问所有数据，无论物理环境如何。

本机应用程序的已确定优势:

*   在避难所自助登记
*   使用接触点响应代替服务点进行数据活动，如庇护所登记。
*   街头宣传
*   利用现有硬件(大多数员工都有智能手机)
*   用户友好的 ui
*   通过自动查询进行数据聚合
*   时间点计数移动设备调查
*   收集街道外展和定点活动的 GPS 坐标

这将是一个文档日志，因为这个项目很可能会比我的有机记忆所能管理的更大。

### 登录()

建立被授权从 ETO 数据库请求信息的会话有几个步骤。第一，用户拥有数据库的实际帐户

1.  获取用户名、密码和 web 服务 URL，并发出 POST 请求。
2.  异步-等待响应
3.  展开响应数据。
4.  如果 POST 请求返回 200-299，则请求成功。如果失败，处理。
5.  成功时，转换返回的 JSON 数据。如果失败，处理。
6.  从 JSON 数据中提取 SSOAuthToken。
7.  如果在 JSON 数据中找到它，则调用 GetListOfEntperises()传递 SSOAuthToken
8.  Async-wait 返回企业列表。
9.  带有参数“成功”的调用完成回调 meothd

```
//
//  ETOWebServiceLogin.swift
//  ETO-TouchPoint-Test
//
//  Created by Thomas Ladvien on 10/13/16.
//  Copyright © 2016 Honeysuckle Hardware. All rights reserved.
//

import Foundation
import Alamofire
import SwiftyJSON

public func login(currentSessionInfo: SessionInfo, username: String, password: String, completion: @escaping (_ response: ReturnInfoWithString) -> Void){

        // ETO Authentication Web Service.
        let SSOAuthenticateService = "https://services.etosoftware.com/API/Security.svc/SSOAuthenticate/"

        let parameters: Parameters = [
            "security": [
                "Email": username,
                "Password": password,
            ]
        ];

        var returnInfo = ReturnInfoWithString()

        // TODO: Check for HTTP response code.
        let request = Alamofire.request(SSOAuthenticateService, method: .post, parameters: parameters, encoding: JSONEncoding.default).validate().responseData { response in
            if let data = response.data {

                switch response.result {
                case .success:
                    let json = JSON(data: data)
                    returnInfo.value = json["SSOAuthenticateResult"]["SSOAuthToken"].stringValue
                    if ("00000000-0000-0000-0000-000000000000" != returnInfo.value) {
                        returnInfo.callback = self.prepareResponse(targetResponse: .Success)
                        currentSessionInfo.SSOAuthToken = returnInfo.value
                        completion(returnInfo)
                    } else {
                        returnInfo.callback = self.prepareResponse(targetResponse: .NoSSOAuthToken)
                        completion(returnInfo)
                    }
                case .failure:
                    returnInfo.callback = self.prepareResponse(targetResponse: .HTTPRequestFail)
                    completion(returnInfo)
                }

            } else {
                returnInfo.callback = self.prepareResponse(targetResponse: .UnknownFailure)
                completion(returnInfo)
            }
        }
        if(DebugMode){
            debugPrint(request)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

好，让我来分解这个函数的步骤。

1.  获取用户名、密码和 web 服务 URL，并发出 POST 请求。
2.  异步-等待响应
3.  展开响应数据。
4.  如果 POST 请求返回 200-299，则请求成功。如果失败，处理。
5.  成功时，转换返回的 JSON 数据。如果失败，处理。
6.  从 JSON 数据中提取 SSOAuthToken。
7.  如果在 JSON 数据中找到它，则调用 GetListOfEntperises()传递 SSOAuthToken
8.  Async-wait 返回企业列表。
9.  带有参数“成功”的调用完成回调 meothd