# git提交代码总是报ssL443错误

报错信息：
fatal: unable to access 'https://github.com/arieltlm/my-blog.git/': LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443 

解决办法：

有网友说：

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```
这个办法是时而有用时而无用了；

或可以重新添加一次ssh key即可
