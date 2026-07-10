-- ============================================================================
-- Product Analytics Platform
-- File Name : funnel.sql
--
-- Business Problem:
-- Analyze the customer journey through the purchase funnel and identify
-- where users drop off before completing a purchase.
--
-- Tables Used:
-- 1. product_events
-- 2. users
-- 3. marketing
--
-- Skills Demonstrated:
-- ✔ SQL Aggregations
-- ✔ CASE WHEN
-- ✔ CTE (Common Table Expressions)
-- ✔ INNER JOIN
-- ✔ Business KPI Calculation
-- ✔ Funnel Analysis
-- ============================================================================


-- ============================================================================
-- STEP 1
-- View Sample Data
-- ============================================================================

SELECT *
FROM product_events
LIMIT 20;



-- ============================================================================
-- STEP 2
-- Count Total Events
--
-- Business Question:
-- Which event occurs the most?
-- ============================================================================

SELECT

event_name,

COUNT(*) AS total_events

FROM product_events

GROUP BY event_name

ORDER BY total_events DESC;



-- ============================================================================
-- STEP 3
-- Total Unique Users
-- ============================================================================

SELECT

COUNT(DISTINCT user_id) AS total_users

FROM product_events;



-- ============================================================================
-- STEP 4
-- Total Sessions
-- ============================================================================

SELECT

COUNT(DISTINCT session_id) AS total_sessions

FROM product_events;



-- ============================================================================
-- STEP 5
-- Funnel Counts
--
-- View
-- ↓
-- Add to Cart
-- ↓
-- Checkout
-- ↓
-- Purchase
-- ============================================================================

SELECT

COUNT(DISTINCT CASE
WHEN event_name='view'
THEN session_id
END) AS Views,

COUNT(DISTINCT CASE
WHEN event_name='add_to_cart'
THEN session_id
END) AS Add_to_Cart,

COUNT(DISTINCT CASE
WHEN event_name='checkout'
THEN session_id
END) AS Checkout,

COUNT(DISTINCT CASE
WHEN event_name='purchase'
THEN session_id
END) AS Purchase

FROM product_events;



-- ============================================================================
-- STEP 6
-- Funnel Conversion Rates
-- ============================================================================

WITH funnel AS
(

SELECT

COUNT(DISTINCT CASE
WHEN event_name='view'
THEN session_id END) views,

COUNT(DISTINCT CASE
WHEN event_name='add_to_cart'
THEN session_id END) carts,

COUNT(DISTINCT CASE
WHEN event_name='checkout'
THEN session_id END) checkouts,

COUNT(DISTINCT CASE
WHEN event_name='purchase'
THEN session_id END) purchases

FROM product_events

)

SELECT

views,

carts,

checkouts,

purchases,

ROUND(carts*100/views,2) AS View_to_Cart,

ROUND(checkouts*100/carts,2) AS Cart_to_Checkout,

ROUND(purchases*100/checkouts,2) AS Checkout_to_Purchase,

ROUND(purchases*100/views,2) AS Overall_Conversion

FROM funnel;



-- ============================================================================
-- STEP 7
-- Funnel Drop-Off
-- ============================================================================

WITH funnel AS
(

SELECT

COUNT(DISTINCT CASE
WHEN event_name='view'
THEN session_id END) views,

COUNT(DISTINCT CASE
WHEN event_name='purchase'
THEN session_id END) purchases

FROM product_events

)

SELECT

views,

purchases,

views-purchases AS dropoff,

ROUND(
(views-purchases)*100/views
,2) AS Dropoff_Percentage

FROM funnel;



-- ============================================================================
-- STEP 8
-- Device Funnel
--
-- Business Question:
-- Which device converts the best?
-- ============================================================================

SELECT

u.device,

COUNT(DISTINCT CASE
WHEN p.event_name='view'
THEN p.session_id END) views,

COUNT(DISTINCT CASE
WHEN p.event_name='add_to_cart'
THEN p.session_id END) carts,

COUNT(DISTINCT CASE
WHEN p.event_name='checkout'
THEN p.session_id END) checkouts,

COUNT(DISTINCT CASE
WHEN p.event_name='purchase'
THEN p.session_id END) purchases

FROM product_events p

JOIN users u

ON p.user_id=u.user_id

GROUP BY u.device;



-- ============================================================================
-- STEP 9
-- Country Funnel
-- ============================================================================

SELECT

u.country,

COUNT(DISTINCT CASE
WHEN p.event_name='view'
THEN p.session_id END) views,

COUNT(DISTINCT CASE
WHEN p.event_name='purchase'
THEN p.session_id END) purchases,

ROUND(

COUNT(DISTINCT CASE
WHEN p.event_name='purchase'
THEN p.session_id END)

*100/

COUNT(DISTINCT CASE
WHEN p.event_name='view'
THEN p.session_id END)

,2)

AS Conversion_Rate

FROM product_events p

JOIN users u

ON p.user_id=u.user_id

GROUP BY u.country

ORDER BY Conversion_Rate DESC;



-- ============================================================================
-- STEP 10
-- Marketing Funnel
-- ============================================================================

SELECT

m.channel,

COUNT(DISTINCT CASE
WHEN p.event_name='view'
THEN p.session_id END) views,

COUNT(DISTINCT CASE
WHEN p.event_name='purchase'
THEN p.session_id END) purchases,

ROUND(

COUNT(DISTINCT CASE
WHEN p.event_name='purchase'
THEN p.session_id END)

*100

/

COUNT(DISTINCT CASE
WHEN p.event_name='view'
THEN p.session_id END)

,2)

AS conversion_rate

FROM product_events p

JOIN marketing m

ON p.session_id=m.session_id

GROUP BY m.channel

ORDER BY conversion_rate DESC;



-- ============================================================================
-- STEP 11
-- Revenue by Funnel Stage
-- ============================================================================

SELECT

event_name,

SUM(price) revenue,

AVG(price) average_price,

COUNT(*) events

FROM product_events

GROUP BY event_name

ORDER BY revenue DESC;



-- ============================================================================
-- STEP 12
-- Checkout Drop-Off
-- ============================================================================

WITH checkout_stats AS
(

SELECT

COUNT(DISTINCT CASE
WHEN event_name='checkout'
THEN session_id END) checkout,

COUNT(DISTINCT CASE
WHEN event_name='purchase'
THEN session_id END) purchase

FROM product_events

)

SELECT

checkout,

purchase,

checkout-purchase AS lost_sessions,

ROUND(

(checkout-purchase)

*100

/

checkout

,2)

AS Checkout_Dropoff

FROM checkout_stats;



-- ============================================================================
-- STEP 13
-- Top Products
-- ============================================================================

SELECT

product,

COUNT(*) purchases,

SUM(price) revenue,

ROUND(AVG(price),2) average_price

FROM product_events

WHERE event_name='purchase'

GROUP BY product

ORDER BY revenue DESC;



-- ============================================================================
-- STEP 14
-- Top Marketing Channels
-- ============================================================================

SELECT

m.channel,

SUM(p.price) revenue,

COUNT(*) purchases,

ROUND(AVG(price),2) average_order_value

FROM product_events p

JOIN marketing m

ON p.session_id=m.session_id

WHERE p.event_name='purchase'

GROUP BY m.channel

ORDER BY revenue DESC;



-- ============================================================================
-- STEP 15
-- Executive Dashboard Summary
-- ============================================================================

SELECT

COUNT(DISTINCT user_id) AS Total_Users,

COUNT(DISTINCT session_id) AS Total_Sessions,

COUNT(
CASE
WHEN event_name='purchase'
THEN 1
END
) AS Total_Orders,

ROUND(

SUM(
CASE
WHEN event_name='purchase'
THEN price
ELSE 0
END
)

,2)

AS Revenue,

ROUND(

AVG(
CASE
WHEN event_name='purchase'
THEN price
END
)

,2)

AS Average_Order_Value;
