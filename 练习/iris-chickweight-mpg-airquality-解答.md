
## 第一题：使用iris数据集进行数据分析与可视化

```r
library(dplyr)
library(ggplot2)

# 1. 数据预处理
# 按Species分组计算各测量变量的平均值和标准差
species_summary <- iris %>%
  group_by(Species) %>%
  summarise(across(where(is.numeric), list(mean = mean, sd = sd), .names = "{.col}_{.fn}"))

# 筛选出花瓣长度大于平均值的观测值
mean_petal_length <- mean(iris$Petal.Length)
long_petal_iris <- iris %>%
  filter(Petal.Length > mean_petal_length)

# 创建花瓣面积变量
iris_with_area <- iris %>%
  mutate(Petal.Area = Petal.Length * Petal.Width)

# 2. 因子处理
# 按花萼长度平均值重新排序Species因子水平
species_sepal_mean <- iris %>%
  group_by(Species) %>%
  summarise(mean_sepal_length = mean(Sepal.Length)) %>%
  arrange(desc(mean_sepal_length))

iris_reordered <- iris %>%
  mutate(Species = factor(Species, levels = species_sepal_mean$Species))

# 为Species设置中文标签
iris_chinese <- iris %>%
  mutate(Species = factor(Species, 
                          levels = c("setosa", "versicolor", "virginica"),
                          labels = c("山鸢尾", "变色鸢尾", "维吉尼亚鸢尾")))

# 3. 数据可视化
# 散点图：花瓣长度与宽度的关系
ggplot(iris, aes(x = Petal.Length, y = Petal.Width, color = Species)) +
  geom_point(alpha = 0.7) +
  geom_smooth(method = "lm") +
  labs(title = "鸢尾花花瓣长度与宽度的关系",
       x = "花瓣长度 (cm)",
       y = "花瓣宽度 (cm)",
       color = "品种") +
  theme_minimal()

# 分面图：按Species显示花萼和花瓣测量数据分布
iris_long <- iris %>%
  pivot_longer(cols = -Species, 
               names_to = c("Part", "Measure"),
               names_pattern = "(Sepal|Petal)\\.(Length|Width)",
               values_to = "Value")

ggplot(iris_long, aes(x = Measure, y = Value, fill = Part)) +
  geom_boxplot() +
  facet_wrap(~Species, nrow = 1) +
  labs(title = "不同品种鸢尾花的测量数据分布",
       x = "测量维度",
       y = "数值 (cm)",
       fill = "花的部位") +
  theme_bw() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

## 第二题：使用ChickWeight数据集进行时间序列分析

```r
library(dplyr)
library(ggplot2)
library(tidyr)

# 1. 数据预处理
# 计算每个时间点各饮食组的平均体重和标准差
diet_time_summary <- ChickWeight %>%
  group_by(Diet, Time) %>%
  summarise(mean_weight = mean(weight),
            sd_weight = sd(weight),
            .groups = "drop")

# 找出每个饮食组生长最快的小鸡
# 先计算每只小鸡的体重增长率
chick_growth <- ChickWeight %>%
  group_by(Chick) %>%
  arrange(Time) %>%
  mutate(growth_rate = c(NA, diff(weight) / diff(Time))) %>%
  ungroup()

# 找出每个饮食组最大增长率的小鸡
fastest_growing_chicks <- chick_growth %>%
  filter(!is.na(growth_rate)) %>%
  group_by(Diet, Chick) %>%
  summarise(max_growth_rate = max(growth_rate, na.rm = TRUE),
            .groups = "drop") %>%
  group_by(Diet) %>%
  filter(max_growth_rate == max(max_growth_rate)) %>%
  ungroup()

# 创建相对于初始重量的增长百分比
chick_percent_growth <- ChickWeight %>%
  group_by(Chick) %>%
  mutate(initial_weight = first(weight),
         percent_growth = (weight - initial_weight) / initial_weight * 100) %>%
  ungroup()

# 2. 因子处理
# 将Diet转换为有序因子，按最终平均体重排序
final_weights <- ChickWeight %>%
  filter(Time == max(Time)) %>%
  group_by(Diet) %>%
  summarise(mean_final_weight = mean(weight)) %>%
  arrange(desc(mean_final_weight))

ChickWeight_ordered <- ChickWeight %>%
  mutate(Diet = factor(Diet, levels = final_weights$Diet))

# 为饮食组创建描述性标签
ChickWeight_labeled <- ChickWeight %>%
  mutate(Diet = factor(Diet, 
                       levels = 1:4,
                       labels = c("高蛋白", "标准饮食", "高碳水", "混合饮食")))

# 3. 数据可视化
# 线图：不同饮食组小鸡的平均体重随时间的变化
ggplot(diet_time_summary, aes(x = Time, y = mean_weight, color = factor(Diet))) +
  geom_line(size = 1) +
  geom_errorbar(aes(ymin = mean_weight - sd_weight, 
                    ymax = mean_weight + sd_weight),
                width = 1, alpha = 0.5) +
  labs(title = "不同饮食组小鸡平均体重随时间的变化",
       x = "时间(天)",
       y = "平均体重(g)",
       color = "饮食组") +
  theme_minimal()

# 箱线图：比较不同饮食组在最终测量日的体重分布
final_time_data <- ChickWeight %>%
  filter(Time == max(Time))

ggplot(final_time_data, aes(x = factor(Diet), y = weight, fill = factor(Diet))) +
  geom_boxplot() +
  labs(title = "最终测量日不同饮食组小鸡的体重分布",
       x = "饮食组",
       y = "体重(g)",
       fill = "饮食组") +
  theme_bw()

# 分面和颜色编码展示各个小鸡的生长轨迹
ggplot(ChickWeight, aes(x = Time, y = weight, group = Chick, color = factor(Diet))) +
  geom_line(alpha = 0.7) +
  facet_wrap(~Diet) +
  labs(title = "各饮食组小鸡的个体生长轨迹",
       x = "时间(天)",
       y = "体重(g)",
       color = "饮食组") +
  theme_light()
```

## 第三题：使用airquality数据集进行环境数据分析

```r
library(dplyr)
library(ggplot2)
library(tidyr)
library(lubridate)
library(reshape2)

# 1. 数据预处理
# 处理缺失值，计算每个月缺失值的数量和比例
missing_by_month <- airquality %>%
  group_by(Month) %>%
  summarise(across(everything(), 
                   ~ sum(is.na(.)), 
                   .names = "missing_{.col}"),
            total_rows = n(),
            missing_percent = sum(is.na(Ozone), is.na(Solar.R)) / (n() * 2) * 100)

# 按月份计算环境指标的统计量
monthly_stats <- airquality %>%
  group_by(Month) %>%
  summarise(across(c(Ozone, Solar.R, Wind, Temp),
                   list(mean = ~ mean(., na.rm = TRUE),
                        median = ~ median(., na.rm = TRUE),
                        sd = ~ sd(., na.rm = TRUE)),
                   .names = "{.col}_{.fn}"))

# 温度转换（华氏度到摄氏度）和风速转换（英里/小时到米/秒）
airquality_converted <- airquality %>%
  mutate(Temp_C = (Temp - 32) * 5/9,
         Wind_mps = Wind * 0.44704)  # 1 mph = 0.44704 m/s

# 2. 因子处理
# 将Month转换为因子并添加月份名称
month_labels <- c("五月", "六月", "七月", "八月", "九月")
airquality_factored <- airquality %>%
  mutate(Month = factor(Month, 
                        levels = 5:9,
                        labels = month_labels))

# 创建温度等级变量
airquality_categories <- airquality %>%
  mutate(Temp_level = cut(Temp,
                          breaks = c(0, 70, 80, 100),
                          labels = c("低温", "适中", "高温"),
                          right = FALSE))

# 创建空气质量变量
airquality_categories <- airquality_categories %>%
  mutate(Air_quality = case_when(
    is.na(Ozone) ~ NA_character_,
    Ozone <= 20 ~ "优",
    Ozone <= 40 ~ "良",
    Ozone <= 80 ~ "一般",
    TRUE ~ "差"
  ),
  Air_quality = factor(Air_quality, levels = c("优", "良", "一般", "差")))

# 3. 数据可视化
# 创建日期变量用于时间序列图
airquality_dated <- airquality %>%
  mutate(Date = make_date(year = 1973, month = Month, day = Day))

# 时间序列图：臭氧浓度随日期变化
ggplot(airquality_dated, aes(x = Date, y = Ozone, color = factor(Month))) +
  geom_line() +
  geom_point() +
  labs(title = "1973年夏季纽约臭氧浓度变化",
       x = "日期",
       y = "臭氧浓度(ppb)",
       color = "月份") +
  scale_color_brewer(palette = "Set1") +
  theme_minimal()

# 散点图：温度与臭氧浓度的关系
ggplot(airquality, aes(x = Temp, y = Ozone)) +
  geom_point(alpha = 0.7) +
  geom_smooth(method = "loess", se = TRUE) +
  labs(title = "温度与臭氧浓度的关系",
       x = "温度(华氏度)",
       y = "臭氧浓度(ppb)") +
  theme_light()

# 分面和箱线图：比较不同月份的空气质量指标分布
airquality_long <- airquality %>%
  pivot_longer(cols = c(Ozone, Solar.R, Wind, Temp),
               names_to = "Variable",
               values_to = "Value")

ggplot(airquality_long, aes(x = factor(Month), y = Value, fill = factor(Month))) +
  geom_boxplot() +
  facet_wrap(~Variable, scales = "free_y") +
  labs(title = "不同月份的环境指标分布",
       x = "月份",
       y = "数值",
       fill = "月份") +
  theme_bw() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

# 热图：环境变量之间的相关性
air_cor <- cor(airquality[, c("Ozone", "Solar.R", "Wind", "Temp")], 
               use = "complete.obs")
air_cor_melted <- melt(air_cor)

ggplot(air_cor_melted, aes(x = Var1, y = Var2, fill = value)) +
  geom_tile(color = "white") +
  geom_text(aes(label = sprintf("%.2f", value)), size = 4) +
  scale_fill_gradient2(low = "blue", mid = "white", high = "red", 
                       midpoint = 0, limits = c(-1, 1)) +
  labs(title = "环境变量相关性热图",
       x = "", y = "", fill = "相关系数") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

## 第四题：使用mpg数据集进行汽车燃油效率分析

```r
library(dplyr)
library(ggplot2)
library(tidyr)

# 1. 数据预处理
# 按制造商和车型分组计算燃油效率平均值
car_efficiency <- mpg %>%
  group_by(manufacturer, model) %>%
  summarise(mean_city = mean(cty),
            mean_hwy = mean(hwy),
            .groups = "drop")

# 找出每个制造商燃油效率最高的车型
best_models_by_manufacturer <- car_efficiency %>%
  group_by(manufacturer) %>%
  mutate(combined_mpg = (mean_city + mean_hwy) / 2) %>%
  filter(combined_mpg == max(combined_mpg)) %>%
  ungroup()

# 创建综合燃油效率指标
mpg_with_combined <- mpg %>%
  mutate(combined_mpg = 0.55 * cty + 0.45 * hwy)

# 2. 因子处理
# 将manufacturer按平均燃油效率重新排序
manufacturer_efficiency <- mpg %>%
  group_by(manufacturer) %>%
  summarise(mean_combined = mean((cty * 0.55) + (hwy * 0.45))) %>%
  arrange(desc(mean_combined))

mpg_ordered <- mpg %>%
  mutate(manufacturer = factor(manufacturer, levels = manufacturer_efficiency$manufacturer))

# 将class变量重新编码为中文标签
class_labels <- c(
  "compact" = "紧凑型",
  "midsize" = "中型车",
  "suv" = "SUV",
  "2seater" = "双座跑车",
  "minivan" = "小型货车",
  "pickup" = "皮卡",
  "subcompact" = "次紧凑型"
)

mpg_chinese <- mpg %>%
  mutate(class_cn = class_labels[class])

# 创建环保等级变量
mpg_eco <- mpg %>%
  mutate(combined_mpg = 0.55 * cty + 0.45 * hwy,
         eco_level = cut(combined_mpg,
                         breaks = c(0, 16, 22, 50),
                         labels = c("耗油", "标准", "节能"),
                         right = FALSE))

# 3. 数据可视化
# 条形图：比较不同制造商的平均燃油效率
manufacturer_class_mpg <- mpg %>%
  group_by(manufacturer, class) %>%
  summarise(mean_combined = mean(0.55 * cty + 0.45 * hwy),
            .groups = "drop")

ggplot(manufacturer_class_mpg, aes(x = reorder(manufacturer, mean_combined), 
                                   y = mean_combined, fill = class)) +
  geom_bar(stat = "identity", position = "dodge") +
  coord_flip() +
  labs(title = "各制造商不同车型的平均燃油效率",
       x = "制造商",
       y = "综合燃油效率(mpg)",
       fill = "车型") +
  theme_minimal()

# 散点图：发动机排量与燃油效率的关系
ggplot(mpg, aes(x = displ, y = hwy, shape = drv, color = drv)) +
  geom_point(size = 3, alpha = 0.7) +
  geom_smooth(method = "lm", se = FALSE) +
  labs(title = "发动机排量与高速公路燃油效率的关系",
       subtitle = "按驱动类型(drv)区分",
       x = "发动机排量(升)",
       y = "高速公路燃油效率(mpg)",
       shape = "驱动类型",
       color = "驱动类型") +
  scale_shape_manual(values = c(16, 17, 18)) +
  scale_color_brewer(palette = "Set1") +
  theme_bw()

# 气泡图：排量、燃油效率和车辆数量
mpg_count <- mpg %>%
  group_by(displ, hwy) %>%
  summarise(count = n(), .groups = "drop")

ggplot(mpg_count, aes(x = displ, y = hwy, size = count)) +
  geom_point(alpha = 0.7, color = "steelblue") +
  scale_size_continuous(range = c(1, 10)) +
  labs(title = "发动机排量与高速公路燃油效率的关系",
       subtitle = "气泡大小表示车型数量",
       x = "发动机排量(升)",
       y = "高速公路燃油效率(mpg)",
       size = "车型数量") +
  theme_minimal()

# 分面小提琴图：比较不同年份不同车型的燃油效率分布
ggplot(mpg, aes(x = class, y = hwy, fill = class)) +
  geom_violin() +
  geom_boxplot(width = 0.1, fill = "white", alpha = 0.7) +
  facet_wrap(~year) +
  labs(title = "不同年份各车型高速公路燃油效率的分布",
       x = "车型",
       y = "高速公路燃油效率(mpg)") +
  theme_bw() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        legend.position = "none")
```

