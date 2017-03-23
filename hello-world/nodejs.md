# 使用nvm安装Node.js
nvm ([Node Version Manager](https://github.com/creationix/nvm))用于管理Node的版本。

### 安装nvm
打开一个Terminal，运行:
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```
安装nvm。

运行:

``` source ~/.bashrc ```

为当前Terminal添加nvm的环境变量，这样你才能直接使用nvm命令。

### 安装最新版Long Term Support node
运行:
```
nvm install --lts
```
安装node。

运行:
```
node -v
```
查看当前Node版本。
