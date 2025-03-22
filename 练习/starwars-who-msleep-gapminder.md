

## 练习题1：starwars数据集（因子排序与分面图）

**题目要求：**

1. 从starwars数据集中筛选出现频率最高的6个物种
2. 使用因子操作，将物种按照身高中位数进行排序
3. 创建一个箱线图，展示不同物种的身高分布
4. 使用分面技术，按性别将图表分为不同部分
5. 为不同物种应用不同颜色，并使用viridis色系
6. 调整x轴标签角度以提高可读性

```r
library(dplyr)
library(ggplot2)
library(forcats)

# 准备数据：选择出现频率最高的6个物种
species_counts <- starwars %>%
  count(species) %>%
  arrange(desc(n)) %>%
  head(6)

# 使用因子排序，按照身高中位数排列物种
starwars_top_species <- starwars %>%
  filter(species %in% species_counts$species, !is.na(height)) %>%
  mutate(species = fct_reorder(species, height, .fun = median))

# 创建箱线图并分面展示性别差异
ggplot(starwars_top_species, aes(x = species, y = height, fill = species)) +
  geom_boxplot() +
  facet_wrap(~ gender, scales = "free_y") +
  labs(title = "主要物种的身高分布（按性别分面）",
       x = "物种（按身高中位数排序）",
       y = "身高(cm)") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        legend.position = "none") +
  scale_fill_viridis_d()
```

## 练习题2：who数据集（宽表转长表与多图表类型）

**题目要求：**

1. 将who数据集从宽表格式转换为长表格式
2. 从变量名中提取性别信息（男性/女性）和年龄组信息
3. 将年龄组转换为有序因子，按照年龄顺序排列
4. 筛选2005-2010年的数据进行分析
5. 计算每个年份、性别和年龄组的结核病例总数
6. 创建一个组合图表，同时包含折线图和散点图
7. 使用分面技术展示不同年龄组的数据
8. 为不同性别应用不同颜色，使用brewer色系

```r
library(dplyr)
library(tidyr)
library(ggplot2)
library(stringr)

# 宽表转长表
who_long <- who %>%
  pivot_longer(
    cols = new_sp_m014:newrel_f65, 
    names_to = "key", 
    values_to = "cases"
  ) %>%
  # 从key中提取性别和年龄信息
  mutate(
    gender = if_else(str_detect(key, "_m"), "男性", "女性"),
    age_group = case_when(
      str_detect(key, "014") ~ "0-14岁",
      str_detect(key, "1524") ~ "15-24岁",
      str_detect(key, "2534") ~ "25-34岁",
      str_detect(key, "3544") ~ "35-44岁",
      str_detect(key, "4554") ~ "45-54岁",
      str_detect(key, "5564") ~ "55-64岁",
      str_detect(key, "65") ~ "65岁以上",
      TRUE ~ "未知"
    )
  ) %>%
  # 设置年龄组为有序因子
  mutate(age_group = factor(age_group, 
                           levels = c("0-14岁", "15-24岁", "25-34岁", 
                                      "35-44岁", "45-54岁", "55-64岁", "65岁以上")))

# 分析2005-2010年数据
who_analysis <- who_long %>%
  filter(year >= 2005 & year <= 2010, !is.na(cases)) %>%
  group_by(year, gender, age_group) %>%
  summarise(total_cases = sum(cases, na.rm = TRUE), .groups = "drop")

# 创建组合图表：折线图+散点图
ggplot(who_analysis, aes(x = year, y = total_cases, color = gender, group = interaction(gender, age_group))) +
  geom_line(size = 0.7) +
  geom_point(size = 2) +
  facet_wrap(~ age_group, scales = "free_y") +
  labs(title = "2005-2010年不同年龄组和性别的结核病例趋势",
       x = "年份",
       y = "病例数",
       color = "性别") +
  theme_bw() +
  scale_color_brewer(palette = "Set1") +
  theme(strip.background = element_rect(fill = "lightblue"))
```

## 练习题3：msleep数据集（多种图表组合与因子排序）

**题目要求：**

1. 筛选msleep数据集中样本量大于等于3的动物目（order）
2. 将体重转换为对数尺度以便更好地可视化
3. 使用因子操作，按照睡眠时间的中位数对动物目进行排序
4. 创建一个结合箱线图和散点图的复合图表：
    - 箱线图展示睡眠时间分布
    - 散点图展示原始数据点，散点大小表示体重（对数尺度）  
        5., 使用facet_grid按保护状态（conservation）分组
5. 为不同动物目应用不同颜色，使用viridis色系
6. 调整x轴标签角度提高可读性

```r
library(dplyr)
library(ggplot2)
library(forcats)

# 数据准备，选择样本量较多的目
msleep_filtered <- msleep %>%
  filter(!is.na(order), !is.na(sleep_total), !is.na(bodywt)) %>%
  group_by(order) %>%
  filter(n() >= 3) %>%
  ungroup() %>%
  # 将体重转为对数方便可视化
  mutate(log_weight = log10(bodywt),
         # 按照睡眠中位数重新排序order
         order = fct_reorder(order, sleep_total, .fun = median))

# 创建多图表组合
ggplot(msleep_filtered, aes(x = order, y = sleep_total, fill = order)) +
  # 箱线图展示分布
  geom_boxplot(alpha = 0.7, outlier.shape = NA) +
  # 散点图展示原始数据点
  geom_jitter(aes(size = log_weight), width = 0.2, alpha = 0.7) +
  # 创建分面图，按保护状态分组
  facet_grid(. ~ conservation) +
  # 图表美化
  labs(title = "不同目的动物睡眠时间分布",
       subtitle = "点的大小表示体重（对数尺度）",
       x = "动物目（按睡眠中位数排序）",
       y = "总睡眠时间(小时/天)",
       size = "体重(对数)") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        legend.position = "right") +
  scale_fill_viridis_d() +
  guides(fill = "none")
```

## 练习题4：gapminder数据集（柱状图与折线图结合，复杂因子操作）

**题目要求：**

- 使用gapminder数据集，计算每个大洲在各年份的：
    - 总人口
    - 平均人均GDP
- 计算每个大洲每相邻年份之间的人口增长率（百分比）
- 按照2007年的平均人均GDP对大洲进行排序
- 创建一个双坐标轴图表：
    - 柱状图展示人口增长率（主y轴）
    - 折线图展示平均人均GDP趋势（次y轴）
- 使用分面技术按大洲分组展示
- 为柱状图和折线图应用不同的颜色方案
- 确保图表包含适当的标题、标签和图例

```r
library(dplyr)
library(ggplot2)
library(gapminder)
library(tidyr)
library(forcats)

# 计算每个洲在各年度的总人口和平均GDP
continent_data <- gapminder %>%
  group_by(continent, year) %>%
  summarise(
    total_pop = sum(pop),
    avg_gdpPercap = mean(gdpPercap),
    .groups = "drop"
  ) %>%
  # 计算人口增长率 - 修正的方法
  arrange(continent, year) %>%
  group_by(continent) %>%
  mutate(
    pop_growth = (total_pop / lag(total_pop) - 1) * 100
  ) %>%
  # 重新排序大洲，按2007年GDP排序
  ungroup() %>%
  mutate(continent = factor(continent)) %>%
  group_by(continent) %>%
  mutate(last_gdp = avg_gdpPercap[year == 2007]) %>%
  ungroup() %>%
  mutate(continent = fct_reorder(continent, last_gdp))

# 创建双轴图：柱状图+折线图
ggplot(continent_data, aes(x = year)) +
  # 柱状图展示人口增长率
  geom_col(aes(y = pop_growth, fill = continent), 
           position = "identity", width = 4, alpha = 0.7) +
  # 折线图展示GDP趋势
  geom_line(aes(y = avg_gdpPercap/100, color = continent, group = continent), 
            size = 1.2) +
  geom_point(aes(y = avg_gdpPercap/100, color = continent), size = 2) +
  # 创建分面图按大洲分组
  facet_wrap(~ continent, scales = "free_y", nrow = 2) +
  # 设置双y轴标签
  scale_y_continuous(
    name = "人口增长率 (%)",
    sec.axis = sec_axis(~.*100, name = "人均GDP (美元)")
  ) +
  # 美化图表
  labs(title = "1952-2007年各大洲的人口增长率和人均GDP趋势",
       x = "年份",
       fill = "大洲",
       color = "大洲") +
  theme_light() +
  theme(legend.position = "bottom") +
  scale_fill_brewer(palette = "Set2") +
  scale_color_brewer(palette = "Set1")
```

