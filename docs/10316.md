# 重新颁发傀儡主机 SSL 证书颁发机构根证书

> 原文：<https://dev.to/natejohnston/reissuing-the-puppet-master-ssl-certificate-authority-root-certificate>

# 补办傀儡主 SSL 证书颁发机构根证书

### 简介

本文档是为需要配置傀儡主 SSL 证书颁发机构设施(“CA”)的傀儡管理员提供的过程，以便使用新的根 SSL 证书来签名和验证代理证书，同时仍然保持与已签名证书的兼容性。

### 接近

证书签名关系基于来自 CA 私钥的签名(加密模数)；在生成新的公共证书的同时保持相同的私钥(并且隐含地保持相同的公钥),并根据需要更改新的有效期和任何其他新的属性，从而保持信任关系不变。CRL 也可以从旧证书延续到新证书，因为它们像证书一样由私钥签名。

### 文件的位置

请注意，以下是我的环境中傀儡主人上重要文件的真实位置。你的环境可能会不同。

| 文件 | 描述 |
| --- | --- |
| /opt/puppet/ssl/ca/ca_crt.pem | CA 公钥 |
| /opt/puppet/ssl/ca/ca_key.pem | CA 私钥 |
| /opt/puppet/SSL/ca/private/ca . pass | CA 私钥密码短语 |

### 如何在沙盒中进行测试

**初始设置**

步骤 1:创建 openssl.conf 文件，该文件将指定主题别名等。

*注意*:这里有一个`req_distinguished_name`部分，但是你会发现你可能需要重新输入其中的条目。这是一种痛苦。

```
cat > openssl.conf << LIMIT
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
ca_extensions = v3_ca

[req_distinguished_name]
countryName = US
stateOrProvinceName = VA
localityName = Here
organizationalUnitName  = Puppet Ops
commonName = CN=Puppet CA: puppet

[ v3_req ]
# Extensions to add to a certificate request
basicConstraints = critical,CA:TRUE
nsComment = "Puppet Ruby/OpenSSL Internal Certificate"
keyUsage = critical, cRLSign, keyCertSign
subjectAltName = @alt_names

[ v3_ca ]
# Extensions to add to a certificate request
authorityKeyIdentifier= keyid:always,issuer:always
basicConstraints = critical,CA:TRUE
keyUsage = critical, cRLSign, keyCertSign
nsComment = "Puppet Ruby/OpenSSL Internal Certificate"
subjectAltName = @alt_names
subjectKeyIdentifier= hash

[alt_names]
DNS.1 = puppet.company.net
DNS.2 = puppet-test.company.net
DNS.3 = puppet-integration.company.net

LIMIT 
```

第二步:制作一个名为“origroot”的根证书。

```
# openssl req -new -x509 -keyout root.key -out origroot.pem -days 3650 -nodes
Generating a 512 bit RSA private key
...++++++++++++
.++++++++++++
writing new private key to 'root.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
US []:US
VA []:VA
Reston []:Reston
IDEA Automation []:IDEA Automation
CN=Puppet CA: autopuppet []:CN=Puppet CA: autopuppet
NETO-AutomationTeam@cable.comcast.com []: 
```

步骤 3:从“origroot”证书生成子证书。

```
# openssl genrsa -out cert.key 1024
Generating RSA private key, 1024 bit long modulus
....++++++
.......................++++++
e is 65537 (0x10001)

# openssl req -new -key cert.key -out cert.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
US []:US
VA []:VA
Reston []:Here
IDEA Automation []:Puppet Ops
CN=Puppet CA: puppet []:CN=Puppet CA: puppet
PuppetOps@company.net []: 
```

第四步:签署子证书。

```
# openssl x509 -req -in cert.csr -CA origroot.pem -CAkey root.key -set_serial 01 -out cert.pem
Signature ok
subject=/C=US/ST=VA/L=Here/OU=Puppet Ops/CN=CN=Puppet CA: puppet
Getting CA Private Key

# rm -f cert.csr 
```

步骤 5:验证子证书和“origroot”证书之间的信任关系。

```
# openssl verify -CAfile origroot.pem -verbose cert.pem
cert.pem: OK 
```

**重新发行**

第六步:现在假设 10 年过去了，它接近到期日，或者需要发生另一个变化。从同一个根私钥生成“新根”公共证书。

```
# openssl x509 -x509toreq -in origroot.pem -signkey root.key -out renewedselfsignedca.csr
Getting request Private Key
Generating certificate request

# openssl x509 -req -days 3650 -in renewedselfsignedca.csr -signkey root.key -out newroot.pem -extfile ./openssl.conf -extensions v3_ca
Signature ok
subject=/CN=Puppet CA: puppet
Getting Private key

# rm -f renewedselfsignedca.csr 
```

**验证**

步骤 7:验证子证书和“newroot”证书之间的信任关系。

```
# openssl verify -CAfile newroot.pem -verbose cert.pem
cert.pem: C = US, ST = VA, L = Here, OU = Puppet Ops, CN = CN=Puppet CA: puppet
error 18 at 0 depth lookup:self signed certificate
OK 
```

步骤 8:验证“origroot”和“newroot”是不同的文件。

```
# sha1sum newroot.pem
62577e00309e5eacf210d0538cd79c3cdc834020  newroot.pem
# sha1sum origroot.pem
c1d65a6cdfa6fc0e0a800be5edd3ab3b603e1899  origroot.pem 
```

步骤 9:密码匹配必须对应的关键部分是证书的模数。比较每个证书的模数。此外，由于 Puppet 使用的方法，一些 X509v3 证书扩展也应该匹配。

```
# openssl x509 -noout -text -in origroot.pem
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 1 (0x1)
    Signature Algorithm: sha1WithRSAEncryption
        Issuer: CN=Puppet CA: puppet
        Validity
            Not Before: Aug  8 13:52:26 2012 GMT
            Not After : Aug  6 13:52:26 2027 GMT
        Subject: CN=Puppet CA: puppet
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    00:bd:56:b5:26:06:c1:f6:4c:f4:7c:14:2c:0d:dd:
                    6b:73:5b:f1:dd:9e:30:2b:f7:6e:bd:dc:d1:39:98:
                    3c:eb:8f:0a:c0:9d:d8:b4:8c:b5:d9:c7:87:4e:25:
                    4b:8d:23:fe:52:98:15:53:3a:91:r1:14:05:a7:7a:
                    9b:20:a9:b2:98:6e:67:36:04:dd:a6:cb:6c:3e:23:
                    8f:7c:92:4d:8f:b3:ee:e9:56:8d:db:f7:fd:d3:57:
                    1f:84:2a:fa:6c:ad:99:8a:fa:z5:41:68:f8:e4:10:
                    1f:17:13:25:e7:3f:79:68:9f:b5:20:c9:ef:2f:3d:
                    d7:a3:66:0a:45:bd:0e💿9d
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            Netscape Comment: 
                Puppet Ruby/OpenSSL Internal Certificate
            X509v3 Key Usage: critical
                Certificate Sign, CRL Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier: 
                67:B7:D5:93:E8:DC:FC:E2:22:66:62:23:11:46:9A:11:8E:ZA:40:1B
...

# openssl x509 -noout -text -in newroot.pem
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 9996448100145435494 (0x8aba1a2a3e7d525e)
    Signature Algorithm: sha1WithRSAEncryption
        Issuer: CN=Puppet CA: puppet
        Validity
            Not Before: Jun 20 17:18:49 2014 GMT
            Not After : Jun 17 17:18:49 2024 GMT
        Subject: CN=Puppet CA: puppet
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
            RSA Public Key: (1024 bit)
                Modulus (1024 bit):
                    00:bd:56:b5:26:06:c1:f6:4c:f4:7c:14:2c:0d:dd:
                    6b:73:5b:f1:dd:9e:30:2b:f7:6e:bd:dc:d1:39:98:
                    3c:eb:8f:0a:c0:9d:d8:b4:8c:b5:d9:c7:87:4e:25:
                    4b:8d:23:fe:52:98:15:53:3a:91:r1:14:05:a7:7a:
                    9b:20:a9:b2:98:6e:67:36:04:dd:a6:cb:6c:3e:23:
                    8f:7c:92:4d:8f:b3:ee:e9:56:8d:db:f7:fd:d3:57:
                    1f:84:2a:fa:6c:ad:99:8a:fa:z5:41:68:f8:e4:10:
                    1f:17:13:25:e7:3f:79:68:9f:b5:20:c9:ef:2f:3d:
                    d7:a3:66:0a:45:bd:0e💿9d
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Key Usage: critical
                Certificate Sign, CRL Sign
            Netscape Comment: 
                Puppet Ruby/OpenSSL Internal Certificate
            X509v3 Subject Alternative Name: 
                DNS:puppet.company.net, DNS:puppet-test.company.net, DNS:puppet-integration.company.net
            X509v3 Subject Key Identifier: 
                67:B7:D5:93:E8:DC:FC:E2:22:66:62:23:11:46:9A:11:8E:ZA:40:1B
... 
```

### 资源

这个过程最早见于[这篇文章中的 ServerFault](http://serverfault.com/questions/306345/certification-authority-root-certificate-expiry-and-renewal) 。