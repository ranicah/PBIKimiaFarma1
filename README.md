# PBIKimiaFarma1

CREATE TABLE rakamin-kf-analytics-455106.kimia_farma.kf_table_analysis AS 
WITH
main AS (
SELECT
  transaction_id, date, t.branch_id, branch_name, kota, provinsi, c.rating rating_cabang, customer_name, t.product_id, product_name, p.price actual_price, discount_percentage,

  CASE
    WHEN p.price <= 50000 THEN 0.1
    WHEN p.price > 50000 and p.price <= 100000 THEN 0.15
    WHEN p.price > 100000 and p.price <= 300000 THEN 0.2
    WHEN p.price > 300000 and p.price <= 500000 THEN 0.25
    ELSE 0.3

  END

    AS percentage_gross_laba, p.price*(1-discount_percentage) AS nett_sales
    FROM
      rakamin-kf-analytics-455106.kimia_farma.kf_final_transaction t 

    LEFT JOIN
      rakamin-kf-analytics-455106.kimia_farma.kf_kantor_cabang c
    ON 
      t.branch_id = c.branch_id
LEFT JOIN
      rakamin-kf-analytics-455106.kimia_farma.kf_product p
    ON  
      t.product_id = p.product_id )

    SELECT  
      DISTINCT main.*, (actual_price*percentage_gross_laba)-(actual_price-nett_sales) nett_profit, 
      t.rating rating_transaksi
    
    FROM 
      main, rakamin-kf-analytics-455106.kimia_farma.kf_final_transaction t

    WHERE 
      main.transaction_id = t.transaction_id

    ORDER BY
    date DESC;
