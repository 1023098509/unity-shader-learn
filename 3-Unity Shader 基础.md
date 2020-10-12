
# **Material**和**Unity Shader**

## **Material**（材质）

材质通过 Mesh 和 Particle Systems 组件来使用。材质与 Unity Shader 的关系就像对象与类，材质是 Unity Shader 的一种实体，可以绑定 Unity Shader 需要的各种参数，并且这些参数可以通过材质面板轻松设置。

## **Unity Shader**

Unity Shader 是对渲染流水线中着色器的一层封装。它是游戏引擎与显卡驱动之间通讯的桥梁。

封装的好处：

1. 渲染所需的各种参数，可以通过材质或着色器面板轻松设置，不用写代码。

2. 一个 Unity Shader 里可以实现渲染流水线多个不同阶段的着色器，比如顶点着色器和片元着色器。

3. 跨平台，Unity 可以通过配置生成对应平台的着色器。

# **Unity Shader** 的结构

Unity Shader 使用 ShaderLab 语言编写。

    Shader "name" { [Properties] Subshaders [Fallback] [CustomEditor] }

## **Unity Shader** 命名

    Shader "xxxxx/xxxx"

## **Properties**

Properties语义块中包含了一系列属性（property），这些属性将会出现在材质面板中。

    Properties { Property [Property ...]}
    //数字和滑动条
    name ("display name", Range (min, max)) = number
    name ("display name", Float) = number
    name ("display name", Int) = number
    //颜色和矢量
    name ("display name", Color) = (number,number,number,number)
    name ("display name", Vector) = (number,number,number,number)
    //纹理
    name ("display name", 2D) = "defaulttexture" {}
    name ("display name", Cube) = "defaulttexture" {}
    name ("display name", 3D) = "defaulttexture" {}

### 属性特性和绘制器

在属性前面，可指定可选的特性（用方括号括起）。

* *[HideInInspector]* - 不在材质检视面板中显示属性值。
* *[NoScaleOffset]* - 对于具有此特性的纹理属性，材质检视面板不会显示纹理平铺/偏移字段。
* *[Normal]* - 表示纹理属性需要法线贴图。
* *[HDR]* - 表示纹理属性需要高动态范围 (HDR) 纹理。
* *[Gamma]* - 表示在 UI 中将浮点/矢量属性指定为 sRGB 值（就像颜色一样），并且可能需要根据使用的颜色空间进行转换。请参阅着色器程序中的属性。
* *[PerRendererData]* - 表示纹理属性将以 MaterialPropertyBlock 的形式来自每渲染器数据。材质检视面板会更改这些属性的纹理字段 UI。
* *[MainTexture]* - 表示一个属性 (property) 是材质的主纹理。默认情况下，Unity 将属性 (property) 名称为 _MainTex 的纹理视为主纹理。如果您的纹理具有其他属性 (property) 名称，但您希望 Unity 将这个纹理视为主纹理，请使用此属性 (attribute)。如果您多次使用此属性 (attribute)，则 Unity 会使用第一个属性 (property)，而忽略后续属性 (property)。
* *[MainColor]* - 表示一个属性 (property) 是材质的主色。默认情况下，Unity 将属性 (property) 名称为 _Color 的颜色视为主色。如果您的颜色具有其他属性 (property) 名称，但您希望 Unity 将这个颜色视为主色，请使用此属性 (attribute)。如果您多次使用此属性 (attribute)，则 Unity 会使用第一个属性 (property)，而忽略后续属性 (property)。

## **SubShader**

Unity 中的每个着色器都包含一个子着色器列表。根据用户显卡选择第一个可以运行的子着色器。目的是为不同用户达到不同性能的渲染效果。

    Subshader { [Tags] [CommonState] Passdef [Passdef ...]}

### **1. SubShader Tags**

子着色器使用标签来告知它们期望何时以何种方式被渲染到渲染引擎。

    Tags { "TagName1" = "Value1" "TagName2" = "Value2" }

    // 渲染顺序 
    Queue = Background | Geometry | AlphaTest | Transparent | Overlay 

    // 将着色器分为几个预定义的组
    RenderType 

    // 关闭批处理
    DisableBatching  = False | True | LODFading

    // 不投射阴影
    ForceNoShadowCasting  = False | True

    // 忽略投影
    IgnoreProjector = False | True

    // 能使用精灵图集
    CanUseSpriteAtlas = False | True
    
    // 指示材质检视面板预览应如何显示材质
    PreviewType = Plane | Skybox

### **2. CommonState**

设置的公共状态将应用到所有 Pass。

    //设置多边形剔除模式。
    Cull Back | Front | Off

    //设置深度缓冲区测试模式。
    ZTest (Less | Greater | LEqual | GEqual | Equal | NotEqual | Always)

    //设置深度缓冲区写入模式。
    ZWrite On | Off

    //开启并设置混合模式。
    Blend SrcFactor DstFactor

### **3. Pass**

Pass 代码块将使游戏对象的几何体被渲染一次。

    Pass { [Name and Tags] [RenderSetup] }

#### Name

为当前通道提供 PassName 名称。为通道提供名称即可让 UsePass 命令能够引用该通道。

    Name "PassName"

#### Pass Tags

    Tags { "TagName1" = "Value1" "TagName2" = "Value2" }

    //定义通道在光照管线中的角色。
    LightMode = Always | ForwardBase | ForwardAdd | Deferred | ShadowCaster | MotionVectors | PrepassBase | PrepassFinal | Vertex | VertexLMRGBM | VertexLM

    //一个通道可指示一些标志来更改渲染管线向通道传递数据的方式。
    PassFlags = OnlyDirectional

    //一个通道可指示仅当满足某些外部条件时才渲染该通道
    RequireOptions = SoftVegetation

#### RenderSetup（渲染状态设置）

    //设置多边形剔除模式。
    Cull Back | Front | Off

    //设置深度缓冲区测试模式。
    ZTest (Less | Greater | LEqual | GEqual | Equal | NotEqual | Always)

    //设置深度缓冲区写入模式。
    ZWrite On | Off

    //设置 Z 缓冲区深度偏移。
    Offset OffsetFactor, OffsetUnits

    //设置 Alpha 混合、Alpha 操作和 alpha-to-coverage 模式。
    Blend sourceBlendMode destBlendMode
    Blend sourceBlendMode destBlendMode, alphaSourceBlendMode alphaDestBlendMode
    BlendOp colorOp
    BlendOp colorOp, alphaOp
    AlphaToMask On | Off

    //设置颜色通道写入遮罩。
    ColorMask RGB | A | 0 | R、G、B、A 的任意组合

### **UsePass**

UsePass 命令使用来自另一个着色器的指定通道。在内部，所有通道名称均大写，因此 UsePass 必须引用大写名称。

    UsePass "Shader/NAME"

### **GrabPass**

GrabPass 是一种特殊通道类型，它把即将绘制对象时的屏幕内容抓取到纹理中。在后续通道中即可使用此纹理，从而执行基于图像的高级效果。

    GrabPass { "TextureName" } 

## **Fallback**

如果没有任何子着色器能够在此硬件上运行，则尝试使用另一个着色器中的子着色器

    Fallback "name"    

# Unity Shader的形式

## 表面着色器

由Unity实现，处理了大量光照细节，可以方便的使用，但渲染代价比较大。

## 顶点着色器/片元着色器

更灵活，但需要开发者控制更多细节。

## 固定函数着色器

废弃了，可以让着色器在旧的机器上运行。
