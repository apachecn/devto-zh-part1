# Swift 中的 ETO REST API 企业、站点和计划选择

> 原文：<https://dev.to/ladvien/eto-rest-api-in-swift-enterprise-site-and-program-select-11b8>

1.  解开 SSOAuthToken。否则，处理错误。
2.  从 webservice 和 SSOAuthToken 创建请求。
3.  发出 GET 请求。
4.  异步-等待响应
5.  展开响应数据。
6.  如果 GET 请求返回 200-299，则请求成功。如果失败，处理。
7.  成功时，转换返回的 JSON 数据。如果失败，处理。
8.  从 JSON 数据中获取企业名称和 enterpriseGUUID 字符串。
9.  使用企业名称作为关键字，将 enterpriseGUUID 字符串添加到字典中
10.  带有参数“成功”的调用完成回调 meothd

```
public func getListOfEnterprisesAvailable(currentSessionInfo: SessionInfo, completion: @escaping (_ response: ReturnInfoWithDictionary) -> Void){

    let SSOAuthToken = currentSessionInfo.SSOAuthToken!

    var returnInfo = ReturnInfoWithDictionary()
    var enterpriseDictionary = Dictionary<String, String>()

    let GetSSOEnterprisesService = "https://services.etosoftware.com/API/Security.svc/GetSSOEnterprises/\(SSOAuthToken)"

    let request = Alamofire.request(GetSSOEnterprisesService, method: .get, parameters: nil, encoding: URLEncoding.default).validate().responseJSON { response in

        switch(response.result){
            case .success:
                if let jsonData = response.data {
                    let enterprisesList = JSON(data: jsonData)
                    for i in 0..<enterprisesList.count {
                        let key = enterprisesList[i]["Key"].string!
                        let value = enterprisesList[i]["Value"].string!
                        enterpriseDictionary.updateValue(key, forKey: value)
                    }
                    returnInfo.dictionary = enterpriseDictionary
                    returnInfo.callback = self.prepareResponse(targetResponse: .Success)
                    completion(returnInfo)
                } else {
                    returnInfo.callback = self.prepareResponse(targetResponse: .FailedToGetEnterpriseList)
                    completion(returnInfo)
                }
                break;
            case .failure:
                returnInfo.callback = self.prepareResponse(targetResponse: .HTTPRequestFail)
                completion(returnInfo)
                break;
        }
    }
    if(DebugMode){
        debugPrint(request)
    }
}

public func setEnterprise(currentSessionInfo: SessionInfo, selectedEnterprise: String){
    let etoUserDefaults = ETOUserDefaults()
    etoUserDefaults.setUserDefault(key: .enterpriseID, value: selectedEnterprise)
    currentSessionInfo.setEnterprise(chosenEnterprise: selectedEnterprise)
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  解开 SSOAuthToken。否则，处理错误。
2.  展开 selectedEnterpriseGuuid。否则，处理错误。
3.  从 web 服务 URL，SSOAuthToken，Enterprise GUUID 创建请求。
4.  发出 GET 请求。
5.  异步-等待响应
6.  展开响应数据。
7.  如果 GET 请求返回 200-299，则请求成功。如果失败，处理。
8.  成功时，转换返回的 JSON 数据。如果失败，处理。
9.  从 JSON 数据中获取站点名称和站点编号。
10.  使用站点名称作为关键字将站点编号添加到字典中
11.  带有参数“成功”的调用完成回调 meothd

```
public func getListOfSites(currentSessionInfo: SessionInfo, completion: @escaping (_ response: ReturnInfoWithDictionary) -> Void){

    var returnInfo = ReturnInfoWithDictionary()

    let SSOAuthToken = currentSessionInfo.SSOAuthToken ?? ""
    let selectedEnterpriseGuuid = currentSessionInfo.selectedEnterprise()

    if "" == SSOAuthToken {
        returnInfo.callback = prepareResponse(targetResponse: .NoSSOAuthToken)
        completion(returnInfo)
    } else if "" == selectedEnterpriseGuuid {
        returnInfo.callback = prepareResponse(targetResponse: .NoEnterpriseIDSelected)
        completion(returnInfo)
    } else {

        let GetSSOSitesService = "https://services.etosoftware.com/API/Security.svc/GetSSOSites/\(SSOAuthToken)/\(selectedEnterpriseGuuid)"

        let request = Alamofire.request(GetSSOSitesService, method: .get, parameters: nil, encoding: URLEncoding.default).validate().responseJSON { response in

            switch(response.result){
            case .success:
                if let jsonData = response.data {
                    let siteList = JSON(data: jsonData)
                    for i in 0..<siteList.count {
                        let keyInt = siteList[i]["Key"].int!
                        let key = String(keyInt)
                        let value = siteList[i]["Value"].string!
                        returnInfo.dictionary.updateValue(key, forKey: value)
                    }
                    returnInfo.callback = self.prepareResponse(targetResponse: .Success)
                    completion(returnInfo)
                } else {
                    returnInfo.callback = self.prepareResponse(targetResponse: .FailedToGetSiteList)
                    completion(returnInfo)
                }
                break;
            case .failure:
                returnInfo.callback = self.prepareResponse(targetResponse: .HTTPRequestFail)
                completion(returnInfo)
                break;
            }
        }
        if(DebugMode){
            debugPrint(request)
        }
    }       
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  解开 SSOAuthToken。否则，处理错误。
2.  展开 selectedEnterpriseGuuid。否则，处理错误。
3.  从 webservice、SSOAuthToken 和 Enterprise GUUID 创建请求。
4.  发出 GET 请求。
5.  异步-等待响应
6.  展开响应数据。
7.  如果 GET 请求返回 200-299，则请求成功。如果失败，处理。
8.  成功时，转换返回的 JSON 数据。如果失败，处理。
9.  从 JSON 数据中的标记分配站点 sessionSecurityToken。
10.  使用参数“成功”调用完成回调方法

```
public func setSelectedSite(currentSessionInfo: SessionInfo, completion: @escaping (_ response: ReturnInfoWithString)-> Void){

  let etoUserDefault = ETOUserDefaults()
  var returnInfo = ReturnInfoWithString()

  let SSOAuthToken = currentSessionInfo.SSOAuthToken ?? ""
  let selectedEnterpriseGuuid = currentSessionInfo.selectedEnterprise()
  let selectedSite = currentSessionInfo.selectedSite()

  etoUserDefault.setUserDefault(key: .siteID, value: selectedSite)

  var secondsFromGMT: Int { return NSTimeZone.local.secondsFromGMT() }
  let utcOffsetInHours = String(secondsFromGMT / 60)

  if "" == SSOAuthToken {
      returnInfo.callback = prepareResponse(targetResponse: .NoSSOAuthToken)
      completion(returnInfo)
  } else if "" == selectedEnterpriseGuuid {
      returnInfo.callback = prepareResponse(targetResponse: .NoEnterpriseIDSelected)
      completion(returnInfo)
  } else if "" == utcOffsetInHours {
      returnInfo.callback = prepareResponse(targetResponse: .InvalidUTC)
      completion(returnInfo)
  } else {
      let body = "https://services.etosoftware.com/API/Security.svc/SSOSiteLogin/\(selectedSite)/\(selectedEnterpriseGuuid)/\(SSOAuthToken)/"
          + utcOffsetInHours
      let request = Alamofire.request(body, method: .get, encoding: URLEncoding.default).validate().responseJSON { response in

          switch(response.result){
          case .success:
              if let jsonData = response.data {
                  let sessionSecurityToken = JSON(data: jsonData)
                  currentSessionInfo.sessionSecurityToken = sessionSecurityToken.string!
                  returnInfo.value = sessionSecurityToken.string!
                  returnInfo.callback = self.prepareResponse(targetResponse: .Success)
                  completion(returnInfo)
              } else {
                  returnInfo.callback = self.prepareResponse(targetResponse: .FailedToGetSiteList)
                  completion(returnInfo)
              }
              break;
          case .failure:
              returnInfo.callback = self.prepareResponse(targetResponse: .HTTPRequestFail)
              completion(returnInfo)
              break;
          }
      }
      if(DebugMode){
          debugPrint(request)
      }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  展开 selectedEnterpriseGuuid。否则，处理错误。
2.  从 webservice URL、sessionSecurityToken 和 EnterpriseGUUID 创建请求
3.  发出 GET 请求。
4.  异步-等待响应
5.  展开响应数据。
6.  如果 GET 请求返回 200-299，则请求成功。如果失败，处理。
7.  成功时，转换返回的 JSON 数据。如果失败，处理。
8.  从 JSON 数据中获取程序名和程序号。
9.  使用程序名作为关键字将程序号添加到字典中
10.  带有参数“成功”的调用完成回调 meothd

```
public func getListOfPrograms(currentSessionInfo: SessionInfo, completion: @escaping (_ response: ReturnInfoWithDictionary)  -> Void){

     var returnInfo = ReturnInfoWithDictionary()

     let headers = headersWithEnterpriseIDAndSecurityToken(currentSessionInfo: sessionInfo)

     let selectedSite = currentSessionInfo.selectedSite()

     if "" == currentSessionInfo.SSOAuthToken {
         returnInfo.callback = prepareResponse(targetResponse: .NoSSOAuthToken)
         completion(returnInfo)
     } else if "" == currentSessionInfo.selectedEnterprise() {
         returnInfo.callback = prepareResponse(targetResponse: .NoEnterpriseIDSelected)
         completion(returnInfo)
     } else if "" == selectedSite {
         returnInfo.callback = prepareResponse(targetResponse: .NoSiteIDSelected)
         completion(returnInfo)
     } else {
         let body = "https://services.etosoftware.com/API/Form.svc/Forms/Program/GetPrograms/" + selectedSite

         let request = Alamofire.request(body, method: .get, encoding: URLEncoding.default, headers: headers).validate().responseJSON { response in

             switch(response.result){
             case .success:
                 if let jsonData = response.data {
                     let programList = JSON(data: jsonData).arrayValue
                     for i in 0..<programList.count {
                         let programIDInt = programList[i]["ID"].int
                         let disabled = programList[i]["Disabled"].boolValue //TODO: Make an option.
                         if !disabled {
                             if let programIDInt = programIDInt {
                                 returnInfo.dictionary.updateValue(String(programIDInt), forKey: programList[i]["Name"].string!)
                             }
                         }
                     }
                     currentSessionInfo.programsDictionary = returnInfo.dictionary
                     returnInfo.callback = self.prepareResponse(targetResponse: .Success)
                     completion(returnInfo)
                 } else {
                     returnInfo.callback = self.prepareResponse(targetResponse: .FailedToGetSiteList)
                 }
                 break;
             case .failure:
                 returnInfo.callback = self.prepareResponse(targetResponse: .HTTPRequestFail)
                 completion(returnInfo)
                 break;
             }
         }
         if(DebugMode){
             debugPrint(request)
         }
     }
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  展开选定的程序。否则，处理错误。
2.  展开 selectedEnterpriseGuuid。否则，处理错误。
3.  从 webservice、selectedProgram、SSOAuthToken 和 Enterprise GUUID 创建请求。
4.  发出 GET 请求。
5.  异步-等待响应
6.  展开响应数据。
7.  如果 GET 请求返回 200-299，则请求成功。如果失败，处理。
8.  成功时，转换返回的 JSON 数据。如果失败，处理。

9.  使用参数“成功”调用完成回调方法

```
public func setSelectedProgram(currentSessionInfo: SessionInfo, setToProgramID: String,completion: @escaping (_ response: ReturnInfoWithJSON) -> Void){

  var etoUserDefaults = ETOUserDefaults()
  var returnInfo: ReturnInfoWithJSON = ReturnInfoWithJSON()

  etoUserDefaults.setUserDefault(key: .programID, value: setToProgramID)

  if "" == currentSessionInfo.SSOAuthToken {
      returnInfo.callback = prepareResponse(targetResponse: .NoSSOAuthToken)
      completion(returnInfo)
  } else if "" == currentSessionInfo.selectedEnterprise() {
      returnInfo.callback = prepareResponse(targetResponse: .NoEnterpriseIDSelected)
      completion(returnInfo)
  } else if "" == setToProgramID {
      returnInfo.callback = prepareResponse(targetResponse: .NoProgramIDSelected)
      completion(returnInfo)
  } else {
      let parameters: Parameters = [
          "ProgramID": setToProgramID
      ];

      let headers = headersWithEnterpriseIDAndSecurityToken(currentSessionInfo: currentSessionInfo)

      let body = "https://services.etosoftware.com/API/Security.svc/UpdateCurrentProgram/"

      let request = Alamofire.request(body, method: .post, parameters: parameters, encoding: JSONEncoding.default, headers: headers).validate().responseJSON { response in

          switch(response.result){
          case .success:
              if let responseData = response.data {
                  let jsonData = JSON(data: responseData)
                  currentSessionInfo.selectedProgramInfo = jsonData
                  currentSessionInfo.setProgram(chosenProgram: setToProgramID)
                  returnInfo.json = jsonData
                  returnInfo.callback = self.prepareResponse(targetResponse: .Success)
                  completion(returnInfo)
              } else {
                  returnInfo.callback = self.prepareResponse(targetResponse: .FailedToGetSiteList)
              }
              break;
          case .failure:
              returnInfo.callback = self.prepareResponse(targetResponse: .HTTPRequestFail)
              completion(returnInfo)
              break;
          }
      }
      if(DebugMode){
          debugPrint(request)
      }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public func headersWithEnterpriseIDAndSecurityToken(currentSessionInfo: SessionInfo) -> HTTPHeaders{
    let selectedEnterpriseGuuid = currentSessionInfo.selectedEnterprise()
    let sessionSecurityToken = currentSessionInfo.sessionSecurityToken

    let headers: HTTPHeaders = [
        "Content-Type": "application/json",
        "Accept": "application/json",
        "enterpriseGuid": selectedEnterpriseGuuid,
        "securityToken": sessionSecurityToken,
        ]

    return headers
}

public func sortDictionary(dictionaryToSort: Dictionary<String, String>) -> Dictionary<String, String>{
    var dictionaryToReturn = Dictionary<String, String>()

    let sortedKeys = Array(dictionaryToSort.keys).sorted()

    for key in sortedKeys {
        if let valueForThisKey = dictionaryToSort[key] {
            dictionaryToReturn.updateValue(valueForThisKey, forKey: key)
        }
    }

    return dictionaryToReturn
}

private func prepareResponse(targetResponse: AuthenticationServiceResponseTypes) -> AuthenticationServiceResponse {
    var candidateResponse: AuthenticationServiceResponse = AuthenticationServiceResponse()
    candidateResponse.responseType = targetResponse
    candidateResponse.responseMessage = AuthenticationServiceResponseMessages[targetResponse]!
    return candidateResponse
}

public struct AuthenticationServiceResponse {
  var responseType: AuthenticationServiceResponseTypes
  var responseMessage: String

  init(){
      self.responseType = .UnknownFailure
      self.responseMessage = AuthenticationServiceResponseMessages[.UnknownFailure]!
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public struct ReturnInfoWithString {
    var value: String
    var callback: AuthenticationServiceResponse

    init(){
        self.value = ""
        self.callback = AuthenticationServiceResponse()
    }
}

public struct ReturnInfoWithDictionary {
    var dictionary: Dictionary<String, String> = Dictionary<String, String>()
    var callback: AuthenticationServiceResponse

    init(){
        self.dictionary = Dictionary<String, String>()
        self.callback = AuthenticationServiceResponse()
    }
}

public struct ReturnInfoWithJSON {
    var json: JSON?
    var callback: AuthenticationServiceResponse

    init(){
        self.json = JSON(["":""])
        self.callback = AuthenticationServiceResponse()
    }
}

public enum AuthenticationServiceResponseTypes {
    case
    Success,
    NoSSOAuthToken,
    HTTPRequestFail,
    FailedToGetEnterpriseList,
    FailedToGetSiteList,
    InvalidUTC,
    NoEnterpriseIDSelected,
    UnknownFailure,
    NoSiteIDSelected,
    NoProgramIDSelected,
    failedToConnectToServer

    init(){
        self = .UnknownFailure
    }
}

public let AuthenticationServiceResponseMessages: Dictionary<AuthenticationServiceResponseTypes, String> = [
    .Success : "Success",
    .NoSSOAuthToken : "Authorization denied.",
    .HTTPRequestFail : "Failed to get information from server.",
    .FailedToGetEnterpriseList : "Failed to get enterprise list.",
    .FailedToGetSiteList : "Failed to get site list.",
    .InvalidUTC : "UTC Offset invalid.",
    .NoEnterpriseIDSelected : "No enterprise selected.",
    .UnknownFailure : "An unknown failure has occurred.",
    .NoSiteIDSelected: "No site selected.",
    .NoProgramIDSelected: "No program selected.",
    .failedToConnectToServer : "Failled to connect to server."
] 
```

Enter fullscreen mode Exit fullscreen mode