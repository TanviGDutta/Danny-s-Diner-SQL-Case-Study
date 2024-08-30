# Danny-s-Diner-SQL-Case-Study
CASE STUDY DANNY'S DINER SQL
1. WHAT IS THE TOTAL AMOUNT EACH CUSTOMER SPENT AT THE RESTAURANT?

  SELECT A.CUSTOMER_ID,SUM(B.PRICE) AS TOTAL_AMOUNT FROM SALES AS A
  LEFT JOIN MENU AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
  GROUP BY A.CUSTOMER_ID
  

  2. HOW MANY DAY'S EACH CUSTOMER VISITED THE RESTAURANT?

  SELECT CUSTOMER_ID,COUNT(DISTINCT(ORDER_DATE)) AS NO_OF_DAYS FROM SALES
  GROUP BY CUSTOMER_ID

  
  3.WHAT WAS THE FIRST ITEM FROM THE MENU PURCHASED BY EACH CUSTOMER?
  
  WITH FIRSTPURCHASE AS
  (
  SELECT A.CUSTOMER_ID,B.PRODUCT_NAME,ROW_NUMBER() OVER(PARTITION BY CUSTOMER_ID ORDER BY ORDER_DATE) AS RANKING FROM SALES AS A
  LEFT JOIN MENU AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
  )
  SELECT CUSTOMER_ID,PRODUCT_NAME,RANKING FROM FIRSTPURCHASE
  WHERE RANKING=1
  
  4.WHAT IS THE MOST PURCHASED ITEM ON THE MENU AND HOW MANY TIMES WAS IT PURCHASED BY ALL CUSTOMERS?
  
  SELECT TOP 1 A.CUSTOMER_ID,COUNT(B.PRODUCT_NAME) AS COUNT_,B.PRODUCT_NAME FROM SALES AS A
  LEFT JOIN MENU AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
  GROUP BY A.CUSTOMER_ID,B.PRODUCT_NAME
  ORDER BY COUNT(B.PRODUCT_NAME) DESC
  

  5.WHICH ITEM WAS THE MOST POPULAR FOR EACH CUSTOMER?
  
  SELECT A.CUSTOMER_ID,COUNT(B.PRODUCT_NAME) AS COUNT_,B.PRODUCT_NAME FROM SALES AS A
  LEFT JOIN MENU AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
  GROUP BY A.CUSTOMER_ID,B.PRODUCT_NAME
  ORDER BY COUNT(B.PRODUCT_NAME) DESC

 6. WHICH ITEM WAS PURCHASED FIRST BY THE CUSTOMER AFTER THEY BECAME A MEMBER?
  
  WITH MEMBER_ AS
  (
  SELECT B.CUSTOMER_ID,A.PRODUCT_NAME,B.ORDER_DATE FROM MENU AS A
  INNER JOIN SALES AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
  INNER JOIN MEMBERS AS C
  ON B.CUSTOMER_ID=C.CUSTOMER_ID
  WHERE B.ORDER_DATE>=C.JOIN_DATE
  ),
  RANKING AS
  (
  SELECT ORDER_DATE,CUSTOMER_ID,PRODUCT_NAME,ROW_NUMBER() OVER(PARTITION BY CUSTOMER_ID ORDER BY ORDER_DATE) AS RANKS FROM MEMBER_
  )
  SELECT ORDER_DATE,CUSTOMER_ID,PRODUCT_NAME,RANKS FROM RANKING
  WHERE RANKS=1

  7. WHICH ITEM WAS PURCHASED JUST BEFORE THE CUSTOMER BECAME A MEMBER?
  
  WITH MEMBER_ AS
  (
  SELECT B.CUSTOMER_ID,A.PRODUCT_NAME,B.ORDER_DATE FROM MENU AS A
  INNER JOIN SALES AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
  INNER JOIN MEMBERS AS C
  ON B.CUSTOMER_ID=C.CUSTOMER_ID
  WHERE B.ORDER_DATE<C.JOIN_DATE
  ),
  RANKING AS
  (
  SELECT ORDER_DATE,CUSTOMER_ID,PRODUCT_NAME,ROW_NUMBER() OVER(PARTITION BY CUSTOMER_ID ORDER BY ORDER_DATE DESC) AS RANKS FROM MEMBER_
  )
  SELECT ORDER_DATE,CUSTOMER_ID,PRODUCT_NAME,RANKS FROM RANKING
  WHERE RANKS=1

  8.WHAT IS THE TOTAL ITEMS AND AMOUNT SPENT FOR EACH MEMBER BEFORE THEY BECAME A MEMBER?
  
  SELECT B.CUSTOMER_ID,COUNT(PRODUCT_NAME) AS TOTAL_ITEM,SUM(A.PRICE) AS AMOUNT_SPENT FROM MENU AS A
  INNER JOIN SALES AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
  INNER JOIN MEMBERS AS C
  ON B.CUSTOMER_ID=C.CUSTOMER_ID
  WHERE B.ORDER_DATE<C.JOIN_DATE
  GROUP BY B.CUSTOMER_ID

  9.  IF EACH $1 SPENT EQUATES TO 10 POINTS AND SUSHI HAS A 2X POINTS MULTIPLIER-HOW MANY POINTS WOULD EACH CUSTOMER HAVE?
 
  SELECT *,
  (CASE WHEN B.PRODUCT_NAME='SUSHI' THEN PRICE*10*2 ELSE PRICE*10 END) AS POINTS 
  FROM SALES AS A
  INNER JOIN MENU AS B
  ON A.PRODUCT_ID=B.PRODUCT_ID
