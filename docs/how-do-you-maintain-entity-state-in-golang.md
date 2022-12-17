# 你如何在 Golang 中维护实体状态

> 原文：<https://dev.to/purplebooth/how-do-you-maintain-entity-state-in-golang>

这是在 Go 中创建一个不能被设置为无效状态的实体的最好方法吗？

这里有一个我可能如何实现它的例子。我不确定这是不是很习惯。

```
package  custom_subdomain  import  (  "errors"  "regexp"  )  type  domainReg  struct  {  domainName  string  // Other values ...  }  func  NewDomainRegistration(domainName  string)  (*domainReg,  error)  {  reg  :=  &domainReg{}  if  err  :=  reg.SetDomainName(domainName);  err  !=  nil  {  return  nil,  err  }  return  reg,  nil  }  func  (r  *domainReg)  GetDomainName()  string  {  return  r.domainName  }  func  (r  *domainReg)  SetDomainName(domain  string)  error  {  matches,  err  :=  regexp.Match("\\.example.com$",  []byte(domain))  if  err  !=  nil  {  return  err  }  if  !matches  {  return  errors.New("all domains must be sub-domains to .example.com")  }  r.domainName  =  domain  return  nil  }  type  DomainRegistration  interface  {  GetDomainName()  string  SetDomainName(domain  string)  error  } 
```

```
package  main  import  (  "fmt"  "github.com/purplebooth/entity-state-example"  "log"  )  func  main()  {  reg,  err  :=  custom_subdomain.NewDomainRegistration("test.example.com")  if  err  !=  nil  {  log.Panic(err)  }  fmt.Println(reg.GetDomainName())  } 
```