# 光伏产能并表的控制期间与口径重列

这是本地演练用的合成来源资料。CSV 使用 UTF-8、有首行字段名，空字段表示未提供。除非字段语义明确为零，空值没有数值含义。

## 资料契约

业务日为 2026 年 11 月，日期区间起日含、止日不含。valid_from 与 wave 分别指业务生效和到达批。controls 标记取得实际控制，economic_share 是直接经济权属，right_id 相同表示同一底层收益权；只认 active 的最高版本。产能单位 MW，交付数量单位 MWp，两者不相加。monthly_report 按生产线及月期汇总。slice 的数量为已证明片区，只剩余量不能倒推未经证明片区，coverage 保证窗口所列逐批行全量并引用可替代的 slice。内部抵消按 ship_id 引用及买卖双方匹配，金额币种相同，终点确认可迟到。

以下文件位于 `data/`。

- `ownership.csv`（6 条）：`wave`、`right_id`、`owner`、`member`、`valid_from`、`valid_to`、`controls`、`economic_share`、`rev`、`status`
- `lines.csv`（4 条）：`line_id`、`operator`、`from_day`、`to_day`、`capacity_mw`
- `monthly_reports.csv`（4 条）：`wave`、`report`、`line_id`、`from_day`、`to_day`、`output_mwp`、`rev`
- `certified_slices.csv`（5 条）：`wave`、`slice_id`、`report`、`report_rev`、`from_day`、`to_day`、`output_mwp`
- `shipments.csv`（5 条）：`wave`、`ship_id`、`line_id`、`seller`、`buyer`、`day`、`mwp`、`yuan`
- `shipment_coverage.csv`（2 条）：`wave`、`line_id`、`slice_id`、`from_day`、`to_day`、`ship_ids`
- `purchase_receipts.csv`（3 条）：`wave`、`receipt`、`ship_id`、`buyer`、`seller`、`book_day`、`mwp`、`yuan`

`manifest.json` 记录完整资料包的列名、行数、校验值和跨表引用。完整资料包可含业务冲突或同一来源的多次投递，校验器保留这些原始记录。

## 本地查看

适用 Python 3.11，无需安装第三方库或构建。

```sh
python3 inspect_inputs.py
python3 -m unittest -v test_input_pack
```

查看命令打印各原始表的记录数。单元测试校验资料结构、引用与文件完整性，可离线执行。
