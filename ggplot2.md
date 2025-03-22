
在 R 脚本或 Rmarkdown 文件的开头，必须加载必要的库。  
例如：

- **`ggplot2`** 用于可视化：[https://ggplot2-book.org/](https://ggplot2-book.org/)
- **`patchwork`** 用于拼接图表（将单独的 ggplot 图组合在一起）
- 设置全局主题

```
# 加载必要的库
library(ggplot2)     # 数据可视化
library(patchwork)   # 拼接多个 ggplot 图

# 设置全局主题
theme_set(theme_minimal())
```
**通过上述设置，可以在后续代码中更方便地使用 `ggplot2` 和 `patchwork`，并确保图表样式保持一致**

### CO2 排放量

为了说明这一点，我们首先查看一个包含 **2005 至 2011 年间不同国家经济指标**的数据集。  
每个国家和年份记录了以下数据（其中包括但不限于）：

- **GDP**：国内生产总值（Bruttoinlandsprodukt）
- **CO2emission**：总排放的二氧化碳量（单位：千吨）

```
countries <- readRDS("co2data.Rds") 
countries[1:5, 1:8] |> knitr::kable() |> kableExtra::kable_styling("striped")
```
- `knitr::kable()`：将提取的数据转换为可用于展示的表格。
- `kableExtra::kable_styling("striped")`：使用 `kableExtra` 包美化表格，添加“条纹”样式（表格的奇偶行背景颜色不同）。

### 散点图 (scatter plot)

生成的 ggplot 图表由以下几个主要部分组成：

1. **数据**：
    - 数据以 **数据框 (Data Frame)** 的形式提供，作为绘制图表的基础。
2. **美学映射 (aesthetic mappings)**：
    - 使用 `aes()` 函数定义，它决定了数据中的变量如何映射到图形的视觉属性（例如：这里指定了哪些变量用于 x 轴和 y 轴绘图）。
3. **几何层对象 (geometric layer object)**：
    - 描述每个数据点的呈现方式。在本例中，我们使用 `geom_point()`，表示将每个数据点绘制为一个点。

```
countries11 <- subset(countries, year == 2011, select = c("continent", "GDP", "Population", "CO2emission"))

ggplot(data = countries11, mapping = aes(x = GDP, y = CO2emission)) + geom_point()

ggplot(countries11, aes(x = GDP, y = CO2emission)) + layer(mapping = NULL, data = NULL, geom = "point" , stat = "identity" , position = "identity")
```
- 在 R 中，`subset()` 函数用于从数据集中提取满足特定条件的子集
- 重要的是要理解，ggplot 的结构是模块化的。也就是说，**可以通过 `"+"` 添加不同的层（Layer）以及其他图形元素**
- 如果没有添加 `geom_point` 图层，初始情况下只会生成一个“空”的图表，但其坐标轴的比例已经根据 x 和 y 变量进行了调整
- **`geom_point()`** 是 ggplot2 提供的高级函数，用于快速添加散点图层。它是 `layer()` 的一个封装，内部实际上调用了 `layer()` 函数，但隐藏了许多默认设置，简化了使用。

## geom...()

通常，我们使用 `geom...()` 函数（有多种选择）来创建不同类型的几何对象，例如：`geom_bar()`、`geom_point()`、`geom_line()`、`geom_smooth()`、`geom_histogram()`、`geom_boxplot()`

```
# 我们还可以为图表添加其他图层，例如一条折线：
ggplot(countries11, aes(x = GDP, y = CO2emission)) + geom_point() + geom_line()

# 对（底层数据）进行对数变换
countries11$logGDP <- log(countries11$GDP)
countries11$logCO2emission <- log(countries11$CO2emission)

ggplot(countries11, aes(x = logGDP, y = logCO2emission)) + geom_point()

# 对数变换函数
ggplot(countries11, aes(x = GDP, y = CO2emission)) + geom_point() + scale_x_log10() + scale_y_log10()

# 颜色被全局更改
ggplot(countries11, aes(x = logGDP, y = logCO2emission)) + geom_point(col = "red")

# 颜色被分配给变量 `continent`
ggplot(countries11, aes(x = logGDP, y = logCO2emission)) + geom_point(aes(col = continent))
```
- **对 X 轴数据进行对数变换 ：scale_x_log10()**
- **对 Y 轴数据进行对数变换：scale_y_log10()**
#### 对数变换

对数变换（Log-transformation）是一种常见的数据处理方法，通常用于解决以下问题

### **1. 缩小数据范围，减少数值差异**

- 当数据的取值范围跨度很大（例如从几十到几百万），对数变换可以将数据压缩到一个更小的范围内，使其更容易观察和分析。
- 例如，GDP、收入、人口等通常呈现指数增长，对数变换可以将这些变量的增长趋势线性化。

---

### **2. 处理非对称分布，使数据更接近正态分布**

- 很多统计分析方法（如回归分析、假设检验）假设数据呈正态分布。
- 如果数据呈现右偏或左偏分布（长尾分布），对数变换可以减弱偏态，使数据分布更接近正态分布，从而满足统计模型的假设。

---

### **3. 解决异方差性问题**

- 异方差性（Heteroscedasticity）是指数据的方差随观测值的大小而变化。
- 对数变换可以稳定数据的方差，使其与观测值的大小无关，从而提高模型的鲁棒性和准确性。

---

### **4. 更容易解释指数增长或比例变化**

- 对数变换后，变量的变化可以用比例解释，而不是绝对值。例如：
    - 在对数坐标系下，指数增长表现为直线。
    - 对数变换后，变量的变化幅度（差距）可以被解释为“倍数”关系，而不是绝对差值。

---

### **5. 降低异常值的影响**

- 原始数据中的异常值可能对模型产生较大的影响。
- 对数变换通过压缩较大的值，减弱了异常值（比如极大值）的影响，提高了模型对数据的适应性。

### **适用场景**

以下情况下可以考虑对数变换：

1. **数据跨度较大**（如收入、人口、GDP）。
2. 数据分布**偏态或有长尾**。
3. 数据方差不稳定（异方差性）。
4. 数据包含指数增长模式。
5. 数据中存在较大的异常值。

---

### **注意事项**

- 对数变换要求数据为正值（`log(x)` 仅对 `x > 0` 定义），因此在数据中包含零或负值时需要特殊处理，例如使用 `log(x + 1)` 或 Box-Cox 变换。
- 不适合那些本身已经接近正态分布或不需要缩减范围的数据。


### GDP 和 CO2 排放相对于人口数量的关系

```
countries11$logGDP <- log(countries11$GDP/countries11$Population)
countries11$logCO2emission <- log(countries11$CO2emission/countries11$Population)

ggplot(countries11, aes(x = logGDP, y = logCO2emission)) + geom_point(aes(col = continent))

# 全局更改点的表示方式：
ggplot(countries11, aes(x = logGDP, y = logCO2emission)) + geom_point(aes(col = continent), pch = 20)

# 将点的表示分配给变量 `continent`（例如，用于色盲/色弱情况）
ggplot(countries11, aes(x = logGDP, y = logCO2emission)) + geom_point(aes(col = continent, pch = continent))

# 手动更改刻度通过 `scale_<type>_manual`
ggplot(countries11, aes(x = logGDP, y = logCO2emission)) + geom_point(aes(col = continent)) + scale_color_manual(
name = "Kontinent", values = c("red", "blue", "black", "brown", "grey"), labels = c("Afrika", "Amerika", "Asien", "Europa", "Ozeanien"))
```

- `pch` 参数控制点的形状，可以是以下类型：
    - **0-25**：基础图形中预定义的点形状（如空心方块、三角形、圆形等）。
    - 更大的点形状值可以被用于特定的符号和图案
- `scale_color_manual`：更改颜色刻度  
- `scale_x_manual`：更改 x 轴刻度，等等。

|`pch` 值|点形状|
|---|---|
|1|空心圆|
|2|空心三角形|
|3|加号|
|4|叉号|
|16|实心圆|
|17|实心三角形|
|20|小实心圆（默认）|

## ggplot2对象保存

```
vaccines <- readRDS("vaccs.Rds")
p_vaccs <- ggplot(vaccines, aes(x = date, y = dosen, col = impfstoff)) + geom_line()
p_vaccs

# 使用 ggplot2 创建的图形可以保存为 R 对象，并随后重新使用或修改
p_vaccs <- p_vaccs + facet_wrap(~region)
p_vaccs

# 可以通过更新数据（但保留图形的特定设置）来重复使用 ggplot 对象
# 抽取子数据集，类似SQL里面的select from ... where ...
vaccines_sub <- subset(vaccines, region %in% c("DE-SL", "DE-HB", "DE-HE", "DE-BE"))
# 给列的因子按照指定顺序重新排序并赋予名称
vaccines_sub$region <- factor(
  vaccines_sub$region,
  levels = c("DE-SL", "DE-HB", "DE-HE", "DE-BE"),
  labels = c("Saarland", "Bremen", "Hessen", "Berlin")
)
# p_vaccs图形样式上更换数据集（抽取过的数据集）
p_vaccs_sub <- p_vaccs %+% vaccines_sub
p_vaccs_sub

# 变更图形的对象，根据impfstoff列不同自动分配不同的linetype
p_vaccs_sub + aes(lty = impfstoff)

# 重复使用 ggplot 对象
p_vaccs_sub <- p_vaccs_sub +
  labs(
    y = "Verimpfte Dosen",
    x = "Datum",
    title = "Anzahl verimpfter Dosen",
    subtitle = "Saarland, Bremen, Hessen, Berlin",
    colour = "Impfstoff\nProduzent"
  )

p_vaccs_sub

# 修改主题，主题控制了图表的非数据元素，包括背景、网格线、文字样式、轴线等
p_vaccs_sub +
  theme(
    axis.text.x = element_text(size = rel(1.2)),  # 调整 X 轴刻度文字大小，比默认值放大 1.2 倍
    axis.ticks = element_line(colour = "red", linewidth = 2),  # 设置轴刻度线为红色，线宽为 2
    strip.background = element_rect(fill = "steelblue")  # 设置分面标题背景为钢蓝色
  )

# 修改主题中的图例属性
p_vaccs_sub + theme( 
	legend.position = "bottom" , 
	legend.text = element_text(face = "italic"), 
	legend.background = element_rect(fill = "steelblue")
)
```

**`facet_wrap(~region)`**:
- `facet_wrap` 是 ggplot2 的一个分面函数，用于根据一个变量的不同值生成多个子图。
- `~region` 表示将数据中的 `region` 列作为分面变量。
    - 每个 `region` 的值都会生成一个单独的子图。
    - 子图会按照网格形式排列。
	- `~` 在 R 中主要用于指定变量之间的关系，通常表示 "依赖于" 或 "与...相关" 的意思
**`subset()` 函数**:
- 用于从数据框中筛选出满足特定条件的子集。
- 语法：**`subset(data, condition)`**，其中：
	- `data` 是要筛选的数据集。
    - `condition` 是筛选的条件。
**`region %in% c("DE-SL", "DE-HB", "DE-HE", "DE-BE")`**:
- 筛选条件：检查 `region` 列中的值是否属于 `c("DE-SL", "DE-HB", "DE-HE", "DE-BE")`。
- `%in%` 是一个 R 运算符，用于判断某个值是否存在于一个向量中，如果 `region` 的值属于向量 `c("DE-SL", "DE-HB", "DE-HE", "DE-BE")`，则该行会被保留。
**`vaccines_sub`**:
-  筛选后的子集数据框被赋值给 `vaccines_sub`。
-  它是原始数据集 `vaccines` 的一个子集，只包含满足条件的行。
**`%in%`**：
- 是 R 中的一个逻辑运算符，用于判断一个值或一组值是否存在于另一个向量中。它返回布尔值（`TRUE` 或 `FALSE`）
**`%+%`**：
- 是一个特殊的操作符，用于将一个新的数据集替换到现有的 ggplot 对象中，而保留原图形的所有图层、主题和设置
**`aes(lty = impfstoff)`**:
- 添加了一个新的美学映射，将变量 `impfstoff` 映射为线型（`lty`）。
- **`lty`（line type）** 是 ggplot2 中用于控制线条样式的美学属性，例如实线、虚线、点线等
**`labs()`** 
- 是一个用来设置或修改图表标签的函数。它的全称是 **labels**，可以用来调整标题、副标题、轴标签、图例标题等内容。通过 `labs()`，你可以让图表更加清晰和具有解释性
- 常用有：
	- x - X轴标题
	- y - Y轴标题
	- title - 图表标题
	- subtitle - 图表副标题
	- 图例标题 - colour
**`\n`**：
	代表字符串换行

## 修改主题

以下是可以通过 **theme** 修改的元素的概览：
- **文本元素**（例如刻度标签、标题等）通过 **`element_text()`** 进行设置。  
    （例如：`tick labels`, `titles` 等）
- **线条元素通过** **`element_line()`** 进行设置。  
    （例如：`geom_line`, `geom_point` 等）
- **对于生成图例的标度**（例如颜色、形状），显示内容是通过相应的 **`scales`** 控制的。  
    （例如：`scale_color`, `scale_shape`）
- 然而，图例中显示的元素样式是通过 **theme** 来控制的。
- **`element_rect`** 是 ggplot2 中用于设置矩形元素样式的函数，常用于修改背景、边框等矩形区域的外观。它可以设置颜色、填充、边框宽度等属性。
- **axis**：轴的显示（例如 `axis.title`、`axis.ticks`、`axis.x.ticks` 等）。
- **legend**：图例的显示（例如 `legend.title`）。
- **panel**：图表的“内部”窗口，用于显示数据（位于坐标系内，例如 `panel.grid`、`panel.background`）。
- **plot**：图表的“外部”窗口，用于显示图表的所有元素（例如 `plot.title`、`plot.title.position`、`plot.background`）。
- **strip**：子图的文本框（例如应用 `facet_wrap/facet_grid` 后的文本框）。
- **注意：不要过度调整！ 通常情况下，使用 `theme_bw()` 就足够了。**

```
p_vaccs_sub + theme_bw()
```

## 轴限与子图

使用 `xlim` 和 `ylim` 用来直接设置 x 轴和 y 轴的范围，但是，会移除超出范围的观测值。  这可能会影响基于数据计算的几何图形（geoms），例如：`geom_boxplot`、`geom_smooth` 等。

```
# 基础图表：创建散点图并添加平滑线和参考线
p_iris <- ggplot(iris, aes(x = Petal.Length, y = Petal.Width)) +
  geom_point() +                                   # 添加散点
  geom_smooth(method = "lm", se = FALSE) +         # 添加线性回归平滑线，无置信区间
  geom_vline(xintercept = 2, lty = 2) +            # 添加垂直虚线 x = 2
  geom_hline(yintercept = 0.5, lty = 2)            # 添加水平虚线 y = 0.5

# 使用 xlim 和 ylim 设置范围
p_xylim <- p_iris +
  xlim(c(1, 2)) +                                  # 限制 x 轴范围为 [1, 2]
  ylim(c(0, 1))                                    # 限制 y 轴范围为 [0, 1]

# 使用 coord_cartesian 限制显示范围，不移除数据
p_coord <- p_iris +
  coord_cartesian(xlim = c(1, 2), ylim = c(0, 1))  # 缩放视图范围
```

### **`coord_cartesian()`**
	`coord_cartesian()` 是 ggplot2 中用于设置图表显示范围的函数。与 `xlim()` 和 `ylim()` 不同，它不会移除超出范围的观测值，只是调整了显示区域。


## 图形组合与 `patchwork`

R 包 **`patchwork`** 是 ggplot2 的扩展，能够方便地将多个 ggplot 对象组合到一个图形中。  

这在需要展示不同类型的可视化时非常有用。  
相比之下，`facet_wrap` 和 `facet_grid` 仅限于根据组（通常是分类变量的不同取值）对同一类型的图形进行分面展示。


