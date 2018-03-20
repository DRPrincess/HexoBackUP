

##  写在前面

为了18年春节后的接口迁移工作，真容检测APP整理如下。

## 正式和测试环境区分
项目中测试环境和正式环境的域名规定如下：

```
 public static final String HOST_API_ONLINE = "https://www.chemao.com/";
 public static final String HOST_API_DEBUG = "https://test.365eche.net/";
 public static final String HOST_WAP_ONLINE = "https://m.chemao.com/";
 public static final String HOST_WAP_DEBUG = "https://testm.365eche.net/";
 public static final String Host_H5_Debug = "https://testapph5.365eche.net/";
 public static final String Host_H5_Online = "https://apph5.chemao.com/";
```

## 首页模块


| 用途| 地址 |
|:---|:---|
|首页Banner|http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.index&c=GET_AD_LIST|
|版本更新|http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client&c=CHECK_APP_UPGRADE|





##  质保检测


| 用途| 地址 |
|:---|:---|
|根据车辆首次上牌、公里、新车指导价判断车辆有无质保可买|http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.detect_car&c=GET_CAR_CAN_BUY_QA_OR_NOT|
|根据VIN码获取相关车辆信息|http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.detect_car&c=CHECK_CAR_VIN_CAN_USE_OR_NOT|
|根据VIN码获取最近一次车辆质保检测记录日志| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.detect_car&c=GET_LAST_DETECT_INFO_LOG |
| 创建质保检测车，并记录检测记录| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.detect_car&c=CREATE_DETECT_CAR |


## 用户登录
| 用途| 地址 |
|:---|:---|
| 检测APP登录验证| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.cert_worker&c=WORKER_LOGIN |

## 检测认证

| 用途| 地址 |
|:---|:---|
| 根据vin码，获取该车辆是否已经检测过| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.car_vin&c=GET_CERT_INFO_BY_VIN |
| 校验核销码V2| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.verification_code&c=VERIFY_CODE_V2|
| 购买4s查询记录，自动购买，循环购买（车鉴定=》查博士）| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.4s_record&c=BUY_REPORT_V2 |
| 根据vin获取车型的相同点和不同点|http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.car_vin&c=GET_CHEKUAN_INFO_BY_VIN |
| 获取4s查询报告地址| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.4s_record&c=GET_REPORT_URL|
| 检测检测上传数据接口| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.upload&c=UPLOAD_CERT_INFO_V3 |
|获取车型优势| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.check&c=CHECK_MODEL_ADVANTAGE_INFO |
| 根据vin码，获取该车辆历史检测记录| http://testfapis.365eche.net/reflection.php?ctl=doc&p=app_client.cert.car_vin&c=GET_HISTORY_CERT_INFO_BY_VIN |


## 车辆品型款

| 用途| 地址 |
|:---|:---|
|获取汽车的品牌列表| http://testfapis.365eche.net/reflection.php?ctl=doc&p=pub_lib.car_attribute&c=CAR_CATEGORY_BRAND_LIST_V2 |
| 获取汽车的车系列表
车品(brand)->车系(model)| http://testfapis.365eche.net/reflection.php?ctl=doc&p=pub_lib.car_attribute&c=CAR_CATEGORY_MODEL_GROUP_LIST_V2 |
| 获取汽车的车型列表
车品(brand)->车系(model)->车型(chexing)|http://testfapis.365eche.net/reflection.php?ctl=doc&p=pub_lib.car_attribute&c=CAR_CATEGORY_CHEXING_LIST|


## 图片上传

| 用途| 地址 |
|:---|:---|
| 获取阿里云sts账号| http://testfapis.365eche.net/reflection.php?ctl=doc&p=pub_lib.sts&c=ALIYUN_USER |
