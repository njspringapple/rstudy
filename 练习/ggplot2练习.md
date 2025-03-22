
### 题目1 - 基础条形图与定制

**数据集**：内置的 `mpg` 数据集。

**可视化要求**：

1. 创建一个条形图，展示不同制造商(manufacturer)的汽车数量
2. 按照数量从高到低排序条形
3. 为条形添加不同的填充颜色
4. 添加数据标签，显示每个制造商的具体车型数量
5. 设置适当的标题、坐标轴标签和主题

**需要实现的代码**：

### 题目2 - 分组箱线图

**数据集**：内置的 `iris` 数据集。

**可视化要求**：

1. 创建一个分组箱线图，比较三种鸢尾花种类(Species)的花瓣长度(Petal.Length)
2. 为每个种类的箱线图使用不同颜色
3. 在箱线图上叠加实际数据点，使用抖动效果(jitter)避免重叠
4. 添加均值点，用不同形状标识
5. 自定义主题，移除网格线并设置背景色

**需要实现的代码**：

### 题目1 - 多组柱状图对比

**数据集**：从以下代码创建的销售数据


```
sales_data <- data.frame(
  product = rep(c("产品A", "产品B", "产品C", "产品D"), each = 3),
  region = rep(c("北区", "中区", "南区"), times = 4),
  sales = c(125, 105, 90, 85, 100, 75, 130, 120, 95, 110, 115, 105)
)
```

**可视化要求**：

1. 创建一个分组柱状图，展示不同产品在各区域的销售量
2. 使用不同颜色区分不同区域
3. 在柱状图上添加数据标签，显示具体销售数值
4. 添加图例，并将其放置在图表底部
5. 为图表添加标题、坐标轴标签和合适的主题

**需要实现的代码**：

### 题目2 - 并列箱线图与散点叠加

**数据集**：内置的 `ToothGrowth` 数据集。

**可视化要求**：

1. 创建一个箱线图，比较不同维生素C剂量(dose)下牙齿生长长度(len)的分布
2. 将原始数据点叠加在箱线图上，并使用抖动效果(jitter)避免重叠
3. 按给药方式(supp)分面
4. 使用不同颜色区分不同剂量
5. 添加均值点，并用特殊形状标识
6. 设置有意义的标题和标签

**需要实现的代码**：

### 题目3 - 多序列折线图

**数据集**：从以下代码创建的时间序列数据


```
# 创建月度销售数据
set.seed(123)
dates <- seq(as.Date("2022-01-01"), as.Date("2022-12-01"), by = "month")
products <- c("笔记本电脑", "智能手机", "平板电脑", "智能手表")

sales_ts <- expand.grid(date = dates, product = products)
sales_ts$sales <- c(
  # 笔记本电脑销售趋势
  120, 115, 118, 125, 135, 145, 155, 165, 160, 150, 170, 190,
  # 智能手机销售趋势
  200, 195, 210, 215, 225, 240, 260, 255, 245, 250, 270, 290,
  # 平板电脑销售趋势
  90, 85, 80, 95, 100, 110, 105, 115, 120, 125, 130, 140,
  # 智能手表销售趋势
  50, 55, 60, 65, 75, 85, 95, 100, 90, 95, 105, 115
)
```

**可视化要求**：

1. 创建一个多序列折线图，展示各产品的月度销售趋势
2. 使用不同颜色和线型区分不同产品
3. 添加点标记，突出每个月的具体数据点
4. 为图表添加网格线，便于读取数值
5. 自定义x轴日期标签格式，只显示月份
6. 添加图例、标题和坐标轴标签

**需要实现的代码**：

### 题目4 - 堆叠与分组柱状图对比

**数据集**：从以下代码创建的销售数据


```
quarterly_data <- data.frame(
  quarter = rep(c("Q1", "Q2", "Q3", "Q4"), each = 3),
  department = rep(c("销售部", "市场部", "研发部"), times = 4),
  revenue = c(150, 100, 80, 170, 120, 90, 140, 130, 100, 190, 150, 120)
)
```

**可视化要求**：

1. 创建两个并排的柱状图：一个堆叠柱状图和一个分组柱状图
2. 展示各部门在不同季度的收入
3. 使用相同的颜色方案，便于对比
4. 为堆叠柱状图添加百分比标签，显示每个部门占该季度总收入的比例
5. 为分组柱状图添加数值标签
6. 添加适当的标题、标签和图例

**需要实现的代码**：

### 题目5 - 带置信区间的多组折线图

**数据集**：从以下代码创建的实验数据


```
set.seed(42)
# 创建三组实验数据，每组5个时间点，每个时间点有10次观测
time_points <- 1:5
groups <- c("实验组A", "实验组B", "对照组")

experiment_data <- expand.grid(
  time = time_points,
  group = groups,
  replicate = 1:10
)

# 设置不同组的趋势和方差
experiment_data$value <- case_when(
  experiment_data$group == "实验组A" ~ 20 + 5 * experiment_data$time + rnorm(nrow(experiment_data), 0, 3),
  experiment_data$group == "实验组B" ~ 20 + 3 * experiment_data$time + rnorm(nrow(experiment_data), 0, 2),
  experiment_data$group == "对照组" ~ 20 + 1 * experiment_data$time + rnorm(nrow(experiment_data), 0, 2)
)
```

**可视化要求**：

1. 创建一个折线图，展示三个组在不同时间点的平均值变化
2. 添加95%置信区间带
3. 使用不同颜色和线型区分不同组
4. 添加散点表示原始数据，使用透明度避免重叠
5. 使用虚线标记一个重要的参考值（如y=35）
6. 添加文本注释，解释图表中的关键发现

**需要实现的代码**：

### 题目6 - 复合箱线图与小提琴图

**数据集**：内置的 `iris` 数据集。

**可视化要求**：

1. 创建一个组合图表，同时显示箱线图和小提琴图，展示三种鸢尾花种类的花瓣长度(Petal.Length)分布
2. 箱线图放置在小提琴图内部
3. 使用不同颜色区分不同种类
4. 在小提琴图上叠加显示原始数据点
5. 添加均值和中位数标记
6. 设置合适的标题、标签和图例

**需要实现的代码**：

### 题目7 - 多变量柱状图

**数据集**：从以下代码创建的学生成绩数据


```
student_scores <- data.frame(
  class = rep(c("一班", "二班", "三班", "四班"), each = 3),
  subject = rep(c("数学", "语文", "英语"), times = 4),
  avg_score = c(85, 82, 78, 88, 85, 82, 76, 80, 75, 90, 87, 85),
  pass_rate = c(0.92, 0.95, 0.90, 0.94, 0.97, 0.93, 0.85, 0.88, 0.82, 0.96, 0.94, 0.91)
)
```

**可视化要求**：

1. 创建一个柱状图，展示各班级在不同科目的平均分
2. 使用柱子的填充颜色表示科目
3. 使用柱子的边框颜色表示及格率：设置一个颜色渐变，从低及格率(红色)到高及格率(绿色)
4. 添加数据标签，显示具体的平均分
5. 添加次坐标轴，展示及格率数据
6. 添加图例和标题

**需要实现的代码**：

### 题目8 - 气泡图与折线图组合

**数据集**：从以下代码创建的公司业绩数据



```
companies <- LETTERS[1:6]  # 六家公司
years <- 2018:2022  # 五年数据

company_data <- expand.grid(company = companies, year = years)
set.seed(123)

company_data$revenue <- case_when(
  company_data$company == "A" ~ 100 + 15 * (company_data$year - 2018) + rnorm(5, 0, 5),
  company_data$company == "B" ~ 120 + 10 * (company_data$year - 2018) + rnorm(5, 0, 7),
  company_data$company == "C" ~ 90 + 20 * (company_data$year - 2018) + rnorm(5, 0, 8),
  company_data$company == "D" ~ 110 + 5 * (company_data$year - 2018) + rnorm(5, 0, 6),
  company_data$company == "E" ~ 95 + 8 * (company_data$year - 2018) + rnorm(5, 0, 4),
  company_data$company == "F" ~ 105 + 12 * (company_data$year - 2018) + rnorm(5, 0, 9)
)

company_data$profit <- company_data$revenue * (runif(nrow(company_data), 0.05, 0.25))
company_data$employees <- case_when(
  company_data$company == "A" ~ 500 + 50 * (company_data$year - 2018) + rnorm(5, 0, 20),
  company_data$company == "B" ~ 600 + 30 * (company_data$year - 2018) + rnorm(5, 0, 25),
  company_data$company == "C" ~ 450 + 60 * (company_data$year - 2018) + rnorm(5, 0, 30),
  company_data$company == "D" ~ 550 + 20 * (company_data$year - 2018) + rnorm(5, 0, 15),
  company_data$company == "E" ~ 400 + 40 * (company_data$year - 2018) + rnorm(5, 0, 10),
  company_data$company == "F" ~ 480 + 35 * (company_data$year - 2018) + rnorm(5, 0, 25)
)
```

**可视化要求**：

1. 创建一个组合图表：用折线连接的气泡图
2. x轴为年份，y轴为收入(revenue)
3. 气泡大小表示员工数量(employees)
4. 气泡颜色表示利润(profit)
5. 折线用于连接同一公司不同年份的数据点
6. 为最新一年(2022)的数据点添加公司标签
7. 添加趋势线，显示行业平均收入趋势

**需要实现的代码**：

### 题目9 - 多面板箱线图

**数据集**：从以下代码创建的健康指标数据


```
set.seed(42)
age_groups <- c("18-24岁", "25-34岁", "35-44岁", "45-54岁", "55-64岁", "65岁以上")
genders <- c("男性", "女性")
health_metrics <- c("血压收缩压", "血压舒张压", "血糖", "总胆固醇")

health_data <- expand.grid(
  age_group = factor(age_groups, levels = age_groups),
  gender = genders,
  metric = health_metrics,
  id = 1:10  # 每组10个样本
)

# 为不同指标设置不同的基线值和变化趋势
health_data$value <- case_when(
  health_data$metric == "血压收缩压" ~ 
    110 + as.integer(health_data$age_group) * 3 + 
    ifelse(health_data$gender == "男性", 5, 0) + rnorm(nrow(health_data), 0, 5),
  health_data$metric == "血压舒张压" ~ 
    70 + as.integer(health_data$age_group) * 1.5 + 
    ifelse(health_data$gender == "男性", 3, 0) + rnorm(nrow(health_data), 0, 3),
  health_data$metric == "血糖" ~ 
    5 + as.integer(health_data$age_group) * 0.1 + 
    rnorm(nrow(health_data), 0, 0.3),
  health_data$metric == "总胆固醇" ~ 
    4 + as.integer(health_data$age_group) * 0.15 + 
    ifelse(health_data$gender == "男性", -0.1, 0.1) + rnorm(nrow(health_data), 0, 0.4)
)
```

**可视化要求**：

1. 创建一个多面板箱线图，每个健康指标一个面板
2. x轴为年龄组，y轴为测量值
3. 按性别给箱线图着不同颜色
4. 添加参考线，表示每个指标的标准范围
5. 使用不同的填充样式区分不同性别
6. 添加标题、坐标轴标签和图例

**需要实现的代码**：

### 题目10 - 分组堆叠柱状图

**数据集**：从以下代码创建的能源生产数据


```
countries <- c("中国", "美国", "德国", "印度", "巴西")
years <- c(2010, 2015, 2020)
energy_types <- c("煤炭", "石油", "天然气", "核能", "可再生能源")

energy_data <- expand.grid(
  country = countries,
  year = years,
  energy_type = energy_types
)

set.seed(123)
# 设置不同国家不同能源类型的基础生产量和增长率
energy_data$production <- case_when(
  # 中国能源生产情况
  energy_data$country == "中国" & energy_data$energy_type == "煤炭" ~ 
    1500 + 100 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 50),
  energy_data$country == "中国" & energy_data$energy_type == "石油" ~ 
    600 + 30 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 20),
  energy_data$country == "中国" & energy_data$energy_type == "天然气" ~ 
    300 + 50 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 15),
  energy_data$country == "中国" & energy_data$energy_type == "核能" ~ 
    100 + 40 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 10),
  energy_data$country == "中国" & energy_data$energy_type == "可再生能源" ~ 
    200 + 120 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 30),
  
  # 美国能源生产情况
  energy_data$country == "美国" & energy_data$energy_type == "煤炭" ~ 
    900 - 50 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 40),
  energy_data$country == "美国" & energy_data$energy_type == "石油" ~ 
    800 + 60 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 30),
  energy_data$country == "美国" & energy_data$energy_type == "天然气" ~ 
    700 + 80 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 25),
  energy_data$country == "美国" & energy_data$energy_type == "核能" ~ 
    300 + 10 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 15),
  energy_data$country == "美国" & energy_data$energy_type == "可再生能源" ~ 
    300 + 100 * (energy_data$year - 2010) / 5 + rnorm(1, 0, 35),
  
  # 其他国家根据类似逻辑设置能源生产数据
  TRUE ~ runif(1, 100, 800)
)
```

**可视化要求**：

1. 创建一个分组堆叠柱状图，展示不同国家在不同年份的能源结构
2. x轴分组为年份，每组内按国家划分
3. 使用不同的填充颜色表示不同能源类型
4. 为每个能源类型添加标签，显示所占百分比
5. 添加图例、标题和坐标轴标签
6. 添加注释，突出可再生能源增长最快的国家

### 题目3 - 多面板热图

**数据集**：从以下代码创建的数据框


```
set.seed(123)
months <- rep(month.abb, 3)  # 1月到12月重复3次
years <- rep(c("2021", "2022", "2023"), each = 12)  # 每年12个月
values <- runif(36, 10, 100)  # 36个随机值

heatmap_data <- data.frame(
  month = factor(months, levels = month.abb),
  year = factor(years),
  value = values
)
```

**可视化要求**：

1. 创建一个热图，展示不同年份不同月份的数值
2. x轴为月份，y轴为年份，使用颜色表示数值大小
3. 使用合适的颜色渐变(如从蓝到红)
4. 在每个单元格中添加数值标签，保留一位小数
5. 添加适当的图例和标题

**需要实现的代码**：

### 题目4 - 高级散点图与回归

**数据集**：内置的 `diamonds` 数据集。

**可视化要求**：

1. 创建一个散点图，展示钻石克拉数(carat)与价格(price)的关系
2. 使用颜色表示钻石切工质量(cut)
3. 使用点的大小表示钻石净度(clarity)
4. 对不同切工质量添加单独的回归线，并添加95%置信区间
5. 对x轴和y轴应用对数变换
6. 添加适当的图例和注释

**需要实现的代码**：

### 题目5 - 时间序列可视化

**数据集**：内置的 `economics` 数据集。

**可视化要求**：

1. 创建一个多线图，在同一图表上展示失业人数(unemploy)和个人储蓄率(psavert)的变化趋势
2. 使用左y轴表示失业人数，右y轴表示储蓄率
3. 使用不同颜色和线型区分两个变量
4. 添加垂直参考线标记重要经济事件(如2008年金融危机)
5. 自定义x轴日期格式，使其更易读
6. 添加适当的图例、标题和标签

**需要实现的代码**：

### 题目6 - 密度图与直方图叠加

**数据集**：内置的 `faithful` 数据集(包含黄石公园老忠实间歇泉的喷发数据)。

**可视化要求**：

1. 创建一个叠加了密度曲线的直方图，展示间歇泉喷发持续时间(eruptions)的分布
2. 调整直方图的填充颜色和透明度
3. 为密度曲线设置不同的颜色和线宽
4. 添加垂直线标记均值
5. 使用facet将数据按照等待时间(waiting)的高低分成两组进行对比(提示：可以使用中位数作为分界点)

**需要实现的代码**：

### 题目7 - 棘轮图(Radial Bar Chart)

**数据集**：从以下代码创建的数据框


```
wind_data <- data.frame(
  direction = c("N", "NNE", "NE", "ENE", "E", "ESE", "SE", "SSE", 
                "S", "SSW", "SW", "WSW", "W", "WNW", "NW", "NNW"),
  frequency = c(8, 5, 7, 3, 6, 4, 9, 12, 14, 16, 10, 7, 5, 4, 8, 6)
)
```

**可视化要求**：

1. 创建一个棘轮图(也称为极坐标条形图)，展示不同风向的频率
2. 调整填充颜色，可以使用渐变色来表示频率大小
3. 设置适当的标签和图例
4. 调整极坐标轴的刻度和网格线
5. 添加标题和数据来源注释

**需要实现的代码**：

### 题目8 - 交互式地图可视化

**数据集**：内置的 `maps` 包中的 `world` 数据集和以下自定义数据


```
library(maps)
library(dplyr)

# 创建一些国家的GDP数据
country_data <- data.frame(
  country = c("USA", "China", "Japan", "Germany", "India", "UK", "France", "Italy", "Brazil", "Canada"),
  gdp = c(21.4, 14.7, 5.1, 3.8, 2.9, 2.7, 2.6, 2.0, 1.8, 1.6),
  continent = c("North America", "Asia", "Asia", "Europe", "Asia", "Europe", "Europe", "Europe", "South America", "North America")
)
```

**可视化要求**：

1. 创建一个世界地图，只显示country_data中包含的国家
2. 使用填充色表示GDP大小
3. 按大洲(continent)为地图面板分面
4. 添加国家名称标签
5. 使用适当的投影和主题
6. 添加标题和数据来源注释

**需要实现的代码**：

### 题目9 - 复合图表与注释

**数据集**：内置的 `gapminder` 包中的 `gapminder` 数据集(需安装)。


```
# 安装并加载gapminder包
# install.packages("gapminder")
library(gapminder)
data(gapminder)

# 筛选2007年数据
gap_2007 <- gapminder %>% 
  filter(year == 2007)
```

**可视化要求**：

1. 创建一个散点图，展示人均GDP(gdpPercap)和预期寿命(lifeExp)的关系
2. 使用点的大小表示人口(pop)
3. 使用颜色表示大洲(continent)
4. 对GDP应用对数变换
5. 添加注释，标出一些特定国家(如美国、中国、印度)
6. 添加趋势线及置信区间
7. 添加标题、副标题和坐标轴标签

**需要实现的代码**：

### 题目10 - 动画图表

**数据集**：内置的 `gapminder` 包中的 `gapminder` 数据集。

**可视化要求**：

1. 使用 `gganimate` 包创建一个动画散点图，展示1952年至2007年间各国人均GDP与预期寿命的变化
2. 按照大洲着色，使用点的大小表示人口
3. 添加标记特定国家的文本标签
4. 设置适当的动画过渡效果
5. 添加标题和字幕，显示当前年份
6. 自定义主题和调色板

**需要实现的代码**：

```
# 需要安装gganimate包
# install.packages("gganimate")
```

### 题目11 - 复杂树状图

**数据集**：从以下代码创建的分层数据



```
library(data.tree)
library(ggplot2)
library(dplyr)

# 创建分层数据
org_data <- data.frame(
  pathString = c(
    "Company",
    "Company/Administration",
    "Company/Administration/HR",
    "Company/Administration/Finance",
    "Company/Operations",
    "Company/Operations/Production",
    "Company/Operations/Logistics",
    "Company/Sales",
    "Company/Sales/Domestic",
    "Company/Sales/International",
    "Company/R&D",
    "Company/R&D/Research",
    "Company/R&D/Development"
  ),
  size = c(100, 20, 8, 12, 40, 25, 15, 30, 12, 18, 10, 4, 6)
)

# 转换为tree对象
org_tree <- as.Node(org_data)
```

**可视化要求**：

1. 使用 `ggraph` 包创建一个树状图(也可以使用其他适合的方法)
2. 使用节点大小表示部门规模
3. 添加节点标签，显示部门名称
4. 设置合适的配色和主题
5. 添加图表标题和说明

**需要实现的代码**：


```
# 需要安装ggraph包
# install.packages("ggraph")
```

### 题目12 - 多变量关系图矩阵

**数据集**：内置的 `mtcars` 数据集。

**可视化要求**：

1. 使用 `GGally` 包的 `ggpairs` 函数创建一个散点图矩阵，展示 mpg, disp, hp, wt 之间的关系
2. 按汽缸数(cyl)对点进行着色
3. 在对角线上显示各变量的分布直方图
4. 在上三角显示相关系数，下三角显示散点图和拟合线
5. 自定义颜色和主题

**需要实现的代码**：


```
# 需要安装GGally包
# install.packages("GGally")
```

### 题目13 - 复杂玫瑰图

**数据集**：从以下代码创建的季节性数据


```
seasons_data <- data.frame(
  month = factor(month.abb, levels = month.abb),
  rainfall = c(45, 40, 35, 30, 25, 15, 10, 15, 20, 30, 35, 40),
  temperature = c(5, 7, 10, 15, 20, 25, 28, 27, 23, 18, 12, 7),
  season = c(rep("Winter", 2), rep("Spring", 3), rep("Summer", 3), rep("Autumn", 3), "Winter")
)
```

**可视化要求**：

1. 创建一个复合玫瑰图(polar coordinate bar chart)，展示每月的降雨量
2. 使用填充色按季节分组
3. 在同一图表上添加温度变化的线图
4. 自定义极坐标的刻度和网格
5. 添加适当的标签、图例和标题

**需要实现的代码**：

### 题目14 - 高级箱线图与小提琴图组合

**数据集**：内置的 `ToothGrowth` 数据集。

**可视化要求**：

1. 创建一个结合了箱线图和小提琴图的组合图，展示不同维生素C剂量(dose)和补充类型(supp)对牙齿生长长度(len)的影响
2. 在小提琴图内部添加箱线图
3. 添加原始数据点，使用抖动效果避免重叠
4. J检索配色方案，为两种补充类型使用对比色
5. 添加统计比较注释，标记显著性差异
6. 添加适当的标题、轴标签和图例

**需要实现的代码**：

### 题目15 - 交互式网络图

**数据集**：从以下代码创建的社交网络数据


```
# 创建节点数据
nodes <- data.frame(
  id = 1:10,
  label = paste("Person", 1:10),
  group = c(rep("A", 3), rep("B", 4), rep("C", 3)),
  size = sample(10:30, 10)
)

# 创建边数据
set.seed(42)
edges <- data.frame(
  from = sample(1:10, 15, replace = TRUE),
  to = sample(1:10, 15, replace = TRUE),
  weight = sample(1:10, 15, replace = TRUE)
)
# 删除自环
edges <- edges[edges$from != edges$to, ]
```

**可视化要求**：

1. 使用 `ggraph` 或 `igraph` 创建一个网络图，展示个体之间的关系
2. 节点大小表示个体影响力，颜色表示所属群组
3. 边的粗细表示关系强度
4. 添加节点标签
5. 使用适当的布局算法(如"fruchterman.reingold")
6. 添加图表标题和图例

**需要实现的代码**：


```
# 需要安装igraph和ggraph包
# install.packages(c("igraph", "ggraph"))
```

### 题目16 - 定制化沃罗诺伊图(Voronoi Diagram)

**数据集**：从以下代码创建的空间数据

```
set.seed(42)
points_data <- data.frame(
  x = runif(20, 0, 10),
  y = runif(20, 0, 10),
  category = sample(LETTERS[1:5], 20, replace = TRUE),
  value = rnorm(20, 100, 20)
)
```

**可视化要求**：

1. 使用 `ggvoronoi` 包创建一个沃罗诺伊图，展示空间区域划分
2. 使用填充色表示区域类别(category)
3. 使用点的大小表示区域值(value)
4. 添加区域边界线，并设置适当的线型和颜色
5. 添加坐标网格和标签
6. 使用自定义调色板

**需要实现的代码**：


```
# 需要安装ggvoronoi包
# install.packages("ggvoronoi")
```

### 题目17 - 复杂雷达图

**数据集**：从以下代码创建的产品评分数据


```
products <- c("Product A", "Product B", "Product C", "Product D")
attributes <- c("Quality", "Price", "Design", "Usability", "Performance", "Reliability")

# 创建评分矩阵
set.seed(123)
ratings <- matrix(runif(length(products) * length(attributes), 2, 10), 
                 nrow = length(products))
ratings <- round(ratings, 1)

# 转换为数据框
ratings_df <- data.frame(product = products)
for (i in 1:length(attributes)) {
  ratings_df[[attributes[i]]] <- ratings[, i]
}
```

**可视化要求**：

1. 创建一个雷达图(也称为蜘蛛网图或星图)，比较不同产品在各属性上的评分
2. 使用不同颜色和线型区分产品
3. 添加网格线和刻度
4. 在各轴上显示属性名称
5. 添加图例和标题

**需要实现的代码**：


```
# 可以使用ggradar包
# install.packages("ggradar")
```

### 题目18 - 高级热力地图

**数据集**：从以下代码创建的时间序列数据


```
# 创建时间序列数据
set.seed(123)
dates <- seq(as.Date("2022-01-01"), as.Date("2022-12-31"), by = "day")
hours <- 0:23

# 为每天的每小时创建数据
time_data <- expand.grid(date = dates, hour = hours)
time_data$value <- rnorm(nrow(time_data), 
                        mean = 15 + 10 * sin(2 * pi * (time_data$hour - 6) / 24), 
                        sd = 3)
time_data$month <- format(time_data$date, "%b")
time_data$day <- as.numeric(format(time_data$date, "%d"))
time_data$weekday <- factor(weekdays(time_data$date), 
                           levels = c("Monday", "Tuesday", "Wednesday", 
                                     "Thursday", "Friday", "Saturday", "Sunday"))
```

**可视化要求**：

1. 创建一个热力地图，展示一年中每天每小时的数值变化
2. x轴为小时，y轴为日期
3. 使用合适的颜色渐变表示数值
4. 按月份分面
5. 添加适当的标题、坐标轴标签和图例
6. 优化图表布局，使其易于阅读

**需要实现的代码**：

### 题目19 - 复合词云图

**数据集**：从以下代码创建的文本数据


```
library(dplyr)

text_data <- data.frame(
  category = rep(c("Technology", "Environment", "Politics", "Entertainment"), each = 10),
  word = c(
    # 技术类词汇
    "AI", "Data", "Innovation", "Digital", "Software", "Hardware", "Cloud", "Blockchain", "Algorithm", "Automation",
    # 环境类词汇
    "Sustainability", "Climate", "Renewable", "Conservation", "Ecosystem", "Biodiversity", "Green", "Pollution", "Carbon", "Energy",
    # 政治类词汇
    "Democracy", "Policy", "Government", "Election", "Rights", "Reform", "Legislation", "Diplomacy", "Constitution", "Parliament",
    # 娱乐类词汇
    "Cinema", "Music", "Celebrity", "Festival", "Performance", "Media", "Streaming", "Concert", "Award", "Entertainment"
  ),
  frequency = sample(10:100, 40)
)
```

**可视化要求**：

1. 使用 `ggwordcloud` 包创建一个分面词云图，每个类别一个词云
2. 词的大小与其频率成正比
3. 为不同类别使用不同配色方案
4. 设置适当的词云形状和排列
5. 添加标题和类别标签

**需要实现的代码**：


```
# 需要安装ggwordcloud包
# install.packages("ggwordcloud")
```

### 题目20 - 综合仪表板

**数据集**：内置的 `diamonds` 数据集。

**可视化要求**：

1. 创建一个由多个图表组成的综合仪表板，使用 `patchwork` 或 `gridExtra` 包
2. 包含以下图表：
    - 钻石价格分布的直方图
    - 克拉数与价格关系的散点图
    - 不同切工质量的钻石数量条形图
    - 钻石价格按切工和颜色的热图
3. 设置统一的主题和颜色方案
4. 添加整体标题和各子图标题
5. 优化布局和图例位置

**需要实现的代码**：


```
# 需要安装patchwork包
# install.packages("patchwork")
```