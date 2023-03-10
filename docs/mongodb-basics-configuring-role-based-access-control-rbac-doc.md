# MongoDB 基础:配置基于角色的访问控制(RBAC)

> 原文：<https://dev.to/scalegrid/mongodb-basics-configuring-role-based-access-control-rbac-doc>

# MongoDB 基础:配置基于角色的访问控制(RBAC)

MongoDB 是一个开源文档存储库，也是当今市场上最受欢迎的 NoSQL 数据库，它提供了各种高级特性来管理 MongoDB 部署的安全性。在这篇教程文章中，我们将向您展示如何为 reIndex、mongodump 和 mongorestore 设置基于角色的访问控制(RBAC)来管理跨 MongoDB 系统的用户访问。

如果您是 MongoDB 数据库的管理员，您可能会收到请求，要求为单个用户提供执行特定操作的能力。 [MongoDB 的安全](https://scalegrid.io/blog/the-three-as-of-mongodb-security-authentication-authorization-auditing/) [特性](https://docs.mongodb.com/manual/security/)现在已经相当成熟，允许你创建和分配非常细粒度的[基于角色的访问控制](https://docs.mongodb.com/manual/core/authorization/)。

我们将通过一个示例，一步一步地向用户授予执行 [reIndex](https://docs.mongodb.com/manual/reference/privilege-actions/#reIndex) 操作的特定权限。在我们演示如何执行这个常见的安全操作之前，让我们先了解一下 MongoDB 的基于角色的操作控制。

## MongoDB RBAC 如何工作

MongoDB 允许您使用一种 [RBAC](https://en.wikipedia.org/wiki/Role-based_access_control) 机制，通过指定的“角色”来限制用户的访问。默认情况下，不会启用 RBAC 访问控制，必须由您团队中的管理员进行配置。通过授予用户对特定资源的访问权限，您授权该角色或用户对该资源执行操作。

*   #### 资源

    数据库、集合、集合集或群集。

*   #### 行动

    用户可以对资源(通常是数据库)执行的特定操作。

MongoDB 支持名为[的预定义角色，内置角色](https://docs.mongodb.com/manual/reference/built-in-roles/)，动作被整理成逻辑组，如[(只读)、](https://docs.mongodb.com/manual/reference/built-in-roles/#read) [(读写)](https://docs.mongodb.com/manual/reference/built-in-roles/#readWrite)、[备份](https://docs.mongodb.com/manual/reference/built-in-roles/#backup)等。MongoDB 还支持创建[用户定义的角色](https://docs.mongodb.com/manual/core/security-user-defined-roles/)。

## 授权 MongoDB 重新索引 RBAC 权限

现在您已经有了一些背景知识，让我们进入授权用户执行重新索引操作的步骤。

1.  ### 确定权限操作

    MongoDB 上下文中的动作被称为[特权动作](https://docs.mongodb.com/manual/reference/privilege-actions/#privilege-actions)，您可以在 [MongoDB 的文档](https://docs.mongodb.com/manual/reference/privilege-actions/#privilege-actions)中找到这些动作的详尽列表。我们感兴趣的动作是 [reIndex、](https://docs.mongodb.com/manual/reference/privilege-actions/#reIndex)或允许用户在给定数据库或集合上执行 [reIndex 命令](https://docs.mongodb.com/manual/reference/command/reIndex)的特权。由于对于包含大量数据或大量索引的集合来说，reIndex 命令的开销可能很大，因此默认情况下，它是[管理角色](https://docs.mongodb.com/manual/reference/built-in-roles/#dbAdmin)的一部分。

2.  ### 授予角色权限

    一旦我们确定了我们需要的特权，我们就继续向角色授予那些特权。您也可以简单地选择一个已经拥有该权限的内置角色，但是我们建议您创建自己的用户定义角色，而不是修改内置角色，因为它们是很好的默认值。

    1.  #### 创建新的用户定义的角色

        下面是我们将用来[创建](https://docs.mongodb.com/manual/reference/method/db.createRole/)新的用户定义角色的内容:

        ```
        > use test
        switched to db test
        > db.createRole({role: "reIndexing", privileges: [], roles: ["readWrite"]}) // Create a new role out of the default readWrite rule.
        { "role" : "reIndexing", "privileges" : [ ], "roles" : [ "readWrite" ] }
        > db.getRole("reIndexing")
        {
            "role" : "reIndexing",
            "db" : "test",
            "isBuiltin" : false,
            "roles" : [
                {
                    "role" : "readWrite",
                    "db" : "test"
                }
            ],
            "inheritedRoles" : [
                {
                    "role" : "readWrite",
                    "db" : "test"
                }
            ]
        }
        ```

    2.  #### 授予新角色权限

        现在，我们将为新创建的用户定义的角色分配所需的[权限](https://docs.mongodb.com/manual/reference/command/grantPrivilegesToRole/)。

        ```
        > db.grantPrivilegesToRole("reIndexing", [ { resource: { db : "test", collection: "" }, actions: ["reIndex"] } ])
        > db.getRole("reIndexing", {showPrivileges: true})
        {
            "role" : "reIndexing",
            "db" : "test",
            "isBuiltin" : false,
            "roles" : [
                {
                    "role" : "readWrite",
                    "db" : "test"
                }
            ],
            "inheritedRoles" : [
                {
                    "role" : "readWrite",
                    "db" : "test"
                }
            ],
         "privileges" : [
                {
                    "resource" : {
                        "db" : "test",
                        "collection" : ""
                    },
                    "actions" : [
                        "reIndex"
                    ]
                }
            ],
            "inheritedPrivileges" : [
        ....
            ]
        }

        ```

3.  ### 将角色授予用户

    最后一步是简单地[给用户](https://docs.mongodb.com/manual/reference/method/db.grantRolesToUser/)分配这个新角色:

    ```
    > db.getUser("indexUser")
    {
        "_id" : "test.indexUser",
        "user" : "indexUser",
        "db" : "test",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "test"
            }
        ]
    }
    > db.grantRolesToUser("indexUser", ["reIndexing"])
    > db.getUser("indexUser")
    {
        "_id" : "test.indexUser",
        "user" : "indexUser",
        "db" : "test",
        "roles" : [
     {
                "role" : "reIndexing",
                "db" : "test"
            },
            {
                "role" : "readWrite",
                "db" : "test"
            }
        ]
    }

    ```

    请记住在虚拟用户上执行这些步骤，这样您就可以在授予和通知相关用户他们的授权之前，测试和确认新权限的工作。

## 附加特权:mongodump 和 mongorestore

我们首先演示了 reIndex 示例，以说明向角色授予特权和向用户授予角色。

一个更常见的用例是使用 [`mongodump`](https://docs.mongodb.com/manual/reference/program/mongodump/) 和 [`mongorestore`](https://docs.mongodb.com/manual/reference/program/mongorestore/) 授权执行[备份](https://docs.mongodb.com/manual/reference/built-in-roles/#backup)和[恢复](https://docs.mongodb.com/manual/reference/built-in-roles/#restore)操作。这可以通过 MongoDB 的内置角色一步完成，并且可以授予备份和恢复权限，使用户能够分别运行`mongodump`和`mongorestore`。

例如，下面是我们如何授予用户备份和恢复任何数据库的权限。*请注意，这些角色仅对管理数据库中的用户可用。*

```
> db.grantRolesToUser("backupUser", ["backup", "restore"])
> db.getUser("backupUser")
{
    "_id" : "admin.backupUser",
    "user" : "backupUser",
    "db" : "admin",
    "roles" : [
        {
            "role" : "restore",
            "db" : "admin"
        },
        {
            "role" : "backup",
            "db" : "admin"
        },
        {
            "role" : "readWrite",
            "db" : "admin"
        }
    ]
}

```

在单个数据库或集合上添加备份和还原权限需要更多的工作。对于`mongodump`，您必须额外授予对该给定数据库的 [find](https://docs.mongodb.com/manual/reference/program/mongodump/#required-access) 权限，但是 [`mongorestore`](https://docs.mongodb.com/manual/reference/program/mongorestore/#required-access) 有更复杂的权限要求。

#### 对 MongoDB 安全性感兴趣？以下是关于 MongoDB 安全主题的热门帖子:

*   [MongoDB 安全的三个 A——认证、授权&审计](https://scalegrid.io/blog/the-three-as-of-mongodb-security-authentication-authorization-auditing/)
*   [提高 MongoDB 安装安全性的三个简单步骤](https://scalegrid.io/blog/three-simple-steps-to-improve-the-security-of-your-mongodb-installation/)
*   [在 MongoDB 3.x 上默认配置 MongoDB-CR 认证](https://scalegrid.io/blog/configuring-mongodb-cr-authentication-as-default-on-mongodb-3-x/)
*   [node . js 中带有自签名证书的 MongoDB SSL](https://scalegrid.io/blog/mongodb-ssl-with-self-signed-certificates-in-node-js/)
*   [提高 MongoDB 安全性的 10 个技巧](https://scalegrid.io/blog/10-tips-to-improve-your-mongodb-security/)

T2】