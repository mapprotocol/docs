# near-lake-s3
near-lake-s3 用於將某些塊從 AWS S3 存儲桶同步到 redis 服務器。 該塊必須包含與指定帳戶相關的交易/收據。

## 如何構建？

```shell
cargo build --release
```

./target/release 中將生成二進製文件“near-lake-s3”。

## 如何運行

### AWS S3 證書

為了能夠從 AWS S3 存儲桶中獲取對象，您需要提供 AWS 憑證。

使用 aws configure 的 AWS 默認配置文件配置類似於以下內容：

`~/.aws/credentials`
```
[default]
aws_access_key_id=
aws_secret_access_key=
```

[AWS 文檔：配置和憑證文件設置](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

### 環境配置

您可以將 env.example 複製到 .env，修改它並將其放在與 near-lake-s3 相同的目錄（或其父目錄）中。

也許您可以為以下配置安裝 [redis](https://redis.io/docs/getting-started/installation/)。

```
// Get the start block height from cache if true, if no record is found, use START_BLOCK_HEIGHT
START_BLOCK_HEIGHT_FROM_CACHE=true
START_BLOCK_HEIGHT=10000

// The URL of the redis
REDIS_URL="redis://127.0.0.1:6379"
// Redis list name where the block will push to
PUB_LIST="blocks"

// The account name to watch
MCS="mcs.testnet"

// True for NEAR tesnet, false for NEAR mainnet
TEST=true

// Log file
LOG_FILE="./near-lake.log"

// Log level
LOG_LEVEL="INFO"
```


### 運行
```shell
./target/release/near-lake-s3
```