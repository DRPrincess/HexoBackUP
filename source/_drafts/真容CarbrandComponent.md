# CarbrandComponent

### 功能说明：
车辆品型款模块
截止2018.7.16，支持两种选择模式：

1. 品牌-车系 二列展现模式；含有热门品牌；含有不限品牌，不限车系，不限车型的选项；（真容二手车中使用，用于选择车型筛选信息）
2. 品牌-车系-车型 三列展现模式；（真容检测中使用，用于选择车型填入信息）


### 依赖模块
```
implementation project(':baselib')
implementation project(':basicres')
implementation project(':cmnetworklib')
```

### 使用方法

需要在APP moduel的asset 文件夹中手动添加以下内容
- 通用的字体文件 iconfont.ttf ，这个严格意义上是baseRes 中的要求
- 车辆的logo图片 brandicon



#### 输入参数：

```
/**
 * 描述   ：选择车型库输入参数实体类
 * <p>
 * 作者   ：Created by DR on 2018/7/11.
 */

public class BrandSelectINBean implements Serializable {

    /**
     * 车辆品型款选择模式
     */
    public SelectMode mode = SelectMode.Brand_Model_Chexing;
    /**
     * 品牌列表上次选择的位置
     */
    public int brandPosition = -1;
    /**
     * 车系列表上次选择的位置
     */
    public int modelPosition = -1;

    /**
     * 车型列表上次选择的位置
     */
    public int chexingPosition = -1;



}

```


#### 输出参数：

```

/**
 * 描述   ：车型库选择返回结果实体类
 * <p>
 * 作者   ：Created by DR on 2018/7/11.
 */

public class BrandSelectResultBean implements Serializable {

    /**
     * 品牌ID
     */
    public String brandId;
    /**
     * 品牌名称
     */
    public String brandName;
    /**
     * 车系ID
     */
    public String modelId;
    /**
     * 车系名称
     */
    public String modelName;

    /**
     * 品牌列表上次选择的位置
     */
    public int brandPosition;
    /**
     * 车系列表上次选择的位置
     */
    public int modelPosition;

    /**
     * 车型列表上次选择的位置
     */
    public int chexingPosition;
    /**
     * 车型id
     */
    public String chexingId;
    /**
     * 车型名称
     */
    public String chexingName;

    /**
     * 排放标准 1-国1, 2-国2, 3-国3, 4-国4, 5-国5, 6-国3带OBD, 7-国4京5, 8-欧6
     */
    public String effluent;
    /**
     * 燃料类型
     */
    public String fuel;
    /**
     * 车型排量
     */
    public String displa;
    /**
     * 排量类型
     */
    public String displaType;
    /**
     * 车型变速箱类型
     */
    public String gearbox;
    /**
     * 指导价格(单位：万元)
     */
    public String newCarPrice;
    /**
     * 车辆类型
     */
    public String carType;
    /**
     * 年款
     */
    public String year;
    /**
     * 座位数
     */
    public String seat;

}


```
