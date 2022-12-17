# 条件 SQL 连接

> 原文：<https://dev.to/tyzia/conditional-sql-join>

我想分享我在 SQL 连接方面的经验。

问题:我有 3 张表:“供应商”、“发票”、“条款”。我在外部加入了“供应商”和“发票”，现在我想加入“条款”表，但 ON 子句是有条件的。如果 invoices.term_id 不为空，则我根据 invoices.term_id 连接“条款”表，否则我根据 vendors.default _ terms _ id 连接“条款”表。我如何在 ON 子句中使用 if？

解决方法:
我在 [Jef 的 SQL Server 博客](http://weblogs.sqlteam.com/jeffs/archive/2007/04/03/Conditional-Joins.aspx)上找到的，非常感谢他。

1.  不要在 ON 子句中使用条件。
2.  根据每个条件创建两个左外连接，即

    ```
    LEFT OUTER JOIN terms t ON invoices.term_id = t.term_id
    LEFT OUTER JOIN terms t ON vendors.default_term_id = t.term_id
    ```

3.  在 SELECT 语句中借助 COALESCE()函数只选择那个不为空的字段:

    ```
    SELECT COALESCE(invoices.term_id, vendors.default_term_id)
    ```

4.  在 COALESCE()中，我们首先放入字段，该字段可能不为空。但是如果它是 null，那么我们使用一个缺省值，它总是不为 null。
5.  完整的查询看起来像这样(有额外的约束，但你会有一个想法):

    ```
    SELECT v.vendor_id AS "Vendor #",
        vendor_name AS "Vendor Name",
        vendor_contact_first_name AS "Contact First Name",
        vendor_contact_last_name AS "Contact Last Name",
        invoice_id AS "Invoice #",
        invoice_date AS "Invoice Date",
        COALESCE(t.terms_description, t_default.terms_description) AS "Description of Terms"
    FROM vendors v
        LEFT JOIN invoices i
            ON v.vendor_id = i.vendor_id
        LEFT JOIN terms t
            ON t.terms_id = i.terms_id 
        LEFT JOIN terms t_default
            ON t_default.terms_id = v.default_terms_id
    WHERE v.vendor_contact_last_name LIKE 'Z%'
      OR (v.vendor_contact_last_name > 'V' AND v.vendor_contact_last_name < 'Y')
    ORDER BY vendor_contact_last_name DESC;
    ```

6.  干杯！