# 淘宝用户购买行为分析

## 项目简介
本项目基于淘宝移动端用户行为数据，分析用户 PV（页面访问量）和 UV（独立用户数）在不同日期和小时的变化趋势，挖掘用户活跃规律，为运营活动和营销策略提供参考。

## 数据概览
- **train_user_sample.csv**：用户行为日志（user_id, item_id, behavior_type, item_category, date, hour）  
- **train_item_sample.csv**：商品信息（item_id, item_category）  
- 字段说明：
  | 字段 | 含义 |
  |------|------|
  | user_id | 用户标识 |
  | item_id | 商品标识 |
  | behavior_type | 用户行为类型（1=浏览, 2=收藏, 3=加购, 4=购买） |
  | item_category | 商品分类 |
  | date | 日期（YYYY-MM-DD） |
  | hour | 小时（0-23） |

## 数据清洗
- 检查空值和异常值  
- user 表 `time` 拆分为 `date` + `hour`  
- item 表 `item_category` 按 `item_id` 一一对应 train_user 表  

**SQL 示例**：

```sql
-- 检查异常值
SELECT behavior_type AS b
FROM train_user
WHERE behavior_type NOT IN (1,2,3,4) OR behavior_type IS NULL;

-- 添加 date 和 hour
ALTER TABLE train_user
ADD COLUMN date DATE,
ADD COLUMN hour TINYINT;

UPDATE train_user
SET date = STR_TO_DATE(time, '%Y-%m-%d %H'),
    hour = CAST(SUBSTRING_INDEX(time, ' ', -1) AS UNSIGNED);

-- 合并商品分类
UPDATE train_user u
LEFT JOIN train_item i
ON u.item_id = i.item_id
SET u.item_category = i.item_category;
