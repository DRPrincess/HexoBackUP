## 代码规范

### 注释
- 保证每个 class 都有类说明注释
- 重要代码和方法，必须有详细注释

### 命名

请使用英语命名，放弃拼音，如有不会请查词典，尽量选择简单易懂无歧义的单词。

关于类,方法和成员变量的命名如下：

|类型|描述|示例|
|---|---|---|
|class|大驼峰|MainActivity|
|method|小驼峰|setUserPassword()|
|成员|小驼峰|newPassword|

变量命名细分：

|类型|前缀|示例|
|---|---|---|
|成员变量|m|mInstance|
|静态变量|s|sInstance|
|成员|无|instance|


## 资源命名规范
- 采用 小写字母_下划线 的组合形式，并遵循前缀表明类型的习惯，形如 type_name.xml。

### Id

- 控件 Id 的命名应该以该控件类型的缩写作为前缀
- 代码中的控件名保持一致。
- 命名方式采用小驼峰

|类型|前缀|示例|
|---|---|---|
|TextView	|tv|tvTitle|
|ImageView	|iv|ivShare|
|EditText	|et|rtPassword|
|Button	|btn|btnLogin|
|RecyclerView	|rv|rvCars|


### Layout

对于如何排版一个布局文件，请尽量遵循以下规范：

- 每个属性独占一行，缩进四个空格
- android:id 作为第一个属性存在
- 如果存在 style 属性，则紧随 id 之后
如果不存在 style 属性，则 android:layout_xxx 紧随 id 之后
- 当布局中的一个元素不再包含子元素时，另起一行，使用自闭合标签 />，方便调整和添加新的属性
- 善用 IDE 的 Reformat Code 功能，尽量在编辑完 XML 文件后进行格式化


布局文件应该与 Android 组件的命名相匹配，以组件类型作为前缀，并且能够清晰的表达意图所在。基本规则如下：   

|类型|描述|
|---|---|
|Activity|activity_|
|Fragment|fragment_|
|Adapter|item_|
|Dialog|dialog_|
|Partial Layout |视使用方式不同选择不同前缀，partial_或者 include_ 或者 merge_|



### String

通用模块：

|类别|前缀|
|---|---|
|提示语|msg_|
|错误提示|error_|
|标题提示，例如对话框|title_|
|动作按钮|action_|

功能模块：

|描述|命名|
|----|----|
|模块名_作用| 例如：my_reset_password|

### Drawable

#### 图片：

|类别|前缀|
|---|---|
|背景图|bg_|
|按钮|btn_|
|分割线|divider_|
|其他小图标|ic_|


其他小图标细分：

|类别|前缀|
|---|---|
|一般图片|ic_|
|启动图标|ic_launcher|
|对话框使用|ic_dialog|
|Tab 按钮|ic_tab|


#### .xml 文件

待规范

### Color

Color 以颜色来分类，颜色相同，可以加上副词,以下是项目中使用的，当然你也可以自定义：

|类别|
|---|
|very_light|
|light|
|primary|
|secondary|
|third|
|dark|


例如：

```

<color name="grey_very_light">#f9f9f9</color>
<color name="grey_light">#F2F2F2</color>
<color name="grey_primary">#dadada</color>
<color name="grey_secondary">#b7b7b7</color>
<color name="grey_third">#9B9B9B</color>
<color name="grey_dark">#666666</color>

```


### Dimens


|类别|前缀|
|---|---|
|字体|font_|
|按钮高度|btn_height_|
|标题高度|toolbar_|
|Padding|padding_|
|Margin|margin_|
|分隔|spacing_|


类似颜色，统一类别之后，可以用副词修饰，以下是建议副词，当然你也可以自定义，但是你自己要记住，以后好找一点。

|类别|
|---|
|mini|
|smaller|
|small|
|normal|
|large|
|larger|
|huge|

```
<!-- 字体大小规范-->
<dimen name="font_mini">9sp</dimen>
<dimen name="font_smaller">10sp</dimen>
<dimen name="font_small">13sp</dimen>
<dimen name="font_normal">14sp</dimen>
<dimen name="font_large">16sp</dimen>
<dimen name="font_larger">18sp</dimen>
<dimen name="font_huge">20sp</dimen>

```


### Style

- 大驼峰命名
- 按类型可以使用多个 Style 文件而不是全部写在 styles.cml 里，如：style_home.xml，style_item_details.xml，styles_forms.xml 等。

- 核心准则是保证 Layout 属性（position, margin, size 等）和 content 属性（text, src 等）在布局文件中，同时将所有的外观细节属性（color, padding, font）放在 Style 文件中。
