# 常用函数

## 函数

```shell
# 年龄
# 当前年减去出生年月
=YEAR(NOW())-MID(D2,7,4)

# 男女
# 倒数第二位是奇数的是男 偶数是女
=IF(MOD(INT(MID(D2,17,1)),2)=1,"男","女")
```

## 筛选

```shell
# 重复值筛选
# 选中数据 条件格式 突出显示单元格式规则  重复值
```
