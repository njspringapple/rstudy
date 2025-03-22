
## 题目一：Diamonds数据集分析

**题目描述：**  
使用diamonds数据集，分析钻石的切工(cut)、颜色(color)和价格(price)之间的关系。

**要求：**

1. 使用dplyr筛选克拉数(carat)大于1的钻石
2. 将color按照从D到J的顺序处理为有序因子
3. 计算每种切工和颜色组合的平均价格
4. 创建一个分面箱线图，展示不同颜色和切工的钻石价格分布

**参考答案：**

```r
library(dplyr)
library(ggplot2)

# 1. 筛选carat > 1的钻石
large_diamonds <- diamonds %>% 
  filter(carat > 1)

# 2. 将color处理为有序因子
large_diamonds <- large_diamonds %>%
  mutate(color = factor(color, levels = c("D", "E", "F", "G", "H", "I", "J"), ordered = TRUE))

# 3. 计算每种切工和颜色组合的平均价格
avg_prices <- large_diamonds %>%
  group_by(cut, color) %>%
  summarize(avg_price = mean(price), .groups = "drop")

# 4. 创建分面箱线图
ggplot(large_diamonds, aes(x = color, y = price, fill = cut)) +
  geom_boxplot() +
  facet_wrap(~ cut) +
  scale_y_log10() +
  labs(title = "钻石价格分布：按切工和颜色",
       x = "颜色（D最好，J最差）",
       y = "价格（对数刻度）",
       fill = "切工") +
  theme_minimal()
```

## 题目二：Economics数据集的分布分析

**题目描述：**  
使用economics数据集，通过直方图和频率图分析美国经济指标的分布特征。

**要求：**

1. 创建失业率的直方图，分析其分布特征
2. 创建个人储蓄率的密度图，比较不同年代的分布
3. 创建消费支出的频率多边形图，观察其变化趋势

**参考答案：**

```r
# 加载必要的包
library(ggplot2)
library(dplyr)

# 数据准备
econ_data <- economics %>%
  # 计算失业率
  mutate(
    unemployment_rate = (unemploy / pop) * 100,
    # 添加年代分类
    decade = case_when(
      date < as.Date("1980-01-01") ~ "70年代",
      date < as.Date("1990-01-01") ~ "80年代",
      date < as.Date("2000-01-01") ~ "90年代", 
      date < as.Date("2010-01-01") ~ "00年代",
      TRUE ~ "10年代"
    )
  )

#----- 1. 失业率直方图 -----#
ggplot(econ_data, aes(x = unemployment_rate)) +
  # 创建直方图
  geom_histogram(binwidth = 0.5, fill = "skyblue", color = "white") +
  # 添加平均线
  geom_vline(aes(xintercept = mean(unemployment_rate)), 
             color = "red", linetype = "dashed", size = 1) +
  labs(
    title = "美国失业率分布 (1967-2015)",
    subtitle = "红线表示平均失业率",
    x = "失业率(%)",
    y = "频数"
  ) +
  theme_minimal()

#----- 2. 个人储蓄率密度图（按年代比较）-----#
ggplot(econ_data, aes(x = psavert, fill = decade)) +
  # 创建密度图
  geom_density(alpha = 0.5) +
  labs(
    title = "不同年代美国个人储蓄率分布",
    x = "个人储蓄率(%)",
    y = "密度",
    fill = "年代"
  ) +
  theme_minimal()

#----- 3. 消费支出频率多边形图 -----#
ggplot(econ_data, aes(x = pce)) +
  # 创建频率多边形图
  geom_freqpoly(binwidth = 500, size = 1, color = "darkgreen") +
  # 使用适当的刻度格式
  scale_x_continuous(labels = function(x) paste0(x/1000, "K")) +
  labs(
    title = "美国个人消费支出分布",
    x = "个人消费支出(十亿美元)",
    y = "频数"
  ) +
  theme_minimal()
```

## 为什么Economics数据集适合这些图表：

### 1. 直方图的优势

- **展示整体分布形态**：失业率的直方图可以直观显示其分布是偏态还是正态
- **识别集中趋势和异常**：可以清晰看出失业率的常见范围和极端值
- **对比不同经济时期**：通过分组或叠加直方图，可比较不同时期的失业情况

### 2. 密度图的适用性

- **平滑展示分布**：相比直方图更为平滑，适合展示储蓄率等连续变量的分布形态
- **多组比较**：特别适合比较不同年代的储蓄行为变化
- **识别模态特征**：可以显示是单峰还是多峰分布，反映经济行为的模式

### 3. 频率多边形图的优点

- **追踪指标变化**：适合展示如消费支出等随时间的变化趋势
- **不同于时间序列**：关注值的分布而非时间顺序
- **识别主要范围**：帮助理解经济指标的主要活动范围

这套分析特别适合初学者，因为：

1. 代码简单，概念直观
2. 图表类型基础但信息量大
3. 能够直接看到数据的统计特性
4. 适合与经济学知识结合解释

如果您认为这些图表更符合教学需求，可以将它们纳入题目或替换之前的部分散点图题目。

# Storms数据集热力图

让我们修复这个错误。问题出在`month_name`变量的创建时机，我们需要确保在使用`count()`函数之前就已经创建了这个变量。
：

```r
# 加载必要的包
library(ggplot2)
library(dplyr)

# 检查数据集是否可用
if(!exists("storms")) {
  data(storms)  # 显式加载storms数据集
}

# 基本数据准备 - 修复变量创建顺序
storms_clean <- storms %>%
  # 确保category是有序因子
  mutate(
    category = factor(category, levels = 0:5, ordered = TRUE),
    # 转换月份为月份名称 - 确保在使用前创建
    month_name = factor(month, levels = 1:12, 
                        labels = c("1月", "2月", "3月", "4月", "5月", "6月", 
                                  "7月", "8月", "9月", "10月", "11月", "12月"))
  )

#----- 1. 月份与飓风类别的热力图 -----#
# 计算每月各类别飓风的频率 - 确保使用正确的变量名
monthly_category <- storms_clean %>%
  # 计数前去重，避免同一风暴多次计数
  distinct(name, year, month_name, category) %>%
  # 按月份和类别分组计数
  count(month_name, category) %>%
  # 填充缺失组合为0
  complete(month_name, category, fill = list(n = 0))

# 创建热力图
ggplot(monthly_category, aes(x = month_name, y = category, fill = n)) +
  geom_tile(color = "white") +
  # 使用适合热力图的配色方案
  scale_fill_gradient(low = "white", high = "red") +
  # 添加数值标签
  geom_text(aes(label = ifelse(n > 0, n, "")), color = "black", size = 3) +
  labs(
    title = "飓风类别的月度分布",
    subtitle = "数值表示各月份不同类别飓风的发生次数",
    x = "月份",
    y = "飓风类别",
    fill = "次数"
  ) +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.grid = element_blank()
  )

#----- 2. 年份与月份的飓风活动热力图 -----#
# 计算每年每月的飓风活动 - 确保使用正确的变量名
yearly_monthly <- storms_clean %>%
  # 去重，避免同一风暴多次计数
  distinct(name, year, month_name) %>%
  # 按年份和月份分组计数
  count(year, month_name) %>%
  # 填充缺失组合为0
  complete(year, month_name, fill = list(n = 0))

# 选择有代表性的年份范围(每5年)，避免图表过于拥挤
selected_years <- unique(yearly_monthly$year)
selected_years <- selected_years[selected_years %% 5 == 0]
yearly_monthly_filtered <- yearly_monthly %>%
  filter(year %in% selected_years)

# 创建热力图
ggplot(yearly_monthly_filtered, aes(x = month_name, y = factor(year), fill = n)) +
  geom_tile(color = "white") +
  # 使用蓝色调配色方案
  scale_fill_gradient(low = "white", high = "darkblue") +
  labs(
    title = "飓风活动的历史季节性分布(每5年)",
    x = "月份",
    y = "年份",
    fill = "飓风次数"
  ) +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.grid = element_blank()
  )

#----- 3. 风速与气压关系的热力图 -----#
# 创建风速和气压的分组计数
wind_pressure <- storms_clean %>%
  # 将连续变量分组为区间
  mutate(
    wind_group = cut(wind, breaks = seq(20, 160, by = 10)),
    pressure_group = cut(pressure, breaks = seq(900, 1020, by = 10))
  ) %>%
  # 按组计数
  count(wind_group, pressure_group) %>%
  # 填充缺失组合为0
  complete(wind_group, pressure_group, fill = list(n = 0))

# 创建热力图
ggplot(wind_pressure, aes(x = wind_group, y = pressure_group, fill = n)) +
  geom_tile() +
  # 使用梯度配色方案
  scale_fill_gradient2(low = "white", high = "purple", mid = "pink", 
                      midpoint = median(wind_pressure$n[wind_pressure$n > 0])) +
  labs(
    title = "飓风风速与气压的关系",
    subtitle = "颜色深浅表示不同组合的频率",
    x = "风速(节)",
    y = "气压(mb)",
    fill = "观测次数"
  ) +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.grid = element_blank()
  )
```

