# Validator 接入教程

### 1. 下载安装包并解压
`创建目录并进入`
```
mkdir -p ~/LambdaIM && cd ~/LambdaIM
```
`下载安装包`
```
wget https://github.com/LambdaIM/launch/releases/download/v0.2.3/lambda-0.2.3-release.tar.gz
```

`解压安装包`
```
tar zxvf lambda-0.2.3-release.tar.gz && cd lambda-0.2.3-release
```

### 2. 初始化节点  
`将下面命令中的[your-moniker]替换成您自定义的节点名称，不用加中括号`  
`注意：这里的 your-moniker 必须使用英文，用于P2P网络`
```
./lambda init [your-moniker] --chain-id lambda-chain-2.3
```
如果初始化报错，可能是由于有老版本的测试网配置数据导致，可以通过下面的命令清除错误数据
```
rm ~/.lambda/config/config.toml ~/.lambda/config/genesis.json
./lambda unsafe-reset-all
```

### 3. 覆盖genesis.json文件
```
\cp -rf ./genesis.json ~/.lambda/config/genesis.json
```

### 4. 配置节点
`要确保机器已开启端口26656, 26657`
```
./lambdacli config node tcp://0.0.0.0:26657
./lambdacli config chain-id lambda-chain-2.3
./lambdacli config trust-node true
```

### 5. 配置种子节点  
`编辑~/.lambda/config/config.toml文件，将文件中的seeds字段的值替换如下`

国内种子节点推荐
```
vim ~/.lambda/config/config.toml

节点列表
d3440b0b7a0ccf419f506a1242431813cf8a699c@39.104.97.255:26656
98a0a749080b367d218f68b628b2db3d8d175af9@39.100.6.247:26656
91fa14e2c8da164ff7969f2bbc0ba5c6e206da8a@101.201.65.116:26656 
```

国外种子节点推荐
```
vim ~/.lambda/config/config.toml

节点列表
dd3360f3a4334432af394a835662a2b21bf406e2@seednodes.oneweb.one:26656
72e1dd22f2c3effc4e6ff842035f109480a997ae@seednodes1.oneweb.one:26656
```

**注意**

当前支持配置多个种子节点，通过`,`隔开  
切换节点后需要kill掉节点服务并且重启

### 6. 启动节点  
```
nohup ./lambda start --p2p.laddr tcp://0.0.0.0:26656 --rpc.laddr tcp://0.0.0.0:26657 >> /tmp/lambda.log 2>&1 &
```

### 7. 添加账户  
`将[your-account-name]替换成您自定义的账户名称，需要设置您的账户密码，不用加中括号`
```
./lambdacli keys add [your-account-name]
```

```如果是钱包创建的账户导入，则通过钱包创建账户时候的助记词进行操作```
```
./lambdacli keys add [your-account-name] --recover
```
输入命令后按照提示输入密码和助记词即可


### 8. 提交地址
提交上一步命令中返回的账户地址，用钱包签名并在质押系统内提交，我们会在收到后一定时间内转账

### 9. 创建 Validator  
`创建Validator需要如下信息`
* pubkey -- 通过命令`./lambda tendermint show-validator` 获取
* moniker -- 这里的`moniker`名称是您的`Validator`名称，可以使用中文(与第2步的moniker可以不同),
             如果您已经创建，后面的FAQ中也有修改该名称的命令介绍
* your-account-name -- 您在第7步中设置的账户名称

`获取上述信息后，填充如下命令并执行（过程中会提示输入账号密码），即可创建Validator, 注意：所有参数不需要中括号`
```
./lambdacli tx staking create-validator \
  --amount 666666666utbb \
  --pubkey [your-cons-pubkey] \
  --moniker "[your-moniker]" \
  --from [your-account-name] --broadcast-mode block 
```
说明：commission相关参数数值可自行指定，0.1指节点对质押者收取10%的佣金。可使用lambdacli修改节点设置。

执行完上述命令后，会返回类似如下信息，destination-validator 即 Validtor 的操作地址
```
Response:
  Height: 617
  TxHash: 9B67980CFAE286B220B912549D4288119BEDAA7B74B831FA999C3AA60089B85C
  Raw Log: [{"msg_index":"0","success":true,"log":""}]
  Logs: [{"msg_index":0,"success":true,"log":""}]
  GasWanted: 200000
  GasUsed: 121675
  Tags: 
    - action = create_validator
    - destination-validator = lambdavaloper1g2wgwnrdhj29v62jh9nj8kxml48dg3sfrujk2s
    - moniker = ohoh-11
    - identity =
```

Validator 的操作地址也可通过命令获取
```
./lambdacli keys show [your-account-name] --bech val
```

**注意**

当前已经成为validator的节点，在以下情况下会被惩罚，共识网络会扣除节点质押的utbb，并且把  
节点移出validator集合

1. 对块进行双签
2. 在最近的10000个块中对少于500个块签名

被移出的节点需要做如下操作重新加入共识网络

1. 如果不满足validator的最低质押要求`666,666,666utbb`, 需要发起质押补足扣除的utbb，可进入浏览器——验证节点——节点详情——质押列表——查看当前节点账户地址质押代币数量 即为当前已质押代币数量
命令示例参考  
```
./lambdacli tx staking delegate [validator-address] [amount-of-utbb] --from [your-account-name]
```

2. 发起`unjail`消息来重新加入共识网络
被`jail`之后的节点需要等待10分钟的惩罚来发起`unjail`命令

```
./lambdacli tx slashing unjail --from [your-account-name]
```

# FAQ
### 查询账户不存在？

出现如下错误提示, 可能是由于您刚刚启动 lambda 还没有同步到最新块高
可以通过日志(`/tmp/lambda.log`)查看块高情况
```
[root@zh]# ./lambdacli query account lambda1z66gxs2wlhmkhh3rljtkzk96fqkk7809zb123
ERROR: {"codespace":"sdk","code":9,"message":"account lambda1z66gxs2wlhmkhh3rljtkzk96fqkkl7809zb123 does not exist"}
```

### ABCI 连接被拒绝？
出现如下错误提示，可通过`telnet`命令检查您的机器 26656 和 26657 端口是否能通
```
ERROR: ABCIQuery: Post xxx connect: connection refused
```

### 如何修改 Validator 名称
```
./lambdacli tx staking edit-validator --moniker "your-custom-name" --from [your-account-name] --broadcast-mode block -y
```
