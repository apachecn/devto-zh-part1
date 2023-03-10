# 用 npm 构建加速 Jenkins

> 原文：<https://dev.to/khsing/speed-up-jenkins-with-npm-build-3pc>

我们的团队有许多由 Jenkins 交付的项目。每个组件都需要用写在 React.js 或 Vue.js 中的 webpack 来组装。

每个构建将花费 5 或 10 分钟来完成，并且大部分时间花费在`npm install`上。但是我们都知道，如果`package.json`没有改变，`node_modules`目录也不应该改变。

因此，我们使用 MD5 对`package.json`进行校验和检查，并将整个`node_modules`目录打包为缓存。如果哈希字符串没有改变，这意味着什么都没有改变，只需解压缓存的`node_modules`目录，然后执行`npm install`，所有的事情都完成了。

这里有一个简单的例子

```
PKG_SUM=$(md5sum package.json | cut -d\  -f 1)
NPM_TARBALL=node_modules-${PKG_SUM}.tgz
NPM_TARBALL_MD5SUM=${NPM_TARBALL}.md5sum
NPM_TARBALL_CACHE=${HOME}/.cache/npmtarball
S3_NPM_TARBALL=s3://deployment/npmtarball

[[ ! -e $NPM_TARBALL_CACHE ]] && mkdir -p $NPM_TARBALL_CACHE

function downloadNpmTarball(){
    pushd $NPM_TARBALL_CACHE
        if [ ! -f ${NPM_TARBALL} ];then s3cmd get ${S3_NPM_TARBALL}/${NPM_TARBALL} ${NPM_TARBALL}
            s3cmd get ${S3_NPM_TARBALL}/${NPM_TARBALL_MD5SUM} ${NPM_TARBALL_MD5SUM}
            md5sum -c ${NPM_TARBALL_MD5SUM} || rm -f ${NPM_TARBALL} ${NPM_TARBALL_MD5SUM}
        fi popd
}

function checkNpmMod() {
    downloadNpmTarball
    TARBALL=${NPM_TARBALL_CACHE}/${NPM_TARBALL}
    [[ -f $TARBALL ]] && tar xzf $TARBALL
}

function uploadNpmMod() {
    TARBALL=${NPM_TARBALL_CACHE}/${NPM_TARBALL}
    if [ ! -f  ${TARBALL} ];then
        if [ -d node_modules ];then tar zcf ${TARBALL} node_modules || return 1
        fi pushd $NPM_TARBALL_CACHE
             md5sum $NPM_TARBALL > ${NPM_TARBALL_MD5SUM}
             s3cmd put $NPM_TARBALL $S3_NPM_TARBALL/$NPM_TARBALL
             s3cmd put $NPM_TARBALL_MD5SUM $S3_NPM_TARBALL/$NPM_TARBALL_MD5SUM
        popd fi
}
checkNpmMod
npm install uploadNpmMod 
```

Enter fullscreen mode Exit fullscreen mode