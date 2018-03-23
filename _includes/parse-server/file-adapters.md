# 配置文件适配器

Parse Server 允许开发人员在托管文件时从多个选项中选择:

* `GridStoreAdapter`, 由 MongoDB 支持;
* `S3Adapter`, 由[Amazon S3](https://aws.amazon.com/s3/)支持; 
* `GCSAdapter`, 由[Google云存储](https://cloud.google.com/storage/)支持;

`GridStoreAdapter` 默认使用且不需要设置,直接存储在MongoDB中, 如果你想使用S3或者谷歌云存储, 额外的配置信息如下.

当你在Parse中使用文件的时候, 你必须在你的Parse Server配置中使用`publicServerURL`选项. 这是可以从中访问文件的网址, 所以它应该是一个解析到你的Parse服务器的url. 要确保这个路径包含你的Parse API挂载点,比如前面例子中的"/parse".

## 配置 `S3Adapter`

如果你想使用Amazon S3, 下面是Parse Server如何配置使用`S3Adapter`的说明.

### 设置你的bucket和权限

首先你要在S3创建一个bucket来存储文件.

1. 登陆你的[AWS 账户](https://console.aws.amazon.com/) 或者注册一个新账号.
2. 前往S3服务并选择**Create Bucket**(创建Bucket)
3. 填入一个唯一的**Bucket Name**并点击**Create**.
4. 然后去Identity and Access Management (IAM)身份授权管理服务.
5. 点击**Users**标签, 然后**Create New User**.
6. 至少填写一个用户名并勾选**Generate an access key for each user**然后点击**Create**.
7. 确认**Download Credentials**在下一屏.
8. 然后选中**Policies**标签, 然后点击 **Create Policy**.
9. 选择 **Create Your Own Policy**, 填入一个**Policy Name**.
10. 复制下面的配置到**Policy Document**, 修改 **BUCKET_NAME** 为你之前创建的bucket的名字. (note: 比Parse Server必须的要宽松一些, 但是已经可以工作了应该)

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "s3:*"
                ],
                "Resource": [
                    "arn:aws:s3:::BUCKET_NAME",
                    "arn:aws:s3:::BUCKET_NAME/*"
                ]
            }
         ]
     }
    ```
11. 首先确保执行**Validate Policy**, 然后点击**Create Policy**.
12. 返回**Users** 标签选择你之前创建的用户.
13. 在权限管理中, 选择 **Attach Policy** 找到我们刚刚创建的Policy并添加上.

### 配置选项

把Parse Server的文件保存到S3创建的BUCKET非常容易,使用S3文件适配器即可.

#### 使用环境变量

如果你运行独立的Parse Server, 你可以使用下面的环境变量来配置S3 适配器:

| Variable Name | Description | Notes |
| ------------- | ----------- | ----- |
| PARSE_SERVER_FILES_ADAPTER  | 设置为 './Files/S3Adapter.js'. | 必须 |
| S3_ACCESS_KEY               | 有对应bucket权限的用户的Access Key. | 必须 |
| S3_SECRET_KEY               | 上述用户的Secret Key. | 必须 |
| S3_BUCKET                   | bucket名称 | 必须 |
| S3_REGION                   | AWS 区域. | 可选. 默认: 'us-east-1' |
| S3_BUCKET_PREFIX            | 文件的前缀. 使用 'folder/'这样类似的前缀可以把所有文件放在folder文件夹下面 | 可选. |
| S3_DIRECT_ACCESS            | 文件通过Parse Server转发还是直接访问. 如果设置成true, 文件将被设置为公共可读, 不必使用服务器代理转发读取. | 可选. 默认: false |

#### Passing as options

If you're using Node.js/Express:

```javascript
...
var S3Adapter = require('parse-server').S3Adapter;

var api = new ParseServer({
  databaseURI: databaseUri || 'mongodb://localhost:27017/dev',
  appId: process.env.APP_ID || 'APPLICATION_ID',
  masterKey: process.env.MASTER_KEY || 'MASTER_KEY',
  ...
  filesAdapter: new S3Adapter(
    "S3_ACCESS_KEY",
    "S3_SECRET_KEY",
    "S3_BUCKET",
    {directAccess: true}
  ),
  ...
});
```

Don't forget to change **S3_ACCESS_KEY**, **S3_SECRET_KEY** and **S3_BUCKET** to their correct value.

##### S3Adapter constructor options

```js
new S3Adapter(accessKey, secretKey, bucket, options)
```

| Parameter | Description | Notes |
| --------- | ----------- | ----- |
| accessKey    | The AWS access key for a user that has the required permissions | Required. |
| secretKey    | The AWS secret key for the user | Required. |
| bucket       | The name of your S3 bucket. | Required. |
| options      | JavaScript object (map) that can contain: | |
| region       | Key in `options`. Set the AWS region to connect to. | Optional. Default: 'us-east-1' |
| bucketPrefix | Key in `options`. Set to create all the files with the specified prefix added to the filename. Can be used to put all the files for an app in a folder with 'folder/'. | Optional. Default: '' |
| directAccess | Key in `options`. Controls whether reads are going directly to S3 or proxied through your Parse Server. If set to true, files will be made publicly accessible, and reads will not be proxied. | Optional. Default: false |


## Configuring `GCSAdapter`

Unlike the S3 adapter, you must create a new Cloud Storage bucket, as this is not created automatically. See the Google Cloud guide on [Authentication](https://googlecloudplatform.github.io/google-cloud-node/#/docs/google-cloud/master/guides/authentication) for more details.

To generate a private key in the Cloud Platform Console follow [these instructions](https://cloud.google.com/storage/docs/authentication#generating-a-private-key).

### Installation

Starting 2.2.6, GCS Adapter is not provided by default by parse-server. To install run:

```
npm install --save parse-server-gcs-adapter
```

### Configuration options

Writing to your Google Cloud Storage bucket from Parse Server is as simple as configuring and using the GCS files adapter.

#### Using environment variables

You can use Google Cloud Storage to host your static files by setting the following environment variables:

| Variable Name | Description | Notes |
| ------------- | ----------- | ----- |
| PARSE_SERVER_FILES_ADAPTER  | Set this variable to 'parse-server-gcs-adapter'. | Required. |
| GCP_PROJECT_ID              | The project ID from the Google Developer's Console. | Required. |
| GCP_KEYFILE_PATH            | Full path to the a .json, .pem, or .p12 key downloaded from the Google Developers Console. | Required. |
| GCS_BUCKET                  | The name of your GCS bucket. | Required. |
| GCS_BUCKET_PREFIX           | Create all the files with the specified prefix added to the filename. Can be used to put all the files for an app in a folder with 'folder/'. | Optional. |
| GCS_DIRECT_ACCESS           | Whether reads are going directly to GCS or proxied through your Parse Server. | Optional. Default: false |

#### Passing as options

If you're using Node.js/Express:

```javascript
...
var GCSAdapter = require('parse-server-gcs-adapter');

var api = new ParseServer({
  databaseURI: databaseUri || 'mongodb://localhost:27017/dev',
  appId: process.env.APP_ID || 'APPLICATION_ID',
  masterKey: process.env.MASTER_KEY || 'MASTER_KEY',
  ...
  filesAdapter: new GCSAdapter(
    "GCP_PROJECT_ID",
    "GCP_KEYFILE_PATH",
    "GCS_BUCKET",
    {directAccess: true}
  ),
  ...
});
```

##### GCSAdapter constructor options

```js
new GCSAdapter(projectId, keyfilePath, bucket, options)
```

| Parameter | Description | Notes |
| --------- | ----------- | ----- |
| projectId    | The project ID from the Google Developer's Console. | Required. |
| keyfilePath  | Full path to the a .json, .pem, or .p12 key downloaded from the Google Developers Console. | Required. |
| bucket       | The name of your GCS bucket. | Required. |
| options      | JavaScript object (map) that can contain: | |
| bucketPrefix | Key in `options`. Set to create all the files with the specified prefix added to the filename. Can be used to put all the files for an app in a folder with 'folder/'. | Optional. Default: '' |
| directAccess | Key in `options`. Controls whether reads are going directly to GCS or proxied through your Parse Server. | Optional. Default: false |
